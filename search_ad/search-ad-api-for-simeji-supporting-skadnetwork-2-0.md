# Zucks Search Ad API for simeji supporting SKAdNetwork 2.0

Release Date:

Current API:

- https://docs.google.com/document/d/1YEGfBt9vmceCmjInER_7vh2mkSGZWgrShvw8br--9lw/edit

What we talked about during last meeting:

![Overview figure](image/overview-figure-2-0.png)

## Prerequisites:

- Include the Zucks network’s ID in your Info.plist
    - Zucks ad network’s ID: 3qcr597p9d.skadnetwork
    - https://developer.apple.com/documentation/storekit/skadnetwork/configuring_the_participating_apps
- Flow chart:

![Flow chart](image/flow-chart.png)

### ① Campaign API

- API
    - ENDPOINT: https://ms.zucksadnetwork.com/web_api/search_ad/simeji/v2
    - METHOD: GET
    - Example:
      ```
      curl -H 'x-api-key:xxxxxxxxxxxxxxxx' 'https://ms.zucksadnetwork.com/web_api/search_ad/simeji/v2?frame_id=xxxxxx'
      ```
    - You could reuse the x-api-key from the previous version.
- Changes:
    - A new field **skadnSignatureUrl** will be added to the response of campaign which supported SKAdNetwork.
        - For Android or campaigns that do not support SKAdNetwork this field will not exist
    - Instruction on how to use this field would be written below.

```json
{
  "result": [
    {
      "id": 1,
      "appId": "com.example.AndroidApp",
      "appName": "Zucks Android App",
      "os": "android",
      "osMinVersion": "5.1",
      "osMaxVersion": "9.3.5",
      "cpc": 10.5,
      "impUrl": "https://k.zucks.net/i/?x=...",
      "clickUrl": "https://k.zucks.net/rd/?x=..."
    },
    {
      "id": 90,
      "appId": "com.myname.IOSApp",
      "appName": "Zucks IOS App",
      "os": "ios",
      "cpc": 20,
      "impUrl": "https://k.zucks.net/i/?x=...",
      "clickUrl": "https://k.zucks.net/rd/?x=...",
      "skadnSignatureUrl": "https://sh.zucks.net/search_ad/skadn/?x=..."
    }
  ]
}
```

### ② When show ads, request data that is required by SKAdNetwork by calling **skadnSignatureUrl**

Only do this step if:

- Selected campaign contains **skadnSignatureUrl**
- User iOS device supports SKAdNetwork
    - https://developer.apple.com/documentation/storekit/skadnetwork/signing_and_providing_ads

**<span style="text-decoration:underline;">Request:</span>**

**GET skadnSignatureUrl**

- **skadnvers[]**
    - required
    - Array of strings containing the supported skadnetwork versions. We support array of string in query parameter by
      using the syntax key[]=value
        - For eg: "skadnvers[]=2.0&skadnvers[]=2.1"
    - Currently we only accept 2.0, 2.1. Available SkAdNetwork versions and versions supported by Zucks:
        - 1.0: No
        - 2.0: Yes
        - 2.1: In the future
        - 2.2: In the future
- **sourceapp**
    - required
    - ID of publisher app in Apple’s App Store
    - For eg: 899997582
    - This would be a constant ID (899997582) if you only have one ID. If you only have one ID we can get rid of this
      field.

_skadnSignatureUrl might already contain query parameters. So please append the required parameters to the end of the
URL_

Example:

Based on the example from ①, if you selected the campaign with id: 90 then the request to get SKAdNetwork related data
would look like:

```
GET https://sh.zucks.net/search_ad/skadn/?x=...&skadnvers[]=2.0&sourceapp=899997582
```

**<span style="text-decoration:underline;">Response:</span>**

Response Header:

- HTTP status: 200 OK
- Content-Type: application/json;charset=UTF-8

```json
{
  "skadn": {
    "version": "2.0",
    "network": "3qcr597p9d.skadnetwork",
    "campaign": 45,
    "itunesitem": 123456789,
    "nonce": "473b1a16-b4ef-43ad-9591-fcf3aefa82a7",
    "sourceapp": 899997582,
    "timestamp": 1615448225534,
    "signature": "MEQCIEQlmZRNfYzKBSE8QnhLTIHZZZWCFgZpRqRxHss65KoFAiAJgJKjdrWdkLUOCCjuEx2RmFS7daRzSVZRVZ8RyMyUXg=="
  },
  "impUrl": "https://k.zucks.net/i/?x=...",
  "clickUrl": "https://k.zucks.net/rd/?x=..."
}
```

The response contains 3 fields

- skadn
    - Data you need to present an ad with the ability to provide SKAdNetwork postback
    - To see how to use these fields please refer
      to [SKAdNetwork documentation](https://developer.apple.com/documentation/storekit/skadnetwork/signing_and_providing_ads)
- impUrl
    - You will receive impUrl from ① but for SKAdNetwork ads please use this new url instead when you show your ad to
      measure impressions
- clickUrl
    - You will receive clickUrl from ① but for SKAdNetwork ads please use this new url instead when user clicks on your
      ad to measure clicks

### ③ Provide a StoreKit-Rendered Ad from SimejiSDK

- This part is not under our control so we will only provide documentation for you.
- https://developer.apple.com/documentation/storekit/skadnetwork/signing_and_providing_ads

### ④ Click Notification

- As stated in ② you will need to use the clickUrl from ② for SKAdNetwork Ads. To make sure the click request is
  redirected to our measurement partner (MMP) properly, we require you to follow the redirect. (for e.g: if you use cURL
  add the [option -L](https://everything.curl.dev/http/redirects#tell-curl-to-follow-redirects))

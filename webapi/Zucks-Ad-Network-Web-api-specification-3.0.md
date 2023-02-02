# Zucks Ad Network WEB API v3 Specification

## Request

* End point
  * `https://sh.zucks.net/opt/api/v3`
* Method
  * GET

### Request Headers

* `User-Agent`: Required.
  * デフォルトブラウザと同等のものを送信してください
  * 末尾に独自の付加情報が追加されていても、許容されます
  * Parameter `ua` が設定されている場合、Parameter側の設定が優先されます
* `Referer`: Optional.
  * Parameter `ref` が設定されている場合、Parameter側の設定が優先されます
* `Accept-Language`: Optional.
  * Parameter `lang` が設定されている場合、Parameter側の設定が優先されます
* `Cookie`: Optional.
  * ブラウザ/WebView内から直接リクエストを送る場合、Cookie情報も付与するようにリクエストしてください
    * Fetch API: `credentials` オプションを `include` に設定
    * XMLHttpRequest: `withCredentials` プロパティを `true` に設定


### Request Parameters

* `frameid`: Requierd.
  * 管理画面より作成いただいた広告枠ID
  * 間違った値を指定した場合、HTTP Status `400 Bad Request` を返し、広告は返却されません
* `ida`: Optional.
  * IDFA(iOS) or Advertising ID(Android)
  * Parameter `ida` を送信する場合、iOS13以前やAndroidでは後述のParameter `lat` も送信してください
* `lat`: 0 or 1, Optional.
  * 「広告トラッキング制限」が無効な場合: 0
  * 「広告トラッキング制限」が有効な場合: 1
  * iOS14以降では不要となります。代わりに後述のattsパラメータを送信してください。
* `ua`: String, Optional.
  * デフォルトブラウザと同等のUserAgent文字列
  * 末尾に独自の付加情報が追加されていても、許容されます
* `chm`: String, Optional.
  * ブラウザのユーザエージェントクライアントヒントAPI機能(以下, Client Hints)によって取得できる端末モデル名
  * [Client Hintsの取得](#Client-Hintsの取得)
* `chpv`: String, Optional.
  * ブラウザのユーザエージェントクライアントヒントAPI機能(以下, Client Hints)によって取得できるプラットフォームバージョン(OSバージョン)
  * [Client Hintsの取得](#Client-Hintsの取得)
* `ref`: Optional.
  * Web面配信の場合、広告掲載ページのURL
* `lang`: Optional.
  * 端末の言語設定
  * 例 : `ja`
* `ip`: Optional.
  * Source IP address
  * APIへのリクエストをサーバから発行する場合には、広告を表示する端末のIPアドレスを設定してください

#### Client Hintsの取得
[UserAgent Client Hints API](https://developer.mozilla.org/ja/docs/Web/API/User-Agent_Client_Hints_API) 用いてモデル、プラットフォームバージョンを取得する例
```javascript
if(navigator.userAgentData){
  navigator.userAgentData.getHighEntropyValues(["model", "platformVersion"]).then((uaData) => {
    //取得したUA情報の処理
  });
}
```
参考: [API Reference](https://developer.mozilla.org/ja/docs/Web/API/NavigatorUAData/getHighEntropyValues)

[HTTPヘッダー](https://developer.mozilla.org/ja/docs/Web/HTTP/Client_hints) によるClient Hintsの取得も可能です


#### iOS14以降のアプリ面配信の場合

下記のパラメータを必要に応じて追加してください

* atts: 0 or 1 or 2 or 3, Optional.
  * [ATTrackingManager.AuthorizationStatus](https://developer.apple.com/documentation/apptrackingtransparency/attrackingmanagerauthorizationstatus) に応じた値
  * notDetermined: 0, restricted: 1, denied: 2, authorized: 3
* skadnvers[]: Optional.
  * 対応可能SKAdNetworkバージョン（端末OSバージョンや、アプリに導入済みの広告SDKバージョンによる）
  * **※現段階ではZucks Ad Networkではバージョン2.0, 2.1, 2.2に対応しております**
  * 例 : `2.2`
* skadnids[]: Optional.
  * アプリのInfo.plistに指定されているSKAdNetworkID
  * Zucks用のID（ `3qcr597p9d.skadnetwork` ）以外は送信いただかなくても問題ありません
  * 例 : `3qcr597p9d.skadnetwork`
* sourceapp: Optional.
  * 広告を掲載するアプリのApp Store ID
  * Zucks Ad Network管理画面より、アプリのストアURLをご登録済みの場合は不要となります
  * 例 : `727497959`

[SKAdNetwork](https://developer.apple.com/documentation/storekit/skadnetwork)による計測に対応する場合、 `skadnvers[]` および `skadnids[]` は必須となります。また、広告掲載アプリにおいて、下記の設定を追加してください。

* 広告掲載アプリのInfo.plist内の[SKAdNetworkIdentifier](https://developer.apple.com/documentation/bundleresources/information_property_list/skadnetworkitems/skadnetworkidentifier)に、ZucksのSKAdNetwrokIDである `3qcr597p9d.skadnetwork` を追加
  * 参考リンク: [Configure a Source App | Apple Developer Documentation](https://developer.apple.com/documentation/storekit/skadnetwork/configuring_the_participating_apps)


## Response: 広告在庫がある場合

Zucks Ad Serverから、JSON文字列を返却します。

### Response Header

* HTTP status: 200 OK
* Content-Type: application/json;charset=UTF-8

### Response Body

* status: String.
  * `ok`
* imp_url: String.
  * インプレッション計測用URL
* type: String.
  * 広告種別
  * `img` / `html`
* skadn: Object. (Optional)
  * SKAdNetwork計測に対応した広告配信に必要な情報

#### type: img

画像広告のときに返却される項目

* img_src: String.
  * バナー画像URL
  * 画像サイズは、広告枠サイズに合わせたものが返却されます
* landing_url: String.
  * 広告タップ時の遷移先URL

#### type: html

HTML広告のときに返却される項目

* html: HTML Document

#### skadn Object

iOS14以降でSKAdNetwork計測に対応した広告の場合に返却される項目

##### SKAdNetwork2.2以降の場合

* version: String
  * 配信時に利用するSKAdNetwrokバージョン
* network: String
  * SKAdNetworkID
* campaign: Integer
  * SKAdNetworkキャンペーンID
* sourceapp: Integer
  * 広告掲載アプリID
* itunesitem: Integer
  * 広告主アプリID
* fidelities: array
  * Fidelity Typeごとの計測用パラメータ
    * fidelity: Integer
      * SKAdNetworkのFidelity Type
        * 1: StoreKit-Rendered Ads
        * 0: View-Through Ads
    * nonce: String
      * 広告配信毎のユニーク値（UUID形式）
    * signature: String
      * 署名文字列
    * timestamp: Integer
      * タイムスタンプ（ミリ秒）

##### SKAdNetwork2.1以前の場合

* version: String
  * 配信時に利用するSKAdNetwrokバージョン
* network: String
  * SKAdNetworkID
* campaign: Integer
  * SKAdNetworkキャンペーンID
* sourceapp: Integer
  * 広告掲載アプリID
* itunesitem: Integer
  * 広告主アプリID
* nonce: String
  * 広告配信毎のユニーク値（UUID形式）
* signature: String
  * 署名文字列
* timestamp: Integer
  * タイムスタンプ（ミリ秒）

## Response: 広告在庫がない場合

### Response Header

* HTTP status: 204 No Content
* Content-Type: application/json;charset=UTF-8

Response Bodyはありません。

## Example

### Request

```
https://sh.zucks.net/opt/api/v3?frameid=_abcdef1234&chm=Pixel%205&chpv=11
```

SKAdNetwork計測に対応している場合

```
https://sh.zucks.net/opt/api/v3?frameid=_abcdef1234&ida=123e4567-e89b-12d3-a456-426655440000&atts=3&ua=Mozilla%2F5.0%20%28iPhone%3B%20CPU%20iPhone%20OS%2014_0%20like%20Mac%20OS%20X%29%20AppleWebKit%2F605.1.15%20%28KHTML%2C%20like%20Gecko%29%20Version%2F14.0%20Mobile%2F15E148%20Safari%2F604.1&chm=Pixel%205&chpv=11&lang=ja&ip=0.0.0.0&skadnvers%5B%5D=2.2&skadnids%5B%5D=3qcr597p9d.skadnetwork
```

### Response

Img ad:

```json
{
    "status": "ok",
    "type": "img",
    "img_src": "https://static.zucks.net.zimg.jp/image/2016/05/11/173606_php1AQ3sH.png",
    "imp_url": "https://k.zucks.net/...",
    "landing_url": "https://k.zucks.net/...",
}
```

HTML ad:

```json
{
    "status": "ok",
    "type": "html",
    "html": "<div><a href='...'>...</div>",
    "imp_url": "https://k.zucks.net/..."
}
```

Img ad - SKAdNetwork計測に対応:

```json
{
    "status": "ok",
    "type": "img",
    "img_src": "https://static.zucks.net.zimg.jp/image/2016/05/11/173606_php1AQ3sH.png",
    "imp_url": "https://k.zucks.net/...",
    "landing_url": "https://k.zucks.net/...",
    "skadn": {
        "version": "2.2",
        "network": "3qcr597p9d.skadnetwork",
        "campaign": 1,
        "sourceapp": 525463029,
        "itunesitem": 727497959,
        "fidelities": [
            {
                "fidelity": 0,
                "nonce": "271ad367-af41-8fcf-d958-411b2fbb0e05",
                "signature": "MEQCIAh8FmnrCY8g4kIhgejdFFcvqwkZV/PGjiuYyhulbZzlAiBTNp+iPZXXhhXXmpee4MmWh+eWTFypDN96YoBUCpdcKQ==",
                "timestamp": 1611555668665
            },
            {
                "fidelity": 1,
                "nonce": "6aafb7a5-0170-41b5-bbe4-fe71dedf1e28",
                "signature": "MEQCIEQlmZRNfYzKBSE8QnhLTIHZZZWCFgZpRqRxHss65KoFAiAJgJKjdrWdkLUOCCjuEx2RmFS7daRzSVZRVZ8RyMyUXg==",
                "timestamp": 1611555668665
            }
        ]
    }
}
```

HTML ad - SKAdNetwork計測に対応:

```json
{
    "status": "ok",
    "type": "html",
    "html": "<div><a href='...'>...</div>",
    "imp_url": "https://k.zucks.net/...",
    "skadn": {
        "version": "2.2",
        "network": "3qcr597p9d.skadnetwork",
        "campaign": 1,
        "sourceapp": 525463029,
        "itunesitem": 727497959,
        "fidelities": [
            {
                "fidelity": 0,
                "nonce": "271ad367-af41-8fcf-d958-411b2fbb0e05",
                "signature": "MEQCIAh8FmnrCY8g4kIhgejdFFcvqwkZV/PGjiuYyhulbZzlAiBTNp+iPZXXhhXXmpee4MmWh+eWTFypDN96YoBUCpdcKQ==",
                "timestamp": 1611555668665
            },
            {
                "fidelity": 1,
                "nonce": "6aafb7a5-0170-41b5-bbe4-fe71dedf1e28",
                "signature": "MEQCIEQlmZRNfYzKBSE8QnhLTIHZZZWCFgZpRqRxHss65KoFAiAJgJKjdrWdkLUOCCjuEx2RmFS7daRzSVZRVZ8RyMyUXg==",
                "timestamp": 1611555668665
            }
        ]
    }
}
```


## Rendering the Ad

* 広告のサイズは、枠サイズに合うものが配信されます
  * 表示領域のサイズは、登録した枠サイズ（320x50, 300x250等）と同じサイズを確保してください

### Img ad

`img_src` は、png/jpg/gif(アニメーション含む)などの画像ファイルを示すURLです。そのURLから画像を取得し、内容を表示してください。
`img_src` の画像ファイルの内容は不変です。必要に応じてキャッシュして利用することができます。

### HTML ad

`html` の内容を、ブラウザ/WebViewにレンダリングしてください。


## Firing Impressions

Zucks Ad Networkでは、ビーコン送信によりインプレッションを計測しています。

Img ad, HTML adともに、広告領域をレンダリングした直後、 `imp_url` のURLにGETリクエストを送信してください。

* End point
  * `imp_url` のURL
* Method
  * GET
* ブラウザ/WebView内から直接リクエストを送る場合、Cookie情報も付与するようにリクエストしてください
  * Fetch API: `credentials` オプションを `include` に設定
  * XMLHttpRequest: `withCredentials` プロパティを `true` に設定

### SKAdNetwork計測に対応した広告の場合（SKAdNetwork2.2以降のView-through Ad）

`skadn` 内の情報を元に、Apple公式の手順通り、SKAdImpressionも呼び出してください。 `fidelity` が0のものを利用してください。

* [Signing and Providing Ads](https://developer.apple.com/documentation/storekit/skadnetwork/signing_and_providing_ads)


## Firing Clicks

### Img ad

広告バナータップ時、`landing_url` のURLをブラウザで開くようにしてください。

### HTML ad

アプリ内に掲載している場合、広告タップ時の遷移先URLをブラウザで開くようにしてください。

### SKAdNetwork計測に対応した広告の場合

`skadn` 内の情報を元に、Apple公式の手順通り、StoreKitの表示を行ってください。SKAdNetwork2.2以降の場合、 `fidelity` が1のものを利用してください。

* [Signing and Providing Ads](https://developer.apple.com/documentation/storekit/skadnetwork/signing_and_providing_ads)

またレポート集計のために、本来の遷移先URLに対してもバックグラウンドで通知を行うようにしてください。この際リダイレクトは追従するようにしてください。


## Check List

ご利用開始までに確認すべきチェック項目です。

**広告収益の算出ができない場合がありますので、必ず確認してください** 。

- [ ] 広告が表示された
- [ ] Zucks Ad Network管理画面で、インプレション数が増えた
- [ ] 広告タップ後、ブラウザでランディングページが表示された
- [ ] Zucks Ad Network管理画面で、クリック数が増えた


## Trouble Shooting

よくあるトラブルに対する確認項目です。

### 広告が表示されません

- [ ] Request Parameterの `frameid` 値は正しいですか？
- [ ] Request Headerの `User-Agent` 値またはRequest Parameterの `ua` 値は、デフォルトブラウザに準じた値ですか？
- [ ] Zucks Ad Network管理画面上の広告枠設定は正しいですか？

該当枠に対する広告在庫がない場合もございます。詳しくはZucks営業担当にお問い合わせください。

### インプレッションがカウントされません

- [ ] Request Parameterの `frameid` 値は正しいですか？
- [ ] `imp_url` 値のURLにリクエストを発行していますか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく待ってみてください。

### クリックがカウントされません

- [ ] Img adの場合、`landing_url` 値のURLにリクエストを発行していますか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく待ってみてください。

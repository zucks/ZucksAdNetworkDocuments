# Zucks Ad Network Report API Specification v6 for Advertising Agency

This is an older version. Please check the latest version [here](../../).

### Overview

This describes the resources that make up the official Zucks Ad Network Report API v6.

# request

In order to use the report API, you need to issue an API key for authentication. Please contact our person in charge to issue API key, reissue etc.

A request to retrieve a report can be made using the curl command as follows, for example.

```Sh
$ curl -H 'x-api-key:<api-key>' "https://ms.zucksadnetwork.com/web_api/agent/report/v6?date=<date>"
```

### API Request Specification

| | |
| --- | --- |
| URL | `https://ms.zucksadnetwork.com/web_api/agent/report/v6` |
| HTTP method | GET |
| GET parameter | `date`: target date / time to be acquired (eg" 2017-04-20 ") |
| Header | `x-api-key`: API key for authentication with report API |



# Response

Returns the report data of the date and time specified by the GET parameter in CSV format

Character code: UTF-8

| Index | type | item name |
| --- | --- | --- |
| 1 | string | report date (YYYY-MM-DD) |
| 2 | decimal | amount of payment (yen) |
| 3 | integer | number of impression |
| 4 | integer | number of click |
| 5 | integer | number of conversion (CV) |
| 6 | decimal | CPC unit price |
| 7 | string | order ID |
| 8 | string | order name |
| 9 | string | advertiser |
| 10 | string | campaign ID |
| 11 | string | campaign name |
| 12 | string | creative ID |
| 13 | string | creative name |
| 14 | string | ad landing page URL |
| 15 | integer | creative type (1: image, 2: third-party delivery, 4: native) |
| 16 | string | creative image URL |
| 17 | string | native ad title (empty string except creative type 4) |
| 18 | string | native ad text (empty string except creative type 4) |
| 19 | string | OS (iOS, Android, ---) |
| 20 | string | Ad size (320x50 format) |

### Status code

| HTTP status code | description |
| -------------------- | ---- |
| 200 | success |
| 204 | If report for the target date (for example, specify a date and time that is not being delivered, uncalculated, etc.) can't be found |
| 401 | Authentication failed |


#### Response Example

```Sh
$ curl -H 'x-api-key:<api-key>' "https://ms.zucksadnetwork.com/web_api/agent/report/v6?date=2017-04-20"
"2017-04-20","6407.00","6179","527","204","12.16","1","【TEST】テストオーダー","【TEST】テスト広告主","1","【TEST】テストキャンペーン","1","【TEST】テストクリエイティブ","https://zucks.co.jp/","1","---","","","---","320×50"
```


-------------------------------------------------------------------- --------------

※ If you have any questions, please contact our person in charge.

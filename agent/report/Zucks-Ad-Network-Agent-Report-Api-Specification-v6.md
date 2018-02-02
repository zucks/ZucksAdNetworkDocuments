# Zucks Ad Network 代理店向けレポートAPI仕様書 v6

こちらは旧バージョンのAPIとなります。
新しいバージョンの[Zucks Ad Network 代理店様向けレポートAPI仕様書 v7](Zucks-Ad-Network-Agent-Report-Api-Specification-v7.md)を公開しているのでご利用ください。

### 概要

Zucks Ad Networkのレポートを取得するAPIです。

# リクエスト

レポートAPIを使用するためには認証用APIキーを発行する必要があります。APIキーの発行、再発行等は弊社担当者までご連絡ください。

レポート取得のリクエストは例えばcurlコマンドを用いて次のようにできます。

``` sh
$ curl -H 'x-api-key:<api-key>' "https://ms.zucksadnetwork.com/web_api/agent/report/v6?date=<date>"
```

### APIリクエストの仕様

|     |     | 
| --- | --- |
| URL | `https://ms.zucksadnetwork.com/web_api/agent/report/v6` |
| HTTPメソッド | GET |
| GETパラメータ | `date` : 取得したい対象日時 (例: "2017-04-20") |
| ヘッダー | `x-api-key` : レポートAPIで認証するためのAPIキー |



# レスポンス

GETパラメータで指定した日時のレポートデータをCSV形式で返します

文字コード: UTF-8

| index | 型 | 項目名 |
| --- | --- | --- |
| 1 | string | レポート日付 (YYYY-MM-DD) |
| 2 | decimal | 発生金額 |
| 3 | integer | imp数 |
| 4 | integer | click数 |
| 5 | integer | CV数 |
| 6 | decimal | CPC単価 |
| 7 | string | オーダーID |
| 8 | string | オーダー名 |
| 9 | string | 広告主 |
| 10 | string | キャンペーンID |
| 11 | string | キャンペーン名 |
| 12 | string | クリエイティブID |
| 13 | string | クリエイティブ名 |
| 14 | string | 入稿URL |
| 15 | integer | クリエイティブタイプ （1:画像、 2:第三者配信、 4:ネイティブ） |
| 16 | string | クリエイティブ画像のURL |
| 17 | string | ネイティブ広告のタイトル（クリエイティブタイプが4以外は空文字） |
| 18 | string | ネイティブ広告のテキスト（クリエイティブタイプが4以外は空文字） |
| 19 | string | 配信OS （iOS、 Android、 ---） |
| 20 | string | 広告サイズ （320×50 の形式） |

### ステータスコード

| HTTPステータスコード | 説明 |
| -------------------- | ---- |
| 200                  | 成功 |
| 204                  | 対象日のレポートが見つからない場合(配信していない日時を指定、未集計など) |
| 401                  | 認証に失敗 |


#### レスポンス例

``` sh
$ curl -H 'x-api-key:<api-key>' "https://ms.zucksadnetwork.com/web_api/agent/report/v6?date=2017-04-20"
"2017-04-20","6407.00","6179","527","204","12.16","1","【TEST】テストオーダー","【TEST】テスト広告主","1","【TEST】テストキャンペーン","1","【TEST】テストクリエイティブ","https://zucks.co.jp/","1","---","","","---","320×50"
```


----------------------------------------------------------------

※ ご不明な点がございましたら、弊社担当者までお問いわせください。


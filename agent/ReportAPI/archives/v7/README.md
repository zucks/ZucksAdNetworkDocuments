# Zucks Ad Network 代理店様向けレポートAPI仕様書 v7

リリース日: 2017/07/24

**こちらは過去のバージョンになります。最新版は[こちら](../../)からご確認ください。**

### 概要

Zucks Ad Networkのレポートを日単位で取得するAPIです。  


# レポートAPI仕様

### リクエスト

レポートはHTTPリクエストによって取得することが出来ます。

レポートAPIは弊社発行のAPI認証トークンが必要です。  
API認証トークンの発行・再発行は弊社担当までご連絡ください。

#### URL

```
https://ms.zucksadnetwork.com/web_api/agent/report/v7?date={date}
```

| GETパラメータ | 値 | 
| --- | --- |
| date | YYYY-MM-DDで表されたレポート日付 (例: "2017-07-24") |

#### HTTPメソッド

GET

#### HTTPヘッダー

| ヘッダー | 値 | 
| --- | --- |
| x-api-key | 弊社発行のAPI認証トークン |

#### リクエストサンプル

下記はcurlコマンドを用いる場合です。

``` sh
$ curl -H 'x-api-key:abcdef12-3456-....' -X GET 'https://ms.zucksadnetwork.com/web_api/agent/report/v7?date=2017-07-24'
```

### レスポンス

CSV形式のレポートを返します。  

#### HTTPステータスコード

| コード | 概要 | 詳細 |
| --- | ---- | ----- |
| 200 | レポート取得成功 | |
| 204 | レポートが存在しない | 配信実績が無い場合や、集計がまだ終わっていない可能性があります。 |
| 400 | パラメータが不正 | GETパラメータが正しく付与されているかご確認ください。 |
| 401 | 認証に失敗 | HTTPヘッダーにAPI認証トークンが設定されているかご確認ください。 |
| 404 | リソースが存在しない | URLが正しいかご確認ください。 |
| 5xx | サーバーエラー | 連続して発生する場合は、お手数ですが弊社担当までお問い合わせください。 |

#### CSV文字コード

UTF-8

#### CSV列の詳細

| 列 | 型 | 項目名 | 補足 |
| --- | --- | --- | --- |
| 1 | string | レポート日付 | YYYY-MM-DD |
| 2 | decimal | 発生金額 | |
| 3 | integer | imp数 | |
| 4 | integer | click数 | |
| 5 | integer | CV数 | |
| 6 | integer | VTCV数 | |
| 7 | decimal | CPC単価 | |
| 8 | string | オーダーID | |
| 9 | string | オーダー名 | |
| 10 | string | 広告主様 | |
| 11 | string | キャンペーンID | |
| 12 | string | キャンペーン名 | |
| 13 | string | クリエイティブID | |
| 14 | string | クリエイティブ名 | |
| 15 | string | 入稿URL | |
| 16 | integer | クリエイティブタイプ | 1:画像、 2:第三者配信、 4:ネイティブ |
| 17 | string | クリエイティブ画像のURL | |
| 18 | string | ネイティブのタイトル | クリエイティブタイプが4以外は空文字 |
| 19 | string | ネイティブのテキスト | クリエイティブタイプが4以外は空文字 |
| 20 | string | 配信OS | iOS、 Android、 --- |
| 21 | string | 広告サイズ | 320×50 の形式 |

#### レスポンス例

```
$ curl -H 'x-api-key:abcdef12-3456-....' -X GET 'https://ms.zucksadnetwork.com/web_api/agent/report/v7?date=2017-07-24'
"2017-07-24","6407.00","6179","527","204","12","12.16","1","【TEST】テストオーダー","【TEST】テスト広告主","1","【TEST】テストキャンペーン","1","【TEST】テストクリエイティブ","https://zucks.co.jp/","1","---","","","---","320×50"
```

### FAQ

##### レポートはいつ集計されますか？

対象日のレポートは、翌日午前4時頃までに集計が行われます。

* 2017-07-24のレポート => 2017-07-25 午前4時 集計完了

##### 集計が終わっているはずなのに、204が返却される

対象日に配信実績が無い場合も、空のレポートとなるため204を返却します。

※ その他ご不明な点がございましたら、弊社担当までお問いわせください。

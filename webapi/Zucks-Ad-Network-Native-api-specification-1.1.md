# Zucks Ad Network Native Ad Specification


## Request

* End point: http://sh.zucks.net/opt/native/api/v1
* Method: GET

### Request Headers:
* User-Agent: String, Required.
  * デフォルトブラウザと同等のものを送信してください
  * 末尾に独自の付加情報が追加されていても、許容されます
  * Parameter `ua` が設定されている場合、Parameter側の設定が優先されます
* Referer: Optional.
  * Parameter `ref` が設定されている場合、Parameter側の設定が優先されます
* Accept-Language: Optional.
  * Parameter `lang` が設定されている場合、Parameter側の設定が優先されます
* Cookie: Optional.

### Request Parameters:
* frameid: String, Requierd.
  * 枠ID. Zucks Ad Network担当者にご確認ください.
* ida: IDFA(iOS) or Advertising ID(Android), Optional.
  * 現在未対応、将来的に対応予定です
  * 追跡型広告を制限している場合には **送信しない** ようにしてください
* ua: String, Optional.
  * Headerと異なるUser-Agentを利用する場合に設定してください
* ref: String, Optional.
  * Headerと異なるRefererを利用する場合に設定してください
* lang: Optional.
  * Headerと異なるAccept-Languageを利用する場合に設定してください


### その他

* ブラウザ/WebView内からの XmlHttpRequest を使ってリクエストを送る場合:
  * `withCredentials` 属性を `true` にてリクエストしてください


## Response

Zucks Ad Serverから、JSON文字列を返します。

### Response Header

* HTTP status: 200 OK
* Content-Type: application/json;charset=UTF-8

### Response Body

* status: String. `ok` / `no_ad`.
* imp_url: URL. インプレッション計測用エンドポイント.
* type: String. `native`.
* image_src: URL. Banner image src.
  * 画像は正方形サイズとなります。縦横比を保って表示してください。
* width: Integer. 画像の横幅.
* height: Integer. 画像の高さ.
* landing_url: URL. 広告タップ時の遷移先URL.
* text: String. 広告の本文.長さ1～28の文字列.
* sub_text: String. 追加で表示可能なテキスト.長さ0～14の文字列.


#### status: no_ad

配信できる広告案件がないときに返されます。案件がない場合でも、 HTTP status: 200 OK で返します。

`imp_url` 値を後述 Firing Impressions にしたがって処理することで、Zucks Ad Network管理画面上で「配信した広告がなかった」数としてカウントします。

## Example

### Request

```
http://sh.zucks.net/opt/api/v1?frameid=723&ida=xxxx-xxxx-xxxx-xxxx-xxxx
```

### Response

Native ad:

```json
{
    "status": "ok",
    "type": "native",
    "image_src": "http:\u002F\u002Fstatic.zucks.net.zimg.jp\u002Fimage\u002F2015\u002F01\u002F29\u002F162401_phphVPOnl.png",
    "width": "114",
    "height": "114",
    "imp_url": "http:\u002F\u002Fk.zucks.net\u002F...",
    "landing_url": "http:\u002F\u002Fk.zucks.net\u002F...",
    "text": "こちらはネイティブ広告の本文となります",
    "sub_text": "こちらは追加の文言です"
}
```

## Rendering the Ad

`image_src` は、png/jpg/gifなどの画像ファイルを示すURLです。そのURLから画像を取得し、内容を表示してください。
`image_src` の画像ファイルの内容は不変です。必要に応じてキャッシュして利用することができます。
`text` は全ての広告について設定されていますので、必ず表示するようにしてください。
`sub_text` は追加で利用することができるテキストです。広告によっては空文字の場合があります。

## Firing Impressions

Zucks Ad Networkでは、ビーコン送信によりインプレッションを計測しています。

広告領域をレンダリングした直後、 `imp_url` のURLにGETリクエストを送信してください。

* End point: `imp_url` のURL
* Method: GET
* ブラウザ/WebView内からの XmlHttpRequest を使ってリクエストを送る場合:
  * `withCredentials` 属性を `true` にてリクエストしてください。


## Firing Clicks

広告タップ時、`landing_url` のURLでデフォルトブラウザを開いてください。


## Check List

ご利用開始までに確認すべきチェック項目です。

**広告収益の算出ができない場合がありますので、必ず確認してください** 。

- [ ] Native広告が表示された
- [ ] Zucks Ad Network管理画面で、インプレッション数が増えた
- [ ] クリック後、デフォルトブラウザでランディングページが表示された
- [ ] Zucks Ad Network管理画面で、クリック数が増えた


## Trouble Shooting

よくあるトラブルに対する確認項目です。

### no_ad しか返りません

- [ ] Request Parameterの `frameid` 値は正しいですか？
- [ ] Request Headerの `User-Agent` 値またはRequest Parameterの `ua` 値は、デフォルトブラウザに準じた値ですか？
- [ ] Zucks Ad Network上の設定は正しいですか？

該当枠に対する広告在庫がない場合もございます。詳しくはZucks営業担当にお問い合わせください。

### インプレッションがカウントされません

- [ ] Requestの `frameid` 値は正しいですか？
- [ ] `imp_url` 値のURLにリクエストを発行していますか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく待ってみてください。

### クリックがカウントされません

- [ ] `landing_url` 値のURLにリクエストを発行していますか？
- [ ] そのレスポンスは `302 Moved Temporarily` ですか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく待ってみてください。

# Zucks Ad Network WEB API Specification


## Request

* End point: http://sh.zucks.net/opt/api/v1
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
* Origin: Optional.
  * `withCredentials` 属性を `true` にてリクエストする場合は設定してください
* Cookie: Optional.

### Request Parameters:
* frameid: Integer, Requierd.
  * 枠ID. Zucks Ad Network担当者にご確認ください.
* ida: IDFA(iOS) or Advertising ID(Android), Optional.
  * 現在未対応、将来的に対応予定です
* lat: Limit Ad Tracking, Optional.
  * 現在未対応、将来的に対応予定です
* ua: String, Optional.
* ref: String, Optional.
* lang: Optional.
* ip: Source IP address, Optional

### その他

* ブラウザ/WebView内からの XmlHttpRequest を使ってリクエストを送る場合:
  * `withCredentials` 属性を `true` にてリクエストしてください
  * `Request Headers` の `Origin` を設定してください


## Response

Zucks Ad Serverから、JSON文字列を返します。

### Response Header

* HTTP status: 200 OK
* Content-Type: application/json;charset=UTF-8

### Response Body

* status: String. `ok` / `no_ad`.
* imp_url: URL. インプレッション計測用エンドポイント.
* type: String. `img` / `html`.

#### status: no_ad

配信できる広告案件がないときに返されます。案件がない場合でも、 HTTP status: 200 OK で返します。

`imp_url` 値を後述 Firing Impression にしたがって処理することで、Zucks Ad Network管理画面上で「配信した広告がなかった」数としてカウントします。

#### type: img

画像バナー広告のときに返されます:

* image_src: URL. Banner image src.
  * 画像サイズは、予め入稿いただいた枠サイズに合わせたものが配信されます
* landing_url: URL.
  * 広告タップ時の遷移先URL

#### type: html

HTML広告のときに返されます:

* HTML: HTML Document.


## Example

### Request

```
http://sh.zucks.net/opt/api/v1?frameid=723&ida=xxxx-xxxx-xxxx-xxxx-xxxx&
```

### Response

Img ad:

```json
{
    "status": "ok",
    "type": "img",
    "image_src": "http://static.zucks.net.zimg.jp/image/2014/10/28/135005_\u738b\u5bae_320\u00d750(L)_072.jpg.jpeg",
    "imp_url": "http://k.zucks.net/...",
    "landing_url": "http://k.zucks.net/..."
}
```

HTML ad:

```json
{
    "status": "ok",
    "type": "html",
    "html": "<!DOCTYPE html><html><head>...</head><body>...</body></html>",
    "imp_url": "http://k.zucks.net/..."
}
```


## Rendering the Ad

* 広告のサイズは、予めZucks Ad Networkに登録した枠サイズに合うものが配信されます。
  * 表示領域のサイズは、登録した枠サイズ（320x50, 300x250等）と同じサイズを確保してください。
* **information iconについてどうするか検討**

### Img ad

`image_src` は、png/jpg/gifなどの画像ファイルを示すURLです。そのURLから画像を取得し、内容を表示してください。
`image_src` の画像ファイルの内容は不変です。必要に応じてキャッシュして利用することができます。

### HTML ad

`html` の内容を、 WebViewにレンダリングしてください。


## Firing Impressions

Zucks Ad Networkでは、ビーコン送信によりインプレッションを計測しています。

Img ad, HTML adともに、広告領域をレンダリングした直後、 `imp_url` のURLにGETリクエストを送信してください。

* End point: `imp_url` のURL
* Method: GET
* ブラウザ/WebView内からの XmlHttpRequest を使ってリクエストを送る場合:
  * `withCredentials` 属性を `true` にてリクエストしてください。


## Firing Clicks

### Img ad

広告バナータップ時、`landing_url` のURLでデフォルトブラウザを開いてください。

### HTML ad

広告の成果測定のため、広告ページタップ時の遷移先はデフォルトブラウザで開いてください。


## Check List

ご利用開始までに確認すべきチェック項目です。

**広告収益の算出ができない場合がありますので、必ず確認してください** 。

- [ ] Img広告が表示された
- [ ] HTML広告が表示された
- [ ] Zucks Ad Network管理画面で、インプレション数が増えた
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

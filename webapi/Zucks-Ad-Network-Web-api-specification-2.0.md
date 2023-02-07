# Zucks Ad Network WEB API Specification


## Request

* End point: http://sh.zucks.net/opt/api/v2
* Method: GET

### Request Headers:

* `User-Agent`: String, Required.
  * デフォルトブラウザと同等のものを送信してください
  * 末尾に独自の付加情報が追加されていても、許容されます
  * Parameter `ua` が設定されている場合、Parameter側の設定が優先されます
  * 付加されていないまたは標準のものと大きく異なる場合、HTTP Status `406 Not Acceptable` を返し、広告案件は配信されません
* `Referer`: Optional.
  * Parameter `ref` が設定されている場合、Parameter側の設定が優先されます
* `Accept-Language`: Optional.
  * Parameter `lang` が設定されている場合、Parameter側の設定が優先されます
* `Origin`: Optional.
  * `withCredentials` 属性を `true` にてリクエストする場合は設定してください
* `Cookie`: Optional.

### Request Parameters:

* `frameid`: String, Requierd.
  * 枠ID. Zucks Ad Network担当者にご確認ください.
  * 間違った値を指定した場合、HTTP Status `400 Bad Request` を返し、広告は配信されません
* `ida`: String, Optional.
  * IDFA(iOS) or Advertising ID(Android)
  * Parameter `ida` を送信する場合、iOS13以前やAndroidでは後述のParameter `lat` も送信してください
* `lat`: 0 or 1, Optional.
  * 「広告トラッキング制限」が無効な場合: 0
  * 「広告トラッキング制限」が有効な場合: 1
* `ua`: String, Optional.
  * 付加されていないまたは標準のものと大きく異なる場合、HTTP Status `406 Not Acceptable` を返し、広告は配信されません
* `chm`: String, Optional.
  * ブラウザのユーザエージェントクライアントヒントAPI機能(以下, Client Hints)によって取得できる端末モデル名
  * [Client Hintsの取得](#Client-Hintsの取得)
* `chpv`: String, Optional.
  * ブラウザのユーザエージェントクライアントヒントAPI機能(以下, Client Hints)によって取得できるプラットフォームバージョン(OSバージョン)
  * [Client Hintsの取得](#Client-Hintsの取得)
* `ref`: String, Optional.
* `lang`: Optional.
* `ip`: String, Optional.
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

### その他

* ブラウザ/WebView内からの XmlHttpRequest を使ってリクエストを送る場合:
  * `withCredentials` 属性を `true` にてリクエストしてください
  * `Request Headers` の `Origin` を設定してください


## Response: 広告在庫がある場合

Zucks Ad Serverから、JSON文字列を返します。

### Response Header

* HTTP status: 200 OK
* Content-Type: application/json;charset=UTF-8

### Response Body

* status: String. `ok`
* imp_url: URL. インプレッション計測用エンドポイント.
* type: String. `img` / `html`.

#### type: img

画像バナー広告のときに返されます:

* img_src: URL. Banner image src.
  * 画像サイズは、予め入稿いただいた枠サイズに合わせたものが配信されます
* landing_url: URL.
  * 広告タップ時の遷移先URL

#### type: html

HTML広告のときに返されます:

* HTML: HTML Document.


## Response: 広告在庫がない場合

### Response Header

* HTTP status: 204 No Content
* Content-Type: application/json;charset=UTF-8

Response Bodyはありません。

## Example

### Request

```
http://sh.zucks.net/opt/api/v2?frameid=_abcdef1234&chm=Pixel%205&chpv=11.0.0
```

### Response

Img ad:

```json
{
    "status": "ok",
    "type": "img",
    "img_src": "http://static.zucks.net.zimg.jp/image/2014/10/28/135005_\u738b\u5bae_320\u00d750(L)_072.jpg.jpeg",
    "imp_url": "http://k.zucks.net/...",
    "landing_url": "http://k.zucks.net/..."
}
```

HTML ad:

```json
{
    "status": "ok",
    "type": "html",
    "html": "<div><a href='...'>...</div>",
    "imp_url": "http://k.zucks.net/..."
}
```


## Rendering the Ad

* 広告のサイズは、予めZucks Ad Networkに登録した枠サイズに合うものが配信されます。
  * 表示領域のサイズは、登録した枠サイズ（320x50, 300x250等）と同じサイズを確保してください。

### Img ad

`img_src` は、png/jpg/gif(アニメーション含む)などの画像ファイルを示すURLです。そのURLから画像を取得し、内容を表示してください。
`img_src` の画像ファイルの内容は不変です。必要に応じてキャッシュして利用することができます。

### HTML ad

`html` の内容を、ブラウザ/WebViewにレンダリングしてください。


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

> `landing_url` をリクエストすると、
>
> * クリックのカウント
> * コンバージョン計測用のcookieを作成
>
> しつつ、HTTP Status `302 Moved Temporarily` と、リダイレクト先として広告主のURLを返します。
> その後、ブラウザの標準の動作によって、広告主のランディングページへと到達します。

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

### 広告が表示されません

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
- [ ] 標準ブラウザで開いていますか？
- [ ] そのレスポンスは `302 Moved Temporarily` ですか？
- [ ] 管理画面上のレポーティングには多少のタイムラグがあります。しばらく待ってみてください。

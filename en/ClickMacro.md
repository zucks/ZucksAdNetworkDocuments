# Click Macro

We have 2 click macros, `%CLICK_URL%` and `%CLICK_URL_ENC%`.

To specify your destination URL, join url-encoded URL with Javascirpt's encodeURIComponent() or else just after Click Macro. If destination URL is not provided, our ad server returns 1x1 gif image as a beacon.

## %CLICK_URL%

Render a simple click URL.

Example:
* your destination URL: http://example.com
 - your tag: `<a href="%CLICK_URL%http%3A%2F%2Fexample.com">click here</a>`
  - <a href="//k.zucks.net/rd/?k=hoge&p=fuga&v=piyo&rd=http%3A%2F%2Fexample.com">here</a>

## %CLICK_URL_ENC%

Render a url-encoded click URL.

Example:
* your iFrame URL: http://example.com/iframe?click=...
* destination URL: http://example.com
 - your tag: `<iframe src="http://example.com/iframe?click=%CLICK_URL_ENC%http%3A%2F%2Fexample.com" />`
  - will rendered: <iframe src="http://example.com/iframe?click=%CLICK_URL_ENC%http%3A%2F%2Fexample.com" />`


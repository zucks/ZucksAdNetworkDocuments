# Zucks Ad Network / IDFA Targeting Ad

### 仕様

以下の構造を持った `zucksIdfa` オブジェクトをJavaScriptのグローバル変数をZucksの配信スクリプトの読み込み前に宣言することで、広告IDを用いたターゲティング広告を配信することが出来ます。

* ida
  * 広告ID(Apple IDFAかGoogle Advertising IDのどちらかを設定してください)
  * string
  * 必須
* lat
  * Limit Ad Tracking。デバイスのリマーケティングが制限されているかどうか。
    * この値が`1`の場合は、広告IDを用いたターゲティングを行なわず、通常の広告が表示されます。
  * `0` or `1`(制限をしている場合は`1`を指定)
  * 必須

※ `zucksIdfa` オブジェクトが存在しない、存在するが仕様を満たしていない場合、広告IDを用いない広告が配信されたり、広告の配信を停止することがあります。

### 実装例

```html
<script type="text/javascript”>
zucksIdfa = {
  ida: "abcedfg",
  lat: 1
}
</script>

<!-- ここから先はZucksの配信スクリプト -->
<script type="text/javascript" src="http://j.zucks.net.zimg.jp/j?f=xxx"></script>
```

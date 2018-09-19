# Zucks Ad Network AMP広告タグについて

### 前提
 - メディアのサイトがAMPに対応している必要があります


### 導入

###### 1. Zucksアドネットワークの管理画面で枠を作成します

###### 2. Zucksアドネットワーク用のamp-adタグを作ります
```html
<amp-ad
    width="{WIDTH}"
    height="{HEIGHT}"
    type="zucks"
    data-frame-id="{FRAME-ID}">
</amp-ad>
```

- 必要なパラメータにそれぞれの値を置き換えます
    - WIDTH、HEIGHTは枠のサイズ
    - FRAME-IDはZucksアドネットワーク管理画面のメディア/広告枠管理ページの`Frame ID`で取得してください
    - ネイティブ広告の場合はdata-ad-typeを指定する必要があります

例：
#### バナー広告

```html
<amp-ad
    width="320"
    height="50"
    type="zucks"
    data-frame-id="_abc123">
</amp-ad>
```    

#### ネイティブ広告

```html
<amp-ad
    width="320"
    height="50"
    type="zucks"
    data-adtype="native"
    data-frame-id="_abc123">
</amp-ad>
```    

###### 3. サイトにAMPタグを貼り付ける

詳細はこちら：https://www.ampproject.org/docs/reference/amp-ad.html


### 参考
- https://ampbyexample.com/components/amp-ad/
- https://www.ampproject.org/docs/reference/amp-ad.html

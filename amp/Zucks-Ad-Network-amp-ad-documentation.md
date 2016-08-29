# Zucks Ad Network AMP広告タグについて

### 前提
 - メディアのサイトがAMPに対応している必要があります
 - 対応する枠の種類:
    - インラインのみ


### 導入

###### 1. Zucksアドネットワークの管理画面で枠を作成します

###### 2. Zucksアドネットワーク用のamp-adタグを作ります
```html
<amp-ad
    width="{WIDTH}"
    height="{HEIGHT}"
    type="zucks"
    data-frame_id="{HID}">
</amp-ad>
```

- 必要なパラメータにそれぞれの値を置き換えます
    - WIDTH、HEIGHTは枠のサイズ
    - HIDはZucksアドネットワークの管理画面の枠詳細ページで取得してください

例：
```html
<amp-ad
    width="320"
    height="50"
    type="zucks"
    data-frame_id="_abc123">
</amp-ad>
```    


###### 3. サイトにAMPタグを貼り付ける

- 注意   
    - AMP広告タグを適応するため、サイトに下記のスクリプトを入れる必要があります

```html
<script async custom-element="amp-ad" src="https://cdn.ampproject.org/v0/amp-ad-0.1.js"></script>
```


### 参考
- https://ampbyexample.com/components/amp-ad/
- https://www.ampproject.org/docs/reference/amp-ad.html

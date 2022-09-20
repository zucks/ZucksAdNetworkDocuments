# Zucks Ad Network 代理店向けIDFA/AdID リストアップロード API 仕様書 v1.1

リリース日: 2017/02/01

## 概要

Zucks AdNetwork の配信でターゲティングとして利用したい IDFA/Android advertising ID (以下 ida) のリストをアップロードするためのAPIです。

Zucks AdNetwork では以下のAPIを提供します。

* idaリストのアップロード
* idaリストの更新
* idaリストのステータス確認

## 準備

* Zucks AdNetwork idaリスト用 APIキー
  * 不明な場合は Zucks 担当者にご確認ください

## 認証

* 管理画面で確認できる idaリスト用 APIキーをリクエストヘッダに使った認証
  * 将来的にAPIキーは管理画面から再発行、無効化を行えるようにする予定

## リクエスト例

    curl  -X POST <URL> -H "x-api-key:${API_KEY}" -d '<PAYLOAD>'

## PAYLOAD形式

リクエストのPAYLOADはJSON(UTF-8)の形式で送信してください。

## レスポンス形式

レスポンスのコンテンツはJSONで返却されます。

また、マルチバイト文字はUnicodeエスケープシーケンスの形式で返却されますが、以降のレスポンス例では 便宜上デコード後のマルチバイト文字を記載しています。

## idaリストのアップロード

idaのリストをアップロードし、ターゲティングで利用するための準備を行います。
idaリストを配信設定で指定することで配信時のターゲティングとして利用されます。

ターゲティングとして利用できるようになるためにはアップロードしてから最大15分程度掛かります。
利用できる状態かどうかは、ステータス確認APIを使用して確認することができます。

### URL

    https://ms.zucksadnetwork.com/web_api/ida


  * リクエストメソッド: POST

### PAYLOAD

 * name: アップロードした idaリストの名前 (120文字以内)、リスト名はユニークである必要があります (必須)
 * advertiser_id: idaリストを利用する広告主ID(管理画面から確認できます) (必須)
 * ida: 配列の要素でidaを指定 (必須)

```json
{
  "name": "10日起動していないユーザー_2015-10-30",
  "advertiser_id": 10324,
  "ida":["aaaaaaaa-bbbb-cccc-1111-222222220000","aaaaaaaa-bbbb-cccc-1111-222222220001"]
}
```

### レスポンス

アップロードが成功したかどうかはHTTPのレスポンスのステータスコードが200かどうかで確認できます。
空文字列の場合はその文字列は取り込まずスキップします。

#### ステータスコード

 * 200: 正常に終了
 * 400: データのバリデーションエラーなどの場合
 * 401: 認証に失敗した場合

#### レスポンス内容

* 成功した場合
  * list_id: 作成されたidaリストID、更新時にこのIDを指定することでidaリストを更新することができます
  * created_at: リストを作成した日時
  * expired_at: リストの有効最終日時
  * request_at: APIをリクエストされた日時

```json
{
  "list_id": 1,
  "created_at": "2015-04-01T12:00Z",
  "expired_at": "2015-06-01T12:00Z",
  "request_at": "2015-05-01T12:00Z"
}
```

 * 失敗した場合

```json
{
  "error": ["Requested ida parameter is invalid. Reconfirm ida parameter."],
  "request_at": "2015-05-01T12:00Z"
}
```

## idaリストの更新

上記で作成したときにレスポンスに含まれる idaリストIDを指定することで、idaリストを新しい内容に更新します。

ターゲティングとして利用できるようになるためにはアップロードしてから15分程度掛かります。
そのため、更新APIを叩いた直後はターゲティングとしてシステムに取り込まれていないため該当案件の配信ボリュームは減少します。
利用できる状態かどうかは、ステータス確認APIを使用して確認することができます。

### URL


    https://ms.zucksadnetwork.com/web_api/ida/<更新したいidaリストID>


  * リクエストメソッド: PUT


### PAYLOAD

* list
  * name: アップロードした idaリストの名前 (120文字以内)、リスト名はユニークである必要があります(オプション)
  * ida: 配列の要素でidaを指定(必須)


nameを省略した場合は、更新元の名前とidaリストIDを基に、自動で名前を付与します。


```json
{
  "name": "10日起動していないユーザー_2015-11-01",
  "ida":["aaaaaaaa-bbbb-cccc-1111-222222220000", "aaaaaaaa-bbbb-cccc-1111-222222220001"]
}
```

### レスポンス

アップロードが成功したかどうかはHTTPのレスポンスのステータスコードが200かどうかで確認できます。

#### ステータスコード

 * 200: 正常に終了
 * 400: データのバリデーションエラーなどの場合
 * 401: 認証に失敗した場合
 * 404: IDに対応するidaリストが見つからない場合

#### レスポンス内容

 * 成功した場合
  * list_id: 作成されたidaリストID、更新時にこのIDを指定することでidaリストを更新することができます
  * created_at: リストを作成した日時
  * expired_at: リストの有効最終日時
  * request_at: APIをリクエストされた日時

```json
{
  "list_id": "<作成されたidaリストのID>",
  "created_at": "2015-04-01T12:00Z",
  "expired_at": "2015-06-01T12:00Z",
  "request_at": "2015-05-01T12:00Z"
}
```

 * 失敗した場合

```json
{
  "error": ["Not found IDFA list. Reconfirm your token and list id parameters."],
  "request_at": "2015-05-01T12:00Z"
}
```

## idaリストのステータス確認

上記で作成、更新したidaリストの現在の状態を取得します。
idaリストはアップロード完了後にシステム内への取り込み処理が行われ、その後配信設定に利用可能となりますが、
その取り込み処理が完了したかどうか、取り込みに成功した件数等を確認することができます。

### URL


    https://ms.zucksadnetwork.com/web_api/ida/<idaリストID>


  * リクエストメソッド: GET


### PAYLOAD

なし

### レスポンス

リクエストが成功したかどうかはHTTPのレスポンスのステータスコードが200かどうかで確認できます。

#### ステータスコード

 * 200: 正常に終了
 * 401: 認証に失敗した場合
 * 404: IDに対応するidaリストが見つからない場合

#### レスポンス内容

 * 成功した場合
  * list_id: idaリストID、更新時にこのIDを指定することでidaリストを更新することができます
  * name: アップロード時に付けられたidaリストの名前
  * status: idaリストの取り込み状況
    * "ready": idaリストのシステムへの取り込みが完了し、ターゲティングとして利用可能な状態
    * "queued": システムへの取り込み処理中の状態。
  * success_count: 取り込みに成功した件数（取り込みが完了している場合のみ、返します）
  * failure_count: 取り込みに失敗した件数（取り込みが完了している場合のみ、返します）
  * created_at: リストを作成した日時
  * expired_at: リストの有効最終日時
  * request_at: APIをリクエストされた日時

例：取り込みが完了している場合

```json
{
  "list_id": 1,
  "name": "10日起動していないユーザー_2015-11-01",
  "status": "ready",
  "success_count": 990,
  "failure_count": 10,
  "created_at": "2015-04-01T12:00Z",
  "expired_at": "2015-06-01T12:00Z",
  "request_at": "2015-05-01T12:00Z"
}
```

例：取り込みが完了していない場合

```json
{
  "list_id": 1,
  "name": "10日起動していないユーザー_2015-11-01",
  "status": "queued",
  "created_at": "2015-04-01T12:00Z",
  "expired_at": "2015-06-01T12:00Z",
  "request_at": "2015-05-01T12:00Z"
}
```

 * 失敗した場合

```json
{
  "error": ["Not found IDFA list. Reconfirm your token and list id parameters."],
  "request_at": "2015-05-01T12:00Z"
}
```

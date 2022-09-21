# Zucks Ad Network 代理店向けS3経由のIDFA/AdID配信セグメントの自動更新 v1.0

リリース日 : 2017/03/29

## 概要

Zucks Ad Network の配信でターゲティングしたい IDFA/Android advertising ID (以下 ida) のリストをS3にアップロードし、セグメントを自動更新する仕組み

## 事前準備

S3での自動更新を利用される場合は、弊社担当者にご連絡ください。
連携に必要なIAMユーザーのキーの発行、アップロード先S3の用意を行います。

* 発行されたIAMユーザーで用意されたアップロード先S3にアクセスできることを確認してください
* アップロードするパスの形式
    * `s3://zucks-advertisers-idfas/(代理店専用のプレフィックス)/advertisers/(広告主ID)/idfa-list-groups/(idaリストグループID)/(idaリストファイル)`
    * 広告主IDは[Zucks Ad Network管理画面](https://ms.zucksadnetwork.com/agent)の広告主一覧ページ(`https://ms.zucksadnetwork.com/agent/<agent_id>/company/advertiser/list`)で確認ください

## 連携手順

1. idaリストファイルを用意する
    * 1行に1つのidaを指定してください

   例:

    ```
    $ cat ida-list-upload-test.txt
    A0000000-1111-2222-3333-444444444444
    B0000000-1111-2222-3333-444444444444
    C0000000-1111-2222-3333-444444444444
    D0000000-1111-2222-3333-444444444444
    E0000000-1111-2222-3333-444444444444
    F0000000-1111-2222-3333-444444444444
    G0000000-1111-2222-3333-444444444444
    ```

1. idaリストグループを用意する
    * https://github.com/zucks/ZucksAdNetworkDocuments/blob/master/agent/IDAListAPI/
1. S3にidaリストファイルをアップロードする
    * アップロードしたidaリストファイルから`idaリスト`が作成されます
        * アップロードしたidaリストファイルの名前が生成されるidaリストの名前になります
        * idaリストファイル名にマルチバイト文字列は使用できません
    * 作成されたidaリストはアップロードしたs3オブジェクトのパス(キー)に含まれるidaリストグループIDの`idaリストグループ`に紐づけられます

#### その他補足

* idaリストグループを作成後、そのidaリストを配信で使えるよう取り込み処理が走ります
    * idaリストファイルのサイズ等にもよりますがidaリストの取り込みには数分から数時間かかります
        * 取り込み状況の確認は[API](https://github.com/zucks/ZucksAdNetworkDocuments/blob/master/agent/IDAListAPI/README.md#idaリストグループのステータス確認)から確認できます

#### 用語

* idaリスト ... リターゲティング配信で使うセグメントの一単位
* idaリストグループ ... 複数のidaリストをグループとして名前をつけたもの。リターゲティング配信で使える複合セグメント

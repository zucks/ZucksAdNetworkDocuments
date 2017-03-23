# Zucks Ad Network S3経由のIDFAリストファイルの受け渡しとセグメント自動更新

## 連携手順

(事前準備)

Zucks Ad Network担当者にご確認ください

* IAMユーザのキーの発行
* アップロード先S3パスの確認
  - `s3://zucks-advertisers-idfas/(代理店、広告主ごとのパスプレフィックス)`

(手順)

1. S3にIDFAリストファイルをアップロード
  - アップロードするパス: `s3://zucks-advertisers-idfas/(代理店、広告主ごとのパスプレフィックス)/advertisers/(広告主ID)/idfa-list-groups/(IDFAリストグループID)/(IDFAリストファイル)`
  - アップロードしたIDFAリストファイルからIDFAリストが作成されます
  - アップロードしたIDFAリストファイルの名前が作成されるIDFAリストの名前になります
  - 作成されたIDFAリストはアップロードしたs3オブジェクトのパス(キー)に含まれるIDFAリストグループIDのIDFAリストグループに紐づけられます

※
* 広告主IDは[Zucks Ad Network管理画面](https://ms.zucksadnetwork.com/agent)の広告主一覧ページでご確認ください
* IDFAリストグループの作成等について
  - https://github.com/zucks/ZucksAdNetworkDocuments/blob/master/idalist/ida-list-api-specification-2.0.md

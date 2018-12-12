
# メディアパートナー様向け レポートAPI 変更履歴

## 2018-12-12, Version 1.0
 - 旧バージョン（Version 1.0）のAPIを廃止する

## 2016-10-17, Version 2.0
- エントリポイントにバージョン番号を含めるようになりました
  - 例
    - Version 1.0
      - `https://ms.zucksadnetwork.com/web_api/media/report/frame/daily.csv`
      - `https://ms.zucksadnetwork.com/web_api/media/report/v1.0/frame/daily.csv`
    - Version 2.0
      - `https://ms.zucksadnetwork.com/web_api/media/report/v2.0/frame/daily.csv`
  - バージョンの指定がない場合は、`v1.0` として動作します
- 「1. 枠分析レポート（日別）」のレスポンス項目を追加・削除しました
  - 削除
    - `frame_name`
  - 追加
    - `frame_page`
    - `frame_size`
    - `frame_position_name`
    - `frame_type_name`
    - `frame_os_name`

## 2016-09-28, Version 1.0
- 「1. 枠分析レポート（日別）」の `frame_name` に関する誤記を修正
- 「リクエスト例」の誤記を修正

## 2016-09-02, Version 1.0
- APIを公開



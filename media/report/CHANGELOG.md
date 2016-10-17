
# メディアパートナー様向け レポートAPI 変更履歴

## 2016-10-17, Version 2.0
- エントリポイントにバージョン番号を含めるようになりました
  - 例
    - Version 1.0
      - `https://ms.zucksadnetwork.com/web_api/media/report/frame/daily.csv`
      - `https://ms.zucksadnetwork.com/web_api/media/report/v1.0/frame/daily.csv`
    - Version 2.0
      - `https://ms.zucksadnetwork.com/web_api/media/report/v2.0/frame/daily.csv`
  - バージョンの指定がない場合は、これまで通り動作します
- 「1. 枠分析レポート（日別）」のレスポンス項目を追加・削除しました
  - `frame_size` を追加
  - `frame_position_name` を追加
  - `frame_type_name` を追加
  - `frame_name` を追加
  - `frame_page` を削除

## 2016-09-28, Version 1.0
- 「1. 枠分析レポート（日別）」の `frame_name` に関する誤記を修正
- 「リクエスト例」の誤記を修正

## 2016-09-02, Version 1.0
- APIを公開



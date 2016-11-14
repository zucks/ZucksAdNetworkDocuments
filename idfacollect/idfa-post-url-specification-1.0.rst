=============================
 Zucks IDFA/AdID収集連携仕様
=============================

:version: 1.0

キックバック先情報
==================

以下に記載するURLにパラメータを含めて ``GET`` でリクエストしてください。

IDFA/AdIDキックバック先エンドポイント
-------------------------------------
キックバック先エンドポイントは以下のとおりです。

``http://k.zucks.net/id/``

お送りいただくパラメータ
------------------------

``idfa``, ``adid``, ``device_id`` はいずれか一つに値を渡してください。デバイスのOSによってパラメータを変更する必要はありません。
複数に値を入力した場合は、 ``idfa``, ``adid``, ``device_id`` の順で最初に取得できた値を記録します。

``from`` には貴社の計測サービス名をお渡しください。貴社のサービスからの通知全てに同じ値を付与するようにしてください。

``app_id`` は貴社サービス上で管理されている通知対象のアプリを識別する文字列を付与してください。


.. list-table::
   :header-rows: 1

   * - パラメータ名
     - 型
     - 説明
     - 例
   * - ``idfa``, ``adid``, ``device_id``
     - 文字列
     - 端末識別用の広告ID
     - | ``idfa=XXXX-XXXXXX-XXXXX``
       | ``adid=XXXX-XXXXXX-XXXXX``
   * - ``from``
     - 文字列
     - 計測サービス識別名
     - ``from=zucks``
   * - ``app_id``
     - 文字列
     - 対象アプリ識別子
     - ``app_id=2841``
   * - ``event_type``
     - 文字列
     - イベント名
     - ``event_type=launched``
   * - ``amount``
     - 実数(省略可)
     - 成果発生時の金額
     - | ``price=800``
       | ``price=10.0``
   * - ``currency``
     - 文字列(省略可)
     - 成果金額の通貨単位
     - | ``currency=jpy`` (日本円)
       | ``currency=usd`` (米ドル)

ステータスコード及びレスポンスボディ
====================================

リクエストが正常に処理された場合は以下のようなレスポンスを返却します。

.. list-table::
   :header-rows: 1

   * - 項目
     - 値
   * - ステータスコード
     - 200
   * - Content-Type
     - ``text/plain``
   * - レスポンスボディ
     - ``1``

URL例
=====

purchased というイベントが発生し、600円の成果が発生したという例
    ``http://k.zucks.net/id/?event_type=purchased&idfa=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX&from=zucks&app_id=1234&price=600&currency=jpy``

アプリ起動時に launched というイベントを通知する例
    ``http://k.zucks.net/id/?event_type=launched&device_id=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX&from=zucks&app_id=1234``

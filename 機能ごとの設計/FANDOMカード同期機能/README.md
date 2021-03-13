# 機能概要
FANDOMに登録されたカードのデータを本システムで利用できるように定期的に取り込みを行う（画面はなし）
# 俯瞰
本システムのサーバーサイドは大部分がNode.jsで構築されているが、FANDOMのAPIのクライアントで最もよく実装されていたものがPython製であったため、当該部分はPythonで実装される。Node側にDB変更のためのAPIを立てるが、サーバーマシン内でやりとりが完結するため、外部にポートを開いていない55000番に対してlocalhostでリクエストさせる。  

# データフォーマット
## サマリ取得（todo: 仮称）
URL: http://localhost:55000/current_cards
メソッド: GET  
レスポンス（200の場合）
```
[
    {
        "pageid": Number // ページID,
        "last_revid": Number // リビジョン
    },
    ...（上記が所要個）
]
```
## カード追加・カード更新
URL: http://localhost:55000/upsert
メソッド: POST  
リクエストボディ（JSON）  
```
{
    "pageid": Number // FANDOMのpageid
    "name": String // 名称（カードの要素）
    "cost": Number // コスト（カードの要素）
    "attack": Number // 確保力（カードの要素）
    "oc": String // オブジェクトクラス（カードの要素）
    "maintypes": Array // メインタイプ（カードの要素）
    "subtypes": Array // サブタイプ（カードの要素）
    "effect" : String // 効果（カードの要素。ただし効果外テキストもここに記載される）
    "tags": Array // タグ（カードの要素）
    "banned": Boolean // 永久収容
    "latest_revid": Number // FANDOMのリビジョン
}
```
## カード削除
URL: https://localhost:55000/bulk_delete
メソッド: POST
リクエストボディ（JSON）
```
{
    deleteTargetCardPageids: Number[] // 削除対象のpageidの配列
}
```
## 上記全てについて、422や500などのエラーの場合
レスポンス
```
{
    error: any // エラー原因を表すJSON
}
```
## 定期実行の方法
crontabを利用する（todo: 間隔は未定）
## 死活監視
todo: 未定（おそらくNodeにハートビート用のURLを生やして定期的にPythonに呼んでもらう）
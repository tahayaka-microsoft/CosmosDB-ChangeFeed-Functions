# Change Feed ハンズオン

## CosmosDB Change Feedとは
- CosmosDBのコレクションに付随する追加・更新キュー
- 暗黙的に作成される
- 削除については記録されない
    - 今後、FullFidelityChangeFeedの実装が予定されている(リリース時期不明)
    - ドキュメントの_TTLを短い時間で更新するという手がある
        - 受信側では_TTLを見て削除を判断する

## 実装のパターン
- Azure Functionsで実装
  - 定番かつお手軽
  - ドキュメントを引数として起動される
  - スケーリングについてはAppServicesPlanが推奨
  - 
- ChangeFeedProssessorを使った実装
  - VM等の実行基盤が必要になる
    - FunctionsはChangeFeedProcessorを裏で利用する

## デモ

## デモ環境
### アーキテクチャー
(絵)
### コード
(コードサンプル)

## その他留意点
- 



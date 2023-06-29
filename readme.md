# Cosmos DB (NoSQL) 概要 & Hands On

## Agenda

1. Cosmos DB NoSQL 概要 (1hour)
1. Cosmos DB NoSQLの操作 (30min)
1. Cosmos DB Change Feed 概要 (30min)
1. Cosmos DB Change Feedの操作 (1hour)
    1. Azure Functionsの作成
    1. Azure Functionsのコーディング
    1. テスト

## Cosmos DB NoSQL 概要

### Cosmos DBとは？

- Microsoft Azureで利用できるNoSQLデータストア
- 利点
  - 利点
- トレードオフ
  - ACID特性 (トランザクション)

## Change Feedの概要 

### CosmosDB Change Feedとは
- CosmosDBのコレクションに付随する追加・更新キュー
- 暗黙的に作成される
- 削除については記録されない(Latest Version Mode)
    - 今後、All Versions and Delete Modeの実装が予定されている(PublicPreview中)
    - ドキュメントの_TTLを短い時間で更新するという手がある
        - 受信側では_TTLを見て削除を判断する

### 実装のパターン
- Azure Functionsで実装
  - 定番かつお手軽
  - ドキュメントを引数として起動される
  - スケーリングについてはAppServicesPlanが推奨

- ChangeFeedProssessorを使った実装
  - VM等の実行基盤が必要になる
    - FunctionsはChangeFeedProcessorを裏で利用する

### デモ

### デモ環境
#### アーキテクチャー
(絵)
#### コード
(コードサンプル)

### その他留意点
- 

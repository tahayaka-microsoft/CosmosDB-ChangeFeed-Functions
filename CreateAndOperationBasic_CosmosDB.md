# Cosmos DB NoSQLの操作

## CosmosDBアカウントの作成
   
- Azure PortalよりCosmosDBを検索

![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/eb6a3812-acdf-4259-bc98-e2e20bc9e682)

- コア(SQL)を選択

![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/ff3e816a-91bd-4422-9789-6d4e8cdea208)

- リソースグループを選択(もしくは新規作成)し、アカウント名、リージョンを決定。容量モードはプロビジョニングスループットを設定。その他はデフォルト値で「レビュー＋作成」を押下

![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/004bf69b-d60b-450f-b624-d5378e23f371)
![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/c46e334a-7229-4e37-98f7-b990a0686145)

- 内容をレビューし、「作成」を押下

![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/92cbcf73-af76-4be5-a2fe-64597be82e60)

## データベース・コンテナーの作成

### データベースの作成

- ポータルからCosmosDBアカウントを選択し、「データエクスプローラー」を開く  
![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/bf09c3bc-bbf9-41fa-9ae9-7d21923b2d21)

- データエクスプローラーより"New Container"の横の"v"をクリックし、"New Database"を押下  
![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/d01595f8-8123-4418-8d2f-21cb882584b6)

- 右側に現れた入力欄に以下の情報を入力  
![image](https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/478856a1-a62e-41f7-ab77-8004ce124b63)

|項目名|意味|サンプル入力|
|---|---|---|
|Database Id|データベース名(必須)| db1 |
|Provision Throughput|プロビジョンスループットにするか| ☑ |
|Database Throughput (autoscale)|標準スループットかオートスケールか| Autoscale |
|Database MaxRU/s|最大RU/s|1,000|

## アイテムの作成・更新・削除
## クエリの発行
## 管理操作
   - スループットの変更
   - インデックスポリシーの変更

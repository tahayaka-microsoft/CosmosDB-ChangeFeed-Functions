# Cosmos DB NoSQLの操作

## CosmosDBアカウントの作成
   
- Azure PortalよりCosmosDBを検索

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/eb6a3812-acdf-4259-bc98-e2e20bc9e682" width="200">

- コア(SQL)を選択

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/ff3e816a-91bd-4422-9789-6d4e8cdea208" width="400">

- リソースグループを選択(もしくは新規作成)し、アカウント名、リージョンを決定。容量モードはプロビジョニングスループットを設定。その他はデフォルト値で「レビュー＋作成」を押下

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/004bf69b-d60b-450f-b624-d5378e23f371" width="400">
<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/c46e334a-7229-4e37-98f7-b990a0686145" width="400">

- 内容をレビューし、「作成」を押下

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/92cbcf73-af76-4be5-a2fe-64597be82e60" width="400">

## データベース・コンテナーの作成

### データベースの作成

- ポータルからCosmosDBアカウントを選択し、「データエクスプローラー」を開く  
<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/bf09c3bc-bbf9-41fa-9ae9-7d21923b2d21" width="200">

- データエクスプローラーより"New Container"の横の"v"をクリックし、"New Database"を押下  
<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/d01595f8-8123-4418-8d2f-21cb882584b6" width="400">

- 右側に現れた入力欄に以下の情報を入力  
<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/478856a1-a62e-41f7-ab77-8004ce124b63" width="400">

|項目名|意味|サンプル入力|
|---|---|---|
|Database Id|データベース名(必須)| db1 |
|Provision Throughput|プロビジョンスループットにするか| ☑ |
|Database Throughput (autoscale)|標準スループットかオートスケールか| Autoscale |
|Database MaxRU/s|最大RU/s|1,000|

### コンテナーの作成

- データエクスプローラーより"New Container"をクリック
<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/e9777c21-661a-43c5-8017-77f2c0f0deb2" width="400">

- 右側に現れた入力欄に以下の情報を入力
<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/93b0d3d6-53c2-49ab-8ee0-ee21bd486b99" width="400">

|項目名|意味|サンプル入力|
|---|---|---|
|Database Id|データベース名(必須)| Existingを選択し、db1 |
|Container Id|コンテナー名(必須)|container1|
|Partition Key|パーティションキー| /id |

その他はデフォルトでOK


## アイテムの作成・更新・削除

-データエクスプローラーでコンテナーを選んで、"New Item"を押下

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/90b0339d-b82d-411e-a34c-c6484272adfc" width="400">

-出てきたウインドウにJSONフォーマットでデータを入力し、SAVE。(以下をコピー&ペーストし、任意に改変)
```JSON
{
   "id"     : "000001",
   "name"   : "John Smith",
   "sku"    : "20223",
   "price"  : 23.95,
   "shipTo" : {
      "name" : "Jane Smith",
      "address" : "123 Maple Street",
      "city" : "Pretendville",
      "state" : "NY",
      "zip"   : "12345"
   },
   "billTo" : {
      "name" : "John Smith",
      "address" : "123 Maple Street",
      "city" : "Pretendville",
      "state" : "NY",
      "zip"   : "12345"
   }
}
```

Save後、システムプロパティが付きリストに登録されることを確認する。

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/12fea94b-dce0-422b-89e1-d898a3a05aee" width="400">

**"id"の値と他の項目の値を変更しつつ**同様の操作を何回か繰り返し、5~10程度のドキュメントを保存する。

## クエリの発行

### サンプルデータの作成

下記2つのJSONアイテムを、前項の操作に従って作成する。

- サンプルデータその1

``` JSON 
{
  "id": "863e778d-21c9-4e2a-a984-d31f947c665c",
  "categoryName": "Surfboards",
  "name": "Teapo Surfboard (6'10\") Grape",
  "sku": "teapo-surfboard-72109",
  "price": 690.00,
  "manufacturer": {
    "name": "Taepo",
    "location": {
      "type": "Point",
      "coordinates": [ 
        34.15562788533047, -118.4633004882891
      ]
    }
  },
  "tags": [
    { "name": "Tail Shape: Swallow" },
    { "name": "Color Group: Purple" }
  ]
}
```

- サンプルデータその2

``` JSON 
{
  "id": "6e9f51c1-6b45-440f-af5a-2abc96cd083d",
  "categoryName": "Sleeping Bags",
  "name": "Vareno Sleeping Bag (6') Turmeric",
  "price": 120.00,
  "closeout": true,
  "manufacturer": {
    "name": "Vareno"
  },
  "tags": [
    { "name": "Color Group: Yellow" },
    { "name": "Bag Shape: Mummy" }
  ]
}
```


### クエリ操作

データエクスプローラー上のツールバーにある、"New SQL Query"を押下。

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/ee82db52-9e27-4f9a-90b0-4a01a1831bab" width="400">

右側ペインに以下のコードを張り付けて"Execute Query"を押下

```SQL
select count(1) AS CNT from c
```

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/7b71b6f8-d3ba-48b2-b84a-3b53bc43e223" width="400">

### クエリパターン

- 全件取得
- 


## 管理操作
   - スループットの変更
   - インデックスポリシーの変更

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

右側ペインにコードを張り付けて"Execute Query"を押下

<img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/7b71b6f8-d3ba-48b2-b84a-3b53bc43e223" width="400">

クエリ
```SQL
select count(1) AS CNT from c
```

結果
```JSON
[
    {
        "$1": 2
    }
]
```
※`count(1)`に対して別名がついてないので"$1"になっている。結果の件数はコンテナーのデータ件数によっては異なる。

### クエリパターン

#### 全件取得

クエリ
```SQL
SELECT * FROM c
```
結果
```JSON
[
    {
        "id": "863e778d-21c9-4e2a-a984-d31f947c665c",
        "categoryName": "Surfboards",
        "name": "Teapo Surfboard (6'10\") Grape",
        "sku": "teapo-surfboard-72109",
        "price": 690,
        "manufacturer": {
            "name": "Taepo",
            "location": {
                "type": "Point",
                "coordinates": [
                    34.15562788533047,
                    -118.4633004882891
                ]
            }
        },
        "tags": [
            {
                "name": "Tail Shape: Swallow"
            },
            {
                "name": "Color Group: Purple"
            }
        ],
        "_rid": "OsMpAKRdg3MBAAAAAAAAAA==",
        "_self": "dbs/OsMpAA==/colls/OsMpAKRdg3M=/docs/OsMpAKRdg3MBAAAAAAAAAA==/",
        "_etag": "\"c600c175-0000-0700-0000-64a4f7960000\"",
        "_attachments": "attachments/",
        "_ts": 1688532886
    },
    {
        "id": "6e9f51c1-6b45-440f-af5a-2abc96cd083d",
        "categoryName": "Sleeping Bags",
        "name": "Vareno Sleeping Bag (6') Turmeric",
        "price": 120,
        "closeout": true,
        "manufacturer": {
            "name": "Vareno"
        },
        "tags": [
            {
                "name": "Color Group: Yellow"
            },
            {
                "name": "Bag Shape: Mummy"
            }
        ],
        "_rid": "OsMpAKRdg3MCAAAAAAAAAA==",
        "_self": "dbs/OsMpAA==/colls/OsMpAKRdg3M=/docs/OsMpAKRdg3MCAAAAAAAAAA==/",
        "_etag": "\"c600c275-0000-0700-0000-64a4f7a50000\"",
        "_attachments": "attachments/",
        "_ts": 1688532901
    }
]
```

#### WHERE句で条件を設定

クエリ
```SQL
SELECT * FROM c
WHERE c.categoryName = "Sleeping Bags"
```
結果
```JSON
[
    {
        "id": "6e9f51c1-6b45-440f-af5a-2abc96cd083d",
        "categoryName": "Sleeping Bags",
        "name": "Vareno Sleeping Bag (6') Turmeric",
        "price": 120,
        "closeout": true,
        "manufacturer": {
            "name": "Vareno"
        },
        "tags": [
            {
                "name": "Color Group: Yellow"
            },
            {
                "name": "Bag Shape: Mummy"
            }
        ],
        "_rid": "OsMpAKRdg3MCAAAAAAAAAA==",
        "_self": "dbs/OsMpAA==/colls/OsMpAKRdg3M=/docs/OsMpAKRdg3MCAAAAAAAAAA==/",
        "_etag": "\"c600c275-0000-0700-0000-64a4f7a50000\"",
        "_attachments": "attachments/",
        "_ts": 1688532901
    }
]
```


#### JSONを他の形式に変換(プロジェクション) : productというオブジェクトを新しく生成

クエリ
```SQL
SELECT {
  "name": c.name,
  "sku": c.sku,
  "vendor": c.manufacturer.name
} AS product
FROM c 
WHERE c.sku = "teapo-surfboard-72109"
```
結果
```JSON
[
    {
        "product": {
            "name": "Teapo Surfboard (6'10\") Grape",
            "sku": "teapo-surfboard-72109",
            "vendor": "Taepo"
        }
    }
]
```

#### SELECT VALUEを使ったフラット化

クエリ
```SQL
SELECT VALUE {
  "name": p.name,
  "sku": p.sku,
  "vendor": p.manufacturer.name
}
FROM c
WHERE c.sku = "teapo-surfboard-72109"
```  
結果
```JSON
[
    {
        "name": "Teapo Surfboard (6'10\") Grape",
        "sku": "teapo-surfboard-72109",
        "vendor": "Taepo"
    }
]
```

## 管理操作
   - スループットの変更
      - データエクスプローラーの**データベースの下**にある"Scale"を選択  
        <img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/0eb4eadd-0cb0-49d9-b92f-a08919149f30" width="400">  
      - Manual or Automaticを選択し、スループット(RU/s)を入力し、"Save"  
        <img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/c8f7d8b7-5800-4e51-90ac-7e448b760e1e" width="400">
 
   - インデックスポリシーの変更
      - データエクスプローラーの**コンテナーの下**にある"Settings"を選択  
         <img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/0c230f22-dd0d-4408-8bab-0df0fb1e5827" width="400">  
      - 右ペインのタグ"Index Policy"を開く  
        <img src="https://github.com/tahayaka-microsoft/CosmosDB-ChangeFeed-Functions/assets/94338329/b5e3c84e-6de6-4506-b0c7-8d98cebd94c4" width="400">

   - インデックスポリシーの内容を追記・変更して"Save"することで反映される。  
     インデックスの変更は使われていないRU/sを消費してバックグラウンドに実行される。

   ```JSON
   {
       "indexingMode": "consistent", ← "consistent" or "none"
       "automatic": true,  ← 自動で作成するか
       "includedPaths": [   ← インデックスを生成する項目・パス
           {
               "path": "/*"   ←「ルートからすべての項目」の意味
           }
       ],
       "excludedPaths": [   ← インデックスを生成しない項目・パス
           {
               "path": "/\"_etag\"/?"  ←"/etag"にはインデックスを作らない。\はエスケープ
           }
       ]
   }
   ```
# Cosmos DB (NoSQL) 概要 & Hands On

> 本ページはCosmos DB for **NoSQL**の概要について説明する。<br>
その他のCosmos DB APIについての説明は[こちら](https://learn.microsoft.com/ja-jp/azure/cosmos-db/https://)を参照のこと

# 目次

1. Cosmos DB NoSQL 概要 (1hour)
1. [Cosmos DB NoSQLの操作](./CreateAndOperationBasic_CosmosDB.md) (30min)
1. Cosmos DB Change Feed 概要 (30min)
1. Cosmos DB Change Feedの操作 (1hour)
    1. Azure Functionsの作成
    1. Azure Functionsのコーディング

# Cosmos DB for NoSQL 概要

## Cosmos DB for NoSQLとは？

- Microsoft Azureで利用できる**NoSQLデータストア**
    - いわゆる**データベース**とは異なる概念と理解するのが良い
    - ファイルの簡便性とRDBMSの高速検索の良いとこどり
    - RDBMSを補完する概念であり、排他的ではない 

- 優位性
  - **表形式でない**ドキュメント(JSON)の取り扱い
  ```JSON
  {
      "name"   : "John Smith",
      "sku"    : "20223",
      "price"  : 23.95,
      "shipTo" : { "name" : "Jane Smith",
                   "address" : "123 Maple Street",
                   "city" : "Pretendville",
                   "state" : "NY",
                   "zip"   : "12345" },
      "billTo" : { "name" : "John Smith",
                   "address" : "123 Maple Street",
                   "city" : "Pretendville",
                   "state" : "NY",
                   "zip"   : "12345" }
  }
  ```
  - 大量データの中から特定の少量データを**高速に検索**できる
  - データ登録を起点とする**イベント起動**ができイベント駆動型データ処理を実現
  - 同時アクセスなどによる**高負荷時でも処理を分散**してレイテンシーを落とさない
  - 地理分散を利用して**アクセス元の地域に寄らない高速なアクセス速度**を保証
  - **マルチマスター**による高速な書き込み
  - **負荷に合わせて性能を自由に変更**できる。自動調整も可能。(注:制約はある)
  - SLAで**99.999%の可用性**を保証
  - 1KB Readに関しては**99%の処理を10ms以内で応答**

- トレードオフ
  - トランザクション
      - 利用できる状況が限定的
  - 結合・集計処理
      - できないことはないが非常に高コスト
      - 分析用途としてSynapse Linkという他サービスとの連携機能を装備しており、そちらで対応
   
- 優位性とトレードオフを加味すると以下のようなシステムに向いている
    - 利用者や同時アクセス数は多い方が良い
    - 蓄積されるデータは多い方が良い
    - クエリはシンプルなほうが良い
    - 1回に抽出されるデータは少ない方が良い
    - データが"HOT"な期間、蓄積して利用するのが良い
        - 長期的なデータ保存やデータ分析はBlob Storage、Spark、RDMBSなど別の仕組みに任せるほうがよい
        - 別の仕組みにデータを移すための便利な仕組み(ChangeFeed/Synapse Link)が組み込まれている

- 上記を考慮したユースケース
    - Webアプリ・モバイルアプリのデータ管理
    - IoTデバイスデータ処理
    - プロダクトカタログ
    - ログデータ管理
    - エンタープライズ・データ中継 

- 開発者向けのメリット
    - システム変更の効率化に寄与
        - データベース項目の変更があってもスキーマレスのため過去データを直さなくてよい
        - アクセス負荷の急激な変更に強い

# Cosmos DBの仕組み

## スループット(Request Unit:RU)

スループットは**コンテナーに対するアイテムの読み書き性能**を規定する。

単位は**RU(Request Unit)**で、1RU = 1KBの読み込みにかかる処理で消費されるリソースを指す。

CosmosDB for NoSQLの処理性能は、すべてこのRUで計算される。これは、以下の内部処理を含む。
- 書き込みのためのロック
- インデックスの書き込み、読み出し、検索
- データ検索でフィルター条件にヒットしなかった読み込み

そのため、基本的にはRead 1KB=1RUを基準とした場合、
- 変更 = 2~3RU
- 書き込み = 5~6RU
程度が最低でも消費される

CosmosDB for NoSQLでは、次の２つの設定を用いて性能を定義する。

- プロビジョニングスループット
    - あらかじめスループットを100RU/秒単位で定義しておき、その性能を確保(プロビジョニング)する。
    課金単位は100RU/秒で、確保された性能以上のリクエストはリクエスト超過(**429**)エラーとして弾かれる。
    - スループットの確保の仕方には「標準」と「自動(オートスケール)」の２つがある
        - 標準スループット
        常に同じRU/秒を確保する。
        - 自動(オートスケール)スループット
        最大で利用可能なRU/秒を確保し、アイドル時は最小1/10までRU/秒を下げる。

- サーバーレス
    - 使用時のみスループットを確保する。ただし、以下の制限がある
      - スループットの最大は20,000RU/秒
      - ストレージの最大量は1TBまで
    - 後述する物理パーティションの状況により、RU/秒は影響を受ける。

## データベース・コンテナー・アイテム

### アイテム
格納の基本単位。JSONドキュメントが格納される。

JSONドキュメントの項目は自由に定義できるが、コンテナーに定義されているパーティションキーの値を必ず含める必要がある。

各JSONドキュメントにはユーザーが格納した項目以外にシステム管理上のプロパティがいくつか自動的に設定される。

|プロパティ名|目的|
|---|---|
|_rid|項目の一意識別子|
|_etag|オプティミスティック同時実行制御に使用されるエンティティタグ|
|_ts|項目の最終更新のタイムスタンプ(UNIX時間)|
|_self|項目のアドレス指定可能なURI|

_ttlプロパティを設定すると、指定した秒数後に自動的に削除される。

### パーティションキー

アイテムの格納場所を決める項目。

コンテナー作成時に指定が必須であり、コンテナー作成後に変更することはできない。

Cosmos DBのスケールアウトを活用するためには以下の条件を満たす項目をパーティションキーとして設定することが望ましい。

1. 適度に値がばらつくこと。
   - データ格納時の分散の基準となるので、バラつかないと分散できない 
   - 少なくとも(総データ量 ÷ 5~10GB)以上のユニーク数を持つことが望ましい。
2. パーティションキーに設定される値は、アクセスしてくるシステムから常に利用される可能性があること
   - タイムスタンプなど、いずれアクセス頻度が下がる項目ではないこと

パーティションキーの項目は、階層(Hieralchy)を持つことができる。
階層は3つまで指定可能。上位の階層の検索についても効率化が測られる。


### コンテナー

アイテムが格納される場所。

コンテナーごとにスループットを設定する。

コンテナーの作成時に、パーティションキーを設定する。アイテムはこのパーティションキーに基づいて、論理パーティションに分散格納される。

コンテナに対してTime to Live(TTL)を設定すると、アイテムは指定した秒数後に自動的に削除される。

コンテナーにはストアドプロシージャ、トリガー、ユーザー定義関数を定義することができる。

### データベース

コンテナーのグループ。

共有スループットの利用時にはデータベースごとに25コンテナーまでスループットの共有が可能。

## 論理・物理パーティション

コンテナーごとにアイテムを格納する単位。
**分散アクセスの方法を決定する**キーとなる概念のため、設計上注意が必要。

**論理パーティション**は同一のパーティションキーを持つアイテムを20GBまで格納できる。**一つの論理パーティションのサイズが20GB以上になることはできない。**
論理パーティションのアイテムは単一の物理パーティションに配置される。

**物理パーティション**は、最大10,000RU/秒の処理能力と50GBのストレージをもつ分散処理の単位である。この物理パーティション内にいくつかの論理パーティションを持つ。

設定されたスループットや格納されたデータサイズが物理パーティションの上限を超えることにより物理パーティションの数は増加するが、減少することはない(*1)。

設定されたスループットは存在する物理パーティション間で均等に分配される(*2)。

例 : 5,000RU/sで利用しているコンテナーを、12,000RU/sに変更した場合、2つの物理パーティションに分散される。その後5,000RU/sに戻した場合でも、2つの物理パーティションに分散されたままとなる。

> (*1) 物理パーティションをマージする機能がプレビュー中。<BR>
> (*2) 特定の物理パーティションへのRU割り当てを増加させることができる機能が現在プレビュー中。

## インデックス、インデックスポリシー


## 整合性レベル

## 接続方法

## SDK

## バックアップ

## その他 
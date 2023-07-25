# 用語集

NDB に関連する用語を説明しておきます。
※思いついたら随時追加編集予定・・・


# NDB 的な DB / DB Server の考え方

## Database
普通にデータベースのことです。ただし、NDB の管理では、データベースと、それをホストするデータベース サーバ（の仮想マシン）を区別して扱います。

![NDBのデータベース](assets\NDB-CDM\ndb-15.png)

## Database Server VM
データベース管理システム（DBMS。PostgreSQL、Oracle Database、SQL Serverなど）をインストールした仮想マシンです。そして NDB にとっての DB サーバは、Nutanix HCI 上で起動されている必要があります。

![NDBのDBサーバVM](assets\NDB-CDM\ndb-16.png)

## Greenfield Database / Greenfield Database Server VM
「グリーン フィールド」とは新規構築する環境を指しており、NDB の機能でプロビジョニングした DB、DB サーバを指します。

## Brownfield Database / Brownfield Database Server VM
「ブラウン フィールド」とは既に構築された環境を指しており、既存 DB を NDB に登録したものを指します。NDB に DB（と DB サーバ）を登録するには、まず対象の DB サーバを、何らかの方法で NDB が管理する Nutanix HCI に移行しておく必要があります。

## Source Database / Source Database Server VM
NDB に登録した DB、もしくは NDB でプロビジョニングした DB（と DB サーバ）です。NDB でクローン作成した DB は、Source DB としては扱われません。

## Clone / Cloned Database
NDB の機能でクローン作成した DB を指します。これは、Source Database から Time Machine 機能で取得したスナップショットから作成します。Prism Element から手動クローンした DB サーバなど、NDB の機能を利用していない複製データベースは含まれません。

## Clustered Database
NDB における DB クラスタは、DB サーバ仮想マシンのグループです。
たとえば、PostgreSQL の HA クラスタ、Oracle RACを構成する仮想マシン群がこれにあたります。


# NDB の前提知識

## Cluster
Nutainx / NDB で DB ～ DB サーバのレイヤを管理するうえで、いろいろな「クラスタ」が登場しますが、すべて別の概念なので文脈で判断する必要があります。

- DB サーバのクラスタ（PostgreSQL の HA、Oracle RAC など）
- DB のクラスタ（DBMS によっては、DB の集合をクラスタと呼ぶ PostgreSQL など）※NDBではほぼ話題にならない。
- Nutanix Cluster（Nutanix HCI のクラスタ。Prism Element で管理する）
- NDB Cluster（NDB の管理サーバをクラスタ化できる。Nutanix Cluster とも異なるもの）※大規模な NDB 環境を構成したり、NDB を API 操作する際に意識することになる。
- NDB の Source Cluster（NDB Server の Time Machine 機能が紐付けられている Nutanix Cluster）
- vSphere クラスタ（vSphere HA / DRS などを構成する）※ハイパーバイザとして ESXi を利用する場合のみ話題になる。Nutanix Cluster とハイパーバイザのノードを一致させることが多いが、必ずしも Nutanix Cluster = vSphere Cluster ではない。

## Nutanix Cluster
Nutanix HCI のクラスタです。NDB が管理する DB サーバが稼働するプラットフォームになります。「仮想化基盤 + Nutanix 独自の分散ストレージ」となっていて、NDB ではハイパーバイザとして Nutanix AHV または VMware ESXi が利用されます。
Prism Element で管理する、Nutainx エンジニアにいちばん馴染みのあるクラスタかなと思います。

## Prism Element
Nutanix HCI のクラスタを管理する Web UI です。ハイパーバイザとして AHV と ESXi のどちらを選択しても利用します。

## Nutanix Volumes Block Storage
Nutainx HCI の分散ストレージ（AOS Storage / Distributed Storage Fabric）から、iSCSI でブロック デバイスを提供する機能です。NDB では、DB サーバの DBMS、DB 構成ファイルの格納領域などとして利用します。

![Prism から見た DB サーバ VM - VG](assets\NDB-CDM\ndb-17.png)

## vSphere（vCenter Server + ESXi）
Nutanix HCIでは、仮想化基盤のハイパーバイザとして ESXi が選択できます。その場合は、管理サーバとして vCenter Server も必要になります。


# NDB ならではの用語

## NDB Server
NDB の管理サーバです。仮想アプライアンスです。Nutanix 製品としてはめずらしく、Prism Element / Central とは別の管理用 Web UI を提供します。

## NDB Database Agent
NDB が管理する DB サーバにインストールされるエージェントです。

## Profiles
NDB では、DB や DB サーバの構成をプロファイルで管理します。DB / DB サーバをプロビジョニングやクローン作成する際には、各種プロファイルを指定することになります。
- ソフトウェア（Software）※DBMS のこと
- コンピュート（Compute）
- ネットワーク（Network）
- データベースのパラメータ（Database Parameters）
- Windows ドメイン（Windows Domain）

## Time Machine
NDB で DB のクローンやバックアップ / リストアを実施するための機能で、DB サーバ VM のスナップショットや、DB からトランザクションログ取得や保持期間を管理する。NDB に DB を登録する際に、DB 単位で必ず作成することになる。

![NDB UI でのタイムマシン](assets\NDB-CDM\ndb-36.png)

## Log Catch-up Operation
NDB Server が、NDB で管理する DB サーバから、DB のトランザクション ログを定期的にキャッチアップします。NDB Time Machine での PIT リカバリなどで利用します。
## SLAs
NDB の Time Machine を利用するうえでの、スナップショットの取得間隔やトランザクション ログの保持期限を設定する設定です。

## NDB Drive
Source Database Server VM に接続されたドライブで、NDB に転送する DB トランザクションログを一時的に格納します。

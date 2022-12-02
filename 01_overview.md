# 概観

## NDB の概要

Nutanix Database Service（NDB）は、データベース管理を自動化して、
（1-Click に近い）シンプルな操作による、データベースのプロビジョニングとライフサイクル管理を実現するソリューションです。
たとえば、DBA がデータベースを用意する際に、サーバのネットワーク構成検討や HA 構成といった部分も含めて標準化して、簡素な手順に落とし込むことができます。
ちなみに、以前は Nutanix Era という名前でした。


NDB が自動化する対象になるのは、次のような DBA が日常的に実施するオペレーションです。
* 汎用的な、あるいは同様の構成が複数必要になるような DB サーバと DB の構築（プロビジョニング）
* 既存 DB の複製（クローン）
* DB のバックアップ / リストア
* DBMS ソフトウェアや、OS のパッチ適用

NDB によって提供される主要な機能として、次のものがあります。
* 1-Click プロビジョニング
* コピー データ管理（CDM：Copy Data Management）
* DB 保護（バックアップ / リストア）
* 1-Click パッチ適用

一方で、

## 前提となる Nutanix コンポーネント

Nutanix NDB は、Nutanix HCI 上での DB サーバ仮想マシンを管理対象とします。
つまり、Nutanix の外部で稼働している DB サーバは物理 / 仮想マシンのいずれの場合も管理対象外であり、NDB で管理するためには何らかの方法で Nutanix HCI 上の仮想マシンとして移行する必要があります。
また、NDB の管理サーバ（NDB Server）も、Nutanix HCI 上で稼働している必要があります。

NDB の提供する機能では、おもに Nutanix HCI に関係する下記のコンポーネントが利用されます。（細かくは、下記以外の機能も利用されます）
* Prism Element
* AOS Storage（Nutanix Distributed Storage Fabric）
* ハイパーバイザ： AHV または VMware ESXi
* Nutanix Volumes Block Storage

ちなみに NDB Server は、最近の Nutanix 製品にしてはめずらしく直接的に Prism Central に接続しません。

Note: NDB 構成イメージ coming soon!

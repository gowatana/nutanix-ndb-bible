# 管理対象の DBMS

NDB では、次の DBMS を管理できます。

* PostgreSQL
* Oracle Database
* Microsoft SQL Server
* MySQL
* MariaDB
* MongoDB

NDB で DB をプロビジョニングするには、もとにする DB サーバが必要となっており、基本的には次のような手順が必要です。

1. Nutanix HCI 上の仮想マシンとして DB サーバを構築する。仮想マシンには、DBMS がインストールされた状態にしておく必要がある。
2. DB サーバを NDB に登録して、ソース DB として扱えるようにする。DB も同時に登録でき、その場合は Time Machine も作成することになる。
3. ソース DB から、ソフトウェア プロファイルを作成する。
4. DB（とそれをホストする DB サーバ）をプロビジョニングする。このとき、DBMS のソフトウェアのプロファイルを指定する必要がある。

基本的には既存の DB サーバと DB を登録してから、それを管理対象やソースとして利用する形式になっています。ただし、それぞれの DBMS には OOB のソフトウェア プロファイルが用意されているため、とりあえず DB をプロビジョニング（いわゆる 1-Click プロビジョニング）してみることは簡単です。DBMS のソフトウェア自体も、NDB のソフトウェア プロファイルに含まれています。

> Out-of-Box： 「箱から出したらすぐに使える」という意味があり、NDB Server セットアップ直後からすぐに利用できるもの。

しかし、上記の DBMS のうち Oracle と SQL Server には OOB プロファイルが用意されていません。そのため、NDB とは別に DBMS インストーラを用意して、最初に手作業で DB サーバを構築しておく必要があります。

当然ながら、各 DBMS の全てのバージョンがサポートされるわけではないため、NDB のドキュメントなどで、DB ごとにサポート対象バージョンを確認する必要があります。
また、Oracle と SQL Server については、DBMS バージョンごとにサポートされる OS がドキュメントに記載されています。

> - [Blog] NDB で PostgreSQL DB をプロビジョニングしてみる。（OOB 編）  
>   https://blog.ntnx.jp/entry/2022/12/08/233530
> - [Blog] Nutanix の NDB と Oracle Database。  
>   https://blog.ntnx.jp/entry/2022/12/07/235802
> - [Blog] NDB で DB をプロビジョニングしてみる。（Oracle CDB）  
>   https://blog.ntnx.jp/entry/2022/12/15/030326
> - [Blog] NDB での DB / DB サーバの削除の様子。（Oracle）  
>   https://blog.ntnx.jp/entry/2022/12/14/231934

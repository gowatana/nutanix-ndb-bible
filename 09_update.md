# NDB でのアップデート


## NDB Server のアップデート

NDB Server のソフトウェア アップデート（アップグレード）は、NDB Server の　Web UI から実施できます。

> - [Blog] NDB Server をアップグレードしてみる。（NDB 2.5.1.1）  
>   https://blog.ntnx.jp/entry/2022/12/20/232920


## DBMS ソフトウェアのアップデート

NDB から、DB のアップデートを実施できます。
NDB での DB アップデートでは、ソフトウェア プロファイルと、ソフトウェア プロファイル バージョンを利用します。


## DB サーバ VM の OS アップデート

NDB では、DB サーバ VM へのパッチ適用機能も用意されています。
この機能では Linux のみがサポートされており、内部では yum update コマンドが利用されています。

> - [Blog] NDB で OS アップデートの様子を見てみる。  
>   https://blog.ntnx.jp/entry/2022/12/22/235816
> - [Blog] NDB で PostgreSQL DB サーバ に OS パッチを適用してみる。  
>   https://blog.ntnx.jp/entry/2022/12/23/234103

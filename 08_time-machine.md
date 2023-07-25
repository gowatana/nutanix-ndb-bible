# タイムマシン

NDB では、DB のスナップショットやバックアップ/リストア、クローンによる Copy Data Management（CDM）機能のために、タイムマシンと呼ばれるコンポーネントを利用します。

NBD の Copy Data Management（CDM）機能では、

![タイムマシン](assets\NDB-CDM\ndb-9.png)

タイムマシンは、ソース DB を登録またはプロビジョニングする際に、DB 単位で必ず作成することになります。

![タイムマシン](assets\NDB-CDM\ndb-31.png)


## 主な機能

タイムマシンでは、下記の機能を提供しています。
- スナップショットの取得
- ログのキャッチアップ
- DB のバックアップ / リストア
‐ クローン DB の作成
- SLA ポリシー
- スケジュール設定


## スナップショットの取得

NDB 側で管理されるスナップショットです。NDB UI で DB と同時に作成されたタイムマシンには、自動的に最初のスナップショット（era_auto_snapshot）が作成されます。

NDB のスナップショットには Nutanix HCI の Protection Domain スナップショットが活用されていますが、作成や削除は NDB から実施されます。

下記のスクリーンショットでは、NDB のタイムマシンに9件のスナップショットが作成されています。
![NDB UI から見たスナップショット](assets\NDB-CDM\ndb-39.png)

これは、Prism Element では保護ドメインのスナップショットです。
![Prism Element から見た NDB スナップショット](assets\NDB-CDM\ndb-40.png)


NDB によるスナップショットは、アプリケーションの整合性を意識して取得されます。たとえば Oracle Database であればバックアップ モードに切り替えて取得されるので、アーカイブログ モードが有効化されていないと取得が失敗します。
![スナップショット取得時の Oracle Database アラート ログ](assets\NDB-CDM\ndb-38.png)


## ログのキャッチアップ

NDB によって、定期的に DB のログがキャッチアップされます。
このログは DB の更新を記録するもので、PostgreSQL の WAL ログ、Oracle Database の REDO ログ、SQL Server のトランザクション ログなどを指します。

ログのキャッチアップは、手動で開始することもできます。

3rd-Party の（NDB 以外の）バックアップ ソフトウェアでも DB をバックアップする場合、バックアップ完了後に DB のログを削除することがあります。そういったバックアップ処理と NDB を併用する場合は、3rd-Party によるバックアップ処理の開始前に NDB のログ キャッチアップが実施されるように考慮しておきましょう。

NDB のタイムマシンでは、スナップショットとログのキャッチアップを併用して実現されています。
![NDB のタイムマシン](assets\NDB-CDM\ndb-33.png)

> DB でログ キャッチアップが実行できない（グレーアウトされている）場合には、タイムマシンに割り当てられている SLA ポリシーが「継続的なログの保存期間」の日数が設定されたものであることを確認します。


## SLA ポリシー

タイムマシンでは、スナップショットとログの定期的な取得と保持期間の管理を SLA というポリシーで定義します。

SLA は、デフォルトで下記のものが用意されていますが、追加作成もできます。保持期限は、日時、週次、月次、四半期での指定が可能です。

![Time Machine SLA](images/tm-sla.png)


ログのキャッチアップと、ポイント イン タイム（PIT）のクローン/リカバリを実施するには、SLA で「連続」（のログキャッチアップ）の日数が指定されている必要があります。
![NDB のタイムマシン](assets\NDB-CDM\ndb-50.png)


> SLA のタイムマシンへの割り当ては、タイムマシンの作成後でも変更可能です。しかし SLA 割り当てを変更すると、それまでに取得された DB のスナップショットやログが削除されてしまうので、変更以前のスナップショット/リストアによるリストアやクローン処理が実施できなくなります。

## スケジュール設定

スナップショットの取得は、スケジュール設定できます。


## DB のリストア

NBD では、2種類の DB リストアが可能です。

- スナップショットを指定した DB のリストア
- 時間を指定した DB のリストア、つまり PIT（Point-in-Time）でのリカバリ

> DB の「ポイント イン タイム」でのリストアを実施する場合には、SLA は「継続的なログの保存期間」の日数が設定されたものを指定することで、ログをキャッチアップしておく必要があります。

> - [Blog] NDB で DB を PIT リカバリしてみる。（Oracle）  
>   https://blog.ntnx.jp/entry/2022/12/16/040309


## クローン DB の作成

NDB でのクローン DB の作成でも、タイムマシンを利用します。
クローン DB を作成することで、容量を消費しないクローンや、定期的なリフレッシュのような CDM を実現できます。
![NDB の CDM](assets\NDB-CDM\ndb-32.png)

NDB では、クローン DB は、クローン元のソース DB とは明確に区別されています。
![NDB の CDM](assets\NDB-CDM\ndb-52.png)

NDB のクローンでは、DB と一緒に新規 DB サーバ VM を作成するか、DB を既存 DB サーバ VM に作成することになります。
また、プリ / ポスト コマンドの実行機能を利用することで、データ マスキングなども実現できます。

> - [Blog] NDB で Oracle Database をクローンしてみる。（Non-CDB）  
>   https://blog.ntnx.jp/entry/2022/12/13/230617
> - [Blog] NDB で 1台の DB サーバに 複数の Oracle CDB をクローンしてみる。  
>   https://blog.ntnx.jp/entry/2022/12/18/234023
> - [Blog] NDB の Oracle Database クローンでデータ マスキングしてみる。  
>   https://blog.ntnx.jp/entry/2022/12/14/185250


## 参考情報1: タイムマシンによる DB 管理の補足（Oracle Database の場合）

たとえば Oracle Database の場合、DB は下記のようなファイルで構成されます。
![DBMSのDB構成イメージ - Oracle Database](assets\NDB-CDM\ndb-23.png)

障害発生時もデータの整合性を保つため、REDOログとデータは、別のタイミングで書き出されます。
![DBMSのデータ更新イメージ - Oracle Database](assets\NDB-CDM\ndb-24.png)

そして、データの書き出しのみを静止することで、オンライン バックアップできるようになっています。

このとき REDOログ ファイルの書き出しは継続されており、アーカイブログ モードになっている必要があります。そのため、NDB で管理される Oracle Database もアーカイブログ モードに設定されている必要があります。

![DBMSのオンライン バックアップ - Oracle Database](assets\NDB-CDM\ndb-25.png)


## 参考情報2: タイムマシンによるデータ管理の詳細（Oracle Database Single Instance の場合）

タイムマシンによるデータ管理では、スナップショットとログ キャッチアップが併用されています。これは、DB サーバー VM / DB のコンポーネントごとに使い分けられています。
![DB サーバ VM の仮想ディスク構成 - Oracle Database](assets\NDB-CDM\ndb-26.png)

NDB によってプロビジョニングでは、タイムマシンで扱いやすいディスク構成で DB サーバ　VM が作成され、ゲスト OS 側でも VG / LV / ファイルシステムが作成ます。
![DB サーバ VM の仮想ディスク構成 - Oracle Database - AHV to Guest](assets\NDB-CDM\ndb-27.png)

そして、下記のように構成で、DBMS / DB が配置されます。
![DB サーバ VM の仮想ディスク構成 - Oracle Database - AHV to Guest](assets\NDB-CDM\ndb-28.png)

> - [Blog] NDB で作成した Oracle Database の様子。（Single Instance DB Server VM - vDisk 編）    
>   https://blog.ntnx.jp/entry/2023/02/28/235802

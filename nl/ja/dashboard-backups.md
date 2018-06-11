---

copyright:
  years: 2017,2018
lastupdated: "2018-03-02"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# バックアップ
{: #backups}

サービス・ダッシュボードの_「管理」_ページの_「バックアップ (Backups)」_タブから、バックアップの作成とダウンロードを行えます。 日次、週次、月次、オンデマンドでのバックアップを使用できます。 これらは、以下のスケジュールに従って保持されます。

バックアップ・タイプ|保持スケジュール
----------|-----------
日次|日次バックアップは 7 日間保持されます
週次|週次バックアップは 4 週間保持されます
月次|月次バックアップは 3 カ月保持されます
オンデマンド|オンデマンド・バックアップは 1 つ保持されます。 保持されるバックアップは、常に最新のオンデマンド・バックアップです。
{: caption="表 1. バックアップ保持スケジュール" caption-side="top"}

バックアップ・スケジュールと保持ポリシーは固定されています。 保持スケジュールで許可されているよりも多くのバックアップを保持する必要がある場合は、バックアップをダウンロードし、ビジネス要件に従ってアーカイブを保持する必要があります。

## 既存のバックアップの表示

データベースの日次バックアップは自動的にスケジュールされます。 既存のバックアップを表示するには、サービス・ダッシュボードの*「管理」*ページに移動します。 

  ![バックアップ](./images/rabbitmq-backups-show.png "サービス・ダッシュボードに表示されるバックアップのリスト")

対応する行をクリックして、選択可能バックアップのオプションを展開します。

  ![バックアップ・オプション](./images/rabbitmq-backups-options.png "バックアップのオプション") 

### API を使用した既存のバックアップの表示

バックアップのリストを `GET /2016-07/deployments/:id/backups` エンドポイントで取得できます。サービス・インスタンス ID を含むファウンデーション・エンドポイントと、デプロイメント ID は、両方ともサービスの_「概要」_に表示されます。例: 
``` 
https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
```  

## オンデマンドでのバックアップ作成

スケジュールされたバックアップだけでなく、バックアップを手作業で作成することができます。 手動でバックアップを作成する場合は、サービス・ダッシュボードの*「管理」*ページに移動して、*「今すぐバックアップ」*をクリックします。

### API を使用したバックアップの作成

backups エンドポイントに POST 要求 `POST /2016-07/deployments/:id/backups` を送信して、手動でバックアップを開始できます。この要求はただちに戻り、実行中のバックアップのレシピ ID と情報を返します。バックアップを使用するには、バックアップが完了したかどうかを backups エンドポイントで確認し、バックアップ ID を見つける必要があります。`GET /2016-07/deployments/:id/backups/` を使用します。

## バックアップのダウンロード

バックアップをダウンロードするには、サービス・ダッシュボードの*「管理」*ページに移動し、ダウンロードするバックアップに対応する行で*「ダウンロード」*をクリックします。

### API を使用したバックアップのダウンロード

サービスの_「バックアップ」_ページで、リストアするバックアップを見つけて backup_id をコピーするか、`GET /2016-07/deployments/:id/backups` を使用して、Compose API でバックアップとその backup_id を見つけます。次に、backup_id を使用して、特定のバックアップの情報とダウンロード・リンクを取得します (`GET /2016-07/deployments/:id/backups/:backup_id`)。

## バックアップの内容

RabbitMQ バックアップは、ブローカーのメタデータの JSON 表現です。 RabbitMQ 管理プラグインに用意されているエクスポート・コマンドで作成できます。 サービスでエクスポートを実行しても、パフォーマンスへの影響はありません。

## ローカル・データベースのバックアップの使用

{{site.data.keyword.composeForRabbitMQ}} のバックアップ機能を使用して、データベースのローカル・コピーを実行できます。

RabbitMQ ディストリビューションに組み込まれている管理プラグインによって、RabbitMQ のローカル・インスタンスを実行しておく必要があります。 `rabbitmq-plugins enable rabbitmq_management` で有効にしてください。 そうすると、さらに以下の機能も追加されます。

* 管理 UI (`http://localhost:15672/`)
* HTTP API (`http://server-name:15672/api/`)
* API のコマンド・ライン・ツール `rabbitmqadmin` (`http://localhost:15672/cli/`)

JSON バックアップ・ファイルをインポートするには、以下のようにします。

* 管理 UI (http://localhost:15672/) の場合は、_「概要」_ページの下部にある_「定義のインポート/エクスポート」_機能を使用します。
* API の場合は、POST を `http://server-name:15672/api/definitions` に送信します。以下に例を示します。
```http
curl -i -u guest:guest -H "content-type:application/json" -X POST --data @<path_to_your_rabbitmq_backup> http://localhost:15672/api/definitions
```
* `rabbitmqadmin import <your_rabbitmq_backup>` コマンドを使用します。

## バックアップのリストア

新しいサービス・インスタンスにバックアップをリストアするには、既存のバックアップを表示する手順を実行してから、対応する行をクリックして、ダウンロードするバックアップのオプションを展開表示します。 **「リストア (Restore)」**ボタンをクリックします。 復元が開始されたことを示すメッセージが表示されます。 新しいサービス・インスタンスに rabbitmq-restore-[timestamp] という名前が自動的に設定され、プロビジョニングが開始されるとその名前がダッシュボードに表示されます。

### {{site.data.keyword.cloud_notm}} CLI を使用したリストア

{{site.data.keyword.cloud_notm}} CLI を使用して、実行中の RabbitMQ サービスのバックアップを新しい RabbitMQ サービスにリストアするには、次の手順を実行します。 
1. 必要に応じて、[ CLI をダウンロードしてインストールします](https://console.bluemix.net/docs/cli/index.html#overview)。 
2. サービスの_「バックアップ」_ページで、リストアするバックアップを見つけ、バックアップ ID をコピーします。  
  **または**  
`GET /2016-07/deployments/:id/backups` を使用して、Compose API でバックアップとその ID を見つけます。ファウンデーション・エンドポイントとサービス・インスタンス ID は、両方ともサービスの _「概要」_に表示されます。例: 
  ``` 
  https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
  ```  
応答には、そのサービス・インスタンスに使用可能なすべてのバックアップのリストが含まれています。リストアするバックアップを選択し、その ID をコピーします。

3. 適切なアカウントと資格情報を使用してログインします。`bx login` (または、すべてのログイン・オプションを表示するには、`bx login -help` を使用)

4. 組織とスペースに切り替えます (`bx target -o "$YOUR_ORG" -s "YOUR_SPACE"`)。

5. `service create` コマンドを使用して新規サービスをプロビジョンし、リストアするソース・サービスと特定のバックアップを JSON オブジェクトで指定します。例:
``` 
bx service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": "$BACKUP_ID" }'
```
_「SERVICE」_フィールドは compose-for-rabbitmq、_「PLAN」_フィールドはご使用の環境に応じて「Standard」または「Enterprise」のいずれかでなければなりません。_SERVICE\_INSTANCE\_NAME_ は、新規サービスの名前を入力する場所です。_source\_service\_instance\_id_ は、バックアップのソースのサービス・インスタンス ID です。`bx cf service DISPLAY_NAME --guid` を実行して取得できます。ここで、_DISPLAY\_NAME_ はバックアップ元の RabbitMQ サービスの名前です。 
  
  
### 新規バージョンへのマイグレーション

一部のメジャー・バージョンのアップグレードは、現在実行中のデプロイメントでは使用できません。アップグレードしたバージョンを実行する新規サービスをプロビジョンしてから、バックアップを使用してそのサービスにデータをマイグレーションする必要があります。このプロセスは、アップグレード先のバージョンを指定することを除いて、前述のバックアップのリストアと同じです。

``` 
bx service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": ""$BACKUP_ID", "db_version":"$VERSION_NUMBER" }'
```

例えば、古いバージョンの {{site.data.keyword.composeForRabbitMQ}} サービスを RabbitMQ 3.6.14 を実行する新規サービスにリストアする場合は、次のようにします。
```
bx service create compose-for-rabbitmq Standard migrated_rabbit -c '{ "source_service_instance_id": "0269e284-dcac-4618-89a7-f79e3f1cea6a", "backup_id":"5a96d8a7e16c090018884566", "db_version":"3.6.14"  }'


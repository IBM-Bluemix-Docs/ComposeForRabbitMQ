---

copyright:
  years: 2016,2018
lastupdated: "2018-03-27"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.composeForRabbitMQ}} の概要
{: #about-compose-for-rabbitmq}

RabbitMQ は、アプリケーションとデータベースの間のメッセージを非同期的に処理し、データ層とアプリケーション層の分離を可能にします。 開発者は RabbitMQ を利用すると、カスタマイズ可能な永続性レベル、送達設定、確認済みパブリケーションを指定してメッセージをルーティングし、追跡し、キューに入れることができます。 {{site.data.keyword.composeForRabbitMQ_full}} を使用すると、デプロイメントのモニタリング、ボタン・クリックによるスケーリング、ユーザー・セットアップ、ログ・ファイル・アクセスなどといった多数の管理機能を備えた、使いやすい管理インターフェースにアクセスできます。
{:shortdesc}

**注:** 2016 年 9 月 14 日より前にプロビジョンされた、現在もアクティブな Compose サービス・インスタンスは引き続き使用可能で、[https://www.compose.com/](https://www.compose.com) から直接アクセスできます。 その時点以降にプロビジョンされた Compose サービス・インスタンスは、{{site.data.keyword.cloud}} アカウント内で直接アクセスして使用されます。

## {{site.data.keyword.composeForRabbitMQ}} サービス・インスタンスの作成

{{site.data.keyword.composeForRabbitMQ}} サービスは、{{site.data.keyword.cloud_notm}} カタログの [{{site.data.keyword.composeForRabbitMQ}} ページ](https://console.{DomainName}/catalog/services/compose-for-rabbitmq/)から作成できます。

サービス名、およびサービスをプロビジョンする地域、組織、スペースを選択します。 **「データベースのバージョンの選択 (Select a database version)」**フィールドを使用して、データベースの使用できるバージョンを選択できます。

{{site.data.keyword.composeForRabbitMQ}} インスタンスをプロビジョンするときには、*標準*プランか*エンタープライズ*・プランを選択できます。 *エンタープライズ*・プランでは、{{site.data.keyword.composeForRabbitMQ}} インスタンスを利用可能な {{site.data.keyword.composeEnterprise}} クラスターにプロビジョンできます。 {{site.data.keyword.composeEnterprise}} は、企業コンプライアンスで要求されるセキュリティーと分離を提供し、専用ネットワーキングを使用してデプロイ済みデータベースのパフォーマンスを確保します。 詳しくは、[Compose Enterprise 文書](../ComposeEnterprise/index.html)を参照してください。

## {{site.data.keyword.composeForRabbitMQ}} の管理

サービス・ダッシュボードからサービスを管理できます。 ダッシュボードには {{site.data.keyword.cloud_notm}} Compose データベースとそれへの接続方法に関する情報が示されます。 また、以下の操作を行うこともできます。
- バックアップを管理する 
- サービスに割り振るリソースを増やす 
- サービス・パスワードを変更する
- ホワイトリストを使用してデータベースへのアクセスを制限する。 

詳しくは、[設定](./dashboard-settings.html)を参照してください。

{{site.data.keyword.composeRabbitMQ}} は、Cloud Foundry の役割を利用して、サービスへのアクセスを管理します。開発者役割を持つユーザーのみが、サービス・ダッシュボードを表示または使用できます。Cloud Foundry の役割について詳しくは、『[Cloud Foundry アクセス権限](https://console.bluemix.net/docs/iam/cfaccess.html#cfaccess)』および 『[Cloud Foundry のアクセス権限の管理](https://console.bluemix.net/docs/iam/mngcf.html#mngcf)』のページを参照してください。
{: .tip}

## {{site.data.keyword.composeForRabbitMQ}} への接続

サービスに接続するには、サービスと一緒に作成された資格情報を使用するか、サービス・ダッシュボードの*「概要」*タブに表示される接続ストリングとコマンド・ラインを使用します。

## {{site.data.keyword.composeForRabbitMQ}} への {{site.data.keyword.cloud_notm}} アプリケーションの接続

{{site.data.keyword.cloud_notm}} アプリケーションをサービスに接続するには、サービスと一緒に作成された資格情報を使用します。 {{site.data.keyword.cloud_notm}} アプリケーションを {{site.data.keyword.composeForRabbitMQ}} サービスに接続する方法については、[{{site.data.keyword.cloud_notm}} アプリケーションの接続](./connecting-bluemix-app.html)を参照してください。

## {{site.data.keyword.cloud_notm}} 外からの {{site.data.keyword.composeForRabbitMQ}} への接続

{{site.data.keyword.composeForRabbitMQ}} に {{site.data.keyword.cloud_notm}} の外部から接続するには、提供された接続ストリングまたはコマンド・ラインを使用します。 接続方法については、[外部アプリケーションの接続](./connecting-external.html)を参照してください。

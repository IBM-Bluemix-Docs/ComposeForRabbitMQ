---

copyright:
  years: 2016,2018
lastupdated: "2017-10-23"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 关于 {{site.data.keyword.composeForRabbitMQ}}
{: #about-compose-for-rabbitmq}

RabbitMQ 可在应用程序和数据库之间异步处理消息，从而实现数据和应用层的分离。通过 RabbitMQ，开发者可以利用可定制的持久性级别、交付设置和已确认发布，递送、跟踪消息，并将消息移入队列。使用 {{site.data.keyword.composeForRabbitMQ_full}}，您可以获得易用管理界面的访问权，其中包括一系列管理功能，如部署监视、单击按钮扩展、用户设置和日志文件访问等。
{:shortdesc}

**注：**在 2016 年 9 月 14 日之前供应的任何仍处于活动状态的 Compose 服务实例仍可通过 [https://www.compose.com/](https://www.compose.com) 使用和直接访问。从此处供应的任何 Compose 服务实例都可在 {{site.data.keyword.cloud}} 帐户中直接访问和使用。

## 创建 {{site.data.keyword.composeForRabbitMQ}} 服务实例

您可以在 {{site.data.keyword.cloud_notm}}“目录”的 [{{site.data.keyword.composeForRabbitMQ}} 页面](https://console.{DomainName}/catalog/services/compose-for-rabbitmq/)中创建 {{site.data.keyword.composeForRabbitMQ}} 服务。

选择服务名称以及要在其中供应该服务的区域、组织和空间。可以使用**选择数据库版本**字段从可用数据库版本中进行选择。

供应 {{site.data.keyword.composeForRabbitMQ}} 实例时，可以选择*标准*或*企业*套餐。使用*企业*套餐，您可以将 {{site.data.keyword.composeForRabbitMQ}} 实例供应到可用的 {{site.data.keyword.composeEnterprise}} 集群中。{{site.data.keyword.composeEnterprise}} 提供企业合规性所需的安全性和隔离，并使用专用网络来确保已部署的数据库的性能。有关更多详细信息，请参阅 [Compose Enterprise 文档](../ComposeEnterprise/index.html)。

## 管理 {{site.data.keyword.composeForRabbitMQ}}

您可以从服务仪表板管理服务。您可以在此找到有关 {{site.data.keyword.cloud_notm}} Compose 数据库以及如何连接到该数据库的信息。您还可以：
- 管理备份 
- 为服务分配更多资源 
- 更改服务密码
- 使用白名单来限制对数据库的访问。
有关更多信息，请参阅[设置](./dashboard-settings.html)。

## 连接到 {{site.data.keyword.composeForRabbitMQ}}

您可以使用随服务一起创建的凭证，或者使用服务仪表板*概述*选项卡中提供的连接字符串和命令行，来连接到服务。

## 将 {{site.data.keyword.cloud_notm}} 应用程序连接到 {{site.data.keyword.composeForRabbitMQ}}

要将 {{site.data.keyword.cloud_notm}} 应用程序连接到服务，请使用随服务一起创建的凭证。您可以在[连接 {{site.data.keyword.cloud_notm}} 应用程序](./connecting-bluemix-app.html)中查找有关如何将 {{site.data.keyword.cloud_notm}} 应用程序连接到 {{site.data.keyword.composeForRabbitMQ}} 服务的信息。

## 从外部 {{site.data.keyword.cloud_notm}} 连接到 {{site.data.keyword.composeForRabbitMQ}}

如果要从外部 {{site.data.keyword.cloud_notm}} 连接到 {{site.data.keyword.composeForRabbitMQ}}，可以使用提供的连接字符串或命令行。您可以在[连接外部应用程序](./connecting-external.html)中找到有关如何连接的信息。

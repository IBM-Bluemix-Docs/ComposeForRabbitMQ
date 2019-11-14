---

copyright:
  years: 2016,2018
lastupdated: "2018-03-27"

keywords: rabbitmq, compose

subcollection: compose-for-rabbitmq

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# About {{site.data.keyword.composeForRabbitMQ}}
{: #about}

RabbitMQ asynchronously handles the messages between your applications and databases, enabling the separation of the data and application layers. RabbitMQ enables developers to route, track, and queue messages with customizable persistence levels, delivery settings, and confirmed publication. By using {{site.data.keyword.composeForRabbitMQ_full}}, you get access to the easy-to-use administrative interface with a host of management features such as deployment monitoring, click-of-a-button scaling, user setup, and log file access.
{:shortdesc}

**Note:** Any Compose service instances that were provisioned before 14 September 2016 that are still active can still be used and directly accessed at [https://www.compose.com/](https://www.compose.com). Any Compose service instance that is provisioned from this point forward is directly accessed and used within your {{site.data.keyword.cloud}} account.

## Creating a {{site.data.keyword.composeForRabbitMQ}} service instance

You can create a {{site.data.keyword.composeForRabbitMQ}} service from the [{{site.data.keyword.composeForRabbitMQ}} page](https://{DomainName}/catalog/services/compose-for-rabbitmq/) in the {{site.data.keyword.cloud_notm}} catalog.

Choose a service name, and a region, organization and space to provision the service in. You can use the **Select a database version** field to choose from the available database versions.

When you provision your {{site.data.keyword.composeForRabbitMQ}} instance you can choose the *Standard* or *Enterprise* plans. With the *Enterprise* plan, you can provision your {{site.data.keyword.composeForRabbitMQ}} instance into an available {{site.data.keyword.composeEnterprise}} cluster. {{site.data.keyword.composeEnterprise}} provides the security and isolation required by enterprise compliance and uses dedicated networking to ensure the performance of the deployed databases. See the [{{site.data.keyword.composeEnterprise}} documentation](/docs/services/ComposeEnterprise?topic=compose-enterprise-about) for more details.

## Managing {{site.data.keyword.composeForRabbitMQ}}

You can manage your service from the service dashboard. Here you can find information about your {{site.data.keyword.cloud_notm}} Compose database and how to connect to it. You can also:
- manage your backups 
- allocate more resources for your service 
- change the service password
- use whitelists to restrict access to your databases. 

For more information, see [Settings](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-dashboard-settings).

{{site.data.keyword.composeForRabbitMQ}} relies on Cloud Foundry roles to manage access to the service. Only users with the Developer role can see or use the service dashboard. For more information on Cloud Foundry roles, see the [Cloud Foundry access](/docs/iam?topic=iam-cfaccess) and the [Managing Cloud Foundry access](/docs/iam?topic=iam-mngcf) pages.
{: tip}

## Connecting to {{site.data.keyword.composeForRabbitMQ}}

You can connect to your service using the credentials that are created along with the service, or with the connection strings and command line that are provided in the *Overview* tab of your service dashboard.

## Connecting an {{site.data.keyword.cloud_notm}} application to {{site.data.keyword.composeForRabbitMQ}}

To connect an {{site.data.keyword.cloud_notm}} application to your service, use the credentials that are created along with the service. You can find information on how to connect an {{site.data.keyword.cloud_notm}} application to a {{site.data.keyword.composeForRabbitMQ}} service in [Connecting an {{site.data.keyword.cloud_notm}} Application](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-ibmcloud-cf-app).

## Connecting to {{site.data.keyword.composeForRabbitMQ}} from outside {{site.data.keyword.cloud_notm}}

If you want to connect to {{site.data.keyword.composeForRabbitMQ}} from outside {{site.data.keyword.cloud_notm}}, you can use the provided connection strings or command line. You can find information on how to connect in [Connecting an external application](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-connecting-external).

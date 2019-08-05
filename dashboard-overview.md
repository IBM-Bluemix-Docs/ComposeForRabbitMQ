---

Copyright:
  years: 2017,2018
lastupdated: "2017-11-20"

keywords: rabbitmq, compose

subcollection: compose-for-rabbitmq

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Service Overview
{: #dashboard-overview}

The _Overview_ page shows you information about your {{site.data.keyword.cloud}} Compose database. The overview includes essential identifying information and current resource usage. You'll also find a section for connection strings that you can use with tools or make use of tools to connect to your database.

## Deployment Details

The _Deployment Details_ panel shows details of your service.

![Deployment Details](./images/rabbitmq-deployment-details.png "A view of the Deployment Details panel")

### Type

The type of database that is offered by the service, and the database version that your service uses. If a more recent database version is available, a notification is displayed, together with a link to the [Upgrade version](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-dashboard-settings) section of your service dashboard.

### ID

An internal identifier for the service.

### Usage

The size of your database and the amount of storage that is provided by your service plan.

## Recent Tasks

Making administrative changes to your service (such as scaling, or taking a manual backup) starts a task. While the task is running, the _Recent Tasks_ panel shows the task name and a progress bar.

## Connection Strings

You'll find each Connection String for your service in a different tab in the _Connection Strings_ panel.

### HTTPS

The **HTTPS** connection string can be used by some client libraries and contains all the information that is needed for other libraries to connect. You can find out how to use the connection string to connect in [Connecting an external application](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-connecting-external).

All Compose RabbitMQ deployments only accept `amqps://` TLS secured connections, which are backed up with a self-signed certificate on the server.

### Admin

Click the link on the **Admin** tab to open the _RabbitMQ Management_ page. The login information is in the **HTTPS** connection string following `amqps://` and before the `@`.

## Instance Administration API

You can manage your {{site.data.keyword.composeForRabbitMQ}} service through the {{site.data.keyword.cloud_notm}} Compose API.

### Foundation Endpoint

The foundation endpoint is composed of the region the service resides in and the service instance ID. It is at the start of every endpoint.

### Deployment ID

The deployment ID is necessary for most calls, and identifies the specific deployment instance.

### Reference

For more information about using the {{site.data.keyword.cloud_notm}} Compose API, across all {{site.data.keyword.cloud_notm}} Compose services, see [The {{site.data.keyword.cloud_notm}} Compose API](https://www.compose.com/articles/the-ibm-cloud-compose-api/).


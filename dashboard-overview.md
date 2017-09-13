---

Copyright:
  Years: 2017
lastupdated: "2017-09-06"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Service Overview

The _Overview_ page shows you information about your Bluemix Compose database. The overview includes essential identifying information and current resource usage. You'll also find a section for connection strings that you can use with tools or make use of tools to connect to your database.

## Deployment Details

The _Deployment Details_ panel shows details of your service.

![Deployment Details](./images/deployment-details.png "A view of the Deployment Details panel")

### Type

The type of database that is offered by the service; in this case `RabbitMQ`.

### Name

An internal identifier for the service.

### Usage

The size of your database and the amount of storage provided by your service plan.

### Version

The database version that your service uses.

### Scale Resouces

If your service needs additional memory, or you want to reduce the amount of memory allocated to your service, you can do this by scaling resources. See [Scaling Resources](./dashboard-scaling-resources.html) for more details.


## Connection Strings

You'll find each Connection String for your service in a different tab in the _Connection Strings_ panel.

### HTTPS

The **HTTPS** connection string can be used by some client libraries and contains all the information needed for other libraries to connect. You can find out how to use the connection string to connect in [Connecting an external application](./connecting-external.html).

All Compose RabbitMQ deployments only accept `amqps://` TLS secured connections which are backed up with a self-signed certificate on the server.

### Admin

The link on the **Admin** tab will open the _RabbitMQ Managment_ page. The login information is in the **HTTPS** connection string following the 'amqps://' and before the '@'.

### SSL Certificate

Your Compose Bluemix service provides you with an SSL certificate that you can use to connect to your database.


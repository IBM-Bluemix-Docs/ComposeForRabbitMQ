---

Copyright:
  Years: 2017
lastupdated: "2017-07-13"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Dashboard Overview

The service dashboard overview shows you information about your Bluemix Compose database. The overview includes essential identifying information and current resource usage. You'll also find a section for Connection Strings that you can use with tools or make use of tools to connect to your database.

## Deployment Details

The Deployment Details panel shows details of your service.

### Database

The type of database that is offered by the service; in this case `rabbitmq`.

### Usage

Shows the size of your database and the amount of storage provided by your service plan.

## Connecting

You can connect an external application to your service using a **Connection String**, which is provided on your service dashboard overview.

### Connection String

The **Connection String** can be used by some client libraries and contains all the information needed for other libraries to connect. You can find out how to use the Connection String to connect in [Connecting an external application](./connecting-external.html).

All Compose RabbitMQ deployments only accept `amqps://` TLS secured connections which are backed up with a Let's Encrypt certificate on the server.
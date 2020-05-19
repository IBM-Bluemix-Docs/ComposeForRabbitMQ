---
copyright:
  years: 2016,2018
lastupdated: "2018-06-13"

keywords: rabbitmq, compose

subcollection: ComposeForRabbitMQ

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Connection Architecture
{: #connection-architecture}

{{site.data.keyword.composeForRabbitMQ_full}} database connections are managed by 2 HAProxy portals. Each portal has 64 MB of memory. The two portals to allow for applications to maintain connectivity should one of the portals become unreachable.

Failover at the client side is the responsibility of the application designer. If you aren't working with a driver that completely handles failover errors, take the following steps to handle errors yourself.

* Work with a driver that accepts multiple endpoints in its connection configuration.
* Ensure that your application reacts to errors when it to reads from or writes to the database. You can configure your application to react to errors in different ways, including the following:
  + Log the error and reconnect.
  + Reconnect and retry the operation that caused the error.
  + Queue the failed operation and schedule a reconnection.

## Encryption in Transit

All {{site.data.keyword.composeForRabbitMQ}} HAProxy portals have TLS/SSL enabled and support TLS version 1.2. The certificates for the service are Let's Encrypt certificates.

## Connection Limits

Database connections have a limit of 1000 connections per portal. Exceeding the connection limit will make your service unresponsive. You can manage connections from your application through your driver's connection pooling feature.

## Proxy Timeouts

The HAProxy portals manage the lifecycle of connections between the server and client. They are detailed them here as a reference for driver writers and others who have an interest in the low-level activity of {{site.data.keyword.composeForRabbitMQ}} database connections.

Setting | Value | Applies
----------|-----------|-----------
`client` | 3 h | When the client is expect to acknowledge or send data.
`connect` | 10 s | When a connection is being made between the proxy and the server.
`server` | 3 h | When the server is expected to acknowledge or send data.
`check` | 5 s | As a secondary timeout check when a connection is being made between the proxy and the server.
{: caption="Table 1. RabbitMQ HAProxy timeouts" caption-side="top"}





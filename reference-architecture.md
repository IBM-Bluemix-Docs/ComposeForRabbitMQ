---
copyright:
  years: 2016,2018
lastupdated: "2018-06-13"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Architecture 
{: #architecture}

## Replication

An {{site.data.keyword.composeForRabbitMQ_full}} service contains three nodes acting unanimously as a highly available broker, each running the RabbitMQ application and sharing users, virtual hosts, queues, exchanges, bindings, and runtime parameters. When you select a region where the service is deployed, the three nodes spread over the region's availability zones, and all data and state required for the operation of a RabbitMQ broker is replicated across all nodes. 

One node functions as a master and the others function as mirrors. The mirrors apply the operations that occur to the master in exactly the same order as the master and thus maintain the same state. All actions other than a publish action go only to the master, and the master then broadcasts the effect of the actions to the mirrors. If the master fails, one of the mirrors is promoted and the RabbitMQ broker functions normally.

## Disk Encryption

All {{site.data.keyword.composeForRabbitMQ}} services have encryption at rest. Your data resides on servers that have volume-level encryption enabled. 

Because this is {{site.data.keyword.composeForRabbitMQ}} is a managed service, it is possible for {{site.data.keyword.cloud}} Compose operators to see data. In addition to the disk encryption, we recommend that if you are passing personal information through your messaging queue that you encrypt information before sending it or by using extensions or features to enable encryption on RabbitMQ itself. While these methods may impact usability or performance, it is good practice to ensure that personal information is protected with encryption.


## Auto-scaling

Resources are scaled automatically based on the total memory use of the RabbitMQ broker. Storage is based on a ratio of provisioned RAM at 1:1. These resources are bundled as units.

Auto-scaling is designed to respond to the short-to-medium term trends of your database. Every minute, your service is checked and if it is running short on RAM, then more units are allocated to the deployment. 

Auto-scaling does not scale down deployments where disk/memory usage has shrunk. The resources provisioned to your databases remain for your future needs, or until you scale down your deployment manually.
{: tip}

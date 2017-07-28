---

copyright:
  years: 2017
lastupdated: "2017-07-13"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Backups
{: #backups}

You can create and download backups from the *Manage* page of your service dashboard. Both scheduled and manual backups are available.

## Viewing existing backups

Daily backups of your database are automatically scheduled. To view your existing backups, navigate to the *Manage* page of your service dashboard. 

![Backups](./images/pgbackups.png "A list of backups in the service dashboard")

## Creating a backup on demand

As well as scheduled backups you can create a backup manually. To create a manual backup, navigate to the *Manage* page of your service dashboard and click *Backup now*.

## Downloading a backup

To download a backup, navigate to the *Manage* page of your service dashboard and click *Download* in the corresponding row for the backup you wish to download.

## Backup contents

RabbitMQ backups are a JSON representation of your broker's metadata. They are created from an export command provided by the RabbitMQ management plugin. Running the export on your serivice does not impact performance.

## Using a backup with a local database

You can use your {{site.data.keyword.composeForRabbitMQ}} backup to run a local copy of your database.

You will need to have a local instance of RabbitMQ running, with the management plug-in included in the RabbitMQ distribution. Enable it with `rabbitmq-plugins enable rabbitmq_management`. This additionally will get you:

* the Admin UI at `http://localhost:15672/`,
* the HTTP API at  `http://server-name:15672/api/`,
* and the API's command-line tool `rabbitmqadmin` at `http://localhost:15672/cli/ `.

To import the JSON backup file, you can:

* through the Admin UI at http://localhost:15672/, use the _Import/Export definitions_ function at the bottom of the _Overview_ page.
* through the API, send a POST to `http://server-name:15672/api/definitions` example:
```http
curl -i -u guest:guest -H "content-type:application/json" -X POST --data @<path_to_your_rabbitmq_backup> http://localhost:15672/api/definitions
```
* use the command `rabbitmqadmin import <your_rabbitmq_backup>`.

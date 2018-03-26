---

copyright:
  years: 2016,2018
lastupdated: "2017-06-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Connessione a un'applicazione {{site.data.keyword.cloud_notm}}

Per collegare un'applicazione {{site.data.keyword.cloud}} al tuo servizio, utilizza le credenziali create quando è stato eseguito il provisioning del servizio. L'applicazione di esempio illustra come utilizzare Node.js per il collegamento a un servizio {{site.data.keyword.composeForRabbitMQ_full}} utilizzando le credenziali fornite e come creare un database e come leggere e scrivere in esso.

## Collegamento utilizzando l'applicazione di esempio 'Hello World'

L'applicazione di esempio [compose-rabbitmq-helloworld-nodejs](https://github.com/IBM-Bluemix/compose-rabbitmq-helloworld-nodejs) illustra come utilizzare Node.js per il collegamento a un servizio {{site.data.keyword.composeForRabbitMQ}} utilizzando le credenziali fornite. L'applicazione crea, legge da, e scrive su un database.

Scarica l'applicazione di esempio e segui le istruzioni nel file readme. In seguito, nella pagina dei dettagli della tua applicazione in {{site.data.keyword.cloud_notm}}, fai clic su **Visualizza applicazione** per visualizzare il contenuto della tabella *esempi*.

## Credenziali disponibili

Nome campo|Descrizione
----------|-----------
`uri`|L'URI da utilizzare durante il collegamento al servizio. Include lo schema (`amqps:), il nome utente e la password amministratore, il nome host del server, il numero di porta a cui collegarsi e il nome vhost.
`uri_direct_1`|Un URI alternativo da utilizzare durante il collegamento al servizio. Formato come l'`uri`.
`uri_admin`|Un URI in cui recarsi con un browser per accedere all'interfaccia di gestione del database. L'accesso richiede il nome utente e la password amministratore dal campo `uri`.
`uri_admin_1`|Un URI di gestione alternativo - consulta `uri_admin`.
`uri_admin_2`|Un URI di gestione alternativo - consulta `uri_admin`.
`uri_admin_3`|Un URI di gestione alternativo - consulta `uri_admin`.
`uri_admin_4`|Un URI di gestione alternativo - consulta `uri_admin`.
`ca_certificate_base64`|Un certificato auto-firmato che viene utilizzato per confermare che un'applicazione sia collegata al server corretto. Si basa su codifica base64. Devi decodificare la chiave prima di utilizzarla, come mostrato nell'applicazione di esempio.
`deployment_id`|Un identificativo interno per il servizio come creato in Compose.
`db_type`|Il tipo di database offerto dal servizio; in questo caso `rabbitmq`.
`name`|Il nome di distribuzione del database.
{: caption="Tabella 1. Credenziali di Compose for RabbitMQ" caption-side="top"}

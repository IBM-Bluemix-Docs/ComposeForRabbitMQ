---

copyright:
  years: 2017,2018
lastupdated: "2017-10-16"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Backup
{: #backups}

Puoi creare e scaricare i backup dalla scheda _Backups_ della pagina _Manage_ del tuo dashboard del servizio. Sono disponibili backup giornalieri, settimanali, mensili e su richiesta. Vengono conservati in base alla seguente pianificazione:

Tipo di backup|Pianificazione conservazione
----------|-----------
Giornaliero|I backup giornalieri sono conservati per 7 giorni
Settimanale|I backup settimanali sono conservati per 4 settimane
Mensile|I backup mensili sono conservati per 3 mesi
Su richiesta|Il backup su richiesta viene conservato. Il backup conservato è sempre il più recente backup su richiesta.
{: caption="Tabella 1. Pianificazione conservazione backup" caption-side="top"}

Le politiche di conservazione e di pianificazione del backup sono fissate. Se hai bisogno di conservare più backup rispetto a quelli consentiti dalla pianificazione di conservazione, devi scaricare i backup e conservare gli archivi in base ai tuoi requisiti di business.

## Visualizzazione dei backup esistenti

I backup giornalieri del tuo database vengono automaticamente pianificati. Per visualizzare i tuoi backup esistenti, passa alla pagina *Manage* del tuo dashboard del servizio. 

![Backup](./images/rabbitmq-backups-show.png "Un elenco di backup nel dashboard del servizio")

Fai clic sulla riga corrispondente per espandere le opzioni per ogni backup disponibile.

![Opzioni backup](./images/rabbitmq-backups-options.png "Opzioni per il backup.") 

## Creazione di un backup su richiesta

Come per i backup pianificati puoi creare un backup manualmente. Per creare un backup manuale, passa alla pagina *Manage* del tuo dashboard del servizio e fai clic su *Backup now*.

## Scaricamento di un backup

Per scaricare un backup, passa alla pagina *Manage* del tuo dashboard del servizio e fai clic su *Download* nella riga corrispondente del backup che desideri scaricare.

## Contenuti del backup

I backup RabbitMQ sono una rappresentazione JSON dei metadati del tuo broker. Sono creati da un comando export fornito dal plugin di gestione RabbitMQ. Eseguendo export nel tuo servizio non si influenzano le prestazioni.

## Utilizzo di un backup con un database locale

Puoi utilizzare il tuo backup {{site.data.keyword.composeForRabbitMQ}} per eseguire una copia locale del tuo database.

Dovrai disporre di un'istanza locale di RabbitMQ in esecuzione, col il plugin di gestione incluso nella distribuzione RabbitMQ. Abilitalo con `rabbitmq-plugins enable rabbitmq_management`. Inoltre è possibile ottenere:

* la IU di gestione all'indirizzo `http://localhost:15672/`,
* l'API HTTP all'indirizzo `http://server-name:15672/api/`,
* e lo strumento della riga di comando dell'API `rabbitmqadmin` all'indirizzo `http://localhost:15672/cli/ `.

Per importare il file di backup JSON, puoi:

* tramite la IU di gestione all'indirizzo http://localhost:15672/, utilizzare la funzione _Import/Export definitions_ in fondo alla pagina _Overview_.
* tramite l'API, invia un POST a `http://server-name:15672/api/definitions` esempio:
```http
curl -i -u guest:guest -H "content-type:application/json" -X POST --data @<path_to_your_rabbitmq_backup> http://localhost:15672/api/definitions
```
* utilizzare il comando `rabbitmqadmin import <your_rabbitmq_backup>`.

## Ripristino di un backup

Per ripristinare un backup in una nuova istanza, segui le istruzioni per visualizzare i backup esistenti, quindi fai clic sulla riga corrispondente per espandere le opzioni del backup che desideri scaricare. Fai clic sul pulsante **Restore**. Viene visualizzato un messaggio per farti sapere che è stato avviato un ripristino. La nuova istanza del servizio sarà automaticamente denominata "rabbitmq-restore-[timestamp]" e visualizzata nel tuo dashboard dopo l'avvio del provisioning.

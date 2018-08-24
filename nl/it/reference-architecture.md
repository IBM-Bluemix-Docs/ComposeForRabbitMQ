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

# Architettura 
{: #architecture}

## Replica

Un servizio {{site.data.keyword.composeForRabbitMQ_full}} contiene tre nodi che agiscono in modo anonimo come un broker di alta disponibilità, ognuno che esegue l'applicazione RabbitMQ e condivide gli utenti, gli host virtuali, le code, gli scambi, i bind e i parametri di runtime. Quando selezioni una regione in cui viene distribuito il servizio, i tre nodi vengono distribuiti nelle zone di disponibilità della regione e tutti i dati e gli stati necessari alla funzionalità di un broker RabbitMQ vengono replicati in tutti i nodi. 

Un nodo funziona come un master e gli altri come mirror. I mirror si applicano alle operazioni che si verificano nel master esattamente nello stesso ordine del master e quindi mantengono lo stesso stato. Tutte le azioni diverse da un'azione di pubblicazione, vengono applicate solo al master e il master poi trasmette l'effetto delle azioni ai mirror. Se il master riscontra un errore, uno dei mirror viene promosso e il broker RabbitMQ funziona normalmente.

## Crittografia del disco

Tutti i servizi {{site.data.keyword.composeForRabbitMQ}} hanno la crittografia a riposo. I tuoi dati si trovano su server che hanno la crittografia a livello di volume abilitata. 

Poiché questo {{site.data.keyword.composeForRabbitMQ}} è un servizio gestito, è possibile agli operatori {{site.data.keyword.cloud}} Compose di visualizzare i dati. In aggiunta alla crittografia del disco, ti consigliamo, se stai trasmettendo informazioni personali tramite la tua coda di messaggistica, di codificare le informazioni prima di inviarle o utilizzando le estensioni o le funzioni per abilitare la crittografia sullo stesso RabbitMQ. Anche se questi metodi possono influire sull'usabilità o sulle prestazioni, è buona norma assicurarsi che le informazioni personali siano protette con la crittografia.


## Scalabilità automatica

Le risorse vengono ridimensionate automaticamente in base all'utilizzo della memoria totale del broker RabbitMQ. L'archiviazione è basata su un rapporto di RAM di cui viene eseguito il provisioning di 1:1. Queste risorse sono raccolte in bundle come unità.

Il ridimensionamento automatico è progettato per rispondere alle tendenze a medio e a lungo termine del tuo database. Il tuo servizio viene controllato ogni minuto e, se sta esaurendo la RAM, delle ulteriori unità vengono assegnate alla distribuzione. 

Il ridimensionamento automatico non riduce le distribuzioni in cui l'utilizzo di disco/memoria si è ridotto. Le risorse di cui è stato eseguito il provisioning ai tuoi database rimangono per le tue future esigenze oppure finché non riduci manualmente la tua distribuzione.
{: .tip}

---

copyright:
  years: 2016,2018
lastupdated: "2018-01-03"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Prezzi
{: #pricing}

## Configurazione di base
Un servizio {{site.data.keyword.composeForRabbitMQ_full}} viene fornito come un cluster con due nodi di dati, ognuno che contiene 256MB di memoria e 256MB di archiviazione, che equivale a 1 unità di risorse. Il servizio _include_ la replica e l'elevata disponibilità, per cui ogni unità e il prezzo per unità _include_ il costo delle risorse in entrambi i nodi.

La configurazione di base include anche i due portali HAProxy. Ognuno dei due portali HAProxy ha 64MB di memoria.

La configurazione del servizio di base a un prezzo fissato. Consulta i tile del catalogo {{site.data.keyword.cloud_notm}} per i prezzi di base nella tua valuta locale. Ad esempio, il prezzo di base in dollari US è $19.50/mese.

## Aumento delle risorse
Se hai bisogno di ulteriore archiviazione o memoria per il tuo servizio, puoi aumentare le risorse assegnate in un rapporto 1:1 di archiviazione del disco per unità di memoria. L'aumento del disco assegnato alla distribuzione aumenterà anche la RAM assegnata. Un'unità {{site.data.keyword.composeForRabbitMQ}} è composta da 256MB di archiviazione e 256MB di memoria e ogni unità e prezzo per unità _include_ il costo di incremento delle risorse su entrambi i nodi.

## Calcolo del costo della tua distribuzione
{: #tiered-pricing}

Ogni unità aggiuntiva (256MB di archiviazione/256MB di memoria) ha un prezzo per unità, che viene elencato nella tua valuta corrente nel tile del catalogo {{site.data.keyword.cloud_notm}} per il servizio. In dollari US ogni unità aggiuntiva costa $19.50. Quando la dimensione _totale_ di tutti i tuoi servizi {{site.data.keyword.composeForRabbitMQ}} aumenta, il prezzo per unità diminuisce, come mostrato nella seguente tabella dei prezzi a livelli.

Numero di unità|Prezzo per unità
----------|-----------
1 - 9 unità|prezzo per unità di base -- $19.50 USD/Unità
10 - 24 unità|10% di riduzione -- $17.55 USD/Unità
25 - 49 unità|20% di riduzione -- $15.60 USD/Unità
50 - 99 unità|30% di riduzione -- $13.65 USD/Unità
100 - 499 unità|40% di riduzione --$11.70 USD/Unità
500 - 999 unità|50% di riduzione -- $9.75 USD/Unità
1.000 - 4.999 unità|60% di riduzione -- $7.80 USD/Unità
5.000+ unità|70% di riduzione -- $5.85 USD/Unità
{: caption="Tabella 1. Prezzi a livelli di {{site.data.keyword.composeForRabbitMQ}}" caption-side="top"}


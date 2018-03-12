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

# Preisstruktur
{: #pricing}

## Basiskonfiguration
Der {{site.data.keyword.composeForRabbitMQ_full}}-Service ist zunächst als ein Cluster mit zwei Datenknoten mit jeweils 256 MB Hauptspeicher und 256 MB Speicher konfiguriert. Die entspricht 1 Ressourceneinheit. Der Service _umfasst_ Replikation und Hochverfügbarkeit. In jedem Einzelpreis sind jeweils die Ressourcenkosten für beide Knoten _enthalten_.

Zur Basiskonfiguration gehören zudem zwei HAProxy-Portale. Jedes der beiden HAProxy-Portale hat 64 MB Hauptspeicher.

### Kosten
Die Basisservicekonfiguration hat einen Festpreis. Den Grundpreis in Ihrer Landeswährung finden Sie über die entsprechenden Katalogkacheln auf {{site.data.keyword.cloud_notm}}. Der Grundpreis in US-Dollar beträgt zum Beispiel $19,50/Monat.

## Erweiterungsoptionen
Wenn Sie zusätzlichen Speicher oder Hauptspeicher für Ihren Service benötigen, können Sie die zugeordneten Ressourcen in einem Verhältnis von 1:1 von Plattenspeicher zu Speichereinheit erhöhen. Durch Erhöhen des der Bereitstellung zugeordneten Plattenspeichers erhöht sich der zugeordnete RAM entsprechend. Eine {{site.data.keyword.composeForRabbitMQ}}-Einheit besteht aus 256 MB Speicher und 256 MB Hauptspeicher. Im Einzelpreis pro Einheit sind die Kosten für die Erweiterung der Ressourcen auf beiden Knoten _enthalten_.

### Kosten
Jede zusätzliche Einheit (256 MB Speicher/256 MB Hauptspeicher) hat einen Einzelpreis, der auf der {{site.data.keyword.cloud_notm}}-Katalogkachel für den Service in Ihrer Landeswährung aufgelistet ist. In US-Dollar beträgt der Preis für jede zusätzliche Einheit $19.50. Mit zunehmender _Gesamtgröße_ Ihrer {{site.data.keyword.composeForRabbitMQ}}-Services verringert sich der Preis pro Einheit, wie aus der folgenden Tabelle zur gestaffelten Preisstruktur hervorgeht.

### Gestaffelte Preisstruktur
Anzahl der Einheiten|Einzelpreis
----------|-----------
1 - 9 Einheiten|Basiseinzelpreis -- $19,50 USD/Einheit
10 - 24 Einheiten|10% Ermäßigung -- $17,55 USD/Einheit
25 - 49 Einheiten|20% Ermäßigung -- $15,60 USD/USD/Einheit
50 - 99 Einheiten|30% Ermäßigung -- $13,65 USD/Einheit
100 - 499 Einheiten|40% Ermäßigung -- $11,70 USD/Einheit
500 - 999 Einheiten|50% Ermäßigung -- $9,75 USD/Einheit
1.000 - 4.999 Einheiten|60% Ermäßigung -- $7,80 USD/Einheit
5.000+ Einheiten|70% Ermäßigung -- $5,85 USD/Einheit
{: caption="Tabelle 1. Gestaffelte Preisstruktur von {{site.data.keyword.composeForRabbitMQ}}" caption-side="top"}


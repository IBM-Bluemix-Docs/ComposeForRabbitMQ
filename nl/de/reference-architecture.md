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

# Architektur 
{: #architecture}

## Replikation

Ein {{site.data.keyword.composeForRabbitMQ_full}}-Service enthält drei Knoten, die einstimmig als hochverfügbarer Broker agieren und jeweils die RabbitMQ-Anwendung ausführen sowie Benutzer, virtuelle Hosts, Warteschlangen, Austauschvorgänge, Bindungen und Laufzeitparameter gemeinsam nutzen. Wenn Sie eine Region auswählen, in der der Service implementiert ist, verteilen sich die drei Knoten auf die Verfügbarkeitszonen der Region und alle für die Operation eines RabbitMQ-Brokers erforderlichen Daten und Status werden auf allen Knoten repliziert. 

Ein Knoten hat die Funktion des Masters und die übrigen Knoten fungieren als Spiegel. Die Spiegel wenden die Operationen, die beim Master auftreten, in genau derselben Reihenfolge wie beim Master an und sorgen somit für die Aufrechterhaltung desselben Status. Alle Aktionen mit Ausnahme der Veröffentlichung werden ausschließlich an den Master gerichtet und der Master sendet dann seinerseits die Auswirkungen dieser Aktionen an die Spiegel. Fällt der Master aus, wird einer der Spiegel entsprechend hochgestuft und der RabbitMQ-Broker kann weiterhin normal funktionieren.

## Plattenverschlüsselung

Für alle {{site.data.keyword.composeForRabbitMQ}}-Services erfolgt eine Verschlüsselung im Ruhezustand. Ihre Daten befinden sich auf Servern, auf denen die Verschlüsselung auf Datenträgerebene aktiviert ist. 

Da es sich bei {{site.data.keyword.composeForRabbitMQ}} um einen verwalteten Service handelt, sind Operatoren von Compose auf {{site.data.keyword.cloud}} in der Lage, Daten zu sehen. Wenn Sie persönliche Informationen über die Nachrichtenwarteschlange übergeben, wird zusätzlich zur Plattenverschlüsselung empfohlen, Informationen zu verschlüsseln, bevor sie gesendet werden, oder Erweiterungen bzw. Features zu verwenden, um die Verschlüsselung auf RabbitMQ selbst zu ermöglichen. Diese Methoden können zwar den Bedienungskomfort oder die Leistung beeinträchtigen, doch es hat sich bewährt, den Schutz persönlicher Informationen mit Verschlüsselung sicherzustellen.


## Automatische Skalierung

Die Ressourcen werden basierend auf der Gesamtspeicherbelegung des RabbitMQ-Brokers automatisch skaliert. Der Speicher basiert auf einem Faktor des bereitgestellten Arbeitsspeichers im Verhältnis von 1:1. Diese Ressourcen werden als Einheiten gebündelt.

Die automatische Skalierung ist so konzipiert, dass sie als Reaktion auf die kurz-bis mittelfristigen Trends Ihrer Datenbank erfolgt. Ihr Service wird in Minutenintervallen überprüft. Wenn der Arbeitsspeicher für den Service knapp zu werden droht, werden der Bereitstellung weitere Einheiten zugeordnet. 

Bei der automatischen Skalierung wird kein Scale-down für Bereitstellungen durchgeführt, bei denen sich die Platten-/Speicherbelegung verringert hat. Die für Ihre Datenbanken bereitgestellten Ressourcen bleiben für künftige Anforderungen weiterhin bestehen, sofern Sie Ihre Bereitstellung nicht per Scale-down manuell nach unten skalieren.
{: .tip}

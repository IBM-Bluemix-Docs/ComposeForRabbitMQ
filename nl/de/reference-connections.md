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

# Verbindungskonfiguration
{: #connection-configuration}

{{site.data.keyword.composeForRabbitMQ_full}}-Datenbankverbindungen werden von 2 HAProxy-Portalen verwaltet. Jedes Portal verfügt über 64 MB Hauptspeicher.

Durch die zwei Portale bleibt für Anwendungen die Konnektivität auch dann erhalten, wenn eines der Portale nicht mehr erreichbar ist. Eine Funktionsübernahme auf der Clientseite fällt in die Verantwortung der Anwendungsentwickler. Solange Sie nicht mit einem Treiber arbeiten, der die Funktionsübernahmefehler vollständig behandelt, sollten Sie Folgendes beachten:

* Arbeiten Sie mit einem Treiber, der mehrere Endpunkte in seiner Verbindungskonfiguration akzeptiert.
* Stellen Sie sicher, dass Ihre Anwendungsaufrufe zum Schreiben in oder Lesen aus der Datenbank angemessen auf Fehler reagieren. Dies schließt folgende Optionen ein:
  + Protokollierung des Fehlers und Wiederherstellen der Verbindung.
  + Wiederherstellen der Verbindung und Wiederholen der Operation, die den Fehler verursacht hat.
  + Steuerung der Warteschlange der fehlgeschlagenen Operation und Planung der Verbindungswiederholung.

## Verschlüsselung in Transit

Alle HAProxy-Portale von {{site.data.keyword.composeForRabbitMQ}} sind für TLS/SSL aktiviert und unterstützen TLS Version 1.2. Die Zertifikate für den Service sind Zertifikate von Let's Encrypt.

## Grenzwerte für die Anzahl von Verbindungen

Für Datenbankverbindungen gilt ein Grenzwert von 1.000 Verbindungen pro Portal. Das Überschreiten des Verbindungsgrenzwerts hat zur Folge, dass Ihr Service nicht mehr reagiert. Über die Funktion Ihres Treibers für Verbindungspooling können Sie Verbindungen von ihrer Anwendung aus verwalten.

## Proxy-Zeitlimits

Die HAProxy-Portale verwalten den Lebenszyklus von Verbindungen zwischen dem Server und dem Client. Die Zeitlimitwerte sind hier als Orientierung für Treiberautoren und all diejenigen aufgeführt, die sich für die maschinennahe Aktivität von {{site.data.keyword.composeForEtcd}}-Datenbankverbindungen interessieren.

Einstellung | Wert | Gültigkeit
----------|-----------|-----------
client | 3 h | Wenn vom Client erwartet wird, Daten zu bestätigen oder zu senden.
connect | 10 s | Wenn eine Verbindung zwischen dem Proxy und dem Server hergestellt wird.
server | 3 h | Wenn vom Server erwartet wird, Daten zu bestätigen oder zu senden.
check | 5 s | Als sekundäre Zeitlimitüberprüfung, wenn eine Verbindung zwischen dem Proxy und dem Server hergestellt wird.
{: caption="Tabelle 1. HAProxy-Zeitlimitwerte für RabbitMQ" caption-side="top"}





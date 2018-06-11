---

copyright:
  years: 2017,2018
lastupdated: "2018-03-02"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Sicherungen
{: #backups}

Sie können Sicherungen erstellen und über die Registerkarte _Sicherungen_ der Seite _Verwalten_ Ihres Service-Dashboards herunterladen. Dabei sind tägliche, wöchentliche und monatliche Sicherungen sowie Sicherungen nach Bedarf verfügbar. Die Aufbewahrungszeit richtet sich nach folgendem Zeitplan:

Sicherungstyp|Aufbewahrungszeitplan
----------|-----------
Täglich|Tägliche Sicherungen werden 7 Tage aufbewahrt
Wöchentlich|Wöchentliche Sicherungen werden 4 Wochen aufbewahrt
Monatlich|Monatliche Sicherungen werden 3 Monate aufbewahrt
Bei Bedarf|Es wird eine bei Bedarf erstellte Sicherung aufbewahrt. Dabei ist die aufbewahrte Sicherung immer die letzte bei Bedarf erstellte Sicherung.
{: caption="Tabelle 1. Aufbewahrungszeitplan für Sicherungen" caption-side="top"}

Sicherungszeitpläne und Aufbewahrungsrichtlinien sind festgelegt. Falls Sie mehr Sicherungen benötigen als die, die im Aufbewahrungszeitplan vorgesehen sind, sollten Sie Sicherungen herunterladen und Sicherungsarchive gemäß Ihren Geschäftsanforderungen aufbewahren.

## Vorhandene Sicherungen anzeigen

Tägliche Sicherungen Ihrer Datenbank werden automatisch geplant. Navigieren Sie zum Anzeigen Ihrer vorhandenen Sicherungen zu der Seite *Verwalten* Ihres Service-Dashboards. 

  ![Sicherungen](./images/rabbitmq-backups-show.png "Liste der Sicherungen im Service-Dashboard")

Klicken Sie in eine Zeile, um die Optionen für die entsprechende verfügbare Sicherung zu erweitern.

  ![Sicherungsoptionen](./images/rabbitmq-backups-options.png "Optionen für eine Sicherung.") 

### API verwenden, um vorhandene Backups anzuzeigen

Eine Liste der Backups steht am Endpunkt `GET /2016-07/deployments/:id/backups` zur Verfügung. Die Basisendpunkte mit derServiceinstanz-ID und der Bereitstellungs-ID werden beide in der _Übersicht_ des Service angezeigt. Beispiel: 
``` 
https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
```  

## Sicherung bedarfsgerecht erstellen

Neben geplanten Sicherungen können Sie manuelle Sicherungen erstellen. Navigieren Sie zum Erstellen einer manuellen Sicherung zu der Seite *Verwalten* Ihres Service-Dashboards und klicken Sie auf *Jetzt sichern*.

### API verwenden, um eine Sicherung zu erstellen

Senden Sie eine POST-Anforderung an den Endpunkt der Sicherung, um eine manuelle Sicherung zu initialisieren: `POST /2016-07/deployments/:id/backups`. Daraufhin wird sofort die Anleitungs-ID mit Informationen zur aktiven Sicherung zurückgegeben. Sie müssen den Endpunkt der Sicherung überprüfen, um festzustellen, ob die Sicherung fertiggestellt wurde und um die zugehörige Sicherungs-ID (backup_id) zu suchen, bevor Sie sie verwenden können. Verwenden Sie `GET /2016-07/deployments/:id/backups/`.

## Sicherung herunterladen

Navigieren Sie zum Herunterladen einer Sicherung zu der Seite *Verwalten* Ihres Service-Dashboards und klicken Sie in der entsprechenden Zeile mit der Sicherung, die Sie herunterladen wollen, auf *Herunterladen*.

### API verwenden, um eine Sicherung herunterzuladen

Suchen Sie die Sicherung, die Sie wiederherstellen möchten auf der Seite _Sicherungen_ in Ihrem Service aus und kopieren Sie die Sicherungs-ID (backup_id) oder verwenden Sie `GET /2016-07/deployments/:id/backups`, um eine Sicherung und die zugehörige Sicherungs-ID (backup_id) über die Compose-API zu finden. Anschließend verwenden Sie die Sicherungs-ID (backup_id), um Informationen zu suchen und einen Link für eine bestimmte Sicherung herunterzuladen: `GET /2016-07/deployments/:id/backups/:backup_id`.

## Inhalt von Sicherungen

RabbitMQ-Sicherungen sind eine JSON-Darstellung der Metadaten Ihres Brokers. Sie werden mit einem Exportbefehl erstellt, der vom RabbitMQ-Verwaltungs-Plug-in bereitgestellt wird. Die Ausführung des Exportbefehls für Ihren Service beeinträchtigt die Leistung nicht.

## Sicherung mit lokaler Datenbank verwenden

Sie können mit Ihrer {{site.data.keyword.composeForRabbitMQ}}-Sicherung eine lokale Kopie Ihrer Datenbank ausführen.

Es muss eine lokale Instanz von RabbitMQ aktiv sein und die RabbitMQ-Verteilung muss das Verwaltungs-Plug-in enthalten. Aktivieren Sie es mit dem Befehl `rabbitmq-plugins enable rabbitmq_management`. Dadurch erhalten Sie außerdem Folgendes:

* die Administratorbenutzerschnittstelle unter `http://localhost:15672/`
* die HTTP-API unter `http://server-name:15672/api/`
* das Befehlszeilentool `rabbitmqadmin` der API unter `http://localhost:15672/cli/`

Sie haben beim Import der JSON-Sicherungsdatei folgende Optionen:

* Verwenden Sie in der Administratorbenutzerschnittstelle unter http://localhost:15672/ die Funktion _Import-/Exportdefinitionen_ unten auf der Seite _Übersicht_.
* Senden Sie in der API einen POST-Befehl an `http://server-name:15672/api/definitions`. Beispiel:
```http
curl -i -u guest:guest -H "content-type:application/json" -X POST --data @<path_to_your_rabbitmq_backup> http://localhost:15672/api/definitions
```
* Verwenden Sie den Befehl `rabbitmqadmin import <your_rabbitmq_backup>`.

## Sicherung wiederherstellen

Führen Sie zum Wiederherstellen einer Sicherung in einer neuen Serviceinstanz die Schritte zum Anzeigen der vorhandenen Sicherungen aus. Klicken Sie dann in die entsprechende Zeile, um die Optionen für die Sicherung zu erweitern, die Sie herunterladen wollen. Klicken Sie auf die Schaltfläche **Wiederherstellen**. Es wird eine Nachricht darüber angezeigt, dass eine Wiederherstellung eingeleitet wurde. Die neue Serviceinstanz erhält automatisch den Namen "rabbitmq-restore-[timestamp]" und wird beim Start der Bereitstellung in Ihrem Dashboard angezeigt.

### Wiederherstellung über die {{site.data.keyword.cloud_notm}}-CLI

Führen Sie die folgenden Schritte aus, um eine Sicherung aus einem aktiven RabbitMQ-Service mithilfe der {{site.data.keyword.cloud_notm}}-CLI in einem neuen RabbitMQ-Service wiederherzustellen. 
1. Sie müssen die CLI [herunterladen und installieren](https://console.bluemix.net/docs/cli/index.html#overview). 
2. Suchen Sie die Sicherung, die Sie wiederherstellen möchten auf der Seite _Sicherungen_ in Ihrem Service aus und kopieren Sie die Sicherungs-ID.   
  **ODER**  
  Suchen Sie eine Sicherung und die zugehörige ID über die Compose-API mithilfe von `GET /2016-07/deployments/:id/backups`. Der Basisendpunkt und die Serviceinstanz-ID werden beide in der _Übersicht_ des Service angezeigt. Beispiel: 
  ``` 
  https://composebroker-dashboard-public.mybluemix.net/api/2016-07/instances/$INSTANCE_ID/deployments/$DEPLOYMENT_ID/backups
  ```  
  Die Antwort enthält eine Liste aller verfügbarer Sicherungen für diese Serviceinstanz. Wählen Sie die Sicherung aus, die Sie wiederherstellen möchten, und kopieren Sie die zugehörige ID. 

3. Melden Sie sich mit dem entsprechenden Konto und den zugehörigen Berechtigungsnachweisen an. `bx login` (oder `bx login -help` zur Anzeige aller Anmeldeoptionen).

4. Wechseln Sie zu Ihrer Organisation und Ihrem Bereich `bx target -o "$YOUR_ORG" -s "YOUR_SPACE"`

5. Verwenden Sie den Befehl `service create`, um einen neuen Bereich bereitzustellen und stellen Sie den Quellenservice und die spezifische Sicherung zur Verfügung, die Sie in einem JSON-Objekt wiederherstellen. Beispiel:
``` 
bx service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": "$BACKUP_ID" }'
```
  Für das Feld _SERVICE_ sollte 'compose-for-rabbitmq' und für das Feld _PLAN_ sollte abhängig von Ihrer Umgebung entweder 'Standard' oder 'Enterprise' angegeben sein. In _SERVICE\_INSTANCE\_NAME_ geben Sie den Namen für Ihren neuen Service an. _source\_service\_instance\_id_ ist die Serviceinstanz-ID der Quelle der Sicherung. Diese kann durch die Ausführung von `bx cf service DISPLAY_NAME --guid` abgerufen werden, wobei _DISPLAY\_NAME_ der Name des RabbitMQ-Service ist, von dem die Sicherung stammt.  
  
  
### Migration auf eine neue Version

Einige Upgrades der Hauptversion sind in der aktuell ausgeführten Bereitstellung nicht verfügbar. Sie müssen einen neuen Service bereitstellen, der die aktualisierte Version ausführt und anschließend Ihre Daten mithilfe einer Sicherung in diese neue Version migrieren. Dieser Prozess entspricht der Wiederherstellung einer Sicherung wie oben beschrieben. Der einzige Unterschied besteht darin, dass Sie die Version angeben, auf die Sie ein Upgrade durchführen möchten. 

``` 
bx service create SERVICE PLAN SERVICE_INSTANCE_NAME -c '{"source_service_instance_id": "$SERVICE_INSTANCE_ID", "backup_id": ""$BACKUP_ID", "db_version":"$VERSION_NUMBER" }'
```

Die Wiederherstellung einer älteren Version eines {{site.data.keyword.composeForRabbitMQ}}-Service auf einem neuen Service unter Ausführung von RabbitMQ 3.6.14 sieht wie folgt aus: 
```
bx service create compose-for-rabbitmq Standard migrated_rabbit -c '{ "source_service_instance_id": "0269e284-dcac-4618-89a7-f79e3f1cea6a", "backup_id":"5a96d8a7e16c090018884566", "db_version":"3.6.14"  }'


---

copyright:
  years: 2017
lastupdated: "2017-06-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Externe Anwendung verbinden
{: #connecting-external-app}

Sie finden die Informationen, die Sie zum Herstellen einer Verbindung zu {{site.data.keyword.composeForRabbitMQ_full}} benötigen, auf der Seite *Übersicht* Ihres {{site.data.keyword.composeForRabbitMQ}}-Service. 

Die hier aufgeführten Beispiele gelten für Node, Java, Ruby, Python und Go. Sie sollten zuerst das Beispiel [Java und RabbitMQ](#java-and-rabbitmq) lesen, weil darin beschrieben wird, welche Konzepte es gibt, wie Sie eine Verbindung herstellen und überprüfen, ob Ihr Code wie erwartet funktioniert und wie Sie überprüfen, ob Sie mit dem richtigen Host verbunden sind.

Den vollständigen Code des nachstehenden und des darauffolgenden Beispiels finden Sie unter [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns).</p></div>

Bevor Sie anfangen, sollten Sie auch einen Blick auf die [RabbitMQ-Lernprogramme](http://www.rabbitmq.com/getstarted.html) werfen.

## Node und RabbitMQ

### Client installieren

Erstellen Sie Ihr Projekt und installieren Sie dann [amqplib](https://www.npmjs.com/package/amqplib) mit `npm install amqplib --save`. Nach dieser Installation können Sie an die Erstellung des Codes gehen. Das Paket 'amqplib' hat zwei APIs: den traditionelleren Callback-Stil und einen "when"-basierten Promise-Stil. Im vorliegenden Beispiel wird die Callback-API verwendet.

### Verbindung erstellen

Zuerst wird der Code komplett gezeigt, danach unterteilt und erläutert:

```javascript
#!/usr/bin/env node

var amqp = require('amqplib/callback_api');
var url = require('url');

function bail(err, conn) {
    console.error(err);
    if (conn) conn.close(function() {
        process.exit(1);
    });
}

rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);

    conn.createChannel(function(err, channel) {

        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";

        channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });

    	});

    setTimeout(function() { conn.close(); process.exit(0) }, 500);

});

```

Definieren Sie zuerst die erforderlichen `require`-Funktionen, die in der Bibliothek aufzurufen sind. Beachten Sie, dass dies das Paket `URL` einschließt. Sie sollten auch eine Funktion namens `bail()` in demselben Stil wie in den RabbitMQ-Beispielen erstellen.

```text
#!/usr/bin/env node

var amqp = require('amqplib/callback_api');
var url = require('url');

function bail(err, conn) {
    console.error(err);
    if (conn) conn.close(function() {
        process.exit(1);
    });
}
```

Nun können Sie den Verbindungsprozess starten:

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

Definieren Sie zuerst eine Variable mit der Verbindungszeichenfolge-URL aus der Übersicht der Compose-Konsole. Derzeit sendet die Bibliothek 'amqp' keinen Servernamen, damit die TLS/SSL-SNI-Unterstützung funktioniert, Sie können jedoch die URL in ihre einzelnen Komponenten parsen und `{ servername: parsedurl.hostname }` zu den `amqp.connect`-Optionen hinzufügen, um diese Eigenschaft in die Verbindung einzubinden. Wenn die Verbindung hergestellt wurde, wird die Callback-Funktion aufgerufen und führt eine erste Fehlerprüfung durch.

Mithilfe der Verbindung kann das Programm nun eine Nachricht an einen Austausch veröffentlichen. Zuerst erstellt es einen Kanal für diese Veröffentlichung. Der Code fährt in einer Callback-Funktion fort:

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

Der Code macht eine Fehlerprüfung. Ist diese erfolgreich, erstellt er Variablen, die die Nachricht, den Routing-Schlüssel und den Austauschnamen darstellen, an den gesendet werden soll. Mit `exchangeName` wird sichergestellt, dass der benannte Austausch vorhanden ist. Die Funktion `assertExchange` wird mit dem Namen, dem Typ, Optionen und einer Callback-Funktion aufgerufen. Sofern der Austausch vorhanden ist oder erstellt werden kann, wird der Code fortgesetzt:

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

Die Funktion `publish` übergibt den Austauschnamen und den Routing-Schlüssel und schließt die Nachricht in einen Puffer ein. Die Nachricht wird gesendet und der Code wird beendet:

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

Da der Code asynchron ist, legt er ein Zeitlimit fest, um die Verbindung zu schließen und die Knotenlaufzeit zu verlassen.

Zur Vollständigkeit wird hier das "when"-Promise-Beispiel aufgeführt:

```javascript
#!/usr/bin/env node

var amqp = require('amqplib');
var when = require('when');
var url = require('url');

rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }).then(function(conn) {
    return when(conn.createChannel().then(function(channel) {
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
        var ok=channel.assertExchange(exchangeName, "direct", { durable: true });
        return ok.then(function(_qok) {
            channel.publish(exchangeName, routingKey, new Buffer(message));
            return;
        });
    })).ensure(function() { conn.close(); });
}).catch(console.warn);
```

Der Ablauf ist quasi identisch, aber die Promise-Objekte stellen sicher, dass die Ereignisse in einer einfacher zu verwaltenden Reihenfolge ablaufen. 

Wenn Sie eine dieser beiden Varianten ausführen, springen Sie weiter zum Abschnitt [Beispielverbindung überprüfen](#section-verifying-the-example-connection), um sicherzustellen, dass das Verhalten den Erwartungen entspricht.

## Java und RabbitMQ

### Client installieren

Installieren Sie den offiziellen [RabbitMQ-Java-Client](http://www.rabbitmq.com/java-client.html). Wählen Sie die Option aus, die für Ihre Entwicklungsumgebung passt. 

### Verbindung erstellen

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

Dies ist nur ein Beispiel, das zeigt, dass der Code alles in der Hauptmethode macht. Zuerst ruft er eine Verbindungsfactory (ConnectionFactory) für RabbitMQ-Verbindungen ab. Dann wird der URI für die Bereitstellung an die Factory gesendet, damit er Verbindungen herstellt, die sich mit RabbitMQ verbinden. Beachten Sie den Eintrag 'amqps://' im URI. 

Dann kann der Code die Factory nach einer neuen Verbindung fragen. Mithilfe dieser Verbindung kann das Programm nun eine Nachricht an einen Austausch veröffentlichen. Zuerst erstellt es einen Kanal für diese Veröffentlichung: 

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

Danach richtet es Nachrichtennutzdaten ein, in diesem Fall eine Zeichenfolge, einen Routing-Schlüssel für seine Weiterreise und den Namen eines Austausches, an den es gesendet werden soll.

Mit den neu festgelegten Werten kann es den Austausch deklarieren (einen direkten Austausch, der den Routing-Schlüssel verwenden kann), welcher erstellt wird, wenn er noch nicht vorhanden ist. Dann kann es an den benannten Austausch veröffentlichen, wobei ein Routing-Schlüssel und die Nachrichtennutzdaten als Byte verschlüsselt werden:

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

Nun muss der Code nur noch den Kanal schließen, die Verbindung schließen und einen Abfangbefehl für alle Ausnahmebedingungen einbinden, die möglicherweise ausgelöst werden:

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## Beispielverbindung überprüfen

Wenn Sie die hier aufgeführten Beispiele ausführen, stellt der Code unbeaufsichtigt eine Verbindung her, übergibt die Nachricht und trennt die Verbindung. Um zu überprüfen, ob er etwas gemacht hat, müssen Sie sich an der RabbitMQ-Administratorbenutzerschnittstelle anmelden – die URL wird in der Compose-Konsole unter den Verbindungszeichenfolgen angezeigt – und die Registerkarte 'Austauschvorgänge' auswählen. Dort sollte ein Austausch namens "postal" angezeigt werden, den der Code erstellt hat. Außerdem sollte in den Diagrammen eine gewisse Aktivität angezeigt werden. 

Um zu überprüfen, ob die Nachricht angekommen ist, und weil es nicht möglich ist, in den Austausch zu sehen, müssen Sie eine Warteschlange erstellen, um die Nachricht zu lesen.

+ Wechseln Sie auf die Registerkarte 'Warteschlangen'.
![Registerkarte 'Warteschlangen' in der Administratorbenutzerschnittstelle.](./images/queues_tab.png)
+ Fügen Sie eine Warteschlange mit einem Namen hinzu, beispielsweise *fred*. 
+ Kehren Sie auf die Registerkarte 'Austauschvorgänge' zurück. 
+ Wählen Sie den Austausch *postal* aus.
![Registerkarte 'Austauschvorgänge' in der Administratorbenutzerschnittstelle.](./images/exchanges_tab.png)
* Wählen Sie **Bindungen** aus. 
* Wählen Sie im Bereich _Bindung aus diesem Austausch hinzufügen_ die Option "zur Warteschlange" aus und geben Sie *fred* als Warteschlangenname ein.
* Geben Sie "tributes" als Routing-Schlüssel ein.
![Bindung auf Registerkarte 'Austauschvorgänge' hinzufügen.](./images/add_binding.png)
* Klicken Sie auf **Binden**.
* Führen Sie den Beispielcode aus, um eine Nachricht zu senden.
* Wechseln Sie in der Administratorbenutzerschnittstelle auf die Registerkarte 'Warteschlangen'. 
* Wählen Sie die Warteschlange *fred* aus.
![Übersicht über eine Warteschlange.](./images/queue_overview.png)
* Öffnen Sie die Anzeige 'Nachrichten abrufen'.
![Dialog 'Nachrichten abrufen'.](./images/get_message_dialog.png)
* Klicken Sie auf **Nachricht abrufen**, um die Nachricht abzurufen.
![Ergebnisse des Nachrichtenabrufs.](./images/get_message_results.png)

Alle Nachrichten, die gesendet werden, bevor eine Warteschlange an den Austausch gebunden wurde, werden automatisch verworfen, weil sie nicht weitergeleitet werden können. RabbitMQ besitzt einen Mechanismus namens [Alternate Exchanges](https://www.rabbitmq.com/ae.html), mit dem Nachrichten, die nicht weitergeleitet werden können, in bestimmten Fällen abgefangen werden, doch generell ist es am besten, sicherzustellen, dass alles in der Nachrichtenarchitektur weitergeleitet werden kann.

In diesem Fall verbleibt die Nachricht in der Warteschlange, obwohl das Abrufen einer Nachricht ein zerstörerischer Akt ist. Das liegt daran, dass die Standardeinstellung in der Anzeige _Nachrichten abrufen_ besagt, dass Nachrichten nach dem Abrufen erneut in die Warteschlange gestellt werden.

## Ruby und RabbitMQ

Es gibt eine Reihe von Treibern für die Sprache Ruby. [Bunny](http://rubybunny.info/) ist einer der bekanntesten davon - auf der Website von [Bunny](http://rubybunny.info/) finden Sie vollständige Lernprogramm und die Dokumentation. Derzeit ist Bunny 2.7.0 noch nicht freigegeben. Dieses Release enthält ein Patch für die Verwendung von SNI beim Herstellen der TLS-Verbindung. Sie können dies mithilfe der Befehle `gem install specific_install ` und danach `gem specific_install https://github.com/ruby-amqp/bunny` selbst erstellen.

Verwenden Sie den folgenden Code, um eine Verbindung zu Compose RabbitMQ herzustellen und dasselbe wie im obigen Beispiel zu tun:
```ruby
require 'bunny'

conn = Bunny.new('amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity')
conn.start

ch = conn.create_channel

message = 'This is not a message, this is a ruby tribute to a message'
routingKey = 'tributes'
exchangeName = 'postal'

x = ch.direct(exchangeName, durable: true)

x.publish(message, routing_key: routingKey)

ch.close
conn.close
```
Bei der Ausführung gibt der Code eine Warnung aus, die der folgenden ähnelt:
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
Der Server ist nicht für die Überprüfung von Clients konfiguriert (und Compose bietet derzeit keine Überprüfung von Clientzertifikaten an). Trotz dieser Nachricht gelingt das Verbindungsupgrade und wird mit einem vertrauenswürdigen Zertifikat von Let's Encrypt überprüft.
## Python und RabbitMQ

Dieser Code verwendet die Bibliothek [pika](http://pika.readthedocs.org/en/0.10.0/index.html), wie von den RabbitMQ-Entwicklern empfohlen.
```python
#!/usr/bin/env python
import pika
import sys
import ssl

parameters = pika.URLParameters('amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity')

connection = pika.BlockingConnection(parameters)
channel = connection.channel()

message='This is not a message, this is a pythonic tribute to a message'
my_routing_key='tributes'
exchange_name='postal'

channel.exchange_declare(exchange=exchange_name,
                         type='direct',
                         durable=True)

channel.basic_publish(exchange=exchange_name,
                      routing_key=my_routing_key,
                      body=message)

channel.close()
connection.close()

```
Der Code bindet zuerst die benötigten Bibliotheken ein. Dann erstellt er die Parameter, die zum Erstellen einer Verbindung erforderlich sind - insbesondere die RabbitMQ-URL.
Der Code folgt dem Modell der obigen Beispiele und verwendet sie dann, um eine Verbindung herzustellen und mit einem Routing-Schlüssel eine Nachricht an den Austausch `postal` zu übertragen.

## Go und RabbitMQ

Für Go wird das Paket [github.com/streadway/amqp](https://github.com/streadway/amqp) empfohlen. 

Im vorliegenden Go-Beispiel erstellt der Code nur eine mit dem Server überprüfte Verbindung. 
```go
package main

import (
	"fmt"
  "log"

	"github.com/streadway/amqp"
)

func failOnError(err error, msg string) {
	if err != nil {
		log.Fatalf("%s: %s", msg, err)
		panic(fmt.Sprintf("%s: %s", msg, err))
	}
}

func main() {

	conn, err := amqp.Dial("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity")
  failOnError(err, "Failed to connect to RabbitMQ")
	defer conn.Close()
```

Beachten Sie, dass die Funktion `failonError` die Go-Fehlerbehandlung verkürzt.

Die Hauptmethode beginnt mit dem Erstellen der Verbindung. Das RabbitMQ-Kennwort wird an die Funktion `Dial` übergeben. Es gibt auch eine Funktion namens `DialTLS`. Die Verwendung von `amqps` in der URL reicht jedoch aus, um TLS-Verbindungen zu aktivieren.

Mit `defer` wird sichergestellt, dass die Verbindung beim Beenden getrennt wird.

Der Rest des Go-Codes dient wie in den obigen Beispielen dazu, einen Kanal zu öffnen, den Austausch zu erstellen und eine Nachricht zu senden.

```go
	message := "This is not a message, this is a go tribute to a message"
	routingKey := "tributes"
	exchangeName := "postal"

	ch, err := conn.Channel()
	failOnError(err, "Failed to open a channel")
	defer ch.Close()

	err = ch.ExchangeDeclare(
		exchangeName, // name
		"direct",     // type
		true,         // durable
		false,        // auto-deleted
		false,        // internal
		false,        // no-wait
		nil,          // arguments
	)
	failOnError(err, "Failed to declare an exchange")

	err = ch.Publish(
		exchangeName, // exchange
		routingKey,   // routing key
		false,        // mandatory
		false,        // immediate
		amqp.Publishing{
			ContentType: "text/plain",
			Body:        []byte(message),
		})
	failOnError(err, "Failed to publish a message")
}
```

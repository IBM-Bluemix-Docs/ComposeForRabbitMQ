---

copyright:
  years: 2017,2018
lastupdated: "2017-06-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Connessione a un'applicazione esterna
{: #connecting-external-app}

Puoi trovare le informazioni di cui hai bisogno per il collegamento a {{site.data.keyword.composeForRabbitMQ_full}} nella pagina *Overview* del tuo servizio {{site.data.keyword.composeForRabbitMQ}}.

Gli esempi qui presenti coprono Node, Java, Ruby, Python e Go. Dovresti iniziare leggendo l'esempio [Java and RabbitMQ](#java-and-rabbitmq), perché ne copre i concetti, come collegarti e verificare che il tuo codice stia funzionando e come verificare che ti stai collegando all'host corretto.

Puoi trovare il codice completo per questo e i seguenti esempi all'indirizzo [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns).</p></div>

Prima di iniziare, potresti anche volere fare riferimento a [RabbitMQ tutorials](http://www.rabbitmq.com/getstarted.html).

## Node e RabbitMQ

### Installazione del client

Crea il tuo progetto e poi installa [amqplib](https://www.npmjs.com/package/amqplib) con `npm install amqplib --save` . Dopo l'installazione puoi iniziare a creare il codice. Il pacchetto amqplib ha due API; un callback di stile più tradizionale e uno stile promesso basato su "when". L'esempio utilizza l'API di callback.

### Creazione della connessione

Viene prima mostrato il codice completo, poi viene spezzato e spiegato:

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

Innanzitutto, definisci le funzioni `require` necessarie da richiamare nella libreria. Tieni presente che includono il pacchetto `URL`. Dovresti anche creare una funzione `bail()` nello stesso stile degli esempi RabbitMQ.

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

Ora puoi avviare il processo di connessione:

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

Inizia definendo una variabile con l'URI della stringa di connessione dalla panoramica della console Compose. Al momento la libreria amqp non invia un nome del server per il supporto TLS/SSL alla funzione, ma puoi analizzare l'URL nelle sue parti di componente e aggiungere `{ servername: parsedurl.hostname }` alle opzioni `amqp.connect` per inserire tale proprietà nella connessione. Quando la connessione è completa, la funzione di callback viene richiamata ed esegue un controllo errori iniziale.

Utilizzando la connessione, il programma ora può pubblicare una messaggio semplice per uno scambio. Innanzitutto, crea un canale per questa pubblicazione. Il codice continua in una funzione di callback:

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

Il codice esegue un controllo alla ricerca di errori. Se con esisto positivo, crea le variabili che rappresentano il messaggio, la chiave di instradamento e il nome dello scambio a cui eseguire l'invio. `exchangeName` viene utilizzato per controllare che lo scambio denominato esista. La funzione `assertExchange` viene denominata con il nome, il tipo, le opzioni e una funzione di callback. Fornendo lo scambio esistente o creandolo il codice passa a:

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

La funzione `publish` passa la chiave di instradamento e il nome dello scambio e include il messaggio in un buffer. Il messaggio viene inviato a il codice esce:

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

Essendo asincrono, il codice imposta un timeout per chiudere la connessione e uscire dal runtime del nodo.

Per completezza, questo è l'esempio promesso "when":

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

Il flusso è praticamente lo stesso, ma le promesse garantiscono che si verifichino le cose in un ordine più gestibile. 

Se esegui entrambi, ignora [Verifica della connessione di esempio](#section-verifying-the-example-connection) per confermare che è ciò che ci aspettiamo.

## Java e RabbitMQ

### Installazione del client

Installa la versione ufficiale di [RabbitMQ Java Client](http://www.rabbitmq.com/java-client.html). Seleziona l'opzione più adatta al tuo ambiente di sviluppo. 

### Creazione di una connessione.

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

Questo è soltanto un esempio, per cui il codice sta facendo tutto nel metodo principale. Inizia richiamando un ConnectionFactory per le connessioni RabbitMQ. L'URI per la distribuzione viene inviato al factory in modo che crei le connessioni per il collegamento a RabbitMQ; nota amqps:// nell'URI. 

Il codice può quindi richiedere un factory per una nuova connessione. Utilizzando tale connessione, il programma ora può pubblicare una messaggio semplice per uno scambio. Innanzitutto, crea un canale per questa pubblicazione:

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

Dopo configura un payload del messaggio, in questo caso una stringa, una chiave di instradamento per il proseguimento e il nome di uno scambio a cui eseguire l'invio.

Con i nuovi valori configurati, dichiara lo scambio (uno scambio diretto che può utilizzare la chiave di instradamento), che sarà creato se non esiste. Puoi quindi pubblicare lo scambio denominato, con una chiave di instradamento e il payload del messaggio codificato in byte:

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

Tutto ciò che deve fare il codice ora è di chiudere il canale e la connessione e acquisire tutte le eccezioni che potrebbero venire riscontrate:

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## Verifica della connessione di esempio

Quando esegui gli esempi qui. Il codice si collega in modalità non presidiata, consegna i messaggi e si scollega. Per verificare se ha fatto qualcosa, accedi alla IU di gestione RabbitMQ –  l'URL viene visualizzato sotto le stringhe di connessione nella console Compose - e seleziona la scheda degli scambi. Dovrebbe esserci un scambio "postal", creato dal codice. Dovrebbe anche esserci dell'attività visualizzata nei grafici. 

Per confermare che il messaggio è arrivato e perché non è possibile ricercare nello scambio, crea una coda per utilizzare il messaggio.

+ Vai alla scheda delle code
![La scheda delle code nella IU di gestione.](./images/queues_tab.png)
+ Aggiungi una coda con un nome, ad esempio *fred* 
+ Torna alla scheda degli scambi 
+ Seleziona lo scambio *postal*
![La scheda degli scambi nella IU di gestione.](./images/exchanges_tab.png)
* Seleziona **Bindings**
* In _Add binding from this exchange_ seleziona "to queue" e immetti *fred* per il nome della coda
* Immetti "tributes" come chiave di instradamento
![Aggiunta di un bind nella scheda degli scambi.](./images/add_binding.png)
* Fai clic su **Bind**
* Esegui il codice di esempio per inviare un messaggio
* Nella IU di gestione, passa alla scheda delle code 
* Seleziona la coda *fred*
![Panoramica di una coda.](./images/queue_overview.png)
* Apri il pannello Get Messages
![Finestra di dialogo Get messages.](./images/get_message_dialog.png)
* Fai clic su **Get Message** per visualizzare il messaggio
![Risultati di Get Message.](./images/get_message_results.png)

Tutti i messaggi inviati prima dell'associazione di una coda allo scambio vengono scartati automaticamente e non possono venire instradati. RabbitMQ ha un meccanismo per acquisire i messaggi non instradabili denominato [Alternate Exchanges](https://www.rabbitmq.com/ae.html) per i casi speciali, ma normalmente è meglio assicurarsi che sia tutto instradato nell'architettura di messaggistica.

In questo caso, il messaggio rimane nella coda anche se ricevere un messaggio è un atto distruttivo. Questo perché, nel pannello _Get Messages_, l'impostazione predefinita è di acquisire nuovamente nella coda i messaggi dopo che sono stati recuperati.

## Ruby e RabbitMQ

Esistono molti driver per il linguaggio Ruby. [Bunny](http://rubybunny.info/) è uno dei più famosi - puoi trovare tutte le esercitazioni e la documentazione nel sito web [Bunny](http://rubybunny.info/). Nel momento in cui scriviamo, Bunny 2.7.0 non è ancora stato rilasciato; che contiene una patch per utilizzare SNI quando si effettua la connessione TLS. Puoi farlo per conto tuo utilizzando `gem install specific_install ` e `gem specific_install https://github.com/ruby-amqp/bunny`.

Per collegarti a Compose RabbitMQ e fare le stesse cose del precedente esempio, utilizza il seguente codice:
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
Quando eseguito, il codice emette un'avvertenza simile a quanto segue:
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
Il server non è configurato per verificare i client (e Compose al momento non offre la verifica del certificato client); nonostante questo messaggio abbia esito positivo e possa essere verificato con un certificato Lets Encrypt affidabile.

## Python e RabbitMQ

Questo codice utilizza la libreria [pika](http://pika.readthedocs.org/en/0.10.0/index.html), come consigliato dagli sviluppatori RabbitMQ.
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
Il codice per prima cosa inserisce le librerie di cui ha bisogno. Successivamente crea i parametri necessari per creare una connessione - nello specifico l'URL RabbitMQ.

Seguendo il modello degli esempi precedente, lo utilizza per il collegamento e inserisce un messaggio nello scambio `postal` con una chiave di instradamento.

## Go e RabbitMQ

Per Go consigliamo il pacchetto [github.com/streadway/amqp](https://github.com/streadway/amqp). 

In questo esempio Go, il codice esegue soltanto una connessione verificata al server. 
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

Tieni presente che la funzione `failonError` riduce la gestione dell'errore di Go.

Il metodo principale avvia la creazione della connessione. La password RabbitMQ viene passata alla funzione `Dial`. Una funzione `DialTLS` già esiste, ma l'utilizzo di `amqps` nell'URL è sufficiente per attivare le connessioni TLS.

Utilizzando `defer` garantisci che la connessione venga chiusa all'uscita.

Il resto del codice Go, come negli esempi precedenti, apre un canale, crea lo scambio e invia un messaggio.

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

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

# Connexion d'une application externe
{: #connecting-external-app}

Vous trouverez les informations nécessaire pour vous connecter à {{site.data.keyword.composeForRabbitMQ_full}} sur la page *Vue d'ensemble* du service {{site.data.keyword.composeForRabbitMQ}}.

Les exemples donnés ici couvrent Node, Java, Ruby, Python et Go. Vous devez commencer par lire l'exemple [Java et RabbitMQ](#java-and-rabbitmq) qui couvre les concepts, montre comment se connecter et vérifier que votre code fonctionne comme prévu et comment vérifier que vous vous connectez à l'hôte approprié.

L'intégralité du code pour cet exemple et les suivants se trouve à l'adresse [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns).</p></div>

Avant de commencer, vous voudrez sans doute également consulter les [tutoriels RabbitMQ](http://www.rabbitmq.com/getstarted.html).

## Node et RabbitMQ

### Installation du client

Créez votre projet puis installez [amqplib](https://www.npmjs.com/package/amqplib) avec `npm install amqplib --save`. Une fois cette installation effectuée, vous pouvez commencer à créer le code. Le package amqplib a deux API ; le style le plus traditionnel de rappel et le style engagements basé sur "when". L'exemple donné ici utilise l'API de rappel.

### Création de la connexion

Le code complet est présenté en premier, puis par fractions et expliqué :

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

Définissez en premier les fonctions `require` nécessaires pour appeler la bibliothèque. Cette opération inclut le package `URL`. Vous devez également créer une fonction `bail()` dans le même style que dans les exemples RabbitMQ.

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

Maintenant, vous pouvez démarrer le processus de connexion :

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

Commencez par définir une variable avec l'URL de la chaîne de connexion issue de la page Vue d'ensemble de la console Compose. Actuellement, la bibliothèque amqp n'envoie pas de nom de serveur pour permettre au support SNI TLS/SSL de fonctionner, mais vous pouvez analyser l'URL dans ses parties de composant et ajouter `{ servername: parsedurl.hostname }` aux options `amqp.connect` afin d'injecter cette propriété dans la connexion. Une fois la connexion établie, la fonction de rappel est appelée et effectue la vérification initiale des erreurs.

A l'aide de la connexion, le programme peut maintenant publier un message simple dans un échange. Tout d'abord, il définit un canal pour cette publication. Le code continue avec une fonction de rappel :

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

Le code recherche les erreurs. En cas de réussite, il crée des variables qui représentent le message, la clé de routage et le nom de l'échange via lequel effectuer l'envoi. `exchangeName` est utilisé pour garantir que l'échange nommé existe. La fonction `assertExchange` est appelée avec le nom, le type, les options et une fonction de rappel. Sous réserve que l'échange existe ou peut être créé, le code se poursuit :

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

La fonction `publish` transmet le nom d'échange et la clé de routage, puis encapsule le message dans un buffer. Le message est envoyé et le code existe :

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

Etant asynchrone, le code définit un délai d'attente avant fermeture de la connexion et sortie de l'exécution du noeud.

Pour terminer, voici l'exemple engagement "when" :

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

Le flux est sensiblement identique, mais l'engagement garantit que les actions se produisent dans un ordre plus administrable. 

Si vous rencontrez l'une de ces cas, passez directement à [Vérification de l'exemple de connexion](#section-verifying-the-example-connection) afin de confirmer que le code exécute ce qui est attendu.

## Java et RabbitMQ

### Installation du client

Installez le [client Java RabbitMQ](http://www.rabbitmq.com/java-client.html) officiel. Sélectionnez l'option la mieux adaptée à votre environnement de développement. 

### Création d'une connexion

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

Il s'agit juste d'un exemple, le code fait donc tout le travail dans la méthode principale. Il commence par obtenir une fabrique de connexions pour les connexions RabbitMQ. L'URI du déploiement est ensuite envoyé à la fabrique de manière à définir des connexions à RabbitMQ ; notez l'élément amqps:// dans l'URI. 

Le code peut ensuite demander une nouvelle connexion à la fabrique. A l'aide de cette connexion, le programme peut maintenant publier un message simple dans un échange. Tout d'abord, il définit un canal pour cette publication :

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

Ensuite, il définit une charge de message, dans le cas présent une chaîne, une clé de routage pour la poursuite de son voyage et le nom d'un échange via lequel envoyer le message.

Avec les nouvelles valeurs définies, il déclare l'échange (un échange direct qui peut utiliser la clé de routage), qui sera créé s'il n'existe pas. Il peut ensuite publier sur l'échange nommé, avec une clé de routage et la charge de message codée en octets :

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

Tout ce que le code doit faire maintenant est de fermer le canal, fermer la connexion et intercepter toutes les exceptions qui peuvent être émises :

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## Vérification de l'exemple de connexion

Lorsque vous exécutez les exemples donnés ici, le code se connecte, distribue le message et se déconnecte en mode silencieux. Pour vérifier qu'il effectue toutes les opérations, connectez-vous à l'interface utilisateur d'administration RabbitMQ (l'URL est affichée sous les chaînes de connexion dans la console Compose) et sélectionnez l'onglet Exchanges. Il doit contenir un échange "postal" que le code a créé. Il doit également contenir quelque activité affichée dans les graphiques. 

Pour confirmer que le message est arrivé, et étant donné qu'il est impossible de regarder dans l'échange, créez une file d'attente pour consommer le message.

+ Accédez à l'onglet Queues
![Onglet Queues de l'interface utilisateur d’administration.](./images/queues_tab.png)
+ Ajoutez une file d'attente et un nom, par exemple *fred* 
+ Revenez dans l'onglet Exchanges 
+ Sélectionnez l'échange *postal*
![Onglet Exchanges de l'interface utilisateur d’administration.](./images/exchanges_tab.png)
* Sélectionnez **Bindings**
* Dans _Add binding from this exchange_ sélectionnez "to queue" et entrez *fred* pour le nom de la file d'attente
* Entrez "tributes" comme clé de routage
![Ajout d'une liaison dans l'onglet Exchanges.](./images/add_binding.png)
* Cliquez sur **Bind**
* Exécutez l'exemple de code pour envoyer un message
* Dans l'interface utilisateur d’administration, accédez à l'onglet Queues 
* Sélectionnez la file d'attente *fred* queue
![Vue d'ensemble d'une file d'attente.](./images/queue_overview.png)
* Ouvrez le panneau Get Messages panel
![Boîte de dialogue Get messages.](./images/get_message_dialog.png)
* Cliquez sur **Get Message** pour afficher le message
![Résultats de Get Message.](./images/get_message_results.png)

Tous les messages envoyés avant la liaison de la file d'attente à l'échange sont automatiquement annulés car ils ne peuvent pas être acheminés. RabbitMQ dispose d'un mécanisme permettant d'intercepter les messages, appelé [mécanisme d’échanges alternatifs](https://www.rabbitmq.com/ae.html), pour des cas spéciaux, mais il est généralement préférable de s'assurer que tous les messages sont acheminés dans l'architecture de messagerie.

Dans le cas présent, le message reste dans la file d'attente même si l'obtention d'un message est une action de destruction. Ceci est dû au fait que, dans le panneau _Get Messages_, le paramétrage par défaut demande la remise en file d'attente des messages après leur extraction.

## Ruby et RabbitMQ

Il existe de nombreux pilotes pour le langage Ruby. [Bunny](http://rubybunny.info/) est l'un des plus connus (voir le site Web [Bunny](http://rubybunny.info/) pour les tutoriels et la documentation le concernant). Au moment de la rédaction du présent document, Bunny 2.7.0 n'a pas encore été publié ; il contient un correctif permettant d'utiliser SNI lors de la définition de la connexion TLS. Vous pouvez le générer pour vous à l'aide de `gem install specific_install `, puis de `gem specific_install https://github.com/ruby-amqp/bunny`.

Pour vous connecter à Compose RabbitMQ et effectuer les mêmes opérations que dans l'exemple ci-dessus, utilisez le code suivant :
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
Lorsqu'il est exécuté, le code génère un avertissement du type :
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
Le serveur n'est pas configuré pour vérifier les clients (et Compose ne propose pas actuellement de vérification des certificats client), mais malgré ce message, la mise à niveau de la connexion réussit et elle est vérifiée avec un certificat Lets Encrypt digne de confiance.
## Python et RabbitMQ

Ce code utilise la bibliothèque [pika](http://pika.readthedocs.org/en/0.10.0/index.html), selon recommandation des développeurs RabbitMQ.
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
Le code commence par extraire les bibliothèques nécessaires. Puis il crée les paramètres requis pour créer une connexion, en particulier, l'URL RabbitMQ.

Suivant le modèle des exemples précédents, il utilise ensuite ces éléments pour se connecter et envoyer un message à l'échange `postal` avec une clé de routage.

## Go et RabbitMQ

Pour Go, nous recommandons le package [github.com/streadway/amqp](https://github.com/streadway/amqp). 

Dans cet exemple Go, le code établit uniquement une connexion vérifiée au serveur. 
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

Notez que la fonction `failonError` réduit le traitement d'erreurs Go.

La méthode principale commence par créer la connexion. Le mot de passe RabbitMQ est transmis à la fonction `Dial`. Il existe également une fonction `DialTLS`, mais l'utilisation de `amqps` dans l'URL suffit pour activer des connexions TLS.

L'utilisation de `defer` garantit la fermeture de la connexion lors de la sortie.

Le reste du code Go, comme dans les exemples précédents, ouvre un canal, crée l'échange et envoie un message.

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

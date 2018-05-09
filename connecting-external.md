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

# Connecting an external application
{: #connecting-external-app}

You can find the information you need to connect to {{site.data.keyword.composeForRabbitMQ_full}} on the *Overview* page of your {{site.data.keyword.composeForRabbitMQ}} service.

The examples here cover Node, Java, Ruby, Python and Go. You should begin by reading through the [Java and RabbitMQ](#java-and-rabbitmq) example, because this covers the concepts, how to connect and verify that your code is working as expected, and how to check you are connecting to the right host.

You can find the full code for this and subsequent examples at [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns).</p></div>

Before you begin, you might also want to refer to the [RabbitMQ tutorials](http://www.rabbitmq.com/getstarted.html).

## Node and RabbitMQ

### Installing the client
{: #installing-client-node}

Create your project and then install [amqplib](https://www.npmjs.com/package/amqplib) with `npm install amqplib --save` . With that installed you can begin creating the code. The amqplib package has two APIs; the more traditional callback style and a "when"-based promises style. The example here uses the callback API.

### Creating the connection
{: #creating-connection-node}

The complete code is shown first, then broken down and explained:

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

First, define the necessary `require` functions to call in the library. Note that this includes the `URL` package. You should also create a `bail()` function in the same style as in the RabbitMQ examples.

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

Now you can start the connection process:

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

Start by defining a variable with the connection string URL from the Compose console's overview. Currently the amqp library does not send a server name for the TLS/SSL SNI support to function, but you can parse the URL into its component parts and add `{ servername: parsedurl.hostname }` to the `amqp.connect` options to inject that property into the connection. When the connection is complete the callback function is invoked and makes an initial error check.

Using the connection, the program can now publish a simple message to an exchange. First, it makes a channel for this publication. The code continues in a callback function:

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

The code checks for errors. If successful, it creates variables representing the message, routing key and exchange name to send to. `exchangeName` is used to ensure the named exchange exists. The `assertExchange` function is called with the name, type, options and a callback function. Providing the exchange exists or can be created the code moves on:

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

The `publish` function passes the exchange name and routing key, and wraps the message in a buffer. The message is sent and the code exits:

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

Being asynchronous, the code sets a timeout to close the connection and exit from the node runtime.

For completeness, here's the "when" promise example:

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

The flow is pretty much the same, but the promises ensure things happen in a more manageable order. 

If you run either of these, skip forward to [Verifying the example connection](#section-verifying-the-example-connection) to confirm it does what we expect.

## Java and RabbitMQ

### Installing the client
{: #installing-client-java}

Install the official [RabbitMQ Java Client](http://www.rabbitmq.com/java-client.html). Select the option that suits your development environment. 

### Creating a connection.
{: #creating-connection-java}

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

This is just an example, so the code is doing everything in the main method. It begins with getting a ConnectionFactory for RabbitMQ connections. The URI for the deployment is then sent the factory so that it makes connections that connect to RabbitMQ; note the amqps:// in the URI. 

The code can then ask the factory for a new connection. Using that connection, the program can now publish a simple message to an exchange. First, it makes a channel for this publication:

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

It then sets up a message payload, in this case a string, a routing key for its onward journey and the name of an exchange to send it on to.

With the new values set, it can declare the exchange (a direct exchange which can make use of the routing key), which will be created if it doesn't exist. It can then publish to the named exchange, with a routing key and the message payload encoded as bytes:

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

All the code has to do now is close the channel, close the connection and put in a catch for all the exceptions that may be thrown:

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## Verifying the example connection

When you run the examples here. the code silently connects, delivers the message, and disconnects. To verify that it did something, log into the RabbitMQ Admin UI –  the URL is displayed below the connections strings in the Compose console – and select the Exchanges tab. There should be a "postal" exchange there, which the code created. There should also be some activity shown in the charts. 

To confirm that the message arrived, and because it is not possible to look into the exchange, create a queue to consume the message.

+ Go to the Queues tab
![The Queues tab in the Admin UI.](./images/queues_tab.png)
+ Add a queue with a name, for example *fred* 
+ Go back to the Exchanges tab 
+ Select the *postal* exchange
![The Exchanges tab in the Admin UI.](./images/exchanges_tab.png)
* Select **Bindings**
* In _Add binding from this exchange_ select "to queue" and enter *fred* for the queue name
* Enter "tributes" as the routing key
![Adding a binding on the Exchanges tab.](./images/add_binding.png)
* Click **Bind**
* Run the example code to send a message
* In Admin UI, go to the Queues tab 
* Select the *fred* queue
![Overview of a Queue.](./images/queue_overview.png)
* Open up the Get Messages panel
![Get messages dialogue.](./images/get_message_dialog.png)
* Click **Get Message** to display the message
![Results of Get Message.](./images/get_message_results.png)

Any messages sent before a queue was bound to the exchange are discarded automatically as they could not be routed. RabbitMQ has a mechanism to catch unroutable messages called [Alternate Exchanges](https://www.rabbitmq.com/ae.html) for special cases, but it is generally best to ensure everything is routed in the messaging architecture.

In this case, the message stays in the queue even though getting a message is a destructive act. This is because, on the _Get Messages_ panel, the default setting is to re-queue the messages after they are retrieved.

## Ruby and RabbitMQ

There are a number of drivers for the Ruby language. [Bunny](http://rubybunny.info/) is one of the best known of them - you can find full tutorials and documentation on the [Bunny](http://rubybunny.info/) web site. As of writing, Bunny 2.7.0 has not been released; this contains a patch to use SNI when making the TLS connection. You can build this for yourself using `gem install specific_install ` then `gem specific_install https://github.com/ruby-amqp/bunny`.

To connect to Compose RabbitMQ and to do the same as the example above, use the following code:
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
When run, the code emits a warning something like this:
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
The server is not configured to verify clients (and Compose doesn't currently offer client certificate verification); despite this message the connection upgrade will succeed and be verified with a trusted Lets Encrypt certificate.

## Python and RabbitMQ

This code uses the [pika](http://pika.readthedocs.org/en/0.10.0/index.html) library, as recommended by the RabbitMQ developers.
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
The code first pulls in the libraries it needs. It then creates the parameters needed to create a connection - specifically the RabbitMQ URL.

Following the model of the previous examples, it then uses that to connect and posts a message to the `postal` exchange with a routing key.

## Go and RabbitMQ

For Go we recommend the [github.com/streadway/amqp](https://github.com/streadway/amqp) package. 

In this Go example, the code only makes a server verified connection. 
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

Note that the `failonError` function shortens the Go error handling.

The main method starts by creating the connection. The RabbitMQ password is passed to the `Dial` function. A `DialTLS` function also exists, but the use of `amqps` in the URL is sufficient to turn on TLS connections.

Using  `defer` ensures that the connection is closed when exiting.

The rest of the Go code, as with previous examples, opens a channel, creates the exchange and sends a message.

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
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

# Conexión de una aplicación externa
{: #connecting-external-app}

Encontrará la información que necesita para establecer conexión con {{site.data.keyword.composeForRabbitMQ_full}} en la página *Visión general* del servicio {{site.data.keyword.composeForRabbitMQ}}.

Los ejemplos de este apartado cubren Node, Java, Ruby, Python y Go. Empiece por leer el ejemplo de [Java y RabbitMQ](#java-and-rabbitmq), en el que se cubren los conceptos y se explica cómo conectar y verificar que el código funciona según lo esperado y cómo comprobar que se ha conectado al host adecuado.

Encontrará el código completo de este ejemplo y de otros en [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns).</p></div>

Antes de empezar, también puede consultar las [guías de aprendizaje de RabbitMQ](http://www.rabbitmq.com/getstarted.html).

## Node y RabbitMQ

### Instalación del cliente
{: #installing-client-node}

Cree el proyecto y luego instale [amqplib](https://www.npmjs.com/package/amqplib) con `npm install amqplib --save`. Con esto instalado puede empezar a crear el código. El paquete amqplib tiene dos API; una de estilo devolución de llamada más tradicional y una de estilo Promise basada en "when". En los ejemplos de este apartado se utiliza la API de devolución de llamada.

### Creación de la conexión
{: #creating-connection-node}

Primero se muestra el código completo y luego se desglosa y se explica:

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

En primer lugar, defina las funciones `requiere` necesarias para llamar a la biblioteca. Esto incluye el paquete `URL`. También debe crear una función `bail()` del mismo estilo que los ejemplos de RabbitMQ.

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

Ahora puede iniciar el proceso de conexión:

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

Empiece definiendo una variable con el URL de la serie de conexión de la visión general de la consola de Compose. Actualmente, la biblioteca amqp no envía un nombre de servidor para que funcione el soporte de SNI TLS/SSL, pero puede analizar el URL por sus partes componentes y añadir `{ servername: parsedurl.hostname }` a las opciones de `amqp.connect` para inyectar la propiedad en la conexión. Cuando la conexión se ha completado, se invoca la función de devolución de llamada, que realiza una comprobación de errores inicial.

Mediante la conexión, ahora el programa puede publicar un mensaje sencillo en un intercambio. En primer lugar, crea un canal para esta publicación. El código continúa en la función de devolución de llamada:

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

El código comprueba si hay errores. Si no los hay, crea variables que representan el mensaje, la clave de direccionamiento y el nombre de intercambio al que se va a enviar. Se utiliza `exchangeName` para garantizar que el intercambio nombrado existe. Se llama a la función `assertExchange` con el nombre, el tipo, las opciones y una función de devolución de llamada. Si el intercambio existe o se puede crear, el código continúa de este modo:

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

La función `publish` pasa el nombre del intercambio y la clave de direccionamiento y coloca el mensaje en un almacenamiento intermedio. El mensaje se envía y el código sale:

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

Como es asíncrono, el código define un tiempo de espera para cerrar la conexión y salir del entorno de tiempo de ejecución del nodo.

Para terminar, aquí se muestra un ejemplo de promise "when":

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

El flujo es prácticamente igual, pero los Promises garantizan que las acciones se suceden en un orden que se puede gestionar. 

Si ejecuta cualquiera de estos códigos, vaya directamente a [Verificación de la conexión de ejemplo](#section-verifying-the-example-connection) para confirmar que hace lo que esperaba.

## Java y RabbitMQ

### Instalación del cliente
{: #installing-client-java}

Instale el componente [RabbitMQ Java Client](http://www.rabbitmq.com/java-client.html) oficial. Seleccione la opción que se adecúe a su entorno de desarrollo. 

### Creación de una conexión.
{: #creating-connection-java}

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

Este es solo un ejemplo, de modo que el código lo hace todo en el método principal. Comienza por obtener ConnectionFactory para las conexiones RabbitMQ. Luego el URI correspondiente al despliegue se envía a la fábrica para que establezca conexiones que conecten con RabbitMQ; observe la parte amqps: // del URI. 

Luego el código puede pedir a la fábrica una nueva conexión. Mediante dicha conexión, ahora el programa puede publicar un mensaje sencillo en un intercambio. En primer lugar, crea un canal para esta publicación:

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

Luego configura una carga útil de mensaje, en este caso una serie de caracteres, una clave de direccionamiento la ruta y el nombre de un intercambio al que enviarlo.

Con los nuevos valores definidos, puede declarar el intercambio (un intercambio directo que puede utilizar la clave de direccionamiento), que se creará si no existe. A continuación puede publicar en el intercambio nombrado, con una clave de direccionamiento y la carga útil de mensaje codificados como bytes:

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

Ahora todo lo que tiene que hacer el código es cerrar el canal, cerrar la conexión y definir un método que capture todas las excepciones que se puedan emitir:

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## Verificación de la conexión de ejemplo

Cuando ejecute estos ejemplos, el código se conecta de forma silenciosa, distribuye el mensaje y se desconecta. Para verificar que ha hecho algo, inicie una sesión en la IU de administración de RabbitMQ - el URL se muestra bajo las series de conexión en la consola de Compose - y seleccione el separador Intercambios. Debería haber un intercambio "postal", que ha creado el código. En los diagramas se debería mostrar alguna actividad. 

Para confirmar que el mensaje ha llegado, y debido a que no es posible mirar el intercambio, crear una cola para consumir el mensaje.

+ Vaya al separador Colas ![El separador Colas de la IU de administración.](./images/queues_tab.png)
+ Añadir una cola con un nombre, por ejemplo *fred* 
+ Vuelva al separador Intercambios 
+ Seleccione el intercambio *postal* cambio ![El separador Intercambios de la IU de administración.](./images/exchanges_tab.png)
* Seleccione **Enlaces**
* En _Añadir enlace desde este intercambio_, seleccione "a cola" y especifique *fred* para el nombre de la cola
* Escriba "tributes" como clave de direccionamiento
![Adición de un enlace en el separador Intercambios.](./images/add_binding.png)
* Pulse **Enlazar**
* Ejecute el código de ejemplo para enviar un mensaje
* En la IU de administración, vaya al separador Colas 
* Seleccione la cola *fred* ![Visión general de una cola.](./images/queue_overview.png)
* Abra el panel Obtener mensajes ![Diálogo Obtener mensajes.](./images/get_message_dialog.png)
* Pulse **Obtener mensaje** para ver el mensaje ![Resultados de Obtener mensaje.](./images/get_message_results.png)

Los mensajes enviados antes de que se enlazara una cola al intercambio se descartan automáticamente porque no se han podido direccionar. RabbitMQ tiene un mecanismo para capturar los mensajes que no se pueden direccionar denominado [Alternar intercambios](https://www.rabbitmq.com/ae.html) para casos especiales, pero en general es mejor para garantizar que todo se direcciona en la arquitectura de mensajería.

En este caso, el mensaje permanece en la cola aunque obtener un mensaje es un acto destructivo. Esto se debe a que, en el panel _Obtener mensajes_, el valor predeterminado consiste en volver a colocar en cola los mensajes después de que se recuperen.

## Ruby y RabbitMQ

Hay varios controladores para el lenguaje Ruby. [Bunny](http://rubybunny.info/) es uno de los más conocidos; encontrará guías de aprendizaje completas y documentación en el sitio web de [Bunny](http://rubybunny.info/). En el momento de publicar esta guía aún no ha salido al mercado Bunny 2.7.0; contiene un parche para utilizar SNI cuando se realiza una conexión TLS. Puede crearlo usted mismo mediante `gem install specific_install ` y luego `gem specific_install https://github.com/ruby-amqp/bunny`.

Para conectar con Compose RabbitMQ y hacer lo mismo que en el ejemplo anterior, utilice el siguiente código:
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
Cuando se ejecuta, el código emite un aviso parecido al siguiente:
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
El servidor no está configurado para verificar los clientes (y Compose actualmente no ofrece verificación de certificados de cliente); a pesar de este mensaje, la actualización de la conexión se ejecutará correctamente y se verificará con un certificado de Lets Encrypt fiable.

## Python y RabbitMQ

En este código se utiliza la biblioteca [pika](http://pika.readthedocs.org/en/0.10.0/index.html). tal como recomiendan los desarrolladores de RabbitMQ.
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
En primer lugar el código extrae las bibliotecas que necesita. Luego crea los parámetros necesarios para crear una conexión, en concreto el URL de RabbitMQ.

Siguiendo el modelo de los ejemplos anteriores, lo utiliza para conectar y publica un mensaje en el intercambio `postal` con una clave de direccionamiento.

## Go y RabbitMQ

Por Go recomendamos el paquete [github.com/streadway/amqp](https://github.com/streadway/amqp). 

En este ejemplo de Go, el código solo realiza una conexión verificada de servidor. 
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

Observe que la función `failonError` abrevia el manejo de errores de Go.

El método principal empieza por crear la conexión. La contraseña de RabbitMQ se pasa a la función `Dial`. También existe una función `DialTLS`, pero utilizar `amqps` en el URL es suficiente para activar las conexiones TLS.

El uso de `defer` garantiza que la conexión se cierra al salir.

El resto del código de Go, como sucede con los ejemplos anteriores, abre un canal, crea el intercambio y envía un mensaje.

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

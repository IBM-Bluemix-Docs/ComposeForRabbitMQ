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

# Conectando um aplicativo externo
{: #connecting-external-app}

É possível localizar as informações que você precisa para se conectar ao {{site.data.keyword.composeForRabbitMQ_full}} na página *Visão geral* de seu serviço {{site.data.keyword.composeForRabbitMQ}}.

Os exemplos aqui cobrem Node, Java, Ruby, Python e Go. É necessário começar lendo o exemplo [Java e RabbitMQ](#java-and-rabbitmq), porque isso cobre os conceitos, como conectar e verificar se seu código está funcionando conforme o esperado e como verificar se está se conectando ao host correto.

É possível localizar o código integral para este exemplo e os subsequentes em [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns).</p></div>

Antes de iniciar, você também pode desejar consultar os [Tutoriais do RabbitMQ](http://www.rabbitmq.com/getstarted.html).

## Node e RabbitMQ

### Instalando o cliente

Crie seu projeto e, em seguida, instale o [amqplib](https://www.npmjs.com/package/amqplib) com `npm install amqplib --save`. Com isso instalado, é possível começar a criar o código. O pacote amqplib tem duas APIs; o estilo de retorno de chamada mais tradicional e um estilo de promessa com base em "when". O exemplo aqui usa a API de retorno de chamada.

### Criando a conexão

O código completo é mostrado primeiro, em seguida, dividido e explicado:

```javascript
#!/usr/bin/env node

var amqp = require('amqplib/callback_api'); var url = require('url');

function bail(err, conn) {
    console.error(err); if (conn) conn.close(function() {
        process.exit(1);
    });
}

rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err!== null) return bail(err, conn);

    conn.createChannel(function(err, channel) {

        if (err!== null) return bail(err, conn); var message = "This is not a message, this is a node tribute to a message"; var routingKey = "tributes"; var exchangeName = "postal";

        channel.assertExchange(exchangeName, "direct", {
            durable: true }, function(err, ok) {
            if (err!== null) return bail(err, conn); channel.publish(exchangeName, routingKey, new Buffer(message))
        });

    	});

    setTimeout(function() { conn.close(); process.exit(0) }, 500);

});

```

Primeiro, defina as funções `require` necessárias para chamar na biblioteca. Observe que isso inclui o pacote `URL`. É necessário também criar uma função `bail()` no mesmo estilo que nos exemplos do RabbitMQ.

```text
#!/usr/bin/env node

var amqp = require('amqplib/callback_api'); var url = require('url');

function bail(err, conn) {
    console.error(err); if (conn) conn.close(function() {
        process.exit(1);
    });
}
```

Agora é possível iniciar o processo de conexão:

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity'; parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err!== null) return bail(err, conn);
```

Inicie definindo uma variável com a URL de sequência de conexões na visão geral do console do Compose. Atualmente, a biblioteca amqp não envia um nome do servidor para que o suporte TLS/SSL SNI funcione, mas é possível analisar a URL em suas partes do componente e incluir `{ servername: parsedurl.hostname }` nas opções `amqp.connect` para injetar essa propriedade na conexão. Quando a conexão é concluída, a função de retorno de chamada é chamada e faz uma verificação de erro inicial.

Usando a conexão, o programa pode agora publicar uma mensagem simples para uma troca. Primeiro, ele faz um canal para essa publicação. O código continua em uma função de retorno de chamada:

```javascript
			conn.createChannel(function(err, channel) {
        if (err!== null) return bail(err, conn); var message = "This is not a message, this is a node tribute to a message"; var routingKey = "tributes"; var exchangeName = "postal";
```

O código verifica erros. Se bem-sucedido, ele cria variáveis que representam a mensagem, a chave de roteamento e o nome de troca para envio. `exchangeName` é usado para assegurar que a troca nomeada exista. A função `assertExchange` é chamada com o nome, o tipo, as opções e uma função de retorno de chamada. Considerando que a troca existe ou pode ser criada, o código se move em:

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true }, function(err, ok) {
            if (err!== null) return bail(err, conn); channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

A função `publish` passa o nome de troca e a chave de roteamento e agrupa a mensagem em um buffer. A mensagem é enviada e o código sai:

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

Sendo assíncrono, o código configura um tempo limite para fechar a conexão e sair do tempo de execução do nó.

Para completude, aqui está o exemplo de promessa "when":

```javascript
#!/usr/bin/env node

var amqp = require('amqplib');
var when = require('when');
var url = require('url');

rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity'; parsedurl = url.parse(rabbitmqurl);

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

O fluxo é praticamente o mesmo, mas as promessas asseguram que as coisas aconteçam em uma ordem mais gerenciável. 

Se você executar uma delas, avance para [Verificando a conexão de exemplo](#section-verifying-the-example-connection) para confirmar que faça o que esperamos.

## Java e RabbitMQ

### Instalando o cliente

Instale o [Cliente Java do RabbitMQ](http://www.rabbitmq.com/java-client.html) oficial. Selecione a opção que se adeque a seu ambiente de desenvolvimento. 

### Criando uma conexão.

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

Isto é apenas um exemplo, então o código está executando tudo no método principal. Isso inicia com a obtenção de um ConnectionFactory para conexões do RabbitMQ. O URI para a implementação é então enviado para a factory para que faça conexões que se conectam ao RabbitMQ; observe o amqps:// no URI. 

O código pode então solicitar ao factory por uma nova conexão. Usando essa conexão, o programa pode agora publicar uma mensagem simples para uma troca. Primeiro, ele faz um canal para essa publicação:

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

Em seguida, configura uma carga útil da mensagem, neste caso uma sequência, uma chave de roteamento para sua jornada adiante e o nome de uma troca para enviá-la.

Com os novos valores configurados, é possível declarar a troca (uma troca direta que pode fazer uso da chave de roteamento), que será criada se não existir. É possível então publicar a troca nomeada, com uma chave de roteamento e uma carga útil da mensagem codificada como bytes:

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

Tudo o que o código precisa executar agora é fechar o canal, fechar a conexão e colocar em uma captura para todas as exceções que podem ser lançadas:

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## Verificando a conexão de exemplo

Quando você executa os exemplos aqui, o código se conecta silenciosamente, entrega a mensagem e desconecta. Para verificar se ele fez algo, efetue login na UI de Administrador do RabbitMQ – a URL é exibida abaixo das sequências de conexões no console do Compose – e selecione a guia Trocas. Deve haver uma troca "postal" ali, que o código criou. Também deve haver alguma atividade mostrada nos gráficos. 

Para confirmar que a mensagem chegou e porque não é possível examinar a troca, crie uma fila para consumir a mensagem.

+ Acesse a guia Filas
![The Queues tab in the Admin UI.](./images/queues_tab.png)
+ Inclua uma fila com um nome, por exemplo *fred* 
+ Volte para a guia Trocas 
+ Selecione a troca *postal*
![The Exchanges tab in the Admin UI.](./images/exchanges_tab.png)
* Selecione **Ligações**
* Em _Incluir ligação dessa troca_ selecione "para fila" e insira *fred* para o nome da fila
* Insira "tributes" como a chave de roteamento
![Adding a binding on the Exchanges tab.](./images/add_binding.png)
* Clique em **Ligar**
* Execute o código de exemplo para enviar uma mensagem
* Na UI de Administrador, acesse a guia Filas 
* Selecione a fila *fred*
![Overview of a Queue.](./images/queue_overview.png)
* Abra o painel Obter mensagens
![Get messages dialogue.](./images/get_message_dialog.png)
* Clique em **Obter mensagem** para exibir a mensagem
![Results of Get Message.](./images/get_message_results.png)

Quaisquer mensagens enviadas antes que uma fila fosse ligada à troca são descartadas automaticamente porque não puderam ser roteadas. O RabbitMQ tem um mecanismo para capturar mensagens não roteáveis chamado [Trocas alternativas](https://www.rabbitmq.com/ae.html) para casos especiais, mas é geralmente melhor assegurar que tudo esteja roteado na arquitetura do sistema de mensagens.

Nesse caso, a mensagem permanece na fila mesmo que obter uma mensagem seja um ato destrutivo. Isso ocorre porque, no painel _Obter mensagens_, a configuração padrão é enfileirar novamente as mensagens após serem recuperadas.

## Ruby e RabbitMQ

Há vários drivers para a linguagem Ruby. [Bunny](http://rubybunny.info/) é um dos mais conhecidos deles - é possível localizar tutoriais e a documentação no website do [Bunny](http://rubybunny.info/). Quando isto foi escrito, o Bunny 2.7.0 não tinha sido liberado; isso contém uma correção para usar SNI ao fazer a conexão TLS. É possível construir isso por si usando `gem install specific_install `, em seguida, `gem specific_install https://github.com/ruby-amqp/bunny`.

Para se conectar ao Compose RabbitMQ e executar o mesmo que o exemplo acima, use o código a seguir:
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
Quando executado, o código emite um aviso semelhante a este:
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
O servidor não está configurado para verificar clientes (e o Compose não oferece atualmente a verificação de certificado de cliente); apesar dessa mensagem, o upgrade de conexão será bem-sucedido e será verificado com um certificado Lets Encrypt confiável.

## Python e RabbitMQ

Este código usa a biblioteca [pika](http://pika.readthedocs.org/en/0.10.0/index.html), conforme recomendado pelos desenvolvedores do RabbitMQ.
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
O código primeiro puxa as bibliotecas que ele precisa. Em seguida, cria os parâmetros necessários para criar uma conexão, especificamente a URL do RabbitMQ.

Seguindo o modelo dos exemplos anteriores, ele então usa isso para se conectar e posta uma mensagem para a troca `postal` com uma chave de roteamento.

## Go e RabbitMQ

Para Go, recomendamos o pacote [github.com/streadway/amqp](https://github.com/streadway/amqp). 

Nesse exemplo do Go, o código faz somente uma conexão verificada pelo servidor. 
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

Observe que a função `failonError` reduz a manipulação de erros do Go.

O método principal inicia criando a conexão. A senha do RabbitMQ é passada para a função `Dial`. Uma função `DialTLS` também existe, mas o uso de `amqps` na URL é suficiente para ativar as conexões TLS.

O uso de `defer` assegura que a conexão seja encerrada ao sair.

O resto do código do Go, como nos exemplos anteriores, abre um canal, cria a troca e envia uma mensagem.

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

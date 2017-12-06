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

# 连接外部应用程序
{: #connecting-external-app}

您可以在 {{site.data.keyword.composeForRabbitMQ}} 服务的*概述*页面中找到连接到 {{site.data.keyword.composeForRabbitMQ_full}} 所需的信息。

此处的示例涵盖 Node、Java、Ruby、Python 和 Go。您开始时应该通读 [Java 和 RabbitMQ](#java-and-rabbitmq) 示例，因为这涵盖相关概念、如何连接和验证代码如预期运作，以及如何检查您所连接的是正确的主机。

您可以在 [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns) 上找到此示例和后续示例的完整代码。</p></div>

开始之前，您可能还想参阅 [RabbitMQ 教程](http://www.rabbitmq.com/getstarted.html)。

## Node 和 RabbitMQ

### 安装客户机

创建项目，然后使用 `npm install amqplib --save` 安装 [amqplib](https://www.npmjs.com/package/amqplib)。通过该安装，您可以开始创建代码。amqplib 软件包具有两个 API；更传统的回调样式和基于“when”的约定样式。此处的示例使用回调 API。

### 创建连接

首先显示完整的代码，然后进行细分并解释：

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

首先，定义必要的 `require` 函数以在库中调用。请注意，这包括 `URL` 软件包。您还应使用与 RabbitMQ 示例中相同的样式来创建 `bail()` 函数。

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

现在，您可以启动连接过程：

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

开始时，先通过 Compose 控制台的概述，使用连接字符串 URL 定义一个变量。目前，amqp 库不会向函数发送 TLS/SSL SNI 支持的服务器名称，但您可以将该 URL 解析为其组件部分，并将 `{ servername: parsedurl.hostname }` 添加到 `amqp.connect` 选项，以将该属性注入到连接中。连接完成时，将调用回调函数，并进行初始错误检查。

使用此连接，程序现在可以将简单消息发布到交换器。首先，代码会为本次发布提供一个通道。在回调函数中，代码继续执行：

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

代码会检查是否存在错误。如果成功，代码将创建变量，代表消息、路由密钥和要发送到的交换器名称。`exchangeName` 用于确保指定的交换器存在。使用名称、类型、选项和回调函数来调用 `assertExchange` 函数。如果交换器存在或可以创建，那么代码将继续：

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

`publish` 函数传递交换器名称和路由密钥，并将消息包装在缓冲区中。此时将发送消息，且代码将退出：

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

由于异步的原因，该代码会设置超时以关闭连接，并从节点运行时退出。

为完整起见，以下是“when”约定示例：

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

流程几乎相同，但这些约定确保事情以更易于管理的顺序发生。 

如果您运行其中任何一个，请跳到[验证示例连接](#section-verifying-the-example-connection)以确认其符合预期。

## Java 和 RabbitMQ

### 安装客户机

安装正式 [RabbitMQ Java 客户机](http://www.rabbitmq.com/java-client.html)。选择适合您的开发环境的选项。 

### 创建连接

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

这只是一个示例，因此代码在 main 方法中执行所有操作。它一开始先获取 ConnectionFactory 以进行 RabbitMQ 连接。然后，将部署的 URI 发送到工厂，以便使连接能够连接到 RabbitMQ；请注意 URI 中的 amqps://。 

然后，代码可以向工厂要求新连接。使用此连接，程序现在可以将简单消息发布到交换器。首先，代码会为本次发布提供一个通道：

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

然后，它会设置消息有效内容，在此例中，它是一个字符串、一个用于其向前行程的路由键，以及要将其发送到的交换器的名称。

通过设置新值，它可以声明交换器（可使用路由键的直接交换器），如果不存在，那么将创建该交换器。然后，它可以发布到指定的交换器，并将路由键和消息有效内容编码为字节：

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

现在，代码必须执行的内容是关闭通道、关闭连接并抓取可能抛出的所有异常：

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## 验证示例连接

在此处运行示例时，代码会以静默方式连接、传递消息，然后断开连接。要验证是否已执行操作，请登录到 RabbitMQ 管理 UI - URL 显示在 Compose 控制台中的连接字符串下方，然后选择“交换器”选项卡。那里应该有一个代码所创建的“postal”交换器。图表中还应该显示某个活动。 

要确认消息已到达，并且由于无法查看该交换器，请创建一个队列来使用该消息。

+ 转至“队列”选项卡
![管理 UI 中的“队列”选项卡。](./images/queues_tab.png)
+ 使用名称（例如 *fred*）添加队列 
+ 返回到“交换器”选项卡 
+ 选择 *postal* 交换器
![管理 UI 中的“交换器”选项卡。](./images/exchanges_tab.png)
* 选择**绑定**
* 在_添加此交换器的绑定_中，选择“至队列”并输入 *fred* 作为队列名称
* 输入“tributes”作为路由键
![在“交换器”选项卡上添加绑定。](./images/add_binding.png)
* 单击**绑定**
* 运行示例代码以发送消息
* 在管理 UI 中，转至“队列”选项卡 
* 选择 *fred* 队列
![队列概述。](./images/queue_overview.png)
* 打开“获取消息”面板
![“获取消息”对话框。](./images/get_message_dialog.png)
* 单击**获取消息**以显示消息
![“获取消息”的结果。](./images/get_message_results.png)

在队列绑定到交换器之前发送的任何消息都会由于无法路由而自动废弃。对于特殊情况，RabbitMQ 有一种捕获无法路由消息的机制，称为[轮流交换](https://www.rabbitmq.com/ae.html)，但是通常最好确保在消息传递体系结构中路由所有内容。

在此情况下，消息会保留在队列中，即使获取消息是具有破坏性的行为也是如此。这是因为在_获取消息_面板上，缺省设置是在检索消息后将其重新排入队列。

## Ruby 和 RabbitMQ

有许多适用于 Ruby 语言的驱动程序。[Bunny](http://rubybunny.info/) 是其中最知名的一个，您可以在 [Bunny](http://rubybunny.info/) Web 站点上找到完整的教程和文档。在编写本文时，尚未发布 Bunny 2.7.0；这包含修补程序，以在进行 TLS 连接时使用 SNI。您可以使用 `gem install specific_install `，然后使用 `gem specific_install https://github.com/ruby-amqp/bunny` 来自行构建此项。

要连接到 Compose RabbitMQ 并执行与上述示例相同的操作，请使用以下代码：
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
运行时，代码会发出警告，如下所示：
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
服务器未配置为验证客户机（并且 Compose 当前未提供客户机证书验证）; 不管此消息为何，连接升级都将成功，并通过可信的 Lets Encrypt 证书进行验证。

## Python 和 RabbitMQ

此代码使用 RabbitMQ 开发者建议的 [pika](http://pika.readthedocs.org/en/0.10.0/index.html) 库。
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
代码首先会拉入它需要的库。然后，它将建立创建连接所需的参数 - 具体来说是 RabbitMQ URL。

按照先前示例的模型，代码随后会使用该模型，将消息连接并发布到具有路由键的 `postal` 交换器。

## Go 和 RabbitMQ

对于 Go，我们建议使用 [github.com/streadway/amqp](https://github.com/streadway/amqp) 软件包。 

在此 Go 示例中，代码仅进行经服务器验证的连接。 
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

请注意，`failonError` 函数将缩短 Go 错误处理的时间。

main 方法由创建连接开始。RabbitMQ 密码将传递至 `Dial` 函数。`DialTLS` 函数也存在，但在 URL 中使用 `amqps` 足以开启 TLS 连接。

使用 `defer` 可确保退出时关闭连接。

其余 Go 代码（与先前示例一样）将打开一个通道，创建交换器并发送消息。

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

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

# 連接外部應用程式
{: #connecting-external-app}

您可以在 {{site.data.keyword.composeForRabbitMQ}} 服務的*概觀* 頁面上，找到連接至 {{site.data.keyword.composeForRabbitMQ_full}} 所需的資訊。

這裡的範例涵蓋 Node、Java、Ruby、Python 及 Go。您應該由閱讀 [Java 和 RabbitMQ](#java-and-rabbitmq) 範例開始，因為其中涵蓋概念、如何連接及驗證您的程式碼如預期運作，以及如何檢查您是否連接至正確的主機。

您可以在 [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns) 找到這個範例及後續範例的完整程式碼。</p></div>

開始之前，您可能也想要參閱 [RabbitMQ 指導教學](http://www.rabbitmq.com/getstarted.html)。

## Node 及 RabbitMQ

### 安裝用戶端
{: #installing-client-node}

建立專案，然後使用 `npm install amqplib --save` 來安裝 [amqplib](https://www.npmjs.com/package/amqplib)。安裝完成後，您就可以開始建立程式碼。amqplib 套件有兩個 API；較傳統的回呼樣式及以 "when" 為基礎的 Promise 樣式。這裡的範例使用回呼 API。

### 建立連線
{: #creating-connection-node}

先顯示完整的程式碼，再細分並加以說明：

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

首先，定義要在程式庫中呼叫的必要 `require` 函數。請注意，這包括 `URL` 套件。您也應該依照 RabbitMQ 範例中的相同樣式，建立 `balas()` 函數。

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

現在，您可以啟動連線處理程序：

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

首先，使用 Compose 主控台概觀中的連線字串 URL 來定義變數。目前 amqp 程式庫不會將 TLS/SSL SNI 支援的伺服器名稱傳送至函數，但是您可以將 URL 剖析成它的元件部分，並將 `{ servername: parsedurl.hostname }` 新增至 `amqp.connect` 選項，以將該內容注入至連線。連線完成時，會呼叫回呼函數，並進行起始錯誤檢查。

現在，程式可以使用連線，將簡單訊息發佈至交換。首先，它會建立一個頻道進行此發佈。程式碼會在回呼函數中繼續執行：

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

程式碼會檢查是否發生錯誤。如果成功，它會建立變數，代表訊息、遞送鍵和要傳送至的交換名稱。`exchangeName` 是用來確保具名交換存在。`assertExchange` 函數是藉由名稱、類型、選項及回呼函數呼叫的。假如交換存在或可以建立，程式碼便會繼續執行：

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

`publish` 函數會傳遞交換名稱和遞送鍵，並將訊息包裝在緩衝區中。會傳送訊息，然後程式碼結束：

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

非同步時，程式碼會設定逾時，以關閉連線並從節點運行環境中結束。

對於完整性，以下是 "when" 的 Promise 範例：

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

流程大致相同，但 Promise 確保事物會以更容易管理的順序發生。 

如果您執行上述任一個，請往前跳至[驗證範例連線](#section-verifying-the-example-connection)，以確認它的確符合我們的預期。

## Java 及 RabbitMQ

### 安裝用戶端
{: #installing-client-java}

安裝官方的 [RabbitMQ Java 用戶端](http://www.rabbitmq.com/java-client.html)。選取適合您開發環境的選項。 

### 建立連線。
{: #creating-connection-java}

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

這只是一個範例，因此程式碼會在 main 方法中執行所有作業。一開始會取得 ConnectionFactory 進行 RabbitMQ 連線。接著，將部署的 URI 傳送至 Factory，讓它可以建立連接至 RabbitMQ 的連線；請注意 URI 中的 amqps://。 

然後，程式碼可以要求 Factory 進行新連線。現在，程式可以使用該連線，將簡單訊息發佈至交換。首先，它會建立一個頻道進行此發佈：

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

它會設定訊息有效負載（在此案例中為字串）、之後行程的遞送鍵，以及要將它傳送至的交換名稱。

設定新值後，它可以宣告交換（可以使用遞送鍵的直接交換），而且如果不存在，便會加以建立。然後，它可以發佈至具名交換，並附上遞送鍵及已編碼為位元組的訊息有效負載：

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

現在，所有程式碼必須做的是關閉頻道、關閉連線，以及將所有可能擲出的異常狀況置於 catch 中：

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## 驗證範例連線

當您在這裡執行範例時，程式碼會無聲自動連接、遞送訊息，然後中斷連接。若要驗證它是否執行了某個動作，請登入「RabbitMQ 管理使用者介面」（URL 顯示在 Compose 主控台的連線字串下），然後選取「交換」標籤。那裡應該有一個 "postal" 交換，這是由程式碼所建立。也應該有一些活動顯示在圖表中。 

若要確認訊息已送達，因為無法查看交換，所以請建立佇列以使用訊息。

+ 移至「佇列」標籤
![管理使用者介面中的「佇列」標籤。](./images/queues_tab.png)
+ 新增名稱為 *fred*（舉例來說）的佇列 
+ 回到「交換」標籤 
+ 選取 *postal* 交換
![管理使用者介面中的「交換」標籤。](./images/exchanges_tab.png)
* 選取**連結**
* 在_從這個交換中新增連結_ 中，選取「目標佇列」，並輸入 *fred* 作為佇列名稱
* 輸入 "tributes" 作為遞送鍵
![在「交換」標籤上新增連結。](./images/add_binding.png)
* 按一下**連結**
* 執行範例程式碼來傳送訊息
* 在管理使用者介面中，移至「佇列」標籤 
* 選取 *fred* 佇列
![佇列的概觀。](./images/queue_overview.png)
* 開啟「取得訊息」畫面
![「取得訊息」對話方塊](./images/get_message_dialog.png)
* 按一下**取得訊息**來顯示訊息
![「取得訊息」的結果。](./images/get_message_results.png)

在佇列連結至交換之前傳送的任何訊息會自動捨棄，因為無法遞送它們。RabbitMQ 針對特殊情況，提供了可捕捉無法遞送之訊息的機制，稱為[替代交換](https://www.rabbitmq.com/ae.html)，但通常最好是確保所有訊息都在傳訊架構中遞送。

在此案例中，即使取得訊息是破壞性行動，訊息仍會保留在佇列中。這是因為在_取得訊息_ 畫面上，預設值是在擷取訊息之後將訊息重新放回佇列。

## Ruby 及 RabbitMQ

Ruby 語言有許多驅動程式。[Bunny](http://rubybunny.info/) 是其中一個最為人所知的驅動程式 - 您可以在 [Bunny](http://rubybunny.info/) 網站上找到完整的指導教學和文件。在本文撰寫時，尚未發行 Bunny 2.7.0；這包含在進行 TLS 連線時使用 SNI 的修補程式。您可以先使用 `gem install specific_install`，再使用 `gem specific_install https://github.com/ruby-amqp/bunny`，自行建置。

若要連接至 Compose RabbitMQ，以及執行與上述範例相同的動作，請使用下列程式碼：
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
執行時，程式碼會發出警告，類似如下：
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
伺服器未配置為驗證用戶端（而且 Compose 目前不提供用戶端憑證驗證）；儘管有此訊息，連線升級仍將成功，並使用受信任的 Lets Encrypt 憑證進行驗證。

## Python 及 RabbitMQ

此程式碼依照 RabbitMQ 開發人員的建議使用 [pika](http://pika.readthedocs.org/en/0.10.0/index.html) 程式庫。
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
程式碼首先將它需要的程式庫取入。然後，建立必要參數來建立連線 - 尤其是 RabbitMQ URL。



遵循先前範例的模型，它接著會使用它來連接，並利用遞送鍵張貼訊息至 `postal` 交換。

## Go 及 RabbitMQ

若為 Go，我們建議使用 [github.com/streadway/amqp](https://github.com/streadway/amqp) 套件。 

在此 Go 範例中，程式碼只會建立伺服器驗證的連線。 
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

請注意，`failonError` 函數可縮短 Go 錯誤處理。

main 方法從建立連線開始。RabbitMQ 密碼會傳遞至 `Dial` 函數。也有一個 `DialTLS` 函數，但在 URL 中使用 `amqps` 便足以開啟 TLS 連線。

使用 `defer` 可確保在結束時關閉連線。

與先前範例一樣，Go 程式碼的其餘部分會開啟頻道、建立交換並傳送訊息。

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

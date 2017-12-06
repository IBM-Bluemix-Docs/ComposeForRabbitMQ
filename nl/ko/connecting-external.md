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

# 외부 애플리케이션 연결
{: #connecting-external-app}

{{site.data.keyword.composeForRabbitMQ}} 서비스의 *개요* 페이지에서 {{site.data.keyword.composeForRabbitMQ_full}}에 연결하는 데 필요한 정보를 찾을 수 있습니다.

여기에 나오는 예제에서는 Node, Java, Ruby, Python 및 Go에 대해 다룹니다. [Java 및 RabbitMQ](#java-and-rabbitmq) 예제를 주의 깊게 읽는 것으로 시작해야 합니다. 이 예제에서는 개념, 연결하여 코드가 예상대로 작동하는지 확인하는 방법 및 올바른 호스트에 연결 중인지 검사하는 방법을 다루기 때문입니다.

[github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns)에서 이에 대한 전체 코드와 후속 예제를 찾을 수 있습니다.</p></div>

시작하기 전에 [RabbitMQ 튜토리얼](http://www.rabbitmq.com/getstarted.html)을 참조할 수 있습니다.

## Node 및 RabbitMQ

### 클라이언트 설치

프로젝트를 작성한 후 `npm install amqplib --save`를 사용하여 [amqplib](https://www.npmjs.com/package/amqplib)를 설치하십시오. 설치가 되면 코드 작성을 시작할 수 있습니다. amqplib 패키지에는 두 개의 API, 즉 더 전통적인 콜백 스타일과 "when" 기반 프라미스 스타일이 있습니다. 여기에 나오는 예제에서는 콜백 API를 사용합니다.

### 연결 작성

전체 코드가 먼저 표시된 후 분석되고 설명됩니다.

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

먼저, 라이브러리에서 호출하는 데 필요한 `require` 함수를 정의하십시오. 여기에는 `URL` 패키지가 포함됩니다. 또한 RabbitMQ 예제에서와 동일한 스타일로 `bail()` 함수를 작성해야 합니다.

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

이제 연결 프로세스를 시작할 수 있습니다.

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

Compose 콘솔의 개요에서 연결 문자열 URL로 변수를 정의하여 시작하십시오. 현재 amqp 라이브러리는 TLS/SSL SNI 지원이 작동하도록 하기 위해 서버 이름을 전송하지 않지만 URL을 해당 컴포넌트 파트로 구문 분석하고 `{ servername: parsedurl.hostname }`을 `amqp.connect` 옵션에 추가하여 해당 특성을 연결에 삽입할 수 있습니다. 연결이 완료되면 콜백 함수가 호출되고 초기 오류 검사를 작성합니다.

이제 프로그램에서 연결을 사용하여 단순 메시지를 교환에 게시할 수 있습니다. 먼저, 이 게시를 위한 채널을 작성합니다. 콜백 함수에서 코드가 계속됩니다.

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

코드가 오류를 검사합니다. 성공하면 메시지, 라우팅 키 및 전송한 교환 이름을 나타내는 변수를 작성합니다. `exchangeName`은 이름 지정된 교환이 존재하는지 확인하는 데 사용됩니다. `assertExchange` 함수는 이름, 유형, 옵션 및 콜백 함수와 함께 호출됩니다. 교환이 존재하거나 작성될 수 있는 경우 코드가 다음으로 이동합니다.

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

`publish` 함수는 교환 이름과 라우팅 키를 전달하고 버퍼에서 메시지를 랩핑합니다. 메시지가 전송되고 코드가 종료됩니다. 

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

비동기식이면 코드가 연결을 닫고 노드 런타임에서 종료하는 제한시간을 설정합니다.

완전성을 위해 여기에 "when" 프라미스 예제가 있습니다.

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

플로우는 거의 동일하지만 프라미스는 항목이 더 관리하기 쉬운 순서로 발생하도록 합니다. 

이 중 하나를 실행한 경우 [예제 연결 확인](#section-verifying-the-example-connection)으로 건너뛰어 예상대로 수행하는지 확인하십시오.

## Java 및 RabbitMQ

### 클라이언트 설치

공식 [RabbitMQ Java 클라이언트](http://www.rabbitmq.com/java-client.html)를 설치하십시오. 사용자의 개발 환경에 적합한 옵션을 선택하십시오. 

### 연결 작성

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

이는 예제일 뿐이므로 코드가 기본 메소드에서 모든 작업을 수행합니다. RabbitMQ 연결을 위해 ConnectionFactory를 가져오는 것으로 시작합니다. 그런 다음, RabbitMQ에 연결하는 연결을 작성할 수 있도록 배치용 URI가 팩토리에 전송됩니다. URI의 amqps://에 주의하십시오. 

그런 다음, 코드에서 새 연결을 위한 팩토리를 요청할 수 있습니다. 이제 프로그램에서 해당 연결을 사용하여 단순 메시지를 교환에 게시할 수 있습니다. 먼저, 이 게시를 위한 채널을 작성합니다.

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

그런 다음, 메시지 페이로드(이 경우에는 문자열), 전방 과정을 위한 라우팅 키 및 이를 전송할 교환의 이름을 설정합니다.

설정된 새 값을 사용하여 교환(라우팅 키를 활용할 수 있는 직접 교환)을 선언할 수 있으며, 존재하지 않는 경우 작성됩니다. 그런 다음, 라우팅 키 및 바이트로 인코딩된 메시지 페이로드와 함께 이름 지정된 교환에 게시할 수 있습니다.

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

지금 코드에서 수행해야 하는 모든 작업은 채널을 닫고 연결을 닫은 후 발생할 수 있는 모든 예외를 발견하는 것입니다.

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## 예제 연결 확인

여기서 예제를 실행하면 코드가 자동으로 연결되어 메시지를 전달하고 연결을 끊습니다. 코드가 특정 작업을 수행했는지 확인하려면 RabbitMQ 관리 UI에 로그온하고(URL이 Compose 콘솔의 연결 문자열 아래에 표시됨) 교환 탭을 선택하십시오. 코드가 작성한 "postal" 교환이 거기에 있어야 합니다. 또한 차트에 표시된 일부 활동이 있어야 합니다. 

메시지가 도착했는지 확인하려면 교환의 내부를 볼 수 없으므로 메시지를 이용할 큐를 작성하십시오.

+ 큐 탭으로 이동
![관리 UI의 큐 탭](./images/queues_tab.png)
+ 큐를 이름(예: *fred*)과 함께 추가 
+ 교환 탭으로 되돌아가기 
+ *postal* 교환 선택
![관리 UI의 교환 탭](./images/exchanges_tab.png)
* **바인딩** 선택
* _이 교환에서 바인딩 추가_에서 "대상 큐"를 선택하고 *fred*를 큐 이름으로 입력
* "tributes"를 라우팅 키로 입력
![교환 탭에서 바인딩 추가](./images/add_binding.png)
* **바인드** 클릭
* 예제 코드를 실행하여 메시지 전송
* 관리 UI에서 큐 탭으로 이동 
* *fred* 큐 선택
![큐의 개요](./images/queue_overview.png)
* 메시지 가져오기 패널 열기
![메시지 가져오기 대화 상자](./images/get_message_dialog.png)
* **메시지 가져오기**를 클릭하여 메시지 표시
![메시지 가져오기의 결과](./images/get_message_results.png)

큐가 교환에 바인딩되기 전에 전송된 메시지는 라우팅할 수 없으므로 자동으로 버려집니다. RabbitMQ에는 특별한 경우에 [대체 교환](https://www.rabbitmq.com/ae.html)이라는 라우팅 불가능한 메시지를 발견하는 메커니즘이 있지만 일반적으로 메시징 아키텍처에서 모든 메시지가 라우팅되었는지 확인하는 것이 가장 좋습니다.

이 경우에는 메시지 가져오기가 안전하지 않은 작동인 경우에도 메시지가 큐에 남아 있습니다. _메시지 가져오기_ 패널에서 기본 설정이 메시지가 검색된 후 메시지를 다시 큐에 넣는 것이기 때문입니다.

## Ruby 및 RabbitMQ

다수의 Ruby 언어용 드라이버가 있습니다. [Bunny](http://rubybunny.info/)는 가장 잘 알려진 드라이버 중 하나입니다. [Bunny](http://rubybunny.info/) 웹 사이트에서 전체 튜토리얼과 문서를 찾을 수 있습니다. 작성된 시점을 기준으로 Bunny 2.7.0이 릴리스되지 않았습니다. 여기에는 TLS 연결을 작성할 때 SNI를 사용하기 위한 패치가 포함되어 있습니다. `gem install specific_install`을 사용한 후 `gem specific_install https://github.com/ruby-amqp/bunny`를 사용하여 직접 이를 빌드할 수 있습니다.

Compose RabbitMQ에 연결하고 위의 예제와 동일한 작업을 수행하려면 다음 코드를 사용하십시오.
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
실행 시 코드가 다음과 같은 경고를 생성합니다.
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
서버가 클라이언트를 확인하도록 구성되지 않았습니다(또한 현재 Compose에서 클라이언트 인증서 검증을 제공하지 않음). 이 메시지에도 불구하고 연결 업그레이드가 성공하고 Let's Encrypt 인증서로 확인됩니다.

## Python 및 RabbitMQ

이 코드에서는 RabbitMQ 개발자가 권장하는 대로 [pika](http://pika.readthedocs.org/en/0.10.0/index.html) 라이브러리를 사용합니다.
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
이 코드는 먼저 필요한 라이브러리를 가져옵니다. 그런 다음, 연결을 작성하는 데 필요한 매개변수를 작성합니다(특히, RabbitMQ URL).
그런 다음, 이전 예제의 모델에 따라 이를 사용하여 연결하고 라우팅 키와 함께 `postal` 교환에 게시합니다. 

## Go 및 RabbitMQ

Go의 경우 [github.com/streadway/amqp](https://github.com/streadway/amqp) 패키지를 권장합니다. 

다음 Go 예제에서는 코드가 서버에서 확인된 연결만 작성합니다. 
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

`failonError` 함수는 Go 오류 처리를 단축합니다.

기본 메소드는 연결을 작성하여 시작합니다. RabbitMQ 비밀번호가 `Dial` 함수에 전달됩니다. `DialTLS` 함수도 존재하지만 URL에서 `amqps`를 사용하는 것만으로도 TLS 연결을 켤 수 있습니다.

`defer`를 사용하면 종료될 때 연결이 닫힙니다.

이전 예제와 마찬가지로 Go 코드의 나머지 부분은 채널을 열고 교환을 작성하며 메시지를 전송합니다.

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

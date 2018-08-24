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

# 外部アプリケーションの接続
{: #connecting-external-app}

{{site.data.keyword.composeForRabbitMQ_full}} への接続に必要な情報については、{{site.data.keyword.composeForRabbitMQ}} サービスの*「概要」*ページで確認できます。

以下のサンプルでは、Node、Java、Ruby、Python、Go を取り上げます。 最初に、[Java と RabbitMQ](#java-and-rabbitmq) のサンプルの説明を読んでください。その部分で、いくつかの概念や接続方法、コードの正常な動作の確認方法、接続先が正しいホストであることの確認方法を取り上げています。

以下のサンプルのコード全体が [github.com/compose-ex/rabbitmqconns](https://github.com/compose-ex/rabbitmqconns) にあります。</p></div>

始める前に、可能なら [RabbitMQ のチュートリアル](http://www.rabbitmq.com/getstarted.html)も参照してください。

## Node と RabbitMQ

### クライアントのインストール
{: #installing-client-node}

プロジェクトを作成してから、`npm install amqplib --save` を実行して [amqplib](https://www.npmjs.com/package/amqplib) をインストールします。 インストールが済んだら、コードの作成を開始できます。 amqplib パッケージには 2 つの API があります。従来型のコールバック・スタイルの API と "when" に基づく promise 型のスタイルの API です。 このサンプルではコールバック・スタイルの API を使用します。

### 接続の作成
{: #creating-connection-node}

コード全体を最初に示してから、各部分に分割しながら説明を進めていきます。

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

まず、ライブラリーで呼び出す必要のある `require` 関数を定義します。 `URL` パッケージも含めてください。 また RabbitMQ のサンプルのスタイルと同じスタイルで `bail()` 関数も作成します。

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

次に、接続プロセスを開始します。

```javascript
rabbitmqurl = 'amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity';
parsedurl = url.parse(rabbitmqurl);

amqp.connect(rabbitmqurl, { servername: parsedurl.hostname }, function(err, conn) {
    if (err !== null) return bail(err, conn);
```

最初に、Compose コンソールの概要の部分から取得した接続ストリングの URL によって変数を定義します。 現時点で、amqp ライブラリーは、TLS/SSL SNI サポートが正しく機能するのに必要なサーバー名を送信しませんが、コードの中で URL を解析して各構成要素に分解すれば、`{ servername: parsedurl.hostname }` を `amqp.connect` オプションに追加して、そのプロパティーを接続に挿入できます。 接続が完了してから、コールバック関数を呼び出し、最初のエラー・チェックを実行します。

このプログラムは、接続を使用して、シンプルなメッセージを exchange に発行できます。 まず、その発行処理のためのチャネルを作成します。 コールバック関数内のコードは、以下のように続きます。

```javascript
			conn.createChannel(function(err, channel) {
        if (err !== null) return bail(err, conn);
        var message = "This is not a message, this is a node tribute to a message";
        var routingKey = "tributes";
        var exchangeName = "postal";
```

このコードでエラーをチェックします。 成功すれば、メッセージ、ルーティング・キー、メッセージの送信先の exchange 名を表す変数を作成します。 `exchangeName` によって、その名前の exchange が存在することを確認します。 その名前とタイプとオプションとコールバック関数を指定して `assertExchange` 関数を呼び出します。 その exchange が存在するか、その作成が可能であれば、コードの処理が進んでいきます。

```javascript
				channel.assertExchange(exchangeName, "direct", {
            durable: true
        }, function(err, ok) {
            if (err !== null) return bail(err, conn);
            channel.publish(exchangeName, routingKey, new Buffer(message))
        });
```

`publish` 関数によって、exchange 名とルーティング・キーを渡し、メッセージをバッファーの中にラップします。 メッセージを送信すると、コードは終了します。

```javascript
		});
    setTimeout(function() { conn.close(); process.exit(0) }, 500);
});

```

非同期処理なので、このコードでは、接続を閉じてノードのランタイムから出るためのタイムアウトを設定します。

念のために、"when" に基づく promise 型のサンプルも示します。

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

フローはほぼ同じですが、promise 型のほうが順序を管理しやすいという利点があります。 

どちらかを実行する場合は、[サンプル接続の確認](#section-verifying-the-example-connection)にスキップして、正しく動作することを確認してください。

## Java と RabbitMQ

### クライアントのインストール
{: #installing-client-java}

公式の [RabbitMQ Java Client](http://www.rabbitmq.com/java-client.html) をインストールします。 ご使用の開発環境に適したオプションを選択してください。 

### 接続の作成
{: #creating-connection-java}

```java
public class RabbitMQConnector {
  public static void main(String[] args) {
  	try {
  		ConnectionFactory factory = new ConnectionFactory();
  		factory.setUri("amqps://user:password@portal194-1.rabbity.compose-3.composedb.com:10194/Rabbity");

      Connection conn = factory.newConnection();
```

これは単なるサンプルであり、このコードでは main メソッドですべての処理を実行しています。 最初に、RabbitMQ 接続の ConnectionFactory を取得します。 デプロイメントの URI をファクトリーに送信し、ファクトリーによって RabbitMQ への接続を作成します。URI の amqps:// の部分に注目してください。 

このコードでは、ファクトリーによって別の新しい接続を作成します。 このプログラムは、その接続を使用して、シンプルなメッセージを exchange に発行できます。 まず、その発行処理のためのチャネルを作成します。

```java
  		Channel channel = conn.createChannel();

      String	message = "This is not a message, this is a tribute to a message";
  		String	routingKey = "tributes";
  		String	exchangeName = "postal";
```

その後、メッセージ・ペイロードをセットアップします。この場合は、ストリング、経路のためのルーティング・キー、送信先の exchange の名前です。

そうした新しい値の設定に基づいて exchange (ルーティング・キーを利用できる direct exchange) を宣言します。もし存在しなければ、その exchange を作成します。 その後、ルーティング・キーと、バイトとしてエンコードしたメッセージ・ペイロードに基づいて、その名前の exchange にメッセージを発行します。

```java
            channel.exchangeDeclare(exchangeName,"direct",true);
            channel.basicPublish(exchangeName, routingKey, null, message.getBytes());
```

残りは、チャネルを閉じ、接続を閉じ、スローされるすべての例外を catch するコードです。

```java
  		channel.close();
  		conn.close();
  	} catch (IOException | TimeoutException ex) {
  		Logger.getLogger(RabbitJava.class.getName()).log(Level.SEVERE, null, ex);
  	}
  }
}
```

## サンプル接続の確認

サンプル・コードを実行すると、サイレント・モードで接続し、メッセージを送信してから、切断する、という処理が行われます。 その動作を確認するために、
まず RabbitMQ Admin UI にログインしてください (URL は、Compose コンソールの接続ストリングの下に表示されます)。その後、「Exchanges」タブを選択します。 「postal」という exchange が表示されます。それが、コードによって作成した exchange です。 グラフにいくらかのアクティビティーも表示されているはずです。 

メッセージが届いたことを確認するために、メッセージを取り込むキューを作成します (exchange の中を確認することはできないからです)。

+ 「Queues」タブに移動します。![Admin UI の「Queues」タブ](./images/queues_tab.png)
+ *fred* などの名前のキューを追加します。 
+ 「Exchanges」タブに戻ります。 
+ *postal* exchange を選択します。![Admin UI の「Exchanges」タブ](./images/exchanges_tab.png)
* **「Bindings」**を選択します。
* _「Add binding from this exchange」_で「to queue」を選択し、*fred* というキュー名を入力します。
* ルーティング・キーとして「tributes」と入力します。![「Exchanges」タブでのバインディングの追加](./images/add_binding.png)
* **「Bind」**をクリックします。
* サンプル・コードを実行してメッセージを送信します。
* Admin UI の「Queues」タブに移動します。 
* *fred* キューを選択します。![キューの概要](./images/queue_overview.png)
* 「Get Messages」パネルを開きます。![「Get Messages」ダイアログ](./images/get_message_dialog.png)
* **「Get Message」**をクリックしてメッセージを表示します。![「Get Message」の結果](./images/get_message_results.png)

キューを exchange にバインドする前に送信したメッセージは、自動的に破棄されます。ルーティングができないからです。 RabbitMQ には、ルーティング不能のメッセージをキャッチするためのメカニズムがありますが ([Alternate Exchanges](https://www.rabbitmq.com/ae.html) を参照)、これは特殊なケースなので、通常は、メッセージング・アーキテクチャーですべてのメッセージをルーティングするようにしてください。

メッセージを取得するのは破壊的な行為ですが、この場合は、メッセージがキューに残ります。 _「Get Messages」_パネルのデフォルト設定では、メッセージ取得後に再キューイングによってメッセージが再びキューに入るからです。

## Ruby と RabbitMQ

Ruby 言語に対応したドライバーはいくつもあります。 その中でも、[Bunny](http://rubybunny.info/) は最も一般的なドライバーの 1 つです。詳しいチュートリアルや資料が [Bunny](http://rubybunny.info/) の Web サイトにあります。 この資料の執筆時点で、Bunny 2.7.0 はまだリリースされていませんが、TLS 接続時に SNI を使用するためのパッチがそのドライバーに組み込まれることになっています。 `gem install specific_install` と `gem specific_install https://github.com/ruby-amqp/bunny` を使用して自分でビルドすることも可能です。

Compose RabbitMQ に接続して、上記のサンプルと同じ処理を実行するには、以下のコードを使用します。
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
このコードを実行すると、以下のような警告が表示されます。
```text
W, [2015-11-03T10:45:51.476133 #24628]  WARN -- #<Bunny::Session:0x7fa6319881c0 dj@aws-eu-west-1-portal.1.dblayer.com:11020, vhost=tangy-rabbitmq-80, addresses=[aws-eu-west-1-portal.1.dblayer.com:11020]>: Using TLS but no client certificate is provided! If RabbitMQ is configured to verify peer
certificate, connection upgrade will fail!
```
クライアントの確認機能がサーバーで構成されていません (Compose には現時点でクライアント証明書の確認機能がありません) が、このメッセージが表示されても、接続のアップグレードは成功し、信頼できる Lets Encrypt の証明書で確認が行われます。

## Python と RabbitMQ

このコードでは、RabbitMQ の開発者たちが推奨している [pika](http://pika.readthedocs.org/en/0.10.0/index.html) ライブラリーを使用しています。
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
このコードでは最初に、必要なライブラリーを取り込みます。 その後、接続の作成に必要なパラメーター (RabbitMQ の URL) を作成します。

前のサンプルのモデルと同じく、その情報を使用し、ルーティング・キーに基づいて `postal` exchange に接続し、メッセージを送信します。

## Go と RabbitMQ

Go の場合は、[github.com/streadway/amqp](https://github.com/streadway/amqp) パッケージをお勧めします。 

以下の Go のサンプル・コードは、サーバーによる確認の済んだ接続を作成する部分だけになっています。 
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

`failonError` 関数によって Go のエラー処理を短縮しています。

main メソッドでは、まず接続を作成します。 RabbitMQ のパスワードを `Dial` 関数に渡します。 `DialTLS` 関数もありますが、TLS 接続をオンにするには、URL で `amqps` を使用するだけで十分です。

`defer` を使用することで、終了時に確実に接続を閉じます。

Go のコードの残りの部分では、前のサンプルと同じように、チャネルを開き、exchange を作成して、メッセージを送信します。

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

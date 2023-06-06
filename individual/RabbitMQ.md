RabbitMQ 是一个 AMQP（高级消息队列协议）基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。它支持多种语言，如 Python、Ruby、.NET、Java、JMS、C、PHP 和 Ajax，并且文档齐全，提供了开源的管理界面，很高的社区活跃度，更新频率相当高.

RabbitMQ 的四大核心概念是：Exchange、Queue、Binding 和 Routing Key。六大模式是：简单模式、工作队列模式、发布/订阅模式、路由模式、主题模式和 RPC 模式。

RabbitMQ 的核心概念包括：Server、Connection、Channel、Virtual Host、Exchange 和 Queue。其中，Server 又称 Broker，它接受客户端的连接，实现 AMQP 实体服务；Connection 是连接，应用程序与 Broker 的网络连接 TCP/IP/三次握手和四次挥手；Channel 是网络信道，几乎所有的操作都在 Channel 中进行，Channel 是进行消息读写的通道；Virtual host 是虚拟主机，一个 Broker 里可以开设多个虚拟主机，用作不同用户的权限分离；Exchange 是消息交换机，它指定消息按什么规则，路由到哪个队列；Queue 是消息队列，每个消息都会被投入到一个或多个队列。

## Exchange （交换机）

Exchange 是 RabbitMQ 中的一种对象，用于接收生产者发送的消息并将其路由到一个或多个队列。Exchange 有四种类型：direct、fanout、topic 和 headers。其中，direct 类型的 Exchange 将消息路由到与 Routing Key 完全匹配的队列；fanout 类型的 Exchange 将消息路由到所有与之绑定的队列；topic 类型的 Exchange 将消息路由到与 Routing Key 匹配的队列；headers 类型的 Exchange 将消息路由到与消息头匹配的队列 。

## Channel （信道）

Channel 是我们与 RabbitMQ 打交道的最重要的一个接口，大部分的业务操作是在 Channel 这个接口中完成的，包括定义 Queue、定义 Exchange、绑定 Queue 与 Exchange、发布消息等。如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 Connection 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 thread 创建单独的 Channel 进行通讯，AMQP method 包含了 channel id 帮助客户端和 message broker 识别 channel，所以 channel 之间是完全隔离的。

## Binding （绑定）

Binding 是 Exchange 和 Queue 之间的一种关系，它的作用是将 Exchange 中的消息路由到 Queue 中。Binding 可以使用 Routing Key 来指定消息的路由规则。

## Routing Key （路由键）

Routing Key 是 Exchange 用来将消息路由到与之绑定的 Queue 的关键字。

## Virtual Host （虚拟主机）

Virtual Host 是 RabbitMQ 中的一个逻辑概念，它是一个独立的、完全拥有自己的 Queue、Exchange 和绑定的 RabbitMQ 服务器实体。一个 RabbitMQ 服务器可以有多个 Virtual Host，每个 Virtual Host 之间是相互隔离的，它们拥有自己的用户和权限。

## RabbitMQ 的工作原理

RabbitMQ 的工作原理是：生产者将消息发送到交换机，交换机根据路由规则将消息路由到一个或多个队列，消费者从队列中获取消息进行处理。具体来说，生产者（Producer）与消费者（Consumer）和 RabbitMQ 服务（Broker）建立连接，然后生产者发布消息（Message）同时需要携带交换机（Exchange）名称以及路由规则（Routing Key），这样消息会到达指定的交换机，然后交换机根据路由规则匹配对应的 Binding，最终将消息发送到匹配的消息队列（Quene），最后 RabbitMQ 服务将队列中的消息投递给订阅了该队列的消费者（消费者也可以主动拉取消息）。

## 示例代码

### 发布消息

```python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(
        "localhost", 5672, "/", pika.PlainCredentials("guest", "guest")
    )
)
channel = connection.channel()

channel.exchange_declare(exchange="my_exchange", exchange_type="direct")
channel.queue_declare(queue="my_queue")
channel.queue_bind(
    exchange="my_exchange", queue="my_queue", routing_key="my_routing_key"
)

channel.basic_publish(
    exchange="my_exchange", routing_key="my_routing_key", body="Hello World!"
)
print(" [x] Sent 'Hello World!'")

connection.close()
```

### 消费消息

```python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(
        "localhost", 5672, "/", pika.PlainCredentials("guest", "guest")
    )
)
channel = connection.channel()

channel.exchange_declare(exchange="my_exchange", exchange_type="direct")
channel.queue_declare(queue="my_queue")
channel.queue_bind(
    exchange="my_exchange", queue="my_queue", routing_key="my_routing_key"
)


def callback(ch, method, properties, body):
    print(" [x] Received %r" % body)


channel.basic_consume(queue=queue_name, on_message_callback=callback, auto_ack=True)

print(" [*] Waiting for messages. To exit press CTRL+C")
channel.start_consuming()
```
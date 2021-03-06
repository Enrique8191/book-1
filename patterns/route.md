### Routing

NSQ has two types of message delivery:

- broadcast (fanout)
- balancing

See <a href="https://github.com/IrisMQ/book/blob/master/mq/topics.md">topics and channels</a>

If you come from RabbitMQ, this is how the concept of routing translates to NSQ.

Consider this example straight from RabbitMQ:

<img src="https://itjumpstart.files.wordpress.com/2016/02/rabbit.png">

**Message Producer**

- with NSQ, our message producer would publish three topics: info, error, warning (along with its payload)

**Message Queue**

- NSQ would receive the topics and payload
- NSQ needs to determine which consumer gets which topic(s)

**Message Consumer**

- in the first consumer, C1 would subscribe to topic error and indicate its channel as amq.gen-S9b

- in the second consumer, C2 would subscribe to those three topics and indicate its channel as amq.gen-Ag1

The concept of channel is NSQ's way to determine which consumer will receive which topic. That is, if you need to broadcast the same message to a certain number of consumers, each consumer must have a unique channel name. For instance, to broadcast one message to 20 consumers, you must have 20 unique channel names (consumer:channel is 1:1). See <a href="https://github.com/IrisMQ/book/blob/master/mq/nsqbroadcast.md">broadcast example</a>

In this example, if NSQ sees the same channel name for our two consumers, NSQ will load balance the messages (that is, each consumer will get only one message). See <a href="https://github.com/IrisMQ/book/blob/master/mq/nsqbalancing.md">load balancing example</a>.

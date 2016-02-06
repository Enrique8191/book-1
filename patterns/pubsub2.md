### Publish/Subscribe Revisited

<img src="https://itjumpstart.files.wordpress.com/2016/02/irismq.png">

Once you have used NSQ for a while, it will dawn on you that NSQ basically has one messaging pattern: publish/subscribe.

But this needs more treatment because it becomes more nuanced once you compare it with Iris.

With NSQ, publish/subscribe comes in two forms:

- producers publish a topic, consumers subscribe to topic but each consumer has unique channel name ([broadcasting](https://github.com/IrisMQ/book/blob/master/mq/nsqbroadcast.md))
- producers publish a topic, consumers subscribe to the same topic ([semantic load balancing](https://github.com/IrisMQ/book/blob/master/mq/nsqbalancing.md))

This is the core concept of NSQ: [topics and channels](https://github.com/IrisMQ/book/blob/master/mq/topics.md)

Now, when you use Iris, things are subtly different.

- producers publish a topic, consumers subscribe to topic (broadcast to all consumers)
- Iris may simulate semantic load balancing but at the producer level (that is, producer may partition the topics using namespace)

Moreover, Iris has node load balancing but the messaging pattern is request/reply!

That is, if you have one physical/VM node for client request but multiple nodes for services (reply), Iris will load balance the request to multiple nodes.

To illustrate the concepts, consider the following example:

<img src="https://itjumpstart.files.wordpress.com/2016/02/topics.png">

**NSQ Publish/Subscribe**

This is how you will build your NSQ apps (producers and consumers). Channel names can be anything. Note that producer and consumer are separate NSQ apps and so are consumer1 to consumer4.

- (1) producer publishes topic(unicast), consumer subscribes to topic(unicast) channel(HR)
- (2) producer publishes topic(anycast)

- consumer1 to consumer4 subscribes to topic(anycast), channel(loadbalancing)

- (3) producer publishes topic(groupcast) 

consumer1 subscribes to topic(groupcast), channel(HR) <br>
consumer3 subscribes to topic(groupcast), channel(Accounting) <br>

- (4) producer publishes topic(broadcast) 

consumer1 subscribes to topic(broadcast), channel(HR) <br>
consumer2 subscribes to topic(broadcast), channel(Payroll) <br>
consumer3 subscribes to topic(broadcast), channel(Accounting) <br>
consumer4 subscribes to topic(broadcast), channel(Engineering) <br>

**Iris Publish/Subscribe**

Iris has no concept like NSQ channel but you can do the same in Iris by using namespaces.

- producer publishes topic(unicast), consumer subscribes to topic(unicast-HR)

Iris may load balance or partition the messages at the producer level (like sharding).

- producer publishes topic(anycast-1), consumer subscribes to topic(anycast-1)
- producer publishes topic(anycast-2), consumer subscribes to topic(anycast-2)
- producer publishes topic(anycast-3), consumer subscribes to topic(anycast-3)
- producer publishes topic(anycast-4), consumer subscribes to topic(anycast-4)

Groupcast and broadcast work in the usual sense of Iris [broadcast](https://github.com/IrisMQ/book/blob/master/patterns/irisbroadcast.md).

- producer publishes topic(groupcast), consumer1 and consumer3 subscribe to topic(groupcast)
- producer publishes topic(broadcast), consumer1 to consumer4 subscribe to topic(broadcast)

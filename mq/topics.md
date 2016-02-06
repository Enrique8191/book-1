<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

## Topics and Channels

This post is courtesy of Guillaume Charmes (http://blog.charmes.net/2014/10/first-look-at-nsq.html)

### Concept of the Queue

The idea of a Queue is to ```queue``` (who would have guessed?) messages. So we will have a producer that sends messages to the queue, and 
on the other side, we will have a consumer that pops messages. A nice feature of most queueing systems is what we call pub/sub (i.e. 
multiple consumers can ```subscribe``` to the queue and a message published by a producer will be received by 1 or N consumers).

### Topic and Channels

NSQ allows two mode of communication: broadcast and balancing.

- broadcast: A message published will be received by all subscribers
- balancing: A message published will be received by (at least) one subscriber.

In order to control this, NSQ provides two concepts: topic and channel.

When a consumer is created, it will subscribe to a topic/channel pair. However, when a producer is created, it will only publish to a topic.
A message published on a topic will be copied (broadcast) to each channels, then distributed within this channel.

Let's see an example:

**Balancing**

- Consumer1 subscribe to mytopic / mychannel
- Consumer2 subscribe to mytopic / mychannel
- Consumer3 subscribe to mytopic / mychannel
- Producer1 published to mytopic

In this scenario, each message published will be received only by a single consumer. In a perfect world, if Producer1 publishes 3 messages, 
each consumer will receive one. (In reality, it is random, but you get the idea).

**Broadcast**

- Consumer1 subscribe to mytopic / mychannel1
- Consumer2 subscribe to mytopic / mychannel2
- Consumer3 subscribe to mytopic / mychannel3
- Producer1 published to mytopic

In this scenario, each message published will be received by all the consumers, because they all subscribed to a different channel. 
If Producer1 publishes 3 messages, each consumer will receive all 3 messages.

**Broadcast + Balancing**

Example from nsq.io:

- Consumer1 subscribe to clicks / metrics
- Consumer2 subscribe to clicks / metrics
- Consumer3 subscribe to clicks / metrics
- Consumer4 subscribe to clicks / spam_analytics
- Consumer5 subscribe to clicks / archives
- Producer1 published to clicks

In this scenario, if Producer1 publishes 3 messages:

- Consumer1 receives 1 message (because balanced on the channel)
- Consumer2 receives 1 message
- Consumer3 receives 1 message
- Consumer4 receives 3 message (each message is copied to all the different channels)
- Consumer5 receives 3 message

Once you get the concept, the schema on their website makes a lot of sense:

<img src=https://itjumpstart.files.wordpress.com/2015/12/nsq.gif>

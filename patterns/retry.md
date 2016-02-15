<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Message Retry, Backoff and Jitter

Messaging system like NSQ follows a unidirectional workflow. That is, a message flows from the producer to the message queue (NSQ) and ultimately to the consumer.

So there are only three possibilities on the location of respective components:

- producer and message queue on one node, consumer on another
- producer on one node, message queue and consumer on another
- producer, message queue and consumer each on separate node

On trivial systems, you may colocate producer or consumer with the message queue. The advantage of colocating message producer and message queue on the same node is speed: there is no network trip. 

For distributed systems, it is common that all three components reside separate with each other.

**The rationale for unidirectional workflow is simple:**

- if the producer expects a reply, it is not asynchronous (hence not suitable for messaging). 

Example: Database transaction, User-facing Web page request

- if the producer does not expect a reply in **the same context**, it is asynchronous and suitable for messaging.

Example: Background tasks, batch processing, on-off consumers like mobile phones, etc.


**The network is not reliable**

As a property of distributed system, the network is not reliable. So each component of messaging systems needs to consider network partition. That is, a producer might fail sending its message to the message queue. And so is the message queue to the consumer. The message consumer might fail sending its acknowledgment to the message queue.

It is the responsibility of message producer to keep track of its messages before sending it to the message queue (NSQ). In the event of send failure, the message producer can simply retry. 

Since NSQ is an in-memory message queue, you tradeoff message replication for performance. This is the classic shard vs replication debate (see [distributed system matrix](https://github.com/IrisMQ/book/blob/master/principles/matrix.md)). However, you can turn on [message persistence to disk](http://nsq.io/overview/features_and_guarantees.html) by passing ```--mem-queue-size=0``` to nsqd.

**Resiliency Patterns**

Assuming our message producer is trying to contact nsqd but for some reason, we cannot connect. You can retry, then wait for a little while and try again until you are successfully connected.

Sample below uses a simple Go package from https://github.com/jpillora/backoff.

```go
package main

import (
	"fmt"
	"net"
	"time"

	"github.com/jpillora/backoff"
)

func main() {
	b := &backoff.Backoff{
		Min:    100 * time.Millisecond,
		Max:    10 * time.Second,
		Jitter: true,
	}

	for {
		_, err := net.Dial("tcp", "192.168.0.153:4150")
		if err != nil {
			d := b.Duration()
			fmt.Printf("%s, reconnecting in %s", err, d)
			time.Sleep(d)
			continue
		}
		//connected
		fmt.Println("connected...")
		break
	}

	//continue your work here
	fmt.Println("doing something...")
}
```

Other related links:

- https://github.com/eapache/go-resiliency
- https://github.com/sethgrid/pester
- https://github.com/carlescere/goback
- https://github.com/mreiferson/go-httpclient
- https://github.com/rubyist/circuitbreaker
- https://github.com/sony/gobreaker
- [Don't use Go's default HTTP client](https://medium.com/@nate510/don-t-use-go-s-default-http-client-4804cb19f779#)

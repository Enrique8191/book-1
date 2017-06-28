### Fast Producer, Slow Consumer

There is no harm in reiterating concepts just to emphasize the differences between the following software:

- Iris is an always-on message transport with built-in clustering (cluster membership and automatic detection of failed nodes)
- NSQ is a message queue (distributed, in-memory with optional disk persistence on high-water mark)

In this section, we are going to focus on one case when dealing with a message queue.

A message queue can be categorized into two:

- brokerless - a message producer connects directly to message consumer (nanomsg or ZeroMQ)
- broker-based - a message producer connects to a broker which then connects to message consumer (like NSQ)

Further still, a broker-based message queue can be categorized into two:

- peer-to-peer - in the case of Iris, a message producer connects to a relay which forms an overlay network to connect with other peers
- client/server - a message producer connects to a server (which is the broker like NSQ)

It is important to note that brokerless message queue has its own internal queue that holds the messages. As much as possible, it does not impose too much features (like message retry, backoff, etc) because its primary purpose is to transport messages from point A to point B. That's it.

On the other hand, a message broker sports a lot of features which vary according to its philosophy.

So without further ado, let's focus on NSQ.

With NSQ, you can have lots of message producers which can reside on the same node as the NSQ daemon itself (nsqd) or on different nodes within a private LAN. The message consumers may also reside on the same node as nsqd but since NSQ is a real-time distributed messaging platform, you can decouple message consumers from nsqd. The tool that orchestrates which consumer connects to which producer is the responsibility of nsqlookupd.

nsq clients (producer) <------> nsqd <------> nsqlookupd <------> nsq clients (consumer)

Let's say we have five message producers on five different nodes. Assume these producers are fast and furious. They stream messages at a fast clip.

On the other end are three message consumers. Assume one of these three went down (hardware failure, network partition, etc).

In this case, the two remaining message consumers will be overwhelmed in no time.

By default, Go clients (consumers) accept one message at a time. Once the consumer is done with message processing, it signals to nsqd a success acknowledgment. Using the official Go client library, you just return nil to the handler callback.

```go
package main

import (
	"flag"
	"fmt"
	"github.com/nsqio/go-nsq"
	"log"
	"sync"
	"sync/atomic"
	"time"
)

var start = time.Now()
var ops uint64 = 0
var numbPtr = flag.Int("msg", 10000, "number of messages (default: 10000)")

func main() {

	/*
	   Below are the default port settings
	   nsqd listens at port 4150 (for TCP clients), 4151 (for HTTP clients)
	   nsqlookupd listens at port 4160 (for TCP clients), 4161 (for HTTP clients)
	   nsqadmin listens at port 4171 (for HTTP clients) or
	     to be specified (for go-nsq clients) q.ConnectToNSQLookupd("127.0.0.1:4161")
	   http://tleyden.github.io/blog/2014/11/12/an-example-of-using-nsq-from-go/
	   $ nsqlookupd &
	   $ nsqd --lookupd-tcp-address=127.0.0.1:4160 &
	   $ nsqadmin --lookupd-http-address=127.0.0.1:4161 &
	*/

	flag.Parse()

	wg := &sync.WaitGroup{}

	config := nsq.NewConfig()
	q, _ := nsq.NewConsumer("test", "ch", config)

	q.AddHandler(nsq.HandlerFunc(func(message *nsq.Message) error {
		wg.Add(1)

		//log.Printf("Got a message: %v", string(message.Body))

		atomic.AddUint64(&ops, 1)
		if ops == uint64(*numbPtr) {
			elapsed := time.Since(start)
			log.Printf("Time took %s", elapsed)
		}

		wg.Done()
		return nil
	}))

	//err := q.ConnectToNSQD("127.0.0.1:4150") - hardcoded

	err := q.ConnectToNSQLookupd("127.0.0.1:4161") //better
	if err != nil {
		log.Panic("Could not connect")
	}
	wg.Wait()

	fmt.Scanln()
}
```

Here is another example from another Go client library called [nsqueue](https://github.com/crackcomm/nsqueue) based on go-nsq. In this case, you return msg.Success(). The syntax is different but the semantics is the same.

```go
package main

import (
	"flag"
	"fmt"
	"time"

	"github.com/crackcomm/nsqueue/consumer"
)

var (
	nsqdAddr    = flag.String("nsqd", "127.0.0.1:4150", "nsqd http address")
	maxInFlight = flag.Int("max-in-flight", 30, "Maximum amount of messages in flight to consume")
)

func handleTest(msg *consumer.Message) {
	t := &time.Time{}
	t.UnmarshalBinary(msg.Body)
	fmt.Printf("Consume latency: %s\n", time.Since(*t))
	msg.Success()
}

func main() {
	flag.Parse()
	consumer.Register("latency-test", "consume", *maxInFlight, handleTest)
	consumer.Connect(*nsqdAddr)
	consumer.Start(true)
}
```

Remember that the default behavior of Go NSQ client is to accept messages from nsqd one at a time. To fix the fast producer, slow consumer problem, we need to set MaxInFlight [configuration](https://godoc.org/github.com/nsqio/go-nsq#Config) to a sane value (for example, 10).

This means, our NSQ client would get messages by batch. Since Go has built-in concurrency primitive called goroutines, the official Go client library ([go-nsq](https://github.com/nsqio/go-nsq/blob/master/consumer.go)) takes advantage of that using a function called ```AddConcurrentHandlers```.

```go
// AddConcurrentHandlers sets the Handler for messages received by this Consumer.  It
// takes a second argument which indicates the number of goroutines to spawn for
// message handling.
//
// This panics if called after connecting to NSQD or NSQ Lookupd
//
// (see Handler or HandlerFunc for details on implementing this interface)
func (r *Consumer) AddConcurrentHandlers(handler Handler, concurrency int) {
	if atomic.LoadInt32(&r.connectedFlag) == 1 {
		panic("already connected")
	}

	atomic.AddInt32(&r.runningHandlers, int32(concurrency))
	for i := 0; i < concurrency; i++ {
		go r.handlerLoop(handler)
	}
}
```

So if you set MaxInFlight to 10, you can have 10 messages executing concurrently. The nsqueue library has a Register function that does that exactly for you.

Here is the code for Register from [nsqueue](https://github.com/crackcomm/nsqueue/blob/master/consumer/consumer.go).

```go
// Register - Registers topic/channel handler for messages
// This function creates a new nsq.Reader
func (c *Consumer) Register(topic, channel string, maxInFlight int, handler Handler) error {
	tch := topicChan{topic, channel}

	var config *nsq.Config
	if c.Config == nil {
		config = nsq.NewConfig()
		config.Set("verbose", DefaultVerbose)
		config.Set("max_in_flight", maxInFlight)
	} else {
		config = c.Config
	}

	r, err := nsq.NewConsumer(topic, channel, config)
	if err != nil {
		return err
	}

	r.SetLogger(c.Logger, c.LogLevel)

	q := &queue{handler, r}
	r.AddConcurrentHandlers(q, maxInFlight)
	c.handlers[tch] = q
	return nil
}
```

Setting MaxInFlight to a proper value is a matter of benchmarking and prudence on your end.

If you use the default Go client library (go-nsq), you can have different values for MaxInFlight and concurrency unit in AddConcurrentHandlers.

With nsqueue, it is the same but that is, of course, a matter of preference.

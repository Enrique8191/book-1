### Processing Modes

**Two Fundamentally Different Viewpoints**

As far as computing is concerned, there are only two types of processing modes:

- Pull-based processing (request/reply) - synchronous

- Push-based processing (messaging) - asynchronous

#### Pull-based Processing

**Examples**

- RPC - there is nothing inherently [wrong](http://armstrongonsoftware.blogspot.com/2008/05/road-we-didnt-go-down.html) with RPC as long as it has timeout (like [gRPC](http://www.grpc.io/posts/principles/))
- REST - GET, POST (based on HTTP or HTTP/2)
- GraphQL - form of Map/Reduce
- Map/Reduce - Hadoop
- Kafka - [consumers](http://sookocheff.com/post/kafka/kafka-in-a-nutshell/) pull messages from the message [broker](https://kafka.apache.org/08/design.html) (Kafka)

This is the first model taught in computing, the classic Hello World example.

In this example, the Go program requests or asks the Go runtime (the fmt package) to print the string.

```
package main

import "fmt"

func main() {
	fmt.Println("Hello World")
}
```



The Web is the most popular example of pull-based processing (otherwise known as request/reply pattern)

```go
//https://golang.org/pkg/net/http/#example_Get

package main

import (
	"io"
	"net/http"
	"log"
)

// hello world, the web server
func HelloServer(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "hello, world!\n")
}

func main() {
	http.HandleFunc("/hello", HelloServer)
	err := http.ListenAndServe(":12345", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

Pull-based processing (or request/reply pattern) must operate within a specific time window called time-out. This is called 
[synchronization](https://en.wikipedia.org/wiki/Synchronous_communication) because the request and reply must work in unison. That is why request/reply is called synchronous (or bidirectional).

If the request/reply is asynchronous, it is called unidirectional because the reply to a request is not part of the latter processing but can 
be part of job or batch processing that can be processed at a later time.

It is important to note that the request/reply pattern is a form of client/server computing regardless of deployment:

1. the same process (intra-process)
2. physical/virtual machine (inter-process)
3. within the same network
4. across networks

Moreover, client/server computing does not care whether it follows 

1. centralized (broker) or 
2. decentralized (brokerless or peer-to-peer) architecture

The **main feature for a request/reply pattern** is to enable **time-out** so clients would have a resort on how to deal with failures on server side.

In the world of Web computing, pull-based processing can be further classified into two:

- stateless - read more about JSON Web Token

- stateful - read more [here](http://highscalability.com/blog/2015/10/12/making-the-case-for-building-scalable-stateful-services-in-t.html)

In this material, we will only discuss about stateless computing.



#### Push-based Processing

With push-based processing, it is the same client/server computing but this time, it is the server that pushes data to the client using 
messaging patterns like the following:

- publish/subscribe
- broadcast
- anycast
- unicast
- reactive patterns (ReactiveX, [Kefir](https://github.com/rpominov/kefir), etc)
- [RethinkDB](https://www.rethinkdb.com/faq) apps
- NSQ (message broker) pushes messages to consumers

In the world of ReactiveX, push-based processing is known as Observables. For single items, it is called futures. For multiple items, it is
called observables. However, with observables, it unifies the paradigm of futures into one common API so using observables is like killing
two birds with one stone.

From ReactiveX [site](http://reactivex.io/intro.html),

> ReactiveX is not biased toward some particular source of concurrency or asynchronicity. Observables can be implemented using thread-pools, event loops, non-blocking I/O, actors (such as from Akka), or whatever implementation suits your needs, your style, or your expertise.

One **salient feature of push-based processing** is it is inherently **asynchronous**. If you mix up synchronous request/reply and asynchronous push-based processing, you are in trouble because these two processing modes are not compatible.

In this material, we are going to focus on inter-process communication only.

For intra-process communication, the following Go packages can be used.

- https://github.com/go-mangos (implementation of Scalability Protocols)
- https://github.com/asaskevich/eventbus (used by Lantern desktop)
- https://github.com/alecthomas/gorx
- https://github.com/paulbellamy/pipe
- and a lot others out there

#### Summary

- pull-based - clients use time-out to deal with failures on server side
- push-based - server pushes data to clients so in case of failures on client side, there must be a mechanism on server side to persist the failed message (in memory or on disk) in such a way that the client has the ability to retry processing

Either way, clients regardless whether they use pull-based or push-based processing deal with failures by employing techniques like

- retry
- backoff
- jitter
- time-out
- cancellation

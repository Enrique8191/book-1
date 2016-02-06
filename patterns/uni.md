<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Unicast

In an Iris cluster with three or more nodes, there is no direct way of sending a message from one node to another node. One way is to broadcast a message to all nodes in the cluster. Upon message receipt, each message consumer needs to parse the message and see if it matches the IP address in the message payload. This would be fine if it is a one-off message, but if you have dozens of nodes and you correspond to only one node once in a while, that would be a terrible waste of unnecessary network traffic.

Fortunately, there is [nanomsg](http://nanomsg.org), a socket library that provides several common communication patterns. In this material, I will use [mangos](https://github.com/go-mangos/mangos), an implementation in pure Go of nanomsg (aka Scalability Protocols).

It is important to note that much of nanomsg is also implemented in Iris so if you are confused whether to use nanomsg or Iris for message transport, here is my tip.

- nanomsg - use Scalability Protocols for direct node-to-node communication (1:1)
- Iris - use Iris for cluster-based communication

Having said that, here is an example of unicast.

**Message Producer**


```go
//pushpipeline.go

package main

import (
	"flag"
	"fmt"
	"log"
	"os"

	"github.com/ibmendoza/msgq"
)

//Example:
//pushpipeline -ip tcp://192.168.0.150:65000 -msg "Hello world"

var ip = flag.String("ip", "", "IP address")
var msg = flag.String("msg", "", "message to send")

func main() {
	flag.Parse()

	if *ip == "" {
		log.Fatal("ip address required")
	}

	if *msg == "" {
		log.Fatal("message required")
	}

	fmt.Println(*ip)

	pushpipeline, err := msgq.NewPushPipeline(*ip)
	if err != nil {
		log.Fatal("Error creating pushpipeline socket")
	}

	err = msgq.Dial(pushpipeline, *ip)
	if err != nil {
		fmt.Println(err)
		os.Exit(1)
	}

	for i := 1; i <= 1000; i++ {
		err = msgq.Send(pushpipeline, []byte(*msg))
		if err != nil {
			log.Println("error sending msg")
		}
	}
}
```

**Message Consumer**

```go
//pullpipeline.go

package main

import (
	"flag"
	"fmt"
	"log"

	"github.com/ibmendoza/msgq"
)

//Example:
//pullpipeline -ip tcp://192.168.0.150:65000

var ip = flag.String("ip", "", "IP address")

func main() {
	flag.Parse()

	if *ip == "" {
		log.Fatal("ip address required")
	}

	pullpipeline, err := msgq.NewPullPipeline(*ip)
	if err != nil {
		log.Fatal("Error creating pullpipeline socket")
	}

	err = msgq.Listen(pullpipeline, *ip)
	if err != nil {
		log.Fatal("Error listening to pullpipeline socket")
	}

	for {
		msg, e := msgq.Receive(pullpipeline)
		if e != nil {
			log.Println("Error receiving message from pullpipeline socket")
		}

		fmt.Println(string(msg))
	}
}
```

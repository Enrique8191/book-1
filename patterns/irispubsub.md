### Iris Publish/Subscribe

Unlike NSQ, Iris publisher clients need to have running subscriber clients on the other end (otherwise messages will be lost). Iris is 
an in-memory message transport with no persistence.

Iris Publish Client

```go
//irispub.go

package main

import (
	"flag"
	"fmt"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
	"math/rand"
	"time"
)

var letters = []rune("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ")
var numbPtr = flag.Int("msg", 100, "number of messages (default: 100)")

func randSeq(n int) string {
	b := make([]rune, n)
	for i := range b {
		b[i] = letters[rand.Intn(len(letters))]
	}
	return string(b)
}

func main() {
	flag.Parse()

	conn, err := iris.Connect(55555)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	} else {
		log.Println("Connected to port 55555")
	}

	start := time.Now()

	for i := 1; i <= *numbPtr; i++ {
		conn.Publish("test", []byte(randSeq(320)))
	}

	elapsed := time.Since(start)
	log.Printf("Time took %s", elapsed)

	defer conn.Close()

	fmt.Scanln()
}
```

Iris Subscriber Client

```go
//irissub.go

package main

import (
	"flag"
	"fmt"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
	"runtime"
	"sync/atomic"
	"time"
)

type topicEvent struct{}

var start = time.Now()
var ops uint64 = 0
var numbPtr = flag.Int("msg", 100, "number of messages (default: 100)")

func (t topicEvent) HandleEvent(event []byte) {

	log.Println(string(event))

	atomic.AddUint64(&ops, 1)
	if ops == uint64(*numbPtr) {
		elapsed := time.Since(start)
		log.Printf("Time took %s", elapsed)
	}
}

func main() {

	flag.Parse()

	conn, err := iris.Connect(55555)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	} else {
		log.Println("Connected to port 55555")
	}

	var topicHandler = new(topicEvent)

	conn.Subscribe("test", topicHandler, &iris.TopicLimits{
		EventThreads: runtime.NumCPU(),
		EventMemory:  64 * 1024 * 1024,
	})

	defer conn.Close()

	fmt.Scanln()
}
```

## NSQ Scenarios


### Scenario 3: Scenario 2 + run nsq consumer on the same node

```go
package main

import (
	"flag"
	"fmt"
	"github.com/itmarketplace/go-queue"
	"github.com/nsqio/go-nsq"
	"log"
	"runtime"
)

func main() {

	flag.Parse()

	c := queue.NewConsumer("test", "ch")

	c.Set("nsqd", ":4150")
	c.Set("concurrency", runtime.GOMAXPROCS(runtime.NumCPU()))
	c.Set("max_attempts", 10)
	c.Set("max_in_flight", 150)
	c.Set("default_requeue_delay", "15s")

	c.Start(nsq.HandlerFunc(func(msg *nsq.Message) error {
		log.Println(string(msg.Body))
		return nil
	}))

	fmt.Scanln()

	c.Stop()
}
```

By default, nsqd listens on port 4150. Running nsqd on a port other than 4150 requires setting it accordingly at the nsq consumer.

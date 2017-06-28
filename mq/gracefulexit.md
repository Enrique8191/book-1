## Graceful Exit of NSQ Consumer

So far in our examples, we use ```fmt.Scanln()``` to simulate a long-running process.

In practice, we need to be more robust so we need to use [signals](https://gobyexample.com/signals).

```go
package main

import (
	"flag"
	"github.com/itmarketplace/go-queue"
	"github.com/nsqio/go-nsq"
	"log"
	"os"
	"os/signal"
	"runtime"
	"sync/atomic"
	"syscall"
	"time"
)

var start = time.Now()
var ops uint64 = 0
var numbPtr = flag.Int("msg", 10, "number of messages (default: 10)")
var lkp = flag.String("lkp", "", "IP address of nsqlookupd")

func main() {

	sigChan := make(chan os.Signal, 1)
	signal.Notify(sigChan, syscall.SIGINT, syscall.SIGTERM)

	flag.Parse()

	c := queue.NewConsumer("test", "ch")

	c.Set("nsqlookupd", *lkp+":4161")
	c.Set("concurrency", runtime.GOMAXPROCS(runtime.NumCPU()))
	c.Set("max_attempts", 10)
	c.Set("max_in_flight", 150)
	c.Set("default_requeue_delay", "15s")

	c.Start(nsq.HandlerFunc(func(msg *nsq.Message) error {
		atomic.AddUint64(&ops, 1)
		if ops == uint64(*numbPtr) {
			elapsed := time.Since(start)
			log.Printf("Time took %s", elapsed)
		}

		//log.Println(string(msg.Body))
		return nil
	}))

	//fmt.Scanln()
	for {
		select {
		case <-sigChan:
			c.Stop()
			os.Exit(1)
		}
	}
}
```

<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

## Run nsqd on one node and nsqlookupd on another

nsqlookupd on first node (192.168.56.101) 

```
nsqlookupd
```

nsqd on second node (192.168.56.102)

```
nsqd --lookupd-tcp-address=192.168.56.101:4160 --broadcast-address=192.168.56.102
```

Notes:

- The flag --lookupd-tcp-address may be given multiple times, meaning you can issue it on the command line and point
to multiple instances of nsqlookupd

**Example:**

Given the scenario above, we can have nsq producer on the same node as nsqd and nsq consumer on yet another node (192.168.56.103).

```go
//NSQ Producer Client - producer.go

package main

import (
    "flag"
    "fmt"
    "github.com/ibmendoza/go-lib"
    "github.com/nsqio/go-nsq"
    "log"
    "math/rand"
    "time"
)

var letters = []rune("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!@#$%^&*()1234567890")
var numbPtr = flag.Int("msg", 10, "number of messages (default: 10)")

func randSeq(n int) string {
    b := make([]rune, n)
    for i := range b {
        b[i] = letters[rand.Intn(len(letters))]
    }
    return string(b)
}

func main() {
    config := nsq.NewConfig()

    ipaddr, _ := lib.GetIPAddress()

    w, err := nsq.NewProducer(ipaddr+":4150", config)

    if err != nil {
        log.Fatal("Could not connect")
    }

    flag.Parse()

    start := time.Now()

    for i := 1; i <= *numbPtr; i++ {
        w.Publish("test", []byte(randSeq(320)))
    }

    elapsed := time.Since(start)
    log.Printf("Time took %s", elapsed)

    w.Stop()

    fmt.Scanln()
}
```

Run ```producer```

```go
//NSQ Consumer Client - consumer.go

package main

import (
    "flag"
    "fmt"
    "github.com/itmarketplace/go-queue"
    "github.com/nsqio/go-nsq"
    "log"
    "runtime"
    "sync/atomic"
    "time"
)

var start = time.Now()
var ops uint64 = 0
var numbPtr = flag.Int("msg", 10, "number of messages (default: 10)")
var lkp = flag.String("lkp", "", "IP address of nsqlookupd")

func main() {

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
    fmt.Scanln()
}
```

Run ```consumer -lkp=192.168.56.101```

<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

## NSQ Scenarios


### Scenario 2: Scenario 1 + run nsq producer on the same node

```go
package main

import (
	"flag"
	"fmt"
	"github.com/nsqio/go-nsq"
	"log"
	"math/rand"
	"time"
)

var letters = []rune("abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ!@#$%^&*()1234567890")
var numbPtr = flag.Int("msg", 10, "number of messages (default: 10)")

var ipnsqd = flag.String("ipnsqd", "", "IP address of nsqd")

func randSeq(n int) string {
	b := make([]rune, n)
	for i := range b {
		b[i] = letters[rand.Intn(len(letters))]
	}
	return string(b)
}

func main() {

	flag.Parse()

	config := nsq.NewConfig()
	config.MaxInFlight = 1000

	w, _ := nsq.NewProducer(*ipnsqd+":4150", config)

	start := time.Now()

	var err error
	for i := 1; i <= *numbPtr; i++ {
		err = w.Publish("test", []byte(randSeq(320)))
		if err != nil {
			log.Println(err)
		}
	}

	elapsed := time.Since(start)
	log.Printf("Time took %s", elapsed)

	w.Stop()

	fmt.Scanln()
}
```

Running this program without flags would produce 10 messages.

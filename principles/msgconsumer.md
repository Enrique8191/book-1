<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Message Consumer

The message consumer is the worker process that acts upon the message received from the message queue.

With NSQ, message consumers or workers can be written using Go, Python, JavaScript, Ruby and 
[others](http://nsq.io/clients/client_libraries.html).

It is interesting to note that the message consumer is the last stop in messaging workflow. However, this is the point where the message consumer can become an endpoint to your **business** workflow.

To illustrate, a message consumer can do anything:

- write to relational or non-relational database
- connect to an HTTP or HTTP2 server
- source of stream data
- write to a file system
- connect to another NSQ cluster
- etc (anything goes)

The bottomline is: the message consumer can do whatever you want however you want!

You can use whatever message transport you want (JSON, gRPC, etc). You can pull data (imperative) or push data (reactive). Of course, the message consumer needs to take into account the fallacies of distributed computing.

**Developing NSQ consumer clients**

Since we are using NSQ, here are some guidelines sourced from the official documentation.

- Because typical deployments of NSQ are in distributed environments with many producers and consumers, the client library should automatically add jitter based on a random percentage of the configured value. This will help avoid a thundering herd of connections.

One such package in Go is https://github.com/jpillora/backoff (example below).

Enabling Jitter adds some randomization to the backoff durations. See Amazon's [writeup](http://www.awsarchitectureblog.com/2015/03/backoff.html) of performance gains using jitter. Seeding is not necessary but doing so gives repeatable results.

```go
import "math/rand"

b := &backoff.Backoff{
    Jitter: true,
}

rand.Seed(42)

fmt.Printf("%s\n", b.Duration())
fmt.Printf("%s\n", b.Duration())
fmt.Printf("%s\n", b.Duration())

fmt.Printf("Reset!\n")
b.Reset()

fmt.Printf("%s\n", b.Duration())
fmt.Printf("%s\n", b.Duration())
fmt.Printf("%s\n", b.Duration())
```

Read more [here](http://sethammons.com/post/pester/).

Other packages you may find useful:

- golang.org/x/net/context
- https://github.com/sethgrid/pester (for HTTP-based consumers)
- https://github.com/mreiferson/go-httpclient
- https://github.com/jpillora/backoff
- https://github.com/eapache/go-resiliency

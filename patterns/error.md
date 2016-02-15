<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Error Handling

Let me repeat the various scenarios how distributed systems can be deployed:

**local computing (aka single-node computing)**

- stand-alone programs (intra-process)

Errors are easily handled since they are local from the perspective of the program. In Go, you check if the error is not nil and you handle it accordingly. You can even pass that error as value so it can be processed by upstream handler.

**distributed computing**

- two or more programs as separate processes (inter-process)

If process A fails, there must be a process external to process A that will catch and relay the error to another process handler. With [Iris](https://godoc.org/gopkg.in/project-iris/iris-go.v1),  if process A fails, the Iris relay will catch the error and your Iris app needs to check and handle that error. Since Iris is peer-to-peer, we need to distinguish if the error happens locally or remotely.

In diagram, an Iris cluster of two processes (or Iris apps) residing on different nodes:

On one node, 

processA --> local Iris relay A

On another node,

processB --> local Iris relay B

Since Iris is peer-to-peer, the actual communication takes place between Iris relays A and B.

In the above example, processA needs to check if an error happens locally (for example, network partition) or remotely (perhaps processB cannot access a separate service across a network).

```go
_, err := conn.Request("cluster", request, timeout)
switch err {
  case nil:
    // Request completed successfully
  case iris.ErrTimeout:
    // Request timed out
  case iris.ErrClosed:
    // Connection terminated
  default:
    if _, ok := err.(*iris.RemoteError); ok {
      // Request failed remotely
    } else {
      // Requesting failed locally
    }
}
```

To manage errors, distributed systems employ timeouts. Regardless if processes reside within the same node or across nodes, the **important** thing is that there is an external process that monitors such timeout and forwards it accordingly to appropriate error handlers. In our example, the Iris relay is external with respect to processA or processB.

This is a fundamental idea of distributed computing. Once you integrate timeouts to your applications, you think of various failure modes your application can get into and handle it like a boss!

With HTTP-based apps, you can use ```https://github.com/mreiferson/go-httpclient```.

```go
transport := &httpclient.Transport{
    ConnectTimeout:        1*time.Second,
    RequestTimeout:        10*time.Second,
    ResponseHeaderTimeout: 5*time.Second,
}
defer transport.Close()

client := &http.Client{Transport: transport}
req, _ := http.NewRequest("GET", "http://127.0.0.1/test", nil)
resp, err := client.Do(req)
if err != nil {
    return err
}
defer resp.Body.Close()
```

For TCP-based apps, you can rely on Iris as the basic equivalent of [Erlang OTP](http://armstrongonsoftware.blogspot.com/2008/05/road-we-didnt-go-down.html) to handle error propagation. It's because Iris is peer-to-peer which sees the overall state of the nodes in a cluster which in essence serves as the distributed runtime for the system.

In short, Iris applications regardless whether it is a client or server (or rather, consumer or producer) are linked via the Iris overlay network of relays.

Moreover, the request/reply pattern of Iris which handles timeouts also double as load balancer.

To sum it up, with IrisMQ, NSQ handles the concurrency aspect of distributed computing while Iris handles error propagation.

**Related Links**

- [Turn expected exceptions into metrics](http://yellerapp.com/posts/2015-06-01-getting-to-exception-zero.html)

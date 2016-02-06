<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Cluster in a Node

A Go package from Facebook called [grace](https://github.com/facebookgo/grace) lets you build two or more HTTP-based or net-based servers in a single node (physical or virtual machine) and link them accordingly such that should one server fails, the cluster won't lose the connection.

Below is a verbatim copy from its GitHub repo.

Package grace provides a library that makes it easy to build socket 
based servers that can be gracefully terminated & restarted (that is,
without dropping any connections).

It provides a convenient API for HTTP servers including support for TLS,
especially if you need to listen on multiple ports (for example a secondary
internal only admin server).  Additionally it is implemented using the same API
as systemd providing [socket
activation](http://0pointer.de/blog/projects/socket-activation.html)
compatibility to also provide lazy activation of the server.


**Usage**


Demo HTTP Server with graceful termination and restart:
https://github.com/facebookgo/grace/blob/master/gracedemo/demo.go

1. Install the demo application

        go get github.com/facebookgo/grace/gracedemo

1. Start it in the first terminal

        gracedemo

   This will output something like:

        2013/03/25 19:07:33 Serving [::]:48567, [::]:48568, [::]:48569 with pid 14642.

1. In a second terminal start a slow HTTP request

        curl 'http://localhost:48567/sleep/?duration=20s'

1. In a third terminal trigger a graceful server restart (using the pid from your output):

        kill -USR2 14642

1. Trigger another shorter request that finishes before the earlier request:

        curl 'http://localhost:48567/sleep/?duration=0s'


If done quickly enough, this shows the second quick request will be served by
the new process (as indicated by the PID) while the slow first request will be
served by the first server. It shows how the active connection was gracefully
served before the server was shutdown. It is also showing that at one point
both the new as well as the old server was running at the same time.


**Documentation**


`http.Server` graceful termination and restart:
https://godoc.org/github.com/facebookgo/grace/gracehttp

`net.Listener` graceful termination and restart:
https://godoc.org/github.com/facebookgo/grace/gracenet

```go
// Command gracedemo implements a demo server showing how to gracefully
// terminate an HTTP server using grace.
package main

import (
	"flag"
	"fmt"
	"net/http"
	"os"
	"time"

	"github.com/facebookgo/grace/gracehttp"
)

var (
	address0 = flag.String("a0", ":48567", "Zero address to bind to.")
	address1 = flag.String("a1", ":48568", "First address to bind to.")
	address2 = flag.String("a2", ":48569", "Second address to bind to.")
	now      = time.Now()
)

func main() {
	flag.Parse()
	gracehttp.Serve(
		&http.Server{Addr: *address0, Handler: newHandler("Zero  ")},
		&http.Server{Addr: *address1, Handler: newHandler("First ")},
		&http.Server{Addr: *address2, Handler: newHandler("Second")},
	)
}

func newHandler(name string) http.Handler {
	mux := http.NewServeMux()
	mux.HandleFunc("/sleep/", func(w http.ResponseWriter, r *http.Request) {
		duration, err := time.ParseDuration(r.FormValue("duration"))
		if err != nil {
			http.Error(w, err.Error(), 400)
			return
		}
		time.Sleep(duration)
		fmt.Fprintf(
			w,
			"%s started at %s slept for %d nanoseconds from pid %d.\n",
			name,
			now,
			duration.Nanoseconds(),
			os.Getpid(),
		)
	})
	return mux
}
```

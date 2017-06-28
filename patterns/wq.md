### Work Queues

**Distributing tasks among workers**

Iris can be used as load balancer. To illustrate, consider an Iris cluster like the following:

- reverse proxy listens on port 8080 (HTTP), port 55555 (Iris)
- two HTTP servers at different nodes both listening at port 63450 (HTTP), port 55555 (Iris)

A node is a physical or virtual machine. You may instantiate new VMs with VirtualBox using host-only networking.

Notes:

- First and foremost, NSQ also sports load balancing (see <a href="https://github.com/IrisMQ/book/blob/master/mq/nsqbalancing.md">example</a>)
- I assume you have Turnkey Linux Core 14 VMs
- You can put the Iris executable and RSA key pair to any folder of your choosing
- On VirtualBox > Preferences > Network > Host-only Networks, these are my settings:
- Adapter (IPv4 Address: 192.168.56.1, IPv4 Network Mask: 255.255.255.0)
- DHCP Server (Server Address: 192.168.56.100, Server Mask: 255.255.255.0)
- DHCP Server (Lower Address Bound: 192.168.56.101, Upper Address Bound: 192.168.56.200)
- By default, Iris runs on port 55555


**Objectives**

- Setup an Iris cluster of three VMs using VirtualBox host-only networking
- Run reverse proxy on one VM
- Run identical HTTP servers on two VMs

**Setup an Iris cluster**

- Run three VMs
- On each VM, type ```./iris -net cluster -rsa /home/id_rsa &```
- RSA key pair must be identical on all virtual machines
- On first VM (192.168.56.101), run the reverse proxy. The reverse proxy will accept HTTP requests at port 8080 and forward it to either one of two HTTP servers (load balancing)

**Reverse Proxy (192.168.56.101)**

```go
//reverseproxy.go
// nginx -> :8080 (reverse proxy) -> :63450 (Web app servers)

package main

import (
	"flag"
	"log"
	"net/http"
	"time"

	"github.com/vulcand/oxy/forward"
	"github.com/vulcand/oxy/testutils"
	"gopkg.in/project-iris/iris-go.v1"
)

var cluster = flag.String("cluster", "", "Iris cluster name")
var port = flag.Int("port", 55555, "Iris port")

////https://medium.com/@matryer/the-http-handlerfunc-wrapper-technique-in-golang-c60bf76e6124
func wrapHandlerFunc(c *iris.Connection, f *forward.Forwarder) http.HandlerFunc {

	return func(w http.ResponseWriter, req *http.Request) {

		ip := []byte("ip")
		var ipaddr string
		if reply, err := c.Request(*cluster, ip, time.Second*5); err != nil {
			log.Printf("failed to execute request: %v.", err)
		} else {
			ipaddr = string(reply)
		}

		// let us forward this request to another server
		req.URL = testutils.ParseURI("http://" + ipaddr + ":63450")
		f.ServeHTTP(w, req)
	}
}

func main() {
	flag.Parse()

	conn, err := iris.Connect(*port)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	} else {
		log.Println("Connected...")
	}
	defer conn.Close()

	// Forwards incoming requests to whatever location URL points to, adds proper forwarding headers
	fwd, _ := forward.New()

	redirect := wrapHandlerFunc(conn, fwd)

	// that's it! our reverse proxy is ready!

	// nginx -> :8080 (reverse proxy) -> :63450 (Web app servers)

	s := &http.Server{
		Addr:    ":8080",
		Handler: redirect,
	}
	s.ListenAndServe()
}
```

To run the reverse proxy,

- ```reverseproxy -cluster cluster```

**HTTP servers (192.168.56.102, 192.168.56.103)**

```go
//httpserver.go
package main

import (
	"flag"
	"fmt"
	"log"
	"net/http"
	"runtime"

	"github.com/ibmendoza/go-lib"
	"github.com/julienschmidt/httprouter"
	"gopkg.in/project-iris/iris-go.v1"
)

//iris
type ipEvent struct{}

func (t *ipEvent) Init(conn *iris.Connection) error {
	return nil
}

func (t *ipEvent) HandleBroadcast(msg []byte) {
}

func (t *ipEvent) HandleRequest(req []byte) ([]byte, error) {
	ip, _ := lib.GetIPAddress()
	return []byte(ip), nil
}

func (t *ipEvent) HandleDrop(reason error) {
}

func (t *ipEvent) HandleTunnel(tun *iris.Tunnel) {
}

//iris

func Index(w http.ResponseWriter, r *http.Request, _ httprouter.Params) {
	fmt.Fprint(w, "Welcome!\n")
}

func Hello(w http.ResponseWriter, r *http.Request, ps httprouter.Params) {
	fmt.Fprintf(w, "hello, %s!\n", ps.ByName("name"))
	ip, _ := lib.GetIPAddress()
	fmt.Fprintf(w, "hello, %s!\n", string(ip))
}

var cluster = flag.String("cluster", "", "Iris cluster name")

func main() {
	runtime.GOMAXPROCS(runtime.NumCPU())

	flag.Parse()

	//iris
	_, err := iris.Connect(55555)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	}

	var ipHandler = &ipEvent{}

	_, err = iris.Register(55555, *cluster, ipHandler, nil)
	if err != nil {
		log.Fatalf("failed to register to the Iris relay: %v.", err)
	}
	//iris

	router := httprouter.New()
	router.GET("/", Index)
	router.GET("/hello/:name", Hello)

	log.Fatal(http.ListenAndServe(":63450", router))
}
```
To run the httpserver (on each node),

- ```httpserver -cluster cluster```

**Test the cluster**

- on your Web browser, access the reverse proxy. In our example, type ```http://192.168.56.101:8080/hello/alice``` and refresh it multiple times. You will see that Iris randomly assigns the request to either one of the HTTP servers.

Response:

```
hello, alice!
hello, 192.168.56.103!
```

or 

```
hello, alice!
hello, 192.168.56.102!
```

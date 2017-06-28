### Request/Reply

Request/reply is the most famous messaging pattern in computing. A client makes a request, a server returns a reply (response). The client and server can be intra-process, inter-process, within a network or across networks.

The introductory "Hello world" program is the classic example. The Web is the ubiquitous example.

The request/reply pattern need not be confused with processing.

Remember that computing = input / process / output

The difference between processing and request/reply is that the former does not necessarily return a response while the latter always do (see the process matrix)

The **process matrix** with respect to parameters and response is as follows:

- (0, 0) - no parameter, no response (function with no result)
- (0, 1) - no parameter, with response (function with result)
- (1, 0) - with parameter(s), no response (function with no result)
- (1, 1) - with parameter(s), with response (function with result)

If you have a function with no result, it can be executed asynchronously (fire-and-forget). It is up to you whether to monitor if the function has completed in its entirety. Such monitor can be out-of-band (external) with respect to the running program.

The function with corresponding result is the classic example of request/reply.

However, the response varies depending on languages.

At best, the response can be described in terms of quantity, transport and direction.

**Response Quantity**

- singular
- multiple

**Response Transport**

- pass by value vs pass by reference (intra-process)
- pass raw bytes (inter-process) - examples include JSON, Cap’n Proto, gRPC, etc

**Response Direction**

- pull-based - imperative programming
- push-based - reactive programming (ReactiveX, Vert.x)

**Response Error Handling**

- [errors are values](https://blog.golang.org/errors-are-values) (synchronous)
- errors are timeouts (asynchronous)



Iris Reply Example

```go
//iris-server-reply.go

package main

import (
	"fmt"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
)

type EchoHandler struct{}

func (b *EchoHandler) Init(conn *iris.Connection) error {
	return nil
}

func (b *EchoHandler) HandleBroadcast(msg []byte) {
}

func (b *EchoHandler) HandleRequest(req []byte) ([]byte, error) {
	return req, nil
}

func (b *EchoHandler) HandleDrop(reason error) {
}

func (b *EchoHandler) HandleTunnel(tun *iris.Tunnel) {
}

func main() {
	service, err := iris.Register(55555, "echo", new(EchoHandler), nil)
	if err != nil {
		log.Fatalf("failed to register to the Iris relay: %v.", err)
	}
	defer service.Unregister()

	fmt.Scanln()
}
```

Iris Request Example

```go
//iris-client-request.go

package main

import (
	"fmt"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
	"time"
)

func main() {
	conn, err := iris.Connect(55555)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	} else {
		log.Println("Connected to port 55555")
	}
	defer conn.Close()

	request := []byte("some request binary")
	if reply, err := conn.Request("echo", request, time.Second); err != nil {
		log.Printf("failed to execute request: %v.", err)
	} else {
		fmt.Printf("reply arrived: %v.", string(reply))
	}

	fmt.Scanln()
}
```

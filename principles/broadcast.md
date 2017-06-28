### Iris Broadcast

<img src="https://itjumpstart.files.wordpress.com/2016/02/320px-broadcast-svg.png">

Server Broadcast

```go
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
	log.Println(string(msg))
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

Client Broadcast

```go
package main

import (
	"fmt"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
)

func main() {
	conn, err := iris.Connect(55555)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	} else {
		log.Println("Connected to port 55555")
	}
	defer conn.Close()

	broadcast := []byte("broadcast message")
	if err := conn.Broadcast("echo", broadcast); err != nil {
		log.Printf("failed to execute broadcast: %v.", err)
	} else {
		fmt.Println("broadcast successful")
	}

	fmt.Scanln()
}
```

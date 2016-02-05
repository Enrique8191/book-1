<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### RPC with Iris

In our very basic example, we have only one API or procedure call named "jwt". Doing RPC with Iris is a two-step process:

(1) Register your service (API, procedure or function) <br>
(2) Implement the service handler

Your service may access a database, filesystem, process a complex algorithm etc. Just remember that an RPC has a timeout so you are likely to put tasks that are not time-intensive. Also, you can run multiple identical services so you can take advantage of Iris load balancing.

**RPC server**

```go
//irisrpc-server

package main

import (
	"fmt"
	"github.com/ibmendoza/jwt"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
)

type JwtHandler struct{}

func (b *JwtHandler) Init(conn *iris.Connection) error {
	return nil
}

func (b *JwtHandler) HandleBroadcast(msg []byte) {
}

func (b *JwtHandler) HandleRequest(req []byte) ([]byte, error) {
	claims := make(map[string]interface{})
	claims["sub"] = "1234567890"
	claims["name"] = "John Doe"
	claims["admin"] = true
	claims["exp"] = jwt.ExpiresInMinutes(15)

	naclKey, err := jwt.GenerateKey()
	fmt.Println(naclKey)

	token, err := jwt.Sign("HS384", claims, "secret", naclKey)
	if err != nil {
		return []byte(""), err
	}

	return []byte(token), nil
}

func (b *JwtHandler) HandleDrop(reason error) {
}

func (b *JwtHandler) HandleTunnel(tun *iris.Tunnel) {
}

func main() {
	service, err := iris.Register(55555, "jwt", new(JwtHandler), nil)
	if err != nil {
		log.Fatalf("failed to register to the Iris relay: %v.", err)
	}
	defer service.Unregister()

	fmt.Scanln()
}
```

**RPC Client**

The sample RPC client below is adapted from Alex Edwards' [example](http://github.com/alexedwards/stack). 

```go
//irisrpc-client

package main

import (
	"fmt"
	"github.com/alexedwards/stack"
	"gopkg.in/project-iris/iris-go.v1"
	"log"
	"net/http"
	"time"
)

var conn *iris.Connection

func main() {
	var err error
	//iris
	conn, err = iris.Connect(55555)
	if err != nil {
		log.Fatalf("failed to connect to the Iris relay: %v.", err)
	} else {
		log.Println("Connected to port 55555")
	}
	defer conn.Close()
	//iris

	stk := stack.New(token, stack.Adapt(language))

	http.Handle("/", stk.Then(final))

	http.ListenAndServe(":3000", nil)
}

//RPC
func fromIris(conn *iris.Connection) string {
	request := []byte("")
	reply, err := conn.Request("jwt", request, time.Second*3)
	if err != nil {
		return ""
	}

	return string(reply)
}

func token(ctx *stack.Context, next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		tokn := fromIris(conn)
		ctx.Put("token", tokn)
		next.ServeHTTP(w, r)
	})
}

func language(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		w.Header().Set("Content-Language", "en-gb")
		next.ServeHTTP(w, r)
	})
}

func final(ctx *stack.Context, w http.ResponseWriter, r *http.Request) {
	token, ok := ctx.Get("token").(string)
	if !ok {
		http.Error(w, http.StatusText(500), 500)
		return
	}
	fmt.Fprintf(w, "Token is: %s", token)
}
```

The advantage of using stack over the native golang context is that the former can hold multiple key-value pairs while the latter can only hold one key-value pair at a time. This saves you the trouble of spawning a new context everytime you need to access a separate backend service.

To illustrate, consider the following diagram courtesy of [Harlow Ward](https://github.com/harlow/go-micro-services):

With stack, you can use ```ctx.Put``` (if necessary) for every ```func (ctx *stack.Context, next http.Handler) http.Handler``` and aggregate the results to the final handler.

<img src="https://itjumpstart.files.wordpress.com/2016/02/harlow.png">

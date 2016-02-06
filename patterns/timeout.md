<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Message Timeout

For messaging pattern like request/reply, it is obligatory to impose message timeout or message expiry. The rationale behind timeout is to enforce a course of action within a certain period of time because it may be not practical to hold on with a transaction or session indefinitely. In the world of distributed systems, you won't know if the other end of the communication encounters hardware failure or network partition. As such, it is important to impose timeout so you can work around accordingly.

One implementation in the world of Web services is [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT). You may opt to use JWT instead of [cookies](https://auth0.com/blog/2014/01/27/ten-things-you-should-know-about-tokens-and-cookies/). The idea here is that, by using message timeout, you can move state to and fro without using [sticky sessions](http://12factor.net/processes). The alternative solution is to use key-value store that offers time expiration like memcached or Redis.

Example of a simple JWT package in Go: https://github.com/ibmendoza/jwt

```go
package main

import (
    "fmt"
    "github.com/ibmendoza/jwt"
    "time"
)

func main() {

    claims := make(map[string]interface{})
    claims["sub"] = "1234567890"
    claims["name"] = "John Doe"
    claims["admin"] = true
    claims["exp"] = jwt.ExpiresInSeconds(3)
    //claims["exp"] = jwt.ExpiresInMinutes(1)
    //claims["exp"] = jwt.ExpiresInHours(1)

    //fmt.Println(timeNow())

    naclKey, err := jwt.GenerateKey()
    fmt.Println(naclKey)

    token, err := jwt.Sign("HS384", claims, "secret", naclKey)
    if err != nil {
        fmt.Println(err)
    }

    fmt.Println(token, err) //encrypted claims

    token2, err := jwt.Sign("HS256", claims, "secret", "")
    if err != nil {
        fmt.Println(err)
    }

    fmt.Println(token2, err) //non-encrypted claims

    timer1 := time.NewTimer(time.Second * 2)
    <-timer1.C

    //fmt.Println(timeNow()) //after 2sec

    fmt.Println(jwt.Verify(token, "secret", naclKey)) //true
    fmt.Println(jwt.Verify(token, "scret", ""))       //false

    fmt.Println(jwt.Verify(token2, "secret", ""))     //true
    fmt.Println(jwt.Verify(token2, "scret", ""))      //false
    fmt.Println(jwt.Verify(token2, "secret", "asdf")) //false
    fmt.Println(jwt.Verify(token2, "secret", ""))     //false

    timer1 = time.NewTimer(time.Second * 2) //after 4secs
    <-timer1.C

    fmt.Println(jwt.Verify(token, "secret", naclKey)) //expired
}
```

With NSQ, it also employs message timeout. When a message is pushed from nsqd to message consumers, there is a default timeout of 60 seconds before the message will be put again on queue (auto-requeue). You can configure this option in nsqd with ```-msg-timeout```. More about the command-line options of nsqd [here](http://nsq.io/components/nsqd.html).

With Iris, the [request/reply](https://godoc.org/gopkg.in/project-iris/iris-go.v1) function is explicit about the timeout.

```go
func (c *Connection) Request(cluster string, request []byte, timeout time.Duration) ([]byte, error)
```

### Concurrency vs Parallelism

Once you understand synchronous and asynchronous processing, it is time to understand concurrency vs parallelism.

To quote Andrew Gerrand from the [Go blog](http://blog.golang.org/concurrency-is-not-parallelism),

"Concurrency is about dealing with lots of things at once. Parallelism is about doing lots of things at once."

But that is abstract so let me quote [Wikipedia](https://en.wikipedia.org/wiki/Concurrent_computation).

"Concurrent computing is a form of computing in which several computations are executing during overlapping time periods -concurrently - instead of sequentially (one completing before the next starts)."

"Parallel computing is impossible on a (single-core) single processor, as only one computation can occur at any instant (during any single clock cycle)"

**Concurrency**

- in Go, your concurrency primitives are goroutines, channels and the select statement

Examples:

CSP

- [Golang](https://golang.org/doc/faq#csp) (used by [go-nsq](https://github.com/nsqio/go-nsq))

Actor-based

- [celluloid](https://github.com/celluloid/celluloid) - used by [krakow](https://github.com/chrisroberts/krakow)
- Erlang VM - based on actors (used by [elixir_nsq](https://github.com/wistia/elixir_nsq))

Event Loop

- Tornado IOLoop - used by [pynsq](https://github.com/nsqio/pynsq)
- [gevent](http://www.gevent.org) - used by [gnsq](https://github.com/wtolson/gnsq)
- NodeJS - used by [nsqjs](https://github.com/dudleycarr/nsqjs)
- libev - used by [libnsq](https://github.com/nsqio/libnsq)
- React PHP - used by [nsqphp](https://github.com/davegardnerisme/nsqphp)


**Parallelism**

- in Go, you can execute two or more instances of the same program in a shared-nothing fashion. That is, they don't talk to each other because each program is independent. Note that parallel programs need not be deployed on multiple nodes. In the case of a Go package from Facebook called grace, you may run multiple instances of identical HTTP servers on different ports on the same physical/virtual machine. That package will take care of graceful failover (hence the name).

So here is the matrix of programs whether they exhibit concurrency or parallelism, or both:

- (0, 0) - not concurrent, not parallelism (single-instance)
- (0, 1) - not concurrent, parallelism (multi-instance)
- (1, 0) - concurrent, not parallelism (single-instance)
- (1, 1) - concurrent, parallelism (multi-instance)

In the case of Go servers, being concurrent means making use of context.

**A Tale of Three Context Packages**

- github.com/alexedwards/stack - can hold many values (```map[string]interface{}```)
- golang.org/x/net/context - can hold only one value at a time (```Value(key interface{}) interface{}```)
- github.com/gorilla/context - holds ```map[*http.Request]map[interface{}]interface{}```

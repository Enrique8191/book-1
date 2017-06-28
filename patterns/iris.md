### Iris Cluster Architecture

Iris has a unified model of combining [two fundamentally different processing modes](https://github.com/IrisMQ/book/blob/master/principles/processing.md) into one API:

- RPC (synchronous request/reply has timeout)
- Messaging (asynchronous patterns like broadcast and publish/subscribe)

In this material, you will learn that messaging and [clustering](https://github.com/IrisMQ/book/blob/master/principles/clustering.md) are just two facets of the same coin.

**Architecture**

Iris is a TCP-based system and Iris applications can be categorized into five:

*RPC*

- client (request)
- service (reply)

Client to service can be one-to-one (1:1) or one-to-many (1:m). In Iris, the latter is automatically load-balanced among many services.

*Messaging*

- producer - generates the message
- consumer - handles the message
- prosumer - producer can be consumer depending on context

**(1) RPC**

[RPC](https://en.wikipedia.org/wiki/Remote_procedure_call) (remote procedure call) is a kind of request/reply. Unlike traditional RPC, Iris is not opinionated. You can use any data transport (JSON, MsgPack, etc). You can use any languages (as long as it implements the Iris protocol). When it comes to clustering, this is the exception because it uses peer-to-peer ([Pastry DHT](https://en.wikipedia.org/wiki/Pastry_%28DHT%29)) to build its overlay network of P2P nodes.

In pure Iris application cluster, the architecture is simple:

Client --> Service (1:1) - one-to-one

Client --> Services (1:m) - load balancing

However in some cases, the Iris clients themselves may need to talk to non-Iris software.

For example,

nginx --> proxy --> Iris client --> Iris service

See [proxy example](https://github.com/IrisMQ/book/blob/master/patterns/wq.md).

**Iris Does Not Need Service Discovery**

For example, if we have a cluster named accounting. Any clients asking for accounting services simply need to invoke the Iris Request API.

```go
func (c *Connection) Request(cluster string, request []byte, timeout time.Duration) ([]byte, error)
```

The request is a raw byte encoding of the following data:

- request API (string) - or method if you like
- optional parameters

The services need to implement the Register API and provide the appropriate response.

```go
func Register(port int, cluster string, handler ServiceHandler, limits *ServiceLimits) (*Service, error)
```

See Iris request/reply [example](https://github.com/IrisMQ/book/blob/master/patterns/irisreqrep.md).

The main ideas of RPC are the following:

- bidirectional - a client makes a request, the service returns a reply (response)
- timeout - you need to indicate timeout because anything that can go wrong will go wrong ([Murphy's Law](https://en.wikipedia.org/wiki/Murphy's_law))


**(2) Messaging**

Messaging is different from RPC because it is asynchronous in nature (fire-and-forget) and push-based. Error handling is done via processes external to the producer or consumer.

The idea of messaging boils down to producer/consumer. A producer generates a message regardless if the consumer is on the same node or across a network.

- [broadcast](https://github.com/IrisMQ/book/blob/master/patterns/irisbroadcast.md)
- [publish/subscribe](https://github.com/IrisMQ/book/blob/master/patterns/irispubsub.md)

**In a Nutshell**

So how come Iris does not need [service discovery](http://jasonwilder.com/blog/2014/02/04/service-discovery-in-the-cloud/) tools?

This is the part where I will explain why messaging and [clustering](https://github.com/IrisMQ/book/blob/master/principles/clustering.md) are just two facets of the same coin.

- In the traditional setup, clustering is based on brokers and wiring the nodes (physical or virtual machines) is based on instance (IP address).

- In the Iris setup, clustering is based on peer-to-peer and the Iris relay daemon takes care of the wiring of nodes **automatically**.

Iris knows the state of the cluster like for example, the location of nodes. It does this by using a distributed hash table (Pastry). When a node joins or leaves the cluster, that information is propagated to other peers in the cluster automatically. Of course, this functionality is comparable to gossip protocols (GP) but let me just say that Pastry DHT vs GP is apples-to-oranges comparison. The former is peer-to-peer whereas the latter is broker-based.

An Iris cluster regardless whether it implements RPC (request/reply) or messaging (publish/subscribe or broadcast) has no distinction for nodes in the cluster. As far as the Iris relay daemon is concerned, all it sees are peers of nodes.

**The concepts of clients and services (for RPC) and producers and consumers (for messaging)** are just constructs in our mind to help us when modeling processes in your respective problem domain.

In diagram,

For RPC, **client --> local Iris relay --> IPv4 network --> local Iris relay --> service**

For messaging, **producer --> local Iris relay --> IPv4 network --> local Iris relay --> consumer**

But as far as Iris is concerned, it is just

**peer --> local Iris relay --> IPv4 network --> local Iris relay --> peer**

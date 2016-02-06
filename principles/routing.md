<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Routing Patterns

**Unicast (Pipeline)**

Unicast is a type of broadcast where a producer sends its message to only one consumer.

<img src="https://itjumpstart.files.wordpress.com/2016/02/320px-unicast-svg.png">

For message producer, you will use the Register command as broadcast handler.

```go
func Register(port int, cluster string, handler ServiceHandler, limits *ServiceLimits) (*Service, error)
```

For message consumer, you will use the Broadcast command to broadcast the message itself.

```go
func (c *Connection) Broadcast(cluster string, message []byte) error
```

In Iris, you can carve out multiple logical or semantic clusters within the Iris cluster itself.

For example, to setup an Iris cluster in development mode:

```
iris -dev
```

Once the Iris cluster is up and running, you can use any cluster name of your choosing for use by both message producer and consumer (of course).

See <a href="https://github.com/IrisMQ/book/blob/master/principles/broadcast.md">Broadcast</a> for example.

**Anycast (Load Balancing)**

<img src="https://itjumpstart.files.wordpress.com/2016/02/320px-anycast-svg.png">

In Iris, the request/reply pattern also doubles as the anycast pattern. The message will be load balanced among multiple consumers based on
the algorithm of the Iris runtime.

It is important to note that the message will go to only one consumer among multiple consumers in the cluster.

See <a href="#requestreply">Request/Reply</a> for example.


**Multicast**

Multicast is a type of broadcast wherein you select a group of two or more consumers in a cluster for message broadcast.

<img src="https://itjumpstart.files.wordpress.com/2016/02/320px-multicast-svg.png">

For example, consider a cluster of 20 computers. Assuming 14 out of those 20 computers were fully updated. You want the remaining 6 computers
to be updated so you get their respective IP addresses and update them accordingly.

With multicast in Iris, you specifically indicate the cluster name in broadcast handler and broadcast connection in server and client respectively.

See <a href="https://github.com/IrisMQ/book/blob/master/principles/broadcast.md">Broadcast</a> for example.

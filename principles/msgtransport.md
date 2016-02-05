<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Message Transport

A message transport is responsible for transporting message (duh) from point A to point B. Behind the scenes, the mechanics is based upon a
protocol but suffice to say, the message transport exposes a minimal API so you can readily use it with your program.

In this guide, we are going to use two message transports.

- [Scalablity Protocols](https://github.com/go-mangos/mangos) for ad-hoc networks

- Iris for [scale-free networks](https://en.wikipedia.org/wiki/Scale-free_network)

<img src="https://itjumpstart.files.wordpress.com/2016/01/scale_free.png">

Download Iris at https://github.com/ibmendoza/project-iris/releases or build your own binary from the source code

Why Iris?

- cluster of servers can be addressed by name, not IP addresses (semantic addressing)
- load balancing without HAProxy (using the request-reply pattern)
- built-in messaging patterns (publish/subscribe, request/reply, broadcast)
- built-in security (SSH)
- no need for service discovery tools like etcd, Consul and the like
- Iris clients can talk to any apps that support the Iris protocol (Go, JVM, Erlang VM) under Linux, Windows and ARMv6
    
We don't use Iris feature called tunnel. With Go, this can be also handled using the package context. Moreover, tunneling is
best served by stateful protocols like MySQL. Instead of tunneling, we can use <a href="https://github.com/IrisMQ/book/blob/master/principles/pipeline.md">**pipeline computing**</a>.

**How to Setup a Cluster**

Since Iris uses [Pastry DHT](https://en.wikipedia.org/wiki/Pastry_%28DHT%29), Iris was designed to build a peer-to-peer cluster using [overlay network](https://en.wikipedia.org/wiki/Overlay_network). It uses the following flags to start up the Iris daemon.

- ```-dev``` - start in local developer mode (random cluster and key)
- ```-port``` - relay endpoint for locally connecting clients
- ```-net``` -name of the cluster to join or create
- ```-rsa``` - path to the RSA private key to use for data security"

- ```-cpuprof``` - path to CPU profiling results
- ```-heapprof``` - path to memory heap profiling results
- ```-blockprof``` - path to lock contention profiling results

**Workflow**

- 1) Start the Iris relay daemon with the required flags (see below). There is a 1:1 correspondence between cluster name and port number.
- 2) Build Iris apps (client and server) by connecting it to port number of Iris daemon.
- 3) For Iris client apps, [request](patterns.html#irisreqrep) and [broadcast](patterns.html#irisbroadcast) require the corresponding cluster name.
- 4) For Iris server apps, you use the [Register](https://godoc.org/gopkg.in/project-iris/iris-go.v1#Register) function

In diagram, this is how Iris clients talk to Iris relay which in turn talks to another Iris relay.

**Iris client app --> Iris (relay A) --> [IPv4 network] --> Iris (relay B) --> Iris server app**

Notes:

- Iris relay A can be on the same node or separate node (physical or virtual machine) as Iris relay B.
- Iris clusters work with IPv4 networks only
- Iris app (client or server) always connect to its local Iris relay daemon
- Iris relay talks to its local Iris app or another remote Iris relay

**For Development**

```iris -dev```

**For Production**

```iris -net clustername -rsa /path_of_id_rsa -port portnumber```

where

- -net = cluster name of your choosing (e.g. accounting, payroll, etc)
- -rsa = ssh-keygen -t rsa (no passphrase)
- -port = 55555 (default). Port range can be up to 65535 only

#### Cluster scenarios (or topologies)

Note: A node refers to a physical or virtual machine

**Scenario 1 (one node - one cluster)**

```iris -net accounting -rsa /home/id_rsa```

**Scenario 2 (one node - one cluster - two or more different ports)**

```iris -net accounting -rsa /home/id_rsa```

```iris -net accounting -rsa /home/id_rsa -port=44444```

**Scenario 3 (one cluster - two or more nodes)**

192.168.56.101

```iris -net accounting -rsa /home/id_rsa```

192.168.56.102

```iris -net accounting -rsa /home/id_rsa```

#### Producer and Consumer

- like NSQ, Iris producer and consumer are also called clients
- producer and consumer at the same time on one node (prosumer)
- producer can be on one node, consumer on another node
- producer and consumer can be deployed based on three above-mentioned scenarios
- prosumer may talk within a cluster or across clusters

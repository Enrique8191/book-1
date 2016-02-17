<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Clustering

*With traditional clustering, you think in terms of IPaddr:port. With Iris clustering, you think in terms of cluster name and port. [Give it five minutes.](https://signalvnoise.com/posts/3124-give-it-five-minutes)*

Any non-trivial computing system has notion of clustering. That is, grouping nodes (physical or virtual machines) that act together as one
collective unit of computing.

For trivial systems, one node with VM engine or a static binary is enough. But that is not what you signed up for.

Examples of clustering include load balancing a group of Web servers which in turn load balances a group of Web application servers behind 
a proxy.

**There are two main methods on how to setup a cluster.**

**First** is old-school IP address-based (or instance-based) clustering.

You can see it almost everywhere from HAProxy, MySQL replication, RethinkDB, Caddy proxy, nginx, etc. At least with Consul, an agent only needs to learn about one existing member.

**Second** is name-based clustering which is being used by Project Iris.

With Iris, you just have to specify the cluster name and its port. It uses Pastry DHT to do its magic.

You can create a cluster in one node (physical or VM) for development purposes.

This is the main difference between Iris and traditional IP address-based methods.

With Iris, the cluster is a unit of service that can be referenced with a simple name. This implies that there is no need for 
[service discovery](http://jasonwilder.com/blog/2014/02/04/service-discovery-in-the-cloud/) tools like etcd, Consul or others.

Why?

When you design your business logic as applications that can be deployed as a unit, there is an explicit ordering of message workflow. You
explicitly specify which cluster your application connects to.

This is not the same as the inherently flawed message ordering. With Iris, your apps flow in a unidirectional manner such that it is based on business requirements.

**Clustering and Messaging**

If you think more about it, clustering and messaging are just two sides of the same coin.

On one side, you setup a cluster either using instance-based or name-based clustering. Either way, the system takes care of the following:

- cluster membership (nodes join or leave)
- cluster state (how many nodes are out there and where they are located) - gossip protocol, peer-to-peer uses DHT

If the cluster needs to maintain its state using a distributed key-value store, it needs to communicate amongst those nodes which is messaging!

- some cluster systems use their own version of RPC, publish/subscribe, etc
- Iris has its own protocol but the advantage is, your apps use one and the same protocol to communicate with the cluster

So while system software each has their own version of messaging, would it be better if we just standardize on just one or two protocols for your business apps communicating within the cluster?

**Bottomline**

With respect to three-tier architecture, we may categorize clustering depending on the logic tier or the database tier.

- Logic Tier Clustering - this refers to either instance-based clustering or Iris clustering (it's your choice) at the business logic or application tier.
- Database Tier Clustering - this is "anything goes". Each respective database has its own way of clustering. Cassandra, RethinkDB, InfluxDB, you name it. You have no control over this except if you are its maintainer!

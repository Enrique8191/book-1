### Overview

I recommend reading Tyler Treat's [article](http://bravenewgeek.com/from-the-ground-up-reasoning-about-distributed-systems-in-the-real-world/) about distributed system.

I won't rehash the content of that article. It is suffice to say that a cursory knowledge about the general principles of distributed system is not enough. 

In short, it is a chicken-and-egg problem.

You won't appreciate the theory if you have no practical experience about it. And you won't appreciate the practical knowledge in this material if you have no clue about the theory.

So to make a long story short, I will not dwell with the deep academic theories. Instead, I will present a high-level overview of distributed system.

**Distributed System**

Distributed system = distributed data + distributed computing

**Distributed Data**

Distributed data is the general term for data in motion and data in storage in a distributed fashion.

A Tale of Two Data


1) Data in motion - refers to distributed data that needs to be managed in a certain context. This is called data in motion because data is collectively in transit to and from a data store.

For example, etcd or Consul needs to manage the state of the cluster. It uses a distributed key-value store to keep minimal data just enough for its operation.

It all boils down to using strong consistency because the situation demands it. In this case, you are trading performance for consistency.



2) Data in storage - collectively refers to relational database shards and non-relational data stores (otherwise known as NoSQL). The former uses strong consistency to maintain the ACID nature of the transaction. The latter uses eventual consistency which relaxes the ACID constraints of a relational database (that is why it is called non-relational)

- Relational database shards
- Key-value store
- Graph
- Columnar-oriented
- Document-oriented

**Distributed Computing**

Distributed computing can be roughly categorized into two based on its consistency requirements.

1) Strong consistency

Strong consistency refers to the state of a certain item or transaction as all-or-nothing, meaning the status is completed (processed) or not (as if nothing happened)

- consensus - leader election (etcd, Consul)
- consensus - state machine replication ([log replication](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying))
- consensus - atomic broadcast (Zookeeper)
- consensus protocols - Paxos, Raft, [Viewstampled Replication](https://brooker.co.za/blog/2014/05/19/vr.html)
- transaction - atomic commit (2-phase-commit)

2) Eventual consistency

Eventual consistency refers to the transient state of a certain item or transaction, meaning the status is yet to be completed (processed) in time (eventually) or be discarded if it has been processed already (idempotence).

a) built-in eventual consistency - the discovery service of NSQ (nsqlookupd) is designed to be eventually consistent. nsqlookupd nodes do not coordinate to maintain state or answer queries. From the official NSQ documentation, consumers eventually find all topic producers

b) manual eventual consistency - this is the custom programming you need to do based on your needs. An example is using the Go package 
[roshi](https://github.com/soundcloud/roshi).

Bottomline: If you are a newbie looking forward as a distributed system developer, don't be discouraged if you have not read the academic
papers. As a matter of fact, it is [optional](https://www.consul.io/docs/internals/consensus.html) unless you want to go deep down the rabbit hole. Leave [complexity](https://twitter.com/itmarketplc/status/678842745209798658) to the experts. As a developer, your job is to code business apps (unless you have considerable ops experience and want to go to the next level).


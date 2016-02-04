IrisMQ is a distributed system toolkit for humans (not machines) based on [Iris](https://github.com/ibmendoza/project-iris) and [NSQ](http://nsq.io)

|      | RPC  | Publish/Subscribe                           |Broadcast     |Clustering       |
|:----:|:----:|:-------------------------------------------:|:------------:|:---------------:|
| NSQ  | No   | Broadcast OR Load Balance among consumers   |      No      |   Shard         |
| Iris | Yes  | Broadcast to all consumers                  |      Yes     |P2P (Pastry DHT) |


**Goals**

- Operate in a heterogeneous SOA
- RPC and asynchronous messaging patterns are first-class citizens
- Pluggable serialization and transport — not just JSON over HTTP
- Operate within existing infrastructures — no mandates for specific tools or technologies

**Non-goals**

- Re-implementing functionality that can be provided by wrapping existing packages
- Having opinions on deployment, orchestration, process supervision, etc.
- Having opinions on configuration passing — flags, env vars, files, etc.

Goals and non-goals are shamelessly copied from [go-kit](http://engineering.dailymotion.com/our-way-to-go) but only because IrisMQ is based on [protocols](https://medium.com/this-is-not-a-monad-tutorial/interview-with-jesper-louis-andersen-about-erlang-haskell-ocaml-go-idris-the-jvm-software-and-b0de06440fbd#), not APIs.

It is [anything goes](https://en.wikipedia.org/wiki/Epistemological_anarchism) the way computing should be.


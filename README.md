IrisMQ is a do-it-yourself toolkit for building microservices based on [Iris](https://github.com/ibmendoza/project-iris) and [NSQ](http://nsq.io)

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

There is nothing fundamentally wrong with go-kit or [micro](https://github.com/micro/micro). They are just fundamentally different from IrisMQ. Hey, this is open source also known as diversity!

IrisMQ favors ["anything goes"](https://en.wikipedia.org/wiki/Epistemological_anarchism) the way computing should be. You can use any languages, libraries or packages you want as long as they implement NSQ and/or Iris protocols.

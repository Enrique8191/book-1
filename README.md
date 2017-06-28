Before anything else, read ["How to build stable systems"](https://medium.com/@jlouis666/how-to-build-stable-systems-6fe9dcf32fc4#) from Jesper Andersen and Segment's experience with [NSQ](https://segment.com/blog/scaling-nsq)

IrisMQ Guide Author: [Isagani Mendoza](http://twitter.com/ibm2100) <br>

Acknowledgment: <br>

- [Iris](https://github.com/project-iris) - [Péter Szilágyi](https://github.com/karalabe)
- [NSQ](https://github.com/nsqio) - [Matt Reiferson](https://github.com/mreiferson), [Jehiah Czebotar](https://github.com/jehiah)
- To Iris and NSQ community

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

There is nothing fundamentally wrong with go-kit or [micro](https://github.com/micro/micro). They are just fundamentally different from IrisMQ. Hey, this is open source so you are more than welcome to embrace diversity!

IrisMQ favors ["anything goes"](https://en.wikipedia.org/wiki/Epistemological_anarchism) the way computing should be. You can use any languages, libraries or packages you want as long as they implement NSQ and/or Iris protocols.

In essence, you can adapt code from go-kit, micro and thousands of other packages and [weave](https://en.wikipedia.org/wiki/Ploceidae) them according to your purpose.

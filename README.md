# IrisMQ Book

IrisMQ is a microservices architecture that combines [NSQ](http://nsq.io) and [Project Iris](https://github.com/ibmendoza/project-iris) to stop [API sprawl](https://itjumpstart.wordpress.com/2016/01/04/the-road-to-computing) and use the programming language you want (as long as it supports NSQ and Iris protocols) however you want. Bring your own libraries, modules, packages, you name it.

To quote [Jesper Louis Andersen](https://medium.com/this-is-not-a-monad-tutorial/interview-with-jesper-louis-andersen-about-erlang-haskell-ocaml-go-idris-the-jvm-software-and-b0de06440fbd#),

> Protocols are far better than APIs because they invite multiple competing implementations of the same thing. They debug themselves over time by virtue of non-interaction between peers. And they open up the design space, rather than closing it down. In a distributed world, we should not be slaves to API designs by large mega-corporations, but be masters of the protocols.

IrisMQ does not care what frameworks or libraries you use. It's your choice. It's anything goes the way computing should be.

#### Supported [client libraries](http://nsq.io/clients/client_libraries.html) for NSQ:

- HTTP
- Go
- Python
- JavaScript
- Ruby
- Java
- Erlang
- PHP
- C
- .NET
- Haskell
- Perl
- Scala
- Clojure

#### Supported [client libraries](https://github.com/project-iris/iris) for Iris:

- Go
- Erlang
- Java
- Scala
- build your own here

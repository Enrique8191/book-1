IrisMQ = [Iris](https://github.com/ibmendoza/project-iris) and [NSQ](http://nsq.io)

|      | RPC  | Publish/Subscribe                           |Broadcast     |Clustering       |
|:----:|:----:|:-------------------------------------------:|:------------:|:---------------:|
| NSQ  | No   | Broadcast OR Load Balance among consumers   |      No      |   Shard         |
| Iris | Yes  | Broadcast to all consumers                  |      Yes     |P2P (Pastry DHT) |


The purpose of software is to abstract operations for developers while making it ops-friendly post-development. While software strives to be a black box as much as possible, it does not mean you cannot peek inside how it works. NSQ and Iris are two fine examples of software because they are based on protocols. 

To quote [Jesper Louis Andersen](https://medium.com/this-is-not-a-monad-tutorial/interview-with-jesper-louis-andersen-about-erlang-haskell-ocaml-go-idris-the-jvm-software-and-b0de06440fbd#),

"Protocols are far better than APIs because they invite multiple competing implementations of the same thing. They debug themselves over time by virtue of non-interaction between peers. And they open up the design space, rather than closing it down. In a distributed world, we should not be slaves to API designs by large mega-corporations, but be masters of the protocols."

Of course, you cannot evade the bullet since your choice of programming language has an API of its own. What I am saying is, whatever is your choice, you need to build on top of protocols using your favorite programming language. That is because software development is hard. You do not need to reinvent the wheel unless you have substantial operations experience to back it up or you are competent enough to upend the status quo.

This is a phenomenon that will persist as long as software is still on its process to maturity. Until software becomes invisible, we are going to wrestle with choices.

For now, let me say that we have two weapons to combat this phenomenon.

First, the name of the game is usability. If your software is not easy to use, the markets will punish you.

Second and related to usability is minimalism. That is, while system operations may be complicated, software at least must expose a minimal interface. If you have 100 flag options for your CLI-based software, it is time to refactor your software. If using your software entails 100-step workflow, it is time to overhaul its design. If you have over 100 pages for documentation, it's time to trim it. If your software needs technical support, there is no incentive to free your customers.

It is time to break this shackle of API slavery.

Iris and NSQ don't care whatever is your favorite programming language or libraries. It's your choice. It's anything goes the way computing should be.

It's your call.

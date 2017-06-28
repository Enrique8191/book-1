### Publish/Subscribe

In the world of Amazon Web Services, you have AWS Lambda. In the world of Iron.io, you have IronWorker.

In the private world within your LAN, you have message workers. The only difference here is that AWS Lambda is integrated with other AWS services. And Iron.io runs in multiple cloud providers.

But the basic idea remains the same. See publish/subscribe example <a href="https://github.com/IrisMQ/book/blob/master/mq/nsqbroadcast.md">here</a> (aka broadcast).

To illustrate, consider the following:

**Message Producer**

- with AWS Lambda, an event happened. The same thing with Iron.io
- with NSQ, when an event happened, you publish a topic and its corresponding payload

**Message Queue**

- with AWS Lambda, when an event happened, it is stored within AWS distributed message queue. With Iron.io, the same thing
- with NSQ, the topic and its payload are stored in its message queue

**Message Consumer**

- with AWS Lambda, you upload your code. With Iron.io, you upload your container
- with NSQ, you upload your message worker as single binary or script

In addition, you can develop your message consumers (workers) in the following languages:

- Go ([go-nsq](https://github.com/nsqio/go-nsq), [nsqueue](https://github.com/crackcomm/nsqueue))
- Python (https://github.com/nsqio/pynsq)
- Ruby (https://github.com/chrisroberts/krakow)
- Elixir (https://github.com/wistia/elixir_nsq)
- JavaScript (https://github.com/dudleycarr/nsqjs)
- C (https://github.com/nsqio/libnsq)
- more at http://nsq.io/clients/client_libraries.html

<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Why Messaging?

***Messaging is not an either/or proposition between using peer-to-peer and client/server. It is about when to use which depending on use case***

First and foremost, let me be clear about the term messaging. In this material, I am not talking about messaging in the sense of text messaging, Web sockets, instant messaging and other user-facing communication.

As a matter of fact, I am talking about the opposite: that of messaging which is not part of the user-facing aspect of any process.

So without further ado, you may be wondering why is messaging relevant in your technology stack (amidst the proliferation of software today)?

Databases and the Web comprise the core part of any non-trivial software stack of businesses. It has been that way since the birth of the World Wide Web. Then we persist data to relational databases. Then NoSQL came along and the tech industry is abuzz with Web scale. And yet, messaging has been in the backburner until now.

Why?

Part of the reason is because we are used to think in synchronous fashion. The advent of Node.js demolished that kind of thinking. But still, Node.js just like any language runtime is basically single-node computing. Sure, there is socket-based programming but that is too low level for application developers.

Of course, there have been a lot of prior art out there so what else is new?

Well, this is not about your uncle grandpa's protocols.

<img src="https://itjumpstart.files.wordpress.com/2016/02/unclegrandpa.jpg">

But seriously, why messaging?

Messaging is all about ripping out the asynchronous tasks from the synchronous request/reply of a Web request, and turning them into messages. Of course, processing is not all Web in nature but you get the idea. Any business process which can be scheduled or executed later in the background can be turned into messages. The operative word is asynchronous.

But then again, asynchronous computing has been with us since Node.js and others came along (see [concurrency vs parallelism](https://github.com/IrisMQ/book/blob/master/principles/concurrency.md)). Sure, you can store messages in memory and process it one by one or several messages at once using threads, Web Workers or whatnot. The problem then becomes that of **API sprawl**. If you use JavaScript, you have NodeJS. If you use Python, you have Tornado IOLoop or gevent. If you use Ruby, there is celluloid. If you use Erlang, there is actor-based concurrency built-in with the runtime system.

In a polyglot world of **non-trivial** systems, you can have message producers written in Go, Ruby or Python and have it consumed by Erlang, Elixir or JVM (and all other possible combinations).

That is the **essence** of messaging.

And since we are talking about distributed systems, messaging does not care whether you are using relational databases or not. It does not  care what libraries, packages or modules you use. It does not care what is your favorite message transport (JSON, MsgPack, Thrift, etc). You know what is the best tool for the job depending on your use case. **Messaging does not get in the way of your design or workflow**.

The keyword here is **protocol**. If your messaging is based on protocol (not API), the ecosystem don't provide **plugins**. Instead, the ecosystem comes up with implementations of the protocol based on your favorite programming language.

Later in this material, you will learn how to stop doing clustering using IP addresses. Of course, instance-based clustering is here to stay but that is because it is being imposed by your choice of software. As developers, you only care about building business apps so you better use a couple of protocols like Scalability Protocols for ad-hoc network messaging or Iris for peer-to-peer network, always-on messaging.

Moreover, you will learn why a message queue is the foundation for building microservices.

To sum it up, messaging is not simply about single-node asynchronous processing. Messaging is more about protocols, the building block for distributed systems, that will set you free from the bondage of API slavery.


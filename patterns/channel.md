<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Message Channel

In this material, I have outlined three scenarios how one application can communicate with itself or with another application.

**(1) Stand-alone programs**

In Go, there are built-in primitives where one function can communicate with another asynchronously using goroutines, channels and select statement.

If you want NodeJS-style concurrency using the event loop, you may use [EventBus](https://github.com/asaskevich/EventBus) (which is also being used by [Lantern](https://github.com/getlantern/lantern) desktop)

**(2) Inter-process communication**

- [mangos](https://github.com/go-mangos/mangos) - use this message transport if you want communication with other socket-based programs using direct addressing (IP address)

- [Iris](https://github.com/ibmendoza/project-iris/releases) - use this message transport if you want communication with clusters of servers but using name-based addressing (like DNS but without DNS)

**(3) Message Queue**

- [NSQ](https://nsq.io) - applications communicate with a message queue in the form of either producer or consumer. A message queue serves as a destination of your messages or as an endpoint in your message processing pipeline.


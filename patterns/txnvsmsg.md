<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Transaction vs Message

This is the wisdom of messaging:

If a request needs a response synchronously within arbitrary alloted time, it is a transaction otherwise it is a message (asynchronous).

Classic examples of transactions are ATM withdrawal ([ACID](https://en.wikipedia.org/wiki/ACID)) and user-facing Web page request ([Web service](https://en.wikipedia.org/wiki/Web_service)).

A transaction is an all-or-nothing proposition, that is, the transaction happens in its entirety (no matter how many are the intervening steps) or not at all.

On the other hand, messages are data that serve as input or output for a certain step or processing. That is, messages may be lost in transit (in the event of hardware failure, network partition, etc) but can be re-sent by message producers for re-processing.

This is the reason why message consumers need to implement idempotence, that is, consumers or clients need to filter out duplicate messages.


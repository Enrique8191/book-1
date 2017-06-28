### Fire-and-Forget

Synchronous error handling is only possible with the request/reply pattern so how do you handle errors in asynchronous operations like unicast, anycast, groupcast, broadcast or publish/subscribe? Just as in life, this all boils down to design decisions. From the point of view of the program invoking the asynchronous operation, it is fire-and-forget. Of course, you need to deal with errors but unlike request/reply, you delegate error handling to yet another external process like logs ([syslog](http://jasonwilder.com/blog/2013/07/16/centralized-logging-architecture/), third-party log services, etc).

With a unidirectional message queue like NSQ, the message producer acts in a fire-and-forget manner in that the producer does not care about the message anymore once it reached nsqd. All it cares about is an acknowledgment from the Iris cluster comprising nsqd that the message has been sent successfully.

To illustrate, here is the workflow:

Assuming we have an Iris cluster comprising a message producer and nsqd.

- message producer sends a message to nsqd using Iris request/reply
- nsqd via Iris replies with a success acknowledgment
- message producer sends the next message (and so on and so forth)

On the other hand, once a consumer encounters an error processing the message, it relays an explicit error signal or if the consumer itself fails, the message will be auto-requeued to nsqd after a configurable timeout.

To sum it up, asynchronous operations are not entirely fire-and-forget since you take into account error handling by external process.

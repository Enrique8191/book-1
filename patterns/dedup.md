<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### The Price of Messaging: Deduplication

Deduplication is the process of detecting duplicate messages at the consumer level.

Why? Well, you need to read more about the [principles](https://github.com/IrisMQ/book/blob/master/patterns/principles.md) of distributed systems.

In the case of single-node computing, the message producer and message consumer are on the same node so it is easy for the latter to discard  duplicate messages.

However, with distributed systems where there are multiple message producers and consumers, things can be tricky.

Consider the following: 

- are messages maliciously duplicate (like spam email)?
- are messages deliberately duplicate (text messages can be re-sent)?
- are messages designed to be unique (with UUID)?


In the first two cases, message producers can employ message filters such that the latter 1) discard duplicates and 2) forwards to message queue those messages that gets past the filter.


**Message Filter**

One way to build a message filter is by using a distributed key-value store. Below is the mechanics.

- computes the hash of message content
- checks the hash in a key-value store (preferably in-memory)
- if the hash exists, don't process the message
- if the hash does not exist, forward the message and store the hash to the key-value store
- repeat

It is up to you how long will you store the hash in your distributed key-value store.

**The third case** is interesting. For messages that are designed to be unique, we use UUID or the like. In the parlance of "Enterprise Integration Patterns", this is called **correlation identifier**. In computing, it is simply called **metadata** (data about data).

To illustrate, consider an example from PayPal: IPN or instant payment notification.

**Here is the workflow:**

- once a customer successfully made a payment transaction on a merchant's Web site, the merchant is notified of transaction details via IPN
- the merchant stores in its database all IPN IDs that have been processed already
- the merchant parses the unique IPN ID. If it exists in the database, discard the message
- if the IPN ID does not exist in the database, process the message and signal to PayPal an acknowledgment
- done

**Examples of distributed key-value store:**

- Redis
- Memcached
- Riak
- Onecache

Onecache is a drop-in replacement for memcached so you can use ```https://github.com/bradfitz/gomemcache```. In addition, it is just a single binary so it is a breeze to test it on your computer.

Here is a basic example of using gomemcache.

```go
package main

import (
	"fmt"
	"time"

	"github.com/bradfitz/gomemcache/memcache"
)

func main() {
	mc := memcache.New("127.0.0.1:11211", "127.0.0.1:11212")

	for {
		mc.Set(&memcache.Item{Key: "foo", Value: []byte("my value"),
			Expiration: int32(time.Now().Add(time.Second * 2).Unix())})

		it, err := mc.Get("foo")
		if err != nil {
			fmt.Println(err)
		}

		if err != nil {
			fmt.Println(err)
		} else {
			fmt.Println(string(it.Value))
		}

		time.Sleep(time.Second * 3)
		it, err = mc.Get("foo")

		if err != nil {
			fmt.Println(err)
		} else {
			fmt.Println(string(it.Value))
		}
	}
}
```

You can test a cluster of Onecache nodes on a single machine.

First Onecache instance

```
onecache
```

Second Onecache instance

```
onecache -gossip_port=7947 -port=11212 -join="127.0.0.1:7946"
```

Output from the Go example above:

```
my value
memcache: cache miss
```

Try to kill the first instance of Onecache. You will see that Onecache is still able to set and get request in spite of losing one instance of key-value store.

At the time of writing, Onecache is a side-project but it's great for local testing if your production uses memcached.

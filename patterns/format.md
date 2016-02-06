<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Message Format

- JSON (JavaScript Object Notation)
- BSON ([Binary JSON](http://bsonspec.org/implementations.html))
- [MsgPack](http://ugorji.net/blog/go-codec-primer)
- [Protocol Buffers](http://tleyden.github.io/blog/2014/12/02/getting-started-with-go-and-protocol-buffers/)
- Cap'n Proto - https://github.com/zombiezen/go-capnproto2
- Apache Thrift - https://thrift.apache.org/tutorial/go
- Base64 - https://gobyexample.com/base64-encoding
- Apache Avro - https://github.com/linkedin/goavro

**Instance-based vs Name-based Clustering**

Regardless of your choice of message format, messaging boils down to two types of clustering:

- instance-based - communication by using IP addresses ([gRPC](http://www.grpc.io/posts/principles/), mangos, etc)

- name-based - communication by referencing the name of the cluster (Iris)

Keep it simple. Level up on demand!





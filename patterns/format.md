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





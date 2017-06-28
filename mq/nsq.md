### NSQ Prerequisite

Note: You can use any Linux distro. For this guide, I'm using Turnkey Linux because it is newbie-friendly.
And besides, it is AWS-ready too but there are usage charges when deployed to Amazon Web Services. You have
been forewarned.

- At the time of writing, NSQ version is 0.3.6
- latest version of VirtualBox
- latest version of Go
- LiteIDE (or your favorite Golang IDE)
- Download Turnkey Linux Core 14.0 (turnkey-core-14.0-jessie-amd64.iso) at 
[https://www.turnkeylinux.org/mirrors](https://www.turnkeylinux.org/mirrors)
- Basic knowledge of Linux CLI (mkdir, cd, chmod +x, ls)
- Create Turnkey Linux VM under VirtualBox
- For this guide, we are going to use host-only networking
- Basic understanding of [message queue](https://itjumpstart.wordpress.com/2015/09/12/why-zeromq-hintjens/)

### Overview of NSQ

NSQ - a realtime distributed messaging platform (written in Go)

Architecture:

nsqd <------> nsqlookupd <------> nsq clients

**nsqd** - the daemon that receives, queues, and delivers messages to clients.

- processes messages produced by nsq clients
- registers itself to nsqlookupd
- by default, listens on port 4150 (for TCP clients), 4151 (for HTTP clients)
- receives messages
- queue and re-queue messages
- broadcast topics and channels to nsqlookupd
- nsqd has a built-in Web server (so you can post messages using HTTP)

**nsqlookupd** - the daemon that manages topology information.

- clients query nsqlookupd to discover nsqd producers for a specific topic information
- by default, listens on port 4160 (for TCP clients), 4161 (for HTTP clients)
- must have static IP address as much as possible
- no need for etcd, Consul or Zookeeper
- can run on a node (physical or virtual machine) apart from nsqd

**nsq clients**

- official client libraries in Go, Python, JavaScript
- registers itself to nsqlookupd (for scalability)
- clients are apps that produce or consume messages (can be both at the same time)

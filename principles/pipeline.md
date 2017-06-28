### Pipeline Computing

Before we discuss Project Iris and NSQ, it is important to note the philosophy and workflow for writing messaging-based microservices.

**The Philosophy**

Project Iris and NSQ are both protocol-based software. This is very important because a protocol is one way to stop API sprawl. All software
which follow the protocols simply implement its specification. Of course, there can be two or more implementations written in the same
language but that is completely to be expected.

The far more important issue with protocols is the design. If there is a flaw in the design, the community may contribute patches.

This is the reason why security protocols are designed in the open.

There is no security by obscurity. Only security by design.

Having said that, this is the philosophy behind writing Iris and NSQ-powered apps.

- Distinguish between Web-based (synchronous request/reply) and messaging (asynchronous messaging patterns)
- Bring your own software (language-agnostic)
- Use your own methodology (workflow-agnostic)
- Bring your own libraries or frameworks (library-agnostic)
- Bring your own databases (SQL or NoSQL, it does not matter)
- Designed for physical/virtual machines (porting to containers is another matter)

**The Workflow**

I-P-O-R Model

1. Message Producer (input)
2. Message Transport (Iris)
3. Message Queue (NSQ)
4. Message Consumer (process, output)
5. Message Transport (routing)

Routing

- databases/file system (persistence)
- cache (ephemeral)
- another message queue
- stdout
- where to route the message is really up to you


### Message Producer

Message producer is the point at any program (script or compiled) that produces the data for consumption and comprises the first stage in
messaging workflow.

It is important to note that the messages referred to here are those data that can be processed apart from the synchronous nature of a process 
(for example, a Web page request).

To illustrate, here is one example.

Assume you have a Twitter account and you posted a sample tweet.

> @peter Where are we going tonight? @mary is coming too. #TGIF

In this case, once the Web app parses the tweet, there are three operations that needs to be done.

First, the Web app posts that tweet under your account. This is the context of the synchronous Web request.

Second, @mary is notified of your tweet.

And third, your tweet will be added to the TGIF hashtag.

The last two can be done asynchronously since it is not part of the synchronous Web request.

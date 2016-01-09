# IrisMQ Book

<!-- Begin MailChimp Signup Form -->
<link href="//cdn-images.mailchimp.com/embedcode/slim-081711.css" rel="stylesheet" type="text/css">
<style type="text/css">
	#mc_embed_signup{background:#fff; clear:left; font:14px Helvetica,Arial,sans-serif; }
	/* Add your own MailChimp form style overrides in your site stylesheet or in this style block.
	   We recommend moving this block and the preceding CSS link to the HEAD of your HTML file. */
</style>
<div id="mc_embed_signup">
<form action="//itjumpstart.us3.list-manage.com/subscribe/post?u=c561be27639e38c69e0135458&amp;id=78a6dd3b73" method="post" id="mc-embedded-subscribe-form" name="mc-embedded-subscribe-form" class="validate" target="_blank" novalidate>
    <div id="mc_embed_signup_scroll">
	<label for="mce-EMAIL">Subscribe to our mailing list</label>
	<input type="email" value="" name="EMAIL" class="email" id="mce-EMAIL" placeholder="email address" required>
    <!-- real people should not fill this in and expect good things - do not remove this or risk form bot signups-->
    <div style="position: absolute; left: -5000px;" aria-hidden="true"><input type="text" name="b_c561be27639e38c69e0135458_78a6dd3b73" tabindex="-1" value=""></div>
    <div class="clear"><input type="submit" value="Subscribe" name="subscribe" id="mc-embedded-subscribe" class="button"></div>
    </div>
</form>
</div>

<!--End mc_embed_signup-->

IrisMQ is a microservices architecture that combines [NSQ](http://nsq.io) and [Project Iris](https://github.com/ibmendoza/project-iris) to stop [API sprawl](https://itjumpstart.wordpress.com/2016/01/04/the-road-to-computing) and use the programming language you want (as long as it supports NSQ and Iris protocols) however you want. Bring your own libraries, modules, packages, you name it.

To quote [Jesper Louis Andersen](https://medium.com/this-is-not-a-monad-tutorial/interview-with-jesper-louis-andersen-about-erlang-haskell-ocaml-go-idris-the-jvm-software-and-b0de06440fbd#),

> Protocols are far better than APIs because they invite multiple competing implementations of the same thing. They debug themselves over time by virtue of non-interaction between peers. And they open up the design space, rather than closing it down. In a distributed world, we should not be slaves to API designs by large mega-corporations, but be masters of the protocols.

IrisMQ does not care what frameworks or libraries you use. It's your choice. It's anything goes the way computing should be.

#### Supported [client libraries](http://nsq.io/clients/client_libraries.html) for NSQ:

- HTTP
- Go
- Python
- JavaScript
- Ruby
- Java
- Erlang
- PHP
- C
- .NET
- Haskell
- Perl
- Scala
- Clojure

#### Supported [client libraries](https://github.com/project-iris/iris) for Iris:

- Go
- Erlang
- Java
- Scala
- build your own here

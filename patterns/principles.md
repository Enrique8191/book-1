<script>
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

  ga('create', 'UA-71257746-1', 'auto');
  ga('send', 'pageview');

</script>

### Principles of Distributed System

**Fallacies of Distributed Computing**

By [L Peter Deutsch et al](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing)

- The network is reliable.
- Latency is zero.
- Bandwidth is infinite.
- The network is secure.
- Topology doesn't change.
- There is one administrator.
- Transport cost is zero.
- The network is homogeneous.

**The Twelve Networking Truths**

By [Ross Callon](https://tools.ietf.org/html/rfc1925)

(1)  It Has To Work.

   (2)  No matter how hard you push and no matter what the priority,
        you can't increase the speed of light.

        (2a) (corollary). No matter how hard you try, you can't make a
             baby in much less than 9 months. Trying to speed this up
             *might* make it slower, but it won't make it happen any
             quicker.

(3)  With sufficient thrust, pigs fly just fine. However, this is
        not necessarily a good idea. It is hard to be sure where they
        are going to land, and it could be dangerous sitting under them
        as they fly overhead.

   (4)  Some things in life can never be fully appreciated nor
        understood unless experienced firsthand. Some things in
        networking can never be fully understood by someone who neither
        builds commercial networking equipment nor runs an operational
        network.

   (5)  It is always possible to aglutenate multiple separate problems
        into a single complex interdependent solution. In most cases
        this is a bad idea.

   (6)  It is easier to move a problem around (for example, by moving
        the problem to a different part of the overall network
        architecture) than it is to solve it.

        (6a) (corollary). It is always possible to add another level of
             indirection.

   (7)  It is always something

        (7a) (corollary). Good, Fast, Cheap: Pick any two (you can't
            have all three).

   (8)  It is more complicated than you think.

   (9)  For all resources, whatever it is, you need more.

       (9a) (corollary) Every networking problem always takes longer to
            solve than it seems like it should.

   (10) One size never fits all.

   (11) Every old idea will be proposed again with a different name and
        a different presentation, regardless of whether it works.

        (11a) (corollary). See rule 6a.

   (12) In protocol design, perfection has been reached not when there
        is nothing left to add, but when there is nothing left to take
        away.			 
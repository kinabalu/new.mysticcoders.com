---
layout: post
status: publish
published: true
title: TSSJS Vegas 2009 - Navigating the SOA Minefield
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-03-19T11:00:16.0000Z'
comments: true
---
<a href="http://javasymposium.techtarget.com/html/soa.html#HKeslerSOA" title="Heath Kesler - Navigating SOA Minefield" target="_blank">Heath Kesler</a> was the final presentation of the day that I attended, and covered one of the other hot topics these days, <a href="http://en.wikipedia.org/wiki/Service-oriented_architecture" title="Service-oriented architecture" target="_blank">SOA</a>. Many enterprises have attached to the ideal of a service oriented architecture, but have found issues during implementation that the core team were unable to overcome. Charly^HHeath put together an informative presentation on some of the challenges that present themselves when designing an SOA-based system and/or picking up the pieces of a five alarm fire.

Navigation is the key here, and Heath talks us through some typical on-site with the client problems that he's been involved with during his professional career. Designing or fixing an existing SOA implementation requires some best practices to be discovered and put into place. The best practices discussed for this session:

<ul>
<li><strong>Using an Enterprise Service Bus (ESB)</strong></li>
<li><strong>Asynchronous Processing vs Synchronous Processing</strong></li>
<li><strong>Routing</strong></li>
<li><strong>Translators / Transformations</strong></li>
</ul>
The real-world example that was used as a backdrop for fixing existing implementation problems revolved around an existing system that used a 3rd party as the system of record and added a 45 - 90 second delay in processing. And to top it all off, the processing had to be done synchronously because of the original failure in design. Armed with the a bit of left and right-brain thinking, along with a healthy background in designing scalable SOA systems, the process was broken out and modified to allow for an asynchronous method of update and ensuring that the data model was modified to better suit the objectives the company wanted to achieve. Having a customer wait 90 seconds just to create an account on your system was definitely failing their business goals, and through these changes due to it being asynchronous, control was returned to the customer in negligible time.

I have to give big kudos for Heath, this was his first time leading a session, they moved his presentation from Friday to Wednesday, and basically left him no time to practice, and refine. And he pulled through it giving those in the room a great example of some key elements in refactoring an existing SOA implementation to fix and optimize.

<strong>Be sure to check out our talk on <a href="http://wicket.apache.org/" title="Apache Wicket" target="_blank">Apache Wicket</a> on Friday at 3pm in Breakout Room 1 - If you liked <a href="http://www.mysticcoders.com/blog/2009/03/09/5-days-of-wicket/" title="5 Days of Wicket" target="_top">5 Days of Wicket</a>, youâ€™ll love this in-depth hour and drop us a line if your company needs training in wicket: <a href="mailto:trainings@mysticcoders.com">trainings@mysticcoders.com</a></strong>


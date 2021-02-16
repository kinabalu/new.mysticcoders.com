---
layout: post
status: publish
published: true
title: 'TSSJS Vegas 2009 - Google Web Toolkit: An Introduction'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-03-19T10:00:58.0000Z'
comments: true
---
David Geary's talk on <a href="http://code.google.com/webtoolkit/" title="GWT" target="_blank">Google Web Toolkit</a> was one of the highlights of the day. Oddly enough they held it in the <em>polar room</em> and kept it at a brisk 30 below! Aside from feeling like we were stuck in the North Pole, the presentation was informative and engaging, and David's dry humor left us chuckling (the only warmth we could generate in that room). Have I mentioned that the room was cold?

If you've never heard of GWT, it's yet another framework. This time it's from <a href="http://google.com" title="Google" target="_blank">Google</a> and powers several but definitely not all of their client facing applications. It's basic premise is that "Ajax is hard", and so it does what it must to abstract things away like Javascript and offers awesome productivity boosts by allowing you to code Ajax-based applications almost entirely using Java. And the *really* cool thing is, you can debug your application, by stepping through Java code instead of using something like <a href="http://getfirebug.com/" title="Firebug" target="_blank">Firebug</a> for Javascript. The team has come up with a lot of really innovative new solutions, to old problems. It also allows the programmer that is familiar with idioms like AWT, Swing, and SWT to understand much more rapidly the constructs needed to build a GWT app.

<strong>The five top features that GWT provides:</strong>

<ul>
<li>Application generator for quickstart</li>
<li>Convention over configuration</li>
<li>Instant turnaround after changes</li>
<li>Non-Ajax Ajax</li>
<li>Awesome productivity</li>
</ul>
As GWT is heavily based on the idea that you want most of your interactions with the client to be Ajax based, they have different requirements for the client and server-side portions of code. Everything you code, if its a simple app without the need for external Javascript, will be in Java. The client-side code uses a subset of the Java packages, and allows you to run your app in Hosted mode, or Web mode. In Hosted mode the app runs via the JVM and can be stepped through using the debugger, and in Web mode it compiles your Java code to Javascript for work in a normal browser.

David is obviously a well seasoned presenter and made the process of learning more about GWT engaging. His session was chock full of demo's and actually wrote code during the presentation to show how easy the creation could be. I think given the right circumstances, and a client willing to think outside the box, GWT is something you should add to your skill-set.

<strong>Be sure to check out our talk on <a href="http://wicket.apache.org/" title="Apache Wicket" target="_blank">Apache Wicket</a> on Friday at 3pm in Breakout Room 1 - If you liked <a href="http://www.mysticcoders.com/blog/2009/03/09/5-days-of-wicket/" title="5 Days of Wicket" target="_top">5 Days of Wicket</a>, youâ€™ll love this in-depth hour and drop us a line if your company needs training in wicket: <a href="mailto:trainings@mysticcoders.com">trainings@mysticcoders.com</a></strong>


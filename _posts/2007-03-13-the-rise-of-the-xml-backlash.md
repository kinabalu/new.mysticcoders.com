---
layout: post
status: publish
published: true
title: The Rise of the XML backlash
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2007-03-13T16:20:38.0000Z'
comments: true
---
The writing has definitely been on the wall for some time now.  All the "agile" frameworks out there picking on Java and its love affair with XML had an effect.  More and more projects have sprung up that tout one of their prized features, no XML required.

<a href="http://wicket.sourceforge.net/">Wicket</a> (currently undergoing incubation with Apache) is a good example of a web framework which throws caution to the wind, and has absolutely no XML needed.  We here at Mystic have a lot of love for <a href="http://wicket.sourceforge.net/">Wicket</a> and are actively developing several projects with it currently.  For another example of a no-XML web framework, check out <a href="http://mc4j.org/confluence/display/stripes/Home">Stripes</a>.

<a href="http://www.springframework.org/">Spring</a> is a panacea of functionality for the Java Web developer.  The biggest complaint we've heard, and made ourselves, is the bloated XML required.  Just this week, "Crazy" Bob Lee at Google announced <a href="http://code.google.com/p/google-guice/">GUICE</a>, looking to replace the IoC portions of <a href="http://www.springframework.org/">Spring</a> with an annotation-based approach.  Again, the sans-xml approach in action.

<a href="http://ant.apache.org">Ant</a> has been the build tool of choice for Java developers for many years now.  <a href="http://maven.apache.org/">Maven</a> with the release of version 2 has gained traction as a viable replacement of the beloved Java build tools for web developers.  Our own experience with it has dictated that once you get your pom.xml file in place, leave it alone, the XML is a bit odd to even deal with.  Looking to move into the "no-xml" space is Gosling, which is still in its infancy, but already has several very nice features available.

With all the options available, its good to see teams thinking outside the box and pulling together some interesting new ways to solve problems in web space.

Happy Coding!


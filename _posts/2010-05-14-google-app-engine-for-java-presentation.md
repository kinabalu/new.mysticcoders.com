---
layout: post
status: publish
published: true
title: Google App Engine for Java presentation
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2010-05-14T20:45:25.0000Z'
tags: []
comments: true
---
I gave a talk last night at the <a href="http://ocjug.org" target="_blank">Orange County Java User's Group (OCJUG)</a> on Google App Engine development with the Java platform.  This was the same one we gave at the <a href="http://scandevconf.se" target="_blank">Scandinavian Developer Conference</a> in March.  The whole talk stemmed from a good friend <a href="http://www.eugeneciurana.com" target="_blank">Eugene Ciurana</a> being unable to attend, so I adapted his slides and did the talk.  

One of the aspects I added was a demo showing how to deploy a <a href="http://wicket.apache.org" target="_blank">Apache Wicket</a> based web application to the dev server and then ultimately up to Google App Engine's servers.  In addition, the demo rather than show off the easy-to-use Eclipse plugin opts for using the <a href="http://code.google.com/appengine/downloads.html" target="_blank">App Engine SDK</a> along with Maven to build.  And we also use <a href="http://code.google.com/p/simpleds/" target="_blank">simpleds</a> for persistence in the demo just to show an alternative to the JDO / JPA offerings that Google provides on top of Datastore.

With that, here is a snippet of the Maven pom.xml to grab all the required dependencies:

``` xml
<dependency>
    <groupId>org.extrema-sistemas</groupId>
    <artifactId>simpleds</artifactId>
    <version>0.9</version>
</dependency>

<dependency>
    <groupId>com.google.appengine</groupId>
    <artifactId>appengine-api-1.0-sdk</artifactId>
    <version>1.3.1</version>
</dependency>

<dependency>
    <groupId>asm</groupId>
    <artifactId>asm</artifactId>
    <version>3.1</version>
</dependency>

<dependency>
    <groupId>org.apache.geronimo.specs</groupId>
    <artifactId>geronimo-jta_1.1_spec</artifactId>
    <version>1.1.1</version>
</dependency>

<dependency>
    <groupId>javax.jdo</groupId>
    <artifactId>jdo2-api</artifactId>
    <version>2.3-eb</version>
</dependency>

<dependency>
    <groupId>org.apache.geronimo.specs</groupId>
    <artifactId>geronimo-jpa_3.0_spec</artifactId>
    <version>1.1.1</version>
</dependency>

<dependency>
    <groupId>org.datanucleus</groupId>
    <artifactId>datanucleus-jpa</artifactId>
    <version>1.1.5</version>
</dependency>

<dependency>
    <groupId>org.datanucleus</groupId>
    <artifactId>datanucleus-enhancer</artifactId>
    <version>1.1.4</version>
</dependency>

<dependency>
    <groupId>org.datanucleus</groupId>
    <artifactId>datanucleus-core</artifactId>
    <version>1.1.5</version>
</dependency>

<dependency>
    <groupId>com.google.appengine.orm</groupId>
    <artifactId>datanucleus-appengine</artifactId>
    <version>1.0.5</version>
</dependency>
```

Your mileage certainly may vary on versions, but as of this writing, slamming that into your pom.xml should get you cracking with simpleds for Google App Engine.

And in case you're interested, here's the presentation from last night and March's talk at Scandev.

<a href="http://www.mysticcoders.com/wp-content/uploads/2010/05/Google-App-Engine-Java-HOWTO.pdf"><img src="https://www.mysticcoders.com/wp-includes/images/crystal/document.png" border="0" /></a>&nbsp;-&nbsp;<a href="http://www.mysticcoders.com/wp-content/uploads/2010/05/Google-App-Engine-Java-HOWTO.pdf">Google App Engine for Java</a>

Enjoy!

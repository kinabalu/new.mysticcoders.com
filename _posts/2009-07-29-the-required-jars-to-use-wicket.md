---
layout: post
status: publish
published: true
title: The required jars to use Wicket
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-29T09:57:30.0000Z'
tags: []
comments: true
---
In Java land, we've become very familiar with jarhell, and the associated pain of trying to find every jar required for let's say Hibernate.  The pain involved in this process is greatly reduced by the use of something like <a href="http://maven.apache.org" target="_blank">Maven</a>, and while the initial learning curve sucks, you get into a groove with it.  What about <a href="http://wicket.apache.org" target="_blank">Wicket</a>?<a id="more"></a><a id="more-69"></a>

Here's the dependencies you would need to fulfill, all two of them:

``` xml
        <dependency>
            <groupId>org.apache.wicket</groupId>
            <artifactId>wicket</artifactId>
            <version>1.3.6</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-jcl</artifactId>
            <version>1.4.2</version>
        </dependency>
```

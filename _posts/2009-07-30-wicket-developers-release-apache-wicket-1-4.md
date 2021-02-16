---
layout: post
status: publish
published: true
title: Wicket developers release Apache Wicket 1.4
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-30T20:05:51.0000Z'
tags: []
comments: true
---
The <a href="http://wicket.apache.org" target="_blank">Wicket</a> folks have released the latest incarnation of the framework in <a href="http://wicket.apache.org/apache-wicket-14-takes-type-safety-to-the-next-level.html" target="_blank">Apache Wicket 1.4</a>.  Notable improvements are:

<ul>
<li>Generified IModel interface and implementations increasing type safety in your Wicket applications</li>
<li>Component#getModel() and Component#setModel() have been renamed to getDefaultModel() and setDefaultModel() to better support generified models</li>
<li>The Spring modules have been merged (wicket-spring-annot is now obsolete, all you need is wicket-spring)</li>
<li>Many API's have been altered to better work with Java 5's idioms</li>
<li>Wicket jars are now packaged with metadata that makes them OSGI bundles</li>
</ul>
What are you waiting for?  <a href="http://www.apache.org/dyn/closer.cgi/wicket/1.4.0" target="_blank">Go get it!</a>


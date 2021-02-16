---
layout: post
status: publish
published: true
title: Qwicket - easy boilerplate code for Wicket
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2006-08-21T17:47:47.0000Z'
comments: true
---
<a title="qwicket" href="http://qwicket.sf.net" target="_blank">Qwicket</a> is an easy and fast solution for creating your next project with <a title="Wicket" href="http://wicket.sf.net" target="_blank">Wicket</a>.  Right from the website, you can create an account (if you'd like to save your work as you go), and add beans to your project directly from the UI.

<a title="qwicket 0.4" href="http://sourceforge.net/project/showfiles.php?group_id=171925&amp;package_id=196577&amp;release_id=439770" target="_blank">Qwicket 0.4</a> was just recently released by Justin Lee, and it features some patches I sent over to the project for integration with <a title="Jetty" href="http://jetty.mortbay.org/jetty/index.html" target="_blank">Jetty</a> and <a title="HSQLDB" href="http://www.hsqldb.org/" target="_blank">HSQLDB</a>.  So when you're looking to start your new project with wicket, your options are:

<ul>
<li>Download the wicket dependencies and write all your own boilerplate code</li>
<li>Use the <a title="qwicket" href="http://qwicket.sf.net" target="_blank">Qwicket</a> website, create your domain model and download the boilerplate code.</li>
</ul>
Now with the <a title="jetty" href="http://jetty.mortbay.org/jetty/index.html" target="_blank">Jetty</a> and <a title="hsqldb" href="http://www.hsqldb.org/" target="_blank">HSQLDB</a> additions, all the dependencies needed are handled by the Ant Maven plugin, so you can get to whats important, writing the next web 2.0 app.

Items that are planned for the future: multiple persistence framework selection (<a title="Java Persistence API" href="http://java.sun.com/javaee/overview/faq/persistence.jsp" target="_blank">JPA</a>, <a title="iBatis" href="http://ibatis.apache.org/" target="_blank">iBatis</a>), portlet support, scaffolding, and maybe someone to help with the UI :)

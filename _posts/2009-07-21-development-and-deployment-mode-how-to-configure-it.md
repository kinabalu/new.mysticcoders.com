---
layout: post
status: publish
published: true
title: Development and Deployment Mode- How to configure it
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-21T13:52:39.0000Z'
tags: []
comments: true
---
There are four supplied methods in the <a href="http://wicket.apache.org" target="_blank">Wicket</a> framework for changing your configuration from development to deployment and vice-versa.  The two possible values for this configuration parameter is "development" or "deployment".<a id="more"></a><a id="more-55"></a>

Method one, context-param in web.xml:

``` xml
    <context-param>
        <param-name>configuration</param-name>
        <param-value>development</param-value>
    </context-param>
```

An init-param in the WicketFilter in your web.xml:

``` xml
    <filter>
        <filter-name>wicketFilter</filter-name>
        <filter-class>org.apache.wicket.protocol.http.WicketFilter</filter-class>
        <init-param>
            <param-name>configuration</param-name>
            <param-value>development</param-value>
        </init-param>
        ...
```

A command-line parameter wicket.configuration:

``` shell
    java ... -Dwicket.configuration=development
```

Overriding Application.getConfigurationType() in your Application class:

``` java
    @Override
    public String getConfigurationType() {
        return Application.DEVELOPMENT;
    }
```

In our environments, to reduce headache, we have the configuration in web.xml point to "deployment", and in our Jetty Start class, we pass the command-line parameter locally to make it run in "development" mode.

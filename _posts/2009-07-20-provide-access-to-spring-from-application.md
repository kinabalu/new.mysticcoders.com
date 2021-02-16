---
layout: post
status: publish
published: true
title: Provide access to Spring from Application
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-20T01:33:24.0000Z'
tags: []
comments: true
---
When developing with <a href="http://wicket.apache.org" target="_blank">Apache Wicket</a>, there are times when you won't be able to use wicket-spring to access your bean implementations.  Here is a simple example that you can add to your Wicket Application class to make accessing the context easier<a id="more"></a><a id="more-1712"></a>

``` java

    protected void init() {
        ...
        ServletContext servletContext = super.getServletContext();
        applicationContext = WebApplicationContextUtils.getWebApplicationContext(servletContext);
        ...
    }

    private ApplicationContext applicationContext;


    public Object getBean(String name) {
        if (name == null) return null;

        return applicationContext.getBean(name);
    }
```

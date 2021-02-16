---
layout: post
status: publish
published: true
title: Wicket and Spring
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-20T00:25:43.0000Z'
tags: []
comments: true
---
Wicket makes it very easy to integrate directly with the <a href="http://springframework.org" target="_blank">Spring Framework</a>.<a id="more"></a><a id="more-1711"></a>

In any Component (Page, Panel, etc) to include a Spring bean you would do:

``` java
    @SpringBean
    private MyBean myBean;
```

In your application-specific Application class you would do the following:

``` java
import org.apache.wicket.spring.injection.annot.SpringComponentInjector;

...

   @Override
    protected void init {
        addComponentInstantiationListener(new SpringComponentInjector(this));
        ...
    }
```

If you're using <a href="http://maven.apache.org" target="_blank">Maven</a> for your build management, you would pull in these dependencies assuming wicket 1.3:

``` xml
        <dependency>
            <groupId>org.apache.wicket</groupId>
            <artifactId>wicket-spring</artifactId>
            <version>${wicket.version}</version>
        </dependency>

        <dependency>
            <groupId>org.apache.wicket</groupId>
            <artifactId>wicket-spring-annot</artifactId>
            <version>${wicket.version}</version>
        </dependency>
```

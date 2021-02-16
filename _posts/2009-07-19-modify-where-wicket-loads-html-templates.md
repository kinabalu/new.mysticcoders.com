---
layout: post
status: publish
published: true
title: Modify Where Wicket Loads HTML Templates
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-19T22:53:28.0000Z'
tags: []
comments: true
---
In <a href="http://wicket.apache.org" target="_blank">Apache Wicket</a>, the framework expects the HTML templates to mirror the class-file directory structure.  The example below allows you to define a different path for your HTML files.<a id="more"></a><a id="more-27"></a>

``` java
public class PathStripperLocator extends ResourceStreamLocator {

    public PathStripperLocator() {
    }

    public IResourceStream locate(final Class clazz, final String path) {
        IResourceStream located = super.locate(clazz, trimFolders(path));
        if (located != null) {
            return located;
        }
        return super.locate(clazz, path);
    }

    private String trimFolders(String path) {
        return path.substring(path.lastIndexOf("/") + 1);
    }
}
```

``` java
public class MyApplication extends AuthDataApplication {
    @Override
    protected void init() {
        super.init();
        IResourceSettings resourceSettings = getResourceSettings();
        resourceSettings.addResourceFolder("src/main/webapp"); //this path should be changed
        resourceSettings.setResourceStreamLocator(new PathStripperLocator());
    }
```

<small>(via <a href="http://cwiki.apache.org/WICKET/control-where-html-files-are-loaded-from.html">wicket wiki</a>)</small>

---
layout: post
status: publish
published: true
title: Add Javascript or CSS using a Resource
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-20T00:41:26.0000Z'
tags: []
comments: true
---
A quick howto <small>(via <a href="http://cwiki.apache.org/WICKET/adding-javascript-or-css-using-a-resource.html" target="_blank">wicket wiki</a>)</small> on adding Javascript or CSS to your pages, and having them compressed during the "deployment" cycle automatically by Wicket. So to start with, we need to copy or Javascript or CSS file somewhere in our package hierarchy that we can reference in our Page. For simplicity, we can copy it right next to the HTML file like so:<a id="more"></a><a id="more-38"></a>

```
MyPage.java
MyPage.html
MyPage.js
MyPage.css
```
Then in our Page, we need to reference these items based on the path of our Java file, like so:

``` java
private static final CompressedResourceReference MYPAGE_JS = new CompressedResourceReference(MyPage.class, "MyPage.js");

private static final CompressedResourceReference MYPAGE_CSS = new CompressedResourceReference(MyPage.class, "MyPage.css");
```

This code gives us a ResourceReference that we can add to our page, most use cases to the HTML head element block. To do that in your page:

``` java
add(HeaderContributor.forJavaScript( MYPAGE_JS ));

add(HeaderContributor.forCss( MYPAGE_CSS ));
```

In Wicket 1.4 HeaderContributor.forJavaScript() and HeaderContributor.forCss() are deprecated, you can use the code below:

``` java
add(JavascriptPackageResource.getHeaderContribution(MYPAGE_JS));

add(CSSPackageResource.getHeaderContribution(MYPAGE_CSS));
```

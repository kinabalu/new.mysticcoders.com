---
layout: post
status: publish
published: true
title: Hide Wicket components and use Ajax to unhide later
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2008-09-14T22:55:03.0000Z'
comments: true
---
It is probably already well known, if you're doing Ajax, I might not be popping any big secrets here. But I was reading <a href="http://www.amazon.com/Wicket-Action-Martijn-Dashorst/dp/1932394982?tag=mycoll-20">Wicket in Action</a> tonight, and ran across a very helpful tidbit on how to properly hide a component. If you've constructed a component that you'd like to show, upon an action, you need at least a dummy tag in the dom to attach off of. Add this to that component and you can happily .setVisible(false) and unhide it later.


``` java
setOutputMarkupPlaceholderTag(true);
```

that's it. <a href="http://www.amazon.com/Wicket-Action-Martijn-Dashorst/dp/1932394982?tag=mycoll-20">Wicket in Action</a> is a great read so far, if you're doing any web development, we here at <a href="http://www.mysticcoders.com">Mystic</a> highly recommend picking up a copy.

And if you haven't read Craig's review of Wicket, <a href="/blog/book-review-wicket-in-action">what are you waiting for</a>?

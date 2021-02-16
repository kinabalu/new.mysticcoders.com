---
layout: post
status: publish
published: true
title: Getting Word Count in Java Resource Bundle .properties files
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2006-04-20T18:57:46.0000Z'
tags: []
comments: true
---
With a little help from freenode folks in ##java and #awk .. I've got a one liner which will give you a word count for your resource bundle files:

```
grep -vE '^#' YourResourceBundle.properties | cut -d = -f 2 | wc -w
```

whew.


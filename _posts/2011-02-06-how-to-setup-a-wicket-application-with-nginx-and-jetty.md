---
layout: post
status: publish
published: true
title: How to setup a Wicket application with nginx and Jetty
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2011-02-06T22:46:54.0000Z'
tags: []
comments: true
---
Our signature sample is our <a href="http://www.mysticcoders.com/blog/2009/03/09/5-days-of-wicket/">"5 Days Of Wicket"</a> tutorial app <a href="http://mysticpaste.com">MysticPaste.com</a>.  And recently I decided to migrate the app from being proxied by <a href="http://httpd.apache.org/" target="_blank">Apache</a> to <a href="http://nginx.org">nginx</a>.

We have always backed the pastebin with <a href="http://jetty.codehaus.org" target="_blank">Jetty</a> and this hasn't changed.  Without further adieu here is our config for nginx:

``` xml
server {
  listen  80;
  server_name mysticpaste.com www.mysticpaste.com;

  access_log  /var/log/nginx/mysticpaste.com.access.log;

  location / {
    proxy_pass  http://127.0.0.1:8081/mysticpaste/;
    proxy_pass_header Set-Cookie;
    proxy_pass_header X-Forwarded-For;
    proxy_pass_header Host;
  }

}
```

So far so good.  The app does essentially what we want, hosts in Jetty under the context path /mysticpaste while offering it up on the root path via nginx and our users.  Under this configuration you'll see a slight problem however, in that the cookie path isn't being properly set due to the proxy.  In Apache we would just use <a href="http://httpd.apache.org/docs/current/mod/mod_proxy.html#proxypassreversecookiepath" target="_blank">ProxyPassReverseCookiePath</a>, but this doesn't appear to be an option in nginx anywhere I could find.

Next best thing, add a context-param to our web.xml and change the SessionPath that Jetty uses to be root instead of /mysticpaste.

``` xml
    <context-param>
        <param-name>org.mortbay.jetty.servlet.SessionPath</param-name>
        <param-value>/</param-value>
    </context-param>
```

All works as it did with Apache, and you get some nginx goodness mixed in.

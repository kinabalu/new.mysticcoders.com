---
layout: post
status: publish
published: true
title: Integrating mousetrap.js with Wicket
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2012-11-26T12:58:35.0000Z'
tags: []
comments: true
---
Over the weekend, I came across a neat library <a href="http://craig.is/killing/mice">mousetrap.js</a> used to add key commands to your web application.  It can be used in any browser including:

<ul>
<li>Internet Explorer 6+ (wow)</li>
<li>Chrome</li>
<li>Safari</li>
<li>Firefox</li>
<li>Opera (yay <a href="http://www.theatlantic.com/technology/archive/2012/11/why-is-belarus-the-only-country-where-opera-is-the-most-popular-browser/265406/" target="_blank" rel="nofollow">Belarus!</a>)</li>
</ul>
Basically the way it works, is 

<ol>
<li>add the Javascript library to the page</li>
<li>start binding commands</li>
</ol>
[javascript]
Mousetrap.bind('command+k', function(e) {
  _someFunctionality();
});
[/javascript]

Here's an example of usage in the <a href="http://mysticpaste.com">Mystic Paste</a> code.  We're adding an <a href="http://ci.apache.org/projects/wicket/apidocs/6.0.x/org/apache/wicket/ajax/AbstractDefaultAjaxBehavior.html">AbstractDefaultAjaxBehavior</a> that we're passing to <a href="https://github.com/kinabalu/wicket-mousetrap/blob/master/src/main/java/com/mysticcoders/wicket/mousetrap/Mousetrap.java">Mousetrap</a> and binding the key combination of pressing an 'n' to move the user to the New Paste page.  Within the pastebin, we have the following key commands right now:

<ul>
<li>n - new paste</li>
<li>h - history page
<ul>
<li>left arrow - previous page</li>
<li>right arrow - next page</li>
<li>shift+left arrow - first page</li>
<li>shift+right arrow - last page</li>
</ul>
</li>
</ul>
And here's a snippet of usage:
[java]
final AbstractDefaultAjaxBehavior historyNav = new AbstractDefaultAjaxBehavior() {
    @Override
    protected void respond(AjaxRequestTarget target) {
        throw new RestartResponseException(HistoryPage.class);
    }
};
add(historyNav);

mousetrap.addBind(new KeyBinding().addKeyCombo(&quot;h&quot;), historyNav);
[/java]

We've also done the work of setting up a Maven repository for the library to easily add it to your Wicket code:

[xml]
    &lt;repository&gt;
        &lt;id&gt;mystic-mvn-repo&lt;/id&gt;
        &lt;name&gt;Mystic Github Maven Repository&lt;/name&gt;
        &lt;url&gt;https://raw.github.com/kinabalu/mystic-mvn-repo/master/snapshots&lt;/url&gt;
    &lt;/repository&gt;
    ...
    &lt;dependency&gt;
        &lt;groupId&gt;com.mysticcoders&lt;/groupId&gt;
        &lt;artifactId&gt;wicket-mousetrap&lt;/artifactId&gt;
        &lt;version&gt;0.1-SNAPSHOT&lt;/version&gt;
    &lt;/dependency&gt;
[/xml]

If you'd like to grab it manually to integrate with your code, or just peruse the source you can find it on <a href="https://github.com/kinabalu/wicket-mousetrap">Github wicket-mousetrap</a>.


---
layout: post
status: publish
published: true
title: New functionality in wicket-mousetrap and Mystic Paste
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2012-11-30T16:49:42.0000Z'
tags: []
comments: true
---
On the 26th we released a small component to integrate with the <a href="http://craig.is/killing/mice">mousetrap.js</a> library for key shortcuts with Javascript.  Since then, we've integrated the library into <a href="http://mysticpaste.com">Mystic Paste</a> and have several handy shortcuts available now on the new paste, and history pages.

<h3>New Features</h3>
Our latest update brings global bindings, default bindings, global default bindings, and the ability to define the key event you'd like to listen to for your shortcut.  These functions have afforded us the ability to bind <strong>Cmd+S</strong> (Mac) and <strong>Ctrl+S</strong> (Windows) to "Save a Paste" and ignore the default browser functions, and we can do that while still in the text area.  And we've simplified the reply to paste options, so simply hitting the "Repaste" button, or hitting <strong>"R"</strong> on the keyboard will take you to a form to reply to the original.

<h3>New Bindings</h3>
Global bindings is an <a href="https://gist.github.com/3885446">extension to mousetrap.js</a> that gives you the ability to respond to shortcuts from within form elements on the page.  Making it effectively global no matter where the users focus.  Here's an example of usage:

[java]
getMousetrap().addGlobalBind(new KeyBinding()
    .addKeyCombo(&quot;r&quot;),
    doSomething
);
[/java]

Default bindings is an <a href="https://gist.github.com/3885446">extension to mousetrap.js</a> that overrides the default handling of keyboard shortcuts you define.  It is similar to adding a regular binding in that form fields will ignore the command.  Here's an example of usage:

[java]
getMousetrap().addDefaultBind(new KeyBinding()
    .addKeyCombo(KeyBinding.COMMAND, &quot;s&quot;)
    .addKeyCombo(KeyBinding.CTRL, &quot;s&quot;),
    doSomething
);
[/java]

The only issue with this, is the form fields are going to ignore the command, even though we're overriding the default browser functionality.  So we add one last binding, the default global bind.

[java]
getMousetrap().addDefaultGlobalBind(new KeyBinding()
    .addKeyCombo(KeyBinding.COMMAND, &quot;s&quot;)
    .addKeyCombo(KeyBinding.CTRL, &quot;s&quot;),
    doSomething
);
[/java]

Voila!  Exactly what we need.  And very easy to integrate and useful in your <a href="http://wicket.apache.org">Wicket</a> projects.  And another useful tidbit of how to make all of this integration work smoothly is how to call your Wicket code from Javascript in Wicket 6.

<h3>Calling Wicket 6 from Javascript</h3>
One of the most important features necessary to make our integration successful, is the ability to call Wicket from Javascript.  Our initial integration utilized the new <em>Wicket.Ajax.get</em> call in <a href="http://wicket.apache.org">Wicket 6</a> like so:

[java]
bindCommand.append(&quot;Mousetrap.bind(&quot;)
    .append(&quot;'ctrl+n'&quot;)
    .append(&quot;, function(e) { Wicket.Ajax.get({'u': '&quot;)
    .append(behavior.getCallbackUrl())
    .append(&quot;'}) });
&quot;);
[/java]

And this will give us Javascript code something like the following:

[javascript]
    Mousetrap.bind('ctrl+n', function(e) { Wicket.Ajax.get({'u': '/wicket-ajax-url'}); });
[/javascript]

This works perfectly, for GET-based requests.  And with the help of <a href="https://github.com/martin-g">Martin Grigorov</a> I soon realized if I wanted to POST, it wouldn't work.  So very early in the morning Martin was instrumental in helping me understand the proper way to call Wicket 6 from Javascript, like so:

[java]
bindCommand.append(&quot;Mousetrap.bind(&quot;)
    .append('ctrl+n')
    .append(&quot;, function(e) {&quot;)
    .append(behavior.getCallbackScript())
    .append(&quot;});
&quot;);
[/java]

As with most things Wicket, it helps to review the code and understand more fully what the method achieves.  And when you call getCallbackScript, it interrogates the object it's attached to (in this case a Form), and builds a proper Wicket.Ajax.X call with the appropriate parameters for everything.  That's it.  It's the kind of magic that made me fall in love with Wicket so many years ago.


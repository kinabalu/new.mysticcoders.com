---
layout: post
status: publish
published: true
title: 'IDEA Development: Adding notifications'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-04-22T09:30:23.0000Z'
comments: true
---
Our initial plugin for IDEA that talked to <a href="http://www.mysticpaste.com" target="_blank">MysticPaste.com</a> was fairly simple, offered no feedback to the user that anything happened. I spent some time trying to find out a method of achieving unobtrusive notification that the paste was successful. On initial glance at the API, nothing jumped out except for <em>Messages</em>, and <em>StatusBar</em>.

<a id="more"></a><a id="more-784"></a>
First crack was using <em>Messages</em>:

``` java
    Messages.showInfoMessage("Paste successful!","URL automatically copied to your clipboard");
```

It meets the standard of showing feedback to the end-user, but its definitely obtrusive. The user is forced to click on the OK button, its less of a status and more of a confirmation.

Second crack was using StatusBar. Only thing found in Google is for version 5.0 of the plugin API, and it only has one method, setInfo(String s). Doesn't look very promising. Introspection though, proves that nothing is as it seems. Through some sleuth googling, downloading some plugins that showed the notifications we like, and googling more, a solution was found.

<h2>Adding a Project Component</h2>
There's only one way to get at a <em>StatusBar</em> instance, and that's through the <em>WindowManager</em>. And there isn't a global <em>StatusBar</em>, there's one per Project. So where we got by without a <em>ProjectComponent</em> previously, we need one now. The following snippet gives us a <em>StatusBar</em> object:

``` java
    StatusBar statusBar = WindowManager.getInstance().getStatusBar(project);
```

And from that, we can call fireNotificationPopup with the proper parameters, to show our message. If you happen to find the JavaDoc, congratulations, initial searches came up empty on this method, but it works great. Here's the entire method for the notification, which we add to the event thread so Swing can fire and show it whenever it makes sense:

``` java
    private Project project;

    public MysticPasteIndicationComponent(Project inProject) {
        project = inProject;
    }

    ...

    protected void updateWithStatus(final String statusMessage) {

        /**
         * Don't make Swing angry.  You won't like it when its angry
         *
         */
        ApplicationManager.getApplication().invokeLater(
                new Runnable() {
                    public void run() {
                        JTextArea area = new JTextArea();
                        area.setOpaque(false);
                        area.setEditable(false);
                        StringBuffer notification = new StringBuffer(statusMessage);
                        area.setText(notification.toString());
                        StatusBar statusBar = WindowManager.getInstance().getStatusBar(project);
                        if (statusBar != null)

                        {
                            statusBar.fireNotificationPopup(area, LightColors.GREEN);
                        }
                    }

                }
        );

    }
```

And now all we need is to call this after the paste was successful. So similar to how we grabbed the <em>Editor</em>, we'll again pull out our <em>DataContext</em> object bag of tricks, and find our <em>ProjectComponent</em> instance, and fire off our status update as you can see here:

``` java
    Project project = DataKeys.PROJECT.getData(context);
    MysticPasteIndicationComponent mpiComponent = project.getComponent(MysticPasteIndicationComponent.class);
    mpiComponent.updateWithStatus("Paste successful!nnURL automatically copied to your clipboard");
```

That's it. And we promise, no animals were hurt during this exercise. And DO try this at home.

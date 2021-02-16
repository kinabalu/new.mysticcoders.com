---
layout: post
status: publish
published: true
title: A Gravatar Image component for Wicket
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-11-11T08:35:40.0000Z'
tags: []
comments: true
---
The comment system that was added to <a href="http://mysticpaste.com" target="_blank">mysticpaste.com</a> several months ago was pretty bare, boring.  It consisted of capturing name, email, and the comment of which we show name and comment only.  Since we're avid blog posters and readers, the thought of implementing a custom Wicket component for a <a href="http://gravatar.com" target="_blank">Gravatar</a> immediately came to mind.

First things first, extend our new GravatarImage component from WebComponent.  We don't need the image to hold any nested components, etc:

``` java
public class GravatarImage extends WebComponent {
```

We also override onComponentTag so we can populate the "src" attribute for our image with the Gravatar generated URL:

``` java
    protected void onComponentTag(ComponentTag tag) {
        super.onComponentTag(tag);
        checkComponentTag(tag, "img");
        tag.put("src", getDefaultModelObjectAsString());
    }
```

Next we provide our own Model implementation which performs the MD5 magic on our email address to give its special Gravatar URL pointing to our image.

``` java
    private class GravatarModel extends AbstractReadOnlyModel<string> {
        private static final String GRAVATAR_URL = "http://www.gravatar.com/avatar/";

        String email;
        String gravatarKey;
        int hsize;

        public GravatarModel(IModel<string> model, int hsize) {
            email = model.getObject();
            gravatarKey = MD5Util.md5Hex(email);
            this.hsize = hsize;
        }

        public String getObject() {
            StringBuilder sb = new StringBuilder();
            sb.append(GRAVATAR_URL);
            sb.append(gravatarKey);
            sb.append("?s=");
            sb.append(hsize);
            return sb.toString();
        }
    }
```

And that's it!  Just follow the example given in the javadoc and voila!  You should have Gravatar pictures for the entered email address.  To take a look at the full implementation: <a href="http://kenai.com/projects/mystic-apps/sources/mystic-apps/content/mysticpaste/src/main/java/com/mysticcoders/mysticpaste/web/components/GravatarImage.java?rev=89" target="_blank">Download GravatarImage.java</a>.

<h3>Next steps</h3>
A couple of items can be added to this component to make it more "full-featured" to the Gravatar API.  Gravatar supports a default Gravatar image which may be passed, along with a number of flags one of which is implemented "s" or size.

Hope you enjoyed this, and use this in your next project!

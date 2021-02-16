---
layout: post
status: publish
published: true
title: Using FeedbackPanel with AJAX
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-20T07:58:37.0000Z'
tags: []
comments: true
---
If you'd like to have your FeedbackPanel update with errors in the event of a problem with your form, just adding the FeedbackPanel won't do you any good.  Just as with any other AJAX-updating component in Wicket, you'll need to add it to the AjaxRequestTarget, only difference is, you'll have to do this while overriding onError like so:<a id="more"></a><a id="more-1714"></a>

``` java
final FeedbackPanel feedbackPanel = new FeedbackPanel("feedbackPanel");
feedbackPanel.setOutputMarkupId(true);
form.add(feedbackPanel);
form.add(new AjaxButton("submit") {
    @Override
    protected void onError(final AjaxRequestTarget target, final Form form) {
        target.addComponent(feedbackPanel);
    }
});
```

---
layout: post
status: publish
published: true
title: Add Form Component&#039;s to AjaxTarget with IVisitor
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-20T07:50:16.0000Z'
tags: []
comments: true
---
When writing AJAX-specific code for Wicket, in order to make any updates to a component, it needs to be added to the AjaxTarget.  If you've got a particularly large form, this can get tedious, so use an IVisitor instead!
<a id="more"></a><a id="more-1713"></a>

``` java
@Override
protected void onSubmit(final AjaxRequestTarget target, Form form) {
    form.visitFormComponents(new FormComponent.IVisitor() {
        public Object formComponent(IFormVisitorParticipant
                formComponent) {
            final FormComponent fc = (FormComponent)formComponent;
            target.addComponent(fc);
            return Component.IVisitor.CONTINUE_TRAVERSAL;
        }
    });
    ...
```

And as always, for each component you access via AJAX, you'll need to:

``` java
   component.setOutputMarkupId(true);
```

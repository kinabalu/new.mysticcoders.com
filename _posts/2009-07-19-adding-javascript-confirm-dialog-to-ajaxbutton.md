---
layout: post
status: publish
published: true
title: Adding Javascript confirm dialog to AjaxButton
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-19T20:17:01.0000Z'
tags: []
comments: true
---
If you have an AJAX button in your form, a nice way of adding javascript is to use an IAjaxCallDecorator<a id="more"></a><a id="more-3"></a>

``` java
form.add(new AjaxButton("removeButton") {

    @Override
    protected IAjaxCallDecorator getAjaxCallDecorator() {
        return new AjaxPreprocessingCallDecorator(super.getAjaxCallDecorator()) {
        private static final long serialVersionUID = 1L;

            @Override
            public CharSequence preDecorateScript(CharSequence script) {
                return "if(!confirm('Are you sure you want to delete this?')) return false;" + script;
            }
        };
    }
}
```

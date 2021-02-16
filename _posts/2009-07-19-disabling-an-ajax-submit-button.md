---
layout: post
status: publish
published: true
title: Disabling an AJAX submit button
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-19T20:42:40.0000Z'
tags: []
comments: true
---
A long running process that you'd like to show some indicator of progress or similar, usually means an indicator of some kind.  Here we use an IndicatingAjaxButton to show some progress near the clicked submit button, and we use an IAjaxCallDecorator to disable the submit button so we don't get multiple clicks<a id="more"></a><a id="more-10"></a>

``` java
form.add(new IndicatingAjaxButton("submit", form) {

    @Override
    protected IAjaxCallDecorator getAjaxCallDecorator() {
        return new AjaxPostprocessingCallDecorator(super.getAjaxCallDecorator()) {
            private static final long serialVersionUID = 1L;

            @Override
            public CharSequence postDecorateScript(CharSequence script) {
                return script + "this.disabled = true;";
            }
        };
    }
}
```

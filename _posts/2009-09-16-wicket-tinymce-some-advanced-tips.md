---
layout: post
status: publish
published: true
title: 'Wicket TinyMCE: some advanced tips'
author:
  name: Il Della
  picture: '/assets/blog/authors/ildella.jpg'
  url: https://mysticcoders.com
  email: ildella@gmail.com
date: '2009-09-16T13:04:01.0000Z'
tags: []
comments: true
---
Wicket offers a valid integration with the powerful and open source <a href="http://tinymce.moxiecode.com/">TinyMCE</a>, a Javascript HTML WYSIWYG editor. Wicket-contrib-tinymce is part of the wicketstuff-core and is well done and maintained. Then using TinyMCE within wicket is very easy and the <a href="http://wicketstuff.org/confluence/display/STUFFWIKI/wicket-contrib-tinymce">instructions on the site</a> would be enough to make things work and the example project on the subversion repository will do most of the rest.

<a id="more"></a><a id="more-101"></a>

Here I want to tell something I have learned using this nice toy.

<strong>Use with Maven 2</strong>

To use tinymce in a Maven 2 project, we need to add the wicketstuff maven2 repository:

``` xml
<repository>
	<id>wicket-stuff</id>
	<url>http://wicketstuff.org/maven/repository</url>
</repository>
```

and, of course, the dependency

``` xml
<dependency>
	<groupId>org.wicketstuff</groupId>
	<artifactId>tinymce</artifactId>
	<version>1.4-SNAPSHOT</version>
</dependency>
```

<strong>Creating a CheckBox that toggle TinyMceBehavior on and off.</strong>

In my scenario I need the user to enable Rich Text capabilities for a field in a form. Is nothing complicated but we need to take care about when to instantiate objects. In my solution, I use a checkbox to toggle the state. This is the code I have in the constructor of my panel.

``` java

public class CustomPanel extends Panel {
   private TinyMceBehavior tinyMceBehavior;
   public CustomPanel(String id, IModel model) {
       ...
      TextArea field = new TextArea("text", model);
      tinyMceBehavior = new TinyMceBehavior();
      PropertyModel richProperty = new PropertyModel(model, "rich");
      if (richProperty.getObject()) {
         field.add(tinyMceBehavior);
      }

      add(new AjaxCheckBox("switchRte", richProperty) {

         @Override
         protected void onUpdate(AjaxRequestTarget target) {
            if (getModelObject()) {
               field.add(tinyMceBehavior);
            } else {
               field.remove(tinyMceBehavior);
               tinyMceBehavior = new TinyMceBehavior();
            }
            target.addComponent(CustomPanel.this);
         }
      });
   }
}
```

The information is saved in a property called "rich" in my domain object so it is persisted and I can remember next time. That object is already wrapped in the variable <em>model</em> (a wicket IModel) that in this code is wrapped in a simple wicket PropertyModel. In this example I have used Wicket 1.4 support for generics.

<strong>Switching to Rich Text after the page is load</strong>

One of the problem I had was that my page loaded without any TinyMCEBehavior attached to any component. User can enable the behavior at runtime like in the example before. Well, it did not work because the javascript needs to be load at the time the page is loaded. The solution is that when you have some TinyMCE components in your page, is better that in the constructor of the page you load the Javascript this way:

``` java
add(new HeaderContributor(new IHeaderContributor() {
   public void renderHead(IHeaderResponse response) {
      response.renderJavascriptReference(TinyMCESettings.javaScriptReference());
   }
}));
```

<strong>Subtle trick: submit via Ajax</strong>

Maybe anyone knew this before. I did not. When you submit a form containing a TinyMCE component and the form submits via ajax, say an AjaxSubmitLink or an AjaxButton in Wicket, you *must* use a trick as someone else <a href="http://dwairi.blogspot.com/2006/12/tinymce-ajax.html">already explained</a>. With wicket-tinymce this becomes adding the <em>TinyMceAjaxSubmitModifier</em> to the submit component and that's all.

<strong>Conclusion</strong>

wicket-contrib-tinymce is a well done contribution to the wicketstuff-core and an important library for web developer that uses Wicket. The only small point is that is actually using a one year old version of TinyMCE, dated august 2008. It does the *most* of the stuff and I never felt the need to upgrade. Maybe someone just need some feature or a bug fix present in more recent release. Hope that wicket-tinymce developer will have some time to play with newer TinyMCE.

This is my first contribution to WicketByExample. I hope you liked the article, I will like any feedback. Cheers.
<a href="http://twitter.com/ildella" target="_blank"></a>

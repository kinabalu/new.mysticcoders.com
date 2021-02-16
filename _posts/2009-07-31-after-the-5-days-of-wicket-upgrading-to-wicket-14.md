---
layout: post
status: publish
published: true
title: 'After the 5 Days of Wicket: Upgrading to Wicket 1.4'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-07-31T15:06:30.0000Z'
tags: []
comments: true
---
If you follow anything about Wicket, you know that they just released <a href="http://wicketbyexample.com/wicket-developers-release-apache-wicket-1-4/">Wicket 1.4</a> which offers some very nice improvements and structural changes that make it even more awesome of a framework to work with.  Back in March of this year we brought you <a href="http://www.mysticcoders.com/blog/2009/03/09/5-days-of-wicket/">5 Days of Wicket</a>, so in today's post we upgrade the sample application which is available online at <a href="http://mysticpaste.com">MysticPaste.com</a> to 1.4.<a id="more"></a><a id="more-1094"></a>

<h4>Dependency Changes</h4>
First thing's first, use <a href="http://maven.apache.org">Maven</a>, it's just going to make life a whole lot easier to deal with.  Since the sample application utilized Spring, I'll also include the changes you should make in your pom.xml file for 1.4:

``` xml
<dependency>
     <groupId>org.apache.wicket</groupId>
     <artifactId>wicket</artifactId>
     <version>1.4.0</version>
</dependency>
<dependency>
     <groupId>org.apache.wicket</groupId>
     <artifactId>wicket-ioc</artifactId>
     <version>1.4.0</version>
</dependency>
<dependency>
     <groupId>org.apache.wicket</groupId>
     <artifactId>wicket-spring</artifactId>
     <version>1.4.0</version>
</dependency>
```

The library wicket-spring-annot has been deprecated in favor of wicket-ioc and wicket-spring, and simply modifying the version to 1.4.0 for all wicket-core artifacts should do the trick.

<h4>Configuration</h4>
While this may not cover every case, the only configuration item that was required for us was to change the context-param defining the development/deployment mode:

``` xml
<context-param>
   <param-name>configuration</param-name>
   <param-value>DEPLOYMENT</param-value>
</context-param>
```

becomes

``` xml
<context-param>
   <param-name>wicket.configuration</param-name>
   <param-value>DEPLOYMENT</param-value>
</context-param>
```

<h4>Deprecations ... fare thee well old friends</h4>
In the examples, we use HeaderContributor to references a few CSS files and this has been deprecated in favor of:

``` java
   add(JavascriptPackageResource.getHeaderContribution(MYPAGE_JS));

   add(CSSPackageResource.getHeaderContribution(MYPAGE_CSS));
```

We also utilized the simple DefaultDataProvider for our DataView on the history page, and this too has been axed.  Our solution for this was to just implement IDataProvider ourselves:

``` java
public class HistoryDataProvider implements IDataProvider<PasteItem> {
    ...

    public Iterator<PasteItem> iterator(int first, int count) {
        try {
            return pasteService.getLatestItems("web", count, first, false).iterator();
        } catch (InvalidClientException e) {
            e.printStackTrace();
        }
        return null;
    }

    public IModel<PasteItem> model(PasteItem pasteItem) {
        return new Model<PasteItem>(pasteItem);
    }

    /**
     * @see org.apache.wicket.model.IDetachable#detach()
     */
    public void detach() {
    }
}
```

As with a lot of the framework in 1.4, things have been generified, so instead of casting everywhere, you just generify everywhere =).

<h4>Component Changes</h4>
With Link, you can pass in a Model, and BookmarkablePageLink extends from Link, so if you want your code nice and clean, since it doesn't need it:

``` java
BookmarkablePageLink<Void> myLink = new BookmarkablePageLink<Void>("myWicketId", MyWicketPage.class);
```

Component has also changed, and they have removed the .setModel and .getModel methods in favor of .getDefaultModel and .setDefaultModel.  My understanding is that because IModel was generified, suddenly all components had to be generified, and that would have sucked.  For more info on this <a href="http://cwiki.apache.org/WICKET/migrating-to-wicket-14.html" target="_blank">Wicket's wiki page on Migrating to Wicket 1.4</a>.  Here are a few examples of blocks from the sample app that we modified to support generics:

``` java
   add(new CheckBox("twitter", new PropertyModel<Boolean>(PasteForm.this, "twitter")));

   ...

   add(new TextField<String>("email", new PropertyModel<String>(PasteItemPage.this, "spamEmail")));

   ...

   markAbuseLabel.setDefaultModel(new Model<String>("Marked As Spam"));

   ...

   CommentListModel commentListModel = new CommentListModel(pasteModel.getObject().getId());
   final ListView<PasteComment> commentList = new ListView<PasteComment>("commentList", commentListModel) {
       @Override
       protected void populateItem(final ListItem<PasteComment> item) {         

    ...

    RequiredTextField<String> commentEmail = new RequiredTextField<String>("email");

```

Overall and throughout the code, things seem a bit cleaner, and more pleasant to work with.  If you'd like to take a more in-depth look at the code, you can browse it <a href="http://kenai.com/projects/mystic-apps" target="_blank">on our Kenai project page</a>.  As always we welcome any comments, and if we've made a boo-boo in our conversion, please let us know.  All told it took about 30 minutes to convert the codebase, and since it's small, that makes sense.  If you need more details, please check the ever evolving <a href="http://cwiki.apache.org/WICKET/migrating-to-wicket-14.html" target="_blank">Wicket migration to 1.4</a> on the Wiki.

Happy coding!

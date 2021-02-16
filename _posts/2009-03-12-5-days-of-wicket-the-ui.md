---
layout: post
status: publish
published: true
title: 5 Days of Wicket - The UI
author:
  name: Steve Forsyth
  picture: '/assets/blog/authors/sforsyth.jpg'
  linkedin: /in/steveforsyth
  url: https://mysticcoders.com
date: '2009-03-12T09:00:15.0000Z'
tags: []
comments: true
---
So... you should now have a fairly good understanding of how to put <a target="_blank" href="http://wicket.apache.org/">Wicket</a> together with <a target="_blank" href="http://www.springsource.org/">Spring</a> and <a target="_blank" href="http://www.hibernate.org/">Hibernate</a>, creating your DAOs and services and putting that code through the test gauntlet. We can see that our foundation is rock solid... but we're missing the eye-candy... so let's hop over to the UI and show you where Wicket really shines.
&nbsp;

<!--more-->

# Base Class
Most if not all web applications use some sort of base template to remove duplication such as the header and footer. Wicket has a built-in way of handling this instead of having to use a separate library such as <a href="http://www.opensymphony.com/sitemesh/" target="_blank">SiteMesh</a>. Wicket uses inheritance to facilitate templates. They provide their own base class called <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/WebPage.html">WebPage</a> that our application specific base class will extend from to get started. The <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/WebPage.html">WebPage</a> class sets us up with a blank web page in seconds. For our application, we have a simple header/footer that we want all of our pages to use and a very simple menu that I threw into the base page that I named <em>BasePage</em>.

```public class BasePage extends WebPage {...```
This along with an html page gives us a basic template that all of our pages will extend from.

``` html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
<html>
  <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    <title>Mystic Paste</title>

<link rel="stylesheet" type="text/css" href="css/style.css"/>
    <!--[if IE]>

<link rel="stylesheet" type="text/css" href="css/ie.css" />
    <![endif]-->
  </head>
  <body>

<div id="leftSide">&nbsp;</div>

<div id="rightSide">&nbsp;</div>

<div id="center">
      <!-- header -->

<div id="header">
<div id="logo"><a class="logo" href="http://mysticpaste.com/new"> </a></div>
      </div>

<div id="nav">
<ul id="menus">
<li class="cat-item"><a class="home" title="Home" href="http://www.mysticcoders.com/">Home</a></li>
<li wicket:id="newLinkContainer" class="cat-item"><a wicket:id="newLink" href="#" title="New Paste">New</a></li>
<li wicket:id="historyLinkContainer" class="cat-item"><a wicket:id="historyLink" href="#" title="View Paste History">History</a></li>
<li><a class="lastmenu" href="javascript: return false;"> </a></li>
        </ul>
      </div>

<div id="header_bottom"> </div>

      <!-- content -->

<div id="content">
        <wicket:child/>
      </div>

<div class="clear"> </div>

      <!-- footer -->

<div id="footer_left"> </div>

<div id="footer_right"> </div>

<div id="footer_center">
<div id="copyright">Copyright &copy; 2000-2009 Mystic Coders, LLC</div>
      </div>
    </div>

<div id="logo_footer"><img src="images/logo_bottom.png" width="74" height="57"/></div>
  </body>
</html>
```

This html file sits on the file system in the same package as your <em>BasePage</em> class and is named the same but with a .html extension... BasePage.html. We have decided to separate the java files from the html by putting the html within the same package structure underneath the resources folder. Note the <em>wicket:id</em> attributes and the <em><wicket:child/></em> tag... the <em>wicket:id</em> attributes are used within the java code to identify the components and the <em>wicket:child</em> tag is used as a placeholder signaling that any page that extends this page will be filling in the body of the tag. The 2 links with <em>wicket:id</em> attributes are used for menu item links and the surrounding <em>li</em> tags contain <em>wicket:id</em> attributes to facilitate the highlighting of the current page.
&nbsp;

<h1>PASTE IT!</h1>
Wicket starts to get fun when we get into forms. We need to create a form that will let the user choose the language type for formatting the pasted content, whether or not this is a private post (not easily guessed url and won't show in history), and the content itself. We are going to want to make sure that the end result has a fairly simple url that is easy to copy and paste.
&nbsp;
The first thing we usually do is come up with the page class and the html... so we are going to create a class that extends our <em>BasePage</em>:

```public class PasteItemPage extends BasePage {...```
The matching html page again, resides in the same package as the Java class and is named the same.
PasteItemPage.html:

``` html
<wicket:extend>

<form wicket:id="pasteForm">
<div id="paste_options">
<ul>
<li>private:</li>
<li><input wicket:id="private" type="checkbox" /></li>
      </ul>

<ul>
<li>language:</li>
<li>
<select wicket:id="type" class="language">
<option>Choose One</option>

<option>Java</option>

<option>Groovy</option>

<option>PHP</option>
          </select>
        </li>
      </ul>
    </div>

<div id="paste_content">
<div id="textLeft"> </div>

<div id="textRight"> </div>

<div id="textCenter"><textarea wicket:id="content" wrap="off"></textarea></div>
    </div>

<div id="paste_submit"><input type="submit" value="Paste" /></div>
    </form>
</wicket:extend>
```

Note the <em>wicket:extend</em> tags which tells Wicket that everything within those tags are the contents that we are interested in... for instance, you could have the whole html file with html/head/body tags if you wanted to and wicket would ignore everything except for the data between the <em>wicket:extend</em> tags.
&nbsp;
The <em>wicket:id</em> attributes are placed in the form and the form components. These attributes will allow us to create a Wicket form and bind to the form components.
&nbsp;
Wicket provides components for just about everything you want to do, so we extend the Wicket <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/Form.html">Form</a> class and add that to our page, we then add our form fields (<a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/DropDownChoice.html">DropDownChoice</a>, <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/CheckBox.html">CheckBox</a> and <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/TextArea.html">TextArea</a>) to the form.
&nbsp;
Components in Wicket are hierarchical, you MUST nest/add your components in your java code to match exactly the nesting of your html components. For example, the following snippet is taken from BasePage.html:

``` html
<li wicket:id="newLinkContainer" class="cat-item"><a wicket:id="newLink" href="#" title="New Paste">New</a></li>
```

the corresponding java code looks like this:

``` java
    WebMarkupContainer newLinkContainer = new WebMarkupContainer("newLinkContainer");
    ...
    newLinkContainer.add(new BookmarkablePageLink("newLink", PasteItemPage.class));
    add(newLinkContainer);
```

In the html markup, the <em>href</em> tag marked with <em>wicket:id="newLink"</em> is nested inside of the <em>li</em> tag marked with <em>wicket:id="newLinkContainer"</em>. We therefore need to match this hierarchy  within our corresponding java code. In the Java code, I have created a WebMarkupContainer component with id="newLinkContainer" to match to our <em>li</em> tag, I then add the nested <em><strong>BookmarkablePageLink</strong></em> with id="newLink" to the newLinkContainer component. I then add the newLinkContainer component to the page as the newLinkContainer is not contained within any other wicket tags.
&nbsp;
This nesting can get very deep depending on the web page layout. It is not difficult to keep track of the nesting but sometimes you may forget to fix the html or the Java code when making changes to the either file. However, the Wicket developers built in a clean error message that comes up when you run the application and there is a mismatch between your html and the Java code. For example, if I use our example above and add the newLink to the page rather than to the newLinkContainer, I get the following error message:

``` java
WicketMessage: Unable to find component with id 'newLink' in [MarkupContainer [Component id = newLinkContainer]]. This means that you declared wicket:id=newLink in your markup, but that you either did not add the component to your page at all, or that the hierarchy does not match.
[markup = file:/...paste/web/pages/paste/PasteItemPage.html
```

These error messages make it easy to find the problems with the hierarchy rather than guessing as to where the problem might be.
&nbsp;
The following is the full Java source for our <em>PasteItemPage</em>:

``` java
public class PasteItemPage extends BasePage {

    @SpringBean
    PasteService pasteService;

    public PasteItemPage() {
        super(PasteItemPage.class);
        add(new PasteForm("pasteForm", new CompoundPropertyModel(new PasteItem())));
    }

    public class PasteForm extends Form {
        public PasteForm(String id, IModel model) {
            super(id, model);

            add(new CheckBox("private"));
            add(new DropDownChoice("type", Arrays.asList(LanguageType.JAVA, LanguageType.CSS, LanguageType.HTML)));
            add(new TextArea("content"));
        }

        @Override
        protected void onSubmit() {
            PasteItem pasteItem = (PasteItem) PasteForm.this.getModelObject();
            pasteService.createItem("web", pasteItem);
            PageParameters params = new PageParameters();
            if (pasteItem.isPrivate()) {
                params.put("0", pasteItem.getPrivateToken());
                setResponsePage(ViewPrivatePage.class, params);
            } else {
                params.put("0", Long.toString(pasteItem.getId()));
                setResponsePage(ViewPublicPage.class, params);
            }
        }
    }
}
```

As you might have noticed... Wicket uses Models to back the components. In our case... we use a <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/model/CompoundPropertyModel.html">CompoundPropertyModel</a> which makes it extremely easy to bind to components. It basically tells any component that uses this model to bind the property from the model object with a component using the components id. For instance, we have <em>add(new CheckBox("private"));</em> which says that we want to add a <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/CheckBox.html">CheckBox</a> component with the id of "private" and bind it to the property of our model object with the same name (the private field of <em>PasteItem</em>). I have added the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/model/CompoundPropertyModel.html">CompoundPropertyModel</a> to the form component which automagically backs all components added to the form but can easily be overridden by just passing in a new model to any components that need a different model. There are many other types of Models to choose from as you may not need or want the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/model/CompoundPropertyModel.html">CompountPropertyModel</a> due to mismatches in the names and such. The <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/DropDownChoice.html">DropDownChoice</a> and <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/form/TextArea.html">TextArea</a> components are bound to the html <em>SELECT</em> and <em>TEXTAREA</em> tags in the same manor.
&nbsp;
The last piece of the form submission is completed by overriding the onSubmit method of the form and saving our model object with a simple call to our service. That is it for capturing user input and saving it... not sure that it gets much easier than that!
&nbsp;
As part of the save routine, one other noteworthy tidbit here is how we forward on to the next page:

``` java
        PageParameters params = new PageParameters();
        if (pasteItem.isPrivate()) {
            params.put("0", pasteItem.getPrivateToken());
            setResponsePage(ViewPrivatePage.class, params);
        } else {
            params.put("0", Long.toString(pasteItem.getId()));
            setResponsePage(ViewPublicPage.class, params);
        }
```

The <em>setResponsePage</em> method is exactly that... we give it the page that we want to forward to... in this case, if it is a private message, then we forward to our page we created for private pastes, otherwise, we forward to our regular public view page. Notice that we create a <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/PageParameters.html">PageParameters</a> object, Wicket abstracts away the dreadful request object from you and gives you a convenient object for adding and retrieving page parameters. Now... as I mentioned earlier, we want simple urls... so normally, you would put something like <em>params.put("id", pasteItem.getId());</em> and this would pass the request param of <em>id=5</em> or with Wickets bookmarkable pages, you would see something like <em>http://your.domain.com/view/id/5</em>. We decided we didn't want the id to show as it provides no use within the url itself... so... Wicket gives us the ability to create our own URL encoding strategy and provides a few already implemented strategies. Within the Wicket Application class that was generated on Day 1, we can add the following:

```mount(new IndexedParamUrlCodingStrategy("/view", ViewPublicPage.class));```
This tells Wicket that anyone forwarding to my <em>ViewPublicPage</em> will use the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/request/target/coding/IndexedParamUrlCodingStrategy.html">IndexedParamUrlCodingStrategy</a>... which works as follows: we add/pull params from the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/PageParameters.html">PageParameters</a> using keys of 0, 1, 2... etc. As you can see in our code example, we use 0 as we only have one param. The end result of this is that our url will look something like this:

```http://your.domain.com/view/5```
This doesn't seem like much, but it does have a slightly cleaner url and depending on your application can help greatly with SEO.
&nbsp;

<h1>Simple and Clean</h1>
I have shown you a very simple form and how easy it is to create a working form submission, but have you noticed that there isn't any java snippets of any kind in the html?
In my opinion, that is one of the best features of wicket, there ARE <em>wicket:ids</em> but those are attributes and tags that are ignored by most GUI designers such as Dreamweaver so the HTML can be ported back and forth if need be without the graphics designer hosing the developers hard work. Even if you have to take a fresh copy of the HTML... it is far simpler to just have to add the <em>wicket:ids</em> back in than to merge in all of the XML or JSP crud that most other Java frameworks force the developer to work with.
&nbsp;
Gone are the days of System.outs in your jsps to figure out what is going on in there. With Wicket... all of your code is in Java classes which can be debugged easily with your favorite IDE. You can walk through your loops to see what you are populating and why. You can even debug portions of Ajax calls as Wicket Ajax enabled components hide the complexity of Ajax submissions and data retrieval.
&nbsp;
Enough jabbering... let's see some list action and paging goodness!
&nbsp;

<h1>History</h1>
Form handling and components are wonderful but I think the history page shows off some of my favorite components within Wicket. There is a nice selection of different types of repeater components and a great paging component that we will use to display paste history.
&nbsp;
Let's start off by talking about the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a> component that we are going to use to display the pastes. The <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a> component is a repeater that allows us to easily mark what we want to repeat within the html and fill in the data from our model object. This is done by adding the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a> to our page and then implementing the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a>s <em>populateItem</em> method as follows:

``` java
        add(historyDataView = new DataView("history", new HistoryDataProvider(pasteService), 10) {
            protected void populateItem(Item item) {
                PasteItem pasteItem = (PasteItem) item.getModelObject();

                PageParameters params = new PageParameters();
                params.put("0", Long.toString(pasteItem.getId()));
                item.add(new BookmarkablePageLink("viewLink", ViewPublicPage.class, params));

                final String[] contentLines = pasteItem.getContent().split("n");
                item.add(new Label("lineCount", "(" + contentLines.length + " Line" + (contentLines.length > 1 ? "s" : "") + ")"));

                item.add(new Label("posted", getElapsedTimeSincePost(pasteItem)));

                List lines = new ArrayList();
                int count = 0;
                for (String contentLine : contentLines) {
                    count++;
                    if (count > 5) {
                        break;
                    } else {
                        lines.add(contentLine);
                    }
                }
                item.add(new ListView("content", lines) {
                    protected void populateItem(ListItem item) {
                        String content = (String) item.getModelObject();
                        Label contentLine = new Label("contentLine", ((item.getIndex() + 1) + "     ").substring(0, 5) + content.replaceAll("r", "").replaceAll("n", ""));
                        item.add(contentLine);
                        if ((item.getIndex() + 1) % 2 == 0) {
                            item.add(new SimpleAttributeModifier("class", "highlight"));
                        }
                    }
                });

                item.add(new BookmarkablePageLink("viewLink2", ViewPublicPage.class, params) {
                    @Override
                    public boolean isVisible() {
                        return contentLines.length > 5;
                    }
                });
            }
        });
```

and the corresponding html:

``` html
<div wicket:id="history" class="historyItem">
<div class="view">
<div class="historyItemHeader">
<div class="historyItemView"><a wicket:id="viewLink" href="#">View Paste</a></div>
<div wicket:id="lineCount" class="historyItemLines">(27 lines)</div>
<div wicket:id="posted" class="historyItemTime">1 hour ago</div>
          </div>

<div class="historyItemHeaderBottom"> </div>

<div wicket:id="content"><re wicket:id="contentLine">asdfl;kajsdf; a;sldkfj a;lskdjf</re></div>

<div class="historyItemView"><a wicket:id="viewLink2">More...</a></div>
        </div>
    </div>
```

Digging in... you mark with a <em>wicket:id</em> what you want to repeat... in our case, it is the container div for a history item which we marked as <em>wicket:id="history"</em>. For every object (<em>PasteItem</em>) within our models list, we are going to get a new div with contents. For each object within the list, we add a <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/link/BookmarkablePageLink.html">BookmarkablePageLink</a> which links to the paste view, the line count and elapsed time which we add as <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/basic/Label.html">Label</a> components, a repeater to display the first 5 lines of the paste, and a More link which displays only if there are more than 5 lines in the paste.
&nbsp;
A <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/link/BookmarkablePageLink.html">BookmarkablePageLink</a> means we are going to have a "clean" URL and we have already covered the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/PageParameters.html">PageParameters</a>. The <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/basic/Label.html">Label</a> has a convenience constructor to allow for <a target="_blank" href="http://java.sun.com/javase/6/docs/api/java/lang/String.html">String</a>s rather than having to wrap them in a model. As mentioned earlier, the line count and elapsed time are derived and therefore cannot be pulled from the model object but instead are set manually. Then we have another type of repeater to display the paste. I have chosen a <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/list/ListView.html">ListView</a> as I'm passing it a <a target="_blank" href="http://java.sun.com/javase/6/docs/api/java/util/List.html">List</a> and don't need to worry about length or paging. The last component we add is the conditional link to the paste view where we override the <em>isVisible</em> method to tell Wicket whether or not this component is visible.
&nbsp;
That covers the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a>... now, what about paging? Wicket has a <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/navigation/paging/PagingNavigator.html">PagingNavigator</a> component that has a prebuilt paging mechanism that can be easily overridden to accommodate just about any type of paging look and feel that your little heart desires. The requirements for using the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/navigation/paging/PagingNavigator.html">PagingNavigator</a> are that you need to start with a reapeater that implements <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/navigation/paging/IPageable.html">IPageable</a> (<a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a>) and you will need to supply the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a> with a data provider that implements <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/IDataProvider.html">IDataProvider</a>. I have chosen to extend <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DefaultDataProvider.html">DefaultDataProvider</a> and implement as follows:

``` java

public class HistoryDataProvider extends DefaultDataProvider {

    PasteService pasteService;

    public HistoryDataProvider(PasteService pasteService) {
        this.pasteService = pasteService;
    }

    public Iterator iterator(int first, int count) {
        return pasteService.getLatestItems("web", count, first, false).iterator();
    }

    public int size() {
        return new Long(pasteService.getLatestItemsCount("web")).intValue();
    }

    public IModel model(Object object) {
        return new Model((PasteItem) object);
    }

}
```

You can see that the data provider allows us to only pull what is displayed on the current page and gives the paging mechanism the overall count value via the size method. In return, the paging mechanism supplies the start and count for the pulling of what is to be displayed.
&nbsp;
Last is the addition of the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/navigation/paging/PagingNavigator.html">PagingNavigator</a> components which I have chosen to show at the top and bottom of the list.
&nbsp;
HistoryPage.html

``` html
<wicket:extend>

<div class="navContainer">
<div wicket:id="pageNav" class="pageNav"><a href="#">Previous</a><a href="#">1</a><a href="#">2</a><a href="#">Next</a></div></div>

<div wicket:id="history" class="historyItem">
<div class="view">
<div class="historyItemHeader">
<div class="historyItemView"><a wicket:id="viewLink" href="#">View Paste</a></div>
<div wicket:id="lineCount" class="historyItemLines">(27 lines)</div>
<div wicket:id="posted" class="historyItemTime">1 hour ago</div>
          </div>

<div class="historyItemHeaderBottom"> </div>

<div wicket:id="content"><re wicket:id="contentLine">asdfl;kajsdf; a;sldkfj a;lskdjf</re></div>

<div class="historyItemView"><a wicket:id="viewLink2">More...</a></div>
        </div>
    </div>

<div class="navContainer">
<div wicket:id="pageNav2" class="pageNav"><a href="#">Previous</a><a href="#">1</a><a href="#">2</a><a href="#">Next</a></div></div>
</wicket:extend>
```

and HistoryPage.java

``` java

public class HistoryPage extends BasePage {

    @SpringBean
    PasteService pasteService;

    SimpleDateFormat sdf = new SimpleDateFormat("MM/dd/yyyy");

    DataView historyDataView;

    public HistoryPage() {
        super(HistoryPage.class);

        add(historyDataView = new DataView("history", new HistoryDataProvider(pasteService), 10) {
            protected void populateItem(Item item) {
                PasteItem pasteItem = (PasteItem) item.getModelObject();

                PageParameters params = new PageParameters();
                params.put("0", Long.toString(pasteItem.getId()));
                item.add(new BookmarkablePageLink("viewLink", ViewPublicPage.class, params));

                final String[] contentLines = pasteItem.getContent().split("n");
                item.add(new Label("lineCount", "(" + contentLines.length + " Line" + (contentLines.length > 1 ? "s" : "") + ")"));

                item.add(new Label("posted", getElapsedTimeSincePost(pasteItem)));

                List lines = new ArrayList();
                int count = 0;
                for (String contentLine : contentLines) {
                    count++;
                    if (count > 5) {
                        break;
                    } else {
                        lines.add(contentLine);
                    }
                }
                item.add(new ListView("content", lines) {
                    protected void populateItem(ListItem item) {
                        String content = (String) item.getModelObject();
                        Label contentLine = new Label("contentLine", ((item.getIndex() + 1) + "     ").substring(0, 5) + content.replaceAll("r", "").replaceAll("n", ""));
                        item.add(contentLine);
                        if ((item.getIndex() + 1) % 2 == 0) {
                            item.add(new SimpleAttributeModifier("class", "highlight"));
                        }
                    }
                });

                item.add(new BookmarkablePageLink("viewLink2", ViewPublicPage.class, params) {
                    @Override
                    public boolean isVisible() {
                        return contentLines.length > 5;
                    }
                });
            }
        });

        add(new PagingNavigator("pageNav", historyDataView));
        add(new PagingNavigator("pageNav2", historyDataView));
    }
}
```

Note that we have just added the 2 <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/html/navigation/paging/PagingNavigator.html">PagingNavigator</a>s at the bottom of the code, passing in the <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/markup/repeater/data/DataView.html">DataView</a> that we created above. That is it... you now have a fully functioning history page with paging navigation. Again, not sure it can get much easier than that.
&nbsp;

<h1>Testing</h1>
Testing you say? Whoa... we can't test the front-end without going through a lot of trouble can we?
&nbsp;
Well... the truth is that Wicket provides a way to do quite a bit of front-end testing and it is pretty much as easy as testing any other Java code!
&nbsp;
What would we want to test? Well, I believe we would want to test that a successful paste would indeed go to the correct page and that the view of the post would contain what we pasted. We might also want to see if our links work... do they go to the correct page? We don't have a complicated application, so we are going to show a small test, but the testing framework can check for just about anything that can happen on a page. For now, take a look at this simple test:

``` java
public class TestPastePage extends AbstractIntegrationTest {

    @SpringBeanByType
    private PasteService svc;

    @SpringBeanByType
    private PasteItemDao dao;

    protected WicketTester tester;

    @Before
    public void setup() {
        AnnotApplicationContextMock appctx = new
                AnnotApplicationContextMock();
        appctx.putBean("pasteDao", dao);
        appctx.putBean("pasteService", svc);

        tester = new WicketTester(MysticPasteApplication.class);
        WebApplication app = tester.getApplication();

        app.addComponentInstantiationListener(new SpringComponentInjector(app, appctx));
    }

    @Test
    public void testPaste() {
        tester.startPage(PasteItemPage.class);
        tester.assertRenderedPage(PasteItemPage.class);
        FormTester ft = tester.newFormTester("pasteForm");
        ft.select("type", 0);
        ft.setValue("content", "blahblahblah");
        ft.submit();
        tester.assertRenderedPage(ViewPublicPage.class);
        tester.assertContains("blahblahblah");
        tester.assertLabel("type", "JAVA");
    }

    @Test
    public void testHistoryMenuClick() {
        tester.startPage(PasteItemPage.class);
        tester.assertRenderedPage(PasteItemPage.class);
        tester.clickLink("historyLinkContainer:historyLink");
        tester.assertRenderedPage(HistoryPage.class);
    }
}
```

Well... this looks simple enough. First, we'll test to see if a paste works by looking at what happens in testPaste:

<ul>
<li>start the page we want to look at.</li>
<li>validate that the page was rendered and that we are still on this page.</li>
<li>setup the form tester.</li>
<li>set the values for the language drop-down and the paste content.</li>
<li>submit the form.</li>
<li>assert that it went to the page we were expecting it to go to next.</li>
<li>see if it contains the paste, in this case "blahblahblah".</li>
<li>and finally, see if the label for Language is set to JAVA.</li>
</ul>
Very cool... the test passes... next we test the menu item history link. We open our starting page, kick off the link via clickLink which is set to our history link and then verify that it indeed went to our history page.
&nbsp;
I bet you never thought testing front-end code could be so easy. The <a target="_blank" href="http://wicket.apache.org/docs/wicket-1.3.2/wicket/apidocs/org/apache/wicket/util/tester/WicketTester.html">WicketTester</a> does all the work so you can now have far greater test coverage than you would normally have with a web application.
&nbsp;

<h1>Conclusion</h1>
Wicket allows a developer to create applications as rapidly as any framework I have seen to date while keeping the html as pristine as possible. Occasionally, I am forced to go back to older applications and deal with jsps both old and new and I always come away with a headache and nosebleed due to the punches taken in dealing with jsps and jstl. I wish I had the time and space to go into more details about some of the helpful components that Wicket offers and I haven't even touched on Wickets Ajax components in this version of the MysticPaste application. Pay close attention to our blog to see follow up posts and Wicket higher learning as we make improvements to the MysticPaste application. We will also continue to post Wicket tips and tricks as we come across them.

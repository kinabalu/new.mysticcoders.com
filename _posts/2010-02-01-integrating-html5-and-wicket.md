---
layout: post
status: publish
published: true
title: Integrating HTML5 and Wicket
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2010-02-01T00:13:34.0000Z'
tags: []
comments: true
---
After following some of the debates raging about <a href="http://www.apple.com/ipad/">Apple's new iPad</a> and the future of <a href="http://www.adobe.com/products/flashplayer/">Adobe's Flash</a>, the discussion usually turned to the coming future of <a href="http://dev.w3.org/html5/spec/Overview.html">HTML5</a>.

Seeing as we love <a href="http://wicket.apache.org">Apache Wicket</a> at <a href="http://www.mysticcoders.com" >Mystic</a>, I thought I'd tinker around to see how hard it would be to start adding some support for the new HTML5 tags.  There are quite a few examples out there that show off <code>canvas</code>, <code>geolocation</code>, <code>storage</code>, and of course <code>video</code> and <code>audio</code>.
<a id="more"></a><a id="more-1723"></a>

First thing I set about doing, was to define the <code>video</code> tag.  It takes an optional <code>src</code> attribute among others, or multiple <code>source</code> tags for offering up different video streams for the browser to choose from.  Firefox uses Ogg Vorbis, and Safari uses H.264, so of course, the browser vendors still don't agree.  Here's some code to use what I'd want to see from a <code>video</code> component:

``` java
        final List<mediaSource> mm = new ArrayList<mediaSource>();
        mm.add(new MediaSource("/dizzy.mp4", "video/mp4"));
        mm.add(new MediaSource("/dizzy.ogv", "video/ogg"));

        IModel<list<mediaSource>> mediaSourceList = new AbstractReadOnlyModel<list<mediaSource>>() {
            public List<mediaSource> getObject() {
                return mm;
            }
        };

        add(new Html5Video("dizzy", mediaSourceList) {

            @Override
            protected boolean isControls() {
                return true;
            }

            @Override
            protected boolean isAutoPlay() {
                return true;
            }
        });
```

We've defined a custom Object for use with our new <code>Html5Video</code> component, and it will hold the appropriate attributes we would need to output either a <code>src</code> attribute or a <code>source</code> tag.  You can also see from this example that we've got a few booleans we're overriding by default, and more available in the actual implementation.  Let's take a look at the <code>Html5Video</code> component:

``` java
public class Html5Video extends Html5Media {

    public Html5Video(String id, IModel<list<mediaSource>> model) {
        super(id, model);
    }

    protected int getWidth() { return 0; }

    protected int getHeight() { return 0; }

    @Override
    protected void onComponentTag(final ComponentTag tag) {
        if(getWidth()>0) {
            tag.put("width", getWidth());
        }

        if(getHeight()>0) {
            tag.put("height", getHeight());
        }

        super.onComponentTag(tag);
    }

    protected String getTagName() {
        return "video";
    }
}
```

So you can see we've abstracted this out even further into an <code>Html5Media</code> object which we'll look at shortly.  For now, we have <code>width</code> and <code>height</code> which are specific to just the <code>video</code> tag.  And we're also overriding <code>onComponentTag</code> to throw those attributes into the <code>video</code> tag if they aren't zero.  We also steal from some ideas in wicket core, and implement a method in <code>Html5Media</code> to checkComponentTag based on the results of a method that can be overridden <code>getTagName</code>.

Let's take a look at <code>Html5Media</code> which is where we'll find most of the meat:

``` java
public class Html5Media extends WebMarkupContainer {

    private IModel<list<mediaSource>> sources;

    public Html5Media(String id, final IModel<list<mediaSource>> model) {
        super(id, model);
        this.sources = wrap(model);
        add(new Html5UtilsBehavior());
    }
```

First thing we see, is we're extending <code>WebMarkupContainer</code>, basically because our component can have body text (useful for fallback support).  Next you'll see that we're adding a behavior <code>Html5UtilsBehavior</code>.  The basic purpose is to header contribute a useful javascript file when working with browsers that don't yet support HTML5 (Internet Explorer I'm looking at you!).  Some more code:

``` java
    @Override
    protected void onComponentTag(final ComponentTag tag) {
        String tagName = getTagName();
        if (tagName != null) {
            checkComponentTag(tag, tagName);
        }

        List<mediaSource> sources = getSources();

        if (sources != null && sources.size() == 1) {
            MediaSource source = sources.get(0);
            tag.put("src", source.getSrc());
        }

        if (isAutoBuffer()) {
            tag.put("autobuffer", true);
        }

        if (isAutoPlay()) {
            tag.put("autoplay", true);
        }

        if (isLoop()) {
            tag.put("loop", true);
        }

        if (isControls()) {
            tag.put("controls", true);
        }

        // Default handling for component tag
        super.onComponentTag(tag);
    }

    protected String getTagName() {
        return null;
    }
```

Here we check the component tag to ensure it is the acceptable name.  Then if we only have a single source, we add this to the <code>video</code> tag instead of separate elements in the body.  The next bunch of statements pull from methods and add boolean attributes to the tag if they are true.  And we provide an implementation of <code>getTagName</code> that returns null as a sensible default.

<code>onComponentTagBody</code> is where we optionally will define <code>source</code> tags and the optional attributes that go along with it:

``` java
    @Override
    protected void onComponentTagBody(final MarkupStream markupStream, final ComponentTag openTag) {

        List<mediaSource> sources = getSources();

        if (sources != null && sources.size() > 1) {
            final AppendingStringBuffer buffer = new AppendingStringBuffer();
            for (int index = 0; index < sources.size(); index++) {
                final MediaSource source = sources.get(index);
                buffer.append("
<source ");
                buffer.append("src='");
                buffer.append(source.getSrc());
                buffer.append("'");
                if (source.getType() != null) {
                    buffer.append(" type='");
                    buffer.append(source.getType());
                    buffer.append("'");
                }
                if (source.getMedia() != null) {
                    buffer.append(" media='");
                    buffer.append(source.getMedia());
                    buffer.append("'");
                }

                buffer.append(" />");
            }

            buffer.append("
");

            getResponse().write(buffer.toString());

        }
        super.onComponentTagBody(markupStream, openTag);
    }
```

Here we're ensuring things aren't empty, and then if we have more than one source element (often the case for compatibility between Firefox and Safari), we'll output each <code>source</code> tag.

We've also gone through the trouble of adding an implementation of <code>Html5Audio</code> which consisted of overriding the <code>getTagName</code> method and returning <code>audio</code>.  Pretty simple stuff.

When we put our example into place, we get a video with controls like so:

<a href="http://www.mysticcoders.com/wp-content/uploads/2010/02/Screen-shot-2010-01-31-at-10.59.53-PM.png"><img src="https://www.mysticcoders.com/wp-content/uploads/2010/02/Screen-shot-2010-01-31-at-10.59.53-PM.png" alt="" title="Screen shot 2010-01-31 at 10.59.53 PM" width="496" height="425" class="alignnone size-full wp-image-198" /></a>

So what's next?  If you download the project available and linked below, it also contains an example of using the <code>audio</code> component.  The <code>Html5UtilsBehavior</code> gives us the ability to CSS style the new HTML5 tags even with Internet Explorer, so our code can be more semantic instead of littering it with div's for lack of an alternative.  There are a ton more interactions and behaviors that can be added to support video and audio, support for canvas, postMessage,  storage, Web Database. Web Workers, geolocation, Content Editable, etc.  I have no reason to think any of these would be impossible to integrate into a sensible component with Wicket.

If you'd like to download the example and run it locally, or take a look at the components written, I've started a project over at Google Code called <a href="https://code.google.com/p/wicket-html5/" target="_blank">wicket-html5</a>.  Contact me if you'd like to contribute and start hacking away at some of these components.

To infinity, and beyond!

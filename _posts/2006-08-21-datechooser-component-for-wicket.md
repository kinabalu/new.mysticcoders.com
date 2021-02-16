---
layout: post
status: publish
published: true
title: DateChooser component for Wicket
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2006-08-21T00:17:17.0000Z'
comments: true
---
After much loss of sleep trying to use DatePicker in wicket-extensions, I've instead opted to create a simple component with date selectable dropdowns of month, day, year.  This little pet project was great, in that it helped me really get a better understanding of how Model's work with Wicket<a id="more"></a><a id="more-43"></a>, and what it takes to make a component.  So instead of the javascript date chooser, we have this:

<img id="image42" src="http://www.mysticcoders.com/wp-content/uploads/2006/08/picture-1.png" alt="DateChooser Component Screenshot" />

And the only thing you pass it, is a Date in a Model, and it does the rest of the setting up.  So I'll give you a quick overview of the source and what it does, and include the code as attachments to this post.

First things first, initialization:

``` java
public class DateChooser extends FormComponent {
```

as this is going to be a component, it should extend FormComponent, if we were adding extra functionality onto an existing form element, rather than a collection of them, we could just extend that class, easy.

``` java
public DateChooser(final MarkupContainer parent, final String id, IModel model) {
super(parent, id);
```

This is the constructor from DateChooser, as you can see, its not a 1.x constructor, but written against the new changes in 2.0.  It would be fairly simple to convert this to a 1.2 implementation.

``` java
new DropDownChoice(this, "month", new DateModel(model, Calendar.MONTH), getMonths(), new IChoiceRenderer() { ... }
new DropDownChoice(this, "day", new DateModel(model, Calendar.DAY_OF_MONTH), getDays());
new DropDownChoice(this, "year", new DateModel(model, Calendar.YEAR), getYears());
```

And here are the dropdowns, as you can see from the first dropdown, there is an added IChoiceRenderer, which in the implementation basically grabs the numeric value passed in for the choices Collection, and using SimpleDateFormat, gives the text for that month.

``` java
private class DateModel extends AbstractModel {
```

...on to the implementation of the model, making some simple use of generics here.

``` java
public DateModel(IModel dateModel, int calendarField) {
```

we create a DateModel instance, and pass it the model that is being passed to this component, and in addition we pass the calendarField value, so we know what to modify in our setObject() method.

``` java
if (dateModel.getObject() == null) return null;
Calendar cal = Calendar.getInstance();
cal.setTime((Date) dateModel.getObject());
return cal.get(calendarField);
```

Here we implement the getObject() method, returning the numeric value for the supplied calendarField.

``` java
Date date = (Date)dateModel.getObject();
if(date==null) {
    date = new Date();
}

Calendar cal = Calendar.getInstance();
cal.setTime(date);
cal.set(calendarField, object.intValue());
dateModel.setObject(cal.getTime());
```

and here's the setObject() method, where during the submission of the form, if a Date hasn't been added to the model yet, for us to start setting field values on, it gets created, and then using the value passed in for Object, we set each calendarField in turn as the FormComponent get processed.

There are a few different things that can be done to enhance this:

<ul>
<li>The days dropdown always shows 31 days, whether its got 30, 31, 28, 29, etc.</li>
<li>The ordering of the dropdowns is in M/d/y, which definitely isn't the only way to do it.</li>
</ul>
Would love comments on how to improve this, code attached.

<a id="p44" title="DateChooser Component" href="http://www.mysticcoders.com/wp-content/uploads/2006/08/datechoosercomponent.zip">DateChooser Component</a>

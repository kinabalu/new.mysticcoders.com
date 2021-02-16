---
layout: post
status: publish
published: true
title: Enums internationalization with Wicket
author:
  name: Il Della
  picture: '/assets/blog/authors/ildella.jpg'
  url: https://mysticcoders.com
  email: ildella@gmail.com
date: '2009-10-06T08:30:07.0000Z'
tags: []
comments: true
---
<em>(Editorâ€™s note: <a href="http://yeswicket.com" target="_blank">Olivier Croisier</a> contributed this column from YesWicket.)</em>

Enums are a convenient way to represent finite collections of elements : seasons, week days... As a consequence, they frequently need to be input or displayed in web applications - and it better be in a I18N-aware way.
Let's see how easily Wicket can handle this.

<h2>Enums internationalization</h2>
Throughout this article, we'll use the Seasons Enum as an example :

``` java
public enum Season {
    SPRING, SUMMER, AUTUMN, WINTER;
}
```

Wicket has a powerful hierarchical I18N system : given a logical I18N key (a string), the corresponding translation is first searched for at the current component level, then up its hierarchy - all the way up to the Application root if required.
To benefit from it, the developer only has to call the getString() method on any Wicket component :

``` java
String translation = anyComponent.getString(key);
```

The first thing we need to do is therefore to devise a way to generate a unique I18N key for each Enum constant. The most obvious solution is to use their Fully Qualified Names as a key (eg. "com.yeswicket.wickettips.model.WINTER") ; this should prevent fortuitous name clashes.

The following code snippet demonstrates how to get an Enum's FQN :

``` java
Season enumValue = Season.WINTER;
String messageKey = enumValue.getDeclaringClass().getCanonicalName() + "." + enumValue.name();
```

In order to avoid copy/pasting this code all around the application, and to be able to easily replace it with another algorithm should the need arise, we'll use the Strategy pattern, where every algorithm is encapsulated in its own class.
This might seem easy to implement, but keep in mind that every class accessible from a Wicket component will be serialized along with it in the session store... and that's something you will definetely want to avoid.

Static methods to the rescue !
The following class, inspired from java.util.Locale, has a static-only API for its consumers (thus solving the serialization concern) and still allows the underlying algorithm to be changed at will :

``` java
public abstract class EnumMessageKeyProvider {
	private static EnumMessageKeyProvider provider = new DefaultEnumResourceKeyProvider();

	public static EnumMessageKeyProvider getDefault() {
		return EnumMessageKeyProvider.provider;
	}

	public static void setDefault(EnumMessageKeyProvider provider) {
		EnumMessageKeyProvider.provider = provider;
	}

	public abstract <t extends Enum<t>> String computeMessageKey(T enumValue);

	public static <t extends Enum<t>> String getMessageKey(T enumValue) {
		return EnumMessageKeyProvider.provider.computeMessageKey(enumValue);
	}
}
public class DefaultEnumResourceKeyProvider extends EnumMessageKeyProvider {
	@Override
	public <t extends Enum<t>> String computeMessageKey(T enumValue) {
		return enumValue.getDeclaringClass().getCanonicalName() + "." + enumValue.name();
	}
}
```

Getting an I18N key for an Enum is now as easy as :

``` java
Season enumValue = Season.WINTER;
String key = EnumMessageKeyProvider.getMessageKey(enumValue);
```

To use another key generation algorithm, just write a new strategy...

``` java
public class SimpleNameEnumResourceKeyProvider extends EnumMessageKeyProvider {
	@Override
	public <t extends Enum<t>> String computeMessageKey(T enumValue) {
		return enumValue.name();
	}
}
```

...and register it with the EnumMessageKeyProvider ; a good place for that is the Application's init() method :

``` java
public class WicketTipsApplication extends WebApplication {
	@Override
	protected void init() {
		EnumMessageKeyProvider.setDefault(new SimpleNameEnumResourceKeyProvider());
	}
	(...)
}
```

<h2>Displaying internationalized enums</h2>
Wicket's Label is a very basic component that simply displays the text provided by its Model.
To display Enums, we can develop a new kind of Model that automatically performs internationalization and hands over the resulting text to the component it is attached to.

As an example, we'll extend the very convenient PropertyModel.
Please note that our Model provides a String but operates on an Enum : EnumPropertyModel<t extends Enum<t>> extends PropertyModel<string>. Also, in addition to the PropertyModel's existing constructor parameters (the target object and the name of the property to retrieve), our class needs a Component to call the getString() method on.

``` java
public class EnumPropertyModel<t extends Enum<t>> extends PropertyModel<string> {

	private Component component;

	public EnumPropertyModel(Object modelObject, String expression, Component resourceProvider) {
		super(modelObject, expression);
		this.component = resourceProvider;
	}

	@Override
	public String getObject() {
		final String expression = propertyExpression();
		final Object target = getTarget();
		if (target != null) {
			T enumValue = (T) PropertyResolver.getValue(expression, target);
			String key = EnumMessageKeyProvider.getMessageKey(enumValue);
			return component.getString(key);
		}
		return null;
	}
}
```

This custom Model can now be used with a Label (and many other components) to easily display any Enum :

``` java
public class HomePage extends WebPage {
	private Season season = Season.SPRING;
	public HomePage() {
		add(new Label("label", new EnumPropertyModel<season>(this, "season", this)));
	}
}
```

<h2>Selecting an enum with a DropDownChoice</h2>
Now that we can display an Enum, let's see how to let the user choose one from a dropdown list.

The DropDownChoice component follows the MVC pattern :

<ul>
<li>The DropDownChoice itself is the Controller</li>
<li>The Model it takes as a parameter is, obviously, the Model</li>
<li>The View is rendered by a ChoiceRenderer that is responsible for the HTML
<option> tags generation.</li>
</ul>
Usually, the DropDownChoice relies on the Model to define the set of selectable values. With Enums, those values are determined by the Enum's type itself, so we can bypass the Model and override the DropDownChoice's "getChoices()" method :

``` java
public class EnumDropDownChoice<t extends Enum<t>> extends DropDownChoice<t> {

	public EnumDropDownChoice(String id, IModel<t> model) {
		super(id);
		setModel(model);
		setChoiceRenderer(new EnumChoiceRenderer<t>(this));
	}

	public EnumDropDownChoice(String id, IModel<t> model, EnumChoiceRenderer<t> choiceRenderer) {
		super(id);
		setModel(model);
		setChoiceRenderer(choiceRenderer);
	}

	@Override
	public List<? extends T> getChoices() {
		return Arrays.asList(getModelObject().getDeclaringClass().getEnumConstants());
	}
}
```

The related ChoiceRenderer will generate the
<option> tags using the Enum's name as an identifier, and its internationalized value as a label :

``` java
public class EnumChoiceRenderer<t extends Enum<t>> implements IChoiceRenderer<t> {

	/** The Component used a the root of the I18N search process */
	private final Component resourceProvider;

	public EnumChoiceRenderer(final Component resourceProvider) {
		this.resourceProvider = resourceProvider;
	}

	@Override
	public Object getDisplayValue(final T value) {
		final String key = EnumMessageKeyProvider.getMessageKey(value);
		return resourceProvider.getString(key);
	}

	@Override
	public String getIdValue(final T object, final int index) {
		final Enum<?> enumValue = object;
		return enumValue.name();
	}
}
```

Finally, here is an example of how our components can be used to select and display an Enum in a standart Form :

``` java
public class HomePage extends WebPage {
	private Season season = Season.SPRING;
	public HomePage() {
		Form<void> form = new Form<void>("form");
		form.add(new EnumDropDownChoice<season>("season", new PropertyModel<season>(this, "season")));
		add(form);
		add(new Label("label", new EnumPropertyModel<season>(this, "season", this)));
	}
}
```

<h2>In action</h2>
The complete source code is available at the bottom of this article.
Feel free to play with it to see all those custom components in live action. The example form comes with two additional links, to switch between the French and English locales and see the internationalization magic happen.

Last note : a Gradle build script is provided to help you get started in seconds. Just run the following command in the application's directory. Gradle will automatically compile the classes, copy the required resources, and start a Jetty server.

``` java
gradle jettyRun
```

Then, open a browser at :

``` java
http://localhost:8080/Wicket-tips/
```

Have fun !

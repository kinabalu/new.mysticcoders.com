---
layout: post
status: publish
published: true
title: 'After the 5 Days of Wicket: Eclipse Plugin'
author:
  name: Craig Tataryn
  picture: '/assets/blog/authors/ctataryn.jpg'
  twitter: craiger
  url: https://mysticcoders.com
date: '2009-03-30T07:00:27.0000Z'
comments: true
---
Don't get me wrong, <a href="http://www.mysticpaste.com/">Mystic Paste</a> is great!  However if I'm on ##wicket and someone asks a question about some code, and I know I've written that code, I want to be the first to respond.  I don't want some n00b making me look like a n00b by responding first :)  

So instead of:

<ol style="list-style-type:lower-alpha;">
<li>copying code to the clipboard</li>
<li>opening up my browser</li>
<li>navigating to Mystic Paste</li>
<li>pasting my code</li>
<li>selecting the syntax highlighting language</li>
<li>clicking submit</li>
<li>copying the url for the pasted code to my clipboard</li>
<li>pasting that to irc</li>
<li>bang my head against the wall cause someone else beat me to it...</li>
</ol>
I can instead:

<ol style="list-style-type:lower-alpha;">
<li>right click on the text I have selected in my editor and choose "Add to Mystic Paste"</li>
<li>paste url to irc</li>
</ol>
That's what  the plugin does, it will take that text, figure out which type of editor it resides in and use that information to determine the syntax, post the code to Mystic Paste and copy the url for the code to the clipboard.  Boom, now steps a) through i) become steps a) to b) .
<a id="more"></a><a id="more-572"></a>

<h2>You are simply here for the Plugin</h2>
If you don't actually care to know how the plugin was written, but want to start using it, you may download it from <a href="http://www.mysticpaste.com/plugin">http://www.mysticpaste.com/plugin</a>  the directions are there to show you how to get it to work with Eclipse.

<h2>Setting up the project</h2>
Ok, for the two of you who stayed to learn, here's how the Mystic Paste Eclipse plugin was created.  Firstly, launch Eclipse and choose <strong>File->New->Project</strong> and choose "<strong>Plug-in Project</strong>".

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/eclipse1.png" alt="Eclipse Plug-in Project" title="Eclipse Plug-in Project" width="530" height="544" class="aligncenter size-full wp-image-577" />

This will guide you through a series of dialogs, I'll only go over the ones which are meaningful.  We named our plugin project MysticPasteEclipseProject and we gave it the plugin ID of com.mysticcoders.mysticpaste.  The ID of the plugin is important to Eclipse because when your plugin is loaded, it's the ID which uniquely identifies it amongst the thousands of other plugins which comprise the Eclipse platform.

The plug-in project wizard also lets you pick a template to start a new project from.  Our plugin is going to be one that extends the "Popup Menu" feature of Eclipse, this is any feature which shows a context menu.  The project template we choose is not <em>exactly</em> what we want, but it will be close enough so for now, choose the project template for "Plug-in with popup menu" and proceed.

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/eclipse2.png" alt="Eclipse Popup Menu Template" title="Eclipse Popup Menu Template" width="584" height="760" class="aligncenter size-full wp-image-579" />

Next the project wizard will ask us some questions about the "Action" for our plugin.  The Action, in Eclipses-speak , is the class which houses the actual code that will be executed when the user clicks on your menu item.

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/eclipse3.png" alt="Eclipse Action Setup" title="Eclipse Action Setup" width="584" height="760" class="aligncenter size-full wp-image-582" />

Click the Finish button and let Eclipse build the skeleton project for you.

<h2>Plugin.xml</h2>
plugin.xml is the file which Eclipse itself will read to determine what exactly your plugin does.  Is it a new editor?  Is it a toolbar?  That's determined by the <em>extension point</em> you list in this file.  For context menus, the extension point is <code>org.eclipse.ui.popupMenus</code> and the plug-in project wizard conveniently set this for us.

However, remember above I was mentioning that the project template we picked was not "exactly what we want"?  The template Eclipse uses will assume that the context menu should be applicable to <em>resource types</em>, typically these are the nodes you see in your navigation window on the left hand side of the screen when you are browsing through your project files.  Instead, we want to extend the context menu which shows up when you right click inside an editor.

So, instead of our plugin.xml file looking like this:

``` xml
   <extension
         point="org.eclipse.ui.popupMenus">
      <objectContribution
            objectClass="#CompilationUnitEditorContext"
            id="com.mysticcoders.mysticpaste.contribution1">
         <menu
               label="group.add"
               path="additions"
               id="com.mysticcoders.mysticpaste.menu1">
            <separator
                  name="group1">
            </separator>
         </menu>
         <action
               label="Add to Mystic Paste"
               class="com.mysticcoders.mysticpaste.popup.actions.MysticPasteAction"
               menubarPath="a.menu1/group1"
               enablesFor="1"
               id="com.mysticcoders.mysticpaste.newAction">
         </action>
      </objectContribution>
   </extension>
```

We want to instead change the "objectContribution" tag to a "viewerContribution" tag and all together remove the "menu" tag like so:

``` xml
   <extension
         point="org.eclipse.ui.popupMenus">
		<viewerContribution
			targetID="#CompilationUnitEditorContext"
			id="com.mysticcoders.mysticpaste.actions.compilationuniteditor">
			<action
				label="%Add_to_MysticPaste.name"
				icon="$nl$/icons/mystic16.png"
				class="com.mysticcoders.mysticpaste.popup.actions.MysticPasteAction"
				menubarPath="group.add"
				id="com.mysticcoders.mysticpaste.popup.actions.MysticPasteAction.JAVA5" />
		</viewerContribution>
   </extension>
```

Now what I am showing you above is the actual finished product, and if you are not familiar with Eclipse plugins it probably doesn't amount to a whole lot.  Here's a dissection of the elements:

``` xml
   <extension
         point="org.eclipse.ui.popupMenus">
```
Extension points are places in Eclipse which can be "Extended".  Basically, they are just the IDs of plugins in Eclipse that allow themselves to be extended.  The ID for context menus is "org.eclipse.ui.popupMenus".

``` xml
<viewerContribution
	targetID="#CompilationUnitEditorContext"
	id="com.mysticcoders.mysticpaste.actions.compilationuniteditor">
```

Viewer contributions (as opposed to object contributions) determine which type of "thing" in Eclipse will be contributed to.  In our case, we want a viewer contribution because Editors are a type of viewer.  The <strong>targetID</strong> might seem a little weird, but it's actually a predefined constant used by Eclipse to indicate "any editor that has code which can be compiled".  To us, this means a "Java editor", so our contribution to the popup menu extension is for Java Editors.  The <strong>id</strong>, is a unique id which our contribution is known to Eclipse by, it doesn't have to correspond to a package in your project or anything, it just needs to be unique across plugins.

``` xml
<action
	label="%Add_to_MysticPaste.name"
	icon="$nl$/icons/mystic16.png"
	class="com.mysticcoders.mysticpaste.popup.actions.MysticPasteAction"
	menubarPath="group.add"
	id="com.mysticcoders.mysticpaste.popup.actions.MysticPasteAction.JAVA5" />
```

An "Action" is going to be the class which is loaded by Eclipse when someone clicks on the menu item we have added.  You can see that the class "executed" is determined by the fully qualified class you enter as the value for the class attribute.  It must extend <code>IEditorActionDelegate</code>, this is not the same class that the plug-in project wizard setup for you, so you'll have to change it.

menubarPath is a pre-canned designation setup for an area of the context menu which holds the "Add to Snippets" menu item, I figured Add to Mystic Paste did a very similar job, so decided to have the menu item live there.

You'll notice weird things like %Add_to_MysticPaste.name and $nl$/icons/mystic16.png.  You'll find all sorts of unconventional syntax like that in the Eclipse platform and usually you have to dig deep to find out what exactly it all means.  Well, in the case of %Add_to_MysticPaste.name, this tells Eclipse to look in a <code>plugin.properties</code> file bundled with your plugin and insert the value for the key Add_to_MysticPaste.name.  For $nl$/icons/mystic16.png this tells Eclipse to replace the $nl$ token with the path to your plugin's base directory when loaded into Eclipse.  Actually, it points to the "internationalized" base path, but don't worry about that for now, Mystic Paste's icon isn't language dependent.  <strong>Important</strong>: in order to use values from plugin.properties in your plugin.xml file you must add the following line to <code>META-INF/MANIFEST.INF</code>: Bundle-Localization: plugin

Finally we have the id for the action, again just make something unique.  In our case, I added .JAVA5 to the end of my id so that when the user clicks it, I know what language syntax to use when pasting the selected code to Mystic Paste by querying the action's ID at runtime.  What isn't depicted above is the fact that the actual project has several viewerContribution sections for different types of editors in Eclipse and each action has a .&lt;lang type&gt; appended to it's ID to help me determine what language the editor supports.

<h2>The Code</h2>
The code for Mystic Paste is really quite simple.  We take the selected text in the editor and do an HTTP POST to a Servlet which is setup in the Mystic Paste webapplication.  The Servlet accepts the content of the paste as well as the language type.

The only thing special which needs to be done, is to add the dependencies for <a href="http://hc.apache.org/httpcomponents-client/index.html">Commons Http Client</a>.  This is done by using the plugin.xml editor and adding the dependency jars to your project as follows:

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/eclipse4.png" alt="Build Depenencies" title="Build Depenencies" width="713" height="542" class="aligncenter size-full wp-image-593" />
<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/eclipse5.png" alt="Build Dependencies" title="Build Dependencies" width="711" height="541" class="aligncenter size-full wp-image-594" />

The actual code itself is pretty straight forward especially since the author actually documented the code!

``` java
package com.mysticcoders.mysticpaste.popup.actions;

/**
 * Implements an Action Delegate which responds to a context menu item click<br/>
 * <br/>
 * The delegate posts the selected text to the Mystic Paste webapplication, then places the url for the page
 * where the user can view their paste onto the clipboard.  A "Balloon Tip" is shown after a successful paste.
 *
 * @author Craig Tataryn
 *
 */
public class MysticPasteAction implements IEditorActionDelegate {

	private String selectionText = null;
	private int selectionStart = 0;
	private Shell shell;
	ResourceBundle bundle = null;

	public MysticPasteAction() {
		super();
		bundle = ResourceBundle.getBundle("plugin");
	}

	public void setActiveEditor(IAction action, IEditorPart editorPart) {
		shell = editorPart.getSite().getShell();
	}

	/**
	 * Code which executes when the menu item is clicked.  Code selected is pasted to the Mystic Paste
	 * web application, the view URL is put on the clipboard and an informational message is shown in a
	 * balloon tip.
	 */
	public void run(IAction action) {
		System.out.println("run called");
		if (this.selectionText != null && !this.selectionText.trim().equals("")) {
			//the action is setup in plugin.xml with an ID that ends in .<lang type>
			String type = action.getId().substring(action.getId().lastIndexOf('.') + 1);
			String url = submitPaste(this.selectionText, type);
			Clipboard cb = new Clipboard(this.shell.getDisplay());
			cb.setContents(new Object[] {url}, new Transfer[] {TextTransfer.getInstance()});
			cb.dispose();
			showUrlBox();
		}
	}

	/**
	 * Decides whether or not to enable the mystic paste menu item in the context menu
	 * depending on whether there is selected text.  Unfortunately, because of how eclipse
	 * lazy loads things, this method isn't fired until the menu item is clicked for the
	 * first time, so you can never grey out the item before it is clicked.
	 */
	public void selectionChanged(IAction action, ISelection selection) {
		System.out.println("selectionChanged called");
		if (ITextSelection.class.isAssignableFrom(selection.getClass())) {
			ITextSelection txtSelection = (ITextSelection) selection;
			if (txtSelection == null || txtSelection.isEmpty() || txtSelection.getText().trim().equals("")) {
				action.setEnabled(false);
				this.selectionText = null;
				this.selectionStart = 0;
			} else {
				action.setEnabled(true);
				this.selectionText = txtSelection.getText();
				this.selectionStart = txtSelection.getStartLine();
			}

		}

	}

	/**
	 * Submits the selected text to the Mystic Paste web application.  Web application
	 * urls and other information are stored in the plugin.properties file.<br/>
	 * <br/>
	 * The language type for the selected text is determined by the action's id as setup
	 * in plugin.xml.  For instance, the action for the Java editor will have an id that ends
	 * with .JAVA.  The xml editor's action ID ends in .XML, and so on.  The default is TEXT.
	 * @param content
	 * @param type
	 * @return
	 */
	private String submitPaste(String content, String type) {
		String url = bundle.getString("mysticpaste.url");
		String newPasteContext = bundle.getString("mysticpaste.new");
		String contentParam = bundle.getString("mysticpaste.content.param");
		String langParam = bundle.getString("mysticpaste.language.param");
		String retString = null;

		HttpClient httpClient = new DefaultHttpClient();
		HttpPost post = new HttpPost(url + newPasteContext);
		List <NameValuePair> nvps = new ArrayList <NameValuePair>();
        nvps.add(new BasicNameValuePair(langParam, type));
        nvps.add(new BasicNameValuePair(contentParam, content));

        try {
	        post.setEntity(new UrlEncodedFormEntity(nvps, HTTP.UTF_8));
	        HttpResponse response = httpClient.execute(post);
			HttpEntity entity = response.getEntity();
			retString = EntityUtils.toString(entity, HTTP.UTF_8);
			retString = url + bundle.getString("mysticpaste.view") + retString;
			System.out.println(retString);
		} catch (ClientProtocolException e) {
			e.printStackTrace();
			MessageDialog.openInformation(
					shell,
					"MysticPaste Plug-in",
					"Sorry, we couldn't contact Mystic Paste");
		} catch (IOException e) {
			e.printStackTrace();
			MessageDialog.openInformation(
					shell,
					"MysticPaste Plug-in",
					"Sorry, we couldn't contact Mystic Paste");
		}

		return retString;
	}

	/**
	 * Shows an informational "balloon tip" at the bottom of the screen
	 */
	private void showUrlBox() {
		Rectangle bounds = shell.getDisplay().getPrimaryMonitor().getClientArea();
		ToolTip tip = new ToolTip(shell, SWT.BALLOON | SWT.ICON_INFORMATION);
		tip.setAutoHide(true);
		tip.setText("Your selection has been copied to to Mystic Paste");
		tip.setMessage("The Url is on your clipboard");
		tip.setLocation(bounds.width, bounds.height);
		tip.setVisible(true);

	}
}

```

<h2>Building a Plugin Jar</h2>
Building the plugin jar is pretty simple.  Just go File->Export->Deployable plug-ins and fragments and follow the wizard

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/03/eclipse6.png" alt="Build Jar" title="Build Jar" width="708" height="630" class="aligncenter size-full wp-image-598" />

<h2>Installing the Jar</h2>
Locate where on your file system Eclipse is installed.  Under this directory there should be a "dropins" folder, this is where the jar you built (or downloaded) for the plugin should reside.  Restart Eclipse, and voila!.  <strong>Note</strong>: on a Mac, you'll have to right click on Eclipse.app and choose "Show Package Contents", the dropins folder will then be accessible through the Finder window which pops up.

<h2>Conclusion</h2>
Hopefully this gives you a good idea of how the Mystic Paste Eclipse plugin was built.  It probably took more time explaining than it did actually coding the darn thing.  That being said, programming for Eclipse is not for the faint of heart.  I pretty much "code by debugger" when I have to create Eclipse plugins, a lot of the API is shrouded by interfaces with one method on them, you really never know what the real object is you are dealing with until you inspect it at runtime.

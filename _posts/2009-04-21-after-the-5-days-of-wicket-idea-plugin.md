---
layout: post
status: publish
published: true
title: 'After the 5 Days of Wicket: IDEA Plugin'
author:
  name: Andrew Lombardi
  picture: '/assets/blog/authors/andrew.jpg'
  twitter: kinabalu
  url: https://mysticcoders.com
date: '2009-04-21T09:00:28.0000Z'
comments: true
---
From the beginning of building out <a href="http://www.mysticpaste.com" target="_blank">Mystic Paste</a>, the vision was to have multiple interfaces to allow ubiquitous use of the pastebin. With the variety of plugins we've got out now including Eclipse, IDEA, VIM, and NetBeans, that vision is definitely here. If you reached this link looking for the plugin, look no further, the plugin page has <a href="http://www.mysticpaste.com/plugin" target="_blank">downloads for multiple IDE platforms</a>.

<a id="more"></a><a id="more-782"></a>
For the IDEA plugin we'll be showing how it was developed, so we can remove the 8+ step process of pasting into a simple 3-step process ... select, send, paste.

<h2>Setting up the project</h2>
With IntelliJ IDEA 8.x getting a new plugin project setup couldn't be easier! Launch IDEA and choose File -&gt; New Project... -&gt; Create project from scratch

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/04/picture-2.png" width="480" height="395" alt="Picture 2.png" />

Just as with any project, give it a good name, and for type, select "Plugin Module". Go through the normal project setup details and click Finish when done.

<h4>Plugin configuration</h4>
There is a simple plugin.xml file that governs how IDEA will interact with your masterpiece. The use case for this simple plugin is to capture text selected in an editor and provide an action hook for the right-click menu. I've included the entire plugin.xml file for the mystic paste idea plugin

``` xml

<idea-plugin version="2">
    <name>MysticPastePlugin</name>
    <description>
        <![CDATA[
        This plugin takes a selection from the IDEA editor, and posts it to http://mysticpaste.com.  It then provides the URL in the clipboard
      ]]>
    </description>
    <version>0.2.1</version>
    <vendor url="http://www.mysticcoders.com">Mystic Coders, LLC</vendor>
    <idea-version since-build="8000"/>

    <change-notes>
        <![CDATA[

            <ul>
                <li> Added the notification on the bottom right alerting the user that the paste action was successful</li>
            </ul>


        ]]>
    </change-notes>

    <project-components>
        <component>
            <implementation-class>com.mysticcoders.mysticpaste.plugin.MysticPasteIndicationComponent
            </implementation-class>
        </component>
    </project-components>

    <actions>
        <action id="com.mysticcoders.mysticpaste.plugin.PastebinAction"
                class="com.mysticcoders.mysticpaste.plugin.PastebinAction" text="Add to Mystic Paste..."
                description="Sends the selection to MysticPaste.com">
            <add-to-group group-id="EditorPopupMenu" anchor="last"/>
        </action>
    </actions>

</idea-plugin>

```

The top portion of this file is all identification information for the plugin, version info, vendor, which idea minimum IDEA version it will work with, changelog details. The next 3 elements define:

<ul>
<li>Application Components</li>
<li>Project Components</li>
<li>Actions</li>
</ul>
For a lot more in-depth information on plugin development, the <a href="http://www.jetbrains.net/confluence/display/IDEADEV/Plugin+Development+FAQ" title="Plugin Development FAQ" target="_blank">Plugin Development FAQ</a> is very useful.

<h4>Application Component</h4>
A plugin has many different scopes it can be contained in, and if you're plugin is not project specific, ApplicationComponent scope is a great place to define it. These components are only loaded once when the IDE starts up.

<h4>Project Component</h4>
This component is scoped to be project-specific, so IDEA will make an instance of this plugin available for each open project.

<h4>Actions</h4>
<em>AnAction</em> is used for any code that needs to hook into IDEA's menus and/or toolbars. This is precisely where we'll hook into the right-click menu available to use from the editor, and act upon selected code. In here we define the following:

<ul>
<li>Unique id for our action</li>
<li>Implementation class</li>
<li>Text to show in the popup menu</li>
<li>Description which will show in the status bar</li>
<li>Element add-to-group which defines what popup menu to attach to, and where to anchor it in the list</li>
</ul>
Check out <a href="http://www.jetbrains.com/idea/plugins/plugin_structure.html" title="IntelliJ IDEA Plugin Structure" target="_blank">IDEA plugin structure</a> for much more info.

<h2>Implementation</h2>
Our plugin is really simple, we need to take the current editor selection, open an HTTP connection with MysticPaste.com's plugin servlet, POST the selection text and place the URL in the clipboard. Let's start with getting the editor selection!

``` java
        DataContext context = event.getDataContext();
        Editor editor = DataKeys.EDITOR.getData(context);

        String selectedText = null;
        SelectionModel selection = null;
        if (editor != null) {
            selection = editor.getSelectionModel();
            if (selection.hasSelection()) {
                selectedText = selection.getSelectedText();
            }
        }
```

As you can see from the code snippet, we grab an instance of the Editor using the DataContext which can be retrieved from the AnActionEvent instance passed to every action. From there we get the Editor's SelectionModel, find out if there is indeed a selection, and fill selectedText with the contents.

``` java
    Document doc = editor.getDocument();
    VirtualFile virtualFile = FileDocumentManager.getInstance().getFile(doc);
    String extension = null;
    if (virtualFile.getName().lastIndexOf(".") != -1) {
        extension = virtualFile.getName().substring(virtualFile.getName().lastIndexOf(".") + 1, virtualFile.getName().length());
    }
```

Next up, we want the file extension of the document we've grabbed our selected text. We get an instance of VirtualFile through the Document, substring it to grab what should be the extension, and pass it to the sendPaste method along with the selectedText.

``` java
    private String sendPaste(String text, String extension) {

        String pasteNumber = null;

        try {
            // Construct data
            String data = URLEncoder.encode("content", "UTF-8") + "=" + URLEncoder.encode(text, "UTF-8");

            if(extension != null) {
                data += "&" + URLEncoder.encode("fileExt", "UTF-8") + "=" + URLEncoder.encode(extension, "UTF-8");
            }

            // Send data
            URL url = new URL("http://www.mysticpaste.com/servlet/plugin");
            URLConnection conn = url.openConnection();
            conn.setDoOutput(true);
            OutputStreamWriter wr = new OutputStreamWriter(conn.getOutputStream());
            wr.write(data);
            wr.flush();

            // Get the response
            BufferedReader rd = new BufferedReader(new InputStreamReader(conn.getInputStream()));
            String line;
            while ((line = rd.readLine()) != null) {
                pasteNumber = line;
            }

            wr.close();
            rd.close();
        } catch (Exception e) {
            e.printStackTrace();
        }

        return (pasteNumber != null ? "http://www.mysticpaste.com/view/" + pasteNumber : null);
    }
```

If you're looking to integrate another platform with MysticPaste.com, the code above should provide you all you need. We very simply encode the data for HTTP POST, and use the standard java.net classes to achieve our pasting. The result of this class is a URL in which this paste can be found.

``` java
    CopyPasteManager.getInstance().setContents(new StringSelection(text));
```

After a quick null check, we use IDEA's supplied CopyPasteManager to set the contents to our paste URL.

You can test your app by running it like you would anything else, IDEA provides a target in the Run/Debug configurations to achieve this.


<img src="https://www.mysticcoders.com/wp-content/uploads/2009/04/picture-3.png" width="218" height="417" alt="Picture 3.png" />

IDEA will launch a completely new version of the IDE in which to test your plugin, I would heartily suggest that you create a simple project that can exercise whatever functionality is intended to be used with your plugin.

<h2>Deployment</h2>

<img src="https://www.mysticcoders.com/wp-content/uploads/2009/04/picture-4.png" width="444" height="232" alt="Picture 4.png" />

If you're happy with your debugging efforts, and the functionality works for your needs, under the Build menu select "Prepare Plugin Module ..." and it will build a plugin jar that you can drop into IDEA's plugin directory

<ul>
<li>Windows: C:Documents and Settings&lt;username&gt;.IntelliJIdea80configplugins</li>
<li>OS X: $HOME/Library/Application Support/IntelliJIDEA80</li>
<li>Linux/Unix: $HOME/.IntelliJIdea80/config/plugins</li>
</ul>
That's it. Overall the process isn't too bad once you find all the documentation, grab a few code samples, possibly reverse engineer a few to see how they did it. Have fun, and if you have any questions, or want us to develop a custom IDEA plugin for your organization, <a href="http://www.mysticcoders.com/contact/" title="Mystic Contact Us" target="_top">contact us</a>.

---
layout: post
status: publish
published: true
title: Stress testing OpenLaszlo
author:
  name: Johan Edstrom
  picture: '/assets/blog/authors/jedstrom.jpg'
  url: https://mysticcoders.com
date: '2008-08-08T13:35:21.0000Z'
tags: []
comments: true
---
I was playing with object redrawing in Laszlo (4.0.6).&Acirc;&nbsp;This is what I came up with, &Acirc;&nbsp;it seems I hit flash engine issues above a certain level of attached nodes but it does handle re-draws quite well once rendered.

Here is the code I was playing with and a screenshot....

&Acirc;&nbsp;

[caption id="attachment_76" align="aligncenter" width="300" caption="Interconnected nodes"]<a href="http://www.mysticcoders.com/wp-content/uploads/2008/08/graphs.png"><img class="size-medium wp-image-76" src="http://www.mysticcoders.com/wp-content/uploads/2008/08/graphs.png" alt="Interconnected nodes" width="300" height="268" /></a>[/caption]

<a id="more"></a><a id="more-75"></a>

``` xml
<?xml version=&acirc;&euro;1.0&acirc;&euro;&sup3; encoding=&acirc;&euro;UTF-8&acirc;&euro;&sup3;?>
<!DOCTYPE canvas SYSTEM &acirc;&euro;&oelig;http://www.openlaszlo.org/lps4/tools/lzx.xsd&acirc;&euro;>

<canvas width=&acirc;&euro;100%&acirc;&euro; height=&acirc;&euro;100%&acirc;&euro;>

<attribute name=&acirc;&euro;allNodes&acirc;&euro; value=&acirc;&euro;${new Array()}&acirc;&euro;/>
<attribute name=&acirc;&euro;nodeGroups&acirc;&euro; value=&acirc;&euro;${new Array()}&acirc;&euro;/>

<resource name=&acirc;&euro;node1&acirc;&euro;&sup3;>
   <frame src=&acirc;&euro;CS2-Standard-48&Atilde;&mdash;48.png&acirc;&euro;/>
</resource>

<resource name=&acirc;&euro;node2&acirc;&euro;&sup3;>
    <frame src=&acirc;&euro;InDesign-48&Atilde;&mdash;48.png&acirc;&euro;/>
</resource>

<resource name=&acirc;&euro;node3&acirc;&euro;&sup3;>
    <frame src=&acirc;&euro;CS2-Premium-48&Atilde;&mdash;48.png&acirc;&euro;/>
</resource>

<resource name=&acirc;&euro;group&acirc;&euro;>

    <frame src=&acirc;&euro;Illustrator-128&Atilde;&mdash;128.png&acirc;&euro;/>

</resource>

<!&acirc;&euro;&ldquo; All nodes attach to this view, that way we can iterate over nodes and nodes only&acirc;&euro;&brvbar;. &acirc;&euro;&ldquo;>

<view name=&acirc;&euro;nodeCanvas&acirc;&euro;/>

<class name=&acirc;&euro;linkedLine&acirc;&euro;>

    <attribute name=&acirc;&euro;style&acirc;&euro; value=&acirc;&euro;0&Atilde;&mdash;00ff00&acirc;&euro;&sup3;/>
        <attribute name=&acirc;&euro;myWidth&acirc;&euro; value=&acirc;&euro;2&acirc;&euro;&sup3;/>

            <attribute name=&acirc;&euro;mc&acirc;&euro;/>

                <handler name=&acirc;&euro;oninit&acirc;&euro;>

                    this.mc = this.getMCRef().createEmptyMovieClip(&acirc;&euro;myLine&acirc;&euro;, 10);

                </handler>

                <method name=&acirc;&euro;drawline&acirc;&euro; args=&acirc;&euro;x1,y1,x2,y2&acirc;&euro;&sup3;>

                    Debug.write(&acirc;&euro;Moving line&acirc;&euro;);

                    Debug.write(this);

                    this.sendToBack();

                    mc.clear();

                    mc.lineStyle(this.myWidth, this.style,100);

                    mc.moveTo(x1,y1);

                    mc.lineTo(x2,y2);

                    Debug.write(this);

                </method>

                <method name=&acirc;&euro;alarm&acirc;&euro;>
                    // Alarm behaviour.
                </method>

</class>

<class name=&acirc;&euro;subMap&acirc;&euro; extends=&acirc;&euro;view&acirc;&euro;>
</class>

<class name=&acirc;&euro;nodeLabel&acirc;&euro; extends=&acirc;&euro;text&acirc;&euro;>

    <method name=&acirc;&euro;setLabel&acirc;&euro; args=&acirc;&euro;labelText&acirc;&euro;>
        this.setText(labelText);
    </method>

</class>

<class name=&acirc;&euro;nodeStatus&acirc;&euro;/>

<class name=&acirc;&euro;nodeGroup&acirc;&euro; extends=&acirc;&euro;view&acirc;&euro; resource=&acirc;&euro;group&acirc;&euro; visible=&acirc;&euro;false&acirc;&euro;>

    <attribute name=&acirc;&euro;nodes&acirc;&euro;/>

        <handler name=&acirc;&euro;onmouseover&acirc;&euro;>

            this.info.bringToFront();

            this.info.setVisible(true);

        </handler>

        <method name=&acirc;&euro;alarm&acirc;&euro;>
        </method>

        <handler name=&acirc;&euro;onmouseout&acirc;&euro;>

            this.info.setVisible(false);

        </handler>

        <!&acirc;&euro;&ldquo; A list of the existing attributes &acirc;&euro;&ldquo;>

        <floatinglist name=&acirc;&euro;info&acirc;&euro; width=&acirc;&euro;80&acirc;&euro;&sup3; attach=&acirc;&euro;right&acirc;&euro; visible=&acirc;&euro;false&acirc;&euro;>

            <textlistitem text=&acirc;&euro;Grouping&acirc;&euro;/>

                <textlistitem text=&acirc;&euro;This is a group...&acirc;&euro;/>

                <textlistitem text=&acirc;&euro;Bla&acirc;&euro;/>

                <textlistitem text=&acirc;&euro;Bla&acirc;&euro;/>

        </floatinglist>

        <!&acirc;&euro;&ldquo; Hide the nodes below this node-group, assume the

        position of the first node, move it around, add even if

        we move to the underlaying nodes the x/y coords so we can

        uncover as quickly as possible and revert them back to visible

        state on the canvas. This includes unhiding lines as well.

        This class should self destruct when unraveling the nodes in question.

        &acirc;&euro;&ldquo;>

        <method name=&acirc;&euro;addNodes&acirc;&euro; args=&acirc;&euro;nodes&acirc;&euro;><![CDATA[

            this.nodes = nodes //Save a reference....

            this.setX(nodes[nodes.length-1].x - this.width/2);

            this.setY(nodes[nodes.length-1].y - this.height/2);

            for (n in nodes) {
                nodes[n].hide();
            }

            this.setVisible(true);

            var linkList = new Array();

            Debug.write(&acirc;&euro;Current nodes&acirc;&euro; )

            for (n in nodes) {

                for (c in nodes[n].connected) {

                    linkList.push(nodes[n].connected[c].l);

                }

            }

            var links = linkList.unique(true);

            Debug.write(linkList);

            Debug.write(links);

            Debug.write(nodes);

            for (l in links ) {

                var firstHit = false;

                var secondHit = false;

                for (n in nodes) {

                    for (c in nodes[n].connected) {

                        if (nodes[n].connected[c].l == links[l]){

                            if (firstHit) {

                            secondHit = true;

                            }

                        firstHit = true;

                    }

                }

            }

            if (!(firstHit && secondHit)) {

                links[l].setVisible(true);

            }

            }

            ]]>

        </method>

        <handler name=&acirc;&euro;onclick&acirc;&euro;>

            for (n in nodes) {

                nodes[n].show()

            }

            this.setVisible(false);

            this.destroy();

        </handler>

</class>

<class name=&acirc;&euro;onmsNode&acirc;&euro; extends=&acirc;&euro;view&acirc;&euro;

       resource=&acirc;&euro;node1&acirc;&euro;&sup3;

       onmousedown=&acirc;&euro;dragger.apply()&acirc;&euro; onmouseup=&acirc;&euro;dragger.remove()&acirc;&euro;>

    <attribute name=&acirc;&euro;connected&acirc;&euro;/>

        <attribute name=&acirc;&euro;myLabel&acirc;&euro;/>

            <!&acirc;&euro;&ldquo; myStatus

            A bunch of resources where we set it based on

            response number value from the service - i.e are we going

            all is good or bleat like mad sheep&acirc;&euro;&brvbar;

            &acirc;&euro;&ldquo;>

            <attribute name=&acirc;&euro;myStatus&acirc;&euro;/>

                <dragstate name=&acirc;&euro;dragger&acirc;&euro;/>

                    <method event=&acirc;&euro;oninit&acirc;&euro;>

                        this.connected = new Array();

                        this.myLabel = new nodeLabel();

                        this.myLabel.setLabel(&acirc;&euro;Nodeinfo&acirc;&euro;);

                    </method>

                    <handler name=&acirc;&euro;onload&acirc;&euro;>

                        Debug.write(this.myLabel);

                    </handler>

                    <method name=&acirc;&euro;move&acirc;&euro;>

                        for(a in connected) {

                            connected[a].l.drawline(this.x+(this.width/2),this.y+(this.height/2),

                            connected[a].n.x + (connected[a].n.width/2),connected[a].n.y + (connected[a].n.height/2));

                        }

                        this.myLabel.setX(this.x);

                        this.myLabel.setY(this.y+this.height+2);

                    </method>

                    <method event=&acirc;&euro;ony&acirc;&euro;>

                        move();

                    </method>

                    <method event=&acirc;&euro;onload&acirc;&euro;>

                        move();

                    </method>

                    <method name=&acirc;&euro;link&acirc;&euro; args=&acirc;&euro;n,l&acirc;&euro;>

                        var nodeAff = new Object();

                        nodeAff.n = n;

                        nodeAff.l = l;

                        this.connected.push(nodeAff);

                        move();

                        Debug.write(this.connected);

                    </method>

                    <handler name=&acirc;&euro;onmouseover&acirc;&euro;>

                        this.info.bringToFront();

                        this.info.setVisible(true);

                    </handler>

                    <method name=&acirc;&euro;alarm&acirc;&euro;>

                    </method>

                    <handler name=&acirc;&euro;onmouseout&acirc;&euro;>

                        this.info.setVisible(false);

                    </handler>

                    <!&acirc;&euro;&ldquo; A list of the existing attributes &acirc;&euro;&ldquo;>

                    <floatinglist name=&acirc;&euro;info&acirc;&euro; width=&acirc;&euro;80&acirc;&euro;&sup3; attach=&acirc;&euro;right&acirc;&euro; visible=&acirc;&euro;false&acirc;&euro;>

                        <textlistitem text=&acirc;&euro;NodeId&acirc;&euro;/>

                            <textlistitem text=&acirc;&euro;NodeName&acirc;&euro;/>

                                <textlistitem text=&acirc;&euro;Bla&acirc;&euro;/>

                                    <textlistitem text=&acirc;&euro;Bla&acirc;&euro;/>

                    </floatinglist>

                    <handler name=&acirc;&euro;ondblclick&acirc;&euro;>

                        // Provide edit behaviour of the node.

                    </handler>

                    <method name=&acirc;&euro;hide&acirc;&euro;>

                        this.setVisible(false);

                        this.myLabel.setVisible(false);

                        for (c in this.connected) {

                            connected[c].l.setVisible(false)

                        }

                    </method>

                    <method name=&acirc;&euro;show&acirc;&euro;>

                        this.setVisible(true);

                        this.myLabel.setVisible(true);

                        for (c in connected) {

                            connected[c].l.setVisible(true)

                        }

                    </method>

</class>

<method event=&acirc;&euro;oninit&acirc;&euro;> <![CDATA[

    var baseNodes = new Array();

    for(var ii = 0; ii <=10 ; ii++ ) {

        var baseNode = new onmsNode(nodeCanvas);

        baseNodes.push(baseNode);

        if (ii < 10 ) {

            baseNode.setX( Math.round(600 + Math.cos(ii * (Math.PI/5)) * 400));

            baseNode.setY(Math.round(500 + Math.sin(ii * (Math.PI/5)) * 400));

            baseNode.setAttribute('resource', 'node2');

        } else {

            baseNode.setX( Math.round(600 + Math.cos(ii * (Math.PI/5)) * 150));

            baseNode.setY(Math.round(500 + Math.sin(ii * (Math.PI/5)) * 150));

            baseNode.setAttribute('resource', 'node3');

        }

        var linkColor = randomColor();

        var myWidth = Math.random()*3+1

        var y = baseNode.y;

        var x = baseNode.x;

        var intervalCount = 0;

        if (ii > 0) {

        var l2 = new linkedLine();

        l2.style = 0xaaaaaa;

        l2.myWidth = 5;

        baseNodes[ii].link(baseNodes[ii-1],l2);

        baseNodes[ii-1].link(baseNodes[ii],l2);

    }

    for (var i = 0; i <= 15 ; i++) {

        var l = new linkedLine();

        l.style = linkColor;

        l.myWidth = myWidth;

        var n = new onmsNode(nodeCanvas);

        x = Math.round(baseNode.x + Math.cos(intervalCount * (Math.PI/(15/2))) * 100);

        y = Math.round(baseNode.y + Math.sin(intervalCount * (Math.PI/(15/2))) * 100);

        intervalCount++

        n.setX(x);

        n.setY(y);

        Debug.write(n);

        Debug.write(l);

        baseNode.link(n,l);

        n.link(baseNode,l);

        canvas.allNodes.push(n);

    }

    canvas.allNodes.push(baseNode);

    oldnode = baseNode;

    }

    ]]>

</method>

<!&acirc;&euro;&ldquo; GLobal mouse tracker &acirc;&euro;&ldquo;>

<text name=&acirc;&euro;globalText&acirc;&euro; resize=&acirc;&euro;true&acirc;&euro;>

    <attribute name=&acirc;&euro;x1&acirc;&euro;&sup3;/>

        <attribute name=&acirc;&euro;y1&acirc;&euro;&sup3;/>

            <attribute name=&acirc;&euro;x2&acirc;&euro;&sup3;/>

                <attribute name=&acirc;&euro;y2&acirc;&euro;&sup3;/>

                    <handler name=&acirc;&euro;onmousedown&acirc;&euro; reference=&acirc;&euro;LzGlobalMouse&acirc;&euro;>

                        // this.setText(&acirc;&euro;Mousedown &acirc;&euro; + getMouse(&acirc;&euro;&trade;x') + &acirc;&euro; &acirc;&euro; + getMouse(&acirc;&euro;&trade;y'));

                        this.x1 = getMouse(&acirc;&euro;&trade;x');

                        this.y1 = getMouse(&acirc;&euro;&trade;y');

                    </handler>

                    <handler name=&acirc;&euro;onmouseup&acirc;&euro; reference=&acirc;&euro;LzGlobalMouse&acirc;&euro;>

                        <![CDATA[

                        //this.setText("Mouseup " + getMouse('x') + " " + getMouse('y'));

                        this.x2 = getMouse('x');

                        this.y2 = getMouse('y');

                        var blurThese = new Array();

                        for (n in canvas.nodeCanvas.subviews) {

                            //Debug.write(x1,y1,x2,y2);

                            //Debug.write(canvas.nodeCanvas.subviews[n].x,canvas.nodeCanvas.subviews[n].width,canvas.nodeCanvas.subviews[n].y,canvas.nodeCanvas.subviews[n].height);

                            if (canvas.nodeCanvas.subviews[n].x > this.x1

                            && canvas.nodeCanvas.subviews[n].x + canvas.nodeCanvas.subviews[n].width < this.x2

                            && canvas.nodeCanvas.subviews[n].y > this.y1

                            && canvas.nodeCanvas.subviews[n].y + canvas.nodeCanvas.subviews[n].height < this.y2

                            ){

                            blurThese.push(canvas.nodeCanvas.subviews[n]);

                            }

                        }

                        // Finish up and mark these as a group&acirc;&euro;&brvbar;..

                        if (blurThese.length > 1) {

                            var hide = new nodeGroup();

                            hide.addNodes(blurThese);

                        }

                        ]]>

                    </handler>

</text>

<script>

    <![CDATA[

    function randomColor() {

        var array = new Array("f", "e", "d", "c", "b", "a", "9", "8", "7", "6", "5", "4", "3", "2", "1");

        var endHex = "0x";

        for (var i = 0; i < 6; i++) {

            endHex += array[Math.round(Math.random() * array.length)]; // and each time add a new character to the returned color.

        }

        return endHex;

    }

    // Find things in Arrays&acirc;&euro;&brvbar;

    Array.prototype.find = function(searchStr) {

        var returnArray = false;

        for (i = 0; i < this.length; i++) {

            if (typeof(searchStr) == &acirc;&euro;&tilde;
            function&acirc;&euro;&trade;)
        {

            if (searchStr.test(this[i])) {

                if (!returnArray) {
                    returnArray = []
                }

                returnArray.push(i);

            }

        }
        else
        {

            if (this[i] === searchStr) {

                if (!returnArray) {
                    returnArray = []
                }

                returnArray.push(i);

            }

        }

        }

        return returnArray;

    }

    // Array.unique( strict ) - Remove duplicate values

    Array.prototype.unique = function(b) {

        var a = [], i, l = this.length;

        for (i = 0; i < l; i++) {

            if (a.indexOf(this[i], 0, b) < 0) {
                a.push(this[i]);
            }

        }

        return a;

    };

    // Array.indexOf( value, begin, strict ) - Return index of the first element that matches value

    Array.prototype.indexOf = function(v, b, s) {

        for (var i = +b || 0, l = this.length; i < l; i++) {

            if (this[i] === v || s && this[i] == v) {
                return i;
            }

        }

        return -1;

    };

    ]]>

</script>
</canvas>

```

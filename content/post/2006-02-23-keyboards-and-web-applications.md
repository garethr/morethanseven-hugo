---
comments: true
created_at: 2006/02/23}
date: 2006-02-23T00:00:00Z
title: keyboards and web applications
url: /2006/02/23/keyboards-and-web-applications/
---

<p>
Web applications are still evolving. This we know. One thing any power user tends to pick up on pretty quickly in desktop applications is the keyboard shortcuts. We just cant get enought of them. Why dont we see them more in web applications? Well a couple of reasons as it happens:

</p>
<ul>
<li>
Potential clashes with browser or OS shortcuts

</li>
<li>
Many web applications are still evolving from more promotional websites. The learning curve for a website <em>needs</em> to be instantaneous, otherwise your potential customer just left. But what about a web application? The users may <em>have</em> to use it, as well as having documentation and maybe even training.

</li>
<li>
Because we dont see them in web applications? The circular argument.

</li>
</ul>
<p>
Personally I think in some cases keyboard shortcuts would be nice, I have a little plan for something in mind as well so started experimenting. The clash issue I’m going to leave for another post as it will take a bit of brute force experimenting but I did start playing with a simple way of assigning functions to keys.

</p>
<p>
The following code creates an object to store the lookup/hash/two dimensional array (pick one you like). This is a simple associate between a key, and a function to run when it’s pressed. A function is then assigned to the onkeyup and onkeydown events which monitors them, checks for one of the keys in the lookup to be pressed (in this case along with the ctrl key) and runs the assigned function.

</p>
<div>
<code>

    &lt;txp:php&gt;
    include('/www/htdocs/morethanseven/scripts/sample/keyPress.js');
    &lt;/txp:php&gt;

</code>

</div>
<p>
A simple library function is used to add the event to the document onload. My library obviously includes other helper functions but we dont need any of those for this example.

</p>
<div>
<code>

    &lt;txp:php&gt;
    include('/www/htdocs/morethanseven/scripts/sample/lib.js'); 
    &lt;/txp:php&gt;

</code>

</div>
<p>
Some improvements I can spot from here which I’ll implement as I actually take this out of the lab and into something production like.

</p>
<ul>
<li>
JSON (or YAML) data storage. Either one makes configuration simpler and scores geek points. It’s a win win.

</li>
<li>
Abstract the use of the ctrl key from the function and allow other modifiers to be set when used.

</li>
</ul>
<p>
I’ll try get round to the look at keyboard shortcut clashes, or link to someone who already has, as I get round to it. In the mean time, what do people think about the use of keyboard shortcuts in web applications? Any good or bad examples welcome.

</p>

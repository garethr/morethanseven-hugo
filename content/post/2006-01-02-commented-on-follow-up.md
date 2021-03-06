---
comments: true
created_at: 2006/01/02}
date: 2006-01-02T00:00:00Z
title: '@Commented-on Follow up'
url: /2006/01/02/commented-on-follow-up/
---

<p>
Since the <a href="http://morethanseven.net/posts/commented-on">brief discussion</a> based around <a href="http://www.ben-ward.co.uk">ben’s</a> original idea for using <a href="http://del.icio.us">del.icio.us</a> to track comments you have made on other blogs I’ve been having a play.

</p>
<p>
The main issue that people saw was simplicity, which I see falling under two headings:

</p>
<ul>
<li>
Ideally it should just happen. If you comment somewhere (and opt in, obviously) you browser or online service should keep a track and give you access to this data. This would require changes to existing blogs and in particular the common software like <a href="http://www.textpattern.com">textpattern</a> or <a href="http://wordpress.org">the other one</a>

</li>
<li>
Saving that for later, a simple one click service which does not get in the way and achieves the same thing should be suitable.

</li>
</ul>
<p>
It’s the later that I’ve been playing with and thought I’d present here in it’s early stages. The plan is as follows:

</p>
<ul>
<li>
A <a href="http://en.wikipedia.org/wiki/Bookmarklet">bookmarklet</a> collects the data from the page (title and url) and sends it to an intermediary page, rather than del.icio.us.

</li>
<li>
The intermediary page prompts for a username and password (HTTP authentication) and adds the post to del.icio.us automatically.

</li>
<li>
The page is then either closed or redirected back to the page from whence the request came.

</li>
</ul>
<h3>
The bookmarklet

</h3>
<p>
The bookmarklet section is simple enought modifications of the existing del.icio.us bookmarklets. See the download at the end of this post for details.

</p>
<h3>
Intermediary

</h3>
<p>
The intermediary page uses HTTP authentication to ask for a del.icio.us username and password (note that I’m not storing these at all, or for that matter at present checking if they are correct.) This needs only happen the first time you use the page if you use your browser to store the details or once per browser session otherwise.

</p>
<p>
The page then makes a request to the <a href="http://del.icio.us/help/api/">del.icio.us API</a> using the lovely PHP library from <a href="http://dietrich.ganx4.com/delicious/">dietrich.ganx4.com/delicious/</a>

</p>
<h3>
Return

</h3>
<p>
On a failed request the page reports that an error has occured (I’ll expand on this with more pertinent error messages as time permits.)

</p>
<p>
On success however we have two options, depending on the bookmarklet used. The default is to close the page down, with an alternative beiong to redirect back to the refering page. We do however run into a slight problem here with Firefox. Our favourite browser will only allow windows to be closed via javascript if they where opening by a script (NOT including our bookmarket). Internet Explorer has a similar set up where it prompts the user for an action, although a <a href="http://www.guyfromchennai.com/?p=18">simple fudge</a> get’s us around that one. At the moment therefore the close window bookmarklet is of limited usage for Firefox users.

</p>
<h3>
Future

</h3>
<p>
This could quite easily be expanded for use on a blog as the requests are simple HTTP requests with query string parameters. However the security implications of giving out your username and password would likely limit this approach. I’m going to look into the sending of bookmarks to others that ben mentioned and see if that can be used here – as hopefully that may only require you to give out your username. A service like <a href="http://www.gravatar.com/">gravatar</a> could then be used to do lookups between email addresses and del.icio.us usernames and away we go.

</p>
<p>
Any thoughts, comments or suggestions welcome. And feel free to try out the bookmarklets. I’ve included a handy download below including a quick readme and the bookmarklets.

</p>
<p>
<a href="http://morethanseven.net/commented-on/commented-on.zip">download @Commented-on pack</a>

</p>

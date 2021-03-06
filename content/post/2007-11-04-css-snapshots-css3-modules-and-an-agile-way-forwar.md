---
comments: true
created_at: 2007/11/04}
date: 2007-11-04T00:00:00Z
title: CSS Snapshots, CSS3 Modules and an Agile Way Forward
url: /2007/11/04/css-snapshots-css3-modules-and-an-agile-way-forwar/
---

The state of CSS has been a common topic of conversation this year. Ever since [Andy Budd](http://www.andybudd.com) stirred things up at [The Highland Fling](http://thehighlandfling.com) about [The Future of CSS](http://thehighlandfling.com/sessions/the-future-of-css/) and followed up with a [call for CSS 2.2](http://www.andybudd.com/archives/2007/05/css22/) we've been wanting more.

Well, in response to all this the CSS3 working group have released [CSS Snapshot 2007](http://www.w3.org/TR/css-beijing/) as a working draft. CSS3, in theory, has a modular structure. The idea behind this was that individual pieces could be worked up, specified and released without having to work on the whole thing at once. Sounds a lot like iterative development to me. The problem is we haven't, until now, had a stable *release one* of CSS3.

> At the time this specification enters Candidate Recommendation, Cascading Style Sheets (CSS) is defined by the following specifications:

-   CSS Level 2 Revision 1 (including errata)
-   Selectors Level 3
-   CSS Namespaces
-   CSS Color Level 3

So (at least when this document reaches Candidate Recommendation at some unknown point in the future), we can get on and use all those selectors we keep eyeing up. Colors and Namespaces are not particularly interesting, at least to me, but useful non-the-less. CSS2.1 is important as a baseline going forward, especially for the browser makers, rather than being particular interesting for the jobbing web designer.

The problems come when we look at some of the features we want, and the modules they are part of. Andy specifically references [text-shadow](http://andybudd.com/archives/2007/10/snapshot_2007_i/) as something that's already got more than two sample implementations but is part of the [Text module](http://www.w3.org/TR/css3-text/) which we won't see for a while due to it's complexity. (As a side note text-shadow was formerly part of CSS2.1 but got dropped due to lack of implementations at the time.) [Dave Storey](http://my.opera.com/dstorey/blog/) summed this problem up nicely over on [CSS3.info](http://www.css3.info/css-snapshot-2007-released-as-a-working-draft/#comment-59776)

> I guess this is due to not wanting to put partial parts of a module in this snapshot. Iâ€™d like to see another snapshot draft in around six months that includes full or partial modules that have progressed since the time this snapshot was published.

From a quick look at the [current work](http://www.w3.org/Style/CSS/current-work) of the CSS working group I count six modules at candidate recommendations, six at last call, twenty three at working draft and six not yet started. If modules really are discrete blocks then this to me looks like a recipe for disaster. If modules aren't discrete blocks then why are they divided into modules? Rather than focus on shipping modules it appears that the effort of the working group has been spread out across nearly the whole spectrum of CSS3. This pretty much invalidates the idea of a modular approach.

CSS3 is a big project. And specification, like software, is hard. In well organised and managed agile software teams it's common to split things down into features, or groups of features (modules). The CSS working group did this. In a well managed agile team shipping something early is hugely important. Everyone focuses on that goal, rather than on their favorite part of the project that might not be needed until later on. The CSS working group definitely didn't do that. But it's what I think is needed now.

The working group are now [starting to talk](http://xhtml.com/en/css/conversation-with-css-3-team/) more openly about what they are up to. And the [CSS Eleven](http://csseleven.com/) are looking to add some support from a web design industry point of view. I'm interested to see what they come up with as suggestions and support. The problem I see is that, unless the working group back down on the strict nature of the modules and define sub-modules or simply go to specifying features then we could hit an impasse. The best course of action (from a complete outsider anyhow) would be to get all the people from the working group and from the CSS Eleven working on one module at a time. Only once a little momentum has been built up maybe they could work on more than one at once, just not thirty five this time. All of that would take some hefty project management and some setting aside personal and organisational politics to get done. Fingers crossed.

I actually think things are looking up, but time is certainly a factor here. Lets assume IE.next is released late next year or the start of 2009 (wild speculation). If the [Internet Explorer team](http://blogs.msdn.com/ie/) implement to the current specs at that time - that could easily not include CSS Snapshot 2008, which would be reaching candidate recommendation around about the same time as they ship. Which could in theory mean it's four years or more before we see text-shadow well supported. Never mind elements of CSS3 which currently have no reference implementations at all! Anyone else with any bright idea? Or insider info that you want to share?

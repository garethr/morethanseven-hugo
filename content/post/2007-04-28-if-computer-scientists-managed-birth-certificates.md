---
comments: true
created_at: 2007/04/28}
date: 2007-04-28T00:00:00Z
title: If computer scientists managed birth certificates
url: /2007/04/28/if-computer-scientists-managed-birth-certificates/
---

I've been pondering [authority in microformats](http://morethanseven.net/posts/authoritative-hcards-authority-in-microformats/) and as a result been taking a closer look at [microID](http://microid.org).

The situation I want to find a solution for is; If their are lots of published hcards for a given person they might be different, either out of date or more sinisterly fraudulent. If I do a search for contact details, lets say using the [technorati hcard search engine](http://kitchen.technorati.com/contact/search/), how do I know which one is to be trusted?

If I publish an hcard on [my site](http://morethanseven.net) using the following markup, including a microID using the included email and url details to form the hash.

    <code><div 
       class="vcard microid-mailto+http:sha1:a52b4f86cfa4190b53634d99198c0f56970c8d83">
       <h2 class="fn">gareth rushgrove</h2>
       <a href="http://morethanseven.net" class="url" rel="me">
          morethanseven.net
       </a>
       <a href="mailto:gareth@morethanseven.net" class="email">
          gareth@morethanseven.net
       </a>
    </div></code>

If the hcard search engine reads that hcard on the morethanseven.net domain it could check the hash in the vcard against doings it's own hash with the specified email address and url (checking the url is the same as the one on which the vcard lives). "Ah ha". It can say. This looks like an authoritive hcard.

This does however raise a couple of issues. In order for the search engine or external reader to be able to create its hash you have to include the email in your hcard. I'm also not sure it does solve the issue of evil me:

    <code><div 
       class="vcard microid-mailto+http:sha1:40b46e5c349453bddd0dbd022fb33488494699cb">
       <h2 class="fn">evil gareth rushgrove</h2>
       <a href="http://spammer.com" class="url" rel="me">
          spammer.com
       </a>
       <a href="mailto:gareth@spammer.com" class="email">
          gareth@spammer.com
       </a>
    </div></code>

Assuming the above is published on spammer.com then I'm sunk. It's another authorative hcard with the same name, in this case *my* name. Of course therein lies another problem, when we name our children we tend to just do it on a whim, we even copy names we like from other people! And we change or subvert these names when we dont like them with nick names and the like. Domain names are unique so we avoid this problem.

So, a possible solution does present itself; using the uniqueness of domain names combined with the fact that most people aren't evil spammers. Lets postulate a search engine finds lots of hcards for a given name. Most of these hcards probably point back to the url of the entity they are describing (so most hcards on the web describing me point back to this site). We can then check if one of the hcards is from that domain, and if it has a valid microID. In other words we use the lazy web to identify the most likely location of an authorative hcard.

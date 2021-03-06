---
comments: true
created_at: 2009/05/21}
date: 2009-05-21T00:00:00Z
title: Universal Internet Explorer 6 CSS and different types of sites
url: /2009/05/21/universal-ie6-css-and-different-types-of-sites/
---

Andy Clarke, as only he can, has started something of a slagging match with his proposals for a [single, central IE6 stylesheet](http://forabeautifulweb.com/blog/about/universal_internet_explorer_6_css/). My first impression was that this is basically a much better version of the browser defaults.

Between backslapping and shouts of heresy there are a few good comments floating on the post so far (I'd expect more). But most of them seem to assume only two types of website exist:

1.  Websites for *clients*. You know, business to business or business to consumer sorts of things. Online stores, radio station websites, newspapers, company brochures, etc.
2.  Personal blogs

Obviously that covers only a fraction of web pages, but it covers a much larger proportion of web sites. What do I mean by that?

1.  Admin interfaces. Available all over the place and often used by a very small number of people.
2.  Intranets. If your company default browser isn't IE6 then don't spend more time that you have to supporting it.
3.  Internal application interfaces. Everything from holiday to payroll to IT helpdesk requests are often built as well apps. Again if the audience using them isn't using IE6 then don't waste you're time.

Most people (ok so this might be my opinion) seem to work within small to medium sized agency style places. Smallish companies need to rely more on employees as part of their marketing effort, younger people tend to be more militant and people working for smaller organisations (like Andy) tend to benefit more from a little celebrity. But that ignores an army of people who work in house or in other types of company that just happen to build top notch web sites or applications. I've now worked in everything from small agencies, via medium sized agencies to freelance and inhouse. They are all different and all place different types of time pressure on the people involved.

Another argument to be had here is summed up by Sion ([http://twitter.com/sionnnn/status/1872141409](http://twitter.com/sionnnn/status/1872141409))

> if u write code with all browsers in mind from the offset it doesn't take any longer though? hows that commercially unsound?

And this is pretty much how I used to work. But this means ignoring whole swathes of the unsupported parts of CSS2 and in particular CSS3. CSS3 doesn't really allow you to make designs that you couldn't make before (ok, that's a little unfair, maybe) but it does allow you to do what you did before *much more efficiently*. Multi-column layout stuff, multiple background, rounded corners, opacity, RGBa. Smaller stylesheets are easier and quicker to write, test and maintain. And sometimes the time saved is worth more than the additional overhead of writing and maintaining CSS for IE6. Maybe not for a decent sized consumer project with a reasonable team and a few hundred thousand budget. Maybe not for a local council or government site. But for a surprisingly large range of other types of projects this might be a worthwhile approach.

Something else I think that comes through in many of the comments is that it's often seen as the web designers job to fight for things. So we fight to ensure time is spend on making something accessible. We fight to make sure we use valid code wherever possible. We fight for sensible fallbacks when javascript isn't available. But we also get accused of not being able to see both sides and at times being unrealistic. Worth considering with regards peoples initial reactions to this, mine included.

The only thing I would say to anyone looking to use this is don't create your own version. Use the [one from google code](http://code.google.com/p/universal-ie6-css/). As Andy suggests, do suggest improvements, ideally by making the changes yourself presumably along with a solid test case. The time saving benefits basically disappear if we all have to maintain our own versions, before that means we have to test our own versions.

I'm not saying *ignore IE6*, and neither is Andy (I don't think). I'm saying pick your battles. I've build websites to support everything under the sun. Sometimes it's been absolutely the right thing to do, sometimes in hindsight it's probably been a little bit of wasted time. I'm sure you can think of similar times from the sites you've been involved in.

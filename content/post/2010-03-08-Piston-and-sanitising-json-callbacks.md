---
comments: true
created_at: 2010/03/08
date: 2010-03-08T00:00:00Z
excerpt: null
title: Piston And Sanitising Json Callbacks
url: /2010/03/08/Piston-and-sanitising-json-callbacks/
---

I'm a big fan of [Piston](http://bitbucket.org/jespern/django-piston), the django app for creating RESTful web services. As part of a project at work I ended up looking through the source code, mainly at some of the neat tricks of serialisation of objects. While poking around I came across something in my mind that wanted fixing. This being open source rather than just file a bug report I setup a [bitbucket](http://bitbucket.org) account and got hacking.

<% syntax_colorize :python, type=:coderay do %>
def render(self, request):
	cb = request.GET.get('callback')
        seria = simplejson.dumps(self.construct(),
		cls=DateTimeAwareJSONEncoder,
		ensure_ascii=False, indent=4)

        # Callback
        if cb:
            return '%s(%s)' % (cb, seria)

        return seria
<% end %>

Can you spot the problem? Note the use of the callback passed in the query string arguments and then used *without any checking* in the output.

What we really want to do is something like this:

<% syntax_colorize :python, type=:coderay do %>
if is_valid_jsonp_callback_value(cb):
<% end %>

Which is exactly what has just [gone into the code for Piston](http://bitbucket.org/jespern/django-piston/changeset/f558b2c66dcc/). [This article](http://tav.espians.com/sanitising-jsonp-callback-identifiers-for-security.html) contains lots of background information about why JSONP callbacks can be a security hole, and helpfully provides a [nice Python module](http://github.com/tav/scripts/blob/master/validate_jsonp.py) to help with the sanitisation. Nice to be on the authors list for something I'm using actively.

Thanks to [Jesper Noehr](http://twitter.com/jespern) for Piston, for some pointers on bitbucket and for quickly taking the patch. If you're accepting a callback on your site or application, especially if it's a public service, you really want to do something like this or you just might have an exploitable security hole.

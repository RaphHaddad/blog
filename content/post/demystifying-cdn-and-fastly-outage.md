---
title: 'Demystifying CDN and the Fastly outage'
date: 2021-06-10T00:00:00.000+10:00
draft: false
tags : [security, demystifying]
---

Recently, the media has been reporting on the [fastly
outage](https://www.cnbc.com/2021/06/08/reddit-and-global-news-sites-go-offline.html)
that had caused a raft of high traffic websites to go down. These websites include:
Reddit, Amazon, CNN, and the New York Times. The scale of this list meant that
a large portion of the internet was partially inaccessible for a small period
of time. A post-mortem of the incident has been published by [fastly on their blog](https://www.fastly.com/blog/summary-of-june-8-outage).

Fastly, is one of the world’s fastest, largest, and growing providers of CDN
services. For a service to be down for such a small period of time, and
bringing down with it such a large portion of the internet is, in someway,
impressive. Similar to my previous post about [Demystifying Cyber Security
Jargon](./demystifying-cyber-security-jargon.md) this post will
attempt to demystify CDN jargon to non-technical people. In the hope that an
understanding of technical concepts become more accessible to more people.

## Context (How a Web Page is Loaded)

Before any explanation of CDN is attempted, an explanation of how a page is
loaded on a browser is needed in order to provide context.

When a human goes to a website such as `https://blog.raph.ws` a series of other
requests is sent to a collection of computers (called servers). These
requests then return responses in the form of content. This content is then put
together on the browser and the page is then rendered to what a human can consume.

## What is a CDN?

CDN stands for “Content Delivery Network” and it provides content to humans who
browse websites. On a rudimentary level, content on the internet can be
split into two distinct categories: static content and dynamic content. Static
content may include things like: images, style-definitions, videos, and
instructions on how elements on a page interact with a human ([JavaScript
code](https://en.wikipedia.org/wiki/JavaScript)).

For example: if an email inbox is browsed, the logo of the provider on the top left can be
thought of static content, and the list of emails in the center is dynamic
content.
The image will not change no matter how many times the page is refreshed,
however, the email list will change depending on when emails are received.

## Why is CDN needed?

The primary reason why CDNs are relied upon is optimising load time of a web
page on the internet. They are primarily used for static content, and can
increase performance (that is decrease the loading time of a web page) of this
content through a variety of techniques.  Many of these are explained below:

### Less Overhead

When dynamic content is requested from a server (for example: a list of emails) that request
will need to go through several other servers in order to receive the list of
emails, even *if* that list of emails has not changed. Each
additional server that is needed, creates an overhead in the form of time to
load the entire web page. This is because *when*
new content is available is unknown to the human sending that request, so each
and every dependant server is needed to return accurate content.

In addition to this overhead, dynamic content needs to execute more code than
static content. For example: determining which
content to return depending on the route requested (a route is anything that comes
after a domain name; on the address bar of this browser, anything
that comes after `https://blog.raph.ws/` is considered a 'route').
Routes are a lot more complicated in dynamic content types because they
are often constructed with a lot more logic associated with them, whereas static
content routes are often simple and have a level of determinism.

Each additional dependant server and each additional code execution adds time to
a web page load, which may result in a degraded user experience.

Static content is different than dynamic content, it does not need additional
code execution or dependant servers, and often, having the same servers deal
with both static content and dynamic content adds an overhead associated when
dealing with static content. This is why having a CDN that's optimised for
static content becomes important to performance. CDNs do not have all the
additional overheads associated with servers optimised for dynamic content, so
they are able to deal with static content in a performant way.

### Global Servers

Dynamic content is costly to replicate. For example: a list of emails often
exists in a small collection of servers. This is because the list is constantly
changing and the compute and storage costs associated with data replication is
high. Static content on the other hand, is not costly to replicate because by
its very nature, it does not change regularly.

CDNs can replicate 'where' this content is stored and store it
on a large number of servers dispersed around the world. When a human requests
content from a CDN, the CDN has at its disposal multiple servers around the
world it can choose. Generally, the closer the server is to the requesting
human, the quicker the content will be retrieved. For example: if a human in
Sydney, Australia is requesting the same content from servers in Melbourne,
Australia and New York, USA (and all other things being equal apart from this
one variable); then that content will be delivered to by the server in
Melbourne, Australia more quickly.

### Browser Caching

In addition to replicating static content on servers around the world, a
replication mechanism happens on the user's computer in the form of 'caching'.
If the same content is requested more than once, more often than not, it is
stored on the human's browser so that they don't need to request it again. Thus
decreasing the overall load time of a website.

For example: websites are generally built using a series of 'code libraries' and
these code libraries are used across *different* websites. For example:
`https://blog.raph.ws` may use the same code library as
`https://www.australia.gov.au`. If both these websites used the *same* CDN to
request the same library, then the website that the human goes to second, will
not need to download the content again as it is 'cached'. This increases the
overall page load time.

## Conclusion

There are several more ways that CDNs can optimise
content loading times apart from the ones outlined above (such as multimedia
optimisation), however, it is not within the remit of this post to explain them all.

Whilst CDNs are great for performance, when they do go down, the websites that use
them will go down with them. I hope this post helped you understand CDNs in a
bit more detail and thank you for reading this post!

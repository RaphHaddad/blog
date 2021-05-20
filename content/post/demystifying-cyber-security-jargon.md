---
title: 'Demystifying Cyber Security Jargon'
date: 2021-05-21T00:00:00.000+00:00
draft: true
tags : [security]
---

IT Professionals are guilty of using complicated jargon for things that can be
explained in a simpler fashion. This post will attempt to demystify cyber
security phrases that are casually thrown about by IT professionals. Its
eventual aim is to educate and aid non-technical people to make informed cyber
security decisions.

## Identity

When IT professionals refer to an "identity" they are referring to something
that reveals information about a real life human.

For example: a person may have a Facebook account, Twitter account, and a Gmail
account. All three of these accounts are "identities" even though they belong to
a single person. The identities a human owns online, often do not know about
each other, even though they belong to the same human.

It is important to highlight that a person's identity online can be faked. That
is, someone may create an identity and claim they are another person. If this
has happened to you, please contact your law enforcement authorities within your
jurisdiction.

## Identity Provider

Where an identity reveals information about a real human. An identity provider
allows various systems to talk to another system (called an "Identity Provider")
to give those systems the abilities to query an identity. For example: if you go
to a shopping website and it asks you to login via Facebook. Facebook is
essentially the identity provider.

When IT professionals refer to an Identity Provider, it is best thought of as an
external service or system that enables logging in and logging out.

## Authentication

When a person authenticates, they are proving to the challenging system that
they are who they are claiming. Often, this is in a form of an identity, with a
set of credentials. These credentials are typically a username and password.

When an IT professional refers to "authentication" they are often referring to
whether the entered details of a person (be it a password or something else) is
valid and correct.

## Authorisation

Where authentication is the proof that a person is the one to whom they are
claiming. Authorisation is the activity of a challenging system that a specific
person is able to access a specific resource.

When IT professionals talk about authorisation, they are typically referring to
whether an individual should and can access or modify a specific area in a
system. For example: Raph has a Twitter account and so do you, however you are
not authorised to post to Raph's timeline. We are both authenticated to Twitter,
but only Raph is authorised to post as Raph.

In summary, when a system prompts someone that they are not "authorised" to view
a specific resource, the system isn't necessarily denying their existence as a
valid human, just their ability to see that resource.

## Single Sign On

Within an enterprise or business the term "Single Sign On" refers to using a
single identity to logon to all systems. This means that a person only needs to
remember a single username and password.

This has its advantages and disadvantages. The advantages is that the person
does not need to remember multiple sets of credentials. The disadvantage is that
it creates a single point of failure for all systems for a specific person.
Whilst it's not within the remit of this post to go into details on using single
sign on. Generally, if implemented well, single sign on is a good practice.

## MFA

MFA stands for Multi-Factor Authentication. When MFA is enabled it means that
authenticating to a challenging system requires more than one 'factor'.
Typically something the person knows (a password) and a code from something they
have (for example: a mobile device). You may already have MFA enabled on your
identities if you receive an SMS or need to check an app on your phone after
logging onto a system.

Having MFA enabled on all your identities is important, as it provides an
additional level of security in the event that your password is compromised.

## Data Classification

Data classification is the level of sensitivity associated with data. The higher
the reputational damage and commercial impact of an organisation, as well as
individual safety of people that deal with that organisation, the higher the
data classification. Several jurisdictions have different levels of data
classifications. Below are a few common classifications and their explanations:

- Public: This is data that has intentionally been made public for the interest
  of the wider community. It may include research findings, press releases, and
  simply marketing for a product;
- PII (Personally Identifiable Information): This is data that can identify a
  person, or that is identifying. This may include things like date of birth,
  address, full name, tax file number, and social security number; and
- Security Classified (Protected, Secret, and Top Secret): This is data which
  may influence the security of a nation-state or compromise its citizens.
  Within this classification, there are different levels: Protected, Secret, and
  Top Secret.

When IT professionals refer to the classification of data, they're often
referring to how to deal with that data from both an authorisation perspective
and a systems security perspective. Generally the more sensitive the
classification means fewer people accessing data and the higher the system
security and auditing associated with the systems that house that data.


Finally, thanks for reading this post! I hope it helped you clarify some of the
cyber security jargon that (we) IT Professionals use. I hope you use this post
as a reference point for whenever jargon is throw at you, so that when this
jargon is used, you can act, respond, and make decisions in an informed manner.

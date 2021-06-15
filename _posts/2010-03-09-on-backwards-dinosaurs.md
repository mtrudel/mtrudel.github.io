---
layout: post
title: on backwards dinosaurs
---

I've said it before, but github's [pages](http://pages.github.com) feature is a great application of one of git's most unsung features. to wit:

git repos are cheap. at their minimum, they're just a single top-level subdirectory with some easily parseable files tied to a real, working copy of your repo's contents. One command away, in place on top of an existing working copy. they're so cheap, in fact, that some of the [lightest sharing systems](http://gist.github.com) out there are based on git.

Those of us unfortunate enough to remember a life under subversion may or may not remember the steps involved in setting up a repository (as the tooling guy for a team of five developers, I had to go back to the manual every time I had to set one up). That was bad enough. Those of us who also remember having to clean up after someone checked a subversion repo into your subversion repo probably realized it a bit more acutely; a feeling someone, somewhere, was laughing their ass off at your expense.

In a world where repos are cheap &amp; easy to create, they start making sense as a generic representation of 'chunks of data' being moved around. Why bother using a [blog service](http://www.tumblr.com) with some clunky web-based posting interface? Why risk using a flavour of the month [silo of a cloud store](http://www.dropbox.com)? Why bother trusting [just anyone](http://www.google.com) to be the sole steward of your digital life?

A way forward here seems really easy and obvious given the fact that owning, copying, and generally marshalling sets of data around in the most efficient way possible is exactly what git does. In this far-off futureland, you publish to your blog by pushing to a remote git repo, you copy files between computers by syncing repos, and you share  your address book with your email provider as a bunch of open format files inside a repository. In all these cases, you yourself own a first-class version of the data in question, locally and under your control. Where's the data? It's everywhere it needs to be, including in your safe hands for as long as your care to care for it.

Imagine that. Cloud computing where 'my own personal cloud, please stay the hell out unless I invite you in' is a subset. Cool.

I know people ride my ass about being so hot and bothered about a piece of fucking software. And I am. But I'm excited because I feel like git is the first tool to really _nail_ this sort of interaction, and I see it being the jumping off point for a million great tools. It feels like the future, but one that's untamed and wild enough to help shape. It feels like *new*.
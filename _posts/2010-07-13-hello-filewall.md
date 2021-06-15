---
layout: post
title: Hello filewall
---

I'm sick of firewalls. I'm sick of how arcane and arbitrary their config systems are. I'm sick of the fact that they're never really reproducible, and that only one guy in most organizations understands their network (mostly because that guy was usually me).

On the other hand, I've always liked [shorewall](http://shorewall.net). Its config language has always seemed very flexible and easy to grok, it has great documentation, and since it's just iptables under the covers, there's no magic to it. Thinking about things further, I realized that most of my pain points were really more about how people go about setting up firewalls than about firewalls themselves. Such a central and yet seldom-touched piece of infrastructure is exactly the kind of place you want repeatability and process, and exactly the wrong place for ad-hoc hacks. Hence, [filewall](http://github.com/well/filewall/) was born.

In one line, **git + capistrano + shorewall = filewall**

Filewall is a mix of capistrano and shorewall that lets you keep your firewall configuration in SCM, and lets you deploy it to your routers in a simple, sane and structured way. It takes a great firewall and marries it to a great deployment system to make a great deployable firewall. Wicked.

Changes made with filewall are safely deployable; deployments incorporate a dead-man switch, wherein the rule changes are temporarily turned up for a timeout duration, and actually made permanent only if you subsequently affirm that they do what you intended them to. If you accidentally fry your config, filewall will restore your old configuration as soon as the timeout expires. You'll never be locked out again.

Committed configurations are immediately wired into the filesystem, so your firewall works predictably across reboots. And since it's [capistrano](http://capify.org) based, you have access to all the rollback awesomeness that capistrano provides.

If you're responsible for a more complex network, you can easily store all of your configurations as branches and keep all of your sensitive network config in one place, keeping all of your routers locked up with one public key. And don't forget that software routers are [fast](http://freedomhec.pbworks.com/f/linux_ip_routers.pdf), so you probably won't be outgrowing filewall anytime soon (and even if you do, filewall is hardware agnostic. As long as it runs Debian, it runs filewall).

So say hi to [filewall](http://github.com/well/filewall/). It's what you want.

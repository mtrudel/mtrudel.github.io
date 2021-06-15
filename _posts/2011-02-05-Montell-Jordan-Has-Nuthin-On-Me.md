---
layout: post
title: This is how we do it
---

As [promised](http://news.ycombinator.com/item?id=2183540), a howto on setting up SSH agent forwarding, and the proper use of proxy commands to punch through bastion (aka gateway) hosts.

## Agent Forwarding

The theory is described [here](http://unixwiz.net/techtips/ssh-agent-forwarding.html), but here's a distilled version of how to get SSH Agent forwarding working:

1. If you don't already have one, create your SSH key pair via:

        ssh-keygen -t rsa

    Ensure that you put a passphrase on this (it can be as strong as you want, since you'll only ever have to type it in once).

2. Push the public key component to the remote server you want to connect to:

        cat ~/.ssh/id_rsa.pub | ssh user@remote "cat >> .ssh/authorized_keys"

    Many OS's (not OS X, sadly) include an `ssh-copy-id user@remote` command that automates this.

3. Connect to the remote machine using your new key:

       ssh user@remote

    If you're on OS X or a reasonably friendly Linux box, you should get a native OS dialog asking you for the passphrase you just put on your private key. Enter it, and you should be golden. If you're on another OS that doesn't provide an automated agent, you probably want to look at [this](http://help.github.com/working-with-key-passphrases/) for more info.

4. Note that you can now make onward connections from the remote machine without being prompted to authenticate again. Magic!

## Proxying connections through a bastion host

This is crazy useful for maintaining a cluster of machines without having to expose their SSH servers to the internet. All you need to keep open is ssh on your bastion host and you have access to anything on the internal machines nearly automatically. Here goes:

Let's say you have an internal network of machines in a `.internal` domain (`maebe.internal`, `gob.internal`, etc). Assuming you already have SSH access to these machines as detailed in the previous section, you can simply add something like this to your `~/.ssh/config` file:

    Host *.internal
    ProxyCommand ssh <bastion_host> nc %h %p

and you'll magically be able to run ssh commands like the following:

    ssh maebe.internal

What??? How does my machine know to resolve maebe.internal from anywhere on the internet, you ask? What's happening here is that SSH is applying the config for *.internal to your connection request, and running the request through `ProxyCommand`. As such, your destination hostname actually gets looked up in the DNS context of the bastion host, which (presumably) knows how to resolve maebe.internal. Once again, Magic!
  

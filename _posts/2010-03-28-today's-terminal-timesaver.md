---
layout: post
title: Today's terminal timesaver
---

Via [Allan Odgaard](http://sigpipe.macromates.com/2010/03/28/search-path-for-cd/), a simple and super useful terminal tweak. The `cd` command looks for a `CDPATH` environment variable, which specifies directories `cd` will always look at when trying to change directories. Essentially, what `PATH` is to executing programs, `CDPATH` is to changing directories. 

The upshot of this is you can always change to commonly used directories no matter where you are in the filesystem. For example, I use the following setting:

	export CDPATH=::~:~/Work:~/Desktop:~/Code

and now no matter where I am in the filesystem, I can always change to any subdirectory of my home directory (or `~/Work`, `~/Desktop` or `~/Code`). So, entering `cd Downloads` will bring me to `~/Downloads` no matter where I am.

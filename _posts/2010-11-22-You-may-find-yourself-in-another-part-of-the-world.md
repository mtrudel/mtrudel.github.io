---
layout: post
title: You may find yourself in another part of the world
---

On a dare from [Dylan](http://www.nmr.mgh.harvard.edu/~tisdall/), I wrote this up last night as a quick and dirty way of keeping track of where I left off in a given terminal window. When you're walking away from a task, simply run

    whereami <note to self>

and it will start being reflected in your terminal prompt. When you sit down to work again, run 

    whereami

with no arguments to clean out the note to yourself. Because it works using environment variables, you can use this on any number of terminal windows without interference.

Code here:

<script src="https://gist.github.com/712063.js?file=gistfile1.sh"></script>

This has also been added to my [dotfile project](https://github.com/mtrudel/dotfiles/commit/fe507ea7).

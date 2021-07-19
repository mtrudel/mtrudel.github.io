---
layout: post
title: "Get at it, then"
---

About eighteen months ago I got the idea in my head that we should be able to control our house
air conditioner via HomeKit. Like most of its ductless brethren our air conditioner is
controlled exclusively via IR; my plan was to use [Nerves](http://nerves-project.org) on
a Raspberry Pi to create an IR blaster of sorts that could be controlled via HomeKit. A quick
search confirmed that there were no existing HomeKit libraries for Elixir but this seemed like
more of an invitation than a problem; the [HomeKit Accessory
Protocol](https://developer.apple.com/homekit/specification/) is a publicly available
specification and there are numerous existing libraries such as
[Homebridge](https://homebridge.io) that work with it, so how hard could it be? 

The answer, as it turns out, is 'rather'.

I've mostly reached the end of this project (or at least, I'm far enough into it to have
a complete map of everything that needs to be done to get it over the line), and along the way
I've had to take quite a few detours:

* I built a Nerves powered [desk clock](https://github.com/mtrudel/desk_clock) to learn the ins
  and outs of Nerves ([I did a talk about
  it](https://github.com/mtrudel/talks/blob/master/2020-07-Toronto-Elixir-Night-Nerves.pdf)).
  There were a few sub-detours here as well to [draw things](https://github.com/mtrudel/ex_paint)
  and also to [display them](https://github.com/mtrudel/ssd1322)
* I wrote [Thousand Island](https://github.com/mtrudel/thousand_island), a pure-Elixir socket server
  (along the lines of [ranch](https://github.com/ninenines/ranch)) to power the socket-level
  encryption required by the HomeKit Accessory Protocol (I [did a talk about this
  too](https://github.com/mtrudel/talks/blob/master/2020-01-Toronto-Elixir-Night-Thousand-Island.pdf))
* I wrote a pure-Elixir HTTP server called [bandit](https://github.com/mtrudel/bandit) to work 
  alongside Thousand Island
* I implemented the HomeKit Accessory Protocol in Elixir and released it as [HAP](https://github.com/mtrudel/hap).
  It allows Elixir apps to act as HomeKit accessories & be natively controlled from any iOS device
  (HomeKit support is built into iOS)
* Along the way I submitted a half-dozen or so upstream issues, ranging from [QR code
  rendering](https://github.com/SiliconJungles/eqrcode/pull/11) to a subtle but nasty [crypto bug in
  OTP](https://bugs.erlang.org/browse/ERL-1078)

I'm a big fan of the [make hard changes
easy](https://twitter.com/kentbeck/status/250733358307500032) philosophy, and so while the list of
diversions I've taken along the way may seem long, it was really just a matter of iteratively
breaking the large problem down into a series of small ones & working them through. And
now&mdash;finally!&mdash;I'm at the point where I can write a simple Nerves app that uses HAP to
talk to HomeKit & interfaces with an IR blaster to actually control our air conditioner (I haven't
*quite* figured out that last bit yet, but it's a solvable problem & just needs to be done).

In the spirit of [do things, tell people](http://carl.flax.ie/dothingstellpeople.html) I'll be
laying out a series of posts here over the next several months describing this journey & the
things I've built along the way, as well as the parts I still have to build. While I think there's
a lot of value in HAP on its own, I've come to believe that Thousand Island and bandit are
probably the real crown jewels of this whole project and I'm excited to share them with people.

The Elixir web stack is developing at a breakneck speed, with
[Phoenix](https://www.phoenixframework.org) pioneering a bunch of features that are miles beyond
most other platforms. However, the layers further down the Elixir networking stack start to become
quite opaque quite quickly. [Cowboy](https://github.com/ninenines/cowboy) and
[ranch](https://github.com/ninenines/ranch) are both fantastic libraries, but they can be quite
daunting reads for an Elixir developer unfamiliar with Erlang, even more so as they do quite a bit
more than serve requests to a Plug API. As a result the lower levels of the Elixir network stack
aren't as discoverable as they should be, which is a particular shame as this is one of the places
where the unique genius of OTP really shines. 

[Thousand Island](https://github.com/mtrudel/thousand_island) and
[bandit](https://github.com/mtrudel/bandit) are key missing pieces to help bridge this gap.
Between them, they provide some very tangible benefits to the Elixir community:

* Thousand Island provides straightforward & extremely flexible APIs entirely in native Elixir,
  making it easy for any Elixir developer to build their own network services using a familiar 
  set of tooling. Applications get essentially unlimited network scaling for free, based on
  Thousand Island's use of standard OTP design principles. Comprehensive
  [telemetry](https://github.com/beam-telemetry/telemetry) instrumentation provides built-in
  visibility all the way down to the socket level

* bandit provides a modern web server with robust & extensively conformance tested implementations
  of foundational standards such as [RFC 2616](https://datatracker.ietf.org/doc/html/rfc2616)
  (HTTP/1.1), [RFC 7540](https://datatracker.ietf.org/doc/html/rfc7540) (HTTP/2), and [RFC
  6455](https://datatracker.ietf.org/doc/html/rfc6455) (WebSockets) and is built specifically to
  serve a Plug API. Less complexity & modern idioms make bandit the obvious choice for future
  development on emerging standards such as [QUIC](https://datatracker.ietf.org/doc/html/rfc9000)

* Both libraries have been built from day one to be approachable, clean in design, and easy to
  understand and extend. They both deeply embrace OTP design principles and serve as great case
  studies for what the platform is capable of

* By providing a second implementation of a supporting HTTP server, bandit will help to improve the 
  robustness and flexibility of the Plug and low-level Phoenix APIs to ensure that they are truly
  agnostic to the underlying library & can grow to adopt currently unsupported features such as
  HTTP trailers

All-in-all, I'm **very** excited to see where the rest of this journey takes this project.  While
it's still early days, I don't think it's too audacious of a goal to see Thousand Island & bandit
take the lead as the go-to libraries for networking in Elixir, or even to see them as the default
choice in new Phoenix installs sometime in the future. Stay tuned!


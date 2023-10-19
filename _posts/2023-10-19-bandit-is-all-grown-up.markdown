---
layout: post
title: "Bandit is all grown up"
---

BLUF: Bandit and Thousand Island go 1.0.0 today

I started writing [Bandit](https://github.com/mtrudel/bandit) and [Thousand
Island](https://github.com/mtrudel/thousand_island) almost [four years
ago](https://github.com/mtrudel/bandit/tree/08e230161eeee29e48962c0342a358d1a7b00f41)
(!), initially as a bit of a joke. Despite its humble beginnings it quickly
became apparent to me that there was something to the project beyond humour, and
once things started getting a bit more serious with the project I broke down the
work to get to a 1.0 release, a journey which ended up looking something like
this:

* `0.1.x` series: Proof of concept (along with Thousand Island) sufficient to support HAP. Nov 2019 to Nov 2020 (12 months)
* `0.2.x` series: Revise process model to accommodate forthcoming HTTP/2 and WebSocket adapters. April-May 2021 (2 months)
* `0.3.x` series: Implement HTTP/2 adapter. May-Sept 2021 (4 months)
* `0.4.x` series: Re-implement HTTP/1.x adapter. Sept 2021 to April 2022 (7
  months)
* `0.5.x` series: Implement WebSocket extension & Phoenix support. April-Nov 2022 (7 months)
* `0.6.x` series: Comprehensive performance optimization & telemetry coverage.
  Nov 2022 to Mar 2023 (5 months)
* `0.7.x` series: Enhance startup options, general quality-of-life issues.
  Mar-Apr 2023 (2 months)
* `1.0.0-pre.x` series: Ready for general use, without reservation. Apr 2023-today (5 months)

All in all, I think my planning was pretty solid! There were a few
periods where I would put the project down for months at a time and a few
periods (mostly in the run-up to conference presentations!) where I would do
months worth of work in the span of a few weeks, but generally progress was
pretty steady (if a bit slower than I was expecting).

One mistake that I will note was that I called the `1.0.0-pre.x` series far too
early. In hindsight I ought to have run a `0.8.x` series to shake out any
lingering bugs before moving to a pre-release series, particularly because Hex
requires people to opt-in to get pre-release versions. As a result the testing pool
for the pre-release series wasn't as large as it should have been.

Throughout it all I've been lucky enough to talk about Bandit & Thousand
Island at a number of conferences ([ElixirConf
2021](https://www.youtube.com/watch?v=ZLjWyanLHuk), [EMPEX
MTN](https://www.youtube.com/watch?v=FtZBTUvRt0g), and [ElixirConf EU
2023](https://www.youtube.com/watch?v=usKLrYl4zlY)) and have been a guest on
several podcasts to talk about my work on Bandit and networking in Elixir more
generally. It's been a great trip, and has produced some of the best work I've
ever done. For anyone out there who's pondering starting up their own project
that seems ambitious but attainable at the same time, 10/10 would recommend.
It's a lot of fun.

After all of this, it seems a bit anti-climactic to say that Bandit and Thousand
Island are both going 1.0 later today. There's still a lot more work to be done
(see below!) but none of that work needs to block a `1.0.0` release from
happening. So here we are, get 'em while they're hot:

```elixir
{:bandit, "~> 1.0"},
{:thousand_island, "~> 1.0"}
```

I'd be in remiss if I didn't call out all the folks who have helped out along
the way; over 20 different people have submitted PRs large and small to the
projects, and many more have filed thoughtful bugs that helped highlight some of
the corner cases in the underlying RFCs. In particular,
[moogle19](https://github.com/moogle19), [Ryan
Winchester](https://github.com/ryanwinchester), and [Alisina
Bahadori](https://github.com/alisinabh) have been extremely helpful,
reviewing PRs & helping to triage issues from the community. The project
wouldn't be what it is today without their (and many others!) help. Thanks
everyone!

Looking to the future, we still have a bunch of stuff in the works:

* [Adding support](https://github.com/mtrudel/bandit/issues/91) for WebSockets
  over HTTP/2, as defined in [RFC8441](https://www.rfc-editor.org/rfc/rfc8441).
  This requires a fairly extensive overhaul of the HTTP/2 process model, but
  *should* be doable while maintaining backwards compatibility and so will be a
  later `1.x` release
* Moving protocol switching into Thousand Island, to mirror the transport
  upgrade work [currently in
  progress](https://github.com/mtrudel/thousand_island/pull/86)
* Implementing NIF/Rustler based support for native implementations of hot code
  paths within Bandit. This will be opt-in by default, but should provide for
  some substantial performance gains in many common cases
* Implementing improvements to Plug (or elsewhere) to facilitate gRPC style
  connections. This is likely a `2.0` feature
* HTTP/3 support remains on the horizon, though it's still a long way away

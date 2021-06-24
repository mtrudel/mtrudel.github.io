---
layout: post
title: "bandit on the loose"
---

[bandit](https://github.com/mtrudel/bandit) is the name of an HTTP server I've been writing over
the past several months. It is written entirely in Elixir and supports HTTP/1.x and HTTP/2 over
both secure and unsecure connections. As of version 0.3.2 (released today!), it should support
almost all HTTP client connections, although full HTTP/2 compliance is still a work in progress
(it currently scores 75% on the [h2spec](https://github.com/summerwind/h2spec) suite, with plans
for 100% compliance by the end of the 0.3.x release train). 

As I've [talked about before]({% post_url 2021-06-16-Get-at-it-then %}), bandit was originally
created out of necessity to sit in between [Thousand
Island](https://github.com/mtrudel/thousand_island) and [HAP](https://github.com/mtrudel/hap). The
0.1.x releases of bandit were very much works in progress, with little testing and rough edges
aplenty. It really only implemented the barest of requirements to satisfy the HTTP/1.1 needs of
the HomeKit Accessory Protocol, with Thousand Island providing the custom socket-level encryption
required. Now that HAP has more or less reached completion, I'm turning my attention back to
bandit to focus on its usefulness as a general purpose Plug-centric HTTP server.

### How bandit Works

Like almost any server, bandit has a pretty straightforward job:

1. Wait for connections from clients
2. Handle each connection to completion
3. Do the above as efficiently as possible

Fortunately for us, steps 1 and 3 are largely addressed by the underlying [Thousand
Island](https://github.com/mtrudel/thousand_island) library. As a socket server, its job is to
listen for connections on a TCP port (with optional SSL/TLS security) and pass each separate
client connection to a *handler module* for processing. Thousand Island doesn't concern itself
with what a handler module actually does with the connection; the handler could be an HTTP server
or any other protocol, custom or otherwise.  A socket server's only job is to accept client
connections as they come in and to efficiently pass them up to whatever handler module is
configured.

I'll have lots more to say about Thousand Island in a future post, but for now it's sufficient to
understand that every client connection ends up as a distinct Elixir process; one TCP connection
equals one Elixir process. Each such process is handled in complete isolation from one another, so
a handler module implementation only needs to concern itself with how to handle a single
connection. A handler process is free to take as long as it needs to properly handle its single
connection, without regard for the overall (network) scalability of the server.

When it comes to implementing a handler module to actually handle an HTTP client connection and
see it through to completion, the particular details of HTTP as a protocol come into play. The
atomic unit of an HTTP connection is a **request**. If you've ever connected to an HTTP server via
`telnet` or `nc` you've likely seen something like the following:

```
$ nc example.com 80
> GET /hello_world HTTP/1.1
>
< HTTP/1.1 200 OK
< Content-Length: 12
< 
< Hello, World
```

This is the simplest example of an HTTP/1.1 request from start to finish; a client connects, asks
to undertake a specific action on a specific resource (to `GET` the `/hello_world` resource in
this example), and the server responds with the result. 

In the case of bandit and other [Plug](https://github.com/elixir-plug/plug) based servers, the 
actual business logic of fulfilling a request is carried out by an application by means of the 
Plug API. Each client HTTP request is translated into a `Plug.Conn` struct and passed to the 
application via the [Plug](https://hexdocs.pm/plug/Plug.html) behaviour. The Plug API provides
functionality to enable application code to render responses back to the client, which again is
mediated back to a server implementation via the Plug API (the
[Plug.Conn.Adapter](https://hexdocs.pm/plug/Plug.Conn.Adapter.html) behaviour in this case). At 
its core, even a complex application framework such as Phoenix is 'just' a Plug application[^1].

[^1]: In truth, Phoenix is tied a bit more strongly to the Cowboy HTTP server than it should be, mostly due to the lack of a Plug-like behaviour to abstract WebSocket connections. Refactoring this interface so that Phoenix can work just as well on bandit (or any other compliant HTTP server) is a primary goal of the bandit project.  

Putting all this together, we have a stack that looks something like this:

![Protocol stack diagram](/images/bandit-protocol-stack.png){:.inline-image}

The job of a Plug based HTTP server is to accept client connections and convert every request
within that connection into a Plug call. With HTTP/1.x clients such as the previous example, this
is fairly straightforward. HTTP/1.x connections can only make a single request at a time (and in
fact often only ever issue a single request before closing the connection). From a process
perspective things are simple; since Thousand Island provides us each connection in its own
process, and since only a single request can be in progress at a given time within that
connection, it is a natural fit to simply host the Plug call in the same process that Thousand
Island provides us. It's a simple process model that completely captures the needs of the protocol
while also being scalable.

Supporting HTTP/2 is a bit trickier. The primary benefit of HTTP/2 over previous versions is that it
supports making multiple concurrent requests within a single connection (in HTTP/2, these separate
requests are called *streams*). At any given time, an HTTP/2 connection may have zero, one, or
hundreds of concurrent streams on the go, and the job of an HTTP server becomes much more
complicated as a result. Various failures and error conditions are local to the stream (in which
case other streams within the connection are unaffected), while some are fatal to the connection
as a whole. Moreover, since each stream contains its own request and each request maps directly
onto a Plug call, we need to devise a way to run each stream within its own process but still
be subservient to the overall connection process.

As it turns out, this isn't actually all that difficult thanks to the wonders of OTP's process
model. I'll be covering the bandit HTTP/2 process model in detail in a future post, but the
implementation is actually pretty straightforward; we end up with a single process to manage the
overall connection (this is the same process that is handed to us by Thousand Island), as well as
one process per stream. The individual stream processes are essentially thin wrappers around
a Plug call, and exist mostly to allow streams to be processed concurrently. Meanwhile, the
connection process takes care of marshalling the stream processes, ensuring synchronization of
network communication, and managing the overall connection life cycle.

### Current Status

As of 0.3.2 (released today), bandit should be usable for almost everything you can throw at it 
from common HTTP/1.x and HTTP/2 clients. There are a number of HTTP/2 features still to implement,
but most interactions should be able to hobble along in their absence. The general roadmap forward
is as follows:

* Implement HTTP/2 support in the 0.3.x release train (targeting a Q321 completion)
  * Implement [CONTINUATION](https://datatracker.ietf.org/doc/html/rfc7540#section-6.10) frame support in 0.3.3
  * Implement [WINDOW_UPDATE](https://datatracker.ietf.org/doc/html/rfc7540#section-6.9) and [PRIORITY](https://datatracker.ietf.org/doc/html/rfc7540#section-6.3) frame support in 0.3.4
  * Implement [PUSH_PROMISE](https://datatracker.ietf.org/doc/html/rfc7540#section-6.6) frame support in 0.3.5
  * Improve [SETTINGS](https://datatracker.ietf.org/doc/html/rfc7540#section-6.5.2) compliance in 0.3.6
  * Tackle any remaining h2spec conformance issues in 0.3.7
* Re-implement HTTP/1.x support in the 0.4.x release train (the current implementation is somewhat
  patchwork as it dates from my earliest 0.1.x work)
* Add WebSocket support in the 0.5.x release train (details TBD)
* Improve overall documentation, configurability & telemetry support in the 0.6.x release train
* Integrate with Phoenix's adhoc Cowboy support in the 0.7.x release train
* Roughly targeting Q122 for a bandit / Thousand Island 1.0 release

A primary goal of bandit is strict protocol conformance. Throughout the 0.3.x release train,
I have been making extensive use of the [h2spec](https://github.com/summerwind/h2spec) test suite,
and have wired it up into the [bandit test
suite](https://github.com/mtrudel/bandit/blob/master/test/bandit/http2/h2spec_test.exs).
This is actually a pretty neat little bit of test tooling (due credit to Ace's [h2spec
test](https://github.com/CrowdHailer/Ace/blob/master/test/ace/http2/h2spec_test.exs) which was the
inspiration for this). This setup makes it possible for me to develop features while testing
directly against an external conformance suite, even narrowing the tests down to a particular
section of the HTTP/2 spec:

```shell
# Let's test some stream state transitions...
$ H2SPEC=http2/5.1 mix test.watch --only external_conformance
```

These tests are in addition to exhaustive protocol and Plug API conformance testing, both done
using more conventional ExUnit testing. The combination is proving to be both comprehensive and
efficient, and I'm hoping to set something similar up for the upcoming HTTP/1.x and WebSockets
work.

### Wrapping It Up

Over the next several months I'll have lots more to say about bandit, Thousand Island, and HAP.
I'm planning on doing a series of long-form articles diving into the details of each library and
also some shorter pieces covering current development progress along the way towards 1.0 releases
of bandit and Thousand Island. Please check out the [Thousand
Island](https://github.com/mtrudel/thousand_island) and
[bandit](https://github.com/mtrudel/bandit) projects, kick the tires on them and let me know what
you think (and of course, PRs are always welcome!)

---
title: (paper report) CAP Twelve Years Later
layout: "post"
permalink: cap-twelve-years-later
category: computers
tags: [papers, distributed-systems]
---

*This is part of a series of posts I'm writing for my version of NaNoWriMo, where I summarize/review papers I've read recently. These aren't quite papers -- they're publications in a magazine.*

- Eric Brewer. [CAP Twelve Years Later: How the "Rules" Have Changed][cap-twelve]. (IEEE, 2012)
  - tl;dr: eventual consistency is a thing.
- Eric Brewer. [Lessons from Giant-Scale Services][giant-scale]. (IEEE Computer, 2001)
  - tl;dr: data per query x queries per second &rarr; constant.

## Giant-scale services

Most giant-scale services have the same basic components: a bunch of nodes that are hidden behind a load manager that balances traffic and abstracts away the physical servers/IP addresses.

- Brewer enumerates 6 common components: clients, a best-effort IP netework. the load manager, servers, the (often replicated and/or partitioned) database, and an optional backplane (a frontend that might route traffic or handle load-shedding)
- In almost all cases (even systems that think of themselves as being write-heavy), read-only queries greatly outnumber updates.

### Load management strategies:

The original approach was round-robin DNS, which distributes different IP addresses for a single domain name among its clients. It load balances well, but downed servers have to wait out the DNS's TTL before clients stop connecting to it.

### Notes on recovering from failure

Notes from the Brewer paper about building scalable internet systems [0], relating uptime and mean-time-between-failure (MTBF) and mean-time-to-repair (MTTR):

uptime = (MTBF - MTTR)/MTBF

> you can improve uptime either by reducing the frequency of failures, or reducing the time to fix them. Although the former is more pleasing aesthetically, the latter is much easier to accomplish with evolving systems.

In other words, it's just as important to assume failures and improve tooling around debugging and recovery as it is to program defensively and attempt to prevent failures in the first place.

## footnotes

[cap-twelve]: link
[giant-scale]: link

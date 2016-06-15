---
title: How Complex Systems Fail
layout: "post"
permalink: how-complex-systems-fail
tags: [papers, systems]
---

*This is part of a series of posts I'm writing for my version of NaNoWriMo, where I summarize/review papers I've read recently.*

- link: [How Complex Systems Fail](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.24.3690&rep=rep1&type=pdf)
- authors: Richard Cook
- date published: 1998

Read this with my paper reading group
- uncanny how much this applies to software
  - how and why we do blameless postmortems
- feature flags -- kind of a thing
- idea of an operations envelope
- like how much this focuses on social things


This is a fantastic paper, and I thoroughly agree with all of its proposals. It's a fairly short read! I know it's made its way around the internet

> Safety is an emergent property of complex systems

You can't build it into individual components -- it's something that fails on the edges of systems. The threats change, the meaning of security/safety changes every week.

Defense in depth approach -- similar to safety?
- "this part of the system should never fail, but when it does, here's the thing that catches it"


MTTR/MTBF comparison

http://www.kitchensoap.com/2009/11/12/how-complex-systems-fail-a-webops-perspective/

main points:

takeaways:

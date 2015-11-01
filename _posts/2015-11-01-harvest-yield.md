---
title: (Paper report) Harvest/Yield
layout: "post"
permalink: harvest-yield
tags: [papers, distributed-systems]
---

*This is part of a series of posts I'm writing for my version of NaNoWriMo, where I summarize/review papers I've read recently.*

- Armando Fox & Eric Brewer. [Harvest, Yield, and Scalable Tolerant Systems](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.24.3690&rep=rep1&type=pdf). (Hot Topics in Operating Systems, 1999)

- tl;dr: characterize graceful degradation mechanisms in terms of harvest (the fraction of data reflected in the response) and yield (the probability of completing a request)

## Overview

Coda Hale's blog post on partition tolerance[^1] summarizes this beautifully, so I'm just going to pull from him. 

> Despite your best efforts, your system will experience enough faults that it will have to make a choice between reducing yield (i.e., stop answering requests) and reducing harvest (i.e., giving answers based on incomplete data). This decision should be based on business requirements.

The easiest/default error-handling setup degrades the system entirely (affecting the yield). Instead, Fox and Brewer suggest an alternative approach to increasing availability: mapping faults to a reduction in the amount of data a system operates on -- in other words, by weakening the consistency guarantees of your system.

It's really easy to point to the CAP theorem and assert that systems can't have both strong consistency and availability, ending the discussion there. While some systems absolutely can't tolerate imperfect/degraded answers, there are a wide variety of systems that can tolerate some other point on the consistency spectrum, especially for queries. Clearly defining consistency/availability goals, especially for a system composed of many independent services, is crucial to improving availability.

Given this need, the authors define 2 metrics for correct behavior in a distributed system:

1. yield = requests completed successfully / total requests
  - note: yield is related to uptime, but deals with the number of queries missed, not just time down. (Being down for 10 minutes on Cyber Monday is not the same as being down for 10 minutes at 3 AM on a Tuesday.)
2. harvest = data in response / complete data
  - for example: if 1% of a sharded data store goes down, the request drops 1% of the data it would have returned otherwise, giving it 99% harvest.

> Our approaches tolerate partial failures by emphasizing simple composition mechanisms that promote fault containment, and by translating partial failure modes into engineering mechanisms that provide smoothly-degrading functionality rather than lack of availability of the service as a whole.

The authors begin by talking about harvest degradation mechanisms within a single monolithic system (responding to failures by operating on partial data), and about harvest degradation in a system composed of many independent services.

## Reducing harvest: probabilistic availability

(note: there's not much I can say here that hasn't been said better by Coda[^1], especially starting from "A Readjustment In Focus")

> Nearly all systems are probabilistic whether they realize it or not. ... Availability maps naturally to probabilistic approaches.

A point that I found interesting in here: replicating all data (not just highly-accessed data) might have relatively little impact on harvest/yield. Temporary loss of data that isn't heavily accessed is unlikely to show up in yield numbers, since it's unlikely that requests were made against that data, anyway.

The authors also mention trading off latency for harvest -- a system can declare with some confidence that it's received enough data to make a satisfactory response, without waiting on straggler information, which reminded me of the watermarks in Google's Millwheel pipeline.[^4]

## Reducing harvest: compartmentalization

> Some large applications can be decomposed into subsystems that are independently intolerant to harvest degradation (ie, they fail by reducing yield), but whose independent failure can allows the overall application to continue functioning with reduced utility.

Fox and Brewer emphasize orthogonal system design several times in this paper, defined as a system composed of mechanisms that have essentially no runtime interface to other mechanisms. They cite Nancy Leveson's claim[^2] that "most failures in complex systems result from unexpected inter-component interaction, rather than intra-component bugs." 

Compartmentalization[^3] is a related concept from fire safety codes. It divides a building into several independent compartments that can be isolated in case of a fire, allowing continuing operations and damage containment. I like to think of building fire doors between services: clear entry/exit points with protection mechanisms that can be enabled to contain the spread of system fires. This could be something like circuit-breaker code to shed load in the case of dependent systems, or designing services that can entirely down without affecting the operation of another. If a service errors downstream, it's easy to throw up hands and error out the whole system. Error-handling code is tricky to write, and far enough off the execution path that it's rarely tested, which is where gamedays and `kill -9` tests[^6] come into play.

## Related Papers (where to go from here)

This is what I've read so far. Happy to hear suggestions!

- [You Can't Sacrifice Partition Tolerance]. Coda Hale.
  - mostly what it says on the tin. Plus thoughts about evaluating systems by their CAP properties.
- [Towards robust distributed systems][brewer's PODC keynote], Brewer's 2000 PODC keynote.
  - A fairly comprehensive overview of CAP, its tradeoffs, and its applications to distributed systems design. (as a bonus: also a decent overview of the next 2 papers.)
- [Lessons from giant-scale services][building giant scale systems]. Brewer.
  - Suggests the data/query corollary to harvest/yield: harvest * yield ~ constant
- [CAP twelve years later]. Brewer.
  - How folks have (mis)applied the CAP theorem to distributed system design over the last twelve years. Also: partitions are defined by latency guarantees, and eventual consistency is a thing.

## footnotes

[^1]: [You Can't Sacrifice Partition Tolerance]
[^2]: Her book's [on Amazon][safeware]. This sounds like a series of case studies, which is the best! I love scary systems cautionary tales.
[^3]: [Compartmentalization]
[^4]: MillWheel’s watermark is a lower bound (often heuristically established) on event times that have been processed by the pipeline, used to determine how complete the pipeline's view of the world is at a certain time.
[^6]: [gamedays and `kill -9` tests]

[compartmentalization]: https://en.wikipedia.org/wiki/Compartmentalization_(fire_protection)
[You Can't Sacrifice Partition Tolerance]: http://codahale.com/you-cant-sacrifice-partition-tolerance/
[gamedays and `kill -9` tests]: https://stripe.com/blog/game-day-exercises-at-stripe
[brewer's PODC keynote]: http://www.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf
[CAP twelve years later]: http://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed
[building giant scale systems]: http://www.cs.berkeley.edu/~brewer/Giant.pdf
[google's millwheel paper]: http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41378.pdf
[safeware]: http://www.amazon.com/Safeware-Computers-Nancy-G-Leveson/dp/0201119722/ref=sr_1_1?ie=UTF8&qid=1446372937&sr=8-1&keywords=safeware

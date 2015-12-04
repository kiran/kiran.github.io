---
title: (paper report) Lessons from giant-scale services
layout: "post"
permalink: brewer-giant-scale-services
category: computers
tags: [papers, distributed-systems]
---

*This is part of a series of posts I'm writing for my version of NaNoWriMo, where I summarize/review papers I've read recently.*

- Eric Brewer. [Lessons from Giant-Scale Services][giant-scale]. (IEEE Computer, 2001)
  - tl;dr: data per query is inversely proportional to queries per second at the limits of scale. Use this to inform your capacity planning and load-shedding plans.

This is a paper from Eric Brewer (of the CAP theorem) summarizing his experiences working with large systems. Though the paper is from 2001, the basic tradeoffs of the systems he describes have not really changed since.

The most interesting insight of this paper is the DQ principle, which is related to the principles discussed in the [harvest/yield][harvest-yield] paper:

- (data per query) x (queries per second) &rarr; constant.

This paper is kind of a grab-bag of ideas, but I think the key insight is using the DQ principle to evaluate graceful degradation strategies.

## Measuring availability (harvest/yield)

Uptime, or the amount of time a site handles traffic, is the traditional metric of availability.

As a better set of availability metrics, Brewer reiterates the harvest/yield metrics, discussed [here previously][harvest-yield-heading].

> The key insight is that we can influence whether faults impact yield, harvest, or both. Replicated systems tend to map faults to reduced capacity (and to yield at high utilizations), while partitioned systems tend to map faults to reduced harvest, as parts of the database temporarily disappear, but the capacity in queries per second remains the same.

That is, given a failure, you can either completely answer less queries, or answer queries with less data. Given this data, Brewer goes on to define...

## The DQ principle

The DQ principle is not a strict theorem, but is a useful way of thinking about very constrained systems.

> The intuition behind this principle is that the system's overall capacity tends to have a particular physical bottleneck, ... which is tied to data movement.

- (data per query) x (queries per second) = total amount of data moved per second, which is bounded by this underlying physical limitation.

The DQ value/overall throughput is easily measured, and is a good measure for how overloaded a system is.[^1] The absolute value isn't super important, but it's crucial to understand both how adding/losing nodes affects it relatively and how close to the limit you're operating at. Load testing is a good way to get an initial measurement for the DQ value of an entire system.

## Graceful degradation under load

Being able to avoid saturation is a nice thought, but unless you're horribly over-provisioned, it's near impossible. Brewer presents three main reasons for this:

- the peak-to-average ratio for giant-scale systems seems to be in the range of 1.6:1 to 6:1.
  - most traffic follows diurnal and seasonal patterns.
- single-event bursts can generate far above-average traffic.
  - I've seen this nearly everywhere I've worked -- Stripe sees fairly large flash sales (Humble Bundle dropping a new amazing bundle), the NYT sees a huge news event, etc.
- Some faults, such as power failures,  are not independent. Overall DQ drops substantially in these cases.
  - Often, when AWS fails, it fails multiple systems at once.[^2] 

Given the inherent spikiness of traffic, if you don't plan how you shed load, you still will shed load, but not necessarily the load you can afford to shed.

The DQ principle gives you a disciplined way to think about load shedding:

> you can either focus on harvest through admission control, which reduces Q, or on yield through dynamic database reduction, which reduces D, or use a combination of the two.

There are a couple of different ways this can manifest[^3]:

- setting specific SLAs for high-impact businesses, and dropping other low-value queries. Many companies define a golden path for their business -- this could be the core charge path at Stripe, or searches on Google, or ride requesting at Lyft -- that cannot go down.
- reduced data freshness/caching. The cached queries don't reflect all the data available, and thus have a lower harvest.

## Upgrades

Maintenance and upgrades are basically controlled failures.

> completing upgrades without taking down the site is important becasue giant-scale services are updated so frequently.

Online upgrades can be viewed as a temporary reduction in DQ value --

- ΔDQ = number of nodes to upgrade (*n*) * upgrade time per node (*u*) * average DQ/node = DQ * *u*

There are three main approaches to upgrades:

1. Fast reboots
  - SUPER QUICKLY reboot everything. The entire system is down for *u* time, which might not be a huge drop in yield during off-peak hours.
2. Rolling upgrades
  - Take one node at a time, which probably doesn't reduce harvest or yield at all during off-peak hours, since there's probably enough excess capacity in the system to handle a single node failure. This requires that the new and old systems are inter-compatible, though.
  - This is the most commonly used upgrade path.
3. Big flip
  - Take down and upgrade half the cluster at a time. This allows for incompatible changes -- each half of the cluster handles all the traffic at any point in time. That is -- you take down part A, and upgrade it while B handles all the traffic. Once A is back up, you cut over traffic to it vian DNS, drain B, then take down B for upgrades.
  - You need to be over-provisioned by 100% for this to work.
  - The big flip is pretty powerful, since you don't have to ensure compatibility between the new and old versions of the world. For example, both Inktomi (in the paper) and Stripe (in my experience) have used this for data center moves. It's mostly useful for large and complex changes, which are rare.

Brewer also mentions that most systems set up a staging area to perform upgrades, making rollbacks easy. This, in combination with the big flip, sounds a lot like [blue-green deploys][blue-green-deploys].

## A note about observability/recovery systems

Relating uptime to mean-time-between-failure (MTBF) and mean-time-to-repair (MTTR):

- uptime = (MTBF - MTTR)/MTBF

> you can improve uptime either by reducing the frequency of failures, or reducing the time to fix them. Although the former is more pleasing aesthetically, the latter is much easier to accomplish with evolving systems.

In other words, it's just as important to assume failures and improve tooling around debugging and recovery as it is to program defensively and attempt to prevent failures in the first place. In fact, given that new features often reduce MTBF without affecting MTTR much, it's easier to focus on improving time to recovery.

There's a bit about this in ["How Complex Systems Fail"][how-complex-systems-fail], on how change constantly introduces new forms of failure, which often interact with each other in unexpected ways to cause failure. Essentially -- it's easier to improve observability, debugging tools, and recovery systems (circuit breakers, etc) than it is to solely prevent failures.

> Acceptable quality comes down to software the provides a target MTBF, a low MTTR, and no cascading failures.

## Related Papers (where to go from here)

Happy to hear suggestions!

- [How Complex Systems Fail.][how-complex-systems-fail]. Richard I. Cook.
  - This is an _incredibly_ spot-on paper about systems failures. It also motivates blameless postmortems very effectively.
- [Towards Robust Distributed Systems][podc-keynote]. Eric Brewer.
  - A fairly broad summary of the CAP theorem, harvest/yield, the DQ principle, and how those might apply to system design.
- [Harvest, Yield, and Scalable Tolerant Systems](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.24.3690&rep=rep1&type=pdf). Brewer & Fox.
  - Elaborates on the harvest/yield metrics.

## footnotes

[^1]: This is true only for data-intensive sites. If the system is computationally-bound, or bottlenecked by external systems, there's not much improving DQ can do. Most large services tend to be data-bound, though.

[^2]: I'd give a small fortune for a dependency graph of their systems.<br>SQS and Auto-Scaling groups [are dependent on DynamoDB][dynamo-sqs]. ELB and RDS [depend on EBS][ebs] ([again][ebs-2]). What else?

[^3]: I've omitted the third option Brewer presents: cost-based admission control, which estimates the difficulty (DQ cost) of the query before load-shedding. You can deny a very thorough unindexed query, for example, to enable several quicker ones. This can be taken further, to probabilistically blocking queries.

[dynamo-sqs]: https://aws.amazon.com/message/5467D2/#Impact_on_Other_Services
[ebs]: https://aws.amazon.com/message/2329B7/
[ebs-2]: https://aws.amazon.com/message/680342/
[giant-scale]: http://www.cs.berkeley.edu/~brewer/Giant.pdf
[harvest-yield]: /harvest-yield
[harvest-yield-heading]: /harvest-yield/#harvest-yield-definition
[blue-green-deploys]: http://martinfowler.com/bliki/BlueGreenDeployment.html
[how-complex-systems-fail]: http://web.mit.edu/2.75/resources/random/How%20Complex%20Systems%20Fail.pdf
[podc-keynote]: https://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf

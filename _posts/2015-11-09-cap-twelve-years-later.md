---
title: (paper report) CAP Twelve Years Later
layout: "post"
permalink: cap-twelve-years-later
category: computers
tags: [papers, distributed-systems]
---

*This is part of a series of posts I'm writing for my version of NaNoWriMo, where I summarize/review papers I've read recently. These aren't quite papers -- they're publications in a magazine.*

- Eric Brewer. [CAP Twelve Years Later: How the "Rules" Have Changed][cap-twelve]. (IEEE, 2012)

This is an article from Computer magazine that expands on some misconceptions about the CAP theorem. It mostly focuses on plans for operation during a partition, and recovery afterward.

### (A note on oversimplification)

> The "2 of 3" formulation was always misleading, because it tended to oversimplify the tensions among properties.

Talking about consistency, availability, and partition tolerance requires more nuance. CAP prohibits only a small part of the design space -- it's lazy to completely give up on C, or A, or P because the perfect combination of all three is impossible.

Also, the C and A tradeoff isn't a single choice for a system. Large are, in turn, composed of many subsystems that might be making different CAP choices. The choice can even change depending on the operation being performed, or the data being accessed.

> All three properties are more continuous than binary. The modern CAP goal should be to maximize combinations of consistency and availability that make sense for the specific application. Such an approach incorporates plans for operation during a partition and for recovery afterward.

Also, many systems are beginning to offer a disconnected mode of operation.

> During a partition, allowing one side to update state will cause the nodes to become inconsistent, therefore forfeiting C. Likewise, if the choice is to preserve consistency, one side of the partition should act as if it is unavailable, thus forfeiting P. Only when nodes communicate is it possible to preserve both consistency and availability.

> If users cannot reach the service at all, there is no choice between C and A.

## On partition resilience

> Such an approach incorporates plans for operation during a partition and for recovery afterward.

A strategy that is partition-resilient:

1. detects the start of a partition, 
2. enters an explicit partition mode that may limit some operations, and 
3. initiates a recovery process to restore consistency and compensate for mistakes made during a partition.

### 1. Detecting partitions

> A partition is a time bound on communication.

The partition decision happens in response to an operation timing out. The system can either:

1. cancel the operation and thus decrease availability, or
2. proceed with the operation and thus risk inconsistency.
  - retrying communication indefinitely (via paxos or 2PC) in the face of a partition is in essence choosing C over A

This has some interesting implications for partitions:

> This means that there is no global view of a partition -- some nodes might detect a partition, and some might not.

Partitions can also be one-way -- node A might be timing out when connecting to node B, but node B can see node A without issues.

Because partitions are tied to latency, system designers can tweak the system's sensitivity to partitions.

> Designers can set time bounds intentionally according to target response times: systems with tighter bounds will likely enter partition mode more often and at times when the network is merely slow and not actually partitioned.

### 2. Partition mode

The set of operations that are restricted during a partition depends on the invariants the system must maintain. Ideally, you want to restrict any operations that might violate the invariant, such as externalized events (talking to banking systems, sending emails, etc.)

Methods for dealing with this:

- use [version vectors][version-vectors] to track atomic operations.
  - When the partition heals, you can reorder the vectors to attempt to emulate causal consistency.
- to deal with externalized events, build a write-ahead log in partition mode, and replay these events when the system comes back up.
  - For example: instead of reaching out to credit card networks during a partition, the system records the intent and execute it after the recovery.

This presents a user-interface challenge: "[communicating] that tasks are in progress, but not complete." Some systems present different UI views in disconnected operation, like Google Docs or Bayou.

### 3. Partition recovery

When the partition heals, the system has (hopefully) kept a careful log of operations that happened during the partition. With this history, the system can reconcile its state and make compensations for any inconsistencies/mistakes made during partition mode.

The method used to merge the inconsistent narratives varies by use case. Some systems require explicit manual intervention (git, wiki systems) to resolve conflicts. Some systems always choose to keep additions, some do last-writer wins, and still others constrain operations during offline mode.

CRDTs (commutative repicated data types) are really powerful tools for state convergence after a partition.

That said, CRDTs are only useful for locally-verifiable invariants -- you can't use them to handle externalized calls. Methods for handling externalized mistakes made during a partition depend on the application.

As an example, Brewer brings up bank operations during a partition. Because of communication delays, the banking system defines the amount of risk it's willing to tolerate, and then relies on auditing and compensation (overdraft fees) to make up the difference.

#### (a note on eventual consistency)

Reconciling divergent state is important not just for healing partitions, but also for latency improvements in the general case. Brewer brings up some examples of consistency models optimized for performance:

- Yahoo's PNUTS:
	- the local data copy is the source of truth, since single user data is naturally partitioned according to the user's location.
- Facebook's' odd RYW scheme
	- the master copy is always in one location, so a remote user reads from the closer (potentially stale) copy. When the user issues a write, the update goes to the master copy, and so do all reads for the next 20s, which is much larger than the replication delay.



## Similar Papers (where to go from here)

- [Towards Robust Distributed Systems][podc-keynote]. Eric Brewer.
  - A fairly broad summary of the CAP theorem, harvest/yield, the DQ principle, and how those might apply to system design.
- [You Can't Sacrifice Partition Tolerance]. Coda Hale.
  - mostly what it says on the tin. Plus thoughts about evaluating systems by their CAP properties.
- [Managing Update Conflicts in Bayou][bayou].
  - A very early example of optimistically handling concurrent updates. The paper describes a hypothetical calendar app with an offline mode, and methods for reconciling state after reconnecting to the internet.
- [A comprehensive study of
Convergent and Commutative Replicated Data Types][crdts].
  - This is a fairly long (50 pages!), and fairly formal paper that introduces the idea of convergent data types. For a shorter/lighter version, [this video][crdt-video] by Sean Cribbs is a good introduction.

[cap-twelve]: http://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed
[version-vectors]: https://en.wikipedia.org/wiki/Version_vector
[podc-keynote]: https://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf
[You Can't Sacrifice Partition Tolerance]: http://codahale.com/you-cant-sacrifice-partition-tolerance/
[bayou]: http://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf
[crdts]: http://hal.upmc.fr/file/index/docid/555588/filename/techreport.pdf
[crdt-video]: https://vimeo.com/43903960

---
layout: page
title: Paper Reports
permalink: /papers/
---

I'm trying to read papers more regularly. Here's a list of what I've read so far, with a quick tl;dr for each. Click through for the longer post (I haven't written them all up yet):

- [Harvest, Yield, and Scalable Tolerant Systems](/harvest-yield), Fox & Brewer

  tl;dr: characterize graceful degradation mechanisms in terms of harvest (the fraction of data reflected in the response) and yield (the probability of completing a request)

- [Lessons from Giant-Scale Services](/brewer-giant-scale-services), Brewer
  
  tl;dr: data per query is inversely proportional to queries per second at the limits of scale. Use this to inform your capacity planning and load-shedding plans.

- [CAP Twelve Years Later: How the "Rules" Have Changed](/cap-twelve-years-later), Brewer

  tl;dr: the CAP principle has no nuance, but real systems do. The paper describes methods of handling partitions and ensuring eventual causal consistency.

- "What Goes Around Comes Around", Stonebraker & Hellerstein, 2005. [[pdf]](https://mitpress.mit.edu/sites/default/files/titles/content/9780262693141_sch_0001.pdf)

  tl;dr: A summary of 35 years of database systems' data models, grouped loosely into 9 eras. It's fascinating as a historical record, written with a bunch of opinions and wry humor. The authors describe the historical context of each data model, and often select a representative implementation to discuss. Each section ends with a set of lessons learned during that historical period.

- "Towards robust distributed systems", Brewer's 2000 PODC keynote. [[pdf]](https://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)

  tl;dr: A fairly broad summary of the CAP theorem, harvest/yield, the DQ principle, and how those might apply to system design

- "Exterminate All Operating System Abstractions", Engler & Kaashoek. [[pdf]](http://www.cs.berkeley.edu/~brewer/cs262b/hotos-exokernel.pdf)

  tl;dr: operating systems should not abstract any physical resources.

- "How Complex Systems Fail", Cook, 1998. [[pdf]](http://web.mit.edu/2.75/resources/random/How%20Complex%20Systems%20Fail.pdf)

  tl;dr: Easily one of my favorite systems papers. I want this printed out in large font and wallpapered over my walls. Complex systems always run at the brink of failure; failures have no single root cause; "human error" is not a cause; humans are producers & defenders against failure.

- "Rethink the Sync", Nightingale, Veeraraghavan, Chen, Flinn, 2006. [[pdf]](https://www.usenix.org/legacy/event/osdi06/tech/nightingale/nightingale.pdf)

  tl;dr: Proposes a new IO interface with performance similar to an asynchronous system, but with the interface of a synchronous system. This flushes writes to disk only when the effects of that write might be externally visible (network operation, display write). (speculative execution at the kernel level!)

- "Dynamo: Amazon's Highly Available Key-value Store", 2007. [[pdf]](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)

  tl;dr: This is a fantastically written paper, and is a great introduction to all of the topics it covers. Believes in incremental scalability, symmetry, decentralization, and the ability to exploit hardware heterogeneity. Also, sharding strategies using a distributed hash table.

- "Twitter Heron: Stream Processing at Scale", 2015. [[pdf]](http://dl.acm.org/citation.cfm?id=2742788)

  tl;dr: debuggability and operational ease are important qualities in large-scale systems.

- "The Dataflow Model: A Practical Approach to Balancing Correctness, Latency, and Cost in Massive-Scale, Unbounded, Out-of-Order Data Processing", Akidau et al, 2015. [[pdf]](http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/43864.pdf)

  tl;dr: "We as a field must stop trying to groom unbounded datasets into finite pools of information that eventually become complete, and instead live and breathe under the assumption that we will never know if or when we have seen all of our data, only that new data will arrive, old data may be retracted, and the only way to make this problem tractable is via principled abstractions that allow the practitioner the choice of appropriate tradeoffs along the axes of interest: correctness, latency, and cost."

- "MillWheel: Fault-Tolerant Stream Processing at Internet Scale", Akidau et al, 2013. [[pdf]](http://www.cs.cmu.edu/~pavlo/courses/fall2013/static/papers/p734-akidau.pdf)

  tl;dr: Millwheel operates on streaming data, and keeps centrally recorded metrics (watermarks) of how complete a subsystem's data is for a certain period of time (ie, "I've probably seen all data from 00:00 to 00:05, since I know all downstream systems have finished outputting results up to 0:05.").


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

- Towards robust distributed systems, Brewer's 2000 PODC keynote.

- Exterminate All Operating System Abstractions, Engler & Kaashoek

  tl;dr: operating systems should not abstract any physical resources.

- How Complex Systems Fail, Cook, 1998

  tl;dr: complex systems always run at the brink of failure; failures have no single root cause; "human error" is not a cause

- Rethink the Sync, Nightingale, Veeraraghavan, Chen, Flinn

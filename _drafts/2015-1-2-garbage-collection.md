---
title: Garbage Collection
layout: "post"
permalink: garbage-collection
thumbnail: /images/summer-reading-log-2014/thumbnail.jpg
category: programming
tags: [programming]
---

Important concepts of GC as they relate to Ruby/Python

A garbage collector attempts to free memory occupied by objects that are no longer used by the program.
Syntactic garbage is memory that is no longer reachable by the program. Semantic garbage is memory that will not be used by the program, and is thus much harder to identify. GCs generally tend to stick to freeing syntactic garbage, though some compilers will make an effort to identify and free semantic garbage.

## Tradeoffs

- Pros:
  - limit human overhead
  - prevent a large class of bugs: memory leaks, dangling pointers, etc.
- Cons:
  - can't use in a realtime application, since you can't tell when GC sweeps will be triggered
  - can unpredictably slow down programs

## Algorithms

### Mark and Sweep

### Generational garbage collection

## Profiling Ruby's GC

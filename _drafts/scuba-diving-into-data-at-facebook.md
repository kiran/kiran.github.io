---
title: (paper report) Scuba: Diving into Data at Facebook 
layout: "post"
permalink: scuba-paper-report
category: computers
tags: [papers, distributed-systems, monitoring]
---

> expires data at the same rate.

> SQL query interface (for a subset of SQL including grouping and aggregations, but no joins)

## compression and data storage

Scuba has some fairly involved compression mechanisms.

- Dictionary encoding of strings: each string is stored once in a dictionary and the index in the dictionary (an int) is stored in the row.

| Data type | Compression type  | Representation in row |
| Integer   | Variable length   | 1-8 bytes             |
| String    | dictionary        | Index, uses number of bits for max dictionary index |
| String (alt) | Uncompressed   | 4 bytes length + actual string |
| Sets of string | Dictionary   | Fibonacci encoding of deltas between sorted indexes |
| Vectors of String | Dictionary | 2B count + 1B index size + each index |

they're mostly memory-bound here, not CPU-bound, so they can afford to burn some computation to stuff more data into memory.
as of VLDB14, it was row-oriented, but they were exploring making it a column store. I'm curious if that was ever explored, and how that worked out.

## partitioning

Rows in scuba are sharded randomly (!), which was kind of surprising to me.

> distributed by partitioning each table randomly across all of the servers.

> for each batch of incoming rows, Scuba chooses 2 leaves at random and sends the batch to the leaf with more free memory. The rows for each table thus end up partitioned randomly across all leaves in the cluster.

This load balancing is likely inspired by https://www.eecs.harvard.edu/~michaelm/postscripts/mythesis.pdf

Aggregation of this data happens when you query it. I guess this is the ~one use case where your writes are likely to outweigh your reads significantly?

## data ingestion path

logging calls --> scribe --> scuba via a thrift API

At this point, the Scuba leaf node writes the entire batch to disk, and then compresses and loads data into memory.

## querying data

client --> root aggregator --> 4 intermediate aggregators --> fanouts to leaf aggregators
(incl figure 7)

Scuba has a sort of time-partitioned index.

optimizing regular expression matching: the leaf server maintains a per-query cache of the results of matching the regex against the strings in its dictionary.

a 10ms timeout on the leaf server or aggregator + an independent service that estimates dropped data.

the aggregator and the leaf nodes have a 10ms timeout. (not sure if that incl time calling downstream nodes? probably not?)

there's a separate service that maintains the approx count of rows across all leaves, so once your query completes, you check against that service, and that gives you the ballpark harvest of that query

there are no indexes, but you do specify a time range on all your queries, and that timestamp ordering is the only data indexing in scuba

## fault tolerance

everything's stored on one node, in memory
oh, okay, the data batches are stored on disk as they come in, then scuba compresses it, and throws it into memory. https://research.facebook.com/publications/fast-database-restarts-at-facebook/ is a follow-up.

## innovations

- automatic deletion of old data
- aggressive compression (factor of 6)
- ability to trade accuracy/completion for response time



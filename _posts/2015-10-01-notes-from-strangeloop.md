---
title: Notes from Strangeloop 2015
layout: "post"
permalink: notes-from-strange-loop-2015
category: computers
tags: [strangeloop, conferences]
---

Last week, I attended the Strange Loop conference in St. Louis for the first time! This was a comprehensive and long conference, so pardon my long-windedness!

## Talks

These are the talks and workshops I ended up going to, arranged in no particular order. The conference covered a wide range of topics, and I mostly went to distributed systems-y talks.

- **Property-Based Testing Workshop**, Jessica Kerr

	This was an interactive lecture/workshop, and I really enjoyed this. Jessica introduced property-based testing to the group, which is the idea of using generalized properties (or behavior) to randomly test your application. You define data generators to produce randomized input, and compose those into behavioral tests.

	Shrinking really blew my mind -- when you define data generators, you can define how to simplify the randomized data that your test is feeding into your application. If the framework runs into a failed test, it can shrink the input until it finds the minimal failing case! ([This StackOverflow answer](http://stackoverflow.com/questions/16968549/what-is-a-shrink-with-regard-to-haskells-quickcheck) has a little more detail.)

- **[Architectural Patterns of Resilient Distributed Systems](https://www.youtube.com/watch?v=ohvPnJYUW1E)**, Ines Sombra

	This was one of my favorite talks of the conference. I've been working on Stripe's systems team for about 1.5 years now, and I found a lot of Ines's anecdotes incredibly familiar. This was a really valuable talk, and [all of the papers she recommended](https://github.com/Randommood/Strangeloop2015) are now on my reading list. :)

	[The slides](https://speakerdeck.com/randommood/architectural-patterns-of-resilient-distributed-systems) are really dense and are worth a read just on their own.

- **[Hopelessness and Confidence in Distributed Systems Design](https://www.youtube.com/watch?v=TlU1opuCXB0)**, Camille Fournier

	Lots of cringing as I recognized myself in this talk. Camille's seen some shit.

	This was another one of my favorite talks, and I'm excited to read the two references she mentioned: [getting real about distributed system reliability](http://blog.empathybox.com/post/19574936361/getting-real-about-distributed-system-reliability) and [the distributed systems theory resources](http://the-paper-trail.org/blog/distributed-systems-theory-for-the-distributed-systems-engineer/).

- **[CRDTs Illustrated](https://www.youtube.com/watch?v=9xFfOhasiOE)**, Arnout Engelen

	Pretty diagrams of 2 kinds of eventually consistent data types. This was a great introduction to the concept of CRDTs.

- **[Distributed, Eventually Consistent Computations](https://www.youtube.com/watch?v=lsKaNDj4TrE)**, Christopher Meiklejohn

	This and the CRDTs Illustrated talk worked really well together. Christopher talked about his work on a programming model, called Lasp, where all the data structures are CRDTs, and can be composed into CRDT-style structures.


- **[Sweaters as a Service](https://www.youtube.com/watch?v=8xoowbbzxTA)**, Amy Wibowo

	I really enjoy Amy's speaking -- she's engaging, funny, and infectiously enthusiastic! I've done some (very minimal!) messing with knitting machines before, so it was really fun to see everything it takes to set it up. This was a great talk about hacking something old, and putting it on the internet!

	Also, doges and grumpy cats.

- **[Apache Kafka and the Next 700 Stream Processing Systems](https://www.youtube.com/watch?v=9RMOc0SwRro)**, Jay Kreps

	I've worked a bit with Kafka and other stream processing systems before, so there wasn't that much new in this talk. It's a great overview of Kafka as a stream processing system, though, and references [Jay Kreps' absolutely fantastic blog post about logs](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying).

- **[The Programming Language Called Classical Chinese](https://www.youtube.com/watch?v=vBhg2p8aAQ0)**, David Branner

	This talk explains classical Chinese grammar in the context of context-free grammars. A context-free grammar uses recursive rewriting rules to generate patterns of strings. In this talk, David talks about recursively combining nouns and verbs to form classical Chinese sentences.

	I know very little about either of these subjects, but this was well-explained!

- **[When "Worst" is Best (in Distributed Systems)](https://www.youtube.com/watch?v=ZGIAypUUwoQ)**, Peter Bailis

	This was essentially Peter's [Worst-Case Distributed Systems Design blog post](http://www.bailis.org/blog/worst-case-distributed-systems-design/) in talk form. He stressed building coordination-free systems, which seemed to be a theme at this conference. I'm really intrigued by the principle of reducing shared state across systems.

- **[From Protesting to Programming: Becoming a Tech Activist](https://www.youtube.com/watch?v=gy82S8tjJX8)**, Abby Bobé

	Idalin was a fantastic choice for a keynote speaker. This paired really well with Morgan's keynote about the importance of security and activism the next day.

- **[Security for Humans: Privacy and Coercion Resistant Design](https://www.youtube.com/watch?v=k4ypqzOShZs)**, Morgan Marquis-Boire

	A riveting talk about nation-state malware research.

- **[A History of Programming Languages for Two Voices](https://www.youtube.com/watch?v=J3C79CDqeW4&list=PLcGKfGEEONaCIl5eU53uPBnRJ9rbIH32R&index=31)**, David Nolan and Michael Bernstein

	David and Michael put together a mixtape juxtaposing trends in music and trends in programming languages. This was a really fun and engaging talk!

- **[When the OS gets in the way](https://www.youtube.com/watch?v=-6nrhSdu--s)**, Mark Price

	This was a talk about optimizing service performance for sub-millisecond replies. This was a dive into the Linux kernel -- tricks for optimizing the scheduler, and optimizing what CPUs code runs on. I haven't had reason to dig into this level of optimization, but it's cool to know that people are operating at this scale!

- **[How Machine Learning Helps Cancer Research](https://www.youtube.com/watch?v=vNiyDbcfJDE)**, Evelina Gabasova

	This was a talk about some machine learning models that are composed into cancer research tools. This was a very fluffy/insubstantial overview, but the 30-minute slot was probably too short for a more in-depth view. I'd love to learn more!


## Talks I missed, that I'll be watching the video for

The problem with multi-track conferences is the difficulty of choosing what to see. Luckily, all the talks were recorded and put online in record speed, so I can catch up on them now1

- **[Propositions as Types](https://www.youtube.com/watch?v=IOiZatlZtGU)**, Philip Wadler

	My friend Pam recommended this _highly_. I have no background in programming languages, so I'm super excited to see this!

- **[Strange Loops: Capturing Knots With Powerful Notations](https://www.youtube.com/watch?v=Wahc9Ocka1g)**, Kay Ye

	Knot theory sounds fascinating.

- **[Non-Imperative Network Programming](https://www.youtube.com/watch?v=GNc1t6Q5Dls)**, Mindy Preston

- **Ideology**, Gary Bernhardt

	I'm sad I missed this! Someone told me that the last 2 years have had talks for and against strong types, and this was meant to be a response to both of those talks. (Julia [wrote up a bit](http://jvns.ca/blog/2014/09/24/strange-loop-2014/#types) about the types argument from last year.) Unfortunately, Gary Bernhardt didn't want this taped and publish, so I've missed this forever. :(

- **[Evidence-oriented Programming](https://www.youtube.com/watch?v=uEFrE6cgVNY)**, Andreas Stefik


	I now want to watch this purely off of Tavish's tl;dr:

<blockquote class="twitter-tweet" lang="en"><p lang="en" dir="ltr"><a href="https://twitter.com/tavarm">@tavarm</a> tl;dr your opinions on what prog lang syntax choices are easy to understand aren&#39;t worth much and scientific studies are better</p>&mdash; tavish armless (@tavarm) <a href="https://twitter.com/tavarm/status/650792069070393344">October 4, 2015</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

- **[I See What You Mean](https://www.youtube.com/watch?v=R2Aa4PivG0g)**, Peter Alvaro

- **[Transactions: myths, surprises and opportunities](https://www.youtube.com/watch?v=5ZjhNTM8XU8)**, Martin Kleppmann

## Other Things!

OMG [THE CITY MUSEUM](http://www.citymuseum.org/). The St. Louis City Museum is a weird, fantastic place, with weird fantastic things. I ran around in a giant hamster wheel, took a 10-story slide, and just generally climbed over all of the things.

{% img right /images/strangeloop-2015/hamster-wheel.jpg  St. Louis City Museum %}

The conference hotel was an old train station, and the main lobby looks like St. Louis's version of Grand Central.

Seeing everyone from everywhere was also fantastic! This seems to be one of those conferences that all the computer people come to -- the hallway track was delightful. I met a bunch of friends from the internet, and got to see a lot of people I don't normally. The community around Strange Loop is really vibrant, and I'd come back just for that.

{% img left /images/strangeloop-2015/train-station.jpg Train Station %}

Also, there was a giant BBQ festival going on the same weekend as StrangeLoop, which was fantastic.

{% img right /images/strangeloop-2015/bbq.jpg St. Louis BBQ %}

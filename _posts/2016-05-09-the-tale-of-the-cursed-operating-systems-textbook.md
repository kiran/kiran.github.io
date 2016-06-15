---
title: The Tale of the Cursed Operating Systems Textbook!
layout: "post"
permalink: cursed-os-textbook
category: computers
tags: [operating systems, networks]
---

I have a cursed operating systems textbook. Each chapter I read unearths a new bug in the systems I work on.

Lest you think this is another case of the frequency illusion (or the Baader-Meinhof phenomenon), let me assure you it's not! The problems this book of spells uncovers are massive and unescapable!

While I've been using Unix systems for most of my life), and know *theoretically* how operating systems work, I hadn't spent that much time looking into the depths of more serious/more involved kernels. My understanding of operating systems has been mostly that of the blind men examining an elephant — I knew what the APIs were, and knew how it worked by observation, but I've never really seen the whole system, or really dug into its internals.

Basically, I wanted to be Lex Murphy — “this is a Unix system! I know this!” I firmly believe that having end-to-end familiarity with systems will make me a better programmer. John Allspaw has a great blog post on abstractions:

> are you abstracting away so that you truly can say “I don’t have to worry about this”? Or are you abstracting away because you’re aware of those guts, but want to focus your attention right now in this area.

if you’re thinking about it on a more positive note, it’s less a cursed textbook, and more a grimoire.

It’s essentially a big book of spells and incantations about these systems of magic (um engineering) that we work with, and some of them might cause harm. :P 

## Chapter 1: it's coming from INSIDE THE KERNEL

(credit goes to my coworker Nelson for debugging this issue!)

A while back, Stripe started seeing intermittent sadness with our internal DNS servers. DNS queries would fail out, and our servers would periodically out-of-memory kill processes, despite no application using very much memory.

### OOM Killer -- qu'est-ce que c'est?

A note about the OOM Killer:
It is the job of the linux 'oom killer' to sacrifice one or more processes in order to free up memory for the system when all else fails.

Looking over the OOM killer’s logs, Nelson noted that a HUGE amount of memory was being used by the “slab.” In the Linux kernel “slab” refers to the kernel’s slab allocator, used for internal allocations by the kernel itself. So essentially all of the box’s memory is being used by the Linux Kernel itself, not user applications, which explains why it’s swapping itself to death and OOM-killing even though no application is using much memory.

### /proc

So our current state — something is taking up all our kernel memory, and we’re not really sure what. It’s taking up enough memory that the OOM killer pops in every now and then, and shuts off our NSD process. How do we gather more information?

So let’s take a detour and talk about proc.
/proc is a process information pseudo-file system. It doesn't contain 'real' files but runtime system information about things like devices mounted, hw config, and SYSTEM MEMORY. In fact, quite a lot of system utilities are simply calls to files in this directory.

### forking

So, looking at slabtop, we found that anon_vma was taking up a HUGE amount of memory. Some googling discovered that there was a bug in the Linux kernel’s implementation of garbage collecting these — basically, at fork time, a new level’s created for each fork, and it doesn’t garbage collect any of the parent structures. Having each child become the new parent, and the previous parent exit, results in an infinite stack of `anon_vma` objects.

Armed with that knowledge, I was able to confirm that doing a complete restart (as opposed to graceful reload) of nsd caused the memory to be released, and that doing a thousand graceful reloads caused those numbers to grow rapidly.

## Chapter 2: fiNAGLEing

I’m sorry for the pun. I’m dedicating this pun to my coworker Andreas, here in the audience, who I’ve started blaming for all my bad puns.
So the issue behind this section is — hey, I’ve just met you, and I have this message that I sent you, and I’m sitting here waiting, and I’m getting no response here! argh! the WORST!

so a few months ago, right after the networking chapter of my book, Julia started debugging some slow networking issues with a system. the gist — ”hey I'm publishing messages to this daemon and it's taking 40ms each time”. This daemon, for context, lives on localhost. There is NO REASON publishing to localhost should take 40ms!

The HTTP library we use sends POST requests in 2 small packets — one for the headers, and the other for the body.


For efficiency you want to send full-sized TCP data packets. Nagle’s Algorithm says that if you have a few bytes to send, but not a full packet’s worth, and you already have some unacknowledged data in flight, then you wait, until either the application gives you more data, enough to make another full-sized TCP data packet, or the other end acknowledges all your outstanding data, so you no longer have any data in flight.

Usually this is a good idea. Nagle’s Algorithm is to protect the network from stupid apps that do things like this, where a naive TCP stack might end up sending 100,000 one-byte packets.

### Nagle's algorithm

For efficiency you want to send full-sized TCP data packets. Nagle’s Algorithm says that if you have a few bytes to send, but not a full packet’s worth, and you already have some unacknowledged data in flight, then you wait, until either the application gives you more data, enough to make another full-sized TCP data packet, or the other end acknowledges all your outstanding data, so you no longer have any data in flight.

Usually this is a good idea. Nagle’s Algorithm is to protect the network from stupid apps that do things like this, where a naive TCP stack might end up sending 100,000 one-byte packets.

By default, TCP will delay sending a packet that is less than full size in an attempt to combine multiple small writes into a single packet. Linux, by default, waits 40 milliseconds

The server, here, has delayed ACKs on. The assumption is that the server usually generates a response to a packet sent (ie, if you send a “HI”, the server responds with data — “HELLO” — so you don’t have to send a “RECEIVED.” followed by a “HELLO.”)

In this case, however, the server doesn’t have a response to the headers, so it waits for another packet.

The client’s using Nagle’s algorithm, so it’s waiting for an ack.

oops.

## Chapter 3: oints

A note about our backups: Amazon offers this great service called EBS. It has some issues that make us wary about using it as our main DB store, but it’s _great_ as a secondary to take backups off of! You can snapshot an entire EBS volume in one go, and Amazon will store that for you as a backup.

it was a part of a cryptic log message we got from a corrupted mongo replica
soooo…
that’s a negative size.
112 is a “p” in ASCII.

this is some data corruption on indexes
the error we got is a "the index is pointing at a deleted doc" type of thing

### buffers!

A note about kernel writes: the kernel maintains a bunch of write buffers, so it will return to write calls immediately, and flush that data to disk asynchronously. This means that at any given point in time, you might have half-written data in various buffers/states as the database is in the middle of a write. Before the database confirms the write, it issues a `sync` system call that clears out the buffers and writes everything to disk.

So, before you take the EBS snapshot, you want to make sure everything that the database or kernel has been holding onto is fully flushed out and written to disk, so you don’t end up with oints. When this bug arose a while ago, we realized that we _did_ fsync, but we didn’t actually lock the replica against _more_ writes. we don't actually lock the replica; snapshots get an fsynced state, but that state can easily race. mongo just scribbled on a data file while the snapshot was ongoing

## What's next!

- concurrency
- scheduling
- hard drives!

This meant that when we cleaned and attempted to remount the database, it had half-written data, and barfed.

So — a lesson in non-atomic writes and BUFFERS, friends!


I've been rewriting a large system at Stripe recently, and I was tasked with trying to come up with a "kill the legacy system" plan. For context, this system is Monster, which we first wrote in 2012 as our distributed job queue. We rewrote it again in 2013, to be a Storm-based work queue.

When I started looking into what the new system would look like, I focused entirely on the technologies I wanted to use. I went around to everyone I knew at Stripe who had operated a Kafka before, and asked them the same set of operational questions.

I hadn't operated a work queue before, so I was using "what is possible on the technology I choose?" as a proxy metric for "how sane is the interface this consumer wants?" This is fine as a first approximation, but it's incredibly limiting, and really doesn't avail itself to a sane migration path.

One thing that's really fantastic about new monster's design is how much the interface had been pared down from the previous design. Evan took a great deal of care to define exactly what the Ruby consumers needed to do, and slowly migrated the consumers over to the new path. New-monster still is slightly overloaded as a job queue, database, and rewindable log, but it's remarkable how much simpler the interface is. The Ruby workers know very little about where they're being run or how they're getting work handed to them.

I've realized that the single most important piece of system design is getting the interfaces right. This is something the [Chubby paper]() hammers on -- if you keep your API minimal, you can build a system that's hot-swappable with minimal difficulty. The technology really isn't the important part; you can get your system up and running on a prototype, and switch out whatever it's sitting on with minimal difficulty.


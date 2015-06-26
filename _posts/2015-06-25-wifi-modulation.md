---
title: A Wifi Primer: Squeezing more out of bandwidth
layout: "post"
permalink: wifi-primer-bandwidth
category: computers
tags: [wifi, networks]
---

## Bandwidth

### Modulation Tricks!

Now that you've determined what bits you want to send out, how do you encode that into signals that you can send over the air?

This is where modulation comes in! You may know how AM and FM radio works, or at least the broad strokes -- starting with a carrier wave at some base frequency, you change the amplitude or frequency according to the signal.
The modulation scheme that wifi uses isn't too different -- it's called phase-amplitude modulation. You take a sine wave at a carrier frequency (~5 GHz), and you choose several different levels for phase and amplitude.
For example, given 4 pre-determined phase and amplitude values, you can encode 3 bits of information. Here's a diagram of what the resulting signal might look like:

{% img /images/wifi/qam-time-domain.png 400 phase-amplitude modulation in the time domain %}

RF engineers tend to represent this on a "constellation diagram." You can kind of look at these like polar coordinates -- the angle is the phase, and the radius is the amplitude. The separation between these dots tells you how far apart these symbols are -- how hard it is to distinguish one symbol from its neighbor.

{% img /images/wifi/8-qam-constellation.png 400 eight symbol constellation %}

One way to get more information out of a certain bandwidth is just to pack these symbols more densely. For example, we can go from the 8 symbol constellation to a 16 symbol constellation -- this is one more bit sent every 4 microseconds.

{% img /images/wifi/16-qam-constellation.png 400 sixteen symbol constellation %}

This can get arbitrarily dense. The latest wifi amendment, 802.11ac, allows for up to 256 symbols in the constellation -- that's 16 bits every 4 microseconds!

#### Tradeoff: Yelling over Noisy Channels

Again, though, the separation between the symbols tells you how immune to noise the modulation scheme is. Zooming into the 16-symbol constellation a little to illustrate this:

{% img /images/wifi/in-the-presence-of-noise.png 400 in the presence of noise %}

When you pass a modulated signal through a noisy channel, the symbols tend to get knocked out of place a bit. Engineers tend to model noise as additive -- it adds or removes an arbitrary amount, across all frequencies. This is fine if there's enough space between symbols that the reveiver can resolve where the symbol originally came from. If the noise is large enough relative to the signal, though, the receiver may not be able to distinguish between symbols, and the message becomes irretrievable.

In this case, the receiver and transmitter back off and attempt to negotiate a lower modulation rate -- down to 8 bits per second, say. This is the link adaptation process mentioned earlier. Thus, effective usage of bandwidth is highly dependent on the SNR.

### Improve bandwidth with MORE bandwidth

One thing I sort of glossed over is that phase-amplitude modulation doesn't take up much bandwidth. You don't really need a 20MHz channel to transmit 256QAM!

This is where frequency division comes in -- you can split up the 20 MHz channel into 52 independent streams of data. Thus, if you double the channel width, you double the amount of data you can send!

The newest wifi protocol draft, 802.11ac, allows channels to be up to 160 MHz wide -- this implies 8 times as many streams, providing 8 times the data transmission!

{% img /images/wifi/OFDM.png 400 OFDM %}

#### Tradeoff: Interference

The issue with having wider bandwidth, though, is that it’s hard to find a clear space to transmit in. This is like trying to seat a table of 16 at a restaurant — when it's a popular restaurant with many people using the space, it’s really hard to find a contiguous table that big!

2.4 GHz is a really popular restaurant. Everything talks at 2.4GHz: microwave ovens, garage remotes, cordless telephones, bluetooth devices. There isn't much clear space around that frequency that isn't subject to lots of interference.

5 GHz is a little more empty. It's possible to combine tables to carve out wider channels, since there's enough unused bandwidth that it's plausible to have empty tables/frequency space near each other.
There's talk about moving up way farther in the spectrum, out to the outskirts of frequency space to take advantage of even more bandwidth.

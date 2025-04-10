---
layout: post
title:  "Minecraft & Digital Design 101"
date:   2025-04-08
---

# Overview

I used to play a *ton* of Minecraft as a kid. It is such an amazing game that provides an incredible opportunity to build where the only limiting factor is your imagination.

With my recent experience in FPGAs, I did some research into Minecraft's redstone mechanics and wanted to have some fun with it. So let's learn some digital design!

## Logic Gates

Let's build some basic <=2 input logic gates.

### NOT

|din|dout|
|-|-|
|1|0|
|0|1|

TODO

### AND

|A|B|dout|
|-|-|-|
|0|0|0|
|0|1|0|
|1|0|0|
|1|1|1|

![](/assets/images/2025-04-08/and.gif)

The 'up' position is 1 for each input.

### OR

|A|B|dout|
|-|-|-|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|1|

TODO

### XOR

|A|B|dout|
|-|-|-|
|0|0|0|
|0|1|1|
|1|0|1|
|1|1|0|

TODO


## Generating a Clock (Ring Oscillator)

Before we can make sequential elements, we need a clock. We'll cover basic combinational logic (AND, OR, etc.) later.

Let's start with a circuit:
![](/assets/images/2025-04-08/ring-oscillator-schematic.png)

If you're new to this, this might look super confusing. We have a NOT gate whose output drives itself.

But what if the input is driven by the output like it is here? Won't they always have the same signal? But if the output is inverting the input... how can this even work??? If this is what you're thinking, let's revisit an assumption you likely made while reading this circuit:

```
Signals propagate instantly through elements and wires.
```

In reality, and even in Minecraft, this is not true! Let's test it.

We can implement a NOT gate in minecraft by using a fundamental property of redstone torches

```
A redstone torch can be deactivated by powering the block it is placed on. 
```
~ https://learn.microsoft.com/en-us/minecraft/creator/documents/redstoneguide?view=minecraft-bedrock-stable

![](/assets/images/2025-04-08/burnout.gif)

Uh oh, the torch burned out!

```
A redstone torch experiences "burn-out" when it is forced to change state ... more than eight times in 60 game ticks...
```
~ https://minecraft.fandom.com/wiki/Redstone_Torch

But for a brief moment there, we could see the redstone dust and torch flicker on and off. It'll be easier to illustrate the concept by slowing down the signal a bit more. We do this by adding buffers:

![](/assets/images/2025-04-08/buffered.png)

This can be accomplished with two repeaters, which incurs a 2 game-tick delay each on the signal propagation.

![](/assets/images/2025-04-08/ring-oscillator.gif)

Now we can really see how this circuit leverages signal propagation delay to implement what's called a **ring oscillator**. Just like the name suggests, the circuit has a ring topology and if we probe the circuit and expose it as a signal, it oscillates 0&rarr;1&rarr;0&rarr;1...

![](/assets/images/2025-04-08/annotated.png)

![](/assets/images/2025-04-08/zeroonezeroone.gif)

### Oscillating Frequency

Let's keep units of time in game ticks. We can compute the frequency of oscillation by adding up the delays in the circuit, multiplying it by 2, and inverting the value. We need to multiply by 2 because if we just add the delays, we'll only get the duration of the duty cycle.

|Element|Delay (game ticks)|
|-|-|
|NOT|2|
|BUF1|1|
|BUF1|1|

So the period of the oscillation is 2*(2+1+1)=**8 game ticks**. The frequency is 1/(8 game ticks)=0.125Hz=**125kHz**. We observed that the torch didn't burn out, which makes sense because it changed state every 8 game ticks, so it would have only changed state 60/8=7.5 times per 60 game ticks. Looks like Minecraft uses the floor of 60/8 to make this call.
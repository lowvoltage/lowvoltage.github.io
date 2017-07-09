---
layout: post
title: "Garbled Text over Serial?!"
date: 2017-07-08
summary: "A bunch of grabage symbols is nothing to worry about"
categories: ESP8266 NodeMCU Serial
---

When programming the NodeMCU ESP8266 in the Arduino IDE and using a common 
'bits per second' setting for the Serial Monitor, such as 9600 BPS or 115200 BPS,
a bunch of random nonsense symbols appears on the first line of the monitor, upon every restart:

![115200_bps](/images/NodeMCU_Serial_115200.png)

These can be even more worrying if you're plugging-in a dev board for the first time
and there's no other output or sign of life.

Turns out, these symbols appearing is perfectly normal.
They are printed by a piece of bootloader code which always runs before the user sketch
and transmits at a different buad rate - 74880 BPS.

Here's the same output when using the 74880 BPS in the Serial Monitor:

![74880_bps](/images/NodeMCU_Serial_74880.png)

And in plain text:
```
ets Jan  8 2013,rst cause:2, boot mode:(3,6)

load 0x4010f000, len 1384, room 16
tail 8
chksum 0x2d
csum 0x2d
v09f0c112
~ld
```


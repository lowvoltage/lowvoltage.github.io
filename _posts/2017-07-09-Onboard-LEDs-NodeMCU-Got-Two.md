---
layout: post
title: "Onboard LEDs? NodeMCU's got two!"
date: 2017-07-09
---

As [Blinking a LED](https://www.arduino.cc/en/Tutorial/Blink) is the "Hello, world" of embedded programming,
most development board have an integrated LED. This makes it easier to run a basic piece of code, without having to
hookup any external components.

The NodeMCU ESP8266 board has two of those LEDs! One on the NodeMCU PCB and another on the ESP-12 module's PCB.
Below is a quick side-by-side comparison table:
![NodeMCU Two LEDs](/images/NodeMCU-ESP8266-LEDs.jpg)


|               | NodeMCU LED   | ESP-12 LED |
| ------------- |:-------------:  | :-----: |
| Color         | Blue          | Blue  |
| SMD Footprint | 0603          | 0603  |
| Pin           | GPIO16        | GPIO2 |
| Pin Functions | USER, WAKE    | U1TXD |
| Pin Silkscreen | "D0"    | "D4" |
| Current-limiting Resistor | 470 ohm    | 470 ohm |
| Sketch Pin Values | 16, D0, LED_BUILTIN, BUILTIN_LED    | 2, D4 |
| Notes |     |  Flashes during programming  |
| Schematic Link | <a href="https://github.com/nodemcu/nodemcu-devkit-v1.0/blob/master/NODEMCU_DEVKIT_V1.0.PDF"><img src="/images/NodeMCU-LED-Schematic.png" width="300"></a> | <a href="http://www.esp8266.com/wiki/lib/exe/fetch.php?media=schematic_esp-12e.png"><img src="/images/ESP-12-LED-Schematic.png" width="300"></a> |

 

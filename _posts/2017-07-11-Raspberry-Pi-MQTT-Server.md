---
layout: post
title: "Raspberry Pi as a MQTT server"
date: 2017-07-11
summary: "Setting up `mosquitto` on a headless Raspberry Pi Zero W"
categories: "Raspberry Pi" MQTT
---

## Install a Raspberry Pi image
Pick an image from https://www.raspberrypi.org/downloads/ and install it on the SD card. "Raspbian Jessie Lite" is an excellent fit for a headless (no graphical UI) installation and it's only 1.3Gb.

## Prepare for the first boot
1. With the SD card still connected to your computer, create a `wpa_supplicant.conf` file on the "boot" partition and fill-in your WiFi credentials:
```
network={
       ssid="NetworkSSID"
       psk="NetworkPassphrase"
       key_mgmt=WPA-PSK
    }
```
If the Raspberry Pi will be used on multiple networks, all of them can be defined:
```
network={
       ssid="Home NetworkSSID"
       psk="Home NetworkPassphrase"
       key_mgmt=WPA-PSK
    }

network={
       ssid="Office NetworkSSID"
       psk="Office NetworkPassphrase"
       key_mgmt=WPA-PSK
    }
```
2. Enable SSH access
Create a file on the "boot" partition named `ssh` (no extension)

## Plug in the Raspberry Pi
## (Optional) Assign a static IP
If everything has started up fine, the Pi should have received a dynamic IP address from the router. 
In your router's web interface, check for a "raspberrypi" DHCP client and assign a fixed (static) IP address to it.

## (Optional) Add the Pi's static IP to your computer's hosts file
This is `/etc/hosts` for Linux and `%SystemRoot%\System32\drivers\etc\hosts` on most Windows. Append a line in `ip hostname` format, for example:
```
192.168.0.100   pizerow
```

Setup a Raspberry Pi Zero W
https://raspberrypi.stackexchange.com/questions/62933/set-up-a-raspberry-pi-zero-w-without-monitor-or-ethernet-module

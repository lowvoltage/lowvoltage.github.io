---
layout: post
title: "Raspberry Pi as a MQTT server"
date: 2017-07-11
summary: "Setting up `mosquitto` on a headless Raspberry Pi Zero W"
categories: "Raspberry Pi" MQTT
---

## Install a Raspberry Pi image
Pick an image from https://www.raspberrypi.org/downloads/ and install it on the SD card. "Raspbian Jessie Lite" is an excellent fit for a headless installation (one with no graphical UI) and it's only 1.3Gb.

## Setup network config
This has to be performed before booting up the Raspberry Pi. With the SD card still connected to your computer, create a `wpa_supplicant.conf` file on the "boot" partition and fill-in your WiFi credentials:
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
## Enable SSH access
Create a file on the "boot" partition named `ssh` (no extension)

## Power on the Raspberry Pi
Plug in the SD card and connect the power cable. If everything works fine, the Pi should receive a dynamic IP address from the router. 

## (Optional) Assign a static IP
In your router's web interface, check for a "raspberrypi" DHCP client and assign a fixed (static) IP address to it. Let's say this IP is `192.168.0.100`.

## (Optional) Add the Pi's static IP to your computer's hosts file
This is `/etc/hosts` for Linux and `%SystemRoot%\System32\drivers\etc\hosts` on most Windows. Add a new line in `ip hostname` format, for example:
```
192.168.0.100   pihostname
```
This allows a textual name "pihostname" to be used for all access to the Raspberry Pi (Web, SSH, MQTT, etc) instead of typing in the IP.

## SSH to the Pi
On Raspbian, there's a default user `pi` with password `raspberry`. Run `ssh pi@192.168.0.100` on the command line and input the password.

## (Optional) Add your public SSH key
TODO: Generate key pair link
There are a couple of ways to transfer your public key to the Raspberry:
1. Copy-paste: 
Copy the contents of id_rsa.pub to the clipboard. The on the Pi's command line:
```
cat >> ~/.ssh/authorized_keys
(Paste from clipboard)
(Press Ctrl+D)
chmod 600 ~/.ssh/authorized_keys
```
2. Download from GitHub
If your public key is already on GitHub, it's a one-line command to get it in place:
```
curl https://github.com/<username>.keys | tee -a ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```
Logout and ssh into the Pi again to test the key-based authentication. There should be no prompt for a password.

## (Optional) Tighten up security
TODO

## Install `mosquitto`
TODO: also with command-line tools.
TODO: Mosquitto config? Location, values?
TODO: Assert that the service is running.
TODO: Test sub & pub

Setup a Raspberry Pi Zero W
https://raspberrypi.stackexchange.com/questions/62933/set-up-a-raspberry-pi-zero-w-without-monitor-or-ethernet-module

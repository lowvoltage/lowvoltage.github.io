---
layout: post
title: "Raspberry Pi as a MQTT server"
date: 2017-07-13
categories: "Raspberry Pi" MQTT
---
This is a step-by-step guide on how to install Raspbian on a headless Raspberry Pi Zero W, configure basic system services and install the [`mosquitto`](https://mosquitto.org/) MQTT server. Some of the steps are just a convenience and are optional.

## Required hardware
* Raspberry Pi Zero W (Wireless)
* Micro SD card, 4Gb or more
* A 5V power adapter and a Micro USB cable

## Install a Raspberry Pi image
Download the "Raspbian Jessie Lite" image from https://www.raspberrypi.org/downloads/raspbian/ and install it on a micro-SD card. This is a distribution specifically for headless installations (it comes with no graphical UI) and it's only 1.3Gb.

## Setup WiFi network access
With the SD card still connected to your computer, open the newly created "boot" drive / partition. Its size should be about 42Mb. Create a `wpa_supplicant.conf` file and fill-in your WiFi credentials:
```
network={
       ssid="NetworkSSID"
       psk="NetworkPassphrase"
       key_mgmt=WPA-PSK
    }
```
If the Raspberry Pi will be used on multiple networks, all of them can be listed:
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
Plug in the SD card and connect the power cable. There will be a couple of minutes of initial activity. If everything works fine, the Pi should receive a dynamic IP address from your network's router.

## Assign a static IP
In your router's web interface, check for a "raspberrypi" DHCP client and assign a fixed (static) IP address to it. Let's say this IP is `192.168.0.60`.

## Add the Pi's static IP to your computer's hosts file
This is `/etc/hosts` for Linux and `%SystemRoot%\System32\drivers\etc\hosts` on most Windows. Add a new line in `ip hostname` format, for example:
```
192.168.0.60   pizero
```
This allows a more friendly name "pizero" to be used for all access to the Raspberry Pi (Web, SSH, MQTT, etc) instead of the numeric IP.

## SSH to the Pi
On every Raspbian installation, there's a default user `pi` with password `raspberry`. Run `ssh pi@192.168.0.60` on the command line and input the password.

## Install utility packages
This is the time to install your favorite tools. `vim` is my editor of choice and will be used in all edit commands below.
```
$ sudo apt-get install -y vim htop
```

## Change the hostname
Use a text editor to change the default host name "raspberrypi" in the following two files:
```
$ sudo vim /etc/hostname
$ sudo vim /etc/hosts
```
These changes need a restart to get in effect:
```
$ sudo reboot
```

## Add your public SSH key
There are a couple of ways to transfer your [SSH public key](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#generating-a-new-ssh-key) to the Raspberry:
### 1. Copy-paste: 
Copy the contents of id_rsa.pub to the clipboard. The on the Pi's command line:
```
$ mkdir .ssh
$ cat >> ~/.ssh/authorized_keys
(Paste from clipboard)
(Press Ctrl+D)
```
### 2. Download from GitHub
If your public key is already on GitHub, it's a one-line command to get it in place:
```
$ mkdir .ssh
$ curl https://github.com/<username>.keys | tee -a ~/.ssh/authorized_keys
```

Logout and ssh into the Pi again to test the key-based authentication. There should be no prompt for a password.

## Tighten up security
Pi's default username and password are an obvious security risk and with SSH access enabled we can fully disable password-based authentication. 
```
$ sudo vim /etc/ssh/sshd_config
```
change the following settings to "no":
```
# Authentication:
...
PermitRootLogin no
```
```
# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no
```
and append a line at the bottom of the file:
```
AddressFamily inet
```

Finally, restart the `ssh` service
```
$ sudo service ssh restart
```

## Reduce power usage
In a headless setup the HDMI port is not used, so it can be disabled to save a few milliamps of power: 
```
$ sudo vim /etc/rc.local
```
and add a line before "exit 0":
```
/usr/bin/tvservice -o
```
It's also possible to [disable the LED](https://www.jeffgeerling.com/blogs/jeff-geerling/controlling-pwr-act-leds-raspberry-pi)

## Install `mosquitto`
Both the `mosquitto` server and the `mosquitto_sub` & `mosquitto_pub` command-line utilities can be installed from the default repositories:
```
$ sudo apt-get install -y mosquitto mosquitto-clients
```
The server should be automatically started and we can check its status with
```
$ sudo service mosquitto status

● mosquitto.service - LSB: mosquitto MQTT v3.1 message broker
   Loaded: loaded (/etc/init.d/mosquitto)
   Active: active (running) since Thu 2017-07-13 20:35:15 UTC; 2min 40s ago
   CGroup: /system.slice/mosquitto.service
           └─5547 /usr/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf

Jul 13 20:35:15 pizero mosquitto[5542]: Starting network daemon:: mosquitto.
Jul 13 20:35:15 pizero systemd[1]: Started LSB: mosquitto MQTT v3.1 message broker.
```
To test the command-line tools we need two terminals open:
```
(In terminal 1)
$ mosquitto_sub -t "test/topic" -v
(Will listen to incoming messages. Press Ctrl+C to exit)
```
```
(In terminal 2)
$ mosquitto_pub -t "test/topic" -m "hello world"
```

## `mosquitto` cheatsheet
Here are the server files' locations and control commands:
```
Config file location:  /etc/mosquitto/mosquitto.conf
Log file location:     /var/log/mosquitto/mosquitto.log
Service file location: /etc/init.d/mosquitto

Check server status:        sudo service mosquitto status
Start/stop/restart server:  sudo service mosquitto [start|stop|restart]

Command-line options for "mosquitto_sub" and "mosquitto_pub":
       -h     hostname, defaults to "localhost"
       -t     topic name
       -m     text message
       -v     verbose mode
```

## Links
* https://raspberrypi.stackexchange.com/questions/62933/set-up-a-raspberry-pi-zero-w-without-monitor-or-ethernet-module
* https://mattwilcox.net/web-development/setting-up-a-secure-home-web-server-with-raspberry-pi
* https://www.linode.com/docs/security/securing-your-server/
* https://www.jeffgeerling.com/blogs/jeff-geerling/controlling-pwr-act-leds-raspberry-pi
* https://www.jeffgeerling.com/blogs/jeff-geerling/raspberry-pi-zero-conserve-energy
* https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-ubuntu-16-04

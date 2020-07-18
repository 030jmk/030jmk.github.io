---
layout: post
title:  "Sonifying wireless probe requests"
date:   2019-08-29 12:00:00 +0200
categories: [bots and scripts]
---

# Sonifying wireless probe requests (2.4GHz)

Sonify and make wireless probe requests audible with tshark and a wireless adapter in monitor mode on ubuntu

The following snippets will look at captured probe requests and sonify them in the likes of a Geiger counter. 
Use it as a "detection system" for making sure that no wireless devices are broadcasting around your area or have fun annoying anyone around you.

Install tshark and sox
and generate a beep.wav file
```
sudo apt install sox tshark -y &&
sox -n peep.wav synth 0.01 sine 16000
```
Enable workaround `dumpcap` workaround,
Update IEEE OUI file to get manufacturer info from MAC
```
sudo chmod +x /usr/bin/dumpcap
sudo airodump-ng-oui-update
```

Set `wlx74da38ee4581` to monitor mode if wireless adapter supports it:
```
sudo ifconfig wlx74da38ee4581 down
sudo iwconfig wlx74da38ee4581 mode monitor
sudo ifconfig wlx74da38ee4581 up
```
Check whether monitor mode is set
```
sudo iwconfig
```

capture probe requests with tshark with stdout to CLI
```
tshark -i wlx74da38ee4581 subtype probereq
```
capture probe requests with tshark and safe to file and show line number in CLI

```
tshark -i wlx74da38ee4581 subtype probereq -w /tmp/cap.pcap
```


Since tshark output is buffered, use `-l`, if you want tshark to flush STDOUT after every packet which is neccessary to play a sound (peep.wav) for each new line for the captured tshark command

```
tshark -l -i wlx74da38ee4581 subtype probereq | while read line & do aplay beep.wav 2>&1 1>/dev/null & echo $line ; done

```
use `ctrl+c` to exit
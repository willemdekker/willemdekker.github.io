---
layout: post
title: "Rasberry Pi Time server with GPS."
categories: [blog ]
tags: [rasberry pi, ntpd, gps]
---
# A Rasberry PI 4 timeserver. 
I have created a timeserver serving my home network the time via the NTP protocol. It uses a GPS receiver to get a very acurate, one part per billion deviation time. A Rasberry PI is a nice small computer that runs Linux and thus can run the ntp server 
I used a configuration that does not need any internet server, so this can be used to create a time server in a network that is not connected to the internet. 


-- 
## Equipment
I used the following equipment
- A Rasberry Pi 4 , [Raspberry Pi 4 Model B](https://thepihut.com/collections/raspberry-pi-store/products/raspberry-pi-4-model-b) 
- An European power supply [Official EU Raspberry Pi 4 Power Supply](https://thepihut.com/collections/raspberry-pi-power-supplies/products/raspberry-pi-psu-eu)
- A 16 GB SD card with OS preinstalled['NOOBS' Preinstalled Micro SD Card](https://thepihut.com/products/noobs-preinstalled-sd-card) 
- An Uputronics GPS hat, [Raspberry Pi+ GPS Expansion Board](https://store.uputronics.com/index.php?route=product/product&product_id=81)
- An outdoor GPS antenna [GPS Base Antenna with mount and 10m cable ](https://store.uputronics.com/index.php?route=product/product&path=60_65&product_id=74)
- A case with room for the GPS board and antenna plug [ModMyPi Pi GPS Case](https://store.uputronics.com/index.php?route=product/product&path=66_67&product_id=90) 
-- 
## Build together

First build together all the parts. Make sure the GPS hat is on top of the Rasberry PI board with the GPS chip top side. 
Insert the SD card in the slot and connect ethernet and power. The Rasberry PI should power up. There is only one thing I did not connect the PI to a Screen and Keyboard. It was waiting in the installation program. So how to continue. 

You can make the installation screen go to VNC. 
How: Edit the first partition of the SD card (the one with noobs) and edit the recovery.cmdline file. 
Add 'vncinstall' in it. 
Mine is now:
  quiet ramdisk_size=32768 root=/dev/ram0 init=/init vt.cur_default=1 elevator=deadline sdhci.debug_quirks2=4 vncinstall forcetrigger

Also add the file 'ssh' in the same root directory of NOOBS to enable ssh. 

Then my Rasberry PI is ready to boot and install rasbian. Installation takes some time (15 minutes or so). 



--
## Configuration GPSD
Content of /etc/default/gpsd 
`
# Default settings for the gpsd init script and the hotplug wrapper.

# Start the gpsd daemon automatically at boot time
START_DAEMON="true"

# Use USB hotplugging to add new USB devices automatically to the daemon
USBAUTO="true"

# Devices gpsd should collect to at boot time.
# They need to be read/writeable, either by user gpsd or the group dialout. 
  
DEVICES="/dev/ttyS0 /dev/pps0"

# Other options you want to pass to gpsd
GPSD_OPTIONS="-n"
`
## Configuration NTP 
Content of /etc/ntp.conf

`
# Extra setting to allow for the jitter in transport time of the NEMEA output
tos mindist 0.5
  
#GPS NEMEA sentences. time1 value tuned for my local setup. 
server 127.127.28.0 minpoll 4 maxpoll 4 iburst prefer
fudge 127.127.28.0 time1 0.107 stratum 14 refid GPSb
# extra redundant GPSD pps logging 
server 127.127.28.2 minpoll 4 maxpoll 4
fudge 127.127.28.2 stratum 1 refid GPSd
# flag3 1 to enable kernel PPS 
# flag4 1 added for logging 
server 127.127.22.0 minpoll 4 maxpoll 4
fudge 127.127.22.0  refid PPS flag3 1 flag4 1 stratum 1
` 

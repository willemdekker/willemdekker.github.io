---
layout: post
title: "Rasberry Pi Time server with GPS."
categories: [blog ]
tags: [rasberry pi, ntpd, gps]
---
# A Rasberry PI 4 timeserver. 
I have created a timeserver serving my home network the time via the NTP protocol. The goal is to create a accurate timeserver that does not need the internet. Because if you do use the internet why not use the internet provided ntp servers. There are quite a lot of  setups where internet connectivity is intermittant or not allowed due to security reasons.  I used a GPS receiver to receive time information(UTC) and via the PPS signal also a frequency standard. A Rasberry PI is a nice small computer that runs Linux and thus can run the ntp server. 


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

Software installation . 

I installed Etcher to write the Raspbian Image on de SD card. I used a 64GB A1 qualified Micro SDXC card from SanDisk. 
I used the minimal Rasbian image. 

You can make the installation screen go to VNC. 
How: Edit the first partition of the SD card (the one with noobs) and edit the recovery.cmdline file. 
Add 'vncinstall' in it. 
Mine is now:
  quiet ramdisk_size=32768 root=/dev/ram0 init=/init vt.cur_default=1 elevator=deadline sdhci.debug_quirks2=4 vncinstall forcetrigger

Also add the file 'ssh' in the same root directory of NOOBS to enable ssh. 

Then my Rasberry PI is ready to boot and install rasbian. Installation takes some time (15 minutes or so). 

## user account configuration

## addition software installation
## update of the raspian software 


After installation, check if the GPS receiver works. 


## Configuration of the mublox gps receiver
To configure and check the gps receiver, I used ser2net. This sends the information received over the serial port to the network.
I installed windows software from Ublox called u-centre on a windows machine. From their I could check and configure the GPS. 
I configured the gps for stationary mode. 

--
## Configuration GPSD
Content of /etc/default/gpsd 
```
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
```
## Configuration NTP 
Content of /etc/ntp.conf

```
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
``` 
# Chrony configuration 
Chrony turned out to be much more stable in an disconnected (no internet time servers setting). 
Configuration file  /etc/chrony/chrony.conf 
``` 
# Welcome to the chrony configuration file. See chrony.conf(5) for more
# information about usuable directives.
#pool 2.debian.pool.ntp.org iburst

# This directive specify the location of the file containing ID/key pairs for
# NTP authentication.
keyfile /etc/chrony/chrony.keys

# This directive specify the file into which chronyd will store the rate
# information.
driftfile /var/lib/chrony/chrony.drift

# Uncomment the following line to turn logging on.
#log tracking measurements statistics

# Log files location.
logdir /var/log/chrony

# Stop bad estimates upsetting machine clock.
maxupdateskew 100.0

# This directive enables kernel synchronisation (every 11 minutes) of the
# real-time clock. Note that it can’t be used along with the 'rtcfile' directive.
rtcsync

# Step the system clock instead of slewing it if the adjustment is larger than
# one second, but only in the first three clock updates.
makestep 1 3

refclock SHM 0  delay 0.0 offset 0.107 refid NEMA stratum 14  noselect

# SHM2 from gpsd (if present) is from the kernel PPS_LDISC
# module.  It includes PPS and will be accurate to a few ns
refclock SHM 2 delay 0.0 refid PPS stratum 1 prefer
```
# References: 
https://ava.upuaut.net/?p=951

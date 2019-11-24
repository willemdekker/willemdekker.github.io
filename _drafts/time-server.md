---
layout: post
title: "Rasberry Pi Time server with GPS."
categories: [blog ]
tags: [rasberry pi, ntpd, gps]
---
A Rasberry PI 4 timeserver. 
I have created a timeserver serving my home network the time via the NTP protocol. It uses a GPS receiver to get a very acurate, one part per billion deviation time. A Rasberry PI is a nice small computer that runs Linux and thus can run the ntp server 
I used a configuration that does not need any internet server, so this can be used to create a time server in a network that is not connected to the internet. 


-- 
Equipment
I used the following equipment
- A Rasberry Pi 4 , [Raspberry Pi 4 Model B](https://thepihut.com/collections/raspberry-pi-store/products/raspberry-pi-4-model-b) 
- An European power supply [Official EU Raspberry Pi 4 Power Supply](https://thepihut.com/collections/raspberry-pi-power-supplies/products/raspberry-pi-psu-eu)
- A 16 GB SD card with OS preinstalled['NOOBS' Preinstalled Micro SD Card](https://thepihut.com/products/noobs-preinstalled-sd-card) 
- An Uputronics GPS hat, [Raspberry Pi+ GPS Expansion Board](https://store.uputronics.com/index.php?route=product/product&product_id=81)
- An outdoor GPS antenna [GPS Base Antenna with mount and 10m cable ](https://store.uputronics.com/index.php?route=product/product&path=60_65&product_id=74)
- A case with room for the GPS board and antenna plug [ModMyPi Pi GPS Case](https://store.uputronics.com/index.php?route=product/product&path=66_67&product_id=90) 
-- 
Build together
--
Configuration OS

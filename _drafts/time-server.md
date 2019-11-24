---
layout: post
title: "Rasberry Pi Time server with GPS."
categories: [blog ]
tags: [rasberry pi, ntpd, gps]
---
A Rasberry PI 4 timeserver. 
I have created a timeserver serving my home network the time via the NTP protocol. It uses a GPS receiver to get a very acurate, one part per billion deviation time. 

-- Equipment
I used the following equipment
- A Rasberry PI 4 with 1GB of memory. 
- An Uputronics GPS hat, [Raspberry Pi+ GPS Expansion Board](https://store.uputronics.com/index.php?route=product/product&product_id=81)
- An outdoor GPS antenna [GPS Base Antenna with mount and 10m cable ](https://store.uputronics.com/index.php?route=product/product&path=60_65&product_id=74)
- A case with room for the GPS board and antenna plug [ModMyPi Pi GPS Case](https://store.uputronics.com/index.php?route=product/product&path=66_67&product_id=90) 
-- Build together

Configuration OS

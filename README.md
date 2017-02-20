#BrewPi Installation Guide

#1. Required parts

Raspberry Pi
- Raspberry Pi 
- Power supply
- 8 GB SD Card
 
Andruino
- Arduino Uno
- USB B-A Cable

Other
- SSR
- Temperature sensor DS18B20 
- 4.7k resistance
- LCD Screen 4 x 20

#2. Software Installation

2.1 Install Raspbian
--------------------

2.2 Config RPi
--------------

    sudo raspi-config

2.3 Setup WiFi
--------------

Create a new file ``8192cu.conf`` in ``/etc/modprobe.d/``:

    sudo nano /etc/modprobe.d/8192cu.conf

Add this line to the file and save:

    options 8192cu rtw_power_mgnt=0 rtw_enusbss=0

2.4 Setup static IP address
---------------------------



#3. Hardware Installation



#4. Configuration


References: 
http://brewpi.readthedocs.io/en/latest/index.html

https://community.brewpi.com/t/software-install-guide/312

http://www.instructables.com/id/Multiple-Fermentation-Chamber-Control-with-BrewPi/

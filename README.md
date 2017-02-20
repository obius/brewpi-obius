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

2.2 Configure your system
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

    sudo nano /etc/network/interfaces

My /etc/network/interfaces file looks like this:

    auto lo
    iface lo inet loopback
    iface eth0 inet dhcp
    allow-hotplug wlan0
    auto wlan0
    iface wlan0 inet static
    address 192.168.0.6
    netmask 255.255.255.0
    gateway 192.168.0.1
    wpa-ssid "YOUR_SSID"
    wpa-psk "YOUR_PASSPHRASE"

The right IP addresses depend on your home network setup.
You can run ifconfig before editing the interfaces and write the automatically assigned addresses down.
However, it is recommended to pick a static Ip address that is outside of your router's DHCP range.

Finally, you will need to restart your network interfaces for these changes to occur:

    sudo service networking restart

2.5 Updating programs
---------------------

Keep your programs up to date with these commands:

    sudo apt-get update
    sudo apt-get upgrade


2.6 Updating firmware
---------------------



#3. Hardware Installation



#4. Configuration


References: 
http://brewpi.readthedocs.io/en/latest/index.html

https://community.brewpi.com/t/software-install-guide/312

http://www.instructables.com/id/Multiple-Fermentation-Chamber-Control-with-BrewPi/

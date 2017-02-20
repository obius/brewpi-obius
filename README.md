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


2.1.2 Configure your system
---------------------------

    sudo raspi-config


2.1.3 Setup WiFi
----------------

Create a new file ``8192cu.conf`` in ``/etc/modprobe.d/``:

    sudo nano /etc/modprobe.d/8192cu.conf

Add this line to the file and save:

    options 8192cu rtw_power_mgnt=0 rtw_enusbss=0


2.1.4 Setup static IP address
-----------------------------

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


2.1.5 Updating programs
-----------------------

Keep your programs up to date with these commands:

    sudo apt-get update
    sudo apt-get upgrade


2.1.6 Updating firmware
-----------------------
Finally, let's also update the pi's firmware, with rpi-update:

    sudo rpi-update


2.1.7 Install supporting programs
---------------------------------

Install your web server:

BrewPi is configured by a web server, and as such you need what is known as LAMP (Linux, Apache, MySQL, and PHP/Python/Perl) to run it for you. Type the following commands into the terminal:

    sudo apt-get install apache2
    sudo apt-get install libapache2-mod-php5 php5-cli php5-common php5-cgi

Now install PHP by typing this into the terminal:

    sudo apt-get install php5

The Raspbian Wheezy images comes with python 2.7 already installed, but BrewPi uses a few python modules that you have to install yourself:
- pySerial For communication with the Arduino.
- simplejson To encode and decode JSON objects. Easier to use than the default JSON module.
- ConfigObj For configuration files support.
- psutil To view running processes and close/kill them from within python
- python-git A Python library to work with git repositories. Used by the update script.

All 5 modules can be easily installed with apt-get:

    sudo apt-get install python-serial python-simplejson python-configobj python-psutil python-git

Finally, the python script can reprogram the Arduino, but it needs avrdude to do so. Install arduino-core, which includes avrdude.

    sudo apt-get install arduino-core

Let's get the some other Python stuff we need too; type this command:

    sudo apt-get install build-essential python-dev python-pipsudo pip install psutil --upgrade

Finally, to install the BrewPi script we will use Git. In a terminal window, type the following commands:

    sudo apt-get install git-core




2.2 Install BrewPi
--------------------




#3. Hardware Installation



#4. Configuration


References: 
http://brewpi.readthedocs.io/en/latest/index.html

https://community.brewpi.com/t/software-install-guide/312

http://www.instructables.com/id/Multiple-Fermentation-Chamber-Control-with-BrewPi/

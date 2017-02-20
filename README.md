#BrewPi Installation Guide

#1. Required parts

1.1 Raspberry Pi
----------------
- Raspberry Pi 
- Power supply
- 8 GB SD Card
 
1.2 Andruino
------------
- Arduino Uno
- USB B-A Cable

1.3 Other
---------
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

    sudo apt-get update
    sudo apt-get install apache2
    sudo apt-get install libapache2-mod-php5 php5-cli php5-common php5-cgi

To test your server, you can now visit http://your-rpi-ip/. By default, the root of your web server is /var/www/html/

Now install PHP by typing this into the terminal:

    sudo apt-get install php5
   
For security reasons, your web server runs as a separate user under Linux, the www-data user. Your files in your /var/www/html/ directory should be owned by this user, otherwise your web server will not have the permissions to access the files. You can change owner of everything in your /var/www/html/ directory to the www-data group and user with the following command. The -R flag recursively chowns all files and subdirectories.

    sudo chown -R www-data:www-data /var/www/html

The current version of BrewPi doesn’t use MySQL. It is recommended to not install it. If you want MySQL anyway, you can install it with these commands:

    sudo apt-get install mysql-server mysql-client php5-mysql

To manage your databases from a web interface, you can install PHPMyAdmin:

    sudo apt-get install libapache2-mod-auth-mysql php5-mysql phpmyadmin

Remember to select Apache2 with the space bar when it comes up! After installation, reboot and test: http://your-rpi-ip/phpmyadmin. Reboot with the command:

    sudo shutdown -r now

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

2.1.8 Setting up users and permissions
--------------------------------------

For security reasons, we will run the BrewPi python script under its own user account: an account without sudo rights. We will set up the brewpi user and group now. The web interface will run under the user and group www-data. The www-data user already exists. We will add the brewpi user with useradd, which automatically creates the group brewpi as well. Additionally, we are adding the user brewpi to the www-data and dialout groups (needed for access to the /var/www/html/ dir and serial ports, respectively). Then, we set the password for the brewpi user with passwd.

    sudo useradd -m -k /dev/null -G www-data,dialout brewpi
    sudo passwd brewpi

Now, verify your work:

    id brewpi

You should see something similar to:

    uid=1001(brewpi) gid=1002(brewpi) groups=1002(brewpi),20(dialout),33(www-data)

The python script will reside in the brewpi home directory. It will log data to the ./data subdirectory, keep settings in ./settings and it will copy everything the web interface needs to know to /var/www/html/ and chown it to www-data. By doing it this way, the www-data user does not have to have any rights outside its own directory. To allow the brewpi user to write to the directories owned by www-data, we will have to add it to the www-data group. We will also add the pi user to both groups to make it easier to work with the files.

    sudo usermod -a -G www-data pi
    sudo usermod -a -G brewpi pi 
    
To make sure that all newly created files in the www-data directory have www-data as group, even when they are created by the brewpi user, we set the sticky bit on the www-data directory and all its sub directories. We’ll set the sticky bit for the brewpi home directory as well. Run the following commands:

    sudo chown -R www-data:www-data /var/www/html
    sudo chown -R brewpi:brewpi /home/brewpi
    sudo find /home/brewpi -type f -exec chmod g+rwx {} \;
    sudo find /home/brewpi -type d -exec chmod g+rwxs {} \;
    sudo find /var/www/html -type d -exec chmod g+rwxs {} \;
    sudo find /var/www/html -type f -exec chmod g+rwx {} \;

These commands do the following things:
- Set the ownership of all files and subdirectories to brewpi and www-data (first two lines)
- Give the group all permissions on all files (third and fourth line)
- Give the group all permissions and set the sticky bit on all directories (fifth and sixth line).

    sudo chown -R www-data:www-data /var/www/html/chamber1
    sudo chown -R www-data:www-data /var/www/html/chamber2
    sudo chown -R www-data:www-data /var/www/html/chamber3
    sudo chown -R brewpi:brewpi /home/brewpi/chamber1
    sudo chown -R brewpi:brewpi /home/brewpi/chamber2
    sudo chown -R brewpi:brewpi /home/brewpi/chamber3
    sudo find /home/brewpi/chamber1 -type f -exec chmod g+rwx {} \;
    sudo find /home/brewpi/chamber2 -type f -exec chmod g+rwx {} \;
    sudo find /home/brewpi/chamber3 -type f -exec chmod g+rwx {} \;
    sudo find /home/brewpi/chamber1 -type d -exec chmod g+rwxs {} \;
    sudo find /home/brewpi/chamber2 -type d -exec chmod g+rwxs {} \;
    sudo find /home/brewpi/chamber3 -type d -exec chmod g+rwxs {} \;
    sudo find /var/www/html/chamber1 -type d -exec chmod g+rwxs {} \;
    sudo find /var/www/html/chamber2 -type d -exec chmod g+rwxs {} \;
    sudo find /var/www/html/chamber3 -type d -exec chmod g+rwxs {} \;
    sudo find /var/www/html/chamber1 -type f -exec chmod g+rwx {} \;
    sudo find /var/www/html/chamber2 -type f -exec chmod g+rwx {} \;
    sudo find /var/www/html/chamber3 -type f -exec chmod g+rwx {} \;

Now, use the following command to reboot your Pi:

    sudo reboot



2.2 Install BrewPi
--------------------




#3. Hardware Installation



#4. Configuration


References: 
http://brewpi.readthedocs.io/en/latest/index.html

https://community.brewpi.com/t/software-install-guide/312

http://www.instructables.com/id/Multiple-Fermentation-Chamber-Control-with-BrewPi/

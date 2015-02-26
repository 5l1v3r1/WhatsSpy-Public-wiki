# Getting Started with the Rpi Image

On this page you will learn how to Setup the Raspberry Pi image with WhatsSpy Public already on it. This tutorial is Windows based. You need the 'secret' which is discussed in chapter 1 of the normal [getting started](getting-started).

*Tested on Rpi Model B in combination with Windows*

## 1) Download

[Download the Raspbian image (1.6Gb, unpacked 4GB)](https://drive.google.com/file/d/0B_mlyJwD3c1TU1A5c2sxM3JFc3c/view?usp=sharing)

Download this file to your computer, op the `.zip` with WinRAR or 7Zip and extract the `.img` file to your desktop.

## 2) Writing the .img onto a SD card

Download and install [win32diskimager](http://sourceforge.net/projects/win32diskimager/). This tool allows you to write `.img` files onto a SD card.

* Insert the SD card for the Rpi in your computer (minimal 4GB).
* Open win32diskimager and select the image you've downloaded.
* Select the proper drive letter for your SD card.
* Click the write button and wait for it to complete.


## 3) Booting it up

Before booting make sure that:

* The SD card is in your Rpi.
* The Rpi has a network connection.
* You have a USB keyboard and HDMI screen connected (or you can use SSH).


The login information:

User: `pi`
Password: `whatsspypublic`

## 4) Configuration

Run `sudo nano /var/www/api/config.php` and do the following:

* Fill in the empty fields in `$whatsappAuth` ('number' and 'secret'). The 'secret' is the thing you have retrieved in (chapter 1) Secondary WhatsApp account) of the normal [getting started](getting-started).
* **'number'** needs to be <countrycode><phonenumber> without any prefix 0's. 0031 06 xxx becomes 31 6 xxx (no 0's prefix for both the country code and phonenumber itself).
* **'number'** may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
* Make sure `date_default_timezone_set('YOURTIMEZONE');` is set according to YOUR timezone ([list of timezones](http://php.net/manual/en/timezones.php)).

Save the file by using `Ctrl+X`, type `y` and press `Enter`.


Now make sure that PostgreSQL is using the correct timezone (is currently set at Europe/Amsterdam):
```
psql -U postgres
SET TIME ZONE 'YOURTIMEZONE';
\q
```


## 5) Importing users

Execute `ifconfig` and search for the ipv4 address. Look for `eth0` and find something of the form `inet addr:<IPV4 ADDRESS>`.

Now go to this address on your Windows (in for example Chrome or Firefox):

`http://192.168.2.9/` (where the 192.168.2.9 is the IP in ifconfig).

You need the following authentication:
Name: `whatsspypublic`
Password: `whatsspypublic`

In the GUI you can now:

* Either add any contact manually by using "Add contact by phonenumber".
* Or use "import google Contacts" which is an script that retrieves all your Google Contacts and gives an SQL statement which insert all users into the database.

## 6) Starting the tracker

Once you've inserted some users you can start the tracker by executing:

`sudo bash /home/pi/whatsspy-public-startup`

You only need to execute this command once. The next time the Rpi will startup WhatsSpy Public will start automaticly and it will auto-update as well. 

You can use `cat /home/pi/tracker.log` to view any history of the tracker and use `screen -r` to get a live view of the tracker.


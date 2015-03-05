# Getting Started with the Rpi Image

*Tested on Rpi Model 1B (4GB) and 2B (16GB) in combination with Windows*

On this page you will learn how to Setup the Raspberry Pi image with WhatsSpy Public already on it. This tutorial is Windows based. 

In case you don't get this working, you can create an issue on this Gitlab, or contact me on *maikeldus@hotmail.com*.

## 0) Secondary WhatsApp account

*This is a simplified version of [chapter 1) Secondary WhatsApp account](getting-started) or the normal getting started.*

WhatsSpy Public requires a phonenumber that does not actively uses WhatsApp. This can be for example a landline or just a 5 euro SIM card. Once you have obtained a phonenumber that doesnt use WhatsApp, do the following:

* Download [WART](https://github.com/shirioko/WART/blob/master/WART-1.7.3.0.exe?raw=true).
* Open up WART and fill in **phonenumber** and **password** (just choose one).
* **phonenumber** needs to be the countrycode+phonenumber.
* **phonenumber** needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
* **phonenumber** may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*.
* Press the request code. You will recieve a SMS with the activation code from WhatsApp.
* Enter this code in **Step 2** (only the digits) and press confirm code.
* Write down the password (it's the one-line of strange characters ending with an =).

You have retrieved the WhatsApp secret! Now we can setup WhatsSpy Public.

## 1) Download

[Download the Raspbian image (1.6GB, unpacked 4GB)](https://drive.google.com/file/d/0B_mlyJwD3c1TakNsdWd2X2ViRGs/view?usp=sharing)

Download this file to your computer, open the `.zip` with WinRAR or 7Zip and extract the `.img` file to your desktop.

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

* Fill in the empty fields in `$whatsappAuth` ('number' and 'secret'). The 'secret' is the thing you have retrieved in (chapter 0).
* **'number'** needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
* **'number'** may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
* Make sure `date_default_timezone_set('YOURTIMEZONE');` is set according to YOUR timezone ([list of timezones](http://php.net/manual/en/timezones.php)).

In case you want to enable notifications via WhatsApp add this to the `config.php` file (place it at the end before the `?>`):

```
$whatsspyWhatsAppUserNotification = '';
```
You need to fill in the `''` with the phonenumber you want to retrieve the messages on. The same **'number'** (described above) rules apply on this field. When you fill in this field you can enable notifications via the web interface.

Save the file by using `Ctrl+X`, type `y` and press `Enter`.

## 5) Check the system time

When you have a working internet connection, your time should adjust automatically but just to be sure:

Run `date`, this would output something like:
```
Thu Mar  5 13:38:05 UTC 2015
```
In case this time is not correct run `sudo dpkg-reconfigure tzdata` and follow the instructions on the screen.

## 6) Importing users

Execute `ifconfig` and search for the ipv4 address. Look for `eth0` and find something of the form `inet addr:<IPV4 ADDRESS>`.

Now go to this address on your Windows (in for example Chrome or Firefox):

`http://192.168.2.9/` (where the `192.168.2.9` is the IP in ifconfig).

You need the following authentication:
Name: `whatsspypublic`
Password: `whatsspypublic`

In the GUI you can now:

* Either add any contact manually by using "Add contact by phonenumber".
* Or use "import google Contacts" which is an script that retrieves all your Google Contacts and gives an SQL statement which insert all users into the database. [Check here how to insert them](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/112).

Once you have inserted these users they won't show up automatically. They need to be verified by the tracker which is not running yet.

## 7) Starting the tracker

Once you've inserted some users you can start the tracker by executing:

`bash /home/pi/whatsspy-public-startup`

It will output something like this:
```
-------------------------------------------------
           (Re)starting WhatsSpy Public
      Check gitlab.maikel.pro for more info.
-------------------------------------------------
[update] Checking for updates.
Fetching origin
HEAD is now at f8ab30d Fixed timline bug with less than 2 statuses
[stop] Stopping any existing tracker session.
No screen session found.
[rights] Setting rights.
[start] Starting tracker session.
```

You only need to execute this command once. The next time the Rpi will boot, WhatsSpy Public will start automaticly and it will auto-update as well (every 24 hours). 

## 8) Other steps

You can use `cat /home/pi/tracker.log` to view any history of the tracker and use `screen -r` to get a live view of the tracker. You can exit this view by using `Ctrl + a` and `Ctrl + d`.

I advise you to change the default password to something else. You can do this by using `passwd`.

If you ever choose to shutdown the pi undertake these steps:
```
screen -r
```
Press `Ctrl + c` and enter `exit` followed by a enter.

After this you can shutdown by using `sudo shutdown -h now`.


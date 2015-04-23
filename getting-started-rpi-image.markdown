# Getting Started with the Rpi Image

*Tested on Rpi Model 1B (4GB) and 2B (16GB) and one report of working QEMU working. Make sure you have a new SIM card or phonenumber that does not actively use WhatsApp ready for this guide, you need this in step 4.*

On this page you will learn how to Setup the Raspberry Pi image with WhatsSpy Public already on it. This tutorial is Windows based, altough using Linux should not be a problem. In case you don't get this working, you can create an issue on this Gitlab, or contact me on *maikeldus@hotmail.com*.

## 0) Important notes

* This image will auto-update (shipped with version `1.5.0`) thus removing any change you made in the code.
* The rights in this image are 'loose', don't use this image for any sensitive information, just for the purpose of this PoC.
* **Do not just pull out the power cord out of your Raspberry Pi!!** You need to [properly shutdown your Raspberry Pi](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/rpi-image-tips#shutdown-your-raspberry-pi-in-a-safe-way) before you can safely remove the power cord. **Don't ignore this: sudden powerloss can be fatal for the Operating System.**

## 1) Download

Download the [Raspbian image 1.6GB, unpacked 4GB](https://docs.google.com/uc?id=0B_mlyJwD3c1TMkFwQXFMSUVlQ2s&export=download)

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

*(cannot login? check #141, you may have a different keyboard layout)*

## 4) Getting the `secret`

WhatsSpy Public requires a phonenumber that does not actively uses WhatsApp. This can be for example a landline or just a 5 euro SIM card. Once you have obtained a phonenumber that doesn't use WhatsApp. Execute the following commands on the Raspberry Pi:

* Execute `php /var/www/whatsspy/api/whatsapp/registerTool.php`.
* Enter your phonenumber that you want to use for the WhatsSpy Public tracker (You cannot use this for WhatsApp while the tracker is running).
   * `number` needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
   * `number` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
* Request activation via `SMS` and wait for a SMS to arrive at the phone (try `voice` if you did not get the SMS).
* Enter the retrieved code in the script without any dashes (only the digits!).
* Write down the `secret` (it's the one-line of strange characters ending with an =).

You have retrieved the WhatsApp secret! Now we can configure WhatsSpy Public.

*There are two other methods [described here](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/ways-of-getting-the-secret) in case this method does not work.*

## 5) Configuration

Execute `sudo nano /var/www/whatsspy/api/config.php` to start editing the config to fill in the following details:

* Fill in the empty fields in `$whatsappAuth` (`number` and `secret`). The `secret` is the thing you have retrieved in *chapter 0*.
   * `number` needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
   * `number` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
* Make sure `date_default_timezone_set('YOURTIMEZONE');` is set according to YOUR timezone ([list of timezones](http://php.net/manual/en/timezones.php)).
* You can choose another password for the webUI by changing the value of `$whatsspyPublicAuth`.

Save the file by using `Ctrl+X`, type `y` and press `Enter`.

**Note**: You now configured WhatsSpy Public for version `1.5.0`. The configuration might update in the future, you need to adapt these changes described in the changelog.

## 6) Check the system time

Run `date`, this would output something like:
```
Thu Mar  5 13:38:05 UTC 2015
```
In case this time is not correct run `sudo dpkg-reconfigure tzdata` and follow the instructions on the screen. After this you can check again by running `date`.

## 7) Importing users

Execute `ifconfig` and search for the ipv4 address. Look for `eth0` and find something of the form `inet addr:<IPV4 ADDRESS>`.

Now go to this address on your Windows (in for example Chrome or Firefox):

`http://192.168.2.9/` (where the `192.168.2.9` is the IP in ifconfig).

You need the following authentication:
Password: `whatsspypublic`

In the GUI you can now:

* Either add any contact manually by using "Add contact by phonenumber".
* Or use "import google Contacts" which is an script that retrieves all your Google Contacts and gives an SQL statement which insert all users into the database. [Check here how to insert them](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/112).

Once you have inserted these users they won't show up automatically. They need to be verified by the tracker which is not running yet.

## 8) Starting the tracker

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

## 9) Check check double check

Type `cat /home/pi/tracker.log` after 20 seconds of starting the tracker to check everything went right.

**In case it gives an error like:**
```
[error] Tracker Exception! Connection Closed!
```
Make sure your firewall is allowing traffic from your Raspberry Pi. You can exit this view by using `Ctrl + a` and `Ctrl + d`.

**In case it gives an error like:**

```
[error] Tracker Exception! Login failure!
```
Make sure the `number` and `secret` are correct. If you think they are correct, please re-use the generation script. Another problem can be the fact that you have no internet connection, to check this execute `ping google.com` and check if there are responses. In case you do not get response you might need to [add nameservers](http://superuser.com/questions/617796/how-do-i-set-dns-servers-on-raspberry-pi) to your raspberry pi.


## 10) Optional steps / handy things to know

* You can enable notifications via *NotifyMyAndroid*, *LiveNotifier* or even WhatsApp. For this you need to edit your `config.php` (like before) and change entries in `$whatsspyNotificatons`. After saving your change restart the tracker by again calling `bash /home/pi/whatsspy-public-startup`.
* If you want to share profiles via the internet you need to port forward port 80 on your Raspberry Pi. Use the IP adress you gained in **7)** and [follow this guide](http://portforward.com/english/applications/port_forwarding/Apache/default.htm).
* You can use `cat /home/pi/tracker.log` to view any history of the tracker and use `screen -r` to get a live view of the tracker. You can exit this view by using `Ctrl + a` and `Ctrl + d`.
* I advise you to change the default password of the user `pi` to something else. You can do this by using `passwd`.
* If you ever choose to shutdown the pi undertake these steps:
```
screen -r
```
Press `Ctrl + c` and enter `exit` followed by a enter. After this you can shutdown by using `sudo shutdown -h now`.
* The image automatically updates at 4 AM, you need to keep an eye on the [updatelog](updates) to comply with the settings.


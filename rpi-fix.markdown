# Raspberry Pi fix for image of before 4th of march 2015 ([error] Tracker Exception! Connection Closed)

Anyone who downloaded the Rpi image before the **4th of march 2015** may have connection issues. 

## 1) Identify problem

You can identify this problem by going to the compare page and check if the blue background line is interrupted by a lot of white stripes (like in the image this is usually starts at 0:00 AM).

![bug-fix](https://gitlab.maikel.pro/uploads/maikeldus/WhatsSpy-Public/9f007f8bb0/bug-fix.png)

## 2) Fixing this issue

Connect your keyboard and screen to the Raspberry Pi (or use SSH). The login username is `pi` and password is `whatsspypublic`. Now run these commands:
```
rm /home/pi/whatsspy-public-startup
wget https://maikel.pro/etc/whatsspy-public-startup
chmod 777 whatsspy-public-startup
```

Because this problem created a lot of small tracker sessions you may want to clean your database:
```
psql -U postgres -d whatsspy
DELETE FROM tracker_history;
\q
```
*(or use `Ctrl + Z` in case you cant type `\q`)*

Now reboot the Raspberry Pi:
```
sudo reboot
```
The tracker will restart in 60-80 seconds and this problem is fixed.

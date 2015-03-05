# Raspberry Pi fix for [error] Tracker Exception! Connection Closed.

Anyone who downloaded the Rpi image before the 4 of march may have connection issues. 


You can identify this problem by going to the **compare** page and check if the blue background line is interrupted by a lot of white stripes (like in the image).

![bug-fix](https://gitlab.maikel.pro/uploads/maikeldus/WhatsSpy-Public/9f007f8bb0/bug-fix.png)

## 1) Fixing this issue

Connect your keyboard and screen to the Raspberry Pi (or use SSH). The login username is `pi` and password is `whatsspypublic`. Now run these commands:
```
rm /home/pi/whatsspy-public-startup
wget https://maikel.pro/service/whatsspy/whatsspy-public-startup
chmod 777 whatsspy-public-startup
```

Now reboot the Raspberry Pi:
```
sudo reboot
```
The tracker will restart in 60-80 seconds and this problem is fixed.

# How to properly shutdown the Raspberry Pi image


* Connect up a keyboard or connect using SSH.
* Type `screen -r`. Press `Ctrl` + `c` at the same time to stop the tracker.
* Type `sudo shutdown -h now`.

Now it will show some output about shutting down all services etc. Wait for it to not give any video output anymore. **After this you can safely remove the power cord**.

## What happens after sudden powerless

The Operating System can become corrupt which means all database information can be lost. In this case you need to follow the [tutorial again](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started-rpi-image) and overwrite your corrupt Operating System.
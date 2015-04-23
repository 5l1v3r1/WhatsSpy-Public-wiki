# Raspberry Pi Image tips

In case you followed the [Raspberry Pi Image](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started-rpi-image) tutorial any tips on this page might be of use for you.


## Shutdown your Raspberry Pi (in a safe way)


* Connect up a keyboard or connect using SSH.
* Type `screen -r`. Press `Ctrl` + `c` at the same time to stop the tracker.
* Type `sudo shutdown -h now`.
* You will see output on the screen about shutting down services etc. Wait until you no longer have video output.
* **After this you can safely remove the power cord**.

## Unsafe shutdown Raspberry Pi (pulled power cord without shutting down)

The Operating System can become corrupt which means all database information can be lost. In this case you need to follow the [tutorial again](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started-rpi-image) and overwrite your corrupt Operating System. The database can be considered lost.
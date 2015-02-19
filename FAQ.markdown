# Frequently asked Questions

## Updates

Check the [updatelog here](updates).

## How accurate is the tracker?

It tracks the following properties:

* Online/offline in realtime (10-60 seconds offset due to WhatsApp or your connection).
* Status messages in realtime (10-60 seconds offset due to WhatsApp or your connection).
* Last seen privacy / status message privacy settings every 2 hours.
* Profile picture (and privacy setting) every 3 hours.

## Automatic updates

```
su -s /bin/sh nobody -c 'cd var/www/WhatsSpy-Public/ && git pull -q origin master' >> /var/log/git.log
```
Thanks to @jXhn

## Automatic startup

For Debian/Raspbian/Ubuntu there is a startup script available in `api/tools/service-scripts/startup`. Check [this issue](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/7) for more information.
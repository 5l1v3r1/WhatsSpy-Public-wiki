# Frequently asked Questions

## Updates

Check the [updatelog here](updates).

## Troubleshooting

Check the [troubleshooting](troubleshooting) page.

## How accurate is the tracker?

It tracks the following properties:

* Online/offline in realtime (0-4 seconds offset due to WhatsApp or your connection).
* Status messages in realtime (0-4 seconds offset due to WhatsApp or your connection).
* Last seen privacy / status message privacy settings every 2 hours.
* Profile picture (and privacy setting) every 4 hours.

The more users it tracks, the less accurate it becomes with the online/offline statuses. One can say the tracker is really accurate on the minute (99% of the cases).

## In what ways can I try WhatsSpy Public

You have three options:

* **Normal**: [Setup WhatsSpy Public on a existing VPS/Rpi/Server](getting-started).
* **Raspberry Pi image**: [Use a Raspberry Pi image with WhatsSpy Public already on it](getting-started-rpi-image).
* **QEMU** Use the emulator QEMU with the Raspberry pi image [read experience](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/200).

## How to backup the database

Go to your terminal and use:
```
cd ~
pg_dump whatsspy -U postgres > whatsspy_backup.sql
```
*(where `whatsspy` is the database name)*

This command creates a file called `whatsspy_backup.sql` of the whole database in your home directory. You can copy this file over with for example `WinScp` (if you use SSH) or just copy it over to a USB drive).

## How to shutdown tracker

You can shutdown the tracker down via a proper method (closing the sessions in the database):

Use `screen -r` to re-attach to the tracker session. Now you can use `Ctrl` + `c` to shutdown the tracker process. After this you can quit the screen by using `exit`.

## Automatic updates

```
su -s /bin/sh nobody -c 'cd var/www/WhatsSpy-Public/ && git pull -q origin master' >> /var/log/git.log
```
Thanks to @jXhn

## Automatic startup

For Debian/Raspbian/Ubuntu there is a startup script available in `api/tools/service-scripts/startup`. Check [this issue](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/7) for more information.
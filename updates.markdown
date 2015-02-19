# WhatsSpy Public updates

This page contains notes about how to update a running instance of WhatsSpy Public. This is always relatively easy.


## Before/After updating

Make sure that you before the update undertake these steps:

* Shutdown the tracker by using `screen -r` and then pressing `Ctrl+c` and `Ctrl+a` and `Ctrl+d`.
* After the update start the tracker again by using `screen -r` and execute `` `which php` api/tracker.php``.


## Changelog

**Update 1.0.1/1.0.2/1.0.3** : Small UI and Tracker fix. Replace the `whatsspy/` folder with the one of the repository .

**Update 1.0.4** : Status message bug fixed. Same rules apply as for 1.0.3

**Update 1.0.5** : Fixed broken pipe and added DB check at startup. Same rules apply as for 1.0.3

**Update 1.0.6** : Better error reporting and LiveNotifier added. You can undertake the same steps as 1.0.3. Please add `$whatsspyLNKey = '';` to your current config.

**Update 1.0.7** : Fixed UTF8 support (works in FF only), added lastseen option and fixed some other small issues. same rules apply as `1.0.6`.

**Update 1.0.8/1.0.9** : Fix for people living in timezones which contain +/- 30 minutes. same rules apply as `1.0.6`.

**Update 1.1.0** : Implemented better error logging, implemented new page called "timeline" which shows realtime data. Fixed some timezone issues. same rules apply as `1.0.6`.

**Update 1.1.1/1.1.2** : Bug fix on file rights, PHP timezone and message throttling by WhatsApp. same rules apply as `1.0.6`.

**Update 1.1.3** : Cascading delete button in the UI, small timeline fix for "ghost" users. same rules apply as `1.0.6`.

**People that have problems setting up this project, you can register at this Gitlab server for any issues.**
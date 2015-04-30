# Tracker shutdown errors

WhatsSpy Public might stop tracking due to some unforseen reason. This will show up in the timeline page, this is why you need to keep a look on this page.

## Connection Closed!

There can be various reasons for a "connection closed" error. One of the most common problems is a unreliable internet connection, bad hardware or a busy network. 

If it keeps disconnecting with this message in a short timespan you need to stop the tracker, wait a few days and try to re-connect. This seems to be a problem at WhatsApp.

**Update**: Make sure you run [1.5.5](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/updates) or higher.

## Login failed!

Either the phonenumber is wrong, secret is wrong or your account has been disabled by WhatsApp. Make sure you do not need use a free SMS service. You need a legitimate phonenumber. Also make sure you [entered your phonenumber correct](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started#2-4-setup-the-config).

Regenerate the secret: https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started#2-3-retrieve-the-secret-for-a-secondary-whatsapp-account

And verify the phonenumber:

* Insert your `number` and `secret` in `$whatsappAuth`. 
  * `number` needs to be <countrycode><phonenumber> without any prefix 0's. 0031 06 xxx becomes 31 6 xxx (no 0's prefix for both the country code and phonenumber itself).
  * `number` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174 is correct*
  * `secret` You obtained this in the chapter *2.3) Retrieve the `secret` for a secondary WhatsApp account*.

## Tree length did not match received length!

Restart the tracker. If this does not work wait a few hours and try again. This is an unknown problem caused at WhatsApp side.
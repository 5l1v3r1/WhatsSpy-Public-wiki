# Frequently asked Questions

## Updates

Check the [updatelog here](updates).

## Automatic updates

```
su -s /bin/sh nobody -c 'cd var/www/WhatsSpy-Public/ && git pull -q origin master' >> /var/log/git.log
```
Thanks to @jXhn

## Automatic startup

For Debian/Raspbian/Ubuntu there is a startup script available in `api/tools/service-scripts/startup`. Check [this issue](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/7) for more information.
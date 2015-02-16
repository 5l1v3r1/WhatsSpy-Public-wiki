

## Automatic updates

```
su -s /bin/sh nobody -c 'cd var/www/WhatsSpy-Public/ && git pull -q origin master' >> /var/log/git.log
```
Thanks to @jXhn
# Getting started
*This guide requires knowledge of Linux, PHP, PostgreSQL. There is no proper error reporting. I'm not responsible in any way if you screw it up! WhatsApp might even block your account, I can't tell.*

*Please do not attempt to setup this project if you have no IT knowledge whatsoever! And do not try to set this up on Windows! You're better off with a Virtual Machine containing Ubuntu.*

**Blog articles:**

* [General introduction to WhatsSpy Public](https://maikel.pro/blog/en-whatsapp-privacy-options-are-illusions/)
* [In-depth about the WhatsApp privacy problem](https://maikel.pro/blog/en-whatsapp-privacy-problem-explained-in-detail/)

## Requirements

**Shortlist requirements:**

* Secondary Whatsapp account (phonenumber that doesn't actively uses WhatsApp, because you can't receive messages when the tracker is online)
* Rooted Android phone **OR** Jailbroken iPhone **OR** Use [WART](https://github.com/shirioko/WART) to retrieve the WhatsApp 'secret'
* Linux Server/RPi that runs 24/7 (tested on Debian/Ubuntu) (Do not use Windows, use a [virtual machine instead](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html))
* Nginx or Apache with PHP with PDO (php5-pgsql installed) (you can't host on simple webhoster, you need a terminal!)
* PostgreSQL


## Notice - Secondary WhatsApp account
WhatsSpy Public requires a **secondary Whatsapp account**. Once the tracker is started, you will not be able to receive any messages over WhatsApp for this phonenumber. You can either try to register a non-Whatsapp used phonenumber with [WART](https://github.com/shirioko/WART) or just buy an 5 euro SIM Card and activate this with your jailbroken iPhone or rooted Android phone. You need to do this registration to get a 'secret' from WhatsApp which WhatsSpy Public will use to login.

**The 5 euro SIM-card solution:**

For the tracker to work you need an secret which is retrieved from either your Phone or the register script mentioned above. In case of phone registration you need an **jailbroken iPhone** or **rooted Android** device in order to retrieve the secret. 

* Jailbroken iPhone users: You can retrieve using [this script](https://www.mgp25.com/utilidadiPhone/).
* Rooted Android phones can use the [following APK](https://github.com/venomous0x/WhatsAPI/issues/983) to retrieve the secret. 

In order to retrieve the scecret you need to follow these steps:

* Insert your (new) secondary SIM card in your phone and boot it up.
* Re-install Whatsapp on your phone and activate it using the new phonenumber.
* Use either the APK (Android) or the script (iPhone) to retrieve the WhatsApp secret. Write this secret down, which is required later. 
* Insert your normal SIM card and re-install WhatsApp for normal use.

**[Do not use the WhatsApp application with the same phonenumber as the tracker, this will reset the connection creating an dead tracker.](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/4)**

## Installation

**[Troubleshooting](troubleshooting)**

1. Download the [repository](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/tree/master) and unpack these files on your server at for example `/whatsspy/` in your web directory (for nginx in debian this is `/var/www/`). **note that the git clone does not work for SSH. You can only use HTTP on this gitlab server**

2. Log in your PostgreSQL database and create an new DB and user for WhatsSpy Public **(Insert password for DB user)**:
```
-- Execute command by command!
-- cmd 1
CREATE ROLE whatsspy LOGIN
  PASSWORD ''
  NOSUPERUSER INHERIT NOCREATEDB NOCREATEROLE NOREPLICATION;
-- cmd 2
CREATE DATABASE whatsspy
  WITH OWNER = whatsspy
       ENCODING = 'UTF8'
       TABLESPACE = pg_default
       CONNECTION LIMIT = -1;
-- cmd 3
GRANT ALL ON DATABASE whatsspy TO whatsspy;
```

3. Open `api/whatsspy-db.sql` and execute these SQL commands in your **whatsspy** database (with PgAdmin or step 4). 

4. **Update**: You can use the commmand 
```
cd <location of whatsspy>/api/
psql -U whatsspy -d whatsspy -f whatsspy-db.sql
``` 
to insert these SQL statements in the correct database.

5. rename `config.example.php` to `config.php` located at `api/` and fill in the following details: 

6. Postgresql host/port/dbname/user and password correctly in `$dbAuth`.
7. Insert your 'number' and 'secret' in `$whatsappAuth`. 
8. **'number'** needs to be <countrycode><phonenumber> without any prefix 0's. 0031 06 xxx becomes 31 6 xxx (no 0's prefix for both the country code and phonenumber itself).
9. **'number'** may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
10. **'secret'** If you don't have this yet, read [Notice (scroll up)](#notice)
11. Set the absolute path correct in `$whatsspyProfilePath`. If you've installed WhatsSpy Public in for example `/var/www/whatsspy` the correct directory would be `/var/www/whatsspy/images/profilepicture/` (including `/`)
12. You can set an Optional NotifyMyAndroid key for notifications about the tracker (startup,shutdown,errors etc) in `$whatsspyNMAKey`. 
13. **Check folder rights: the tracker needs read/write acces in both the folder `$whatsspyProfilePath` and `api/`!**
```
# These are guidelines. For debugging you can use 777 instead of 760.
chgrp www-data -R <location-of-whatsspy-public>/api/
chgrp www-data -R <location-of-the-$whatsspyProfilePath-you-set-in-config.php>
chmod 760 -R <location-of-whatsspy-public>/api/
chmod 760 -R <location-of-the-$whatsspyProfilePath-you-set-in-config.php>
```
### Webserver

You need to restrict access to Whatsspy and the api of Whatsspy from unauthorised web access. 

#### Nginx
For Nginx add the following:

```
location ~ /whatsspy/ {
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd; 
    }

    location /whatsspy/api/whatsapp/ {
        deny all;
        return 404;
    }

    location ~ /whatsspy/api/ {
        rewrite ^ /whatsspy/api/index.php last;
    }
``` 
*assuming you installed whatsspy in a subdirectory called `/whatsspy` in the web directory `/var/www/` (default setup)*

You can create an [.htpasswd here](http://www.htaccesstools.com/htpasswd-generator/). Make sure you reload the configuration by executing `service nginx reload`.

#### Apache
create an `.htaccess` in the whatsspy folder and add the following:

```
AuthType Basic
AuthName "Password Protected Area"
AuthUserFile /var/.htpasswd
Require valid-user
```
Do not place the .htpasswd in the `/var/www` folder. You can create an [.htpasswd here](http://www.htaccesstools.com/htpasswd-generator/). The `api/` folder is protected by default.

## Importing users

If everything went well you can now access the WhatsSpy Public interface through your webserver. At this point you need to import users that you want to track ([Troubleshooting](troubleshooting)):

* Either add any contact manually by using "Add contact by phonenumber".
* Or use "import google Contacts" which is an script that retrieves all your Google Contacts and gives an SQL statement which insert all users into the database.

**Once you have inserted these users they won't show up automatically. They need to be verified by the tracker which is not running yet.**

## Starting the tracker
Once you have populated your database with some users, you can start the tracker.

1. start a new `screen` (if you do not have screen: `sudo apt-get install screen` or similar for other distro's)
3. cd to the install of the Whatsspy (for example `/var/www/whatsspy/`) and execute `` `which php` api/tracker.php``.
4. If all runs well it starts spamming information about privacy options and polls.
5. It keeps polling every 2 seconds and outputs any statusses on the screen.
6. You can exit the screen by using `Ctrl+a` and after that `Ctrl+d` (detaching the screen) in your terminal/Putty.


## Other things you can do

Do you want to start WhatsSpy Public on startup? Do you want to auto-update? Please check the [FAQ](FAQ).
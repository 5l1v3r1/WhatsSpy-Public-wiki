# Getting started
*This guide requires knowledge of Linux, PHP, PostgreSQL. I'm not responsible in any way if you screw it up! WhatsApp might even block your account, I can't tell.*

*Please do not attempt to setup this project if you have no IT knowledge whatsoever! And do not try to set this up on Windows! You're better off with a Virtual Machine containing Ubuntu. You can also try the [Raspberry Pi image](getting-started-rpi-image) (more clear and less steps to undertake)*

**Blog articles:**

* [General introduction to WhatsSpy Public](https://maikel.pro/blog/en-whatsapp-privacy-options-are-illusions/)
* [In-depth about the WhatsApp privacy problem](https://maikel.pro/blog/en-whatsapp-privacy-problem-explained-in-detail/)

## Requirements

* Secondary WhatsApp account (phonenumber that doesn't actively uses WhatsApp, because you can't receive messages when the tracker is online).
* [WART](https://github.com/shirioko/WART) on your Windows desktop **OR** Rooted Android phone **OR** Jailbroken iPhone to retrieve the WhatsApp `secret`.
* Linux Server/VPS/RPi that runs 24/7 (tested on Debian/Ubuntu) (**Do not use Windows**, use a [virtual machine instead](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html)).
* Nginx or Apache with PHP with PDO (you can't host on simple webhoster, **you need a terminal**!)
* PostgreSQL



## 1) Secondary WhatsApp account
WhatsSpy Public requires a **secondary Whatsapp account**. Once the tracker is started, you will not be able to receive any messages over WhatsApp for this phonenumber. You need to register at WhatsApp to retrieve a `secret` which you will need later when settting up WhatsSpy Public.

### 1.1) Getting a phonenumber

You need a phone(number) over which you can recieve SMS or voice calls for the WhatsApp activation. You can try to register your landline, or just buy a 5 euro SIM card and register that phonenumber at WhatsApp. Do not use some shady SMS service, this won't work.

### 1.2) Activation at WhatsApp with your secondary phonenumber

You have two methods (stick with WART if you do not have a rooted/jailbroken phone):

* Use [WART](https://github.com/shirioko/WART), a Windows registration tool which allows you to retrieve the secret via a GUI.
* Activate WhatsApp on your jailbroken iPhone or rooted Android phone and retrieve the secret via [this script](https://www.mgp25.com/utilidadiPhone/) (for iPhone users) or the [following APK](https://github.com/venomous0x/WhatsAPI/issues/983) (for Android users).

In case of registration via WART:

* Download [WART](https://github.com/shirioko/WART/blob/master/WART-1.7.3.0.exe?raw=true).
* Open up WART and fill in `phonenumber` and `password` (just choose one).
* `phonenumber` needs to be the countrycode+phonenumber.
* `phonenumber` needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
* `phonenumber` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*.
* Press the request code. You will receive a SMS with the activation code from WhatsApp.
* Enter this code in **Step 2** and press confirm code.
* Write down the `secret` (it's the one-line of strange characters ending with an =).


In case of registration via phone you need a **jailbroken iPhone** or a **rooted Android** device in order to retrieve the secret. In order to retrieve the scecret you need to follow these steps:

* Insert your (new) secondary SIM card in your phone and boot it up.
* Re-install WhatsApp on your phone and activate it using the new phonenumber.
* Use either the APK (Android) or the script (iPhone) to retrieve the WhatsApp secret.
* Write down the `secret` (it's the one-line of strange characters ending with an =).
* Insert your normal SIM card and re-install WhatsApp for normal use.

**[Do not use the WhatsApp application on your phone when the tracker is running, this creates a broken connection.](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/4)**

## 2) Installation
### 2.1) Make the choice

As of now you have two options to install WhatsSpy Public:

* [Download a Raspbian image with WhatsSpy Public pre-installed. This requires a lot less steps.](getting-started-rpi-image)
* Follow the manual installation (below) for your Raspberry Pi / VPS / Server

### 2.2) Manual install (Debian/Ubuntu)

**[troubleshooting](troubleshooting)**

1. Install the following packages:
```
sudo apt-get install postgresql nginx php5 php5-cli php5-curl php5-fpm php5-pgsql git-core screen
```

2. Follow [this tutorial](http://www.thegeekstuff.com/2013/12/nginx-php-fpm/) to setup Nginx with PHP-FPM.

3. Allow local connections for the user `postgres` in PostgreSQL. Check [this post](http://stackoverflow.com/questions/17443379/psql-fatal-peer-authentication-failed-for-user-dev) how to enable this.

4. Make a folder on your machine: `mkdir /var/www/whatsspy/`. After this download WhatsSpy Public:
```
cd /var/www/whatsspy/
git init
git remote add origin https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public.git
git pull
```
*(Please note that SSH does not work on this Gitlab, only HTTPS)*


5. Execute the following commands (choose a password for the user at `cmd 1`):
```
psql -U postgres
-- Execute command by command!
-- cmd 1 (choose a password)
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
\q
```
*(or use `Ctrl`+`Z` in case you can't type `\q`)*

6. Now it is time to insert the WhatsSpy Public database in PostgreSQL. Execute the following commands:
```
cd <location of whatsspy>/api/
psql -U postgres -d whatsspy -f whatsspy-db.sql
``` 

7. rename `config.example.php` to `config.php` located at `api/` and fill in the following details: 

* Postgresql host/port/dbname/user and password correctly in `$dbAuth`.
* Insert your `'number'` and `'secret'` in `$whatsappAuth`. 
  * `'number'` needs to be <countrycode><phonenumber> without any prefix 0's. 0031 06 xxx becomes 31 6 xxx (no 0's prefix for both the country code and phonenumber itself).
  * `'number'` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
  * `'secret'` If you don't have this yet, read chapter 1) Secondary WhatsApp account.
* Set the correct timezone of the place where you are.
* Set the absolute path correct in `$whatsspyProfilePath`. If you've installed WhatsSpy Public in for example `/var/www/whatsspy` the correct directory would be `/var/www/whatsspy/images/profilepicture/` (including `/`)
* Set the path correct in `$whatsspyWebProfilePath`. This path is to make sure you access the `$whatsspyProfilePath` from the web. If you've installed WhatsSpy Public in for example `/var/www/whatsspy` the correct directory would be `/whatsspy/images/profilepicture/` (including `/` and cut the `/var/www`)
* You can set an Optional NotifyMyAndroid key for notifications about the tracker (startup,shutdown,errors etc) in `$whatsspyNMAKey` or `$whatsspyLNKey`. 

8. **Check folder rights: the tracker needs read/write acces in both the folder `$whatsspyProfilePath`, `api/whatsapp/src/wadata/` and `api/`!**

```
# These are guidelines. For debugging you can use 777 instead of 760.
sudo chown www-data:www-data -R <location-of-whatsspy-public>/api/
sudo chown www-data:www-data -R <location-of-the-$whatsspyProfilePath-you-set-in-config.php>
sudo chmod 760 -R <location-of-whatsspy-public>/api/
sudo chmod 760 -R <location-of-the-$whatsspyProfilePath-you-set-in-config.php>
```

### Webserver

You need to restrict access to Whatsspy and the api of Whatsspy from unauthorised web access. I assume you have Nginx and PHP already up and running (you can follow [this tutorial in case you dont](https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-debian-7))

#### Nginx
For Nginx add the following:

```
    index index.html index.php;
    location / {
        try_files $uri $uri/ /index.html;
    }

    location ~ /whatsspy/ {
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd; 
    }

    location /whatsspy/api/whatsapp/ {
        deny all;
        return 404;
    }

    location ~ /whatsspy/api/((?!index\.php).+) {
        deny all;
        return 404;
    }
``` 
*assuming you installed whatsspy in a directory called `/var/www/whatsspy` and remember to put the nginx location for php processing (location ~ \.php$ ....)*

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

## 3) Importing users

If everything went well you can now access the WhatsSpy Public interface through your webserver. At this point you need to import users that you want to track ([Troubleshooting](troubleshooting)):

* Either add any contact manually by using "Add contact by phonenumber".
* Or use "import google Contacts" which is an script that retrieves all your Google Contacts and gives an SQL statement which insert all users into the database.

**Once you have inserted these users they won't show up automatically. They need to be verified by the tracker which is not running yet.**

## 4) Starting the tracker
Once you have populated your database with some users, you can start the tracker.

1. start a new `screen` (if you do not have screen: `sudo apt-get install screen` or similar for other distro's)
3. cd to the install of the Whatsspy (for example `/var/www/whatsspy/`) and execute `` `which php` api/tracker.php``.
4. If all runs well it starts spamming information about privacy options and polls.
5. It keeps polling every second and outputs any statuses on the screen.
6. You can exit the screen by using `Ctrl+a` and after that `Ctrl+d` (detaching the screen) in your terminal/Putty.


## Other things you can do

Do you want to start WhatsSpy Public on startup? Do you want to auto-update? Please check the [FAQ](FAQ).
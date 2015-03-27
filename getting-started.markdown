# Getting started

*Please do not attempt to setup this project if you have no IT knowledge whatsoever! And do not try to set this up on Windows! You're better off with a Virtual Machine containing Ubuntu. You can also try the [Raspberry Pi image](getting-started-rpi-image) (more clear and less steps to undertake)*

*Please note that there might be still install bugs or incorrect setup instructions. If you get stuck just create an account on this gitlab server.*

**Blog articles:**

* [General introduction to WhatsSpy Public](https://maikel.pro/blog/en-whatsapp-privacy-options-are-illusions/)
* [In-depth about the WhatsApp privacy problem](https://maikel.pro/blog/en-whatsapp-privacy-problem-explained-in-detail/)

## Requirements

* Secondary WhatsApp account (phonenumber that doesn't actively uses WhatsApp, because you can't receive messages when the tracker is online).
* [WART](https://github.com/mgp25/WART/) on your Windows desktop **OR** Rooted Android phone **OR** Jailbroken iPhone to retrieve the WhatsApp `secret`.
* Linux Server/VPS/RPi that runs 24/7 (tested on Debian/Ubuntu) (**Do not use Windows**, use a [virtual machine instead](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html)).
* Nginx or Apache with PHP with PDO (you can't host on simple webhoster, **you need a terminal**!)
* PostgreSQL



## 1) Secondary WhatsApp account
WhatsSpy Public requires a **secondary Whatsapp account**. Once the tracker is started, you will not be able to receive any messages over WhatsApp for this phonenumber. You need to register at WhatsApp to retrieve a `secret` which you will need later when settting up WhatsSpy Public.

### 1.1) Getting a phonenumber

You need a phone(number) over which you can recieve SMS or voice calls for the WhatsApp activation. You can try to register your landline, or just buy a 5 euro SIM card and register that phonenumber at WhatsApp. Do not use some shady SMS service, this won't work.

### 1.2) Activation at WhatsApp with your secondary phonenumber

You have three methods (stick with WART if you do not have a rooted/jailbroken phone):

* Use [WART](https://github.com/mgp25/WART/), a Windows registration tool which allows you to retrieve the secret via a GUI.
* Use a supplied PHP script `registerTool.php` that will guide you through the steps (use if WART does not work).
* Activate WhatsApp on your jailbroken iPhone or rooted Android phone and retrieve the secret via [this script](https://www.mgp25.com/utilidadiPhone/) (for iPhone users) or the [following APK](https://github.com/venomous0x/WhatsAPI/issues/983) (for Android users).

**1)** In case of registration via WART:

**WART might tell you the activation code is wrong, use method two in this case.**

* Download [WART](https://github.com/mgp25/WART/blob/master/WART-1.8.0.0.exe?raw=true).
* Open up WART and fill in `phonenumber` and `password` (just choose one).
  * `phonenumber` needs to be the countrycode+phonenumber.
  * `phonenumber` needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
  * `phonenumber` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*.
* Press the request code. You will receive a SMS with the activation code from WhatsApp.
* Enter this code in **Step 2** and press confirm code.
* Write down the `secret` (it's the one-line of strange characters ending with an =).

**2)** In case of registration via the `registerTool.php` script you need to execute the following steps:

* First follow the step **4** under **2.2) Manual installation** (mentioned below).
* Make sure you are still in `/var/www/whatsspy/` (by using `cd`).
* Execute `php api/whatsapp/registerTool.php`.
* Request activation via SMS or Voice.
* Enter the retrieved code in the script.
* Write down the `secret` (it's the one-line of strange characters ending with an =).


**3)** In case of registration via phone you need a **jailbroken iPhone** or a **rooted Android** device in order to retrieve the secret. In order to retrieve the scecret you need to follow these steps:

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
git pull origin master
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
cd /var/www/whatsspy/api/
psql -U postgres -d whatsspy -f whatsspy-db.sql
``` 

7. rename `config.example.php` to `config.php` located at `api/` and fill in the following details: 

* Postgresql host/port/dbname/user and password correctly in `$dbAuth`.
* Insert your `number` and `secret` in `$whatsappAuth`, which you have obtained following chapter *1) Secondary WhatsApp account*. 
  * `number` needs to be <countrycode><phonenumber> without any prefix 0's. 0031 06 xxx becomes 31 6 xxx (no 0's prefix for both the country code and phonenumber itself).
  * `number` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174 is correct*
  * `secret` If you don't have this yet, read chapter 1) Secondary WhatsApp account.
* Set the correct timezone of the place where you are.
* In case you did **not** install WhatsSpy Public in `/var/www/whatsspy/`, set the paths for `$whatsspyProfilePath` and `$whatsspyWebProfilePath` .
  * `$whatsspyProfilePath` is the absolute path for the system to store the profile pictures. For example `/var/www/whatsspy/images/profilepicture/` (default setting), `/var/www/other-dir/images/profilepicture/`. Don't forget the last `/`!
  * `$whatsspyWebProfilePath` is the path for web users to access the profile pictures. This varible is used in this manner `http://localhost/<this-is-the-var>/some-profile-picture.jpg`. Again, don't forget the `/` at the end).


**Check folder rights: the tracker needs read/write acces in the folder `$whatsspyProfilePath`, `api/whatsapp/src/wadata/` and `api/`!**

```
# These are guidelines. For debugging you can use 777 instead of 760.
# In case you chose other a $whatsspyProfilePath, you need to use this variable.
sudo chown www-data:www-data -R /var/www/whatsspy/api/
sudo chown www-data:www-data -R /var/www/whatsspy/images/profilepicture/
sudo chmod 760 -R /var/www/whatsspy/api/
sudo chmod 760 -R /var/www/whatsspy/images/profilepicture/
```

### Webserver

You need to restrict access to WhatsSpy Public and the API of WhatsSpy Public from unauthorized web access.

#### Nginx
We need to update your Nginx configuration to restrict access:

`sudo nano /etc/nginx/sites-available/default`

And make sure your configuration looks like:
```
server {
    listen 80;
    root /var/www;
    index index.html index.php;

    location /whatsspy/ {
        auth_basic "Restricted";
        auth_basic_user_file /etc/nginx/.htpasswd; 
    }

    location /whatsspy/api/whatsapp/ {
        deny all;
        return 404;
    }

    location ~ ^/whatsspy/api/((?!index\.php).+) {
        deny all;
        return 404;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

}
``` 
In case you installed your WhatsSpy Public in a other place you need to edit the paths slightly.

You can create an [.htpasswd here](http://www.htaccesstools.com/htpasswd-generator/). Copy the output of this generator and save it:
```
sudo nano /etc/nginx/.htpasswd
```
Paste in the generated code and save it by using `Ctrl`+`X`, type `y` and press enter.

Make sure you reload the configuration by executing `sudo service nginx reload`.

#### Apache
create an `.htaccess` in the WhatsSpy Public folder and add the following:

```
AuthType Basic
AuthName "Password Protected Area"
AuthUserFile /var/.htpasswd
Require valid-user
```
Do not place the .htpasswd in the `/var/www/..` folder, but store it somewhere save (like `/var/`?). You can create an [.htpasswd here](http://www.htaccesstools.com/htpasswd-generator/). The `api/` folder is protected by default.

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
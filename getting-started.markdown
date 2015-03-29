# Getting started

*Please do not attempt to setup this project if you have no IT knowledge whatsoever! And do not try to set this up on Windows! You're better off with a Virtual Machine containing Ubuntu. You can also try the [Raspberry Pi image](getting-started-rpi-image) (more clear and less steps to undertake)*

*Please note that there might be still install bugs or incorrect setup instructions. If you get stuck just create an account on this gitlab server.*

**Blog articles:**

* [General introduction to WhatsSpy Public](https://maikel.pro/blog/en-whatsapp-privacy-options-are-illusions/)
* [In-depth about the WhatsApp privacy problem](https://maikel.pro/blog/en-whatsapp-privacy-problem-explained-in-detail/)

## 1) Check requirements

* Secondary WhatsApp account (phonenumber that doesn't actively uses WhatsApp, because you can't receive messages when the tracker is online).
* Linux Server/VPS/RPi that runs 24/7 (tested on Debian/Ubuntu) (**Do not use Windows**, use a [virtual machine instead](http://www.oracle.com/technetwork/server-storage/virtualbox/downloads/index.html)).
* Nginx or Apache with PHP with PDO (you can't host on simple webhoster, **you need a terminal**!)
* PostgreSQL

**In case you don't have these resources you can also check out the [Raspberry Pi image](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started-rpi-image) (or [emulate it](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/200))**

## 2) Manual install (Debian/Ubuntu)

**[troubleshooting](troubleshooting)**

### 2.1) Required packages and config

Install the following packages:
```
sudo apt-get install postgresql nginx php5 php5-cli php5-curl php5-fpm php5-pgsql git-core screen
```

**1)** Verify that your PHP-FPM installation uses the Unix socket:
```
sudo nano /etc/php5/fpm/pool.d/www.conf
```

The following line should be in the config file (and **NOT** `listen = 127.0.0.1`):
```
listen = /var/run/php5-fpm.sock
```

**2)** Make sure that your PostgreSQL allows local connections for the user `postgres`:

```

sudo nano /etc/postgresql/9.1/main/pg_hba.conf
``` 
Search for the following line:
```
local   all             postgres                                peer
```

and change it to:
```
local   all             postgres                                trust
```
Reload the configuration by using `sudo service postgresql reload`.

### 2.2) Download WhatsSpy Public

Make a folder on your machine: `/var/www/whatsspy/` (with `mkdir`). After this download WhatsSpy Public:
```
cd /var/www/whatsspy/
git init
git remote add origin https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public.git
git pull origin master
```
*(Please note that SSH does not work on this Gitlab, only HTTPS)*

### 2.3) Retrieve the `secret` for a secondary WhatsApp account
WhatsSpy Public **requires** a secondary WhatsApp account. Once the tracker is started, you will not be able to receive any messages over WhatsApp for this phone number. You need to register at WhatsApp to retrieve a `secret` which you will need later when setting up WhatsSpy Public.

   * Execute `php /var/www/whatsspy/api/whatsapp/registerTool.php`.
   * Enter your phonenumber that you want to use for the WhatsSpy Public tracker (You cannot use this for WhatsApp while the tracker is running).
      * `number` needs to be <countrycode><phonenumber> without any prefix 0's. *0031 06 120..* becomes *31 6 120..* (no 0's prefix for both the countrycode and phonenumber itself).
      * `number` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174*
   * Request activation via `SMS` and wait for a SMS to arrive at the phone.
   * Enter the retrieved code in the script without any dashes (only the digits!).
   * Write down the `secret` (it's the one-line of strange characters ending with an =).

*There are [two other methods described here](ways-of-getting-the-secret) in case this method does not work.*

### 2.4) Setup database

Login into the database:
```
psql -U postgres
```
*(In case you get an authentication error you did not setup the PostgreSQL authentication correct)*

Create the WhatsSpy user in the database (choose a password):
```
CREATE ROLE whatsspy LOGIN
  PASSWORD 'CHOOSEAPASSWORD'
  NOSUPERUSER INHERIT NOCREATEDB NOCREATEROLE NOREPLICATION;
```
*(Using another database username means you need to change every reference in `whatsspy-db.sql`)*

Create database:
```
CREATE DATABASE whatsspy
  WITH OWNER = whatsspy
       ENCODING = 'UTF8'
       TABLESPACE = pg_default
       CONNECTION LIMIT = -1;
```

Grant the rights:
```
ALTER DATABASE whatsspy OWNER TO whatsspy;
GRANT ALL ON DATABASE whatsspy TO whatsspy;
```

Quit the database:
```
\q
```
*(or use `Ctrl`+`Z` in case you can't type `\q`)*

Now it is time to insert the WhatsSpy Public database in PostgreSQL. Execute the following commands:
```
cd /var/www/whatsspy/api/
psql -U postgres -d whatsspy -f whatsspy-db.sql
``` 

### 2.4) Setup the config

Copy `config.example.php` to `config.php` located at `/var/www/whatsspy/api/` and fill in the following details: 

* Postgresql host/port/dbname/user and password correctly in `$dbAuth`.
* Insert your `number` and `secret` in `$whatsappAuth`. 
  * `number` needs to be <countrycode><phonenumber> without any prefix 0's. 0031 06 xxx becomes 31 6 xxx (no 0's prefix for both the country code and phonenumber itself).
  * `number` may only contain digits. Spaces, plus or any other special character are NOT accepted. *Example: 316732174 is correct*
  * `secret` You obtained this in the chapter *2.3) Retrieve the `secret` for a secondary WhatsApp account*.
* Set the correct timezone of the place where you are.
* In case you did **not** install WhatsSpy Public in `/var/www/whatsspy/`, set the path for `$whatsspyProfilePath`.
  * `$whatsspyProfilePath` is the absolute path for the system to store the profile pictures. For example `/var/www/whatsspy/images/profilepicture/` (default setting), `/var/www/other-dir/images/profilepicture/`. Don't forget the last `/`!

### 2.5) Correct file rights

The tracker needs read/write acces in the folder `$whatsspyProfilePath`, `api/whatsapp/src/wadata/`.

```
# These are guidelines. For debugging you can use 777 instead of 760.
# In case you chose other a $whatsspyProfilePath, you need to use this variable.
sudo chown www-data:www-data -R /var/www/whatsspy/api/whatsapp/src/wadata/
sudo chown www-data:www-data -R /var/www/whatsspy/images/profilepicture/
sudo chmod 760 -R /var/www/whatsspy/api/whatsapp/src/wadata/
sudo chmod 760 -R /var/www/whatsspy/images/profilepicture/
```

### 2.6) Configure web server

You need to restrict access to WhatsSpy Public and the API of WhatsSpy Public from unauthorized web access. We need to update your Nginx configuration to restrict access:

`sudo nano /etc/nginx/sites-available/default`

And make sure your configuration looks like:
```
server {
    listen 80;
    root /var/www;
    index index.html index.php;

    
    location /whatsspy/api/whatsapp/ {
        deny all;
        return 404;
    }

    location /whatsspy/images/profilepicture/ {
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
In case you installed your WhatsSpy Public in a other place you need to edit the paths slightly. Make sure you reload the configuration by executing `sudo service nginx reload`.

## 3) Importing users

If everything went well you can now access the WhatsSpy Public interface through your webserver. At this point you need to import users that you want to track ([Troubleshooting](troubleshooting)):

* Either add any contact manually by using "Add contact by phonenumber".
* Or use "import google Contacts" which is an script that retrieves all your Google Contacts and gives an SQL statement which insert all users into the database.

**Once you have inserted these users they won't show up automatically. They need to be verified by the tracker which is not running yet.**

## 4) Starting the tracker
Once you have populated your database with some users, you can start the tracker.

1. Execute `screen`
3. cd to the install of the Whatsspy (`cd /var/www/whatsspy/`) and execute `` `which php` api/tracker.php``.
4. If all runs well it starts spamming information about privacy options and polls.
5. It keeps polling every second and outputs any statuses on the screen.
6. You can exit the screen by using `Ctrl+a` and after that `Ctrl+d` (detaching the screen) in your terminal/Putty.

## 10) Optional steps / handy things to know

* You can enable notifications via *NotifyMyAndroid*, *LiveNotifier* or even WhatsApp. For this you need to edit your `config.php` (like before) and change entries in `$whatsspyNotificatons`. After saving your change restart the tracker.
* If you want to share profiles via the internet you need to make sure your installation is accessible from the internet.

Please check the [FAQ](FAQ).
# Troubleshooting

You can create an account on this Gitlab server and ask questions in the Issues section of this project.

### Login failure

```
[error] Tracker Exception! Login failure!
```
Make sure the `number` and `secret` are correct. First follow the [generation step](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started#2-3-retrieve-the-secret-for-a-secondary-whatsapp-account) again and then edit/verify [your `config.php`](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started#2-4-setup-the-config)

### PHP Warning: file_put_contents ... Permission Denied error while starting tracker

Make sure the files have the correct permissions. Follow [this step again](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/wikis/getting-started#25-correct-file-rights) and make sure to start the tracker with `sudo -u www-data `which php` tracker.php`

### Raspberry pi image: Couldn´t resolve host gitlab.maikel.pro

Wait for the Raspberry Pi to have an internet connection, this may take a while on some networks. You can test if your internet is working by executing `ping google.com`.

### An error occurred. please check your configuration (Blank page)

`http://<ipaddress>/whatsspy/api/?whatsspy=getStats` gives a blank page.

Check your `/var/log/nginx/error.log`. The blank page is caused by a internal server error which is an severe exception.

### Tracker says "An error occured, please check your configuration."

#### Problems while setting it up

Check if `/whatsspy/api/?whatsspy=getStats` gives a blank page.

Check the error logs at `/var/log/nginx/error.log` if you use Nginx. Apache would be roughly the same.

You might have one of the following problems:

* [PDO](http://php.net/manual/en/book.pdo.php) might be not installed in your PHP (make sure you edit the `php.ini` for both the CGI and CLI).
* Make sure you have the newest PHP (5.4+)
* Your configuration might be wrong: Does the PostgreSQL username and password actually work? Does the user have rights to acces the tables? etc.
* Make sure Nginx runs (instead of something like Apache, use `sudo service apache stop && sudo service nginx restart` to be sure)

Try running in case you get any PDO errors: `sudo apt-get install nginx php5-pgsql postgresql php5-curl php5-cli php5`

### Broken pipe

If the network is unreliable or the hardware very busy this might occur. Make sure you track on a device that is not under full load and the network has not too much switches etc. in between.

### PHP Parse error: `syntax error, unexpected '[' ...`

Make sure you have the newest PHP (5.4+).


### Error: Tree length did not match received length

This is a problem occuring at WhatsApp and there is no way to fix this. This problem will resolve itself within a day. Make sure you keep the tracker offline for a few hours so you don't spam login requests.

### I don't have any notification options

You need to enable notification options in your `config.php`. After filling these options in you need to restart the tracker.

### I have started the tracker but the UI shows "tracker offline"

The UI comminucates with the tracker via PostgreSQL. If this problem occurs it means there is something going wrong with PostgreSQL:

* Check if all the tables exist in the correct database `whatsspy`.
* Make sure the connecting database user has rights to read/write/delete in any of these tables.
* Make sure the PDO is working.
* Make sure you understand the difference between **schema** and **database**. You need to use the **database whatsspy** and **schema public**.

To test any database issues try the following in the terminal:
```
psql -U postgres
\connect whatsspy
SELECT * FROM accounts;
```
If the last command returns that there is no relation called accounts you have probably inserted the tables in the wrong database.

You can insert in the correct database by executing the following command:
```
cd <location of whatsspy>/api/
psql -U postgres-d whatsspy -f whatsspy-db.sql
```
**For these commands to work you need to have created the database whatsspy**

### Wrong timezone set

If the tracker reports incorrect online/offline activites there is probably a misconfiguration:

* Check if the timezone in the `config.php` is set properly.

### Error after update

Check what the URL `api/?whatsspy=getStats` gives you. It might say something like: 
```
The following error occured when trying to upgrade DB:
<something more specific here>
```

This will probably a DB right issue, which you need to fix for future updates. But you can manually update by using this command:
```
psql -U postgres -d whatsspy -f update/database-<version>.sql
```

#### Error after database update

Some updates have a small database adjustment which can cause problems. Check if the `api/?whatsspy=getStats` yields any "The following error occured when trying to upgrade DB:" error. Usually the problem is that the `whatsspy` database user has no rights to alter the database table. You can do the following to adjust this:

```
psql -U postgres
\connect whatsspy

ALTER TABLE accounts
  OWNER TO whatsspy;
GRANT ALL ON TABLE accounts TO whatsspy;

\q
```
Make sure in the future that all the tables belong to the user `whatsspy` (thus repeating these steps for all the tables). 

If this does not fix the problem use:

```
chmod 777 -R /var/www/whatsspy/
```
(chmod 777 is generally not a good practice, but on your Rpi this should be no problem).



### IPTables rules

Whatsapp uses an HTTPS connection which is going outbound (example for `eth0`):

`iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT`
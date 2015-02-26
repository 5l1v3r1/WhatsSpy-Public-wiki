# Troubleshooting

You can create an account on this Gitlab server and ask questions in the Issues section of this project.

### Tracker says "An error occured, please check your configuration."

Check if `api/?whatsspy=getStats` (if you have installed WhatsSpy Public in `/whatsspy/` this should be `/whatsspy/api/?whatsspy=getStats`) gives any error.

You might have one of the following problems:

* [PDO](http://php.net/manual/en/book.pdo.php) might be not installed in your PHP (make sure you edit the `php.ini` for both the CGI and CLI).
* Make sure you have the newest PHP (5.4+)
* Your configuration might be wrong: Does the PostgreSQL username and password actually work? Does the user have rights to acces the tables? etc.

Check the error logs at /var/log/nginx/error.log if you use Nginx. Apache would be roughly the same.

### Broken pipe and it stops tracking

Look at https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/issues/4

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
* Check if the PostgreSQL timezone is set correct:

```
psql -U postgres
SELECT NOW();
-- this will return a row with something like: 2015-02-26 01:13:05.155364+01
-- Check if the +01 corresponds to your correct timezone.
-- If this timezone is not correct fix this by using this SQL command:
SET TIME ZONE 'Europe/Amsterdam';
```

### Error Reporting

The application does not proper report errors.


### IPTables rules

Whatsapp uses an HTTPS connection which is going outbound:

`iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT`
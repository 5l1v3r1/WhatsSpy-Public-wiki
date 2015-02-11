# Troubleshooting

You can ask questions per mail.

### Tracker says "An error occured, please check your configuration."

Check if `api/?whatsspy=getStats` (if you have installed WhatsSpy Public in `/whatsspy/` this should be `/whatsspy/api/?whatsspy=getStats`) gives any error.

You might have one of the following problems:

* [PDO](http://php.net/manual/en/book.pdo.php) might be not installed in your PHP.
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

### Error Reporting

The application does not proper report errors.


### IPTables rules

Whatsapp uses an HTTPS connection which is going outbound:

`iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT`
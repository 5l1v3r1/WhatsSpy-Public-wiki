# Troubleshooting

You can ask questions per mail.

### Tracker says "An error occured, please check your configuration."

Check if `api/?whatsspy=getStats` (if you have installed WhatsSpy Public in `/whatsspy/` this should be `/whatsspy/api/?whatsspy=getStats`) gives any error.

You might have one of the following problems:

* [PDO](http://php.net/manual/en/book.pdo.php) might be not installed in your PHP.
* Make sure you have the newest PHP (5.4+)
* Your configuration might be wrong: Does the PostgreSQL username and password actually work? Does the user have rights to acces the tables? etc.

Check the error logs at /var/log/nginx/error.log if you use Nginx. Apache would be roughly the same.

### Error Reporting

The application does not proper report errors. This needs to be done in an update.


### IPTables rules

Whatsapp uses an HTTPS connection which is going outbound:

`iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT`
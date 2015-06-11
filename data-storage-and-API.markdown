As a developer you might want to use the data gathered by WhatsSpy Public for other (educational) purposes. This page will describe what API calls are available and where the data is stored.

# Where is the data stored?

The contacts, online times, groups etc are all stored in the Postgres database. You configured the database in `api/config.php` under `$dbAuth`. The database name is `whatsspy` and the schema name is `public` (default in postgres). If you would like to use this database for information you can connect to it using the same login information as stated in the `$dbAuth`.

The following tables exist:

* accounts
* groups
* lastseen_privacy_history 
* profilepic_privacy_history 
* profilepicture_history 
* status_history 
* statusmessage_history 
* statusmessage_privacy_history 
* tracker_history 
* whatsspy_config *(one row with instance information)*

If you want to inspect the database structure in more detail you can look at the [generation script](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/blob/master/api/whatsspy-db.sql) or the database itself with for example PgAdmin.

# API calls

All these calls need the URL to your WhatsSpy Public instance in front (eg `http://mydomain.com/whatsspy/`).

### Authenticate you against WhatsSpy public
```
api/?whatsspy=doLogin
```
*not required when no password is set*



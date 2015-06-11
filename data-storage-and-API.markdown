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

All these calls need the URL to your WhatsSpy Public instance in front (eg `http://mydomain.com/whatsspy/`). Replace the `[1]` in the urls with the values that you desire.

### Login
```
api/?whatsspy=doLogin&password=[1]
```
Login to WhatsSpy Public as a client. 

* `[1]` **required**: password used to login to the WebUI.

*Note: not required to call when password is set to false in `config.php`*

### Contact: Add new one
```
api/?whatsspy=addContact&number=[1]&countrycode=[2]&name=[3]
```
Add a new contact to be verified by the tracker and tracked.

* `[1]` **required**: local phonenumber (excluding the country code)
* `[2]` **required**: countrycode
* `[3]` **optional**: name for the contact (optional means you need to remove `&name=..` from the URL otherwise the name will be '')

### Contact: Set inactive
```
api/?whatsspy=setContactInactive&number=[1]
```
Set a contact inactive. This means the tracker will no longer track the person but all the information stays in the database.

* `[1]` **required**: countrycode + local phonenumber (stripped any prefix 0's in front of countrycode and local phonenumber).

### Contact: Delete contact
```
api/?whatsspy=deleteContact&number=[1]
```
Delete a contact and all the relevant information.

* `[1]` **required**: countrycode + local phonenumber (stripped any prefix 0's in front of countrycode and local phonenumber).

### Contact: Update contact information
```
api/?whatsspy=updateAccount&number=[1]&name=[2]&notify_status=[3]&notify_statusmsg=[4]&notify_profilepic=[5]&notify_privacy=[6]&notify_timeline=[7]&groups=[8]
```
Update any attribute of a contact.

* `[1]` **required**: countrycode + local phonenumber (stripped any prefix 0's in front of countrycode and local phonenumber).
* `[2]` **required**: (soon to be) name of contact.
* `[3]` **optional**: Either `1` (true) or `0` (false) to notify status change.
* `[4]` **optional**: Either `1` (true) or `0` (false) to notify statusmsg change.
* `[5]` **optional**: Either `1` (true) or `0` (false) to notify profile pic change.
* `[6]` **optional**: Either `1` (true) or `0` (false) to notify privacy change.
* `[7]` **optional**: Either `1` (true) or `0` (false) to notify if on timeline.
* `[8]` **optional**: The user group names, seperated by `,` (for example `group1,group2`).

### Config: Update config
```
api/?whatsspy=updateConfig&account_show_timeline_length=[1]&account_show_timeline_tracker=[2]
```
Update the WhatsSpy Public configuration.

* `[1]` **optional**: Change the default showed length of a contact's timeline (integer in days, like 1,2,3..).
* `[2]` **optional**: Change the default showed length of tracker sessions (integer in days, like 1,2,3..)

### Logout
```
api/?whatsspy=doLogout
```
Logout of WhatsSpy Public.



# Example usage API

* Call `doLogin` with the password `whatsspypublic`.
* Get the overview page information with `getStats`.
* Call `doLogout` to logout and stop communicating with WhatsSpy Public.
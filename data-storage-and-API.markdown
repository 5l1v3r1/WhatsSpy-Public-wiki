As a developer you might want to use the data gathered by your local WhatsSpy Public for other (educational) purposes. This page will describe what API calls are available and where the data is stored.

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

The WebUI that you use, uses the exact same calls. You can view these API calls by using Developers tools in your webbrowser.

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

### Contact: generate token
```
api/?whatsspy=generateToken&number=[1]
```
Generate an user token to share the users profile. There can be only one valid token at a time. Generating a new one will invalidate all previous tokens.

* `[1]` **required**: countrycode + local phonenumber (stripped any prefix 0's in front of countrycode and local phonenumber).

### Contact: reset token
```
api/?whatsspy=resetToken&number=[1]
```
Reset the token, this means that no-one will be able to see the shared profile.

* `[1]` **required**: countrycode + local phonenumber (stripped any prefix 0's in front of countrycode and local phonenumber).

### Contact: Get profilepicture
```
api/?whatsspy=getProfilePic&hash=[1]&token=[2]
```
Get the profile picture of an contact.

* `[1]` **required**: Users profilepicture hash (can be obtained to query for details of a contact or the `getStats`).
* `[2]` **optional**: Token authentication (used if cannot `doLogin` because you are an external visitor without the password).

### Contact: Get all user details
```
api/?whatsspy=getContactStats&number=[1]&token=[2]
```
Get all relevant information of a specific user.

* `[1]` **required**: countrycode + local phonenumber (stripped any prefix 0's in front of countrycode and local phonenumber).
* `[2]` **optional**: Token authentication (used if cannot `doLogin` because you are an external visitor without the password). NOTE: This output differs than the information you get from a normal `doLogin` (information like phonenumbers is missing).

### General: Get overview information
```
api/?whatsspy=getStats&token=[1]
```
Get the overview information (as used by the WebUI).

* `[1]` **optional**: Token authentication (used if cannot `doLogin` because you are an external visitor without the password). NOTE: This output differs than the information you get from a normal `doLogin` (information like phonenumbers and other contacts is missing).

### Timeline: timeline information
```
api/?whatsspy=getTimelineStats&showTrackerInfo=[1]&activities_since=[2]&sid_status=[3]
```
Get timeline information.

* `[1]` **optional**: Output tracker events in the activities feed.
* `[2]` **optional**: Output events after this epoch timestamp (integer, epoch in seconds).
* `[3]` **optional**: Get any online status after or equal to this Sid (status id) (integer).

### Statistics: Global stats
```
api/?whatsspy=getGlobalStats&component=global_stats&group=[1]
```
Get general statistics about the tracker and userbase.

* `[1]` **optional**: GroupID (remove `&group=..` from the query if not used).

### Statistics: Top users
```
api/?whatsspy=getGlobalStats&component=top_usage_users&group=[1]&users=[2]
```
Give the top x users back with relevant online/status times.

* `[1]` **optional**: GroupID (remove `&group=..` from the query if not used).
* `[2]` **optional**: Amount of top-x users (like 10,20 etc: integer).

### Statistics: Global stats

```
api/?whatsspy=getGlobalStats&component=user_status_analytics_user&group=[1]
```
Get information back about the online time/status amounts BASED ON USER.

* `[1]` **optional**: GroupID (remove `&group=..` from the query if not used).

### Statistics: Global stats
```
api/?whatsspy=getGlobalStats&component=user_status_analytics_time&group=[1]
```
Get information back about the online time/status amounts BASED ON TIME (day of week/hour).

* `[1]` **optional**: GroupID (remove `&group=..` from the query if not used).

### Group: add new group
```
api/?whatsspy=addGroup&name=[1]
```
Generate a new group.

* `[1]` **required**: Name of the group.

### Group: Delete group
```
api/?whatsspy=deleteGroup&name=[1]
```
Delete a group.

* `[1]` **required**: GroupId.

### Group: generate token
```
api/?whatsspy=generateToken&group=[1]
```
Generate an user token to share the users profile.

* `[1]` **required**: GroupID.

### Group: reset token
```
api/?whatsspy=resetToken&group=[1]
```
Reset the token, this means that no-one will be able to see the shared profile.

* `[1]` **required**: GroupID.

### Config: Update config
```
api/?whatsspy=updateConfig&account_show_timeline_length=[1]&account_show_timeline_tracker=[2]
```
Update the WhatsSpy Public configuration.

* `[1]` **optional**: Change the default showed length of a contact's timeline (integer in days, like 1,2,3..).
* `[2]` **optional**: Change the default showed length of tracker sessions (integer in days, like 1,2,3..)

### Other: Version/about information
```
api/?whatsspy=getAbout
```
Get information about the version, project and other information used in the about page (like notification agents).

### Logout
```
api/?whatsspy=doLogout
```
Logout of WhatsSpy Public.


Undocumented API calls:

* doShutdown (experimental)
* doUpdate (experimental)
* doStartup (experimental)

Check [the source](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/blob/master/api/index.php) for more information.

# Example usage API

* Call `doLogin` with the password `whatsspypublic`.
* Get the overview page information with `getStats`.
* Call `doLogout` to logout and stop communicating with WhatsSpy Public.
# Getting started
*This guide requires average knowledge of webservers, phones and PHP.*

## Requirements

**Shortlist requirements:**

* Secondary Whatsapp account (phonenumber that doesn't use Whatsapp)
* Rooted Android phone OR Jailbroken iPhone OR PHP knowledge
* Server/RPi that runs 24/7
* Nginx or Apache with PHP (you can't host on simple webhoster, you need bash)
* Postgresql


### Notice
WhatsSpy Public requires an **secondary Whatsapp account**. Once the tracker is started, you will not be able to recieve any messages over Whatsapp for this phonenumber. You can either try to register an non-Whatsapp used phonenumber ([with for example this script](https://github.com/mgp25/WhatsAPI-Official/blob/master/examples/exampleRegister.php)) or just by an 5 euro SIM Card and use this phonenumber for the tracker.

For the tracker to work you need an secret which is retrieved from either your Phone or the register script mentioned above. In case of phone registration you need an **jailbroken iPhone** or **rooted Android** device in order to retrieve the secret. 

* Jailbroken iPhone users: You can retrieve using [this script](https://www.mgp25.com/utilidadiPhone/).
* Rooted Android phones can use the [following APK](https://github.com/venomous0x/WhatsAPI/issues/983) to retrieve the secret. 

Write this secret down, which is required later.

## Installation

1. Download the [repository](https://gitlab.maikel.pro/maikeldus/WhatsSpy-Public/tree/master) and unpack these files on your server at for example `/whatsspy/` in your web directory (for nginx in debian this is `/var/www/`).

2. Install SQL

2. rename `config.example.php` to `config.php` located at `api/` and fill in the following details: 

* Postgresql host/port/dbname/user and password correctly in `$dbAuth`.
* Your secondary Whatsapp account's phone number in 'number' and the retrieved secret in 'secret' (look at requirements if not retrieved yet) in `$whatsappAuth`.
* Set the absolute path correct in `$whatsspyProfilePath`. If you've installed WhatsSpy Public in for example `/var/www/whatsspy` the correct directory would be `/var/www/whatsspy/images/profilepicture/` (including `/`)
* You can set an Optional NotifyMyAndroid key for notifications about the tracker (startup,shutdown,errors etc) in `$whatsspyNMAKey`. 

3. Setup Nginx

4. Check folder rights

## Importing users


## Starting the tracker

## Other information

### IPTables rules

Whatsapp uses an HTTPS connection which is going outbound:

`iptables -A OUTPUT -o eth0 -p tcp --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT`

`iptables -A INPUT -i eth0 -p tcp --sport 443 -m state --state ESTABLISHED -j ACCEPT`

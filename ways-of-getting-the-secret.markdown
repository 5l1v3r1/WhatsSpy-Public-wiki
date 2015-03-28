# How to get the `secret` used by WhatsApp?

WhatsSpy Public requires a **secondary WhatsApp account**. Once the tracker is started, you will not be able to receive any messages over WhatsApp for this phone number. You need to register at WhatsApp to retrieve a `secret` which you will need later when setting up WhatsSpy Public.

### 1.1) Getting a phone number

You need a phone(number) over which you can receive SMS or voice calls for the WhatsApp activation. You can try to register your land line, or just buy a 5 euro SIM card and register that phone number at WhatsApp. Do not use some shady SMS service, this won't work.

### 1.2) Activation at WhatsApp with your secondary phone number

You have three methods:

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

* First make sure you have downloaded WhatsSpy Public on your Linux machine (Assuming you installed it in `/var/www/whatsspy/`).
* Execute `php /var/www/whatsspy/api/whatsapp/registerTool.php`.
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
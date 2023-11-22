---
title: "So I bought a Yubikey, now what?"
date: "2017-08-09 16:45:03"
type: "post"
---


# So I bought a Yubikey, now what?
[Yubikey](https://www.yubico.com/products/yubikey-hardware/)s  are small usb security keys that can be used in various ways to improve the day to day workflow from the security point of view. I recently got one and started exploring and everything you can do with Yubikey 4 on a Linux machine.

First and most simple usage of Yubikeys are for 2nd factor authentication of several different websites like Github, Google etc. However, the native support for Yubikeys (also called as U2F standard keys) exists only in Google chrome browser as of now.

Because I recently moved to Firefox 54 (see this if you want to know [what is special about v54 release of FF](https://medium.com/mozilla-tech/the-search-for-the-goldilocks-browser-and-why-firefox-may-be-just-right-for-you-1f520506aa35)) and have been pretty much happy with it. I can keep over 30-40 tabs open without every worrying about my computer getting slow. Sure if I open a lot of tasks, Firefox itself gets a bit slow, but I guess this compromise works well where I can keep enough tabs open when I want.

Moving on, I was pretty bummed to find out that Firefox doesn’t have native support for U2F keys, however, someone pointed me to this [Add-On](https://medium.com/mozilla-tech/the-search-for-the-goldilocks-browser-and-why-firefox-may-be-just-right-for-you-1f520506aa35) using which you can use Yubikeys and other U2F keys in Firefox. I immediately installed everything and it worked like a charm. It looks like a popular add-on with 23k users and 4/5 ratings.

I added my Yubikey on Github and all it took was a single button press to register the device.  I haven’t exactly read anything about U2F protocol, but it looks like it emits some sort of OTP like an input device on the highlighted text input area which probably the websites grab and use for authentication. I will definitely check out U2F protocol and maybe write another post on it.

Another notable use of Yubikeys are to store gpg keys. It is advised to store your master key offline and safe somewhere and only add subkeys to Yubikeys so that you can revoke the keys in case you loose the Yubikey. I find that to be very good advice, but I am still wondering the exact use cases where I can store my gpg key on the Yubikey and not worry too much about loosing it. It does protect the keys with a Pin and probably won’t be that bad if you can revoke the keys, but people don’t update revocations all so frequently, which makes me a bit skeptical about storing my gpg keys on Yubikey.

Here is some piece of documentation from the Yubico-personalization-gui for programming Yubikeys that I didn’t know about:


    YubiKey 2.x and later comes with two configuration slots. Each configuration slot works independent of each other and can be programmed in any of the following modes:
        1. Yubico OTP
        2. OATH-HOTP
        3. Static Password
        4. Challenge-Response (YubiKey 2.2 and onwards)
    The first slot is used to generate the output when the YubiKey button is touched between 0.3 to 1.5 seconds and released and the second slot is used if the button is touched between 2 to 5 seconds.
    When the YubiKey 2.X is shipped, it's first configuration slot is factory programmed for Yubico OTP mode (which works with online Yubico OTP validation server) and the second configuration slot is left blank.

Ok, so this makes things more clear to me:


- There are two slots which you can use for one of the following four functions each:
  - Yubico OTP (works when you use the OTP mode in Websites like Github/Gmail)
  - OATH-HOTP (works when you want something like Google Authenticator but counter based instead of time based OTP)
  - Static Password (a fixed static password that can be either programmed or auto generated on the key, maximum length of 64 chars)
  - Challenge Response (can be used for local authentication using HMAC-SHA1 in offline mode, this can be used with libpam-yubico to [login into computer](https://developers.yubico.com/yubico-pam/Authentication_Using_Challenge-Response.html) or any other PAM related functions)

I hope this is a good basic introduction and will help others to get started after buying a new Yubikey!



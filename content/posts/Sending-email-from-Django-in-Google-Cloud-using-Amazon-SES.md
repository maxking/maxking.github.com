---
title: "Sending email from Django in Google Cloud using Amazon SES"
date: "2017-07-12 15:30:35"
---


# Sending email from Django in Google Cloud using Amazon SES
First question to this from many people would be Why?

The simple answer is that I have free credits in GCloud and SES is simple and cheap to send emails. I don’t want a full GApps account to to send emails.

After you have signed up for Amazon SES and configured your domains and sending address, you need to configure your GCloud server to send emails via SES. By default, you cannot use any standard SMTP port like 25, 587 or 465 from GCloud. 

Blocking outgoing connections on port 25 is not something uncommon today and most cloud providers do that to thwart spam coming from their server, but not many of them block 465 and 587 isn’t something I have seen often. 

But, SES allows you to use port 2587 to connect to using the SMTPS protocol (secure smtp over TLS). The following configuration works for me when using Django:


    EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
    EMAIL_HOST = 'email-smtp.us-west-2.amazonaws.com'
    EMAIL_PORT = 2587
    EMAIL_USE_TLS = True
    EMAIL_HOST_USER = <my-ses-smtp-username>
    EMAIL_HOST_PASSWORD = <my-ses-smtp-password>

If you were confused how to receive emails (like me), you don’t need to worry as incoming connections on 25, 587 and 465 aren’t blocked. So, you can have postfix or whatever client you want listening to emails on any of these ports for email.



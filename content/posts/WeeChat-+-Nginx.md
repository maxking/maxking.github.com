---
title: "WeeChat + Nginx"
date: "2017-07-19 08:43:12"
---


# WeeChat + Nginx
I talked before about different [solutions for persistent IRC](https://asynchronous.in/2017/03/15/Persistant-IRC-Setup/) and how I settled on using WeeChat. This post is about how I use the *relay* feature in WeeChat to interact with it remotely.

WeeChat has a remote user protocol called as [relay protocol](https://weechat.org/files/doc/devel/weechat_relay_protocol.en.html)  that people can use to connect WeeChat clients to remote running instances of it. It is helpful when you want to run WeeChat on a remote server and connect using clients from various different places.

It is very simple to add a new relay to your already running WeeChat:


    /relay add weechat 8080
    /set relay.network.password YOURPASSWORD

This will make WeeChat listen for clients on TCP port 8080 for incoming connections from clients. In a closed or local environment this might work, but it is must that you use SSL when using it over internet. 

WeeChat allows you to create SSL relays and manages SSL certs too! [See here](https://pthree.org/2016/05/20/weechat-relay-with-lets-encrypt-certificates/) for a great introduction to running an SSL relay.

While there hasn’t been any problem with running weechat over SSL, if you are using the same server for serving some other stuff, it makes managing SSL certificates in two places a little too cumbersome. Also, you need to reload each application after you renew the Lets Encrypt certificate (which is what I use!) in every 90 days.

So, instead I decided to use Nginx to reverse proxy my WeeChat. Here is the configuration that I use to make it work:

    limit_req_zone $binary_remote_addr zone=weechat:10m rate=5r/m;
    
    map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
    }
    
    server {
    
            listen 443 ssl;
    
            server_name _;
            ssl_certificate /etc/letsencrypt/live/<DOMAIN_NAME>/fullchain.pem;
            ssl_certificate_key /etc/letsencrypt/live/<DOMAIN_NAME>/privkey.pem;
    
            location ^~ /weechat {
                    proxy_pass http://0.0.0.0:8080;
                    proxy_http_version 1.1;
                    proxy_read_timeout 4h;
                    proxy_set_header Connection "upgrade";
                    proxy_set_header Upgrade $http_upgrade;
                    limit_req zone=weechat burst=1 nodelay;
            }
    
    }

Make sure that you use the correct `bind_address` and port in the `proxy_pass` directive above. If you are using a non-standard port like 2000 or 3000 or sometimes even 8080, it is possible that SELinux would deny Nginx access to that port. To allow Nginx access to the ports run the command below:


    $ sudo setsebool httpd_can_network_connect 1 -P

That is all, now to connect any WeeChat client, point it to your Domain and port 443 using the password that you set for your relay!



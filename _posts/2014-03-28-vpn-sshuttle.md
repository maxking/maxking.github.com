---
name: vpn-sshuttle
layout: post
title: Sshuttle VPN to bypass proxy
time: 2014-03-28 16:41:00.001000 +05:30
tags: vpn sshuttle proxy
---

Proxy servers in my college has made my life like a living hell. Blocking IRC and
SSH is the worst thing that you can do to a developer I suppose. For past few
years I have been trying to totally eliminate the problems associated with proxy,
but was never successful. My previous attempt was to create a ssh tunnel using
a amazon-ec2 instance and create a socks proxy to be used by applications. The
configuration was like below:

    Host amazon
     User ubuntu
     HostName 54.214.39.19
     Port 443
     ProxyCommand corkscrew <proxy-address> <port> %h %p
     IdentityFile /home/maxking/.ssh/my.pem
     Localforward 6667 irc.freenode.net:6667
     LocalForward 9930 imap.gmail.com:993

The above configuration coupled with `autossh` creates a socks proxy tunneling
all the data trough the ssh tunnel. A simple command that you can use is

    autossh -f -N -g -D 9999 amazon

This creates a socks proxy on localhost:9999 and tunnels all the request
trough the ssh tunnel created. `Autossh` helps to reconnect the ssh session
as soon as it breaks( which is very often behind my college proxy ).

Recently I came across another solution for this which made my life more easier,
[sshuttle][1]. It is a "Transparent proxy server that works as a poor man's
VPN. Forwards over ssh.  Doesn't require admin. Works with Linux and
MacOS. Supports DNS tunneling."

So with the above setup I just have to run

    sudo sshuttle -r amazon 0/0 --dns -x 144.0.0.0 -x 10.0.0.0 -D

and all my requests without a proxy are forwarded over the tunnel and everything
seems to **just work**.


[1]: [https://github.com/apenwarr/sshuttle]
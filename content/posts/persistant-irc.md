+++
date = "2017-03-15T12:57:20-07:00"
title = "Persistant IRC Setup"
+++

[IRC](https://en.wikipedia.org/wiki/Internet_Relay_Chat) stands for Internet
Relay Chat, a text based chat network that is commonly used by programmers and
open source community to collaborate over projects. You can read more about it
in the above linked Wikipedia article.

IRC is very lightweight and really easy to use, although it has a few
shortcomings when you compare it to other messaging apps/technologies that exist
today, biggest of which is persistence. It is a synchronous chat. What that
means is that you can send/receive messages only when you are actively connected
to the IRC network.

There are several solutions to fix this problem that I have explored in the
past. You can setup an IRC bouncer, that keeps running as a proxy on a computer
that is always on, and relays the logs/messages to you when you connect it.
[ZNC](http://wiki.znc.in/ZNC) seems to be the most popular choice but for some
reason I was never *easily* able to setup ZNC for my personal use. I don’t know
many people in my personal circle that it use it either, but it does work if you
want to give it a try.

Another solution that I have tried in the past is a web based service,
[IRCCloud](https://www.irccloud.com/). I used IRCCloud for quite some time back
in my college. It was initially in beta, and so free to use. It allowed for
persistent connections on 5 networks and has a neat interface. It also has a
very neat nice android App so that you can chat over your smartphone too! The
biggest benefit that IRCCloud offers is that it doesn’t need me to run/manage
anything on my own. Also, it was easier for me to use as my university had a
stupid proxy to use internet which blocked all outgoing ports except HTTP and
HTTPS.

 Then later, IRCCloud came out of beta and started with a paid plan, with a
 basic free tier which would keep your connection persistent for only some time
 (7 days?). The paid model was $5/mo, which is what a small [Digital
 Ocean](https://www.digitalocean.com/) Droplet costs. Now, even
 [Linode](http://welcome.linode.com/features/?keyword=linode&utm_source=bing&utm_medium=cpc&utm_campaign=Linode%20-%20Brand%20-%20Search&utm_term=linode&utm_content=Linode)
 and [AWS](https://aws.amazon.com/) have similar budget friendly servers. It
 might make sense to someone who doesn’t want to manage their own service to use
 the paid plan, but I decided to instead get a small VPS and try something
 new. Also, [Github’s Education Pack](http://education.github.com) started
 offering $100 in Digital Ocean credits to first time users, which made the
 transition free for me.

 Right now, there are several other options if you want to get a server for free for a small time (or longer).  Like:

- Amazon Web Service (AWS) Free tier offers a micro instance free for 1 year for new users. I had some problems using it initially because I didn’t own a credit card at the time in India. 
- Digital Ocean offers $100 in free credits through Github education pack, which can run it’s smallest instace ($5/mo) for 20 months.
- Google Cloud Platform (GCP) provides $300 in free credits to use for 12 months which I *think* can also power a micro/small instance for an year. Recently at Google’s Next ’17 conference, they announced that GCP will have a [free tier](https://cloud.google.com/free/) that would remain free, that will remain free *forever*. 

So, we now have a plenty of options to have a small VPS online to run your
persistent IRC. Let’s look at some of the software that I have explored to run
on my server:

[Quassel](http://www.quassel-irc.org/) was recommended to my by one of my
friends. It has a server client architecture where the client can be used to
connect to an always running instance of server. Having used it for almost 2
years, I can vouch that it is a *very* stable piece of software. Setting it up
is almost a breeze. Debian/Ubuntu also have packages for them in their
repositories that you can easily install. On the first run, you can setup your
configuration and then connect from clients from different places. It has one
for Windows, Linux, Android (probably even Mac, never checked).

I had no good reason to switch from Quassel to anything else, except for the
fact that I got bored of its client. There are no other alternatives that you
can use to connect to. Also, it doesn’t have any support for
plugins/scripts. So, I came to know about this new alternative that everyone
used at my new University (OSU), Weechat.

[Weechat](https://weechat.org/) is an open source project that works similar to
Quassel but has much more functionality to offer. It can act as an IRC proxy and
so you can use your favorite client with it. Also, it has a custom protocol and
clients that use that. I use an HTML5 based web frontend called [Glowing
Bear](http://www.glowing-bear.org/), which is also an android app. There is a Qt
based client, an emacs client too! It supports scripting and has a [very good
list of scripts](https://weechat.org/scripts/) already written for it. I still
haven’t used this feature too much, but it is really awesome and easy to
use. And did I mention that you can use Python to write scripts for it ?!
Setting up WeeChat is a teeny bit more difficult that quassel if you want to use
SSL, which I wanted to. Glowing Bear doesn’t allow self signed certificates, so
I had to get one from [Lets Encrypt](https://letsencrypt.org/), which btw is
very easy to get.  I will write another blog post with details about how to
setup WeeChat with SSL enabled on a VPS.



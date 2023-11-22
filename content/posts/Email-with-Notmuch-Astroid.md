+++
date = "Fri Apr 21 10:51:03 IST 2017"
title = "Email with notmuch and astroid"
type = "post"
+++


Email is an integral part of our everyday life. I have spent a large amount of time trying to experiment with different email clients available on Linux. I have tried [Thunderbird](https://www.mozilla.org/en-US/thunderbird/), [Evolution](https://wiki.gnome.org/Apps/Evolution/), [notmuch](https://notmuchmail.org) with emacs, [mutt](http://www.mutt.org/), [mu4e](https://www.emacswiki.org/emacs/mu4e).

Since I am a Linux user, the list above includes only the clients compatible with Linux, particularly Debian/Ubuntu ecosystem. You can classify most of the email clients in two broad workflow categories


- Folder based workflow - This kind of workflow means you have separate folders for all the kinds of emails you receive. It could be separate email accounts each with their own sub-folders. You keep on top of your emails and mostly look frequently at the emails on the top in each folder. Thunderbird, Evolution, Gmail Web is an example of such kind.
- Search based - This kind of workflow is completely based on what you want to see. It usually doesn’t have a concept of folders at all. You work with tags and an indexer. Email indexers like [mu](http://www.djcbsoftware.nl/code/mu/) and [notmuch](https://notmuchmail.org/) are what powers this. All emails are indexed and tagged so that you can find the email you are looking for *very* easily. If you are a terminal person or the one who loves keyboard more than mouse/trackpad (like me!), then you will fall in love with this workflow (like me!).

Thunderbird is like my fall-back email client. Usually, I always have a working thunderbird setup on my laptop, even when I am using something else. This is because I like to play around with different setups and often screw up loosing everything in my [maildir](https://en.wikipedia.org/wiki/Maildir). Also, sometimes, I have small holes in my knowledge of the workflow to achieve specific things (mostly, keybindings ;-). A very important and absolutely necessary plugin is [Enigmail](https://www.enigmail.net/index.php/en/). It powers the integration of gpg in thunderbird, which I feel should be baked in all modern email clients.

Notmuch and mu are, as I have mentioned before, email indexers. Both of them are *really* good and r*eally* fast. If you have ever searched anything in thunderbird/evolution/outlook, you will be amazed at how fast they are.

My current setup involves four major componenets. Offlineimap to fetch email, notmuch to index email, Astroid to view/search/read/write emails and msmtp to send emails.

First, [offlineimap](http://www.offlineimap.org/) fetches all my email from different email servers using IMAP protocol. It is written in python. The maintainer for this project has moved on to writing a more robust and new imap framework called [imapfw](http://imapfw.offlineimap.org/). But offlineimap still remains the best and easiest way to sync emails between two different maildirs. Here is a copy of my `.offlineimaprc`  , the configuration for offlineimap using Gmail.


    [general]
    accounts = Gmail
    pythonfile = ~/.offlineimap.py

    [Account Gmail]
    localrepository = GmailLocal
    remoterepository = GmailRemote
    maxage = 100

    [Repository GmailLocal]
    type = Maildir
    localfolders = ~/.maildir/Gmail/

    [Repository GmailRemote]
    type = Gmail
    sslcacertfile = /etc/ssl/certs/ca-certificates.crt
    remoteuser = username@gmail.com
    realdelete = no
    createfolders = false
    remotepasseval = get_password_emacs("imap.gmail.com",
                                         "username@gmail.com",
                                         "993")
    folderfilter = lambda foldername: foldername  not in ['[Gmail]/All Mail',
                                '[Gmail]/Trash', '[Gmail]/Bin',
                                '[Gmail]/Sent Mail', '[Gmail]/Starred',
                                '[Gmail]/Spam','SPAM' ]
    holdconnectionopen = true
    keepalive = 60


The above settings required a python script to fetch the password   `~/.offlineimap.py` :

    #!/usr/bin/python
    import re, os

    def get_password_emacs(machine, login, port):
        s = "machine %s login %s port %s password ([^ ]*)\n" % (machine, login, port)
        p = re.compile(s)
        authinfo = os.popen("gpg -q --no-tty -d ~/.authinfo.gpg").read()
        return p.search(authinfo).group(1)

The above script fetches the password for your account from the encrypted file `.authinfo.gpg` which when decrypted looks like this:


    machine imap.gmail.com login username@gmail.com port 993 password mypassword
    machine smtp.gmail.com login username@gmail.com port 587 password mypassword

To create the encrypted file, you put the above two lines in a file called `.authinfo` and then encrypt it using `gpg` using:


    $ gpg --output .authinfo.gpg --encrypt --recipient username@gmail.com .authinfo

You will also want to have [gpg-agent](https://www.gnupg.org/documentation/manuals/gnupg/Invoking-GPG_002dAGENT.html) setup so that gpg doesn’t prompt you for your passphrase every time it wants to check your email.

So, after this, now you have a working setup for fetching email. To test that you can fetch your emails try:


    $ offlineimap --dry-run

Next, we move on to notmuch. You can install it on Debian/Ubuntu systems using:


    $ sudo apt-get install notmuch

Here is a simple configuration for notmuch, stored at `~/.notmuch-config`

    # .notmuch-config - Configuration file for the notmuch mail system
    #
    # For more information about notmuch, see http://notmuchmail.org

    [database]
    path=~/.maildir

    [user]
    name=Abhilash Raj
    primary_email=username@gmail.com
    # If you have mutiple accuonts, uncomment the line below.
    # other_email=other_email@gmail.com

    [new]
    tags=unread;new;
    ignore=

    [search]
    exclude_tags=deleted;spam;

    [maildir]
    synchronize_flags=true

    [crypto]
    gpg_path=gpg2

After you have this, try `notmuch new` command. It will index all your emails and tag them with rules that you have defined above. If everything went like expected, you should see an output like below if you run `notmuch` without any arguments.


    $ notmuch
    Notmuch is configured and appears to have a database. Excellent!

    At this point you can start exploring the functionality of notmuch by
    using commands such as:

            notmuch search tag:inbox

            notmuch search to:"Your Name"

            notmuch search from:"username@gmail.com"

            notmuch search subject:"my favorite things"

    See "notmuch help search" for more details.

    You can also use "notmuch show" with any of the thread IDs resulting
    from a search. Finally, you may want to explore using a more sophisticated
    interface to notmuch such as the emacs interface implemented in notmuch.el
    or any other interface described at https://notmuchmail.org

    And don't forget to run "notmuch new" whenever new mail arrives.

    Have fun, and may your inbox never have much mail.

Notmuch is used by [several different email clients](https://notmuchmail.org/frontends/). You can test and try which ones you like the best. There is an emacs client so that you don’t ever have to leave emacs for your email. I tried it for a little while but just didn’t feel the best to me.

Last week I stumbled upon another frontend to notmuch called [Astroid](https://astroidmail.github.io/). It is a sleek interface written in C++ and renders emails using webkit. You write emails in your favorite text editor that can be spawn off or embeded inside of Astroid. I have embeded emacs in Astroid which is super freaking awesome!

I compiled and installed Astroid from the [source](https://github.com/astroidmail/astroid) that you can find on Github. The installation instructions are provided in the Readme. A default configuration is generated at `$XDG_CONFIG_HOME/astroid/config`  (most commonly at ~/.config/astroid/config/)the first time you run astroid. Configuring astroid is super easy. You can view all the default keybindings by `?` key in the default view. It also has a very nice [wiki](https://github.com/astroidmail/astroid/wiki) that can help you setup configure Astroid to your taste.

Astroid uses a script called `poll.sh` at `~/.config/astroid/poll.sh` to fetch your email if you want. You can write your own commands in the script which can do several other things than just fetching email, like indexing new emails, notifying you of new emails etc. Here is what mys script looks like:

    #! /bin/bash

    if ! ping -w 1 -W 1 -c 1 mail.google.com; then
        echo "there is no internet connection"
        exit
    fi

    echo "Checking new mail!"
    /usr/bin/offlineimap

    NEW_MAIL=false
    if ! (notmuch new | grep "No new mail."); then
        NEW_MAIL=true
    fi

    # Tag email etc. here
    notmuch tag --batch <<EOF
        +urgent tag:new and subject:URGENT
        +inbox tag:new and folder:Gmail/INBOX
        +work tag:new and from:github or from:gitlab
        +promo tag:new and folder:Gmail/Promotions
        +social tag:new and folder:Gmail/Social
        -new tag:new
    EOF
    notmuch tag -inbox tag:promo or tag:social

    if [ $NEW_MAIL = true ]; then
        notify-send "New mail."
    fi

The most important part of the script that you should change is the part starting with `notmuch tag --batch`  . This is how notmuch tags all your emails based on some pre-defined rules. All the new emails are tagged with `new` and `unread` by default and then the rest of the processing can happen on the emails that have `new` tag.

[Notmuch’s documentation](https://notmuchmail.org/initial_tagging/) mentions different ways in which you can tag your emails as they arrive. There are several different ways and you can choose whatever works the best for you. I encourage you to dive more into tagging and different robust ways to tag emails as this is going to be the central piece of your workflow. If you are smart, you will be able to automate a large part of filtering process and increase your productivity to a very high level. It is very easy to write scripts that can classify your emails to into certain categories based on the headers or content of the email. If you want to get all your Gmail tags in notmuch, you can do this:


    $ notmuch tag +tag-name folder:Gmail/tag-name

Since gmail’s tags are essentially treated as folders by offlineimap (and hence notmuch), you can very easily tag them using `folder:` search query in notmuch. There are other more [advanced ways to search](https://notmuchmail.org/manpages/notmuch-search-terms-7/) in notmuch.

The final thing that you want to setup is how emails are sent. By default, Astroid doesn’t have any inbuilt mechanism to relay emails. It [recommends](https://github.com/astroidmail/astroid/wiki/Astroid-in-your-general-mail-setup) using [msmtp](http://msmtp.sourceforge.net/) for sending emails, although you don’t *have* to. Any smtp software that has a sendmail like interface should work. Here is my configuration for msmtp stored in `~/.msmtprc` :


    defaults
    port 587
    tls on
    auth on
    logfile ~/.msmtp.log
    tls_trust_file /etc/ssl/certs/ca-certificates.crt

    account gmail
    host smtp.gmail.com
    from username@gmail.com
    user username@gmail.com
    tls_starttls on
    tls on
    auth on
    port 587
    passwordeval gpg2 --no-tty -q -d ~/.msmtp-gmail.gpg

Your gmail password is supposed to be stored encrypted in `~/.msmtp-gmail.gpg` file. There are other ways that you can provide your password to msmtp, [have a look at the example](http://msmtp.sourceforge.net/doc/msmtp.html#Examples) configuration in the documentation.

Hope you have fun with emails!

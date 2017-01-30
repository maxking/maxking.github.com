---
layout: post
title: Notes
date: 2015-02-14 12:00:00
featured_image: /images/notes.jpg
---

These are my notes that I make randomly. There can be code, links, documents or
anything else that I feel is important. It is just to keep a record of what I
have been up to and remind me of stuff that I forget quite often.

## Sed

Sed is a command line utility using which you can find and
replace text inside files without even opening them. I find
it very useful in many circumstances. This is a basic usage
of the command:

    sed -i 's/day/night/g' file.txt

The above command would replace all the occurrence  of `day`
in file.txt with `night`. The limitation of sed is that it
takes one file input and can't recursively find-and-replace
in a directory. The command below tries to fix that:

    grep -rl matchstring somedir/ | xargs sed -i 's/string1/string2/g'

Thanks to the fabulous pipe!

## Mysql Collation Error

When you get an error with Mysql and Python like

	 (1267, "Illegal mix of collations (latin1_swedish_ci,IMPLICIT) and (utf8_general_c		i,COERCIBLE) for operation '='")



It is because of the different Collations for client and server. AFAIK right
now, collations are just charsets to be used by the database. You can change
this by using this sql query below:

	ALTER DATABASE mydb DEFAULT COLLATE utf8_unicode_ci;

For more details refer [here][1].


## Magit choose remote branch to push

Press "P C-u C-u P" to make Magit ask you what remote and remote branch to use before pushing.

(With a single prefix argument ("P C-u P") Magit asks what remote to use.)


## How to generate Dockerfile from Image

```
$ docker pull centurylink/dockerfile-from-image
$ alias dimages="docker run -v /var/run/docker.sock:/var/run/docker.sock\
    centurylink/dockerfile-from-image"
$ dimages selenium/node-firefox-debug
```

## Convert Bzr to Git

To convert bzr repos to git you can follow these commands. It converts  a
project named 'mailman' from bazaar to git.

```
$ bzr branch lp:mailman gitify
$ cd gitify
$ git init .
$ bzr fast-export --export-marks=../marks.bzr | git fast-import --export-marks=../marks.git
$ git add all-the-relevant-stuff
$ git commit
```

## Add a swap file

[This][2] is an interesting work around for when you don't have enough memory to
do some tasks. Basically some commands to add a swapfile in a VM. I use that for
the digitalocean instance I have.

## Add a user to a group

TO add an existing user in Linux to an existing group:

```bash
sudo usermod -a -G groupName userName
```


[1]: http://stackoverflow.com/questions/3029321/troubleshooting-illegal-mix-of-collations-error-in-mysql
[2]: http://stackoverflow.com/questions/24455238/lxml-installation-error-ubuntu-14-04-internal-compiler-error

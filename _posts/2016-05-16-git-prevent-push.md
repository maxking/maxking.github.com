---
title: Prevent Git Push
layout: post
name: prevent-git-push
date: 2016-05-16 13:23:08 PST
tags: git 
---

It happens with me sometimes that I push to repositories that I must not push
to. Being a [GNU Mailman][1] core-developer, I have push access to all the
repositories. But, we always add changes to the master repo using
merge-requests, even Barry does the same. But with my poor [Git] skills, I end
up sometims pushing to the wrong repository which might lead to bad code inside
of the main repository. So a small nifty trick to avoid that is to make the
respository read-only. This is how I did it:

```bash
$ git config remote.upstream.pushurl "you really didn't want to do that"
```

[1]: http://list.org

---
date: 2016-05-16T13:23:08Z
name: prevent-git-push
title: Prevent Git Push
type: post
---

It happens with me sometimes that I push to repositories that I must not push
to. Being a [GNU Mailman][1] core-developer, I have push access to all the
repositories. But, we always add changes to the master repo using
merge-requests, even Barry does the same. But with my poor [Git] skills, I end
up sometimes pushing to the wrong repository which might lead to bad code inside
of the main repository. So a small nifty trick to avoid that is to make the
repository read-only. This is how I did it:

```bash
$ git config remote.upstream.pushurl "you really didn't want to do that"
```

[1]: http://list.org

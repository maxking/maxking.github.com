+++
date = "2017-02-08T13:02:38-08:00"
title = "Move to Hugo"
+++

I have been planning to rejuvenate this blog with a new design and hopefully
some good posts! Before this, I used to publish this blog using [Jekyll][1]
which is a great tool if you want to serve static website using [Github
Pages][2].

Jekyll is written in Ruby, which is one of the reasons I was planning to move
away from it. I don't have anything against the language, but if you want to
install Jekyll locally to test out your pages, you have to install the whole
Ruby toolchain on your machine. This was extremely painful. In my experience,
dealing with version conflicts in Ruby is so much more difficult than I want to
deal with for my blog.

So, I moved my blog to a new static site generator called [Hugo][3], which is
written in Golang. It is extremely fast and pretty easy to install and use on my
Ubuntu 16.10 machine. There is a [theme showcase][4] for Hugo that you can use
to get started. Also, Hugo has a nifty little trick to import your blog posts
from Jekyll, see [`hugo import jekyll`][5] command.

The only problem with using Hugo with Github Pages is that you have to manually
generate the webpages and push to your `username.github.io` repository. So, I
automated this process with a `Makefile`:

```
HUGO=/usr/bin/hugo
PUBLISH_DIR=public/
GIT=git --git-dir=$(PUBLISH_DIR).git --work-tree=$(PUBLISH_DIR)

all: run

run:
	echo "Building your website now..."
	$(HUGO)

serve:
	# Run a hugo server that restarts whenever changes are made to files.
	$(HUGO) serve

publish:
	# First, run hugo to compile all the files.
	$(HUGO)
	# Second, add and commit all the changes in the static directory.
	echo "Committing changes..."
	$(GIT) add . && \
	$(GIT) commit -am '' --allow-empty-message --quiet
	# Push the changes to the remote server to publish them.
	$(GIT) push origin master
	echo "Done publishing your website."
```

This way, you will have to create two repos, in one you keep your code and posts
and the 2nd one `username.github.io` keeps your statically generated files.


[1]: https://jekyllrb.com/
[2]: https://pages.github.com/
[3]: https://gohugo.io/
[4]: http://themes.gohugo.io/
[5]: https://gohugo.io/tutorials/migrate-from-jekyll/

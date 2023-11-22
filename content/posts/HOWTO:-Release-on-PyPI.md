---
title: "HOWTO: Release on PyPI"
date: "2017-07-02 23:06:47"
type: "post"
---


# HOWTO: Release on PyPI
These are some commands that is meant to keep a track of how to release a package on [PyPI](https://pypi.python.org), python packaging index.

Before you release, make sure that you have pull the updated version of the project from your source control website (Github/Gitlab) , removed all the debug statements, and updated the versions in documentation.

Then, the first step is to tag the release in version control, here are examples for git:


    $ git tag 3.1.0
    $ git push upstream master --tags

`upstream` here is supposed to be main code repository for the project and not your fork, but if you are strict about Merge(or Pull) Request workflow, then you can push to your fork and send in a pull request and merge it manually.

The next step is to create the tarball which will actually be uploaded to PyPI.


    # Clean the tree to remove all the files that are not required for the release.
    $ git clean -dxffi
    # Check there is nothing in staging/untracked/random dotfiles
    $ git status
    # Create the tarball.
    $ python setup.py sdist

The last command above will create a new tarball named `projectname-version.tar.gz` in `dist/` directory relative to your `setup.py`.

To upload your project, you first need to register with PyPI. If you have already registered, you can proceed to authenticate your machine with PyPI.


    # Command below will log you in PyPI. It will ask you for youg login credentials.
    $ python setup.py register

It is no longer possible to upload the tarball to PyPI directly, like before using the command above. A python package called `[twine](https://pypi.python.org/pypi/twine)`  can be used to securely (over HTTPS) upload signed packages to PyPI.


    # Use sudo with the command below if you want it to be installed globally.
    $ pip install twine
    # The -s flag below will sign your package with the privatekey that belongs to the
    # email address registed with PyPI.
    $ twine upload -s dist/projectname-version.tar.gz

That is all. You can verify that your package is up on PyPI now and enjoy!



---
title: "HOWTO: Create a .deb package"
date: "2017-07-03 20:17:27"
---


# HOWTO: Create a .deb package
This is  [basic tutorial](https://wiki.debian.org/Packaging/Intro?action=show&redirect=IntroDebianPackaging) on how to get started with building debian packages. It mostly assumes that that the package you are trying to build is either using make or some other simple build system.

To get some more details about options and other advanced methods and tooling to build .deb packages, see [this post](https://wiki.debian.org/BuildingTutorial#Edit_the_source_code). It is a tutorial to build a debian package.

I started off with building the package for a [scons](http://scons.org/) based project, [astroid](https://github.com/astroidmail/astroid/). While scons works fine when you are manually trying to compile and install the project, it lacks some important functionality that makes packaging a project based off it difficult. Of the [other problems](https://wiki.gentoo.org/wiki/SCons#Why_you_should_NOT_use_SCons_in_your_project) with this build system, the most prominent one is that it doesn’t honor some environment flags like `CFLAGS` and `CPPFLAGS` or `LD_FLAGS`, which means the package maintainer has to add more code to make the build system honor these. 

Also, scons doesn’t allow specifying a DESTINATION directory to install and installs by default only the directory specified by `--prefix` (e.g. —prefix=/usr will result in package installed at `/usr/bin`). This becomes a problem because when you try to create a .deb package, you need to install everything under `debian/projectname`. 


- [This thread](https://askubuntu.com/questions/245008/what-is-the-right-way-to-debianize-software-that-uses-the-scons-build-system) was very helpful for me to get started on writing build script for ubuntu.
- There are options to make things work for other project by using this [recipe](https://bitbucket.org/scons/scons/wiki/Installer).


The command I am using to build the package is `debuild -us -uc` . Here is a copy of the `debian/rules` script which is the most important one to build:


    #!/usr/bin/make -f
    
    SCONS=scons
    DEB_SCONS_OPTIONS := --prefix=/usr --destdir=$(CURDIR)/debian/astroid/usr
    
    %:
            dh $@
    
    override_dh_auto_build:
            $(SCONS) $(DEB_SCONS_OPTIONS) -j 4 build
    
    override_dh_auto_clean:
            dh_auto_clean
            rm -rf .sconf_tem/ .sconsign.dblite config.log
    
    override_dh_auto_install:
            $(SCONS) $(DEB_SCONS_OPTIONS) install

Note that this is a `Makefile` so everything in indented with a single `TAB` character.

I also had to modify the `SConstruct` file, which is nothing but a python file without any extensions and is sort of like a scons config file for the project.



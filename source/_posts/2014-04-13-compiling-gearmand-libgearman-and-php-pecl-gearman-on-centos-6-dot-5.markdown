---
layout: post
title: "Compiling gearmand, libgearman and php-pecl-gearman on CentOS 6.5"
date: 2014-04-13 00:49
comments: false
categories: 
---

I was having a bit of trouble compiling and installing gearmand job server on a brand new centos 6.5 server so here is a little guide on how to do it.

<!-- more -->

# Install from source

First of all, this is a sort summary of all the process. Later on I'll create a more indepth article.

The easiest way to go about it is to install from source. Not always the best alternative but the easiest for sure.

## Get the latest tar for gearmand

Now there are two branches for gearmand: 1.0 (current stable - as of this writting) and 1.2 (development).
Some things may or may not break with >=1.1 releases so here I'll compile against both branches. Latest from the stable branch (1.0.6) and from the development branch (1.1.12)

- first grab the gearmand tar
- configure make make install

- yum install boost-devel gcc-c++ libevent-devel

## Install the php extension

    wget http://pecl.php.net/get/gearman-1.1.2.tgz
    phpize && configure && make && sudo make install


# Install from EPEL

- Grab the latests rpms from EPEL

- Install libgearman and php-pecl-gearman

    yum install libgearman gearmand

Alternatively you can grab the php extension from https://pecl.php.net/package/gearman then:

	phpize
	make && compile

You should be good to go!

---
layout: default
title: On Redmine, Apache, Subversion, and Ubuntu 9.10
tags: ubuntu redmine subversion apache
description: How to fix Redmine.pm with Apache on Ubuntu 9.10
keywords: ubuntu, apache, redmine, subversion, mpm
---

# On Redmine, Apache, Subversion, and Ubuntu 9.10

<p class="post-date">{{ page.date | date: "%-d %B %Y" }}</p>

If you’re installing Redmine in a recent version of Ubuntu, you may want to be aware of this.

## The Problem

According to the Ubuntu 9.10 [Apache package documentation](http://packages.ubuntu.com/karmic/apache2), the default package for Apache, installed by running sudo aptitude install apache2, installs the apache2-mpm-worker package as one of its dependencies. The [worker MPM](http://httpd.apache.org/docs/2.1/mod/worker.html) implements a hybrid multi-process multi-threaded server, which is used to serve a large number of requests with fewer system resources than a process-based server. If you’re just using Apache to serve up Redmine, this shouldn’t cause an issue. However, if you’re using Redmine.pm to integrate the authentication of of your Subversion repositories with projects in Redmine, you may run into an issue if you try to use any subversion commands besides checkout and commit. When performing other actions, such as COPY, you will see error messages like:

    svn: COPY de '/svn/myapp/!svn/bc/38/trunk': Could not read status line: Secure connection truncated (https://svn.mycompany.com)

if using https and

    svn: COPY de '/svn/myapp/!svn/bc/38/trunk': Could not read status line: connection was closed by server (http://svn.mycompany.com)

if using http.

## The Fix

The fix in Ubuntu is very simple. Just run:

    sudo aptitude install apache2-mpm-prefork

to install the Apache [prefork](http://httpd.apache.org/docs/2.0/mod/prefork.html) module. This will turn off the worker MPM that’s installed by default and instead use the prefork MPM.

This may not be ideal for high traffic sites, but is necessary in order for the Redmine Subversion integration to work until Redmine.pm becomes thread-safe.

## Additional Reading

* [http://www.redmine.org/boards/2/topics/4179](http://www.redmine.org/boards/2/topics/4179)

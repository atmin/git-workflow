git-workflow
============

How to manage multiple SVN paths via Git



### Setup two local SVN repos for testing

We'll create local repositories
* /tmp/test-svn
* /tmp/test-svn2

and populate them with some real SVN data

Details: http://git-scm.com/book/en/Git-and-Other-Systems-Git-and-Subversion

    $ mkdir /tmp/test-svn
    $ mkdir /tmp/test-svn2
    $ svnadmin create /tmp/test-svn
    $ svnadmin create /tmp/test-svn2
    $ cat > /tmp/test-svn/hooks/pre-revprop-change
    #!/bin/sh
    exit 0;<Ctrl-D>
    $ chmod +x /tmp/test-svn/hooks/pre-revprop-change
    $ cp /tmp/test-svn/hooks/pre-revprop-change /tmp/test-svn2/hooks/pre-revprop-change
    $ svnsync init file:///tmp/test-svn http://progit-example.googlecode.com/svn/
    $ svnsync init file:///tmp/test-svn2 http://progit-example.googlecode.com/svn/


### Add an existing SVN path to a local Git repo:

    git svn 
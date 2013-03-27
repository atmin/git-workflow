`☺` How to manage multiple SVN paths via Git 
============================================
----------------------------------------



`✓` Setup two local SVN repos for testing
-------------------------------------

Following code will setup local repositories `/tmp/test-svn` and `/tmp/test-svn2`
and populate them with [some real SVN data](http://progit-example.googlecode.com/svn/)


    mkdir /tmp/test-svn
    mkdir /tmp/test-svn2

    # create empty repositories
    svnadmin create /tmp/test-svn
    svnadmin create /tmp/test-svn2

    # install a pre-revprop-change hook script
    echo -e "#"'!'"/bin/sh\nexit 0;" > /tmp/test-svn/hooks/pre-revprop-change
    chmod +x /tmp/test-svn/hooks/pre-revprop-change
    cp /tmp/test-svn/hooks/pre-revprop-change /tmp/test-svn2/hooks/pre-revprop-change

    # sync our repos with a sample repository
    svnsync init file:///tmp/test-svn http://progit-example.googlecode.com/svn/
    svnsync init file:///tmp/test-svn2 http://progit-example.googlecode.com/svn/
    svnsync sync file:///tmp/test-svn
    svnsync sync file:///tmp/test-svn2

`✎` [git-scm.com/...](http://git-scm.com/book/en/Git-and-Other-Systems-Git-and-Subversion)




`✓` Init new local Git repository
-----------------------------

    mkdir localrepo
    cd localrepo
    git init



`✓` Add an existing SVN path to a local repository
----------------------------------------------

`ℹ` Your settings are stored in `.git/config`

    # Add a new svn-remote as branch named 'test-svn-trunk'
    git config --add svn-remote.test-svn-trunk.url file:///tmp/test-svn/trunk
    git config --add svn-remote.test-svn-trunk.fetch file:///tmp/test-svn/trunk





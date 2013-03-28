`☺` Tips on how to manage multiple SVN paths via Git 
====================================================
--------



`✓` Setup two local SVN repos for testing
-----------------------------------------

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

`✎` http://git-scm.com/book/en/Git-and-Other-Systems-Git-and-Subversion




`✓` Init new local Git repository
---------------------------------

    mkdir localrepo
    cd localrepo
    git init
    # Initialized empty Git repository in /tmp/localrepo/.git/




`✓` Add an existing SVN path to a local repository
--------------------------------------------------

`ℹ` Your settings are stored in `.git/config`

    # Add a new svn-remote as remote branch named 'test-svn-trunk'
    git config --add svn-remote.test-svn-trunk.url file:///tmp/test-svn/trunk
    git config --add svn-remote.test-svn-trunk.fetch :refs/remotes/test-svn-trunk

    # Fetch the whole history in the new branch. Optional: -r BEGINNING_SVN_REVISION
    git svn fetch test-svn-trunk




`✓` Branches
------------

    # List local branches. Add -a to list remotes, as well
    git branch

    # Switch to branch
    git checkout branch-name

    # Create new local branch to track a remote branch
    git checkout -b local-test-svn-trunk -t test-svn-trunk
    # Branch local-test-svn-trunk set up to track local ref refs/remotes/test-svn-trunk.
    # Switched to a new branch 'local-test-svn-trunk'




`✓` Update external SVN path (svn up)
-------------------------------------

    git svn rebase test-svn-trunk
    # Current branch local-test-svn-trunk is up to date.
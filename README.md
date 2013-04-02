`☺` Tips on how to manage multiple SVN paths via Git 
====================================================

Work in progress.
  
    
  
  
  


`✓` Setup local SVN repository for testing
------------------------------------------

Following code will setup a local SVN repository `/tmp/test-svn`
and populate it with [some real SVN data](http://progit-example.googlecode.com/svn/)

    mkdir /tmp/test-svn

    # create empty repository
    svnadmin create /tmp/test-svn

    # install a pre-revprop-change hook script
    echo -e "#"'!'"/bin/sh\nexit 0;" > /tmp/test-svn/hooks/pre-revprop-change
    chmod +x /tmp/test-svn/hooks/pre-revprop-change

    # sync our repos with a sample repository
    svnsync init file:///tmp/test-svn http://progit-example.googlecode.com/svn/
    svnsync sync file:///tmp/test-svn

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
    # Don't forget the trailing slash of the url
    git config --add svn-remote.test-svn-trunk.url file:///tmp/test-svn/trunk/
    git config --add svn-remote.test-svn-trunk.fetch :refs/remotes/test-svn-trunk

    # Fetch the whole history in the new branch. Optional: -r BEGINNING_SVN_REVISION
    git svn fetch test-svn-trunk




`✓` Branches
------------

##### List local branches

	# -a will list remotes, as well
    git branch


##### Switch to branch

    git checkout branch-name


##### Create new local branch to track a remote branch

    git checkout -b local-test-svn-trunk -t test-svn-trunk
    # Branch local-test-svn-trunk set up to track local ref refs/remotes/test-svn-trunk.
    # Switched to a new branch 'local-test-svn-trunk'


##### Rename local branch

    git branch -m old-branch new-branch


##### Rename remote branch

* `git gc` to package all refs into `.git/packed-refs`

* Edit `.git/packed-refs`, rename the branch

* Edit `.git/config`, rename the branch


##### Changes in local branches
You make a new branch to store your work on some issue

    git branch new-issue
    git checkout new-issue

Then you do your work, commit/rebase/cherry-pick/whatever suits our needs
and finally have a batch of commits ready for the upstream svn repo. Your local
git repo history looks something like this:

    A-B        <-- master
       \
        C-D-E  <-- new-issue

An easy thing git-svn can do for you is push merge commits as a squash of all the
commits in the merged branch. This means that in the svn repo you can get a single
commit that has all the changes you made in all your local commits in the `new-issue` branch

In order to do that *and* keep your local branches(so you have more information on
what, when and how you did) you need to do the followiing:

    #go back to your master branch, or whichever branch
    #is tracking the svn upstream you want to push to
    git checkout master

    #rebase form the svn repository, so your up to date
    #befrore commiting back upstream
    git svn rebase

    #merge your local branch back into the branch that's tracking
    #the svn repository
    git merge new-issue

    #push your changes upstream ot the svn repository
    git svn dcommit


Most times merge commits in git are "empty", meaning that they have no
diff assigned to them. They just have two prent commits, each from one of the
branches that are being merged. In case of conflicts during merge, that
git can not resolve on its own, the way you resolve the conflicts is added as
a diff to the merge commit.
For subversion a commit is nothing more than a combination of diffs on files, so
git's merge commits are meaningless in subversion context. So what you get with
git-svn is that a merge commit is converted to a squash(see the [man page](http://linux.die.net/man/1/git-merge))
of the branch you've merged in and only after that commited upstream to the subversion repository.

If you look at your log now, oyu'll see your last commit is still a git merge
commit, referencing the two branches you merged, but also has a subversion
revision number assigned to it by git-svn.

*!NB!* If you do `git svn rebase` after you merge the branch back into master,
the rebase could insert all the commits from svn back before you branched, depending
on when you branched and when you last rebased from svn. So it's easier to just do
the `svn rebase` before making the local merge commit.


`✓` Update external SVN path (svn up)
-------------------------------------

    git svn rebase test-svn-trunk
    # Current branch local-test-svn-trunk is up to date.




`✓` Apply selected commits from one branch to another 
-----------------------------------------------------

Git cherry-pick-ing is basically a shortcut for this SVN task:

* export a patch file from a branch
* move it to another branch
* apply the patch
* remove the temporary patch file

Initial branch structure

    A-B              <-- master
       \
        C-D-E-F-G-H  <-- long_branch

Fork `master` branch into `short_branch`

    git checkout master -b short_branch


Take commits E..H (left side is not inclusive) and apply them to `short_branch`

    # Current active branch is `short_branch`
    git cherry-pick E..H


The result:

        F'-G'-H'     <-- short_branch
       /
    A-B              <-- master
       \
        C-D-E-F-G-H  <-- long_branch

`✎` http://stackoverflow.com/a/9853814  
`✎` http://wiki.koha-community.org/wiki/Using_Git_Cherry_Pick




`✓` A successful Git branching model
------------------------------------
`✎` http://nvie.com/posts/a-successful-git-branching-model/

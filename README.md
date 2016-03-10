# Purpose

Example repository to demonstrate commit squashing

# Description

Commit squashing can be a useful way to keep the repository commit log clean, and ideally make for easier merges as well. The caveat is that for it to not cause problems, you need to be working __alone__ on a __true, short-lived__ feature branch. Otherwise things can get out of sync when multiple developers are squashing different sets of changes.

Once your feature is ready for peer review or merging, on your local branch you can squash all of your individual changes into one change using the interactive rebasing functionality of git.

# How

To use interactive rebase, you need to know the commit id that occurs immediately before your feature changes (use `git log`).

```
commit 8b3926405b3c5efe08a9117248c5b91495cf11a0
Author: Mario Russo <mail.mr@gmail.com>
Date:   Thu Mar 10 10:21:58 2016 +0100

    Updated format of math tables

commit 10d61a67e2aa9c8b51f512959e81c9bb5ba53535
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:21:16 2016 +0100

    Added markdown headers to math tables

commit 60ba34c9dc1f6935a9c29f5d6f8a6d9c6c11ba96
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:19:49 2016 +0100

    Updated multiplication logic

commit e174c1baf55b04d1f2eb1318e21c9b968bd86a4c
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:19:22 2016 +0100

    Updated subtraction logic

commit d4c65b2d0dbb194443d6f47bab78de3d96cc712e
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:19:00 2016 +0100

    Updated addition logic

commit 45be9acdff87c18e749d1e825eaa2aaf63b8c7ea
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:17:44 2016 +0100

    Adding README file

commit 39a208c2db917348c6dbadc84de99204a6f50ed4
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:15:55 2016 +0100

    Initial implementation of math example

```

In this case, I'd like to create a pull request with a single commit that contains all of the updates I made since the initial implementation. So I'll pick this commit:

```
commit 39a208c2db917348c6dbadc84de99204a6f50ed4
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:15:55 2016 +0100

    Initial implementation of math example
    ```
```
To open the interactive rebase editor

`git rebase -i 39a208c2db917348c6dbadc84de99204a6f50ed4`

The following editor window will appear. The goal for this screen is to select which commits you want to squish together.

```
pick 45be9ac Adding README file
pick d4c65b2 Updated addition logic
pick e174c1b Updated subtraction logic
pick 60ba34c Updated multiplication logic
pick 10d61a6 Added markdown headers to math tables
pick 8b39264 Updated format of math tables

# Rebase 39a208c..8b39264 onto 39a208c
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

So in this case, you want to pick one commit, and squash / meld the other commits into the previous. In the end, you want it to look like this

```
pick 45be9ac Adding README file
s d4c65b2 Updated addition logic
s e174c1b Updated subtraction logic
s 60ba34c Updated multiplication logic
s 10d61a6 Added markdown headers to math tables
s 8b39264 Updated format of math tables
```

In the next window you have the opportunity to edit the commit message that will be used in the single commit that results from this operation. Each commit message is given as a reference, so you know what work is included.

```
# This is a combination of 6 commits.
# The first commit's message is:
Adding README file

# This is the 2nd commit message:

Updated addition logic

# This is the 3rd commit message:

Updated subtraction logic

# This is the 4th commit message:

Updated multiplication logic

# This is the 5th commit message:

Added markdown headers to math tables

# This is the 6th commit message:

Updated format of math tables

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# rebase in progress; onto 39a208c
# You are currently editing a commit while rebasing branch 'master' on '39a208c'.
#
# Changes to be committed:
#       new file:   README.md
#       modified:   math.md
#
```

For our usage, the message should contain useful information like redmine issue number, brief description of feature, etc

```
Feature 1234: Fixed logic in math tables

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# rebase in progress; onto 39a208c
# You are currently editing a commit while rebasing branch 'master' on '39a208c'.
#
# Changes to be committed:
#       new file:   README.md
#       modified:   math.md
#
```

Done! Now looking at the `git log`, you'll see the incremental commits are all gone

```
commit 115ac2439124afa432d89de663108e67c9064cf0
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:17:44 2016 +0100

    Feature 1234: Fixed logic in math tables

commit 39a208c2db917348c6dbadc84de99204a6f50ed4
Author: Mario Russo <mr@labfolder.com>
Date:   Thu Mar 10 10:15:55 2016 +0100

    Initial implementation of math example
```

The process for pushing these changes to the remote repository is a little different because in reality, once you have squashed commits there will be changes in the remote repository that no longer exist in your local repository (this is why it is important that you are working alone on the branch!)

To make github not complain about this, you need to force push the changes. By forcing a push it will overwrite the remote repository with your local changes, so make sure you are good about keeping things in sync :)

`git push --force origin/<branch_name>`

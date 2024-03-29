title:Recover a deleted and overwritten branch in Git\
classes: language-markup\
pubDate:2016-06-03T08:28:47-07:00

Recover a deleted and overwritten branch in Git
===============================================

If you delete a branch `git reflog` is your friend, even after a day or two of adding code and moving branches around in the repo.

    git reflog --no-abbrev

Git reflog stores everything that’s happened, even checkouts. Everything has a hash, so you can checkout to that state, even if that branch was later deleted, you can go back to that point in the history.

I wanted to go back to a branch that got deleted because of broken code, I searched by commit message and quickly could find the point where I wanted to be. If the branch was called: feature/tickets/DF-777

    git reflog --no-abbrev | grep DF-777

Then I could check where in history I want to be, then I just checkout the branch, or even create a new branch from that state.

Reflog does get garbage collected so don’t rely on it as a storage medium.

This I discovered at: http://stackoverflow.com/questions/3640764/can-i-recover-branch-after-its-deletion-in-git

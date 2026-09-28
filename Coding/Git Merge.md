---
tags:
---
The Merge command in [[Git]] allows multiple [[#Branches|branches]]to be developed simultaneously and then connected (or merged) back together
## Branches
You can make new branches by using the command `git branch <branch_name>`. You can then change to your new branch using `git checkout <branch_name>`. You can also create a new branch and change to it in a single command by using the `-b` flag with `checkout`, in the form `git checkout -b <branch_name>`.

You can see all of your current branches using `git branch` with no other arguments. The branch that you’re currently on will be indicated with an asterisk. If you want to change back to `main` from any other branch, you can do so just like changing to any other branch using `git checkout main`.

Once you are done working on your feature branch and are ready to bring the commits that you’ve made on it to your main branch, you will need to perform what is known as a `merge`.

Merges are done by using the command `git merge <branch_name>` which will take the changes you’ve committed in `branch_name` and add them to the branch that you’re currently on.

Sometimes, the same lines in a file will have been changed by two different branches. When this happens, you will have a merge conflict when you try and merge those branches together. In order to finish merging the branches you will have to first resolve the conflict, which will be covered in a future lesson.

When you don’t need a branch anymore, it can be deleted using `git branch -d <branch_name>` if the branch has already been merged into `main`, or with `git branch -D <branch_name>` if it hasn’t. You will usually want to delete branches when you’re done with them, otherwise they can pile up and make it more difficult to find the branch you’re looking for when you need it.
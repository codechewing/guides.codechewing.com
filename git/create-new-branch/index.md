# How to create a new branch in Git

This quick guide assumes creating a new git branch from the **command line**.

For example, you've made some **changes to an existing branch**, but have decided these code changes would be better
off in a new branch. Assuming that you haven't yet committed these changes in the current branch, you can effectively
switch these changes into a new branch, with the following Git command:

```
$ git checkout -b branch_name
```

Executing `git status` will reveal your new branch destination. From here, you'll probably want to commit and push
the new branch up to the remote repository!

### Farewell

Running `git branch` is another convenient way to show what branch you're on:

```
$ git branch
  master
* branch_name
```

The branch with the asterisk is now your newly created (and active) branch.

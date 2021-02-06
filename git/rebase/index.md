# How to do a Git rebase

## Introduction

Rebasing rather than merging provides a cleaner history. This is very useful for developers that
are required to comprehend and analyse the introduced features and changes into a shared, possibly
public, branch.

It's especially useful for those who are responsible for maintaining release branches, as changes
will inevitably introduce regressions over time, which will need to be quickly identified and
reverted as necessary.

It's worth noting that rebasing should mostly occur on your **private** branches, due to the fact
you're *re-writing history*. If you're collaborating with other teammates on a **public** branch,
you should avoid rebasing (until the end) to avoid friction, loss of work, and unnecessary
overhead. This also holds true when updating pull requests based on reviewer feedback - the
reviewer(s) want to see your incremental changes pushed up - so save the rebase until the end,
when it's ready for merging. Your fellow teammates will appreciate this.

---

This guide will practically demonstrate a Git rebase by creating a feature branch from scratch,
that will require rebasing onto a target branch, i.e. `master`. This is a commonly performed
routine in software development teams, as something you would do in preparation for merging in
the feature for the next release, for example.

If you're a beginner at this, I recommend you follow along with the setup to get the most from
this tutorial. If you've come here as a quick refresh, skip towards the end where we perform
the git rebase.

Time to dive into how to perform a **git rebase**.

## Setup

We'll begin by setting up the scene:

```
$ mkdir rebase-time
$ cd rebase-time/
$ git init
Initialized empty Git repository in /here/rebase-time/.git/
```

In order to accurately simulate a git rebase, I'll be doing some file changes and commits to provide
some real output.

Let's bring the default `master` branch to life:

<pre><code class="shell">$ git status
On branch master
No commits yet
...
$ touch todo.md
$ git status
On branch master
...
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	todo.md
...
$ git add .
$ git commit -m "New todos"
[master (root-commit) 5d3f246] New todos
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 todo.md
</code></pre>

At this point, we have a `master` branch with a **base** commit:

<pre><code class="shell">$ git log --graph --abbrev-commit
* commit 5d3f246 (HEAD -> master)
  Author: pjlangley <fake@codechewing.com>
  Date:   Sat Jul 14 16:33:00 2018 +0100

      New todos
</code></pre>

Time to create a new feature branch - this is the branch that we'll be rebasing onto `master`. For you,
that might be `develop`, `release-1.0.1` or similar.

```
$ git checkout -b new/feature
Switched to a new branch 'new/feature'
$ touch README.md
$ git add .
$ git commit -m "New readme"
[new/feature f475cd9] New readme
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README.md
```

Let's review the commits so far:

<pre><code class="shell">$ git log --graph --abbrev-commit
* commit f475cd9 (HEAD -> new/feature)
| Author: pjlangley <fake@codechewing.com>
| Date:   Sat Jul 14 16:40:23 2018 +0100
|
|     New readme
|
* commit 5d3f246 (master)
  Author: pjlangley <fake@codechewing.com>
  Date:   Sat Jul 14 16:33:00 2018 +0100

      New todos
</code></pre>

Right now, a rebase will do _nothing_, because `new/feature` is already based on the tip of `master`.

Here comes the first illustration of the state of the two branches at play:

<pre><code class="plain">new/feature
    ↓
    B
   /
  A
  ↑
master
</code></pre>

- ↓ & ↑ indicate the tip of the respective branch
- A == commit `5d3f246`
- B == commit `f475cd9`

You can even attempt a rebase at this stage if you want to experiment:

```
$ git rebase master
Current branch new/feature is up to date.
```

As you can see, nothing happens, as expected.

### Branch `master` moves on with haste

As these things do, `master` will inevitably receive new changes, that we'll need our new feature branch
to rebase off.

To simulate this, I'll switch back to `master`, and create a new commit there:

<pre><code class="shell">$ git checkout master
Switched to branch 'master'
$ echo "A new todo item" >> todo.md
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   todo.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git add .
$ git commit -m "New todo item"
[master 2651450] New todo item
 1 file changed, 1 insertion(+)
</code></pre>

As a quick review, we now have the following commited on `master`:

<pre><code class="shell">$ git log --graph --abbrev-commit
* commit 2651450 (HEAD -> master)
| Author: pjlangley <fake@codechewing.com>
| Date:   Sat Jul 14 17:01:01 2018 +0100
|
|     New todo item
|
* commit 5d3f246
  Author: pjlangley <fake@codechewing.com>
  Date:   Sat Jul 14 16:33:00 2018 +0100

      New todos
</code></pre>

Here's the illustrated view:

<pre><code class="plain">new/feature
    ↓
    B
   /
  A-C
    ↑
  master
</code></pre>

- ↓ & ↑ indicate the tip of the respective branch
- A - as before
- B - as before
- C == commit `2651450`

As you can see from above, the base of `master` has indeed **moved on**. From here, you will hopefully
understand why it is at this point that the `new/feature` branch  is applicable for rebasing.

## Time to rebase

Now we've got something real setup for rebase, let's, well, **rebase**!

<pre><code class="shell">$ git checkout new/feature
Switched to branch 'new/feature'
</code></pre>

3...2...1...

```
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: New readme
```

From the output, specifically the `Applying: New readme` part, we have an indication that the _"New readme"_
commit from the feature branch (originally `f475cd9`) has been applied,
aka rebased, on top of the series of commits from `master`.

A snapshot of the latest illustration will help here:

<pre><code class="plain">  new/feature
      ↓
      B
     /
  A-C
    ↑
  master
</code></pre>

- ↓ & ↑ indicate the tip of the respective branch
- A - as before
- B == commit `0453daa` (this has _changed_ - see below for further discussion).
- C - as before

And here's the _clean history_ result, a key value in applying rebasing to your projects:

<pre><code class="shell">$ git log --graph --abbrev-commit
* commit 0453daa (HEAD -> new/feature)
| Author: pjlangley <fake@codechewing.com>
| Date:   Sat Jul 14 16:40:23 2018 +0100
|
|     New readme
|
* commit 2651450 (master)
| Author: pjlangley <fake@codechewing.com>
| Date:   Sat Jul 14 17:01:01 2018 +0100
|
|     New todo item
|
* commit 5d3f246
  Author: pjlangley <fake@codechewing.com>
  Date:   Sat Jul 14 16:33:00 2018 +0100

      New todos
</code></pre>

You can see that the feature branch changes have been applied linearly **after** the commits on `master`.

This is great, and we're done. There is however, an important change beneath the clean result to be
aware of here.

Behind the scenes, Git has actually changed the commit ID of the feature branch commit. It was
`f475cd9`, but is now `0453daa`.

If there were more commits on this branch, they'd have changed too. The details of this are beyond the
scope of this guide, but it's something to at least know about when performing a rebase.

---

All that's left is to incorporate the `new/feature` branch into `master` when ready.
Depending on what branching/merging strategy you use, this could simply involve a
**no fast forward** merge, for example:

<pre><code class="shell">$ git checkout master
$ git merge --no-ff new/feature
Merge made by the 'recursive' strategy.
 README.md | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README.md
</code></pre>

Here's the Git graph for the resulting logs, post merge.

<pre><code class="shell">$ git log --graph --abbrev-commit
*   commit 01fd424 (HEAD -> master)
|\  Merge: 2651450 0453daa
| | Author: pjlangley <fake@codechewing.com>
| | Date:   Sat Jul 14 17:14:23 2018 +0100
| |
| |     Merge branch 'new/feature'
| |
| * commit 0453daa (new/feature)
|/  Author: pjlangley <fake@codechewing.com>
|   Date:   Sat Jul 14 16:40:23 2018 +0100
|
|       New readme
|
* commit 2651450
| Author: pjlangley <fake@codechewing.com>
| Date:   Sat Jul 14 17:01:01 2018 +0100
|
|     New todo item
|
* commit 5d3f246
  Author: pjlangley <fake@codechewing.com>
  Date:   Sat Jul 14 16:33:00 2018 +0100

      New todos
</code></pre>

### Conclusion

We looked at how to prepare a new feature branch for incorporating into a master branch, by using the
Git rebase capability, a powerful command in the Git toolkit.

By rebasing our feature branch **before** merge, we create a clean, easy to comprehend, neatly batched
commit history, that flows **after** the latest commit in the target branch. This presents a natural view
of the feature inclusion on the receiving branch, clearly visualising the introduction of new code
and functionality.

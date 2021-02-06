# How to fix Git rebase merge conflicts

## Introduction

Dealing with **merge conflicts** that arise from a **Git rebase** can certainly be intimidating,
especially for beginners. The merge conflict alone can be tricky enough to deal with, and handling
this from a rebase attempt differs slightly from a normal Git merge.

It's important to understand the basic commands to help step through the sticky tar pit, that is the
merge conflicts, and come through feeling confident that all issues have been adequately
investigated and resolved.

When confronting merge conflicts with a Git rebase, or otherwise, when you're unsure as to how
it can be confidently resolved, a diligent software engineering practice is to go and speak to the
person that authored the code you're trying to merge with (if practical). Nothing can beat good
communication here.

---

I'll practically demonstrate the running of a feature branch alongside a `master` branch, where
I'll fabricate a scenario that'll trigger a merge conflict during the short life cycle of our
feature branch.

You'll see an example of how conflicts can occur, and how to rectify your code, specifically from
a Git rebase, introducing some simple commands to help you along the way.

Remember, *I can't help you with the complexity of the merge conflict itself* (that needs context
and has a lot of variables), but I will show you the fundamentals of how to overcome and continue
progress after hitting merge conflicts when rebasing your code.

## Setup

This guide will start from the very beginning, for full context. I recommend that beginners
follow along. If however, you just want to read the conflict resolution part, skip towards the
end of this guide.

Here we go...

<pre><code class="shell">$ mkdir git-rebase-conflicts
$ cd git-rebase-conflicts
$ git init
Initialized empty Git repository in /somewhere/git-rebase-conflicts/.git/
</code></pre>

Let's bring the `master` branch to life, and pretend that it's been running for a while, and
contains many a quality (and dodgy) code commit.

<pre><code class="shell">$ touch document.md</code></pre>

Open the file and update the contents to read:

<pre><code class="plain">I must make sure I learn all that git rebase merge conflict stuffs.</code></pre>

Commit this change:

<pre><code class="shell">$ git status
On branch master
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)

	document.md
...
$ git add .
$ git commit -m "New document"
[master (root-commit) 428e2b3] New document
 1 file changed, 1 insertion(+)
 create mode 100644 document.md
</code></pre>

Create a new feature branch off this:

<pre><code class="shell">$ git checkout -b new/feature
Switched to a new branch 'new/feature'
</code></pre>

---

We're now going to make a change on this feature branch, that'll end up conflicting with `master`.
Let's make a simple change to the file - change it to now read:

<pre><code class="plain">I **must** make sure I learn all that git rebase merge conflict stuffs.</code></pre>

To be clear:

<pre><code class="shell">$ git diff
diff --git a/document.md b/document.md
index 0bb85be..708f022 100644
--- a/document.md
+++ b/document.md
@@ -1 +1 @@
-I must make sure I learn all that git rebase merge conflict stuffs.
+I **must** make sure I learn all that git rebase merge conflict stuffs.
</code></pre>

Commit this change on our `new/feature` branch:

<pre><code class="shell">$ git status
On branch new/feature
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   document.md
...
$ git add .
$ git commit -m "Add emphasis"
[new/feature d8b36d1] Add emphasis
 1 file changed, 1 insertion(+), 1 deletion(-)
</code></pre>

Switch back to `master`:

<pre><code class="shell">$ git checkout master
Switched to branch 'master'
</code></pre>

Our `master` branch is blissfully unaware of the changes in `new/feature`:

<pre><code class="shell">$ git log --graph --abbrev-commit
* commit 428e2b3 (HEAD -> master)
  Author: pjlangley <fake@codechewing.com>
  Date:   Sun Jul 22 16:32:18 2018 +0100

      New document
</code></pre>

For once, we're going to create a deliberate conflict - how rebellious!

Let's add a title to the `document.md` file:

<pre><code class="plain"># todos

I must make sure I learn all that git rebase merge conflict stuffs.
</code></pre>

Changing the same line will cause a conflict here when we rebase, because we've already amended
this line in the `new/feature` branch.

To be clear of the changes, here's the diff:

<pre><code class="shell">$ git diff
diff --git a/document.md b/document.md
index 0bb85be..14988d4 100644
--- a/document.md
+++ b/document.md
@@ -1 +1,3 @@
+# todos
+
 I must make sure I learn all that git rebase merge conflict stuffs.
</code></pre>

Now commit the changes:

<pre><code class="shell">$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   document.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git add .
$ git commit -m "Added title"
[master 76b9d4e] Added title
 1 file changed, 2 insertions(+)
</code></pre>

Here's what `master` has lined up in its history:

<pre><code class="shell">$ git log --graph --abbrev-commit
* commit 76b9d4e (HEAD -> master)
| Author: pjlangley <fake@codechewing.com>
| Date:   Tue Jul 24 05:30:03 2018 +0100
|
|     Added title
|
* commit 428e2b3
  Author: pjlangley <fake@codechewing.com>
  Date:   Sun Jul 22 16:32:18 2018 +0100

      New document
</code></pre>

Here's an illustration of the commits in relation to our `new/feature` branch:

<pre><code class="plain">new/feature
    ↓
    B
   /
  A-C
    ↑
  master
</code></pre>

- ↓ & ↑ indicate the tip of the respective branch
- A == commit `428e2b3`
- B == commit `d8b36d1`
- C == commit `76b9d4e`

As you can see, `new/feature` is applicable for rebasing onto `master`.

## Resolving Git rebase merge conflicts

With everything setup for hitting a conflict, it's time to brace yourself as we switch to our
feature branch and **rebase** onto `master`:

<pre><code class="shell">$ git checkout new/feature
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: Add emphasis
Using index info to reconstruct a base tree...
M	document.md
Falling back to patching base and 3-way merge...
Auto-merging document.md
CONFLICT (content): Merge conflict in document.md
error: Failed to merge in the changes.
Patch failed at 0001 Add emphasis
The copy of the patch that failed is found in: .git/rebase-apply/patch

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
</code></pre>

Oh no! A conflict... who knew?! First things first - remember to keep calm and carry on.

<img
    src="/assets/images/posts/git/rebase-merge-conflicts/keep-calm-and-carry-on.jpg"
    alt="Keep calm and carry on with a Git rebase!"
    class="img-thumbnail">

The output reveals some options here:

- If you're not up to the task, or perhaps you've run out of time for the day, and the rebase
conflicts can wait until the morning; `git rebase --abort` will reset to how it was before 😅
- The merge conflict can be skipped over with `git rebase --skip`, which we don't want to do.
If we were to run `--skip` here, we'd lose/drop the conflicting emphasis changes (`**must**`)
we made in our feature branch. I can't think of a common use case for performing a *skip*,
so I suspect that most developers will never need to use this in practice.
- What we're interested in is this line:
>`Resolve all conflicts manually, mark them as resolved with`
>`"git add/rm <conflicted_files>", then run "git rebase --continue".`

Without further ado, let's fix this merge conflict.

Open `document.md` to discover what trouble we've got ourselves into:

<pre><code class="plain"><<<<<<< HEAD
# todos

I must make sure I learn all that git rebase merge conflict stuffs.
=======
I **must** make sure I learn all that git rebase merge conflict stuffs.
>>>>>>> Add emphasis
</code></pre>

As this conflict has relatively low complexity, it's pretty easy to make sense of (especially
as we authored both branches of the code). The `HEAD` section at the top, pulled in from `master`,
contains the new title (`# todos`), and *doesn't* contain our emphasis change (`**must**`).

Let's go ahead and take the top version, from `HEAD`, and manually add the emphasis back in:

<pre><code class="plain"># todos

I **must** make sure I learn all that git rebase merge conflict stuffs.
</code></pre>

Super. Now we can `add` this file so Git is aware we've resolved the conflict, and `continue` on
with our rebase:

<pre><code class="shell">$ git add document.md
$ git status
rebase in progress; onto 76b9d4e
You are currently rebasing branch 'new/feature' on '76b9d4e'.
  (all conflicts fixed: run "git rebase --continue")

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   document.md

$ git rebase --continue
Applying: Add emphasis
</code></pre>

No dramas the second time around. It's also worth noting that on `git status`, the rebase
recovery instructions are helpfully informative.

Here's the newly combined commit log with `master`:

<pre><code class="shell">$ git log --graph  --abbrev-commit
* commit 6d054ee (HEAD -> new/feature)
| Author: pjlangley <fake@codechewing.com>
| Date:   Sun Jul 22 16:43:55 2018 +0100
|
|     Add emphasis
|
* commit 76b9d4e (master)
| Author: pjlangley <fake@codechewing.com>
| Date:   Tue Jul 24 05:30:03 2018 +0100
|
|     Added title
|
* commit 428e2b3
  Author: pjlangley <fake@codechewing.com>
  Date:   Sun Jul 22 16:32:18 2018 +0100

      New document
</code></pre>

You can see that the `"Add emphasis"` commit has been successfully rebased onto `master`.
Mission accomplished!

This can be illustrated like so:

<pre><code class="plain"> new/feature
      ↓
      B
     /
  A-C
    ↑
  master
</code></pre>

- ↓ & ↑ indicate the tip of the respective branch
- A - same as before
- B == commit `6d054ee` (this has changed by design, as a result of the Git rebase, was previously `d8b36d1`)
- C - same as before

Note: If you have more than one conflict, you simply loop through iterations of what we've done above.
You keep doing `git add` and `git rebase --continue` after fixing the next set of conflicts until
you're all done.

### Conclusion

You've learnt how to resolve code conflicts when rebasing a feature branch back onto the target
branch. With a set of simple Git commands, it's really just a matter of correctly incorporating
your changes with the latest changes, incrementally moving forwards with the rebase.

The complexity of this is mostly contextual. It depends how far the code you wish to integrate
has branched away from the code in the target branch (for us, `master`). This is a trained skill,
and one which the code authors of the target changes can help you with.

To learn more about Git rebasing fundamentals, visit my other
[Git rebase guide](/git/rebase).

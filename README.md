# update-refs-test
Testing git rebase --update-refs behavior

Here's a stack (branch-1-in-stack -> branch-2-in-stack -> branch-3-in-stack) that is created, and another branch, branch-that-will-be-merged, that is created and PR'ed and merged during the stack's lifetime. So we can illustate how `git rebase --updated-refs` can be used to rebase the whole stack instead of having to check out each branch in turn and rebase.

## Set up some branches for the test, each with a new file

```
[zarniwoop ~] git clone https://github.com/bakert/update-refs-test.git
Cloning into 'update-refs-test'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (3/3), done.
[zarniwoop ~] cd update-refs-test
[zarniwoop update-refs-test] (main ✔) git switch -c branch-that-will-be-merged
Switched to a new branch 'branch-that-will-be-merged'
[zarniwoop update-refs-test] (branch-that-will-be-merged ✔) git switch -c branch-1-in-stack
Switched to a new branch 'branch-1-in-stack'
[zarniwoop update-refs-test] (branch-1-in-stack ✔) cat >branch-1-in-stack
Hello
[zarniwoop update-refs-test] (branch-1-in-stack x) git add .
[zarniwoop update-refs-test] (branch-1-in-stack x) git commit -m "Branch 1 in stack"
[branch-1-in-stack f085060] Branch 1 in stack
 1 file changed, 1 insertion(+)
 create mode 100644 branch-1-in-stack
[zarniwoop update-refs-test] (branch-1-in-stack ✔) git switch -c branch-2-in-stack
Switched to a new branch 'branch-2-in-stack'
[zarniwoop update-refs-test] (branch-2-in-stack ✔) cat >branch-2-in-stack
File 2
[zarniwoop update-refs-test] (branch-2-in-stack x) git add .
[zarniwoop update-refs-test] (branch-2-in-stack x) git commit -m "Branch 2 in stack"
[branch-2-in-stack 13d94c4] Branch 2 in stack
 1 file changed, 1 insertion(+)
 create mode 100644 branch-2-in-stack
[zarniwoop update-refs-test] (branch-2-in-stack ✔) git switch -c branch-3-in-stack
Switched to a new branch 'branch-3-in-stack'
[zarniwoop update-refs-test] (branch-3-in-stack ✔) cat >branch-3-in-stack
Another file
[zarniwoop update-refs-test] (branch-3-in-stack x) git add .
[zarniwoop update-refs-test] (branch-3-in-stack x) git commit -m "Branch 3 in stack"
[branch-3-in-stack 148e948] Branch 3 in stack
 1 file changed, 1 insertion(+)
 create mode 100644 branch-3-in-stack
[zarniwoop update-refs-test] (branch-3-in-stack ✔) git switch branch-that-will-be-merged
Switched to branch 'branch-that-will-be-merged'
[zarniwoop update-refs-test] (branch-that-will-be-merged ✔) cat >branch-that-will-be-merged
Here's a file that's going to get added AFTER all the stacked branches are created
But merged BEFORE them
[zarniwoop update-refs-test] (branch-that-will-be-merged x) git add .
[zarniwoop update-refs-test] (branch-that-will-be-merged x) git commit -m "Branch that will be merged"
[branch-that-will-be-merged fb6477d] Branch that will be merged
 1 file changed, 2 insertions(+)
 create mode 100644 branch-that-will-be-merged
[zarniwoop update-refs-test] (branch-that-will-be-merged ✔) git branch
  branch-1-in-stack
  branch-2-in-stack
  branch-3-in-stack
* branch-that-will-be-merged
  main
```

## Create some PRs for the stack and for the branch that will be merged while the stack is "open"

```
[zarniwoop update-refs-test] (branch-that-will-be-merged ✔) git switch branch-1-in-stack
Switched to branch 'branch-1-in-stack'
[zarniwoop update-refs-test] (branch-1-in-stack ✔) gh pr create
? Where should we push the 'branch-1-in-stack' branch? bakert/update-refs-test

Creating pull request for branch-1-in-stack into main in bakert/update-refs-test

? Title (required) Branch 1 in stack
? Body <Received>
? What's next? Submit
remote:
remote:
To ssh://github.com/bakert/update-refs-test.git
 * [new branch]      HEAD -> branch-1-in-stack
branch 'branch-1-in-stack' set up to track 'origin/branch-1-in-stack'.
https://github.com/bakert/update-refs-test/pull/1
[zarniwoop update-refs-test] (branch-1-in-stack ✔) git switch branch-2-in-stack
Switched to branch 'branch-2-in-stack'
[zarniwoop update-refs-test] (branch-2-in-stack ✔) gh pr create -B branch-1-in-stack
? Where should we push the 'branch-2-in-stack' branch? bakert/update-refs-test

Creating pull request for branch-2-in-stack into branch-1-in-stack in bakert/update-refs-test

? Title (required) Branch 2 in stack
? Body <Received>
? What's next? ;  [Use arrows to move, type to filter]

[zarniwoop update-refs-test] (branch-2-in-stack ✔) gh pr create -B branch-1-in-stack
? Where should we push the 'branch-2-in-stack' branch? bakert/update-refs-test

Creating pull request for branch-2-in-stack into branch-1-in-stack in bakert/update-refs-test

? Title (required) Branch 2 in stack
? Body <Received>
? What's next? Submit
branch 'branch-2-in-stack' set up to track 'origin/branch-2-in-stack'.
Everything up-to-date
https://github.com/bakert/update-refs-test/pull/2
[zarniwoop update-refs-test] (branch-2-in-stack ✔) git switch branch-that-will-be-merged
Switched to branch 'branch-that-will-be-merged'
[zarniwoop update-refs-test] (branch-that-will-be-merged ✔) gh pr create
? Where should we push the 'branch-that-will-be-merged' branch? bakert/update-refs-test

Creating pull request for branch-that-will-be-merged into main in bakert/update-refs-test

? Title (required) Branch that will be merged
? Body <Received>
? What's next? Submit
remote:
remote:
To ssh://github.com/bakert/update-refs-test.git
 * [new branch]      HEAD -> branch-that-will-be-merged
branch 'branch-that-will-be-merged' set up to track 'origin/branch-that-will-be-merged'.
https://github.com/bakert/update-refs-test/pull/3
```

At this point I merged branch-that-will-be-merged to main but I did NOT update local yet*

## Carry on creating the rest of the stack

The dev working on the stack doesn't know/care that someone has opened branch-that-will-be-merged as a PR and then merged it to main.

```
[zarniwoop update-refs-test] (branch-that-will-be-merged ✔) git switch branch-3-in-stack
Switched to branch 'branch-3-in-stack'
[zarniwoop update-refs-test] (branch-3-in-stack ✔) gh pr create
? Where should we push the 'branch-3-in-stack' branch? bakert/update-refs-test

Creating pull request for branch-3-in-stack into main in bakert/update-refs-test

? Title (required) (branch 3 in stack)

[zarniwoop update-refs-test] (branch-3-in-stack ✔) gh pr create -B branch-2-in-stack
? Where should we push the 'branch-3-in-stack' branch? bakert/update-refs-test

Creating pull request for branch-3-in-stack into branch-2-in-stack in bakert/update-refs-test

? Title (required) Branch 3 in stack
? Body <Received>
? What's next? Submit
remote:
remote:
To ssh://github.com/bakert/update-refs-test.git
 * [new branch]      HEAD -> branch-3-in-stack
branch 'branch-3-in-stack' set up to track 'origin/branch-3-in-stack'.
https://github.com/bakert/update-refs-test/pull/4
```

## Rebase the whole stack

By rebasing the top branch you can rebase the whole stack

```
[zarniwoop update-refs-test] (branch-3-in-stack ✔) git fetch origin main:main
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (1/1), 905 bytes | 905.00 KiB/s, done.
From ssh://github.com/bakert/update-refs-test
   532d7a3..00b5cbc  main       -> main
   532d7a3..00b5cbc  main       -> origin/main
[zarniwoop update-refs-test] (branch-3-in-stack ✔) git rebase --update-refs main
Successfully rebased and updated refs/heads/branch-3-in-stack.
Updated the following refs with --update-refs:
	refs/heads/branch-1-in-stack
	refs/heads/branch-2-in-stack
[zarniwoop update-refs-test] (branch-3-in-stack ✔) git push --force-with-lease origin branch-1-in-stack branch-2-in-stack branch-3-in-stack
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 14 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 812 bytes | 812.00 KiB/s, done.
Total 9 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), done.
To ssh://github.com/bakert/update-refs-test.git
 + f085060...e3a668c branch-1-in-stack -> branch-1-in-stack (forced update)
 + 13d94c4...5befd04 branch-2-in-stack -> branch-2-in-stack (forced update)
 + 148e948...90674e9 branch-3-in-stack -> branch-3-in-stack (forced update)
```

## Prove that the whole stack was rebased with one `git rebase --update-refs`

```
[zarniwoop update-refs-test] (branch-3-in-stack ✔) cat branch-that-will-be-merged
───────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: branch-that-will-be-merged
───────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ Here's a file that's going to get added AFTER all the stacked branches are created
   2   │ But merged BEFORE them
───────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
[zarniwoop update-refs-test] (branch-3-in-stack ✔) git switch branch-1-in-stack
Switched to branch 'branch-1-in-stack'
Your branch is up to date with 'origin/branch-1-in-stack'.
[zarniwoop update-refs-test] (branch-1-in-stack ✔) cat branch-that-will-be-merged
───────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: branch-that-will-be-merged
───────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ Here's a file that's going to get added AFTER all the stacked branches are created
   2   │ But merged BEFORE them
───────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
[zarniwoop update-refs-test] (branch-1-in-stack ✔) git switch branch-2-in-stack
Switched to branch 'branch-2-in-stack'
Your branch is up to date with 'origin/branch-2-in-stack'.
[zarniwoop update-refs-test] (branch-2-in-stack ✔) cat branch-that-will-be-merged
───────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: branch-that-will-be-merged
───────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ Here's a file that's going to get added AFTER all the stacked branches are created
   2   │ But merged BEFORE them
───────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
[zarniwoop update-refs-test] (branch-2-in-stack ✔)
```

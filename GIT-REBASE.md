# Practicing `git rebase` command

## What is it?

Here I do practice on `git-rebase` command.

At first I have this:

```
c1 <-- c2   master
```

After checkout to the new branch `experiment-2` and a couple of commits I have this:

```
c1 <-- c2   master
        \
        c3 <-- c4   experiment-2
```

It looks like command
```
git rebase --onto experiment-2~2 experiment-2~0 experiment-2
```
deletes commits from git history **completely**!

Some exmplanation about above command:

- `experiment-2~2` means 2 commits back from current state (HEAD I suppose)
- `experiment-2~0` means 0 commits back from HEAD i.e. current index

Whole command means 'get commits starting from 2 commits back up to (excluding ?) current index and delete those commits from history'.

This is not what I want... So now I have this
```
c1 <-- c2   master
        \
        c3 <-- c4 <-- c5   experiment-2
```

After squashing it should be like this:
```
c1 <-- c2   master
        \
        c3456   experiment-2
```
Note new `6` number. Since that above new info is commited then there is 4 commits - from 3 to 6.

`git rebase -i HEAD~n` did the job. Now tree looks:

```
c1 <-- c2 <-- c3456   master
        \
        c3456   experiment-2
```

But I want more. I'd like to keep rebased branch history as it is but squash its commits and apply to public. What I need to do to achive this? 

What tree should be:
```
before:                         | after:
                                |
c1   master                     | c1 <-- c234   master
 \                              |  \
  c2 <-- c3 <-- c4   branch     |   c2 <-- c3 <-- c4   branch
```

## Final rebase

When work is complete I need to copy current branch with command `git branch -c exp-3-preserve-log`. Branch `exp-3-preserve-log` become an archived branch with full history of current branch. Then commits from current branch will be squashed and merged into `master`.

Git command for this might look like this:

```
git branch -c current-preserve-log
git rebase -i commit-hash
... do interactive rebasing
git checkout master
git merge current
```

Also note there is `git merge --squash` command that does almost the same.

## Merge and squash 

Rebase appeared an overkill solution for squashing commits. The same behavior can be achived by this command:
```git
git checkout -b new-branch
# ... do commits
git checkout master
git merge --squash new-branch
git commit -m "your message"
```
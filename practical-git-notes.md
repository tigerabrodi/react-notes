# Git log

`git log` shows the commit history of the current branch.

An example output:

```bash
commit 1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
Author: John Doe <
Date:   Mon Jan 1 00:00:00 2020 +0000

    Commit message

commit 1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
Author: John Doe <
Date:   Mon Jan 1 00:00:00 2020 +0000

    Commit message

...
```

# Git rebase interactive

## Git log

You have a PR with 3 commits, and you want to squash them into one commit.

Let's assume this is what you see run `git log`:

```bash
commit 1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
...
commit 2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
...
commit 3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
...
commit 4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
```

The 4th commit here is the one you want to squash the previous 3 commits into. It's the commit in the main branch that you want to squash the PR commits into.

This will turn the three of your commits into one commit.

## End result

In the end, when running `git log`, you should see:

```bash
commit 1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
...
commit 4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
```

## What to do

To achieve this, you can run `git rebase -i HEAD~4` or `git rebase -i <commit-hash>` where `<commit-hash>` is the commit hash of the commit you want to squash the previous commits into. In our case, it's `4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t`.

## Editing the rebase file

When you run this command, your editor of choice will open:

```bash
pick 1a2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
pick 2b3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
pick 3c4d5e6f7g8h9i0j1k2l3m4n5o6p7q8r9s0t
```

This is a list of the commits you want to squash. You can choose to squash the commit and either keep the commit message or discard it. There is one more step after this where you can edit the commit message.

`pick`, or `p` for short, means you want to keep the commit as it is. So if you've subsequent commits where you specify them with `squash` or `fixup`, they will be squashed into this commit.

`squash`, or `s` for short, means you want to squash the commit into the previous commit but keep the commit message. In simpler words: Squashing the commit means the changes in the commit will be added to the previous commit. So this is more of a "functional" change rather than purely the commit message.

`fixup`, or `f` for short, is similar to `squash`, but it discards the commit message of the commit you're squashing. As mentioned, there is one more step where you can edit the commit message. That's where you would see the commit message of the commit you're squashing if you chose `squash`. But `fixup` discards this.

From my experience, in the final step, `fixup` commit messages will be commented out in case you want to see them, and potentially decide to include them.

## What I like to do

Typically, I like to `pick` the first commit and `squash` the last commits. The ones in the middle I'll either `squash` if I wanna keep the commit message, but most of the time I'll `fixup` them. That's because I still see the commit messages in the final step, so I can decide if I want to include them or not.

So it may look like this:

```bash
p abc1234 Commit 1
f def5678 Commit 2
s ghi9012 Commit 3
```

# Git stash

Git stash is a way to save your changes temporarily. This is useful when you want to switch branches but don't want to commit your changes.

When running `git stash`, your changes (staged and unstaged) will be saved and your working directory will be clean.

Stash is like a local temporary storage.

## Stash changes

To quickly stash your changes: `git stash`.

This won't stash untracked files. To stash untracked files as well: `git stash -u`. This will be needed if you create new files but haven't staged them, aka run `git add`.

## Stash with message

Run `git stash save "my stash message"` to stash your changes with a message.

## Listing stashes

To list your stashes: `git stash list`.

This will display a list of your stashes in the format `stash@{n}`, where `n` is the stash index. The most recent stash has index 0.

## Apply stash

`git stash pop` will apply the most recent stash and remove it from the stash list.

`git stash apply` will apply the most recent stash but keep it in the stash list.

`git stash apply stash@{n}` will apply a specific stash. You can find `n` by running `git stash list` if you have stashed it with a message. Otherwise, it'll be tricky to know which stash you want to apply.

## Show stash

`git stash show stash@{1}` will show the changes in the stash.

`git stash show -p stash@{1}` will show the diff of the changes in the stash.

## Delete stash

To delete a specific stash: `git stash drop stash@{1}`.

To clear all stashes: `git stash clear`.

# Cherry pick

Cherry picking is when you pick a commit from one branch and apply it to another branch. You can also pick multiple commits. It's useful in scenarios where you need to apply specific changes or fixes from one branch to another.

One commit: `git cherry-pick <commit-hash>`.

Multiple commits: `git cherry-pick <commit-hash1> <commit-hash2>`.

## Real world example

Imagine you've a production branch and a development branch. Let's your team does releases every Monday.

On Wednesday, a critical bug is found in production. You need to fix it immediately (can't wait for next Monday) without introducing other changes from the development branch.

You can fix the bug in the development branch.

Then you can switch to the production branch, identify the commit that fixes the bug in the development branch, and cherry-pick that specific commit:

1. `git checkout production`
2. `git cherry-pick <commit-hash>`

## Be careful

Cherry picking and urgent situations shouldn't happen often. If it does, it's a sign that your process or level of quality need to be improved.

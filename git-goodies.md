# Git blame

Git blame is a command that shows you who changed what in a file. It shows the commit hash, the author, the date and the line that was changed.

- Blame an entire file: `git blame <file>`
- A line in a file: `git blame -L <start>,<end> <file>`

# Git log

Git log is a command that shows you the history of a repository.

You can format with options like `--oneline`, `--graph`, `--stat`.

# Git reflog

Git reflog is a command that shows you the history of the HEAD reference.

# Git diff

Git diff is a command that shows you the changes between two commits, two branches, two files, etc.

- Diff both staged and unstaged changes: `git diff HEAD`.
- Compare file: `git diff <file>`.
- Compare file between two commits: `git diff <commit1> <commit2> -- <file>`.
- Compare file between two branches: `git diff <branch1> <branch2> -- <file>`.
- All changes between two branches: `git diff <branch1>..<branch2>`.
- All changes in a branch: `git diff <branch>`.
- All changes between two commits: `git diff <commit1>..<commit2>`.

## Useful options

- `--color-words`: Show the changes in words.
- `--stat`: show summary of changes.
- `-b`: igore changes in blank lines

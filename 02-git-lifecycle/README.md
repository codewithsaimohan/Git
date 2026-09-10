# 02 -- Git Lifecycle

Goal: understand the three trees, move files between them, and read history.

## The three trees

```text
Working tree          Staging area (index)          Repository
(files you edit)  ->  (what the next commit has) -> (committed snapshots)
```

| State | Meaning |
|-------|---------|
| Untracked | File exists on disk, Git does not know it |
| Unmodified | Matches the last commit |
| Modified | Changed in the working tree, not staged |
| Staged | Added to the index, waiting for commit |

```bash
git status
git status -sb
```

Short status letters: `??` untracked, `M` modified, `A` added, `D` deleted, `R` renamed.

## add, commit, and the index

```bash
# Stage one file
git add src/app.js

# Stage a folder
git add src/

# Stage everything in the repo (respects .gitignore)
git add .

# Stage part of a file interactively
git add -p

# Unstage (keep the working-tree change)
git restore --staged src/app.js
```

```bash
# Commit only what is staged
git commit -m "feat: add login form"

# Open an editor for a longer message
git commit

# Stage tracked files and commit in one step (does not add new untracked files)
git commit -am "fix: handle empty password"
```

Amend the last commit only if you have not pushed it, and only if you are fixing your own work:

```bash
git add forgotten.txt
git commit --amend --no-edit
```

`--amend` rewrites the last commit hash. Do not amend commits others already pulled.

## Inspect changes

```bash
# Unstaged changes (working tree vs index)
git diff

# Staged changes (index vs last commit)
git diff --staged

# Working tree vs last commit (both staged and unstaged)
git diff HEAD

# One file
git diff -- src/app.js
```

## Read history

```bash
git log
git log --oneline
git log --oneline --graph --decorate --all
git log -5
git log --author="Ada"
git log --since="2 weeks ago"
git log -- src/app.js
git log -p
git log --stat
```

Useful aliases:

```bash
git config --global alias.lg "log --oneline --graph --decorate --all"
git lg
```

## Show a commit or object

```bash
git show HEAD
git show abc1234
git show HEAD:README.md
```

## Remove and rename

```bash
# Stop tracking and delete the file
git rm notes.tmp

# Stop tracking but keep the file on disk
git rm --cached secrets.env

# Rename (Git records this as delete + add; similarity detection makes it a rename)
git mv old-name.js new-name.js
git commit -m "refactor: rename old-name to new-name"
```

## Lifecycle walkthrough

```bash
git init lifecycle-demo && cd lifecycle-demo
echo "v1" > app.txt
git add app.txt
git commit -m "feat: add app.txt"

echo "v2" >> app.txt
git status
git diff
git add app.txt
git diff --staged
git commit -m "feat: append v2"

git log --oneline
```

## HEAD, commits, and hashes

Every commit has a SHA-1 (or SHA-256) hash. You can shorten it to 7+ unique characters.

`HEAD` is a pointer:

- Usually `HEAD` -> `refs/heads/main` -> commit
- Detached `HEAD` means you checked out a commit, not a branch

```bash
git rev-parse HEAD
git rev-parse main
cat .git/HEAD
```

## Practice

1. Create a file, leave it untracked, then stage it, then commit it. Run `git status` after each step.
2. Change two files; stage only one; commit; confirm the other is still modified.
3. Use `git add -p` to stage a single hunk.
4. Run `git diff` and `git diff --staged` and explain the difference out loud.
5. Write a `git lg` alias and use it.

## Check yourself

- What is the staging area for?
- Does `git commit -a` pick up new files?
- How do you unstage without losing edits?
- What does `HEAD` point at on a normal branch?

Next: `03-branching/README.md`.

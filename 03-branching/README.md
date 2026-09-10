# 03 -- Branching

Goal: create, switch, list, rename, and delete branches without fear.

## What a branch is

A branch is a movable pointer to a commit. Creating a branch is cheap: Git writes a 40-character hash into a small file. It does not copy the project.

```text
main     --> C3
              \
feature       --> C4   (after one commit on feature)
```

`HEAD` names which branch you are on. When you commit, that branch pointer moves forward.

## Create and switch

```bash
# List local branches (* is current)
git branch

# Create a branch (does not switch)
git branch feature/login

# Switch to it
git switch feature/login

# Create and switch in one step
git switch -c feature/login

# Older equivalent (still works)
git checkout -b feature/login
```

Prefer `git switch` and `git restore` over overloaded `git checkout`.

## See all branches

```bash
git branch
git branch -v
git branch -a
git branch --merged
git branch --no-merged
```

## Naming conventions

Pick a pattern and stick to it:

```text
feature/add-checkout
fix/null-pointer-login
chore/upgrade-ci
hotfix/payment-timeout
```

Avoid spaces, keep names lowercase, use `/` or `-`.

## Work on a branch

```bash
git switch -c feature/greet
echo "hello" > greet.txt
git add greet.txt
git commit -m "feat: add greet.txt"

# main is unchanged
git switch main
git log --oneline --graph --all
```

## Rename

```bash
# Rename the current branch
git branch -m new-name

# Rename another branch
git branch -m old-name new-name
```

If the branch was already pushed:

```bash
git push origin -u new-name
git push origin --delete old-name
```

## Delete

```bash
# Delete a fully merged local branch
git branch -d feature/login

# Force delete an unmerged branch (you will lose those commits if they live nowhere else)
git branch -D experiment
```

Deleting a branch deletes the pointer, not the commits, until they become unreachable and garbage-collected.

## Detached HEAD

```bash
git switch --detach HEAD~1
```

You are on a commit, not a branch. New commits can be lost when you switch away.

Fix: create a branch where you are.

```bash
git switch -c rescue-work
```

## Track a remote branch

```bash
git fetch origin
git switch feature/login
# or, if it exists only on the remote:
git switch feature/login
# Git can set upstream automatically for matching names
```

```bash
# Explicit upstream
git switch -c feature/login --track origin/feature/login
```

## Compare branches

```bash
# Commits on feature that are not on main
git log main..feature/login

# Diff of the tips
git diff main...feature/login
```

`A..B` means reachable from B, not from A. `A...B` (three dots) for `git diff` is the merge-base vs B.

## Practice

1. From `main`, create `feature/notes`, add a file, commit.
2. Switch back to `main` and confirm the file is gone from the working tree.
3. List branches with `-v` and with `--merged`.
4. Rename `feature/notes` to `feature/notebook`.
5. Check out an old commit, notice detached HEAD, then create a branch to keep it.

## Check yourself

- Why are Git branches cheap?
- What moves when you commit?
- When is `git branch -D` appropriate?
- How do you recover from detached HEAD?

Next: `04-merging/README.md`.

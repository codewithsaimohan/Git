# 12 -- Troubleshooting

Goal: recognize common Git errors and recover without making things worse.

Rule of thumb: if the commit exists, you can usually get it back (`reflog`). If it was never committed, recovery is luck. Commit or stash before experiments.

## "Please tell me who you are"

```text
*** Please tell me who you are.
```

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Detached HEAD

```text
You are in 'detached HEAD' state
```

You checked out a commit or tag, not a branch.

```bash
# Keep work
git switch -c rescue-branch

# Discard and go home
git switch main
```

## Rejected push (non-fast-forward)

```text
! [rejected]        main -> main (non-fast-forward)
```

The remote has commits you do not have.

```bash
git fetch origin
git log --oneline HEAD..origin/main
git pull
# or
git pull --rebase
git push
```

Do not `--force` on `main`.

## Merge conflicts

See `06-conflicts/README.md`.

```bash
git status
# edit, git add, git commit
# or
git merge --abort
```

## Untracked files would be overwritten

Git refuses to switch branches if the other branch has a tracked file that would clobber your untracked file of the same name.

```bash
git stash push -u -m "park untracked"
git switch other-branch
```

Or move/rename the untracked file yourself.

## `git pull` with diverged branches

```text
Need to specify how to reconcile divergent branches
```

```bash
# One-time
git pull --no-rebase
# or
git pull --rebase

# Set a default
git config --global pull.rebase false
```

## Accidentally committed to main

If not pushed:

```bash
git switch -c feature/oops
git switch main
git reset --hard origin/main
```

If pushed: open a PR from that branch; revert on `main` if the commit must disappear from mainline.

## Accidentally committed a secret

1. Treat the secret as leaked. Rotate it (API keys, passwords).
2. Removing it from Git history (`filter-repo`, BFG) is extra; rotation is mandatory.
3. Add the file to `.gitignore` and stop tracking:

```bash
git rm --cached secrets.env
```

## `Permission denied (publickey)`

SSH key is missing or not loaded.

```bash
ssh -T git@github.com
```

Add the public key to GitHub. Use the matching private key locally. HTTPS + token is a fallback.

## Large file / LFS

GitHub rejects files over 100 MB.

- Remove the file from history before pushing, or
- Use Git LFS for binaries your team really must version.

```bash
git rm --cached huge.bin
```

If already committed, history rewrite is required before a successful push of that commit.

## Undo last commit, keep files

```bash
git reset --soft HEAD~1
```

## Undo last commit, it is already on origin

```bash
git revert HEAD
git push
```

## Clean a dirty tree (destructive)

```bash
git status
git restore .
# untracked:
# inspect first with git status; only then decide
git stash push -u
```

Avoid `git clean -fd` until you know which untracked files it would remove. This course does not run destructive clean commands for you.

## Find who changed a line

```bash
git blame src/app.js
git log -p -- src/app.js
```

## Bisect a bug

```bash
git bisect start
git bisect bad
git bisect good v1.0.0
# test, then:
git bisect good
# or
git bisect bad
git bisect reset
```

Git checkouts midpoints until it names the first bad commit.

## Reflog recovery

```bash
git reflog
git switch -c recovered HEAD@{4}
```

## Practice

1. Cause detached HEAD on purpose and recover with a new branch.
2. Simulate a rejected push: commit on two clones of the same repo, push one, pull on the other.
3. Commit a dummy `.env`, `git rm --cached` it, add `.gitignore`.
4. Reset a commit, find it in reflog, cherry-pick it back.

## Check yourself

- When is force-push the wrong fix for a rejected push?
- What must you do if a secret was committed?
- How do you recover a "lost" commit locally?
- Why does Git refuse to overwrite untracked files?

Next: `interview-scenarios/README.md`.

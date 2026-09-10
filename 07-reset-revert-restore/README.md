# 07 -- Reset, Revert, Restore

Goal: undo work on purpose. Know which command rewrites history, which one adds a new commit, and which one only touches files.

## Pick the right undo

| You want to... | Command |
|----------------|---------|
| Throw away unstaged edits in a file | `git restore <file>` |
| Unstage a file, keep edits | `git restore --staged <file>` |
| Move the branch pointer (rewrite) | `git reset` |
| Undo a commit that is already shared | `git revert` |
| Recover a lost commit | `git reflog` then `reset` or `cherry-pick` |

Older Git used `git checkout` for restore. Prefer `git restore`.

## restore -- working tree and index

```bash
# Discard working-tree changes for one file (back to index)
git restore app.js

# Discard all working-tree changes (tracked files only)
git restore .

# Unstage, keep the file contents
git restore --staged app.js

# Unstage and discard, back to HEAD
git restore --source=HEAD --staged --worktree app.js
```

`restore` does not change commit history.

## reset -- move HEAD (and maybe more)

`git reset` moves the current branch to a commit. The mode decides what happens to the index and working tree.

| Mode | HEAD | Index | Working tree | Typical use |
|------|------|-------|--------------|-------------|
| `--soft` | moves | kept as staged | unchanged | redo the last commit message / split commits |
| `--mixed` (default) | moves | matches commit | unchanged | uncommit but keep edits |
| `--hard` | moves | matches commit | matches commit | destroy uncommitted work |

```bash
# Uncommit last commit, keep everything staged
git reset --soft HEAD~1

# Uncommit last commit, keep edits unstaged
git reset HEAD~1

# Delete last commit and all its file changes (dangerous)
git reset --hard HEAD~1
```

`HEAD~1` is the parent of HEAD. `HEAD~3` is three commits back.

Never `reset --hard` if you still need the working-tree changes. They are not in a commit, so reflog will not save them.

Never `reset --hard` (or `--soft` / `--mixed` on a published commit) if others already pulled that commit. Use `revert` instead.

## revert -- undo by adding a commit

`git revert` creates a new commit that applies the opposite patch. History stays honest and shared-safe.

```bash
# Revert the latest commit
git revert HEAD

# Revert a specific commit
git revert abc1234

# Revert a merge commit (mainline parent 1 is usually main)
git revert -m 1 <merge-commit>
```

Good for: hotfixes already on `main`, public history, audit trails.

## reflog -- your safety net

Git remembers where HEAD has been locally for a few weeks.

```bash
git reflog
git reset --hard HEAD@{2}
```

If you "lost" a commit after reset, find it in reflog and reset or cherry-pick it back.

This is local. It does not recover work that was never committed and then destroyed with `--hard`.

## recover a deleted file from history

```bash
git log -- path/to/file
git restore --source=<commit> -- path/to/file
```

## walkthrough

```bash
git init undo-demo && cd undo-demo
echo "v1" > app.txt && git add app.txt && git commit -m "v1"
echo "v2" >> app.txt && git commit -am "v2"
echo "v3" >> app.txt && git commit -am "v3"

# Oops, v3 was wrong but not pushed
git reset --hard HEAD~1
git log --oneline

# Oops, we needed v3 after all
git reflog
git reset --hard HEAD@{1}

# v3 is on shared main -- do not reset; revert
git revert HEAD
```

## Practice

1. Make three commits. `reset --soft HEAD~1` and inspect `git status`.
2. Repeat with `--mixed` and `--hard` in separate experiments.
3. Push is simulated by "pretend this is shared": use `git revert` instead of reset.
4. `reset --hard`, then restore the commit from `git reflog`.
5. Edit a file, `git restore` it, confirm the edit is gone.

## Check yourself

- Which reset mode leaves changes staged?
- Why is `revert` safer on `main`?
- What will reflog not recover?
- How do you unstage without losing work?

Next: `08-stash/README.md`.

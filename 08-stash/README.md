# 08 -- Stash

Goal: park uncommitted work, switch context, and bring the work back.

## When to stash

You have dirty files, but you need a clean tree to switch branch, pull, or bisect. You are not ready to commit.

Stash saves the working tree (and optionally untracked files) onto a stack, then resets the working tree to HEAD.

## Basic flow

```bash
# Save tracked changes
git stash push -m "wip: login validation"

# Short form
git stash

# List
git stash list

# Re-apply and keep the stash
git stash apply

# Re-apply and drop the stash
git stash pop
```

`apply` is safer while you learn: if the apply is messy, the stash is still there.

## Include untracked files

By default stash ignores untracked files.

```bash
# Also stash untracked files
git stash push -u -m "wip: including new files"

# Include ignored files too (rare, noisy)
git stash push -a
```

## Partial stash

```bash
# Interactive hunks
git stash push -p -m "wip: only the parser hunk"
```

## Inspect a stash

```bash
git stash list
git stash show
git stash show -p
git stash show -p stash@{1}
```

## Apply onto another branch

```bash
git stash push -m "wip"
git switch other-branch
git stash pop
```

If the other branch diverged in the same lines, you get a conflict. Resolve it like any other conflict, then `git add`. A conflicted `stash pop` may still leave the stash; check `git stash list`.

## Drop and clear

```bash
# Remove one
git stash drop stash@{0}

# Remove all stashes
git stash clear
```

`clear` is hard to undo. Prefer dropping one entry.

## Stash as a commit

Stashes are commits under `refs/stash`. You can turn one into a branch:

```bash
git stash branch wip-login stash@{0}
```

This creates a branch, checks it out, and applies the stash -- useful when the stash is too big to pop blindly.

## Stash vs commit

| | Stash | Commit on a WIP branch |
|--|-------|------------------------|
| Visible to others | no | yes, if pushed |
| Easy to lose | yes (`clear`, laptop wipe) | no, if pushed |
| Message quality | often poor | can be a real WIP commit |
| Best for | minutes to hours | anything you might need tomorrow |

If work matters, make a WIP commit on a branch instead of leaving a stash for days.

## Practice

1. Dirty a tracked file, stash, switch branch, pop.
2. Create an untracked file, stash without `-u`, confirm it remains. Then stash with `-u`.
3. `stash apply` twice and see duplicated changes / conflicts.
4. Convert a stash into a branch with `git stash branch`.

## Check yourself

- Does `git stash` include new files by default?
- Difference between `apply` and `pop`?
- Why is a WIP branch often better than a week-old stash?
- How do you include untracked files?

Next: `09-cherry-pick/README.md`.

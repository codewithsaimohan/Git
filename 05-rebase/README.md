# 05 -- Rebase

Goal: replay commits onto another base, clean up local history, and know when rebase is dangerous.

## Merge vs rebase

Merge creates a join commit and keeps both histories.

Rebase takes your commits, peels them off, and replays them one by one on top of another commit. History looks linear.

```text
Before:
          C3  (main)
         /
C1 --> C2
         \
          C4 --> C5  (feature)

After git rebase main (on feature):

C1 --> C2 --> C3  (main)
               \
                C4' --> C5'  (feature)
```

`C4'` and `C5'` are new commits with new hashes. The originals remain until garbage collection if nothing points at them.

```bash
git switch feature
git rebase main
```

Then merge (usually fast-forward) into `main`:

```bash
git switch main
git merge feature
```

## When rebase helps

- Update a feature branch with the latest `main` before a pull request.
- Clean up local WIP commits before anyone else sees them.
- Keep `git log --graph` easy to read.

## Golden rule

Do not rebase commits that have been pushed and shared.

Rebase rewrites hashes. Teammates who built on the old hashes will have a diverged history. Recovery then needs force-push and coordination.

Safe: rebase local-only commits.
Unsafe: rebase `main` on a shared repo, or rebase a branch others already pulled.

## Interactive rebase

Rewrite a range of commits you still own.

```bash
# Rebase the last 4 commits
git rebase -i HEAD~4
```

Editor actions:

| Command | Effect |
|---------|--------|
| `pick` | Keep the commit |
| `reword` | Keep, edit the message |
| `edit` | Stop to amend the commit |
| `squash` | Fold into the previous commit, keep both messages |
| `fixup` | Fold into the previous commit, drop this message |
| `drop` | Remove the commit |
| `reword` / reorder lines | Change order |

```bash
# Continue after a stop or a conflict
git rebase --continue

# Skip this commit during replay
git rebase --skip

# Give up and restore the branch to pre-rebase
git rebase --abort
```

## Incoming vs onto

```bash
# Replay current branch's unique commits onto main
git rebase main

# Same idea, explicit
git rebase --onto main start-point feature
```

`--onto` is for advanced slicing (for example, move a chain from one parent to another).

## Pull with rebase

```bash
# Fetch and rebase your local commits on top of the remote branch
git pull --rebase

# Make it the default for this branch
git config pull.rebase true
```

This avoids extra "merge branch origin/main" commits when you are slightly behind.

## Conflicts during rebase

Git applies commits one at a time. A conflict pauses the rebase.

```bash
# Fix files, then
git add <resolved-files>
git rebase --continue
```

See `06-conflicts/README.md` for the conflict markers.

## Practice

1. Create `main` with 2 commits and `feature` with 2 more. Rebase `feature` onto `main`. Compare hashes before and after.
2. Make 3 messy commits on a branch. Use `git rebase -i HEAD~3` to squash them into one.
3. Start a rebase, cause a conflict, then `--abort`.
4. Explain out loud why you must not rebase shared `main`.

## Check yourself

- What happens to commit hashes after a rebase?
- What is the golden rule of rebase?
- What do `squash` and `fixup` do in `rebase -i`?
- How do you cancel a rebase in progress?

Next: `06-conflicts/README.md`.

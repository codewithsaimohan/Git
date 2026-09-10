# 06 -- Conflicts

Goal: understand why conflicts happen, resolve them, and abort cleanly.

## Why conflicts exist

Git can auto-merge when the same file was not edited in overlapping ways. A conflict means both sides changed the same region (or one side edited a file the other deleted). Git will not guess.

Conflicts are normal. They are not a broken repository.

Typical sources:

- Two branches edited the same lines.
- You rebased onto a branch that touched the same code.
- You cherry-picked a commit that does not apply cleanly.
- One side renamed or deleted a file the other modified.

## Recreate a conflict

```bash
git init conflict-demo && cd conflict-demo
echo "hello world" > greet.txt
git add greet.txt && git commit -m "feat: greet"

git switch -c feature
echo "hello feature" > greet.txt
git commit -am "feat: feature greeting"

git switch main
echo "hello main" > greet.txt
git commit -am "feat: main greeting"

git merge feature
```

Git stops and writes markers into the file.

## Conflict markers

```text
<<<<<<< HEAD
hello main
=======
hello feature
>>>>>>> feature
```

| Marker | Meaning |
|--------|---------|
| `<<<<<<< HEAD` | Start of your current branch version |
| `=======` | Divider |
| `>>>>>>> feature` | Incoming branch version |

During rebase, "ours" and "theirs" can feel swapped: `HEAD` is the branch you are rebasing onto.

## Resolve step by step

1. `git status` -- see unmerged paths.
2. Open each conflicted file.
3. Edit until the file is the version you want. Delete all `<<<<<<<`, `=======`, `>>>>>>>` markers.
4. `git add` each resolved file.
5. Finish the operation:

```bash
# If you were merging
git commit

# If you were rebasing
git rebase --continue
```

Keep the whole file from one side:

```bash
# Keep our version (current branch, for a merge)
git checkout --ours greet.txt
git add greet.txt

# Keep their version (incoming)
git checkout --theirs greet.txt
git add greet.txt
```

Prefer editing by hand for code. Blind `--ours` / `--theirs` is easy to get wrong during rebase.

## Tools

```bash
git mergetool
git diff
git diff --name-only --diff-filter=U
```

Many editors highlight conflict hunks (VS Code, JetBrains, Vim fugitive).

## Abort

```bash
git merge --abort
git rebase --abort
git cherry-pick --abort
```

Use abort when you are unsure. The branch returns to the pre-operation state (working tree must allow it).

## Binary files

Git cannot merge binaries as text. You must pick one copy:

```bash
git checkout --ours assets/logo.png
git add assets/logo.png
```

## After a messy resolve

```bash
# Confirm no markers remain
git grep -n '<<<<<<<' || true

git status
git diff --cached
```

Then run tests. A syntactically resolved file can still be logically wrong.

## Practice

1. Recreate the `greet.txt` conflict above and resolve it by combining both ideas: `hello main and feature`.
2. Abort a merge after the conflict appears, then retry.
3. Cause a conflict with rebase instead of merge. Notice which side is `HEAD`.
4. Search the repo for leftover conflict markers before you commit.

## Check yourself

- What do the three conflict markers mean?
- Why might `--ours` during rebase not be "my feature"?
- How do you finish vs abort a conflicted merge?
- Why should you run tests after resolving?

Next: `07-reset-revert-restore/README.md`.

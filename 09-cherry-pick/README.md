# 09 -- Cherry-pick

Goal: copy one or more existing commits onto your current branch.

## What cherry-pick does

`git cherry-pick <commit>` takes the patch of that commit and applies it as a new commit on HEAD. The original commit stays where it was. The new commit has a new hash.

```text
main:     A -- B -- C
hotfix:   A -- B -- D     (D is the fix)

git switch main
git cherry-pick D

main:     A -- B -- C -- D'
```

`D'` has the same change as `D`, not the same hash.

## When to use it

- A bug fix landed on a release branch and `main` needs the same fix.
- You committed on the wrong branch (if the commit is not pushed yet, reset may be cleaner).
- You want one commit from a messy feature branch, not the whole branch.

Do not cherry-pick as a substitute for merge when you need the entire branch.

## Commands

```bash
# One commit
git cherry-pick abc1234

# A range (does not include the start commit)
git cherry-pick first^..last

# Multiple SHAs
git cherry-pick abc1234 def5678

# Apply the change but do not commit
git cherry-pick --no-commit abc1234

# Abort
git cherry-pick --abort

# Continue after resolving conflicts
git cherry-pick --continue
```

## Conflicts

Same workflow as merge/rebase:

```bash
# Fix files
git add <files>
git cherry-pick --continue
```

If the change is already present, Git may say the pick is empty:

```bash
git cherry-pick --skip
```

## Duplicate commits

Cherry-pick copies changes, not identity. Later merges can see "the same" patch twice and confuse you with extra conflicts.

If two long-lived branches both need a fix, consider:

- merging a dedicated hotfix branch into both, or
- cherry-picking once and documenting the original SHA in the new message.

```bash
git cherry-pick -x abc1234
```

`-x` adds `(cherry picked from commit abc1234)` to the message. Useful on maintained release branches.

## Wrong-branch recovery

```bash
# You committed on main by mistake
git log -1 --oneline
git switch feature
git cherry-pick <that-sha>
git switch main
git reset --hard HEAD~1
```

Only reset `main` if the bad commit was not pushed.

## Practice

1. Make commit `D` on a side branch. Cherry-pick it onto `main`. Compare `git show` of both SHAs.
2. Cherry-pick with `--no-commit` and inspect the index.
3. Cherry-pick a commit that conflicts; abort; retry and continue.
4. Use `-x` and read the resulting message.

## Check yourself

- Does cherry-pick move a commit or copy it?
- Why do hashes change?
- When is merge better than cherry-pick?
- What does `-x` record?

Next: `10-tags/README.md`.

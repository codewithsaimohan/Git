# 13 -- Bisect

Goal: use `git bisect` to find the commit that introduced a bug by binary search on history.

## The concept

You know the bug exists now (`bad`). You know it did not exist at some older commit or tag (`good`). Walking every commit in between is slow. Bisect cuts the range in half each time.

```text
good                          bad
 v                             v
 A -- B -- C -- D -- E -- F -- G
              ^
              Git checks out the midpoint (say D)

If D is good, the bug is in E..G.
If D is bad, the bug is in B..D.
```

Each answer (`good` or `bad`) halves the remaining commits. About `log2(n)` tests find the first bad commit in a range of `n`.

1000 commits ~ 10 tests. That is the point.

## When to use it

- A regression appeared, and `git log` / `git blame` is too noisy.
- Tests passed on last week's tag and fail on `main`.
- You did not write the breaking change and need a SHA to revert or discuss.

When not to bother:

- You already know the file and the likely commit (`git blame`, `git log -p -- path`).
- The bug is environmental (config, data, dependency not in Git).

## Manual bisect

Start from a clean working tree on the branch that has the bug.

```bash
git switch main
git status

git bisect start
git bisect bad HEAD
git bisect good v1.0.0
```

Git checks out a midpoint. You are in detached HEAD. Test the project:

- bug still there -> `git bisect bad`
- bug gone -> `git bisect good`
- cannot tell (build broken for another reason) -> `git bisect skip`

Repeat until Git prints the first bad commit:

```text
<sha> is the first bad commit
```

Then leave bisect and go back to your branch:

```bash
git bisect reset
```

Always `reset` when finished. Until you do, HEAD stays detached on some old commit.

## End-to-end example

```bash
git init bisect-demo && cd bisect-demo

echo "ok" > app.txt
git add app.txt && git commit -m "feat: start"

echo "ok 2" > app.txt
git commit -am "feat: still ok"

echo "BUG" > app.txt
git commit -am "fix: oops this is the bug"

echo "later" >> other.txt
git add other.txt && git commit -m "chore: unrelated"

git bisect start
git bisect bad HEAD
git bisect good HEAD~3

# Git jumps to a midpoint. Inspect app.txt.
# If it says BUG:
git bisect bad
# If it says ok:
git bisect good

git bisect reset
```

In a real app you run the test suite or reproduce the bug in the UI, not just read a file.

## Visualize the range

```bash
git bisect start
git bisect bad
git bisect good v1.0.0
git bisect visualize
# or
git bisect view
```

Opens `gitk` (or `git log`) on the remaining range.

```bash
git bisect log
```

Shows the path of good/bad marks so far. You can save it and replay:

```bash
git bisect log > bisect-log.txt
git bisect reset
git bisect replay bisect-log.txt
```

## skip, abort, terms

```bash
# This commit cannot be tested (does not build, missing fixture)
git bisect skip

# Stop and return to the starting branch
git bisect reset

# Give up without waiting for an answer
git bisect reset
```

If many commits are skipped, Git may not name a single first bad commit; it reports a range.

Default words are `good` / `bad`. For other hunts you can rename them:

```bash
git bisect start --term-new broken --term-old working
git bisect broken
git bisect working v1.0.0
```

Same algorithm; clearer when you are not looking at a "bug".

## Automated bisect (`git bisect run`)

If you can write a command that exits:

- `0` -- current commit is good
- `1-127` (except 125) -- current commit is bad
- `125` -- skip this commit
- `255` -- abort the bisect

```bash
git bisect start HEAD v1.0.0
git bisect run npm test
```

Or a small script:

```bash
git bisect start HEAD v1.0.0
git bisect run ./scripts/check-regression.sh
```

```bash
#!/usr/bin/env bash
set -euo pipefail
npm test
```

Git loops until it finds the first bad commit, then you still run:

```bash
git bisect reset
```

Make the script test only the regression. A flaky test will pin the wrong commit.

## Limit the search to a path

If you are sure the bug lives under one directory:

```bash
git bisect start HEAD v1.0.0 -- src/payments
```

Git only considers commits that touched that path. Faster, but wrong if the real change was elsewhere (config, dependency lockfile).

## After you find the commit

```bash
git show <sha>
git log -1 --stat <sha>
```

Then:

- revert it if it is isolated (`git revert <sha>`)
- cherry-pick a follow-up fix
- talk to the author with evidence, not blame

`bisect` names the commit where the test first failed. That commit might only expose an older latent bug. Read the diff.

## Bisect vs blame vs log

| Tool | Best at |
|------|---------|
| `git blame` | Who last touched this line |
| `git log -p -- path` | History of one file |
| `git bisect` | Which commit made a behavior change, even across many files |

Use blame when you have a line. Use bisect when you have a symptom.

## Common mistakes

- Dirty working tree: commit or stash first.
- Marking `good`/`bad` backwards: the current broken tip is `bad`.
- Forgetting `git bisect reset` and committing on detached HEAD.
- Range includes a merge-only history you cannot build; `skip` those.
- Testing the wrong thing (environment, not the commit).

## Practice

1. Create a 8-commit repo. Introduce a bug in commit 5. Use manual bisect to find it. Count how many tests you needed.
2. Repeat with `git bisect run` and a script that greps for `BUG` in `app.txt`.
3. `skip` one midpoint on purpose, then finish.
4. Forget `reset` once, notice detached HEAD, then `git bisect reset`.

## Check yourself

- Why is bisect faster than walking commits one by one?
- What do `good`, `bad`, and `skip` mean?
- What exit codes does `git bisect run` expect?
- Why must you `git bisect reset` when you are done?
- When is `git blame` a better first tool?

Next: `interview-scenarios/README.md`.

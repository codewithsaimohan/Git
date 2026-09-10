# 11 -- GitHub Workflow

Goal: work with remotes, branches, pull requests, and reviews the way most teams do.

Git is the tool. GitHub (or GitLab/Bitbucket) is where teams share branches and review code. The Git commands are the same.

## Remotes

A remote is a nickname for another copy of the repo.

```bash
git remote -v
git remote add origin https://github.com/org/app.git
git remote add origin git@github.com:org/app.git
```

`origin` is a convention, not a special Git feature.

```bash
git fetch origin
git pull origin main
git push -u origin main
```

- `fetch` -- download commits, do not merge
- `pull` -- fetch + merge (or rebase, if configured)
- `push` -- upload your commits

## Clone vs fork vs branch

| | Clone | Fork | Feature branch |
|--|-------|------|----------------|
| What | Your local copy | Your copy on GitHub of someone else's repo | A line of work inside a repo |
| Use | Daily work | Open source, or no write access to upstream | Almost all team work |

Typical team (you have write access):

```text
origin/main  <-  your machine  <-  feature/login  ->  pull request  ->  main
```

Typical open source:

```text
upstream/main  <-  your fork  <-  your machine  <-  PR to upstream
```

```bash
git remote add upstream https://github.com/original/app.git
git fetch upstream
git switch main
git merge upstream/main
```

## Feature-branch workflow

```bash
git switch main
git pull

git switch -c feature/add-search
# ... commits ...
git push -u origin feature/add-search
```

Open a Pull Request (PR) on GitHub: `feature/add-search` into `main`.

After review and CI:

- squash merge, merge commit, or rebase-and-merge (team policy)
- delete the remote feature branch
- locally:

```bash
git switch main
git pull
git branch -d feature/add-search
git fetch --prune
```

`git fetch --prune` drops stale remote-tracking branches.

## Upstream tracking

```bash
git push -u origin feature/add-search
git status
# "Your branch is ahead of origin/feature/add-search by 1 commit"
```

After `-u`, `git push` and `git pull` know which remote branch to use.

## Keep a feature branch current

```bash
git switch main
git pull
git switch feature/add-search
git merge main
# or
git rebase main
```

Rebase only if you are allowed to rewrite this branch (solo feature branch: usually yes). If others push to the same feature branch, merge is safer.

## Force-push (careful)

After a local rebase of a feature branch you own:

```bash
git push --force-with-lease
```

`--force-with-lease` refuses to overwrite if the remote moved in a way you have not seen. Still never force-push `main`.

## PR hygiene

- Small diffs review faster.
- One concern per PR.
- Description: why, how to test, screenshots if UI.
- Do not commit secrets. Rotate if you did.
- Wait for CI.

## SSH vs HTTPS

```bash
# Test SSH
ssh -T git@github.com
```

HTTPS uses a credential helper or token. SSH uses keys. Teams often prefer SSH for daily push.

## GitHub-specific extras (optional)

- Branch protection: no direct push to `main`, required reviews, required checks.
- CODEOWNERS for automatic reviewers.
- Draft PRs while you are still iterating.
- `gh pr create` if GitHub CLI is installed.

## Practice

1. Create a local repo, add a fake `origin` (or a real empty GitHub repo if you have one).
2. Push `main`, create `feature/readme`, push, imagine opening a PR.
3. Run `git fetch --prune` after deleting a remote branch.
4. Practice `--force-with-lease` only on a throwaway feature branch.

## Check yourself

- fetch vs pull vs push?
- What does `-u` do?
- When is force-push acceptable?
- Fork vs branch?

Next: `12-troubleshooting/README.md`.

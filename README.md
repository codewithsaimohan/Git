# Git & GitHub — DevOps / Multi-Cloud / SRE Learning Path

> A practical, day-wise Git & GitHub roadmap covering the Git lifecycle, daily commands, branching, collaboration, conflicts, recovery, releases, and interview-ready workflows.

## 🎯 Goal

Build strong Git fundamentals and progress to real-world DevOps/SRE usage:

```text
Working Directory
       ↓ git add
Staging Area
       ↓ git commit
Local Repository
       ↓ git push
Remote Repository (GitHub)
```

Git is the distributed version-control tool; GitHub is a remote collaboration and hosting platform. Git can be used independently of GitHub.

---

# 📅 Day-Wise Learning Plan

## Day 01 — Git Fundamentals

- What is Git?
- Version Control System (VCS)
- Distributed VCS
- Git vs GitHub
- Git vs SVN
- Why Git was created
- Git snapshots, commits and history
- `.git` directory
- Git objects: blob, tree, commit
- SHA-1 and data integrity

### Commands

```bash
git --version
git help
git help <command>
```

---

## Day 02 — Installation & Configuration

### Install

```bash
# RHEL/CentOS/Amazon Linux
sudo yum install git -y

# Ubuntu/Debian
sudo apt-get install git -y
```

### Configure identity

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

git config --global user.name
git config --global user.email
git config --list
```

### Configuration levels

```text
--system  → all users
--global  → current user
--local   → current repository
```

Most specific configuration wins.

---

## Day 03 — Git Repository & Lifecycle

Understand the four zones:

```text
Working Directory
      │
      │ git add
      ▼
Staging Area
      │
      │ git commit
      ▼
Local Repository
      │
      │ git push
      ▼
Remote Repository
```

### Practice

```bash
mkdir git-lab
cd git-lab

git init

touch file1.txt
git status

git add file1.txt
git status

git commit -m "Add file1"

git log --oneline
```

---

## Day 04 — Status, Add, Commit & Diff

### Core commands

```bash
git status
git add <file>
git add .
git add -p

git commit -m "Meaningful message"
git commit -am "Update tracked files"
```

> `git commit -am` works for already tracked files; it does not stage brand-new files.

### Compare changes

```bash
git diff
git diff --staged
git diff HEAD
git diff --name-only
git diff --stat

git diff <commit1> <commit2>
git diff <branch1> <branch2>
```

---

## Day 05 — GitHub & Remote Repositories

### Clone

```bash
git clone <repository-url>
```

### Remote inspection

```bash
git remote -v
git remote
git remote show origin
```

### Add/change remote

```bash
git remote add origin <repository-url>
git remote set-url origin <repository-url>
```

### Push & pull

```bash
git push
git pull
git fetch
```

Key difference:

```text
fetch = download remote changes, don't merge
pull  = fetch + integrate changes
```

---

## Day 06 — SSH Authentication

Generate an SSH key:

```bash
ssh-keygen
```

Add the public key to GitHub SSH keys.

Test:

```bash
ssh -T git@github.com
```

Use SSH remote:

```bash
git remote set-url origin git@github.com:<user>/<repo>.git
```

Verify:

```bash
git remote -v
```

> Never commit or expose private keys, passwords, or access tokens.

---

## Day 07 — Branching

Branches provide isolated lines of development.

### Commands

```bash
git branch
git branch <branch>
git checkout <branch>

# Modern alternatives
git switch <branch>
git switch -c <branch>

# Create + switch
git checkout -b <branch>

# Rename
git branch -m old-name new-name

# Delete
git branch -d <branch>
git branch -D <branch>
```

### Remote branch

```bash
git push -u origin <branch>
git push origin --delete <branch>
```

Recommended naming:

```text
feature/login
bugfix/null-pointer
hotfix/payment-crash
release/v2.0
```

---

## Day 08 — Real-World Feature Branch Workflow

```bash
git checkout main
git pull

git checkout -b feature/login

# develop
git status
git add .
git commit -m "Add login validation"

git push -u origin feature/login
```

Then:

```text
Feature Branch
      ↓
Pull Request
      ↓
Code Review
      ↓
CI/CD Checks
      ↓
Approval
      ↓
Merge
      ↓
Main
```

For production repositories, avoid direct commits to `main`; use feature branches and Pull Requests.

---

## Day 09 — Merge & Rebase

### Merge

```bash
git checkout main
git pull
git merge feature/login
```

Merge combines branch histories.

### Rebase

```bash
git checkout feature/login
git rebase main
```

Rebase moves/replays your commits onto a new base and can create a cleaner linear history.

### Important rule

```text
Shared/public commits → avoid rebasing them
Private/local work    → rebase can be useful
```

### Pull strategies

```bash
git pull --no-rebase
git pull --rebase
```

---

## Day 10 — Merge Conflicts

Conflicts can occur when branches modify the same lines or when merge/rebase/cherry-pick operations cannot be resolved automatically.

Git may show:

```text
<<<<<<< HEAD
your changes
=======
incoming changes
>>>>>>> feature/login
```

### Resolve

```bash
git status

# edit the conflicting file
# remove conflict markers
# keep/combine the correct code

git add <file>
git commit
```

For rebase:

```bash
git add <file>
git rebase --continue
```

For cherry-pick:

```bash
git add <file>
git cherry-pick --continue
```

Abort when required:

```bash
git merge --abort
git rebase --abort
git cherry-pick --abort
```

---

## Day 11 — Undoing Changes

### Working Directory

```bash
git restore <file>
```

Older equivalent:

```bash
git checkout -- <file>
```

### Unstage

```bash
git restore --staged <file>
git restore --staged .
```

### Reset

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

Meaning:

```text
soft  → keep changes staged
mixed → keep changes in working directory
hard  → discard changes
```

⚠️ Be very careful with `reset --hard`, especially on production/shared history.

### Safe undo on shared branches

```bash
git revert <commit-hash>
```

`revert` creates a new commit that reverses an earlier commit without rewriting shared history.

---

## Day 12 — Stash & Temporary Work

Useful when you have unfinished work but need to switch branches.

```bash
git stash
git stash push -m "WIP: payment changes"

git stash list
git stash show
git stash apply
git stash pop

git stash drop stash@{0}
git stash clear
```

Create a branch from a stash:

```bash
git stash branch <new-branch>
```

### Typical scenario

```bash
git stash push -m "WIP"
git checkout main
git pull

# fix urgent issue

git checkout feature/login
git stash pop
```

---

## Day 13 — Cherry-Pick

Apply a specific commit instead of merging an entire branch.

```bash
git log <branch> --oneline

git checkout main
git cherry-pick <commit-hash>
git push
```

Multiple commits:

```bash
git cherry-pick <hash1> <hash2>
git cherry-pick <start-hash>^..<end-hash>
```

Apply without immediately creating a commit:

```bash
git cherry-pick --no-commit <commit-hash>
```

### Typical SRE/DevOps use

```text
dev branch
   │
   └── critical hotfix commit
             ↓
         cherry-pick
             ↓
           main
```

Useful for hotfixes and backporting a specific fix.

---

## Day 14 — Logs, Inspection & Recovery

### History

```bash
git log
git log --oneline
git log --oneline --graph --all
git log --author="name"
```

### Inspect commits

```bash
git show <commit-hash>
git blame <file>
```

### Reflog

```bash
git reflog
git reflog show <branch>
git reflog --date=iso
```

Recover a lost commit:

```bash
git checkout -b recovered <old-hash>
```

or, when appropriate:

```bash
git reset --hard <old-hash>
```

> `reflog` is local and is one of Git's most useful recovery mechanisms.

---

## Day 15 — Tags & Releases

Tags mark important commits, commonly releases.

```bash
git tag
git tag v1.0.0

git tag -a v1.0.0 -m "Release v1.0.0"

git show v1.0.0

git push origin v1.0.0
git push origin --tags

git tag -d v1.0.0
git push origin --delete v1.0.0

git describe --tags
```

### Semantic Versioning

```text
MAJOR.MINOR.PATCH

2.0.0 → breaking change
2.1.0 → backward-compatible feature
2.1.1 → backward-compatible bug fix
```

---

# 🧰 Left-Over / Must-Know Commands

## Remote Management

```bash
git remote -v
git remote show origin
git remote add upstream <url>
git remote rename origin upstream
git remote set-url origin <url>
git fetch origin
git fetch --all
```

## Branch Inspection

```bash
git branch
git branch -a
git branch -r
git branch -vv
git show-branch
```

## Comparison

```bash
git diff
git diff --staged
git diff HEAD
git diff main origin/main
git diff branch1 branch2
git diff <hash1> <hash2>
git diff --name-only
git diff --stat
```

## File & Repository Cleanup

```bash
git clean -n
git clean -fd
```

> Always preview with `git clean -n` before deleting untracked files.

## Repository Inspection

```bash
git status
git log --oneline --graph --all
git show <hash>
git blame <file>
git reflog
```

## Tags

```bash
git tag
git tag -a <tag> -m "message"
git push origin <tag>
git push origin --tags
git tag -d <tag>
git push origin --delete <tag>
git show <tag>
git describe --tags
```

---

# 🚨 Common Git Problems & Quick Fixes

| Problem | Typical approach |
|---|---|
| Push rejected / non-fast-forward | `git pull` or fetch + integrate, resolve conflicts, then push |
| Merge conflict | `git status` → edit → `git add` → `git commit` |
| Rebase conflict | resolve → `git add` → `git rebase --continue` |
| Wrong file staged | `git restore --staged <file>` |
| Wrong local commit | choose `reset` mode carefully |
| Shared commit needs undo | `git revert <hash>` |
| Lost commit | `git reflog` |
| Temporary unfinished work | `git stash` |
| Need one commit from another branch | `git cherry-pick <hash>` |
| Can't find remote URL | `git remote -v` |
| SSH authentication problem | `ssh -T git@github.com` |
| Local and remote differ | `git fetch` + inspect `git diff` |

---

# 🔐 `.gitignore`

Use `.gitignore` to exclude files that should not be committed.

Typical examples:

```gitignore
.env
*.log
*.tmp
node_modules/
.terraform/
*.tfstate
*.tfstate.*
.idea/
.vscode/
```

Never commit:

```text
Passwords
Private keys
API tokens
Cloud credentials
Secrets
Environment files containing credentials
```

---

# 🌳 GitHub Collaboration

Understand:

- Repository
- Fork
- Clone
- Branch
- Pull Request
- Review
- Approval
- Merge
- Issues
- GitHub Actions
- Branch protection
- CODEOWNERS
- Status checks

### Branch protection concepts

```text
Developer Branch
       ↓
     Push
       ↓
 Pull Request
       ↓
 Code Review
       ↓
 CI/CD + Security Checks
       ↓
 Approval
       ↓
     main
```

Useful protections include:

- Require Pull Request
- Require approvals
- Require status checks
- Require branch to be up to date
- Restrict push/merge permissions
- Require signed commits
- Prevent force pushes

---

# 🧪 DevOps/SRE Practice Scenarios

Practice these until you can troubleshoot them without looking up commands:

1. Two developers cloned the same repository and one gets a rejected push.
2. Two branches changed the same line and a merge conflict occurs.
3. You accidentally committed the wrong file.
4. You accidentally staged the wrong file.
5. You need to undo a commit already pushed to `main`.
6. You lost a commit after `reset --hard`.
7. You are halfway through a feature and need to fix an urgent production issue.
8. A critical fix exists on `dev`, but only that commit is required on `main`.
9. You need to prepare a production release from a known commit.
10. GitHub is unavailable — explain what work can still continue locally.
11. Your SSH authentication suddenly fails.
12. Your branch is behind `main` before opening a Pull Request.
13. A PR has conflicts with the latest `main`.
14. You need to inspect who changed a particular line.
15. A production branch must prevent direct pushes.

---

# 🧠 Golden Git Lifecycle

```text
Create / Edit
     ↓
Working Directory
     ↓ git add
Staging Area
     ↓ git commit
Local Repository
     ↓ git push
Remote Repository
     ↓ Pull Request
Review + CI/CD
     ↓
Merge
     ↓
Release / Deploy
```

# ⚡ Daily Cheat Sheet

```bash
# Status
git status

# Stage
git add <file>
git add .

# Commit
git commit -m "message"

# History
git log --oneline --graph --all

# Branch
git switch -c feature/name
git switch main

# Sync
git fetch
git pull
git push

# Compare
git diff
git diff --staged

# Merge
git merge <branch>

# Rebase
git rebase main

# Stash
git stash
git stash pop

# Undo
git restore <file>
git restore --staged <file>
git revert <hash>

# Recovery
git reflog

# Specific commit
git cherry-pick <hash>

# Release
git tag -a v1.0.0 -m "Release v1.0.0"
git push origin v1.0.0
```

---

# 📚 Recommended Repository Structure

```text
git-learning/
├── README.md
├── 01-git-basics/
├── 02-git-lifecycle/
├── 03-branching/
├── 04-merging/
├── 05-rebase/
├── 06-conflicts/
├── 07-reset-revert-restore/
├── 08-stash/
├── 09-cherry-pick/
├── 10-tags/
├── 11-github-workflow/
├── 12-troubleshooting/
└── interview-scenarios/
```

---

## 🏁 Outcome

By completing this roadmap, you should be comfortable with:

**Git Fundamentals → Lifecycle → GitHub → Branching → Merge → Rebase → Conflicts → Reset/Restore/Revert → Stash → Cherry-Pick → Tags → Recovery → Pull Requests → Branch Protection → DevOps/SRE troubleshooting**

> **Learn the command → understand the lifecycle → practice the scenario → explain the reason.**

---

### Source Notes

This roadmap consolidates the uploaded Git/GitHub study materials, including the 39-page complete notes, Git/GitHub lab notes, branching/PR material, and conflict notes. The command coverage and workflow follow the terminology and examples in those materials.

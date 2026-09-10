# Git Learning Path

A structured, hands-on path to Git: from first commit to interview-ready workflows.

Work through the numbered folders in order. Each folder has its own `README.md` with concepts, commands, examples, and practice tasks.

## How to use this repo

1. Read the topic README.
2. Run the commands in a scratch repository (or clone this one and experiment on a throwaway branch).
3. Complete the practice tasks at the bottom of each topic.
4. Use `interview-scenarios/` when you can already use Git day to day and want to practice explaining it.

```bash
# Create a scratch repo for experiments
mkdir git-sandbox && cd git-sandbox
git init
```

## Learning map

| # | Topic | What you will learn |
|---|-------|---------------------|
| 01 | [Git Basics](01-git-basics/README.md) | What Git is, install, config, clone, first commit |
| 02 | [Git Lifecycle](02-git-lifecycle/README.md) | Working tree, staging area, commits, status, log, diff |
| 03 | [Branching](03-branching/README.md) | Create, switch, list, rename, and delete branches |
| 04 | [Merging](04-merging/README.md) | Fast-forward vs merge commits, merge strategies |
| 05 | [Rebase](05-rebase/README.md) | Replay commits, interactive rebase, when not to rebase |
| 06 | [Conflicts](06-conflicts/README.md) | Why conflicts happen, how to resolve and abort |
| 07 | [Reset, Revert, Restore](07-reset-revert-restore/README.md) | Undo work safely vs rewrite history |
| 08 | [Stash](08-stash/README.md) | Park uncommitted work and come back later |
| 09 | [Cherry-pick](09-cherry-pick/README.md) | Copy selected commits onto another branch |
| 10 | [Tags](10-tags/README.md) | Lightweight vs annotated tags, versioning releases |
| 11 | [GitHub Workflow](11-github-workflow/README.md) | remotes, push/pull, PRs, forks, reviews |
| 12 | [Troubleshooting](12-troubleshooting/README.md) | Common errors and how to recover |
| -- | [Interview Scenarios](interview-scenarios/README.md) | Questions, stories, and whiteboard answers |

## Suggested path

```text
Basics -> Lifecycle -> Branching -> Merging
                              \-> Rebase
                                      \-> Conflicts
Undo tools: Reset / Revert / Restore, Stash, Cherry-pick, Tags
Team work: GitHub Workflow -> Troubleshooting -> Interview Scenarios
```

## Core mental model

Git stores snapshots, not diffs. Every commit points at:

- a tree (the files at that moment)
- one or more parents (history)
- metadata (author, message, time)

You move through history with three names:

- `HEAD` -- where you are now
- a branch -- a movable pointer to a commit
- a remote-tracking branch -- last known tip on the server (`origin/main`)

## Everyday command cheat sheet

```bash
git status
git add <file>
git commit -m "message"
git log --oneline --graph --decorate --all
git branch
git switch <branch>
git merge <branch>
git pull
git push
```

## Conventions used in this course

- Commands are shown with comments on the line above, not at the end of the line.
- `<angle-brackets>` mean you replace the value.
- Examples assume a default branch named `main`.
- Never rebase or force-push commits that other people already pulled.

## Practice repo layout

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

Start here: `01-git-basics/README.md`.

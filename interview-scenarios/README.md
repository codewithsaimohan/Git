# Interview Scenarios

Goal: explain Git out loud the way interviewers expect: correct, calm, and with a story from real work.

Use this after you have worked through topics 01--12. Speak answers in 60--90 seconds, then offer a short example.

## How to structure an answer

1. One-sentence definition.
2. When you use it.
3. A command or two.
4. A risk (rewrite vs shared history, data loss).
5. Optional: a war story.

## Conceptual questions

### What is Git? How is it different from GitHub?

Git is a distributed version control system. Every clone has the full history. GitHub is a hosting and collaboration product (PRs, issues, CI hooks) that stores Git remotes.

### What is a commit?

A snapshot of the project plus metadata (author, message, time) and parent pointer(s). Identified by a hash. History is a graph of commits.

### Explain the working tree, index, and repository.

Working tree: files you edit. Index (staging area): the next commit you are building. Repository: committed objects under `.git`. `add` copies working tree -> index. `commit` copies index -> repository.

### What is HEAD?

A pointer to the current commit, usually via the current branch (`ref: refs/heads/main`). Detached HEAD means HEAD points at a commit directly.

### Merge vs rebase?

Merge joins histories with a merge commit (or fast-forward). Rebase replays your commits on top of another tip and rewrites hashes. Rebase for local cleanup; merge for shared integration. Never rebase published commits others depend on.

### Fast-forward vs merge commit?

Fast-forward: the target branch has not diverged, so Git moves the pointer. Merge commit: both sides diverged; Git creates a commit with two parents. `--no-ff` always creates a merge commit.

### reset vs revert vs restore?

`restore` changes files/index, not history. `reset` moves a branch pointer (can rewrite). `revert` adds a new commit that undoes a previous one -- safe for shared `main`.

### What is a conflict? How do you resolve it?

Overlapping edits Git will not auto-merge. Open the file, remove markers, keep the correct code, `git add`, then `commit` or `rebase --continue`. Abort if you are lost. Run tests after.

### What is cherry-pick?

Copy the patch of a commit onto the current branch as a new commit. Used for hotfixes across release branches. Can duplicate changes and complicate later merges.

### Soft, mixed, hard reset?

All move HEAD. Soft leaves index and working tree. Mixed (default) resets index, keeps working tree. Hard resets both -- discards uncommitted work.

### What does force-push do? When is it OK?

Overwrites the remote branch pointer. OK on a short-lived feature branch you own after rebase, preferably `--force-with-lease`. Never on `main` or shared long-lived branches.

### What is a tag?

An immutable name for a commit, usually a release (`v1.2.0`). Annotated tags store message, tagger, date. Branches move; tags should not.

### fetch vs pull?

`fetch` updates remote-tracking refs (`origin/main`) only. `pull` is fetch plus merge (or rebase) into the current branch.

### Squash merge?

PR lands as one commit on `main`. Feature commits disappear from mainline history. Clean `main`; lost granularity.

## Scenario questions (practice out loud)

### 1. You committed on `main` by mistake (not pushed).

Create a branch at HEAD, reset `main` to `origin/main`, keep working on the new branch.

```bash
git switch -c feature/oops
git switch main
git reset --hard origin/main
```

### 2. You pushed a bad commit to `main`.

Do not reset public `main`. `git revert HEAD` and push. If a secret leaked, rotate the secret first.

### 3. A teammate force-pushed the feature branch you both use.

Fetch. If you have local commits, recover them (`reflog`, cherry-pick) onto the new tip. Agree: only one person rebases a shared feature branch.

### 4. You need yesterday's experiment, but you reset --hard.

`git reflog`, find the commit, `git switch -c recovered <sha>`.

### 5. Hotfix is on `release/1.2` and must also land on `main`.

Cherry-pick the hotfix commit onto `main` with `-x`, or merge a dedicated hotfix branch into both.

### 6. PR has 20 "wip" commits.

Interactive rebase (`git rebase -i main`) to squash/reword, then `--force-with-lease` if the branch is yours alone. Or use GitHub squash merge and leave local history messy.

### 7. `git pull` reports diverged branches.

Inspect `git log --oneline --graph --decorate --all`. If local commits are yours, `git pull --rebase`. If you already created merge noise, a merge pull is fine. Do not force-push `main`.

### 8. Two features touched the same lines.

Merge or rebase, resolve conflicts, run tests, ask the other author if the combined logic is unclear.

### 9. You must ship only one commit from a long feature branch.

`git cherry-pick <sha>` onto a clean branch from `main`. Confirm you do not need later commits that depend on it.

### 10. Detached HEAD after checking out a tag.

`git switch -c hotfix/from-tag` if you will commit; otherwise `git switch main`.

## Workflow design questions

Interviewers often ask "how does your team use Git?"

A solid default answer:

- `main` is always releasable.
- Feature branches, short-lived.
- PRs with review and CI.
- No direct push to `main` (branch protection).
- Rebase private feature branches; merge (or squash) into `main`.
- Tags for releases.
- Revert to undo production, not history rewrite.

Mention what you have actually used (GitHub Flow, GitLab Flow, a simple release branch). Honesty beats a textbook process you never ran.

## Live whiteboard: draw this

```text
main:    A -- B -- C -- F (merge)
               \      /
feature:        D -- E
```

Then draw the same story after rebase:

```text
main:    A -- B -- C -- D' -- E'
```

Be ready to point at HEAD, merge base, and first-parent of F.

## Rapid-fire commands

Say what each does in one line:

```bash
git status
git add -p
git commit --amend
git log --oneline --graph --all
git switch -c feature/x
git merge --no-ff
git rebase -i HEAD~3
git stash push -u
git cherry-pick -x <sha>
git revert HEAD
git reset --soft HEAD~1
git reflog
git fetch --prune
git push --force-with-lease
```

## Self-drill

Close the notes. For each scenario 1--10, talk for one minute, then compare. Weak spots map back to the numbered topic folders.

When you can teach merge vs rebase without notes, you are ready for the Git portion of most interviews.

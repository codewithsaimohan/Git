# 04 -- Merging

Goal: combine branch histories with fast-forward and merge commits.

## Why merge

You built work on `feature/login`. `main` should include it. Merge brings those commits into the current branch.

```bash
git switch main
git merge feature/login
```

## Fast-forward merge

If `main` has not moved since you branched, Git just slides the `main` pointer forward. No merge commit.

```text
Before:  main --> C1 --> C2
                          \
feature                   C3 --> C4

After:   main, feature --> C1 --> C2 --> C3 --> C4
```

```bash
git merge feature/login
```

Force a merge commit even when a fast-forward is possible:

```bash
git merge --no-ff feature/login
```

Teams often use `--no-ff` so each feature remains a visible bubble in `git log --graph`.

## Three-way merge (true merge)

If both branches moved, Git finds the merge base (common ancestor) and produces a new merge commit with two parents.

```text
          C3'  (main)
         /
C1 --> C2
         \
          C3 --> C4  (feature)

After merge on main:

C1 --> C2 --> C3' ------ C5 (merge commit)
         \             /
          C3 --> C4 --
```

```bash
git switch main
git merge feature/login -m "merge: feature/login"
```

## Abort a merge

If you started a merge and want out before committing:

```bash
git merge --abort
```

Works only if the working tree was clean enough for Git to restore it.

## Merge strategies you will hear about

| Strategy | When |
|----------|------|
| `ort` (default) | Normal branch merge |
| `ours` | Keep our side, record a merge |
| octopus | Merge more than two branches |

```bash
git merge -s ours obsolete-branch
```

## Squash merge

Fold all feature commits into one commit on the current branch. The feature branch is not recorded as a parent.

```bash
git merge --squash feature/login
git commit -m "feat: add login"
```

Useful for messy local history. You lose per-commit detail on `main`.

## Recreate a merge demo

```bash
git init merge-demo && cd merge-demo
echo "base" > app.txt
git add app.txt && git commit -m "feat: base"

git switch -c feature/hello
echo "hello" >> app.txt
git commit -am "feat: hello"

git switch main
echo "main-line" > other.txt
git add other.txt && git commit -m "feat: other file"

git merge feature/hello
git log --oneline --graph --decorate
```

## Best practices

- Merge into `main` from a clean working tree.
- Prefer short-lived feature branches.
- Pull (or rebase) current `main` into your feature branch before the final merge to reduce surprises.
- Write merge messages that name the feature, not "merge branch".

## Practice

1. Create a fast-forward situation and merge it. Inspect `git log --graph`.
2. Repeat with `--no-ff` and compare the graph.
3. Diverge two branches, merge, and find the merge commit with two parents (`git log --merges`).
4. Try `git merge --squash` and see that `main` has one new commit.

## Check yourself

- When is a merge a fast-forward?
- How many parents does a merge commit have?
- What does `--no-ff` preserve?
- How is squash merge different from a merge commit?

Next: `05-rebase/README.md`. Then read `06-conflicts/README.md` before you rely on either merge or rebase in a team.

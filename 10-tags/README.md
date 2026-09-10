# 10 -- Tags

Goal: mark releases and other fixed points in history.

## What a tag is

A tag is a name that points at a commit and (usually) does not move. Branches move; tags should not.

Use tags for versions: `v1.0.0`, `v2.1.3`.

## Lightweight vs annotated

| | Lightweight | Annotated |
|--|-------------|-----------|
| What it is | A name pointing at a commit | A tag object: message, tagger, date, optional signature |
| Create | `git tag v1.0.0` | `git tag -a v1.0.0 -m "msg"` |
| For releases | weak | preferred |

```bash
# Lightweight
git tag v0.1.0

# Annotated (prefer this for releases)
git tag -a v1.0.0 -m "Release 1.0.0"

# Tag a past commit
git tag -a v0.9.0 abc1234 -m "Hotfix snapshot"
```

## List and inspect

```bash
git tag
git tag -l "v1.*"
git show v1.0.0
```

## Push and fetch tags

Tags are not pushed with a normal `git push`.

```bash
# One tag
git push origin v1.0.0

# All tags
git push origin --tags

# Fetch tags
git fetch --tags
```

## Check out a tag

```bash
git switch --detach v1.0.0
```

You are in detached HEAD. Create a branch if you need to commit:

```bash
git switch -c hotfix/1.0.1 v1.0.0
```

## Delete

```bash
# Local
git tag -d v1.0.0

# Remote
git push origin --delete v1.0.0
```

Deleting a published tag surprises people who already cloned it. Treat published tags as immutable; if the release was wrong, publish `v1.0.1`.

## SemVer reminder

```text
vMAJOR.MINOR.PATCH
v1.4.2
```

- MAJOR: breaking changes
- MINOR: new features, backward compatible
- PATCH: bug fixes

Git does not enforce this. Your team does.

## Sign tags (optional)

```bash
git tag -s v1.0.0 -m "signed release"
git tag -v v1.0.0
```

Requires GPG/SSH signing set up. Common in open-source releases.

## Practice

1. Create an annotated tag `v1.0.0` on HEAD and `git show` it.
2. Create a lightweight tag and compare `git show`.
3. Tag an older commit.
4. Explain why you would not move `v1.0.0` after people downloaded it.

## Check yourself

- Why prefer annotated tags for releases?
- Do tags move like branches?
- How do you push a single tag?
- What happens if you check out a tag?

Next: `11-github-workflow/README.md`.

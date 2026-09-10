# 01 -- Git Basics

Goal: install Git, configure identity, understand a repository, and make your first commit.

## What Git is

Git is a distributed version control system. Every clone is a full copy of the project history. You can commit offline. Collaboration happens by exchanging commits with remotes (GitHub, GitLab, Bitbucket, or a bare repo on a server).

Git is not:

- a backup tool (though history can save you)
- GitHub (GitHub is a hosting service that uses Git)
- required to be online

## Install

```bash
# Debian / Ubuntu
sudo apt-get update && sudo apt-get install -y git

# macOS (Homebrew)
brew install git

# Check version
git --version
```

## First-time configuration

Git stamps every commit with your name and email. Set them once globally.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Sensible defaults
git config --global init.defaultBranch main
git config --global core.editor "nano"
git config --global color.ui auto
git config --global pull.rebase false

# Review all settings
git config --list --show-origin
```

Config layers (later wins):

1. System -- `/etc/gitconfig`
2. Global -- `~/.gitconfig`
3. Local -- `.git/config` in the repo

```bash
# Set a value only for the current repo
git config --local user.email "work@company.com"
```

## Create a repository

Two ways: start empty, or copy an existing one.

```bash
# Start a new project
mkdir demo && cd demo
git init

# Copy an existing project
git clone https://github.com/example/project.git
cd project
```

`git init` creates a `.git/` directory. That directory is the database. Delete `.git` and the folder is just files again.

## Anatomy of a repo

```text
demo/
├── .git/          Git database (objects, refs, config)
├── README.md      your files (working tree)
└── src/
```

Important pieces inside `.git/`:

- `objects/` -- blobs, trees, commits
- `refs/heads/` -- local branches
- `HEAD` -- current branch (or detached commit)
- `config` -- repo-specific settings

## Your first commit

```bash
echo "# Demo" > README.md

# See what Git thinks
git status

# Stage the file
git add README.md

# Snapshot the staged files
git commit -m "docs: add README"

# Confirm history
git log --oneline
```

A commit message should say why, not only what. Common style:

```text
type: short summary in imperative mood

Optional body with context.
```

Types you will see: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`.

## Clone options you will use

```bash
# Shallow clone (less history, faster)
git clone --depth 1 https://github.com/example/project.git

# Clone a specific branch
git clone --branch develop https://github.com/example/project.git

# Clone into a named folder
git clone https://github.com/example/project.git my-folder
```

## Getting help

```bash
git help
git help commit
git commit --help
git commit -h
```

## `.gitignore`

Tell Git which files never to track.

```bash
cat > .gitignore << 'EOF'
node_modules/
.env
*.log
.DS_Store
EOF

git add .gitignore
git commit -m "chore: add gitignore"
```

Rules:

- Patterns are relative to the `.gitignore` location.
- `*.log` ignores all log files.
- `/build` ignores `build` only at repo root.
- `!keep.log` un-ignores a previously ignored file.
- Already-tracked files are not ignored until you untrack them (`git rm --cached`).

## Practice

1. Install Git and set `user.name` and `user.email`.
2. `git init` a folder, add a `README.md`, commit it.
3. Change the README, run `git status`, then commit the change.
4. Clone a public repo with `--depth 1`.
5. Create a `.gitignore` that ignores `*.tmp` and commit it.

## Check yourself

- What is the difference between Git and GitHub?
- Where does Git store history?
- What three config scopes exist?
- Why does `.gitignore` not hide a file you already committed?

Next: `02-git-lifecycle/README.md`.

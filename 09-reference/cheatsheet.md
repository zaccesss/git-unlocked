# Git cheatsheet

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

Every essential Git command on one page, organised by task.

---

## Table of contents

- [Setup and configuration](#setup-and-configuration)
- [Starting a repository](#starting-a-repository)
- [Staging and committing](#staging-and-committing)
- [Branching](#branching)
- [Merging and rebasing](#merging-and-rebasing)
- [Remote repositories](#remote-repositories)
- [Undoing changes](#undoing-changes)
- [Inspection and history](#inspection-and-history)
- [Stash](#stash)
- [Tags](#tags)
- [Submodules](#submodules)
- [Worktrees](#worktrees)
- [Advanced operations](#advanced-operations)
- [Monorepo and performance](#monorepo-and-performance)
- [Useful aliases](#useful-aliases)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Setup and configuration

```bash
# Identity (required before first commit)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Recommended global settings
git config --global init.defaultBranch main
git config --global pull.rebase true
git config --global rebase.autoSquash true
git config --global rebase.autoStash true
git config --global rebase.updateRefs true
git config --global rerere.enabled true
git config --global merge.conflictStyle zdiff3
git config --global diff.algorithm histogram
git config --global diff.colorMoved default
git config --global fetch.prune true
git config --global push.autoSetupRemote true
git config --global column.ui auto
git config --global branch.sort -committerdate
git config --global core.fsmonitor true
git config --global feature.manyFiles true

# SSH commit signing (Git 2.34+, preferred over GPG)
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
git config --global tag.gpgsign true

# View config
git config --list --show-origin
git config --global --edit
```

---

## Starting a repository

```bash
git init                            # initialise in current directory
git init project-name               # create folder and initialise
git init --bare repo.git            # bare repository (for servers)

git clone <url>                     # clone into folder named after repo
git clone <url> my-folder           # clone into specific folder
git clone --depth 1 <url>           # shallow clone (latest commit only)
git clone --branch main <url>       # clone a specific branch
git clone --recurse-submodules <url> # clone with submodules

# Partial clone (monorepos and large repos)
git clone --filter=blob:none <url>  # blobless: best for developers
git clone --filter=tree:0 <url>     # treeless: best for single-build CI
git clone --filter=blob:none --sparse <url>  # blobless + sparse checkout
scalar clone <url>                  # all monorepo optimisations in one go
```

---

## Staging and committing

```bash
git status                          # show working tree and staging area
git status -s                       # short format
git status -sb                      # short with branch info

git add <file>                      # stage a specific file
git add .                           # stage all changes in current directory
git add -p                          # interactive patch staging (review each chunk)
git add -u                          # stage all tracked changes (no untracked)

git commit -m "message"             # commit with message
git commit -am "message"            # stage tracked files and commit
git commit --amend --no-edit        # add staged changes to last commit, keep message
git commit --amend -m "new message" # rewrite last commit message
git commit --fixup=<sha>            # create fixup targeting a specific commit
git commit -s -m "message"          # sign off with DCO Signed-off-by trailer

git diff                            # unstaged changes
git diff --staged                   # staged changes (vs last commit)
git diff main...feature             # PR diff: changes since branching from main
git diff --stat                     # per-file summary only
git diff --word-diff                # word-level diff (good for prose)
```

---

## Branching

```bash
git branch                          # list local branches
git branch -a                       # list local and remote branches
git branch -v                       # list with last commit
git branch --sort=-committerdate    # sort by most recently committed

git switch -c feat/name             # create and switch to new branch
git switch -c feat/name upstream/main  # branch from upstream
git switch main                     # switch to existing branch
git switch -                        # switch to previous branch

git branch -d feat/name             # delete merged branch
git branch -D feat/name             # force delete unmerged branch
git branch -m old-name new-name     # rename branch

# Tracking
git branch --set-upstream-to=origin/main main
git branch -vv                      # show tracking info
```

---

## Merging and rebasing

```bash
# Merging
git merge feat/name                 # merge branch into current
git merge --no-ff feat/name         # force a merge commit
git merge --squash feat/name        # squash to one commit, then git commit
git merge --ff-only feat/name       # fast-forward only, abort if not possible
git merge --abort                   # abort an in-progress merge

# Rebasing
git rebase main                     # rebase current branch onto main
git rebase -i HEAD~3                # interactive rebase: last 3 commits
git rebase -i --autosquash main     # auto-arrange fixup! commits
git rebase --onto new-base old-base # move branch to a new base
git rebase --continue               # continue after resolving conflicts
git rebase --abort                  # abandon the rebase

# Cherry-pick
git cherry-pick <sha>               # apply a single commit
git cherry-pick A..B                # apply a range (exclusive of A)
git cherry-pick A^..B               # apply a range (inclusive of A)
git cherry-pick --no-commit <sha>   # apply changes without committing
git cherry-pick -x <sha>            # append "cherry picked from" note
git cherry-pick -m 1 <merge-sha>    # cherry-pick a merge commit
```

---

## Remote repositories

```bash
git remote -v                       # list remotes
git remote add origin <url>         # add a remote
git remote add upstream <url>       # add upstream (for forks)
git remote remove origin            # remove a remote
git remote set-url origin <new-url> # change remote URL

git fetch                           # fetch all remotes
git fetch origin                    # fetch from origin
git fetch --all --prune             # fetch all, delete stale tracking refs
git fetch upstream                  # fetch from upstream

git pull                            # fetch and merge (or rebase if configured)
git pull --rebase                   # fetch and rebase
git pull --rebase upstream main     # sync fork branch with upstream

git push                            # push to tracked remote branch
git push -u origin feat/name        # push and set tracking upstream
git push --force-with-lease         # safe force push (checks remote hasn't moved)
git push --force-with-lease --force-if-includes  # stricter (Git 2.30+)
git push origin --delete feat/name  # delete a remote branch
git push --tags                     # push all tags
git push origin v1.2.3              # push a specific tag

# Mirror
git clone --mirror <url>            # mirror-clone (all refs)
git push --mirror <dest-url>        # push everything to destination (destructive)
```

---

## Undoing changes

```bash
# Safe (non-destructive)
git restore <file>                  # discard unstaged changes in file
git restore --staged <file>         # unstage a file (keep changes)
git restore --source=HEAD~2 <file>  # restore file from 2 commits ago
git revert <sha>                    # create a new commit undoing <sha>
git revert -m 1 <merge-sha>         # revert a merge commit (keep mainline)

# History-rewriting (local use only unless you force-push)
git reset --soft HEAD~1             # undo last commit, keep changes staged
git reset --mixed HEAD~1            # undo last commit, keep changes unstaged (default)
git reset --hard HEAD~1             # undo last commit, discard all changes
git reset --hard ORIG_HEAD          # undo a just-completed merge or rebase
git reset --hard HEAD@{1}           # go back to previous reflog position

# Reflog (recovery)
git reflog                          # show all HEAD movements
git reflog show main                # reflog for a specific branch
git reflog --date=iso               # reflog with timestamps
git reset --hard HEAD@{n}           # recover to reflog position n

# Find dangling objects after expired reflog
git fsck --full --no-reflogs --unreachable --lost-found
```

---

## Inspection and history

```bash
git log                             # full history
git log --oneline                   # one line per commit
git log --oneline --graph --all     # visual branch graph
git log --oneline --decorate --all  # with branch and tag labels
git log -n 10                       # last 10 commits
git log --author="Name"             # filter by author
git log --since="2 weeks ago"       # filter by date
git log --grep="keyword"            # filter by commit message
git log -- path/to/file             # history of a specific file
git log -p -- path/to/file          # history with diffs
git log --follow -- path/to/file    # follow file through renames
git log -S "function_name"          # pickaxe: commits that added/removed string
git log -G "regex"                  # commits where diff matches regex
git log --merges                    # merge commits only
git range-diff old..new             # diff between two rebase iterations

git show <sha>                      # show a commit
git show <sha>:path/to/file         # show file at a specific commit
git show HEAD~3:package.json        # file from 3 commits ago

git blame -w path/to/file           # annotate lines with last commit (ignore whitespace)
git blame -w -M -C -C -C file      # follow moves and copies across files

git diff --name-only origin/main...HEAD   # files changed since main
git diff --stat origin/main...HEAD        # summary of changes since main

git bisect start
git bisect bad                      # current commit is broken
git bisect good v1.0                # last known good commit
git bisect run npm test             # automate bisect with a test command
git bisect reset                    # end bisect session
```

---

## Stash

```bash
git stash push -m "description"     # stash with a message
git stash push --keep-index         # stash unstaged only (keep staged)
git stash push -u                   # include untracked files
git stash list                      # list all stashes
git stash show -p stash@{0}         # show diff of stash
git stash pop                       # apply most recent stash and delete it
git stash apply stash@{1}           # apply a specific stash (keep it)
git stash drop stash@{0}            # delete a specific stash
git stash clear                     # delete all stashes
git stash branch feat/name          # create branch from stash
```

---

## Tags

```bash
git tag                             # list all tags
git tag -l "v1.*"                   # list tags matching pattern
git tag v1.2.3                      # create lightweight tag
git tag -a v1.2.3 -m "Release"      # create annotated tag
git tag -s v1.2.3 -m "Release"      # create signed annotated tag
git tag -a v1.2.3 <sha>             # tag a specific commit

git show v1.2.3                     # show tag details
git push origin v1.2.3              # push a single tag
git push --tags                     # push all tags
git push origin --delete v1.2.3     # delete a remote tag

git tag -d v1.2.3                   # delete local tag
git checkout v1.2.3                 # check out a tag (detached HEAD)
```

---

## Submodules

```bash
git submodule add <url> path/to/sub # add a submodule
git submodule init                  # initialise submodule config
git submodule update                # checkout recorded commits
git submodule update --init --recursive  # full init + update
git submodule update --remote       # update to latest upstream commit
git submodule foreach git pull      # update all submodules

git clone --recurse-submodules <url>     # clone with submodules
git submodule set-url <name> <new-url>  # change submodule URL
```

---

## Worktrees

```bash
git worktree add ../hotfix main     # new working directory on main
git worktree add ../feat feat/name  # new working directory on a branch
git worktree list                   # list all working trees
git worktree remove ../hotfix       # remove a working tree
git worktree prune                  # clean up stale worktree references
```

---

## Advanced operations

```bash
# Filter repo (replace git filter-branch - officially deprecated)
git filter-repo --path config/secrets.json --invert-paths
git filter-repo --use-base-name --path id_rsa --invert-paths
git filter-repo --replace-text replacements.txt

# Maintenance and integrity
git gc                              # garbage collect
git gc --aggressive --prune=now     # aggressive clean (slow, use rarely)
git fsck --full                     # check repository integrity
git maintenance start               # enable background maintenance
git commit-graph write --reachable --changed-paths  # build commit graph

# Notes and attributes
git notes add -m "note text" <sha>  # add a note to a commit
git notes show <sha>                # show notes on a commit

# Reflog expiry (careful)
git reflog expire --expire=now --all
git gc --prune=now

# Pathspec magic
git log -- ':(top)README.md'        # anchor to repo root
git log -- ':(icase)*.MD'           # case-insensitive
git grep -n 'TODO' -- ':(exclude)vendor/'  # exclude vendor
```

---

## Monorepo and performance

```bash
# Sparse checkout
git sparse-checkout init --cone
git sparse-checkout set path/to/dir another/dir
git sparse-checkout add path/to/more
git sparse-checkout list
git sparse-checkout disable

# Performance config
git config core.fsmonitor true
git config core.untrackedCache true
git config index.sparse true
git config feature.manyFiles true
git config fetch.writeCommitGraph true
git config checkout.workers 0

# Commit graph
git commit-graph write --reachable --changed-paths
git commit-graph write --reachable --changed-paths --split

# Affected files since main (feed to task runners)
git diff --name-only origin/main...HEAD

# Partial clone filters
git clone --filter=blob:none <url>   # blobless
git clone --filter=tree:0 <url>      # treeless (CI only)
git clone --filter=blob:limit=1m <url>  # size cap
```

---

## Useful aliases

```bash
git config --global alias.lg \
  "log --graph --abbrev-commit --decorate \
  --format=format:'%C(bold blue)%h%C(reset) %C(bold green)(%ar)%C(reset) \
  %s %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)' --all"

git config --global alias.st "status -sb"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.amend "commit --amend --no-edit"
git config --global alias.please "push --force-with-lease"
git config --global alias.ri "rebase -i --autosquash"
git config --global alias.sync "!git fetch --all --prune && git pull --rebase"
git config --global alias.unstage "restore --staged"
git config --global alias.aliases "config --get-regexp ^alias\."
```

---

## Sources and Further Reading

- [git-scm.com: reference manual](https://git-scm.com/docs)
- [git-scm.com: Pro Git book](https://git-scm.com/book/en/v2)
- [GitHub: git cheatsheet (PDF)](https://education.github.com/git-cheat-sheet-education.pdf)
- [Atlassian: Git tutorials](https://www.atlassian.com/git/tutorials)
- [git-filter-repo documentation](https://github.com/newren/git-filter-repo)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

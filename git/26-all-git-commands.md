# All Git Commands

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

This is the complete reference for Git commands. Every command is grouped by category with a brief description, the most common options and a usage example. Use this file when you know what you want to do but cannot remember the exact command or flags.

Commands are marked with difficulty levels: 🟢 everyday use, 🟡 intermediate, 🔴 advanced.

---

## Table of Contents

- [Getting started](#getting-started)
- [Configuration](#configuration)
- [Basic workflow](#basic-workflow)
- [Branching](#branching)
- [Merging and rebasing](#merging-and-rebasing)
- [Remote repositories](#remote-repositories)
- [Undoing and recovering](#undoing-and-recovering)
- [Inspection and history](#inspection-and-history)
- [Stash](#stash)
- [Tags](#tags)
- [Submodules](#submodules)
- [Bisect](#bisect)
- [Advanced operations](#advanced-operations)
- [Plumbing commands](#plumbing-commands)
- [Quick reference card](#quick-reference-card)

---

## Getting Started

### git init 🟢

Create a new Git repository in the current folder.

```bash
git init                    # initialise in current directory
git init project-name       # create and initialise a new folder
git init --bare repo.git    # create a bare repository (for servers)
```

### git clone 🟢

Download a repository from a remote URL.

```bash
git clone <url>                          # clone into folder named after repo
git clone <url> my-folder                # clone into specific folder
git clone --depth 1 <url>               # shallow clone (last commit only)
git clone --branch main <url>           # clone a specific branch
git clone --recurse-submodules <url>    # clone including submodules
git clone --filter=blob:none <url>      # partial clone (no file content)
```

---

## Configuration

### git config 🟢

Read and write Git configuration.

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global --list              # list all global settings
git config --global --edit              # open global config in editor
git config --local user.email "work@company.com"   # repo-specific setting
git config --global alias.st status     # create an alias
git config --global --unset alias.st    # remove a setting
git config --list --show-origin         # list all settings with source file
```

---

## Basic Workflow

### git status 🟢

Show the state of the working directory and staging area.

```bash
git status                  # full status
git status -s               # short format
git status -sb              # short format with branch info
git status --ignored        # show ignored files too
```

### git add 🟢

Stage changes for the next commit.

```bash
git add filename.txt        # stage a specific file
git add .                   # stage all changes in current directory
git add -A                  # stage all changes in entire repo
git add -p filename.txt     # stage changes interactively (choose hunks)
git add -u                  # stage changes to tracked files only (not new files)
```

### git commit 🟢

Save staged changes as a commit.

```bash
git commit -m "add: my change"          # commit with message
git commit                              # open editor for message
git commit -am "fix: tracked files"     # stage tracked files and commit
git commit --amend -m "new message"     # rewrite the last commit
git commit --amend --no-edit            # add to last commit, keep message
git commit -S -m "signed commit"        # sign the commit with GPG or SSH
```

### git diff 🟢

Show line-by-line differences.

```bash
git diff                    # unstaged changes vs last commit
git diff --staged           # staged changes vs last commit
git diff HEAD               # all changes vs last commit
git diff main feature       # compare two branches
git diff a1b2c3d e4f5g6h    # compare two commits
git diff --name-only        # show only changed filenames
git diff --stat             # show summary stats
git diff --word-diff        # show word-level differences
```

### git restore 🟢

Restore files in the working directory or staging area.

```bash
git restore filename.txt            # discard working directory changes
git restore .                       # discard all working directory changes
git restore --staged filename.txt   # unstage a file
git restore --staged .              # unstage everything
git restore --source HEAD~2 file    # restore file to 2 commits ago
```

---

## Branching

### git branch 🟢

List, create, rename or delete branches.

```bash
git branch                  # list local branches
git branch -a               # list all branches (local and remote)
git branch -r               # list remote branches
git branch -v               # list with last commit
git branch -vv              # list with tracking info
git branch feature-name     # create a branch (don't switch)
git branch -d feature-name  # delete a merged branch
git branch -D feature-name  # force delete (unmerged)
git branch -m old new       # rename a branch
git branch -u origin/main   # set upstream tracking
git branch --merged         # branches merged into current
git branch --no-merged      # branches not yet merged
git branch --sort=-committerdate   # sort by most recently committed
```

### git switch 🟢

Switch branches (recommended over `git checkout` for this purpose).

```bash
git switch main                     # switch to existing branch
git switch -c feature/new-branch    # create and switch
git switch -                        # switch to previous branch
git switch -c fix origin/fix        # create local branch from remote
```

### git checkout 🟡

Multi-purpose command (switch branches, restore files, detach HEAD).

```bash
git checkout main               # switch branch
git checkout -b feature         # create and switch
git checkout a1b2c3d            # detach HEAD at a commit
git checkout -- filename.txt    # discard working directory changes
git checkout HEAD~2 -- file     # restore file from 2 commits ago
```

---

## Merging and Rebasing

### git merge 🟡

Combine a branch into the current branch.

```bash
git merge feature-branch        # merge into current branch
git merge --no-ff feature       # force a merge commit
git merge --squash feature      # squash all commits into one
git merge --abort               # abort a conflicted merge
git merge -m "message" feature  # merge with custom message
git merge --continue            # continue after resolving conflict
```

### git rebase 🔴

Reapply commits onto a different base.

```bash
git rebase main                 # rebase current branch onto main
git rebase -i HEAD~3            # interactive rebase of last 3 commits
git rebase -i main              # interactive rebase onto main
git rebase --onto main old-base feature   # move commits to new base
git rebase --continue           # continue after resolving conflict
git rebase --skip               # skip the current conflicted commit
git rebase --abort              # abort and return to original state
```

### git cherry-pick 🔴

Apply a specific commit to the current branch.

```bash
git cherry-pick a1b2c3d             # apply one commit
git cherry-pick a1b2c3d e4f5g6h     # apply multiple commits
git cherry-pick a1b2c3d..i7j8k9l    # apply a range (exclusive start)
git cherry-pick -x a1b2c3d          # append cherry-pick source note
git cherry-pick --no-commit a1b2c3d # apply without committing
git cherry-pick --abort             # abort a conflicted cherry-pick
git cherry-pick --continue          # continue after resolving conflict
```

---

## Remote Repositories

### git remote 🟢

Manage remote repository references.

```bash
git remote -v                                       # list remotes with URLs
git remote add origin <url>                         # add a remote
git remote remove origin                            # remove a remote
git remote rename origin upstream                   # rename a remote
git remote set-url origin <new-url>                 # change a remote's URL
git remote show origin                              # show remote details
git remote prune origin                             # remove stale tracking branches
```

### git fetch 🟢

Download from a remote without merging.

```bash
git fetch                   # fetch from default remote
git fetch origin            # fetch from specific remote
git fetch --all             # fetch from all remotes
git fetch --prune           # fetch and remove stale tracking branches
git fetch origin main       # fetch a specific branch
```

### git pull 🟢

Fetch and merge from a remote.

```bash
git pull                    # pull from tracked remote branch
git pull origin main        # pull specific remote and branch
git pull --rebase           # rebase instead of merge
git pull --ff-only          # only allow fast-forward pulls
git pull --all              # pull all remotes
```

### git push 🟢

Send commits to a remote.

```bash
git push                            # push to tracked remote
git push origin main                # push to specific remote and branch
git push -u origin feature          # push and set upstream tracking
git push --all                      # push all branches
git push --tags                     # push all tags
git push --follow-tags              # push commits and annotated tags
git push origin --delete branch     # delete a remote branch
git push --force-with-lease         # force push safely
```

---

## Undoing and Recovering

### git reset 🟡

Move the branch pointer to a different commit.

```bash
git reset --soft HEAD~1     # undo last commit, keep changes staged
git reset HEAD~1            # undo last commit, unstage changes
git reset --hard HEAD~1     # undo last commit, discard changes
git reset --hard a1b2c3d    # reset to a specific commit
git reset HEAD filename     # unstage a specific file (older syntax)
```

### git revert 🟡

Create a new commit that undoes a previous commit.

```bash
git revert HEAD                  # revert the last commit
git revert a1b2c3d               # revert a specific commit
git revert --no-commit a1b2c3d   # apply undo without committing
git revert -m 1 HEAD             # revert a merge commit (parent 1)
git revert a1b2c3d..HEAD         # revert a range of commits
```

### git reflog 🔴

View the history of HEAD positions.

```bash
git reflog                  # show all HEAD movements
git reflog show main        # show movements for a specific branch
git reflog --date=iso       # show with timestamps
git reflog expire           # manually expire old entries
```

### git clean 🟡

Remove untracked files from the working directory.

```bash
git clean -n                # dry run - show what would be removed
git clean -f                # remove untracked files
git clean -fd               # remove untracked files and directories
git clean -fX               # remove ignored files only
git clean -fdx              # remove all untracked and ignored files
```

---

## Inspection and History

### git log 🟢

Show commit history.

```bash
git log                             # full log
git log --oneline                   # one line per commit
git log --oneline --graph --all     # visual branch graph
git log -5                          # last 5 commits
git log --author="Your Name"        # filter by author
git log --since="2 weeks ago"       # filter by date
git log --grep="fix"                # filter by commit message
git log -- filename.txt             # commits that changed a file
git log -p                          # show diff for each commit
git log --stat                      # show changed files summary
git log --format="%h %s %an"        # custom format
git log -S "search term"            # find commits that added/removed a string
```

### git show 🟢

Show information about a commit, tag or tree.

```bash
git show HEAD               # show last commit
git show a1b2c3d            # show a specific commit
git show v2.0.0             # show a tag
git show HEAD:filename.txt  # show file contents at HEAD
git show --stat HEAD        # show changed files summary
```

### git blame 🟡

Show who last modified each line of a file.

```bash
git blame filename.txt          # annotate every line
git blame -L 10,20 file         # annotate lines 10-20 only
git blame -w file               # ignore whitespace changes
git blame --since=1.month file  # ignore commits older than 1 month
git blame -e file               # show email instead of name
```

### git grep 🟡

Search file contents across the repository.

```bash
git grep "search term"              # search in working directory
git grep "term" HEAD                # search at HEAD
git grep "term" v2.0.0              # search at a specific tag
git grep -n "term"                  # show line numbers
git grep -i "term"                  # case insensitive
git grep -l "term"                  # show filenames only
git grep -c "term"                  # show count per file
```

### git shortlog 🟢

Summarise commit history by author.

```bash
git shortlog                # commits grouped by author
git shortlog -sn            # count sorted by number of commits
git shortlog -sn --all      # include all branches
git shortlog -sne           # include email addresses
```

### git describe 🟡

Describe a commit using the nearest tag.

```bash
git describe                    # describe HEAD using nearest tag
git describe --tags             # use any tag (not just annotated)
git describe --always           # always output something even without tags
git describe a1b2c3d            # describe a specific commit
```

Output format: `v2.0.0-14-ga1b2c3d` means 14 commits after tag `v2.0.0`, at commit `a1b2c3d`.

### git archive 🟡

Export a snapshot of the repository as a zip or tar archive.

```bash
git archive --format=zip HEAD > project.zip           # zip of current state
git archive --format=tar.gz HEAD > project.tar.gz     # tar.gz of current state
git archive --format=zip v2.0.0 > release-v2.zip      # zip at a specific tag
git archive --format=zip HEAD -- src/ > src.zip        # zip a subdirectory only
```

Useful for distributing source code without the `.git` folder.

### git notes 🔴

Add notes to commits without amending them.

```bash
git notes add -m "reviewed by team"    # add a note to HEAD
git notes add -m "note" a1b2c3d        # add a note to a specific commit
git notes show a1b2c3d                 # show the note on a commit
git notes edit a1b2c3d                 # edit an existing note
git notes remove a1b2c3d              # remove a note
git log --show-notes                   # show notes in git log
git push origin refs/notes/commits     # push notes to remote
git fetch origin refs/notes/commits:refs/notes/commits   # fetch notes
```

Notes are stored separately from commits. They are useful for adding post-hoc information like code review results, deployment records or QA sign-offs without altering the commit hash.

---

## Stash

### git stash 🟡

Save work in progress without committing.

```bash
git stash                           # stash tracked changes
git stash push -m "description"     # stash with a message
git stash -u                        # include untracked files
git stash -a                        # include all files
git stash list                      # list all stashes
git stash show                      # show most recent stash
git stash show -p                   # show diff of most recent stash
git stash pop                       # apply and remove most recent stash
git stash apply stash@{2}           # apply a specific stash (keep it)
git stash drop stash@{1}            # remove a specific stash
git stash clear                     # remove all stashes
git stash branch feature stash@{0}  # create branch from stash
git stash push -p                   # stash interactively
```

---

## Tags

### git tag 🟡

Create, list and delete tags.

```bash
git tag                             # list all tags
git tag -l "v2.*"                   # list tags matching pattern
git tag -n                          # list with messages
git tag v2.0.0                      # create lightweight tag
git tag -a v2.0.0 -m "message"      # create annotated tag
git tag -s v2.0.0 -m "message"      # create signed tag
git tag -a v2.0.0 a1b2c3d           # tag a specific commit
git tag -d v2.0.0                   # delete local tag
git push origin v2.0.0              # push a tag
git push origin --tags              # push all tags
git push origin --delete v2.0.0     # delete remote tag
git checkout v2.0.0                 # checkout a tag (detached HEAD)
git show v2.0.0                     # show tag details
git verify-tag v2.0.0               # verify a signed tag
```

---

## Submodules

### git submodule 🔴

Manage embedded repositories.

```bash
git submodule add <url> path/       # add a submodule
git submodule init                  # initialise from .gitmodules
git submodule update                # checkout recorded commits
git submodule update --init         # init and update
git submodule update --recursive    # update nested submodules
git submodule update --remote       # update to latest upstream commits
git submodule status                # show current commit of each
git submodule foreach git pull      # run command in all submodules
git submodule deinit path/          # unregister a submodule
```

---

## Bisect

### git bisect 🔴

Binary search to find which commit introduced a bug.

```bash
git bisect start            # begin a bisect session
git bisect bad              # mark current commit as bad
git bisect good v2.0.0      # mark a known good commit
git bisect good             # mark current checkout as good
git bisect bad              # mark current checkout as bad
git bisect skip             # skip current commit
git bisect run ./test.sh    # automated bisect with a script
git bisect log              # show bisect session history
git bisect reset            # end session, return to original HEAD
```

---

## Advanced Operations

### git filter-repo 🔴

Rewrite Git history (requires separate installation).

```bash
pip install git-filter-repo

git filter-repo --path secret.env --invert-paths    # remove a file
git filter-repo --path-glob '*.pem' --invert-paths  # remove by pattern
git filter-repo --email-callback '...'              # rewrite emails
```

### git worktree 🔴

Work on multiple branches simultaneously without switching.

```bash
git worktree add ../hotfix hotfix/urgent    # create a linked working tree
git worktree list                           # list all working trees
git worktree remove ../hotfix               # remove a working tree
git worktree prune                          # remove stale worktree entries
```

### git sparse-checkout 🔴

Work with only a subset of files.

```bash
git sparse-checkout init --cone         # enable cone mode
git sparse-checkout set src/ docs/      # check out specific directories
git sparse-checkout add tests/          # add more directories
git sparse-checkout list                # show current patterns
git sparse-checkout disable             # disable sparse checkout
```

### git lfs 🔴

Manage large files (requires separate installation).

```bash
git lfs install                         # one-time setup per machine
git lfs track "*.psd"                   # track file type with LFS
git lfs ls-files                        # list LFS-tracked files
git lfs status                          # show LFS file status
git lfs pull                            # download LFS files
git lfs migrate import --include="*.psd" --everything   # migrate to LFS
```

### git maintenance 🔴

Run background repository maintenance.

```bash
git maintenance start               # schedule background maintenance
git maintenance run                 # run all maintenance tasks now
git maintenance run --task=gc       # run specific task
git maintenance stop                # stop background maintenance
```

### git gc 🔴

Run garbage collection manually.

```bash
git gc                      # run standard garbage collection
git gc --aggressive         # thorough but slow - run infrequently
git gc --auto               # run only if thresholds are met
git gc --prune=now          # remove unreachable objects immediately
```

### git bundle 🔴

Package repository data into a single file for offline transfer.

```bash
git bundle create repo.bundle --all         # bundle entire repository
git bundle create recent.bundle HEAD~10..HEAD  # bundle last 10 commits
git bundle verify repo.bundle               # verify a bundle
git clone repo.bundle my-repo               # clone from a bundle
git pull repo.bundle main                   # pull from a bundle
```

Useful for transferring repositories in air-gapped environments with no network access.

---

## Plumbing Commands

Low-level commands for scripting and inspection. Most developers rarely use these directly.

### git cat-file 🔴

Inspect Git objects.

```bash
git cat-file -t a1b2c3d     # show object type
git cat-file -p a1b2c3d     # show object contents
git cat-file -s a1b2c3d     # show object size
git cat-file --batch        # batch processing (read from stdin)
```

### git hash-object 🔴

Compute object hashes.

```bash
git hash-object filename.txt        # compute hash without storing
git hash-object -w filename.txt     # compute and store
git hash-object --stdin             # read from stdin
```

### git ls-files 🟡

List files in the index.

```bash
git ls-files                # list tracked files
git ls-files --stage        # show with blob hashes and stage numbers
git ls-files --others       # show untracked files
git ls-files --ignored      # show ignored files
git ls-files -m             # show modified files
git ls-files -d             # show deleted files
```

### git ls-tree 🔴

List tree contents.

```bash
git ls-tree HEAD            # list root tree at HEAD
git ls-tree HEAD src/       # list contents of a directory
git ls-tree -r HEAD         # recursive listing
git ls-tree --name-only HEAD   # show filenames only
```

### git rev-parse 🟡

Parse Git references into commit hashes.

```bash
git rev-parse HEAD                      # show HEAD's hash
git rev-parse main                      # show branch's hash
git rev-parse HEAD~3                    # show hash 3 commits back
git rev-parse --abbrev-ref HEAD         # show current branch name
git rev-parse --show-toplevel           # show repo root path
git rev-parse --is-inside-work-tree     # check if inside a git repo
```

### git rev-list 🔴

List commit objects.

```bash
git rev-list HEAD               # list all commits from HEAD
git rev-list HEAD~5..HEAD       # list last 5 commits
git rev-list --count HEAD       # count total commits
git rev-list --objects --all    # list all objects (for repo analysis)
```

### git update-index 🔴

Manipulate the index directly.

```bash
git update-index --assume-unchanged filename    # tell Git to ignore changes
git update-index --no-assume-unchanged filename # undo the above
git update-index --skip-worktree filename       # more robust version of above
```

### git verify-commit 🔴

Verify commit signatures.

```bash
git verify-commit a1b2c3d       # verify GPG or SSH signature
git log --show-signature        # show signatures in log
git log --format="%G?"          # show signature status per commit
```

### git fsck 🔴

Verify database integrity.

```bash
git fsck                        # check object database
git fsck --unreachable          # find unreachable objects
git fsck --lost-found           # write unreachable objects to .git/lost-found/
git fsck --dangling             # show dangling objects only
```

### git count-objects 🔴

Count objects and measure repository size.

```bash
git count-objects               # count loose objects
git count-objects -v            # verbose output
git count-objects -vH           # human-readable sizes
```

### git check-ignore 🟡

Check whether files are ignored and why.

```bash
git check-ignore -v filename    # show which .gitignore rule applies
git check-ignore -v .           # check all files in current directory
```

---

## Quick Reference Card

The commands used most often in everyday work:

| Command | What it does |
|---|---|
| `git status` | See what has changed |
| `git add .` | Stage all changes |
| `git commit -m "message"` | Save a snapshot |
| `git push` | Upload to remote |
| `git pull` | Download from remote |
| `git switch -c branch-name` | Create and switch to a branch |
| `git switch main` | Switch to main |
| `git merge feature` | Merge a branch into current |
| `git log --oneline` | View compact commit history |
| `git log --oneline --graph --all` | Visual branch graph |
| `git diff` | See unstaged changes |
| `git diff --staged` | See staged changes |
| `git blame filename` | See who wrote each line |
| `git stash` | Save work in progress |
| `git stash pop` | Restore stashed work |
| `git reset --soft HEAD~1` | Undo last commit (keep changes) |
| `git revert HEAD` | Safely undo a pushed commit |
| `git restore --staged .` | Unstage everything |
| `git restore .` | Discard all working directory changes |
| `git remote -v` | See configured remotes |
| `git fetch --prune` | Update remote references |
| `git tag -a v1.0.0 -m "msg"` | Create a release tag |
| `git reflog` | Find lost commits |
| `git bisect start` | Begin binary search for a bug |
| `git archive --format=zip HEAD > out.zip` | Export repo as zip |

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/docs) - the authoritative reference for every command
- [Pro Git book](https://git-scm.com/book/en/v2) - the complete free guide to Git
- [Git cheat sheet by GitHub](https://education.github.com/git-cheat-sheet-education.pdf) - a printable one-page reference
- [Git cheat sheet by Atlassian](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet) - another concise reference with visual examples

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

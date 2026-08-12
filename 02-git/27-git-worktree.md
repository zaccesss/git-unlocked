# Git Worktree

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

Git worktree lets you check out multiple branches simultaneously in separate directories on your machine - without stashing, without switching, without losing your place. Each worktree is a fully independent working directory linked to the same repository. It is one of Git's most underused features and one of the most genuinely useful for developers who regularly context-switch between branches.

---

## Table of Contents

- [What is git worktree?](#what-is-git-worktree)
- [Why worktrees exist](#why-worktrees-exist)
- [Adding a worktree](#adding-a-worktree)
- [Listing worktrees](#listing-worktrees)
- [Working inside a worktree](#working-inside-a-worktree)
- [Creating a new branch in a worktree](#creating-a-new-branch-in-a-worktree)
- [Removing a worktree](#removing-a-worktree)
- [Worktree with bare repositories](#worktree-with-bare-repositories)
- [Practical workflows](#practical-workflows)
- [Worktree limitations](#worktree-limitations)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is git worktree?

Every Git repository has one main working directory - the folder where you see and edit your files. `git worktree` lets you create additional working directories, each checked out to a different branch, all sharing the same underlying `.git` repository.

```
my-project/           <- main worktree (checked out: main)
  .git/               <- the single shared repository
  src/
  README.md

../my-project-hotfix/ <- linked worktree (checked out: hotfix/urgent)
  src/
  README.md

../my-project-feature/ <- linked worktree (checked out: feature/new-ui)
  src/
  README.md
```

All three directories share the same `.git` folder. They have separate working directories and staging areas, but identical history, branches and remotes. A commit made in any worktree is immediately visible in all others.

---

## Why Worktrees Exist

The traditional solution to "I need to work on two branches at once" involves one of:

- **Stashing** your current changes, switching branches, doing the work, switching back, unstashing
- **Cloning** the repository a second time into a separate folder (wastes disk space, two separate `.git` databases, pushing from the second clone is error-prone)
- **Committing incomplete work** just to switch branches (pollutes history)

Worktrees solve all of this cleanly. You keep your current branch exactly as it is and work on the other branch in a separate directory simultaneously. No stashing, no second clone, no interruption.

**The most common scenarios:**

You are mid-way through a feature when a critical hotfix is reported in production. Instead of stashing everything, you open a new terminal, `cd` to the hotfix worktree and fix it there - your feature branch is completely untouched.

You are reviewing a colleague's pull request and want to run it locally alongside your own work without switching away from your current branch.

You are maintaining multiple release versions and need to make the same fix to `release/v2` and `release/v3` simultaneously.

---

## Adding a Worktree

**Create a linked worktree for an existing branch:**

```bash
git worktree add ../project-hotfix hotfix/urgent
```

This creates a new directory `../project-hotfix` with the `hotfix/urgent` branch checked out. The path can be anywhere on your filesystem - it does not have to be a sibling of the main project.

**Create a worktree and a new branch at the same time:**

```bash
git worktree add -b feature/new-dashboard ../project-dashboard main
```

Creates the directory `../project-dashboard`, creates a new branch `feature/new-dashboard` based on `main` and checks it out there.

**Create a worktree in detached HEAD state:**

```bash
git worktree add --detach ../project-review HEAD~5
```

Useful for inspecting or building a specific commit without creating a branch.

**Create a worktree from a remote branch:**

```bash
git fetch origin
git worktree add ../project-pr-review origin/feature/colleague-work
```

---

## Listing Worktrees

```bash
git worktree list
```

Output:

```
/home/user/my-project           abc1234 [main]
/home/user/my-project-hotfix    def5678 [hotfix/urgent]
/home/user/my-project-dashboard ghi9012 [feature/new-dashboard]
```

The main worktree is always listed first. Each line shows the path, the current HEAD commit hash and the checked-out branch.

**Verbose output:**

```bash
git worktree list --verbose
```

Shows additional information including whether any worktree has a lock.

**Porcelain output (for scripting):**

```bash
git worktree list --porcelain
```

---

## Working Inside a Worktree

Once a worktree is created, working inside it is identical to working in the main project directory:

```bash
cd ../my-project-hotfix
git status          # shows status of the hotfix branch
git add .
git commit -m "fix: critical payment bug"
git push origin hotfix/urgent
```

All standard Git commands work inside a linked worktree. The `git log`, `git branch`, `git remote` and all other commands show the same repository state as the main worktree because they share the same `.git` database.

**Switching between worktrees** is simply switching directories in your terminal:

```bash
cd ~/my-project           # back to main branch work
cd ~/my-project-hotfix    # back to hotfix work
```

Many developers open each worktree in a separate terminal tab or IDE window.

---

## Creating a New Branch in a Worktree

The `-b` flag creates a new branch in the worktree:

```bash
git worktree add -b feature/payment-redesign ../project-payments main
```

This is equivalent to:

```bash
git branch feature/payment-redesign main
git worktree add ../project-payments feature/payment-redesign
```

You can also base the new branch on any existing branch, tag or commit:

```bash
git worktree add -b hotfix/v2.1.1 ../project-v2-fix v2.1.0
```

Creates a new branch `hotfix/v2.1.1` starting from the tag `v2.1.0`.

---

## Removing a Worktree

**Remove a linked worktree:**

```bash
git worktree remove ../my-project-hotfix
```

This deletes the directory and cleans up the worktree reference in `.git`. Git will refuse to remove a worktree with uncommitted changes unless you use `--force`.

**Force remove a worktree (discards uncommitted changes):**

```bash
git worktree remove --force ../my-project-hotfix
```

> [!WARNING]
> `--force` permanently discards any uncommitted changes in the worktree. Make sure you have committed or stashed anything you need before force-removing.

**Prune stale worktree references:**

If you deleted a worktree directory manually (without using `git worktree remove`), Git may still have a stale reference to it. Clean these up with:

```bash
git worktree prune
```

Git automatically prunes stale worktrees during `git gc`, but running `prune` manually clears them immediately.

---

## Worktree with Bare Repositories

A popular advanced pattern combines bare repositories with worktrees. Instead of a normal clone (which has a working directory), you clone as a bare repository (only the `.git` database, no working files) and then create worktrees for every branch you need:

**Clone as bare:**

```bash
git clone --bare https://github.com/YOUR_USERNAME/YOUR_REPO.git project.git
cd project.git
```

**Add worktrees for the branches you work on:**

```bash
git worktree add ../project-main main
git worktree add ../project-feature feature/new-ui
```

This pattern keeps all working directories as siblings with the bare `.git` database at the centre. It is particularly clean for maintaining multiple long-running branches (multiple release versions, for example).

```
project.git/        <- bare repository (no working files)
project-main/       <- worktree: main branch
project-feature/    <- worktree: feature/new-ui branch
project-v2/         <- worktree: release/v2 branch
```

---

## Practical Workflows

### Hotfix while mid-feature

```bash
# You are working on feature/new-dashboard
# A critical bug is reported in production

# Create a hotfix worktree without touching your feature work
git worktree add -b hotfix/login-crash ../project-hotfix main

# Fix the bug in the hotfix worktree
cd ../project-hotfix
# ... make fix ...
git add . && git commit -m "fix: login crash on empty email"
git push origin hotfix/login-crash

# Open a PR from hotfix/login-crash → main
# Then return to your feature work - nothing was disturbed
cd ../my-project
git status   # feature branch exactly as you left it
```

### Reviewing a pull request locally

```bash
# A colleague opened PR #42 from feature/payment-redesign
git fetch origin
git worktree add ../project-pr-42 origin/feature/payment-redesign

cd ../project-pr-42
# Run the app, run tests, inspect the code
npm install && npm test

# When done
cd ../my-project
git worktree remove ../project-pr-42
```

### Maintaining multiple release branches

```bash
# Set up permanent worktrees for each active release
git worktree add ../project-v2 release/v2
git worktree add ../project-v3 release/v3

# Apply a security fix to both releases
cd ../project-v2
git cherry-pick <fix-commit-hash>
git push origin release/v2

cd ../project-v3
git cherry-pick <fix-commit-hash>
git push origin release/v3
```

---

## Worktree Limitations

**The same branch cannot be checked out in two worktrees simultaneously.**

If `main` is checked out in the main worktree, you cannot create a second worktree also on `main`. Git enforces this to prevent conflicts between the two staging areas.

```bash
git worktree add ../other-main main
# error: 'main' is already checked out at '/home/user/my-project'
```

**Each worktree has its own staging area (index).**

Changes staged in one worktree are not visible in another. This is by design - each worktree is independent.

**Some operations are restricted in linked worktrees.**

You cannot run `git clone` inside a linked worktree. Operations that reconfigure the repository (like changing the HEAD of another worktree) are not allowed.

**IDE support varies.**

Some IDEs handle worktrees well (VS Code with multiple windows, terminal-based editors). Others may get confused if you open a worktree directory without recognising it as part of the same repository.

**File watchers may see unexpected changes.**

If your build system or editor watches the filesystem, it may detect changes made in a linked worktree as changes in the project root. Usually a minor inconvenience.

---

## Try It Yourself

**Step 1.** Create a repository with two branches:

**Windows (PowerShell):**

```powershell
mkdir worktree-practice && cd worktree-practice && git init
echo "# Main project" > README.md
git add README.md && git commit -m "add: initial README"
git switch -c feature/new-feature
echo "new feature work" > feature.md
git add feature.md && git commit -m "add: feature work in progress"
git switch main
```

**Mac and Linux:**

```bash
mkdir worktree-practice && cd worktree-practice && git init
echo "# Main project" > README.md
git add README.md && git commit -m "add: initial README"
git switch -c feature/new-feature
echo "new feature work" > feature.md
git add feature.md && git commit -m "add: feature work in progress"
git switch main
```

**Step 2.** Add a worktree for the feature branch:

```bash
git worktree add ../worktree-feature feature/new-feature
```

**Step 3.** List all worktrees:

```bash
git worktree list
```

You should see both the main directory and the new worktree listed.

**Step 4.** Verify the worktree has the feature branch checked out:

**Windows:**

```powershell
cd ../worktree-feature
git branch
ls
```

**Mac and Linux:**

```bash
cd ../worktree-feature
git branch
ls
```

You should see `feature.md` in this directory but not in the main directory.

**Step 5.** Make a commit in the worktree and verify it appears in the main repository:

```bash
echo "more feature work" >> feature.md
git add feature.md && git commit -m "update: more feature work"
cd ../worktree-practice
git log --oneline --all
```

The new commit appears in the log even though you made it in the other directory.

**Step 6.** Create a hotfix worktree based on main:

```bash
git worktree add -b hotfix/quick-fix ../worktree-hotfix main
git worktree list
```

**Step 7.** Remove the worktrees:

```bash
git worktree remove ../worktree-feature
git worktree remove ../worktree-hotfix
git worktree list
```

Only the main worktree should remain.

**Step 8.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q worktree-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf worktree-practice
```

---

## Common Mistakes

**Trying to check out the same branch in two worktrees.**

Git prevents this. If you need to compare two states of the same branch, use `git stash`, create a temporary branch or use `git show` to inspect files without checking them out.

**Deleting a worktree directory manually instead of using `git worktree remove`.**

Manually deleting the directory leaves a stale entry in `.git/worktrees/`. Run `git worktree prune` to clean these up or use `git worktree remove` in the first place.

**Forgetting that each worktree has its own index.**

If you stage changes in one worktree and switch to another, those staged changes are not there. Each worktree's staging area is completely independent.

**Opening both worktrees as separate projects in an IDE that does not handle this well.**

Some IDEs treat the two directories as unrelated projects and may not understand that they share a `.git` directory. VS Code handles this well with multiple windows. If your IDE behaves strangely, use a terminal for the worktree operations.

**Not pruning stale worktrees.**

After `git gc` or manual cleanup, old worktree references can accumulate. Run `git worktree prune` periodically to keep things clean or just let `git gc` handle it.

---

## Summary

- `git worktree add <path> <branch>` creates a new working directory checked out to a different branch
- All worktrees share the same `.git` repository - one database, multiple working directories
- The same branch cannot be checked out in two worktrees simultaneously
- Each worktree has its own independent staging area
- `git worktree list` shows all active worktrees with their paths, commit hashes and branches
- `git worktree remove <path>` removes a linked worktree and cleans up its reference
- `git worktree prune` removes stale references left by manually deleted worktree directories
- Bare repository + worktrees is a clean pattern for managing multiple long-running branches
- Primary use cases: hotfixes while mid-feature, local PR review, maintaining multiple release branches

---

## Sources and Further Reading

- [Official git worktree documentation](https://git-scm.com/docs/git-worktree) - the complete reference for all worktree commands and options
- [Pro Git book, Chapter 3.5: Git Tools - Multiple Worktrees](https://git-scm.com/docs/git-worktree) - official guide with examples
- [Atlassian - git worktree](https://www.atlassian.com/git/tutorials/git-worktree) - practical guide to worktrees with workflow examples

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

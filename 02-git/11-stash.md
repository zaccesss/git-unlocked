# Stash

**Difficulty:** 🟡 Intermediate | **Time:** 20 minutes

Stashing lets you save your work-in-progress without committing it. It is the solution to one of the most common situations in development: you are halfway through something when you suddenly need to switch context - fix an urgent bug, review a colleague's branch or pull the latest changes. Stash saves your unfinished work, clears your working directory and lets you come back to it later.

---

## Table of Contents

- [What is stashing?](#what-is-stashing)
- [Basic stash workflow](#basic-stash-workflow)
- [Stashing with a message](#stashing-with-a-message)
- [Viewing your stashes](#viewing-your-stashes)
- [Applying a stash](#applying-a-stash)
- [Popping a stash](#popping-a-stash)
- [Dropping a stash](#dropping-a-stash)
- [Stashing untracked and ignored files](#stashing-untracked-and-ignored-files)
- [Stashing specific files](#stashing-specific-files)
- [Creating a branch from a stash](#creating-a-branch-from-a-stash)
- [Stash and branch switching](#stash-and-branch-switching)
- [How stash works internally](#how-stash-works-internally)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is Stashing?

Stashing takes all your uncommitted changes - both staged and unstaged - and saves them onto a stack. Your working directory is restored to match the last commit, giving you a clean state. When you are ready to return to your work, you pop the stash back and your changes are restored exactly as they were.

Think of it like putting your work in a drawer. You clear your desk, deal with something else and then open the drawer and pick up exactly where you left off.

The stash is stored locally - it is not pushed to the remote when you run `git push`. Stashes are personal to your machine.

---

## Basic Stash Workflow

**Save your current changes to the stash:**

```bash
git stash
```

This stashes all modified tracked files and staged changes. Your working directory is now clean.

**Do whatever you needed to do** - switch branches, pull changes, fix a bug, review someone else's work.

**Restore your stashed changes:**

```bash
git stash pop
```

Your changes are back exactly as they were. The stash entry is removed from the stack.

That is the core workflow. Everything else in this file builds on these two commands.

---

## Stashing with a Message

When you have multiple stashes, the default message (`WIP on branch-name: hash commit message`) is not very descriptive. Add a message to identify stashes easily:

```bash
git stash push -m "half-finished login form validation"
```

Or the older syntax which does the same thing:

```bash
git stash save "half-finished login form validation"
```

The message appears when you list your stashes, making it easy to find the right one later.

---

## Viewing Your Stashes

**List all stashes:**

```bash
git stash list
```

Output:

```
stash@{0}: On main: half-finished login form validation
stash@{1}: WIP on feature/dashboard: a1b2c3d add: sidebar component
stash@{2}: WIP on main: e4f5g6h update: homepage layout
```

Stashes are numbered from newest (`stash@{0}`) to oldest. Every time you stash, the new stash becomes `stash@{0}` and existing stashes shift up by one number.

**Show the contents of the most recent stash:**

```bash
git stash show
```

**Show the full diff of the most recent stash:**

```bash
git stash show -p
```

**Show a specific stash:**

```bash
git stash show stash@{2}
```

**Show the full diff of a specific stash:**

```bash
git stash show -p stash@{2}
```

---

## Applying a Stash

`git stash apply` restores the stashed changes but keeps the stash entry in the list. Use this when you want to apply the same changes to multiple branches or when you want to keep the stash as a backup.

**Apply the most recent stash:**

```bash
git stash apply
```

**Apply a specific stash:**

```bash
git stash apply stash@{2}
```

After applying, the stash remains in the list. You can apply it again later if needed or drop it when you no longer need it.

---

## Popping a Stash

`git stash pop` applies the stash and removes it from the list in one step. This is the most common way to restore stashed work when you only need it once.

**Pop the most recent stash:**

```bash
git stash pop
```

**Pop a specific stash:**

```bash
git stash pop stash@{2}
```

> [!NOTE]
> If applying the stash causes a conflict, Git leaves the stash in the list even after `git stash pop`. This is deliberate - the stash is kept as a safety net until you resolve the conflict and confirm everything is correct. After resolving, drop it manually with `git stash drop`.

---

## Dropping a Stash

**Drop the most recent stash:**

```bash
git stash drop
```

**Drop a specific stash:**

```bash
git stash drop stash@{1}
```

**Drop all stashes at once:**

```bash
git stash clear
```

> [!WARNING]
> Dropped stashes cannot be recovered through normal Git commands. If you accidentally drop a stash you needed, it may be recoverable via `git fsck --unreachable` followed by `git show` on the returned hashes - but this is not guaranteed and requires the objects to still be in Git's object database before garbage collection runs. Do not rely on it. Be sure before you drop.

---

## Stashing Untracked and Ignored Files

By default, `git stash` only saves changes to files Git is already tracking. New files you have not yet run `git add` on are left behind.

**Include untracked files in the stash:**

```bash
git stash -u
```

Or the long form:

```bash
git stash --include-untracked
```

**Include both untracked and ignored files:**

```bash
git stash -a
```

Or:

```bash
git stash --all
```

> [!NOTE]
> You rarely need `--all`. Including ignored files (build output, compiled artefacts, `.DS_Store`) in your stash is usually unnecessary and makes the stash slower to create and restore.

---

## Stashing Specific Files

Sometimes you only want to stash some of your changes, not all of them.

**Stash specific files:**

```bash
git stash push -m "login form work" -- login.html login.css
```

The `--` separates the stash options from the file paths.

**Stash changes interactively (choose which changes to stash):**

```bash
git stash push -p
```

This opens an interactive prompt for each change in your working directory, similar to `git add -p`. For each change you choose `y` to stash it or `n` to leave it in the working directory.

---

## Creating a Branch from a Stash

If you stashed changes and then continued working on the original branch to the point where applying the stash would cause conflicts, `git stash branch` is the solution. It creates a new branch starting from the commit that was HEAD when you stashed, applies the stash to that new branch and drops the stash if successful.

```bash
git stash branch feature/login-form stash@{0}
```

This is also useful when you realise the stashed work should have been on its own branch all along.

---

## Stash and Branch Switching

One of the most common uses of stash is switching branches when you have uncommitted changes.

**The scenario:**

You are on `feature/dashboard` with uncommitted changes. You need to urgently switch to `main` to fix a bug. Git may refuse to switch if the changes conflict with the other branch.

**The solution:**

```bash
git stash
git switch main
# fix the bug
git switch feature/dashboard
git stash pop
```

Your changes are waiting for you exactly where you left them.

> [!TIP]
> Git will actually allow you to switch branches with uncommitted changes if there is no conflict - it carries the changes over to the new branch. However this can be confusing and lead to accidentally committing changes on the wrong branch. Using stash makes the intent explicit and keeps your branches clean.

---

## How Stash Works Internally

Understanding what stash does internally helps explain some of its behaviour.

When you run `git stash`, Git creates two commits that are not on any branch:

- One commit for the staged changes (the index state)
- One commit for the unstaged changes (the working tree state)

These commits are stored under `refs/stash`. The stash list you see with `git stash list` is the history of this special ref.

This is why stash entries survive branch switching, repository cloning operations and other Git operations - they are real commits, just not attached to any branch.

It is also why stash is not pushed with `git push`. The `refs/stash` ref is not included in normal push and fetch operations.

---

## Try It Yourself

**Step 1.** Create a repository with an initial commit:

**Windows (PowerShell):**

```powershell
mkdir stash-practice && cd stash-practice && git init
echo "# Stash Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir stash-practice && cd stash-practice && git init
echo "# Stash Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Start working on something:

**Windows:**

```powershell
echo "Work in progress..." > feature.md
git add feature.md
echo "More changes" >> README.md
```

**Mac and Linux:**

```bash
echo "Work in progress..." > feature.md
git add feature.md
echo "More changes" >> README.md
```

You now have a staged change (`feature.md`) and an unstaged change (`README.md`).

**Step 3.** Check the status:

```bash
git status
```

**Step 4.** Stash everything with a message:

```bash
git stash push -m "work in progress on feature"
```

**Step 5.** Check the status again - your working directory is clean:

```bash
git status
```

**Step 6.** List your stashes:

```bash
git stash list
```

**Step 7.** Do some other work on main:

```bash
echo "Urgent fix" >> README.md
git add README.md && git commit -m "fix: urgent change to README"
```

**Step 8.** Restore your stashed work:

```bash
git stash pop
```

**Step 9.** Check the status - your changes are back:

```bash
git status
```

Both `feature.md` and the changes to `README.md` should be restored. Note that staged changes may come back as unstaged - this is normal behaviour.

**Step 10.** Check there are no remaining stashes:

```bash
git stash list
```

**Step 11.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q stash-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf stash-practice
```

---

## Common Mistakes

**Forgetting you have stashes.**

Stashes are easy to forget about, especially if you created them weeks ago. Run `git stash list` periodically to review what you have saved. Old stashes that are no longer needed should be dropped to keep things tidy.

**Using stash as a long-term storage solution.**

Stash is designed for short-term context switching, not long-term work storage. If you are stashing something for more than a day or two, consider committing it to a branch instead - even with a `wip:` prefix. Commits are more visible, have messages and are included in your backup when you push. Stashes are not pushed.

**Expecting stash to be shared with teammates.**

Stashes are local. They are not pushed to the remote and teammates cannot see them. If you need to share work in progress, commit it to a branch and push.

**Not knowing which stash is which.**

Default stash messages like `WIP on main: a1b2c3d update: README` are not very helpful when you have several. Always use `git stash push -m "descriptive message"` to label what you are saving.

**Losing a stash after a conflict.**

When `git stash pop` causes a conflict, Git keeps the stash in the list. Resolve the conflict, stage the resolved files and then drop the stash manually with `git stash drop`. Do not pop it again - it is already applied.

**Stashing when you should just commit.**

Some developers over-use stash when a `wip:` commit would be better. A commit with the message `wip: login form - incomplete` is safer (it is pushed, it has a timestamp, it is on a branch) and can be amended or squashed before the final merge. Reserve stash for genuinely temporary context switching.

---

## Summary

- `git stash` saves uncommitted changes and cleans your working directory
- `git stash pop` restores the most recent stash and removes it from the list
- `git stash apply` restores a stash without removing it from the list
- `git stash push -m "message"` saves a stash with a descriptive label
- `git stash list` shows all saved stashes
- `git stash drop` removes a stash; `git stash clear` removes all stashes
- `git stash -u` includes untracked files; `git stash -a` includes ignored files too
- `git stash push -p` lets you interactively choose which changes to stash
- `git stash branch branch-name` creates a new branch from a stash
- Stashes are local - they are not pushed to the remote
- Use stash for short-term context switching; use commits on branches for longer-term work in progress

---

## Sources and Further Reading

- [Official git stash documentation](https://git-scm.com/docs/git-stash) - the complete reference for all stash options and flags
- [Pro Git book, Chapter 7.3: Stashing and Cleaning](https://git-scm.com/book/en/v2/Git-Tools-Stashing-and-Cleaning) - the official in-depth guide to stashing
- [Atlassian - Git stash](https://www.atlassian.com/git/tutorials/saving-changes/git-stash) - clear walkthrough of stash workflows with examples

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

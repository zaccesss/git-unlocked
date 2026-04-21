# Rebase

**Difficulty:** 🔴 Advanced | **Time:** 35 minutes

Rebase is one of Git's most powerful and most misunderstood features. Used correctly it produces clean, readable history. Used incorrectly it causes serious problems for collaborators. This file explains exactly what rebase does, when to use it, when to avoid it and how to use interactive rebase to rewrite history with surgical precision.

---

## Table of Contents

- [What is rebase?](#what-is-rebase)
- [Rebase vs merge](#rebase-vs-merge)
- [Basic rebase](#basic-rebase)
- [Interactive rebase](#interactive-rebase)
- [Rebase commands in detail](#rebase-commands-in-detail)
- [Rebasing onto a different base](#rebasing-onto-a-different-base)
- [git pull --rebase](#git-pull---rebase)
- [Handling conflicts during rebase](#handling-conflicts-during-rebase)
- [Aborting a rebase](#aborting-a-rebase)
- [The golden rule of rebasing](#the-golden-rule-of-rebasing)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is Rebase?

Rebase moves or replays a sequence of commits onto a new base commit. Instead of creating a merge commit to join two branches, rebase rewrites the feature branch commits as if they were made after the latest commit on the target branch.

The name describes what it does: it changes the **base** commit that your branch starts from.

When you create a feature branch, it starts from a specific commit on `main`. As you work on your branch, other commits may land on `main`. Rebasing takes your branch's commits and replays them on top of the latest `main` commit, making it appear as though you started your work today rather than last week.

**Before rebase:**

```
main
 |
 A -- B -- C -- F -- G
               \
                D -- E
                     |
              feature/login
```

**After rebasing `feature/login` onto `main`:**

```
main
 |
 A -- B -- C -- F -- G
                      \
                       D' -- E'
                              |
                       feature/login
```

`D` and `E` become `D'` and `E'` - new commits with new hashes but the same changes. The branch now starts from `G` (the latest commit on `main`) instead of `C` (where it originally branched).

---

## Rebase vs Merge

Both rebase and merge integrate changes from one branch into another. The end result in your working directory is identical. The difference is entirely in the history.

**Merge preserves history faithfully.**

A merge commit records exactly when two branches came together. The history shows the parallel development that actually happened. It is accurate but can look complex in a project with many branches.

```
 A -- B -- C -- F -- G -- M (merge commit)
               \         /
                D -- E --
```

**Rebase creates a clean linear history.**

After rebasing and merging, the history looks as if the feature was built sequentially after everything else, with no branching at all.

```
 A -- B -- C -- F -- G -- D' -- E'
```

**Which to use:**

| Situation | Use |
|---|---|
| Integrating a completed feature into `main` | Merge (preserves history accurately) |
| Updating a feature branch with `main` changes during development | Rebase (keeps the branch clean) |
| Preparing commits for a pull request review | Interactive rebase (clean up messy history) |
| Shared branches that others are working on | Never rebase |
| Your own local branches not yet pushed | Either, your choice |

The most common pattern in professional teams: rebase your feature branch onto `main` to keep it current during development, then use a merge (via pull request) to integrate it when it is complete.

---

## Basic Rebase

**Rebase the current branch onto `main`:**

```bash
git switch feature/login
git rebase main
```

This replays all commits on `feature/login` on top of the latest commit on `main`.

**What happens step by step:**

1. Git finds the common ancestor of `feature/login` and `main`
2. Git saves the commits on `feature/login` since that ancestor as temporary patches
3. Git resets `feature/login` to point at the latest commit on `main`
4. Git applies each saved patch one at a time, creating new commits

If any patch applies cleanly, Git moves to the next one. If a patch conflicts with something on `main`, Git pauses and asks you to resolve the conflict.

**After rebasing, the branch is linear:**

```bash
git log --oneline --graph
```

No merge commits. Clean history showing only your feature branch commits on top of `main`.

---

## Interactive Rebase

Interactive rebase (`git rebase -i`) is one of the most useful tools in Git. It lets you rewrite the history of your commits before they are shared - combining messy work-in-progress commits into clean, logical ones that tell a clear story.

**Start an interactive rebase for the last N commits:**

```bash
git rebase -i HEAD~3
```

This opens your configured editor showing the last 3 commits:

```
pick a1b2c3d add: login form HTML
pick e4f5g6h wip: validation not working yet
pick i7j8k9l fix: validation now working, also fixed typo

# Rebase f0e1d2c..i7j8k9l onto f0e1d2c (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# d, drop <commit> = remove commit
# l, label <commit> = label current HEAD with a name
# t, reset <commit> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
```

You edit this file to control what happens to each commit, save and close. Git executes your instructions.

**Interactive rebase onto a branch:**

```bash
git rebase -i main
```

This opens an editor showing all commits on the current branch that are not on `main`.

---

## Rebase Commands in Detail

### pick

Keep the commit exactly as it is. The default - leaving a line unchanged is the same as `pick`.

```
pick a1b2c3d add: login form HTML
```

### reword (r)

Keep the commit's changes but edit the commit message. Git applies the commit and then opens your editor for the new message.

```
reword a1b2c3d add: login form HTML
```

Use this to fix a typo in a commit message, or to make a vague message more descriptive.

### edit (e)

Pause the rebase after applying this commit so you can amend it. You can stage additional changes, unstage things, or split the commit into multiple commits.

```
edit a1b2c3d add: login form HTML
```

When Git pauses, you are in a detached HEAD state at that commit. Make your changes, then:

```bash
git add .
git commit --amend
git rebase --continue
```

Or to split the commit into two:

```bash
git reset HEAD~1      # unstage the commit's changes
git add login.html
git commit -m "add: login form HTML structure"
git add validation.js
git commit -m "add: login form validation"
git rebase --continue
```

### squash (s)

Combine this commit with the commit above it. Git opens your editor to write a new commit message that combines both.

```
pick a1b2c3d add: login form HTML
squash e4f5g6h add: login form CSS
squash i7j8k9l add: login form validation
```

Result: one commit containing all three sets of changes, with a combined message you write.

### fixup (f)

Like squash, but silently discards this commit's message. The result uses only the picked commit's message. Faster than squash when you do not need to rewrite the message.

```
pick a1b2c3d add: login form HTML
fixup e4f5g6h wip: forgot to save
fixup i7j8k9l fix: typo
```

Result: one clean commit with the message `add: login form HTML`.

### drop (d)

Remove the commit entirely. The changes are gone.

```
drop a1b2c3d add: debug logging
```

Use this to remove debugging code, console logs or test commits that should not be part of the final history.

### Reordering commits

You can reorder commits by simply reordering the lines in the editor:

```
pick i7j8k9l add: login form validation   <- was third, now first
pick a1b2c3d add: login form HTML         <- was first, now second
pick e4f5g6h add: login form CSS          <- was second, now third
```

Git applies them in the new order. This can cause conflicts if commits depend on each other - Git will pause and ask you to resolve them.

---

## Rebasing onto a Different Base

The `--onto` flag lets you rebase a range of commits onto any target, not just the tip of another branch. This is useful for complex history restructuring.

**Move a branch from one base to another:**

```bash
git rebase --onto main feature/old-base feature/my-feature
```

This takes all commits on `feature/my-feature` that are not on `feature/old-base` and replays them onto `main`. Useful when you accidentally branched off the wrong branch.

**Before:**

```
main -- A -- B
              \
               C -- D (feature/old-base)
                     \
                      E -- F (feature/my-feature)
```

**After `git rebase --onto main feature/old-base feature/my-feature`:**

```
main -- A -- B -- E' -- F' (feature/my-feature)
              \
               C -- D (feature/old-base)
```

`E` and `F` are now based on `B` (the tip of `main`) rather than `D`.

---

## git pull --rebase

`git pull --rebase` fetches remote changes and rebases your local commits on top of them instead of creating a merge commit.

```bash
git pull --rebase
```

**Without `--rebase`:**

```
origin/main: A -- B -- C -- D
local/main:  A -- B -- E -- F

After git pull: A -- B -- C -- D -- M (merge commit)
                              \    /
                               E -- F
```

**With `--rebase`:**

```
origin/main: A -- B -- C -- D
local/main:  A -- B -- E -- F

After git pull --rebase: A -- B -- C -- D -- E' -- F'
```

The local commits are replayed on top of the remote commits. No merge commit, linear history.

**Set rebase as the default pull behaviour:**

```bash
git config --global pull.rebase true
```

> [!NOTE]
> `git pull --rebase` is generally safe for personal feature branches. Be careful using it on shared branches - if your local commits have already been pushed and pulled by others, rebasing them will cause divergence.

---

## Handling Conflicts During Rebase

When a commit being replayed conflicts with something on the target branch, Git pauses the rebase and marks the conflicts in the affected files - exactly like a merge conflict.

**When a conflict occurs:**

```
CONFLICT (content): Merge conflict in login.html
error: could not apply a1b2c3d... add: login form validation
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
```

**Resolve the conflict:**

1. Open the conflicted file and fix it (remove conflict markers, keep the correct version)
2. Stage the resolved file:

```bash
git add login.html
```

3. Continue the rebase:

```bash
git rebase --continue
```

Git applies the next commit. If that also conflicts, repeat the process. If there are no more conflicts, the rebase completes.

**Skip a commit:**

If a commit's changes are no longer relevant after the rebase (for example, the conflict resolution makes the commit's changes redundant), you can skip it:

```bash
git rebase --skip
```

**Abort the rebase entirely:**

```bash
git rebase --abort
```

This returns your repository to the exact state it was in before you started the rebase.

---

## The Golden Rule of Rebasing

> [!CAUTION]
> **Never rebase commits that have been pushed to a shared remote branch.**

This is the single most important rule in all of Git rebasing.

When you rebase, you create new commits with new hashes. The old commits still exist in anyone else's clone. If you force push the rebased commits to the remote, the remote now has different commits than what your colleagues have pulled. Their history has diverged from yours. The next time they try to pull or push, Git cannot reconcile the divergence cleanly and they face a painful, confusing situation.

**Safe to rebase:**
- Local commits that have never been pushed
- Commits on a branch that only you are working on
- Commits on a branch before opening a pull request

**Never rebase:**
- `main` or any shared branch
- Any branch that has been pushed and that others have pulled
- Any branch that is the base for someone else's branch

**If you must clean up commits that have been pushed (on your own branch, not shared):**

```bash
git rebase -i HEAD~3
# clean up the commits
git push --force-with-lease
```

`--force-with-lease` is safer than `--force` - it fails if the remote has commits you do not have locally, preventing you from overwriting someone else's work.

---

## Try It Yourself

**Part 1 - Basic rebase**

**Step 1.** Set up a repository:

**Windows (PowerShell):**

```powershell
mkdir rebase-practice && cd rebase-practice && git init
echo "# Project" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir rebase-practice && cd rebase-practice && git init
echo "# Project" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Create a feature branch and add commits:

```bash
git switch -c feature/login
echo "login form" > login.html
git add login.html && git commit -m "add: login form"
echo "login styles" > login.css
git add login.css && git commit -m "add: login styles"
```

**Step 3.** Add commits to main while you were working:

```bash
git switch main
echo "homepage" > index.html
git add index.html && git commit -m "add: homepage"
echo "global styles" > style.css
git add style.css && git commit -m "add: global styles"
```

**Step 4.** View the diverged history:

```bash
git log --oneline --graph --all
```

**Step 5.** Rebase the feature branch onto main:

```bash
git switch feature/login
git rebase main
```

**Step 6.** View the linear history:

```bash
git log --oneline --graph --all
```

The feature branch commits now appear after the main commits. Linear, clean history.

---

**Part 2 - Interactive rebase**

**Step 7.** Add some messy commits to the feature branch:

```bash
echo "validation" >> login.html
git add login.html && git commit -m "wip: working on validation"
echo "more validation" >> login.html
git add login.html && git commit -m "fix: forgot something"
echo "done" >> login.html
git add login.html && git commit -m "finally done"
```

**Step 8.** View the messy history:

```bash
git log --oneline
```

**Step 9.** Clean it up with interactive rebase (last 3 commits):

```bash
git rebase -i HEAD~3
```

In your editor, change it to:

```
pick [hash] wip: working on validation
fixup [hash] fix: forgot something
fixup [hash] finally done
```

Then reword the first commit to something clean like `add: login form validation`.

**Step 10.** View the cleaned history:

```bash
git log --oneline
```

Three messy commits are now one clean commit.

**Step 11.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q rebase-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf rebase-practice
```

---

## Common Mistakes

**Rebasing shared branches.**

The golden rule exists for a reason. Rebasing `main`, `develop` or any branch that multiple people have pulled rewrites history that others depend on. Their clones diverge from the remote. The fix is painful for everyone involved.

**Using `git push --force` instead of `--force-with-lease`.**

After an interactive rebase, your branch's history differs from the remote and a normal push will be rejected. It is tempting to use `--force` to override this. Use `--force-with-lease` instead - it fails if the remote has commits you do not have, protecting against accidental overwrites.

**Starting an interactive rebase and not knowing how to exit the editor.**

If Git opens Vim for the rebase todo list and you do not know how to save and exit:
- Save and exit: `Esc`, then `:wq`, then Enter
- Exit without saving (aborts the rebase): `Esc`, then `:q!`, then Enter

Set a more comfortable editor with:

```bash
git config --global core.editor "nano"
```

Or VS Code:

```bash
git config --global core.editor "code --wait"
```

**Rebasing with unstaged or uncommitted changes.**

If you have uncommitted changes when you try to rebase, Git will refuse. Stash your changes first:

```bash
git stash
git rebase main
git stash pop
```

**Squashing the first commit in a rebase.**

You cannot squash a commit into one that comes before it if it is the first commit in the rebase range. `squash` and `fixup` require a commit above them to merge into. If you want to squash everything, change all but the first line to `fixup` and leave the first as `pick`.

**Losing work after a confusing rebase.**

If you made a mistake during a rebase and cannot figure out where you are, `git reflog` saves you. It records every position HEAD has been at, including before the rebase started. Find the pre-rebase commit and reset to it:

```bash
git reflog
git reset --hard HEAD@{5}
```

---

## Summary

- Rebase moves commits from one branch so they start from a different base commit, creating a linear history
- `git rebase main` replays the current branch's commits on top of `main`
- Interactive rebase (`git rebase -i`) lets you rewrite commits: squash, fixup, reword, edit, drop or reorder
- Use `pick` to keep a commit, `squash`/`fixup` to combine, `reword` to edit the message, `drop` to remove
- `git pull --rebase` fetches and replays your local commits on top of remote commits without a merge commit
- During a rebase conflict: fix the file, `git add`, then `git rebase --continue`
- `git rebase --abort` cancels and returns to the pre-rebase state at any point
- The golden rule: **never rebase commits that have been pushed to a shared branch**
- Use `git push --force-with-lease` (not `--force`) after rebasing a branch you own

---

## Sources and Further Reading

- [Official git rebase documentation](https://git-scm.com/docs/git-rebase) - the complete reference for all rebase options
- [Pro Git book, Chapter 3.6: Rebasing](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) - the official guide to rebasing with diagrams
- [Atlassian - Merging vs rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing) - detailed comparison with visual diagrams showing when to use each
- [Interactive rebase documentation](https://git-scm.com/docs/git-rebase#_interactive_mode) - full reference for interactive rebase commands
- [Oh Shit, Git!?!](https://ohshitgit.com) - practical recovery steps for when a rebase goes wrong

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

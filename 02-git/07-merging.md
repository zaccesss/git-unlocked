# Merging

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Merging is how you bring work from one branch into another. It is the natural conclusion of the branching workflow - you build something on a branch, and when it is ready, you merge it back. Understanding how Git merges, and what happens in each scenario, makes you a much more confident collaborator.

---

## Table of Contents

- [What is merging?](#what-is-merging)
- [The three types of merge](#the-three-types-of-merge)
- [How to merge](#how-to-merge)
- [Fast-forward merges](#fast-forward-merges)
- [Three-way merges](#three-way-merges)
- [Squash merges](#squash-merges)
- [Merge commit messages](#merge-commit-messages)
- [Undoing a merge](#undoing-a-merge)
- [Merging in IDEs](#merging-in-ides)
- [Merge vs rebase](#merge-vs-rebase)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is Merging?

Merging combines the history of two branches into one. When you have finished working on a feature branch, you merge it into `main` so that the feature becomes part of the main codebase.

Git finds the point where the two branches diverged - called the **merge base** or **common ancestor** - and combines everything that happened on each branch since that point.

The result depends on what changed on each branch since they diverged:

- If only one branch has new commits, Git can fast-forward
- If both branches have new commits, Git creates a merge commit
- If both branches changed the same lines of the same file differently, Git pauses and asks you to resolve the conflict

Conflicts are covered in the next file: [git/08-merge-conflicts.md](08-merge-conflicts.md). This file covers everything up to that point.

---

## The Three Types of Merge

### Fast-forward merge

Happens when the branch being merged has a linear path ahead of the current branch - there are no new commits on the current branch since the feature branch was created. Git simply moves the current branch pointer forward.

### Three-way merge

Happens when both branches have new commits since they diverged. Git uses the two branch tips and their common ancestor to create a new merge commit that brings both histories together.

### Squash merge

Combines all commits from the feature branch into a single new commit on the target branch. The feature branch's commit history is condensed rather than preserved. Used when you want a clean history on `main` without all the intermediate commits from development.

---

## How to Merge

Merging always works in the same direction: **you merge into the branch you are currently on**.

To merge `feature/user-login` into `main`:

```bash
git switch main
git merge feature/user-login
```

Step 1 is critical. You must be on the branch you want to merge **into**. A very common mistake is being on the feature branch and running the merge - this merges `main` into your feature branch, which is the opposite of what you want.

**The general pattern:**

```bash
git switch target-branch
git merge source-branch
```

Where `target-branch` is where you want the changes to end up, and `source-branch` is where the changes are coming from.

---

## Fast-Forward Merges

A fast-forward merge happens when the target branch has not moved since the feature branch was created. There is nothing to combine - the feature branch is simply ahead of the target branch in a straight line.

```
Before merge:

main
 |
 A -- B -- C -- D -- E
                      |
               feature/login

After fast-forward merge:

                    main
                     |
 A -- B -- C -- D -- E
                      |
               feature/login
```

Git just moves the `main` pointer forward to point at the same commit as `feature/login`. No merge commit is created. The history stays perfectly linear.

**Running a fast-forward merge:**

```bash
git switch main
git merge feature/login
```

Output:

```
Updating a1b2c3d..e4f5g6h
Fast-forward
 login.html | 45 +++++++++++++++++++++++++++++++++++++++++++++
 1 file changed, 45 insertions(+)
 create mode 100644 login.html
```

**Force a merge commit even when fast-forward is possible:**

```bash
git merge --no-ff feature/login
```

Some teams prefer to always create a merge commit so the history shows exactly when each feature was integrated, even if it could have been fast-forwarded. GitHub's default "Create a merge commit" option does this.

---

## Three-Way Merges

A three-way merge happens when both branches have diverged - both have commits the other does not have.

```
Before merge:

      main
       |
 A -- B -- C -- F
              \
               D -- E
                    |
             feature/login

After three-way merge:

              main
               |
 A -- B -- C -- F -- G (merge commit)
              \     /
               D -- E
                    |
             feature/login
```

Git uses three commits to compute the merge:
- The tip of `main` (`F`)
- The tip of `feature/login` (`E`)
- Their common ancestor (`C`)

It compares what changed between `C` and `F`, and between `C` and `E`, and combines both sets of changes. If they touched different files or different parts of the same file, Git handles it automatically. If they touched the same lines, you get a conflict.

The result is a new **merge commit** (`G`) with two parents - `F` and `E`. This is the only type of commit with more than one parent.

**Running a three-way merge:**

```bash
git switch main
git merge feature/login
```

If the merge succeeds without conflicts, Git opens your configured editor for the merge commit message (or uses a default). Save and close the editor to complete the merge.

---

## Squash Merges

A squash merge takes all the commits from the feature branch and combines them into a single new commit on the target branch. The feature branch commits themselves are not merged into the history - only their combined effect is.

```
Before squash merge:

main
 |
 A -- B -- C
            \
             D -- E -- F -- G
                             |
                      feature/messy-work

After squash merge:

                  main
                   |
 A -- B -- C -- H (squash commit - contains D+E+F+G combined)
```

`H` is a brand new commit. It contains all the changes from `D`, `E`, `F` and `G` but appears as a single commit authored by you. The feature branch's history does not appear in `main`'s log.

**Running a squash merge:**

```bash
git switch main
git merge --squash feature/messy-work
git commit -m "add: complete user authentication feature"
```

Note that `--squash` stages the changes but does not automatically commit. You run `git commit` separately to write a clean, descriptive commit message for the squashed result.

**When to use squash merges:**

- When the feature branch has many small "WIP" commits that are not meaningful on their own
- When your team prefers a clean, linear `main` history
- When each entry in `main`'s log should represent a complete feature or fix, not individual development steps

**When not to use squash merges:**

- When the feature branch's detailed commit history has value - for example, a large feature where individual commits explain a series of decisions
- When multiple people contributed to the branch and you want their authorship preserved

> [!NOTE]
> After a squash merge, Git does not consider the feature branch merged because the original commits are not in the target branch's history. This means `git branch --merged` will not show it and `git branch -d` will refuse to delete it. Use `git branch -D` to force delete after a squash merge.

---

## Merge Commit Messages

When a three-way merge creates a merge commit, Git generates a default message like:

```
Merge branch 'feature/user-login' into main
```

You can customise this message. Either let Git open your editor (by not using `-m`) and edit the default, or provide your own:

```bash
git merge feature/user-login -m "merge: integrate user authentication feature"
```

Some teams leave the default message. Others add context about what the merge represents. Either is fine - the important thing is consistency within your team.

---

## Undoing a Merge

### Before pushing - reset to pre-merge state

If you merged but have not pushed yet:

```bash
git reset --hard HEAD~1
```

This moves `main` back one commit, to before the merge commit. Your working directory is restored to the pre-merge state.

> [!WARNING]
> `--hard` discards all changes permanently. Only use it if you are certain you want to undo the merge completely.

### After pushing - use revert

If the merge has already been pushed to a shared remote, do not reset. Use `git revert` instead. Reverting a merge commit requires specifying which parent to revert to:

```bash
git revert -m 1 HEAD
```

The `-m 1` flag tells Git to revert to the first parent (the branch you merged into). This creates a new commit that undoes the merge without rewriting history.

### Aborting a merge in progress

If you started a merge and it has conflicts but you want to abandon it entirely:

```bash
git merge --abort
```

This restores your repository to the state it was in before you started the merge.

---

## Merging in IDEs

All major IDEs have built-in merge support that makes the process visual.

### VS Code

When a merge succeeds with no conflicts, VS Code shows the result in the Source Control panel ready to commit. When there are conflicts, VS Code opens a three-panel merge editor showing the current branch version, the incoming version and the result. You click "Accept Current", "Accept Incoming" or "Accept Both" for each conflicted section.

Open the merge editor: Source Control panel -> click any file showing a conflict marker.

### JetBrains IDEs

Go to **Git -> Merge** or use the merge from the branch dropdown in the bottom status bar. Conflicts open in a three-panel diff view with Current, Changes and Result columns. Use the arrows to accept changes from either side, or edit the Result column directly.

### Visual Studio 2026

The Git Changes window shows merge conflicts. Click "Resolve conflicts" to open the inline merge editor showing both versions side by side.

---

## Merge vs Rebase

Both merge and rebase integrate changes from one branch into another. They produce the same end result but with different histories.

**Merge** preserves the full history of both branches exactly as it happened. You can see when branches diverged and when they came back together. The history is accurate but can look complex with many branching and merging lines.

**Rebase** rewrites the feature branch commits so they appear to start from the tip of the target branch, creating a linear history as if the work happened sequentially. The history is cleaner but less accurate - it does not reflect when the branches actually diverged.

```
Merge result:

 A -- B -- C -- F -- G (merge commit)
              \     /
               D -- E

Rebase result:

 A -- B -- C -- F -- D' -- E'
```

Note that after rebasing, `D` and `E` become `D'` and `E'` - they are new commits with new hashes, even though their content is the same.

**General guidance:**

- Use **merge** for integrating completed feature branches into `main` - it preserves the full history
- Use **rebase** to keep a feature branch up to date with `main` during development - it keeps the branch's history linear
- Never rebase branches that other people are working on - rewriting shared history causes serious problems

Rebase is covered in full in [git/13-rebase.md](13-rebase.md).

---

## Try It Yourself

This exercise demonstrates both a fast-forward merge and a three-way merge so you can see the difference.

**Step 1.** Set up a repository:

**Windows (PowerShell):**

```powershell
mkdir merge-practice && cd merge-practice && git init
echo "# Merge Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir merge-practice && cd merge-practice && git init
echo "# Merge Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Create a feature branch and add a commit:

```bash
git switch -c feature/about
echo "# About" > about.md
git add about.md && git commit -m "add: about page"
```

**Step 3.** Merge it into main (this will be a fast-forward):

```bash
git switch main
git merge feature/about
git log --oneline --graph --all
```

Notice there is no merge commit. The history is a straight line.

**Step 4.** Now simulate a three-way merge. Add a commit to main:

```bash
echo "Contact: hello@example.com" >> README.md
git add README.md && git commit -m "update: add contact info to README"
```

**Step 5.** Create another feature branch and add commits:

```bash
git switch -c feature/contact
echo "# Contact" > contact.md
git add contact.md && git commit -m "add: contact page"
echo "Email us anytime." >> contact.md
git add contact.md && git commit -m "update: add email note to contact page"
```

**Step 6.** Merge it into main (this will be a three-way merge):

```bash
git switch main
git merge feature/contact
git log --oneline --graph --all
```

This time you should see a merge commit with two parents, and the graph shows the branch diverging and merging back together.

**Step 7.** Clean up branches and repository:

```bash
git branch -d feature/about
git branch -d feature/contact
cd ..
```

**Windows:**

```powershell
rmdir /s /q merge-practice
```

**Mac and Linux:**

```bash
rm -rf merge-practice
```

---

## Common Mistakes

**Being on the wrong branch when merging.**

`git merge feature/login` merges `feature/login` into whatever branch you are currently on. If you are on `feature/login` instead of `main`, you merge `main` into your feature branch - the opposite of what you intended. Always check `git branch` or `git status` before merging.

**Forgetting to pull before merging.**

If `main` has new commits from the remote that you do not have locally, your merge will be based on an outdated version. Always pull the target branch before merging into it:

```bash
git switch main
git pull
git merge feature/my-feature
```

**Force pushing after undoing a merge.**

If you reset to undo a merge that has already been pushed, your local branch is behind the remote. Pushing will be rejected. It is tempting to force push, but this rewrites shared history and causes serious problems for collaborators. Use `git revert -m 1 HEAD` instead to create a new commit that undoes the merge.

**Leaving merge conflicts unresolved.**

If you start a merge and Git reports conflicts, you must resolve them before the merge is complete. Files with conflicts contain conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`). Until you resolve those and commit, your repository is in a mid-merge state. Run `git merge --abort` to cancel, or resolve the conflicts and commit. Conflicts are covered fully in [git/08-merge-conflicts.md](08-merge-conflicts.md).

**Merging the wrong branch.**

Double-check the branch name before running `git merge`. Merging the wrong branch into `main` is recoverable but inconvenient. Use `git log --oneline --graph --all` to verify the state of all branches before merging.

---

## Summary

- Merging brings the history of one branch into another - always run `git merge` from the branch you want to merge **into**
- A **fast-forward merge** moves the branch pointer forward when there are no diverging commits - no merge commit is created
- A **three-way merge** creates a new merge commit with two parents when both branches have diverged
- A **squash merge** combines all feature branch commits into one new commit on the target branch
- Abort a merge in progress with `git merge --abort`
- Undo an unpushed merge with `git reset --hard HEAD~1`
- Undo a pushed merge safely with `git revert -m 1 HEAD`
- Use merge to integrate completed feature branches; use rebase to keep branches up to date during development

---

## Sources and Further Reading

- [Official git merge documentation](https://git-scm.com/docs/git-merge) - the complete reference for all merge options and flags
- [Pro Git book, Chapter 3.2: Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) - the official guide to merging with detailed diagrams
- [Atlassian - Git merge](https://www.atlassian.com/git/tutorials/using-branches/git-merge) - clear visual explanation of merge types with diagrams
- [GitHub - About merge methods](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/about-merge-methods-on-github) - GitHub's explanation of merge, squash and rebase options for pull requests

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

# Reset and Revert

**Difficulty:** 🔴 Advanced | **Time:** 30 minutes

Knowing how to undo things in Git is one of the most valuable skills you can have. Git gives you multiple ways to undo changes depending on what you want to undo, how far back you want to go and whether the commits involved have been shared with others. Using the wrong tool for the situation can make things worse. This file explains every approach clearly so you always know which one to reach for.

---

## Table of Contents

- [The three levels of undo](#the-three-levels-of-undo)
- [Undoing working directory changes](#undoing-working-directory-changes)
- [Undoing staged changes](#undoing-staged-changes)
- [git reset](#git-reset)
- [git revert](#git-revert)
- [git restore](#git-restore)
- [Amending the last commit](#amending-the-last-commit)
- [The reflog - your safety net](#the-reflog---your-safety-net)
- [Undoing a merge](#undoing-a-merge)
- [Removing a file from history](#removing-a-file-from-history)
- [Reset vs revert - when to use which](#reset-vs-revert---when-to-use-which)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## The Three Levels of Undo

Before choosing a command, identify what you want to undo:

| What you want to undo | How far back | Shared with others? | Use |
|---|---|---|---|
| Changes in a file not yet staged | Working directory | No | `git restore` |
| Changes you staged but not committed | Staging area | No | `git restore --staged` |
| The last commit (keep changes) | One commit | No | `git reset --soft HEAD~1` |
| The last commit (discard changes) | One commit | No | `git reset --hard HEAD~1` |
| Several commits (keep changes) | Multiple commits | No | `git reset --soft HEAD~N` |
| A commit that has been pushed | Any | Yes | `git revert` |
| A typo in the last commit message | One commit | No | `git commit --amend` |

The key question is always: **has this been pushed to a shared remote?**

If no - you can safely rewrite history with `git reset`.
If yes - you must use `git revert` to create a new undoing commit instead.

---

## Undoing Working Directory Changes

You edited a file and want to discard those changes and return the file to its last committed state.

**Discard changes in a specific file:**

```bash
git restore README.md
```

**Discard changes in all modified files:**

```bash
git restore .
```

> [!WARNING]
> `git restore` on an unstaged file permanently discards your changes. There is no undo for this - the changes are gone. Make sure you actually want to discard them before running it.

This only affects files that have not been staged. If you already ran `git add`, see the next section.

---

## Undoing Staged Changes

You ran `git add` and want to unstage a file - move it back to the working directory without losing the changes.

**Unstage a specific file:**

```bash
git restore --staged README.md
```

**Unstage all staged files:**

```bash
git restore --staged .
```

The changes remain in your working directory - they are just no longer staged. You have not lost anything.

> [!NOTE]
> Older tutorials use `git reset HEAD filename` to unstage files. This still works but `git restore --staged` is the current recommended command. It was introduced in Git 2.23 specifically to make this operation clearer.

---

## git reset

`git reset` moves the current branch pointer to a different commit, effectively "uncommitting" everything after that point. What happens to the changes from those commits depends on which mode you use.

### The three modes

**`--soft` - move the branch pointer, keep changes staged**

```bash
git reset --soft HEAD~1
```

The branch pointer moves back one commit. The changes from the undone commit are placed in the staging area, ready to commit again. Your working directory is unchanged. Use this when you want to redo the commit differently - perhaps to rewrite the message, split it into two commits or combine it with other changes.

**`--mixed` (the default) - move the branch pointer, unstage changes**

```bash
git reset HEAD~1
```

or

```bash
git reset --mixed HEAD~1
```

The branch pointer moves back one commit. The changes from the undone commit are placed in the working directory as unstaged changes. Use this when you want to recommit the changes differently - perhaps staging only some of the files.

**`--hard` - move the branch pointer, discard changes completely**

```bash
git reset --hard HEAD~1
```

The branch pointer moves back one commit. The changes from the undone commit are gone. Your working directory matches the new HEAD. Use this when you want to completely discard a commit and its changes.

> [!CAUTION]
> `git reset --hard` permanently discards changes. They cannot be recovered through normal Git commands. The reflog may save you within 30-90 days (see [The reflog](#the-reflog---your-safety-net)), but do not rely on it. Be certain before running `--hard`.

### Resetting to a specific commit

```bash
git reset --soft a1b2c3d
```

This moves the branch pointer back to commit `a1b2c3d`. All commits after `a1b2c3d` are undone, with their changes placed in the staging area (`--soft`), working directory (`--mixed`) or discarded (`--hard`).

### Resetting using relative references

```bash
git reset --soft HEAD~1    # go back 1 commit
git reset --soft HEAD~3    # go back 3 commits
git reset --hard HEAD~5    # go back 5 commits, discard everything
```

`HEAD~N` means "N commits before HEAD".

### Resetting a single file

You can also reset a single file to a specific version without moving the branch pointer:

```bash
git reset HEAD~1 -- README.md
```

This restores `README.md` to its state one commit ago. The file is placed in the staging area. Other files are untouched and the branch pointer does not move.

---

## git revert

`git revert` creates a new commit that undoes the changes from a specific commit. It does not rewrite history - it adds to it. This is the safe way to undo changes that have already been pushed to a shared remote.

**Revert the most recent commit:**

```bash
git revert HEAD
```

**Revert a specific commit:**

```bash
git revert a1b2c3d
```

Git creates a new commit with the inverse of the specified commit's changes. If the original commit added a line, the revert removes it. If it deleted a line, the revert adds it back.

**Revert without automatically committing:**

```bash
git revert a1b2c3d --no-commit
```

Git applies the inverse changes to your staging area but does not commit. Useful when you want to revert multiple commits and combine the undo into a single commit:

```bash
git revert a1b2c3d --no-commit
git revert e4f5g6h --no-commit
git revert i7j8k9l --no-commit
git commit -m "revert: undo the last three commits"
```

**Revert a range of commits:**

```bash
git revert a1b2c3d..i7j8k9l
```

This reverts all commits in the range, creating one revert commit per original commit.

**Revert a merge commit:**

```bash
git revert -m 1 a1b2c3d
```

The `-m 1` flag specifies which parent to revert to. For a merge commit, parent 1 is the branch you merged into (usually `main`). Reverting a merge commit is covered in detail in [git/07-merging.md](07-merging.md).

---

## git restore

`git restore` was introduced in Git 2.23 to provide a cleaner interface for restoring files. It handles two operations that were previously done with the overloaded `git checkout` command.

**Restore a file in the working directory to the last committed state:**

```bash
git restore README.md
```

**Restore a file to its state at a specific commit:**

```bash
git restore --source a1b2c3d README.md
```

**Unstage a file (restore the staged version to the working directory state):**

```bash
git restore --staged README.md
```

**Restore a file to a specific commit and stage it:**

```bash
git restore --source a1b2c3d --staged README.md
```

---

## Amending the Last Commit

If you made a mistake in the very last commit and have not pushed it yet, `git commit --amend` lets you modify it.

**Fix the commit message:**

```bash
git commit --amend -m "fix: correct and complete commit message"
```

**Add a forgotten file to the last commit:**

```bash
git add forgotten-file.md
git commit --amend --no-edit
```

`--no-edit` keeps the existing commit message unchanged.

**Change both the files and the message:**

```bash
git add additional-changes.md
git commit --amend -m "add: complete feature with all files"
```

> [!WARNING]
> `git commit --amend` rewrites the last commit - it creates a new commit with a new hash. Never amend a commit that has already been pushed to a shared branch. Push with `-u` to update the remote if you amend a commit on your own branch before opening a pull request:

```bash
git push --force-with-lease
```

---

## The Reflog - Your Safety Net

The reflog (`git reflog`) records every position HEAD has been at, including operations that appear to be destructive - resets, rebases, amended commits and deleted branches.

```bash
git reflog
```

Output:

```
a1b2c3d (HEAD -> main) HEAD@{0}: commit: add: new feature
e4f5g6h HEAD@{1}: reset: moving to HEAD~1
i7j8k9l HEAD@{2}: commit: wip: accidental commit
f0e1d2c HEAD@{3}: checkout: moving from feature to main
```

Each entry shows where HEAD was and what operation moved it. The `HEAD@{N}` notation lets you reference any of these positions.

**Recover a commit after an accidental `git reset --hard`:**

```bash
git reflog
# find the hash of the commit you lost
git reset --hard a1b2c3d
```

Or create a new branch pointing at the lost commit:

```bash
git switch -c recovery-branch a1b2c3d
```

**Recover a deleted branch:**

```bash
git reflog
# find the last commit that was on the deleted branch
git switch -c recovered-branch a1b2c3d
```

> [!NOTE]
> The reflog is local to your machine. It is not pushed to the remote. It retains entries for 30-90 days by default (90 days for reachable commits, 30 days for unreachable ones) before Git's garbage collection removes them. The reflog saves you from most accidental data loss, but it is not a permanent backup.

---

## Undoing a Merge

**Undo a merge that has not been pushed:**

```bash
git reset --hard HEAD~1
```

Moves the branch pointer back before the merge commit and discards all changes.

**Undo a merge that has been pushed:**

```bash
git revert -m 1 HEAD
```

Creates a new commit that undoes the merge. The `-m 1` flag specifies parent 1 (the branch you merged into) as the version to revert to.

**Abort a merge that is in progress (before committing):**

```bash
git merge --abort
```

---

## Removing a File from History

Sometimes a file gets committed that should never have been there - a `.env` file with real credentials, a large binary, a file committed by mistake. Removing it from the current commit is not enough - it remains in every previous commit.

> [!CAUTION]
> If you accidentally committed credentials, **revoke them immediately** before doing anything else. Assume they have already been seen, even if the repository is private. Changing the history does not help if the credentials were already accessed.

The recommended tool for removing files from history is **git-filter-repo**:

**Install:**

```bash
pip install git-filter-repo
```

**Remove a specific file from all history:**

```bash
git filter-repo --path secrets.env --invert-paths
```

**Remove all files with a specific extension:**

```bash
git filter-repo --path-glob '*.pem' --invert-paths
```

**After rewriting history, force push all branches:**

```bash
git push origin --force --all
git push origin --force --tags
```

> [!WARNING]
> Rewriting history with `git filter-repo` creates new commit hashes for every affected commit. Anyone who has cloned the repository will have a diverged history and will need to re-clone. Coordinate with your team before doing this.

> [!NOTE]
> The older `git filter-branch` command also removes files from history but is significantly slower and has known bugs. `git filter-repo` is the official recommendation as of Git 2.24. The BFG Repo Cleaner is another popular alternative for large repositories.

---

## Reset vs Revert - When to Use Which

This is the most important distinction in this file.

**Use `git reset` when:**
- The commit has not been pushed to a shared remote
- You are working on your own local branch
- You want to completely rewrite local history before sharing it

**Use `git revert` when:**
- The commit has already been pushed to a shared remote
- Others may have already pulled the commit
- You need to maintain a clean, accurate history showing that something was intentionally undone
- You are working on `main` or any protected branch

The fundamental difference: `reset` rewrites history (old commits are gone). `revert` adds to history (the undo is itself a commit).

```
git reset:
BEFORE: A -- B -- C -- D (HEAD)
AFTER:  A -- B (HEAD)     C and D are gone

git revert:
BEFORE: A -- B -- C -- D (HEAD)
AFTER:  A -- B -- C -- D -- D' (HEAD)   D' is the undo of D
```

---

## Try It Yourself

**Step 1.** Set up a repository:

**Windows (PowerShell):**

```powershell
mkdir undo-practice && cd undo-practice && git init
echo "line 1" > file.txt
git add file.txt && git commit -m "add: initial file"
```

**Mac and Linux:**

```bash
mkdir undo-practice && cd undo-practice && git init
echo "line 1" > file.txt
git add file.txt && git commit -m "add: initial file"
```

**Step 2.** Make three more commits:

**Windows:**

```powershell
echo "line 2" >> file.txt && git add . && git commit -m "update: add line 2"
echo "line 3" >> file.txt && git add . && git commit -m "update: add line 3"
echo "line 4" >> file.txt && git add . && git commit -m "update: add line 4"
```

**Mac and Linux:**

```bash
echo "line 2" >> file.txt && git add . && git commit -m "update: add line 2"
echo "line 3" >> file.txt && git add . && git commit -m "update: add line 3"
echo "line 4" >> file.txt && git add . && git commit -m "update: add line 4"
```

**Step 3.** View the log:

```bash
git log --oneline
```

**Step 4.** Soft reset - undo the last commit but keep changes staged:

```bash
git reset --soft HEAD~1
git status
```

The last commit is undone. `file.txt` with "line 4" is in the staging area.

**Step 5.** Recommit it with a better message:

```bash
git commit -m "update: add line 4 to file"
```

**Step 6.** Mixed reset - undo the last commit and unstage changes:

```bash
git reset HEAD~1
git status
```

The commit is undone. Changes are in the working directory, unstaged.

**Step 7.** Restage and recommit:

```bash
git add file.txt
git commit -m "update: add line 4 to file"
```

**Step 8.** Hard reset - undo the last commit and discard changes:

```bash
git reset --hard HEAD~1
git log --oneline
cat file.txt
```

The commit and its changes are gone. `file.txt` only has 3 lines.

**Step 9.** Recover using the reflog:

```bash
git reflog
```

Find the hash of the lost commit, then:

```bash
git reset --hard [hash of the lost commit]
git log --oneline
```

The commit is back.

**Step 10.** Practice revert - undo a commit safely:

```bash
git revert HEAD --no-edit
git log --oneline
```

A new revert commit appears. The history is preserved and the undo is recorded.

**Step 11.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q undo-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf undo-practice
```

---

## Common Mistakes

**Using `git reset --hard` on a pushed branch.**

This rewrites history on the remote after a force push, causing diverged history for anyone who has already pulled. If the commit is already pushed, use `git revert` instead.

**Forgetting that `git reset --hard` is permanent for uncommitted changes.**

If you have modified files that were never committed, `git reset --hard` discards them forever. The reflog only tracks commits - it cannot recover changes that were never committed.

**Amending a commit that has already been pushed.**

`git commit --amend` rewrites the last commit. If it has been pushed, your local history is now different from the remote. Force pushing will cause problems for anyone who has pulled. Only amend commits that have not been shared.

**Not knowing about the reflog.**

Many developers do not discover the reflog until they have lost work and there is nothing they can do about it. Run `git reflog` regularly to understand what it shows. When you do lose something, the reflog is your first stop.

**Reverting a revert.**

If you revert a commit and then decide you want those changes back, you need to revert the revert commit - not re-apply the original. If you re-apply the original, Git may see the changes as already present (in the revert's inverse) and produce unexpected results.

**Using `git filter-branch` instead of `git filter-repo`.**

`git filter-branch` is slow and has known correctness issues. `git filter-repo` is the official recommendation. If you need to remove a file from history, use `git filter-repo`.

---

## Summary

- `git restore filename` discards working directory changes in a file
- `git restore --staged filename` unstages a file without losing changes
- `git reset --soft HEAD~N` undoes N commits, keeping changes staged
- `git reset --mixed HEAD~N` (or just `git reset HEAD~N`) undoes N commits, keeping changes unstaged
- `git reset --hard HEAD~N` undoes N commits and permanently discards the changes
- `git revert <hash>` creates a new commit that undoes a specific commit without rewriting history
- `git commit --amend` rewrites the last commit - never use on pushed commits
- The reflog (`git reflog`) records all HEAD positions and can recover apparently lost commits
- Use `git reset` for unpushed local history; use `git revert` for pushed shared history
- Use `git filter-repo` to permanently remove files from the entire commit history

---

## Sources and Further Reading

- [Official git reset documentation](https://git-scm.com/docs/git-reset) - the complete reference including all modes and options
- [Official git revert documentation](https://git-scm.com/docs/git-revert) - the complete reference for revert
- [Official git restore documentation](https://git-scm.com/docs/git-restore) - the complete reference for the restore command
- [Pro Git book, Chapter 7.7: Reset Demystified](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) - the most thorough explanation of how reset works, with detailed diagrams
- [git-filter-repo](https://github.com/newren/git-filter-repo) - the recommended tool for removing files from Git history
- [Oh Shit, Git!?!](https://ohshitgit.com) - plain-English recovery steps for the most common disaster scenarios
- [Atlassian - Undoing commits and changes](https://www.atlassian.com/git/tutorials/undoing-changes) - visual guide to all undo operations with diagrams

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

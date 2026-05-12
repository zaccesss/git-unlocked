# Cherry-pick

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

Cherry-pick lets you take a specific commit from one branch and apply it to another. Instead of merging an entire branch, you pick only the commits you want. It is a precise tool for specific situations - useful when used appropriately, problematic when overused.

---

## Table of Contents

- [What is cherry-pick?](#what-is-cherry-pick)
- [When to use cherry-pick](#when-to-use-cherry-pick)
- [When not to use cherry-pick](#when-not-to-use-cherry-pick)
- [Basic cherry-pick](#basic-cherry-pick)
- [Cherry-picking multiple commits](#cherry-picking-multiple-commits)
- [Cherry-picking a range of commits](#cherry-picking-a-range-of-commits)
- [Cherry-pick options](#cherry-pick-options)
- [Handling conflicts during cherry-pick](#handling-conflicts-during-cherry-pick)
- [Undoing a cherry-pick](#undoing-a-cherry-pick)
- [Cherry-pick vs merge vs rebase](#cherry-pick-vs-merge-vs-rebase)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is Cherry-pick?

`git cherry-pick` takes the changes introduced by a specific commit and applies them as a new commit on the current branch. The original commit stays where it is - cherry-pick creates a copy of it, not a move.

The new commit has the same changes as the original but a different hash, a different parent and the current timestamp.

```
Before cherry-pick:

main    A -- B -- C
                   \
feature             D -- E -- F
                         ^
                    (we want this one)

After cherry-picking E onto main:

main    A -- B -- C -- E'
                   \
feature             D -- E -- F
```

`E'` is a new commit on `main` containing the same changes as `E` but with a different hash.

---

## When to Use Cherry-pick

Cherry-pick is the right tool in specific, well-defined situations.

**Applying a hotfix to multiple branches.**

You have a critical bug fix on `main` that also needs to go to `release/v1.x` and `release/v2.x`. Rather than merging the entire `main` branch into each release branch, you cherry-pick just the fix commit.

```bash
git switch release/v1.x
git cherry-pick a1b2c3d
git switch release/v2.x
git cherry-pick a1b2c3d
```

**Recovering a useful commit from an abandoned branch.**

You worked on a feature branch that was ultimately rejected, but one specific commit in it fixed a real bug or added a useful utility function. Cherry-pick that one commit onto `main` without bringing the rest of the abandoned work.

**Applying a commit made on the wrong branch.**

You accidentally committed to `main` instead of your feature branch. Cherry-pick the commit onto the correct branch, then revert it from `main`.

```bash
git switch feature/correct-branch
git cherry-pick a1b2c3d        # apply the accidental commit here
git switch main
git revert a1b2c3d             # undo it from main
```

**Selectively backporting features.**

You are maintaining an older stable release and want to bring in a specific improvement from `main` without upgrading the whole codebase.

---

## When Not to Use Cherry-pick

Cherry-pick is often misused as a substitute for proper merging. Recognising when not to use it is as important as knowing when to use it.

**Do not cherry-pick when you want all the changes from a branch.**

If you want everything on `feature/login`, use `git merge feature/login`. Cherry-picking individual commits when you want the entire branch is unnecessary work that produces a messier history.

**Do not cherry-pick routinely to keep branches in sync.**

Some teams cherry-pick fixes between branches instead of merging. This creates duplicate commits with different hashes across branches, making history harder to follow and conflict resolution more complex. Use merging or rebasing for keeping branches synchronised.

**Do not cherry-pick commits that have dependencies you are not also picking.**

If commit `F` depends on changes introduced in commits `D` and `E`, cherry-picking only `F` will likely fail or produce incorrect results. You need to cherry-pick the full dependency chain.

---

## Basic Cherry-pick

**Find the commit hash you want to apply:**

```bash
git log --oneline feature/other-branch
```

Or use the full graph to find it visually:

```bash
git log --oneline --graph --all
```

**Switch to the branch you want to apply the commit to:**

```bash
git switch main
```

**Apply the commit:**

```bash
git cherry-pick a1b2c3d
```

Git applies the changes from that commit and creates a new commit on the current branch. The new commit has the same changes and the same commit message, but a new hash and the current timestamp.

**Cherry-pick without automatically committing:**

```bash
git cherry-pick a1b2c3d --no-commit
```

This applies the changes to your working directory and staging area but does not create a commit. Useful when you want to combine the cherry-picked changes with other modifications before committing.

---

## Cherry-picking Multiple Commits

**Cherry-pick several specific commits:**

```bash
git cherry-pick a1b2c3d e4f5g6h i7j8k9l
```

Git applies them in the order you list them, creating one new commit per cherry-picked commit.

---

## Cherry-picking a Range of Commits

**Cherry-pick a range of commits (exclusive of the first, inclusive of the last):**

```bash
git cherry-pick a1b2c3d..i7j8k9l
```

This applies all commits after `a1b2c3d` up to and including `i7j8k9l`. Note that `a1b2c3d` itself is **not** included.

**Cherry-pick a range including the first commit:**

```bash
git cherry-pick a1b2c3d^..i7j8k9l
```

The `^` means "the parent of", so `a1b2c3d^` means "start from before `a1b2c3d`" - effectively including `a1b2c3d` in the range.

---

## Cherry-pick Options

**`-e` or `--edit` - edit the commit message before committing:**

```bash
git cherry-pick a1b2c3d -e
```

Git opens your editor so you can modify the commit message. Useful when you want to note that this commit was cherry-picked from another branch.

**`-x` - append a note to the commit message indicating the source:**

```bash
git cherry-pick a1b2c3d -x
```

The commit message becomes:

```
add: fix payment redirect

(cherry picked from commit a1b2c3d)
```

Highly recommended when cherry-picking hotfixes across release branches - it creates a clear audit trail of where the commit came from.

**`--no-commit` (`-n`) - apply changes without committing:**

```bash
git cherry-pick a1b2c3d --no-commit
```

Stages the changes but does not commit. You can then modify or combine them before committing manually.

**`--signoff` - add a sign-off line to the commit message:**

```bash
git cherry-pick a1b2c3d --signoff
```

Adds `Signed-off-by: Your Name <your@email.com>` to the commit message. Used in some open source projects to track who applied a commit.

**`--strategy-option` - pass options to the merge strategy:**

```bash
git cherry-pick a1b2c3d -X theirs
```

When a conflict arises, automatically accept the incoming (cherry-picked) version. `ours` does the opposite - keeps the current branch version.

---

## Handling Conflicts During Cherry-pick

When the cherry-picked commit conflicts with the current state of the branch, Git pauses and marks the conflicts exactly as it does during a merge.

**When a conflict occurs:**

```
error: could not apply a1b2c3d... fix: payment redirect
hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git cherry-pick --continue".
hint: You can instead skip this commit with "git cherry-pick --skip".
hint: To abort and get back to the original state before "git cherry-pick",
hint: run "git cherry-pick --abort".
```

**Resolve the conflict:**

1. Open the conflicted file and fix it
2. Stage the resolved file:

```bash
git add filename.html
```

3. Continue the cherry-pick:

```bash
git cherry-pick --continue
```

**Skip this commit and continue with the next:**

```bash
git cherry-pick --skip
```

Use this when the conflict indicates the commit's changes are already present on the target branch or are no longer relevant.

**Abort entirely:**

```bash
git cherry-pick --abort
```

Returns to the state before the cherry-pick started.

---

## Undoing a Cherry-pick

**If you have not pushed yet - reset to before the cherry-pick:**

```bash
git reset --hard HEAD~1
```

This removes the last commit (the cherry-picked one) and restores your working directory to the pre-cherry-pick state.

If you cherry-picked multiple commits and want to undo all of them:

```bash
git reset --hard HEAD~3    # remove the last 3 commits
```

**If you have already pushed - use revert:**

```bash
git revert a1b2c3d
```

This creates a new commit that undoes the cherry-picked commit without rewriting history.

---

## Cherry-pick vs Merge vs Rebase

| Operation | What it does | Creates duplicate commits? | Best for |
|---|---|---|---|
| `merge` | Combines entire branch history | No | Integrating completed features |
| `rebase` | Replays commits on a new base | Yes (new hashes) | Updating feature branches, cleaning history |
| `cherry-pick` | Copies specific commits | Yes (new hashes) | Hotfixes across branches, recovering specific commits |

All three create new commits with new hashes when they copy changes. The difference is scope and intent.

---

## Try It Yourself

**Step 1.** Set up a repository with two branches:

**Windows (PowerShell):**

```powershell
mkdir cherry-practice && cd cherry-practice && git init
echo "# Project" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir cherry-practice && cd cherry-practice && git init
echo "# Project" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Create a feature branch and add several commits:

```bash
git switch -c feature/mixed-work
echo "useful utility function" > utils.js
git add utils.js && git commit -m "add: useful utility function"
echo "experimental feature" > experiment.js
git add experiment.js && git commit -m "add: experimental feature"
echo "important bug fix" > bugfix.js
git add bugfix.js && git commit -m "fix: important bug"
echo "more experimental work" >> experiment.js
git add experiment.js && git commit -m "update: more experimental work"
```

**Step 3.** View the log on the feature branch:

```bash
git log --oneline
```

Note the hash for the `fix: important bug` commit.

**Step 4.** Switch to main - only the bug fix and utility are needed:

```bash
git switch main
```

**Step 5.** Cherry-pick the utility commit (replace the hash with your actual one):

```bash
git cherry-pick [hash of "add: useful utility function"] -x
```

**Step 6.** Cherry-pick the bug fix:

```bash
git cherry-pick [hash of "fix: important bug"] -x
```

**Step 7.** View the log on main:

```bash
git log --oneline
```

Only the two cherry-picked commits appear on `main`. The experimental commits stayed on the feature branch.

**Step 8.** Verify the source annotation in the commit message:

```bash
git log -1 --format="%B"
```

You should see the `(cherry picked from commit ...)` note added by `-x`.

**Step 9.** View all branches to confirm the feature branch is untouched:

```bash
git log --oneline --graph --all
```

**Step 10.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q cherry-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf cherry-practice
```

---

## Common Mistakes

**Cherry-picking when you should be merging.**

If you find yourself cherry-picking many commits from a branch, that is a strong signal you should be merging the branch instead. Cherry-pick is for surgical precision on a few specific commits, not for bulk transfer of work.

**Not using `-x` when cherry-picking hotfixes.**

When applying a fix across multiple release branches, always use `git cherry-pick -x`. The note it adds to the commit message (`cherry picked from commit a1b2c3d`) creates an audit trail that is invaluable when tracing where a fix came from months later.

**Cherry-picking commits that depend on other commits you did not pick.**

Commit `F` may rely on changes from commits `D` and `E`. If you only cherry-pick `F`, the result may be incorrect or conflicting because the context it was built on is not present. Always check the commit's context before cherry-picking it in isolation.

**Forgetting that cherry-pick creates a duplicate commit.**

The original commit stays on the source branch. The cherry-picked commit is a copy with a new hash. If you later merge the source branch, you may see duplicate changes or confusing conflicts because Git sees two different commits with the same effect.

**Using cherry-pick to synchronise branches regularly.**

If you need branch A to always have everything from branch B, use merging or rebasing - not cherry-picking. Regular cherry-picking between branches creates a proliferation of duplicate commits that makes history very hard to follow.

---

## Summary

- `git cherry-pick <hash>` copies a specific commit onto the current branch as a new commit
- The original commit is not moved - cherry-pick creates a copy with a new hash
- Use cherry-pick for: hotfixes across release branches, recovering commits from abandoned branches, fixing accidental commits on the wrong branch
- Do not use cherry-pick as a substitute for merging when you want all changes from a branch
- `-x` appends the source commit hash to the message - recommended for traceability
- `--no-commit` applies changes without committing, letting you combine or modify before committing
- Conflicts are resolved the same way as merge conflicts: fix, `git add`, `git cherry-pick --continue`
- `git cherry-pick --abort` cancels and returns to the pre-cherry-pick state
- Undo an unpushed cherry-pick with `git reset --hard HEAD~1`

---

## Sources and Further Reading

- [Official git cherry-pick documentation](https://git-scm.com/docs/git-cherry-pick) - the complete reference for all cherry-pick options
- [Atlassian - git cherry-pick](https://www.atlassian.com/git/tutorials/cherry-pick) - clear guide with examples and use cases
- [Pro Git book](https://git-scm.com/book/en/v2) - background on how Git applies patches, relevant to understanding cherry-pick

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

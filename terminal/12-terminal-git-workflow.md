# A Complete Terminal Git Workflow

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

This file pulls together everything from the previous files into a single, coherent workflow. It shows how the tools - lazygit, delta, fzf, bat, tig, zoxide and starship - work together in real daily use, from starting a feature to getting it merged. Every step uses the tools introduced earlier, with alternatives shown for developers who prefer fewer tools or different combinations.

---

## Table of Contents

1. [The tools in this workflow](#1-the-tools-in-this-workflow)
2. [Starting the day](#2-starting-the-day)
3. [Starting a new feature](#3-starting-a-new-feature)
4. [Working on the feature](#4-working-on-the-feature)
5. [Reviewing your changes before committing](#5-reviewing-your-changes-before-committing)
6. [Committing](#6-committing)
7. [Mid-feature: handling interruptions](#7-mid-feature-handling-interruptions)
8. [Keeping your branch up to date](#8-keeping-your-branch-up-to-date)
9. [Cleaning up commits before a PR](#9-cleaning-up-commits-before-a-pr)
10. [Pushing and creating a pull request](#10-pushing-and-creating-a-pull-request)
11. [Reviewing someone else's work](#11-reviewing-someone-elses-work)
12. [After the PR is merged](#12-after-the-pr-is-merged)
13. [Recovering from mistakes](#13-recovering-from-mistakes)
14. [Putting it together - the full workflow at a glance](#14-putting-it-together---the-full-workflow-at-a-glance)
15. [Try It Yourself](#15-try-it-yourself)
16. [Common Mistakes](#16-common-mistakes)
17. [Summary](#17-summary)

---

## 1. The tools in this workflow

| Tool | Role | File |
| ---- | ---- | ---- |
| zoxide | Navigate between repositories instantly | [11-other-tools.md](11-other-tools.md) |
| starship | Show branch, status and ahead/behind in the prompt | [11-other-tools.md](11-other-tools.md) |
| lazygit | Stage, commit, branch, rebase, push | [08-lazygit.md](08-lazygit.md) |
| delta | Better diff output in the terminal | [07-delta.md](07-delta.md) |
| bat | View file contents with syntax highlighting | [10-bat-and-tig.md](10-bat-and-tig.md) |
| fzf | Fuzzy search branches, commits, files | [09-fzf-and-git.md](09-fzf-and-git.md) |
| tig | Browse history, blame, find a specific commit | [10-bat-and-tig.md](10-bat-and-tig.md) |

None of these tools are required. Every step below has a plain `git` command alternative. The tools make things faster and more visual, but the underlying operations are standard Git.

---

## 2. Starting the day

Open your terminal. Your starship prompt shows where you are and whether any repositories are dirty from yesterday.

**Jump to the repository you need**:

```bash
z myproject        # zoxide - jump by a few characters, no full path needed
# or
z client backend   # multiple terms, all must appear in the path
```

**Check the state of the repository**:

```bash
git status         # quick plain check
# or open lazygit for a full view
lazygit
```

**Update your local main branch**:

```bash
git switch main
git pull           # or: git fetch --all --prune && git rebase origin/main
```

With starship, your prompt shows `main ⇣3` if there are 3 upstream commits to pull, before you even run the command.

**See what is happening across all branches**:

```bash
git lg             # the log alias from 06-git-aliases.md - graph of all branches
# or in tig:
tig
```

---

## 3. Starting a new feature

**Create a branch**:

```bash
git switch --create feature/user-export
# or the alias:
git swc feature/user-export
```

With `push.autoSetupRemote = true` in your config (covered in [05-git-config-in-depth.md](05-git-config-in-depth.md)), your first `git push` will automatically set the upstream without needing `-u origin feature/user-export`.

**Alternatively, use lazygit to create the branch**:

1. Open `lazygit`
2. Press `3` for the Branches panel
3. Press `n` for a new branch
4. Type `feature/user-export` and press Enter

Either way, your starship prompt now shows the branch name.

---

## 4. Working on the feature

Write your code. As you edit, the starship prompt updates:

- `feature/user-export !3` - three modified files
- `feature/user-export +2 !1` - two staged, one modified
- `feature/user-export ⇡1` - one commit ahead of the remote

**Check which files have changed**:

```bash
git s              # the status alias: git status --short --branch
```

**View a specific file with Git change markers**:

```bash
bat src/users/export.py   # shows modified lines highlighted with ~ in the gutter
```

**View the diff of your work so far**:

```bash
git diff           # routed through delta automatically (if configured)
git ds             # the alias: git diff --staged (what is staged)
```

---

## 5. Reviewing your changes before committing

Before committing, review everything you changed. This catches debug code, TODO comments, leftover test data and accidental changes.

**Option 1 - delta in the terminal**:

```bash
git diff           # all unstaged changes, syntax-highlighted by delta
git diff --staged  # all staged changes
```

Navigate between files with `n`/`N` (with `navigate = true` in delta config).

**Option 2 - lazygit**:

Open `lazygit`. In the Files panel, press `Enter` on each file to see its diff. The main panel shows the changes in real time as you navigate the file list.

**Option 3 - tig**:

```bash
tig status   # or press s from inside tig
```

Navigate files with `j`/`k`, press `Enter` to see the diff.

**What to check before committing**:

- No `console.log`, `print()`, `debugger` or `pry` debugging statements
- No `TODO` comments you meant to address before this commit
- No hardcoded API keys, passwords or test data
- Changes are all related to the same concern (not mixing features and bug fixes in one commit)
- Tests pass: `npm test`, `pytest`, `cargo test` etc.

---

## 6. Committing

**Option 1 - lazygit (visual)**:

1. Open `lazygit`
2. In the Files panel, press `space` to stage files individually, or `a` to stage all
3. For hunk-level staging: press `Enter` on a file, navigate hunks with `j`/`k`, press `space` to stage a hunk
4. Press `c` for an inline commit message or `Shift+C` to open your editor for a longer message
5. Type the message and press Enter (or save and close the editor)

**Option 2 - fzf-powered staging**:

```bash
git fa             # the fzf alias: pick files to stage with a diff preview
git cm "feat(users): add CSV export endpoint"
```

**Option 3 - plain git**:

```bash
git add -p         # interactive hunk staging (routed through delta with --color-only)
git commit -m "feat(users): add CSV export endpoint"
```

**Commit message conventions** (from [05-git-config-in-depth.md](05-git-config-in-depth.md) and your team's conventions):

```
feat(users): add CSV export endpoint

Adds a GET /users/export endpoint that streams user data as CSV.
Supports filtering by date range and status.

Closes #342
```

With `commit.verbose = true` in your config, the staged diff appears below the message template in your editor, so you can review exactly what you are committing while writing the message.

---

## 7. Mid-feature: handling interruptions

A colleague reports a production bug. You need to stop your current work and fix it.

**Stash your in-progress work**:

```bash
git stash push -u -m "WIP: user export - halfway through tests"
# or in lazygit: Files panel → s (stash)
```

**Switch to main and create a hotfix branch**:

```bash
git switch main
git pull
git switch --create fix/auth-token-expiry
```

Fix the bug, commit:

```bash
git cm "fix(auth): handle expired tokens correctly"
git push
# Open a PR via gh or your browser
```

**Return to your feature**:

```bash
z myproject        # in case you changed directories
gcb                # fzf fuzzy checkout → pick feature/user-export
git stash pop      # or in lazygit: Stash panel → g (pop)
```

The WIP commit message in the stash list makes it clear what you were in the middle of.

---

## 8. Keeping your branch up to date

While you have been working on your feature, main has moved forward (teammates' PRs were merged). Rebase your branch onto the current main to keep history clean and avoid a noisy merge commit.

```bash
git fetch --all --prune          # update all remote-tracking refs
git rebase origin/main           # rebase your branch onto the new main
```

With `rebase.autoStash = true` in your config, Git stashes any uncommitted changes automatically before rebasing and pops them after. You do not need to manually stash first.

**If there are conflicts**:

```bash
# Git pauses the rebase at the conflicting commit
# Open the conflicting files - zdiff3 conflict markers show all three versions
bat src/users/export.py   # see the conflict markers with syntax highlighting

# Resolve manually, then:
git add src/users/export.py
git rebase --continue

# Or in lazygit: m (continue), or open the merge tool with Shift+M
```

**With `rerere.enabled = true`** in your config, if you have resolved this exact conflict before (same two commits changing the same lines), Git automatically applies your previous resolution.

---

## 9. Cleaning up commits before a PR

You made 7 commits while working on the feature. Some are "WIP" commits, some are minor fixes to previous commits, and two could be squashed together. Before creating a PR, clean them up into a coherent set of commits that reviewers can easily follow.

**View your commits**:

```bash
git log --oneline origin/main..HEAD   # commits ahead of main
# or the lg alias:
git lg
```

**Option 1 - interactive rebase in lazygit**:

1. Open `lazygit`, press `4` for Commits panel
2. Navigate to the commit just before your feature's first commit (the last main commit)
3. Press `i` to start interactive rebase
4. Change commits:
   - WIP commits: `s` (squash) into the previous commit
   - Minor fixes: `f` (fixup) - squash and discard the message
   - Out-of-order: `Ctrl+J`/`Ctrl+K` to reorder
   - Wrong message: `r` to reword
5. Press `m` to run the rebase

**Option 2 - interactive rebase in the terminal**:

```bash
git rebase -i origin/main
# Your editor opens with the rebase todo list
# Change pick to squash/fixup/reword/drop as needed
# Save and close
```

**Option 3 - fixup workflow** (the cleanest approach for ongoing work):

While working, create fixup commits targeting specific earlier commits:

```bash
# Make a fix to the export endpoint commit
git add src/users/export.py
git commit --fixup abc1234   # abc1234 is the hash of the commit you are fixing

# At PR time, squash all fixups automatically:
git rebase --autosquash origin/main
```

The `--autosquash` flag (or `rebase.autoSquash = true` in config) reorders the fixup commits and squashes them into their targets automatically. lazygit has a "squash all fixup commits" option in the Commits panel menu (`x`).

**After cleaning up**:

```bash
git log --oneline origin/main..HEAD  # verify the clean commit list
git diff origin/main                 # verify the total diff is correct
```

---

## 10. Pushing and creating a pull request

**Push the branch**:

```bash
git push    # push.autoSetupRemote handles the first push automatically
```

If you rewrote history with rebase, this push will be rejected because the remote has your old history. Use a safe force push:

```bash
git push --force-with-lease --force-if-includes
# or the alias from 06-git-aliases.md:
git pf
```

**Create a pull request**:

```bash
# GitHub CLI - creates PR from current branch
gh pr create --title "feat(users): add CSV export" \
             --body "Adds CSV export endpoint for users. Closes #342."

# Or open the GitHub web interface
gh pr create --web
```

From lazygit (v0.61+), press `o` in the Branches panel to open the PR creation page in your browser directly.

---

## 11. Reviewing someone else's work

A colleague's PR needs review. Check it out and examine it.

**Fetch and checkout the PR branch**:

```bash
# GitHub CLI
gh pr checkout 456

# Or manually
git fetch origin pull/456/head:pr-456
git switch pr-456

# Or in lazygit: Branches panel → Pull Requests sub-tab → space to checkout
```

**Review the changes**:

```bash
# See all commits in this PR
git log --oneline origin/main..HEAD

# See the total diff against main
git diff origin/main    # routed through delta - syntax highlighted, side-by-side

# Browse the diff commit by commit in tig
tig origin/main..HEAD
```

**Check specific files**:

```bash
bat src/users/export.py   # read the file with syntax highlighting
git blame src/users/export.py  # see when each line was last changed
# or in tig:
tig blame src/users/export.py
```

**Run the code**:

```bash
# Run tests
npm test
# or
pytest tests/test_export.py -v
```

**Leave review comments**:

```bash
# Via GitHub CLI
gh pr review 456 --comment --body "Consider streaming for large datasets"
gh pr review 456 --approve
gh pr review 456 --request-changes --body "Please add error handling for empty result sets"
```

---

## 12. After the PR is merged

Your feature PR was merged into main. Clean up the local branch.

**Update main**:

```bash
git switch main
git pull
```

**Delete the local feature branch**:

```bash
git branch -d feature/user-export    # safe delete (only works if merged)
```

**Delete stale remote-tracking branches and any other gone branches**:

```bash
git fetch --prune        # removes refs to deleted remote branches
git gone                 # the alias from 06-git-aliases.md: delete all local branches whose remote is gone
# or git tidy (the alias that combines both)
```

In lazygit, you can see branches marked as "gone" (remote deleted) and delete them from the Branches panel.

---

## 13. Recovering from mistakes

These are the most common mistakes in daily terminal Git work and how to recover from them quickly.

**Committed to the wrong branch**:

```bash
# Get the commit hash
git log --oneline -1
# Returns: abc1234 feat: add export endpoint

# Reset the wrong branch (remove the commit, keep the changes)
git reset HEAD~1 --mixed   # or the undo alias

# Switch to the right branch and commit there
git switch feature/user-export
git add .
git commit -m "feat(users): add CSV export endpoint"
```

**Committed with the wrong author email**:

```bash
# Amend if it is the most recent commit
git commit --amend --reset-author --no-edit
# or in lazygit: Commits panel → Ctrl+X (amend and edit message)
```

**Accidentally staged a file you did not mean to**:

```bash
git restore --staged secrets.env   # unstage it
# or in lazygit: Files panel → space (toggles staging)
```

**Pushed something you should not have**:

1. If it was sensitive data: rotate the credential immediately, then follow the secret removal process from [04-terminal-safety.md](04-terminal-safety.md)
2. If it was just the wrong code: reset locally and force push:

```bash
git reset HEAD~1 --mixed    # undo the commit
git push --force-with-lease  # update the remote
```

**Deleted a branch you needed**:

```bash
# Find the lost commit in the reflog
git reflog | grep "branch: Created from"
# or search more broadly:
git reflog --all | head -30

# Recover it
git switch --create recovered-branch abc1234
```

**Lost work to git reset --hard**:

The working tree changes are gone - reflog only saves commits. But if you stashed first:

```bash
git stash list       # is there a stash?
git stash pop        # apply it
```

If not stashed, and the changes were never committed, they are gone. This is why stashing before risky operations is a habit worth building.

---

## 14. Putting it together - the full workflow at a glance

A typical feature development cycle with all tools:

```
Morning
  z myproject                          → jump to repo
  git switch main && git pull          → update main
  git lg                               → review what others merged

Feature start
  git swc feature/user-export          → create branch
  [write code]
  bat src/users/export.py              → review file as you go

Commit cycle (repeat multiple times)
  git s                                → quick status check
  lazygit (or git fa + git cm)        → stage and commit
  git diff --staged                   → verify before committing

Interruption
  git stash push -u -m "WIP"          → save in-progress work
  git switch main && git pull          → switch context
  git swc fix/bug && [fix] && git push → fix and push
  gcb → feature/user-export           → fuzzy switch back
  git stash pop                        → restore WIP

Pre-PR cleanup
  git fetch --all --prune              → update remote refs
  git rebase origin/main               → rebase onto current main
  lazygit rebase (i)                   → squash/fixup/reword commits
  git log --oneline origin/main..HEAD  → verify clean history

Publish
  git pf (or git push)                → push (force if rebased)
  gh pr create --web                   → open PR

Review
  gh pr checkout 456                   → checkout colleague's PR
  git diff origin/main                 → review total diff via delta
  tig blame src/file.py                → check specific lines

Cleanup after merge
  git switch main && git pull          → update main
  git branch -d feature/user-export   → delete local branch
  git tidy                             → prune all gone branches
```

---

## 15. Try It Yourself

**Exercise 1 - the full feature cycle**

In a test repository, run through the entire workflow above from scratch. Create a branch, make changes to three files across two commits, rebase onto main, squash the commits into one, push and verify the history is clean.

**Exercise 2 - handle an interruption**

While working on a feature branch with uncommitted changes, stash them with a descriptive name. Switch to main, create a hotfix branch, make a change and commit it. Switch back to your feature branch and pop the stash. Verify all your changes are restored.

**Exercise 3 - interactive rebase with lazygit**

Make five commits on a branch: two feature commits, two WIP commits and one typo fix. Open lazygit, go to the Commits panel, start an interactive rebase and squash the WIP commits into the feature commits. Reword the typo fix message. Verify the result with `git log --oneline`.

**Exercise 4 - recovering a lost commit**

Create a branch and make two commits. Run `git reset --hard HEAD~2` to move back before both. Run `git reflog` to find the lost commits. Create a new branch at one of those commits with `git switch --create recovered HEAD@{2}`. Verify your commits are back.

**Exercise 5 - full review workflow**

Find a recent pull request in any public GitHub repository you follow. Check it out with `gh pr checkout <number>`. View the total diff with `git diff origin/main`. Browse the changes file by file in tig. Run `tig blame` on a specific file to understand the context of a change.

---

## 16. Common Mistakes

Rebasing after already sharing the branch. If a colleague has checked out your feature branch and you rebase it (rewriting the SHAs), their branch and yours now diverge. Coordinate before rebasing a shared branch. If it is only you on the branch, rebase freely.

Forgetting to `git pull` on main before creating a feature branch. A branch created from an outdated main will diverge from the current remote main immediately. Always pull first: `git switch main && git pull && git switch --create feature/new-thing`.

Force pushing without `--force-with-lease`. A bare `git push --force` overwrites the remote regardless of what is there. Use `--force-with-lease` every time. The `pf` alias from [06-git-aliases.md](06-git-aliases.md) makes this the default for force pushes.

Stashing then forgetting about the stash. Stashes are silent unless you check them. Run `git stash list` periodically, especially if you have been context-switching. In lazygit, the Stash panel always shows any pending stashes.

Mixing unrelated changes in a single commit. This makes the commit history hard to read and makes reverting specific changes difficult. Even if it takes two extra minutes to split into two commits, reviewers will thank you and you will thank yourself in three months.

---

## 17. Summary

A productive terminal Git workflow combines specialised tools for each task rather than one tool for everything. zoxide provides instant repository navigation. Starship shows Git context in the prompt without running any commands. lazygit handles staging, committing, interactive rebase, cherry-pick and branch management with a keyboard-driven interface. delta makes every `git diff` output readable with syntax highlighting and side-by-side view. bat provides syntax-highlighted file reading with Git gutter markers. fzf powers fuzzy branch checkout, commit selection and file staging. tig provides fast commit history browsing and blame.

The workflow itself is standard Git: branch, commit, rebase, push, PR. The tools make each step faster and more visual without changing what Git does underneath. Every tool is optional - the plain `git` commands work without any of them. Start with delta (five minutes, immediate impact on every diff) and lazygit (the biggest quality-of-life upgrade for daily Git work), then add the others as you need them.

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

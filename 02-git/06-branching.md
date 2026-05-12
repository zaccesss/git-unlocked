# Branching

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Branching is one of Git's most powerful features and one of the things that makes it genuinely different from older version control systems. Understanding branches properly will change how you work.

---

## Table of Contents

- [What is a branch?](#what-is-a-branch)
- [Why branching matters](#why-branching-matters)
- [The default branch](#the-default-branch)
- [Creating branches](#creating-branches)
- [Listing branches](#listing-branches)
- [Switching branches](#switching-branches)
- [Renaming branches](#renaming-branches)
- [Deleting branches](#deleting-branches)
- [Pushing branches to a remote](#pushing-branches-to-a-remote)
- [Tracking branches](#tracking-branches)
- [Branch naming conventions](#branch-naming-conventions)
- [How branches work under the hood](#how-branches-work-under-the-hood)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Branch?

A branch is an independent line of development. It lets you work on something - a new feature, a bug fix, an experiment - in complete isolation from everything else in the repository.

Technically, a branch is nothing more than a lightweight pointer to a specific commit. It is a file containing 40 characters. Creating a branch in Git takes milliseconds and uses almost no storage, regardless of how large your repository is. This is fundamentally different from older systems like SVN where branching meant copying the entire project.

When you make a new commit on a branch, the branch pointer moves forward to point at that new commit. The rest of the repository is untouched.

---

## Why Branching Matters

Consider a team of four developers working on the same project. Without branches, every developer pushes directly to `main`. Half-finished features land in production. One developer's changes break another developer's work. Nobody can tell what is ready and what is not.

With branches, each developer works in isolation:

- Developer A works on `feature/user-authentication`
- Developer B works on `feature/payment-integration`
- Developer C works on `fix/checkout-crash`
- Developer D prepares `release/v2.1`

None of them interfere with each other. When a feature is complete and reviewed, it gets merged into `main`. The main branch stays stable and deployable at all times.

This is not just theory. The branch-per-feature workflow is standard practice at every professional software company. Learning to work with branches properly is one of the most important skills in this course.

---

## The Default Branch

When you run `git init`, Git creates a default branch. If you followed the setup guide and configured `init.defaultBranch`, that branch is called `main`. If not, older versions of Git use `master`.

Both `main` and `master` are just names - there is nothing special about either one technically. The convention has shifted to `main` across the industry. GitHub and GitLab both use `main` as their default for new repositories.

To check and set your default branch name globally:

```bash
git config --global init.defaultBranch main
```

This only affects new repositories you create. Existing repositories keep their current default branch name.

---

## Creating Branches

**Create a new branch (without switching to it):**

```bash
git branch feature/user-login
```

**Create a new branch and switch to it immediately (most common):**

```bash
git switch -c feature/user-login
```

The `-c` flag stands for "create". This is the command you will use most often - you almost always want to switch to the branch right after creating it.

**Create a branch from a specific commit or branch:**

```bash
git switch -c hotfix/payment-bug main
```

This creates the branch starting from `main` rather than from wherever you currently are. Useful when you need to branch off a specific point in history.

**Create a branch from a specific commit:**

```bash
git switch -c investigate/old-behaviour a1b2c3d
```

Replace `a1b2c3d` with the actual commit hash.

> [!TIP]
> Always create a new branch before starting any piece of work. Even for a small one-line fix. The habit of working on branches rather than directly on `main` is one of the most valuable things you can build as a developer.

---

## Listing Branches

**List all local branches:**

```bash
git branch
```

The branch you are currently on is marked with an asterisk `*` and shown in a different colour in most terminals.

**List all local and remote branches:**

```bash
git branch -a
```

Remote branches are shown as `remotes/origin/branch-name`.

**List only remote branches:**

```bash
git branch -r
```

**List branches with the last commit on each:**

```bash
git branch -v
```

**List branches that have been merged into the current branch:**

```bash
git branch --merged
```

This is useful for finding branches that are safe to delete because their work is already incorporated.

**List branches that have not been merged:**

```bash
git branch --no-merged
```

---

## Switching Branches

**Switch to an existing branch:**

```bash
git switch main
```

**Switch back to the previous branch:**

```bash
git switch -
```

The `-` is shorthand for "the branch I was on before this one". Extremely useful when moving between two branches repeatedly.

> [!WARNING]
> Git will not let you switch branches if you have uncommitted changes that conflict with the branch you are switching to. You have two options: commit your changes first, or stash them temporarily. Stashing is covered in [git/11-stash.md](11-stash.md).

> [!NOTE]
> You may see `git checkout branch-name` in older tutorials. This works but `git checkout` does many different things depending on its arguments - switching branches, restoring files and detaching HEAD. `git switch` was introduced specifically for switching branches and is the current recommendation. This course uses `git switch` throughout.

### What happens when you switch branches

When you switch branches, Git:

1. Moves the HEAD pointer to point at the new branch
2. Updates your working directory to match the state of that branch at its latest commit

Files that exist on the new branch but not the old one appear. Files that exist on the old branch but not the new one disappear. Files that are the same on both branches are unchanged.

This all happens instantly, regardless of how many files are in the repository.

---

## Renaming Branches

**Rename the current branch:**

```bash
git branch -m new-name
```

**Rename a specific branch:**

```bash
git branch -m old-name new-name
```

**Rename the remote branch after renaming locally:**

If you have already pushed the branch, you need to update the remote too:

```bash
git push origin -u new-name
git push origin --delete old-name
```

**Rename the default branch from `master` to `main` on an existing repo:**

```bash
git branch -m master main
git push -u origin main
git push origin --delete master
```

> [!NOTE]
> After renaming a branch on the remote, anyone else who has cloned the repository will need to update their local references. They can run `git fetch --prune` followed by `git branch -u origin/main main` to update their local tracking branch.

---

## Deleting Branches

**Delete a branch that has been fully merged:**

```bash
git branch -d feature/user-login
```

Git refuses to delete a branch that has unmerged commits, protecting you from accidentally losing work.

**Force delete a branch regardless of merge status:**

```bash
git branch -D feature/abandoned-experiment
```

Use this when you are certain you want to discard the branch's commits. The commits are not immediately gone - they remain accessible via `git reflog` for 30-90 days - but they will eventually be garbage collected.

**Delete a remote branch:**

```bash
git push origin --delete feature/user-login
```

Or the shorter form:

```bash
git push origin :feature/user-login
```

**Prune remote-tracking references that no longer exist on the remote:**

```bash
git fetch --prune
```

When someone else deletes a branch on the remote, your local list of remote branches (`git branch -r`) still shows it until you prune. Running this cleans up stale references.

> [!TIP]
> Make a habit of deleting branches after they are merged. A repository with dozens of stale branches is confusing and harder to navigate. After merging a pull request on GitHub, you are offered a "Delete branch" button - use it.

---

## Pushing Branches to a Remote

**Push a branch to the remote for the first time:**

```bash
git push -u origin feature/user-login
```

The `-u` flag sets up tracking so that future `git push` and `git pull` commands on this branch know which remote branch to use. After doing this once, you can just run `git push` without specifying the remote and branch name.

**Push to the remote without setting up tracking:**

```bash
git push origin feature/user-login
```

**Push all local branches:**

```bash
git push --all
```

---

## Tracking Branches

A **tracking branch** is a local branch that has a relationship with a remote branch. When your local `main` tracks `origin/main`, Git knows where to push and pull by default.

When you clone a repository, Git automatically sets up tracking for the default branch. When you push a branch with `-u`, tracking is set up for that branch.

**See tracking relationships for all branches:**

```bash
git branch -vv
```

Output shows each local branch, its last commit and which remote branch it tracks:

```
  feature/user-login a1b2c3d [origin/feature/user-login] add: login form
* main               e4f5g6h [origin/main] update: README
```

**Set up tracking for an existing branch:**

```bash
git branch -u origin/feature/user-login
```

**Check the upstream of the current branch:**

```bash
git rev-parse --abbrev-ref --symbolic-full-name @{upstream}
```

---

## Branch Naming Conventions

Git does not enforce any naming convention, but clear, consistent names make a huge difference in team environments. The most widely used convention:

```
type/short-description
```

**Common prefixes:**

| Prefix | Used for |
|---|---|
| `feature/` | New features |
| `fix/` or `bugfix/` | Bug fixes |
| `hotfix/` | Urgent fixes that go directly to production |
| `release/` | Release preparation branches |
| `chore/` | Maintenance, dependency updates, tooling |
| `docs/` | Documentation only changes |
| `experiment/` | Exploratory work that may be discarded |
| `refactor/` | Code restructuring without behaviour changes |

**Examples:**

```
feature/user-authentication
feature/dark-mode
fix/login-crash-on-mobile
fix/broken-payment-redirect
hotfix/critical-security-patch
release/v2.1.0
docs/update-api-reference
chore/update-dependencies
experiment/new-caching-strategy
```

**Rules for branch names:**

- Lowercase and hyphens only - no spaces, no underscores, no capitals
- Be specific enough that the name explains what the branch does
- Keep it reasonably short - under 50 characters is a good target
- Some teams prefix with the issue number: `feature/GH-142-user-authentication`

> [!NOTE]
> Branch names cannot contain: spaces, `~`, `^`, `:`, `?`, `*`, `\`, `..`, `@{` or start with `-` or end with `.lock`. Git will reject names with these characters.

---

## How Branches Work Under the Hood

Understanding what a branch actually is makes all the commands above make more sense.

A branch is a file in `.git/refs/heads/` containing nothing but a commit hash:

```
.git/refs/heads/main          <- contains "a1b2c3d4e5f6..."
.git/refs/heads/feature/login <- contains "e4f5g6h7i8j9..."
```

That is it. 40 characters. When you create a branch, Git creates this file. When you commit on a branch, Git updates the file to point at the new commit. When you delete a branch, Git deletes the file.

The diagram below shows three commits on `main` and a new branch `feature/login` that diverged after the second commit:

```
         main
          |
A -- B -- C
          \
           D -- E
                |
          feature/login
```

- `A`, `B`, `C` are commits on `main`
- After commit `C`, a new branch `feature/login` was created
- `D` and `E` are commits on `feature/login`
- `main` still points at `C` - it has not changed
- `feature/login` points at `E` - the latest commit on that branch

Both branches share commits `A`, `B` and `C` in their history. No data is duplicated. Git is simply using pointers.

When `feature/login` is merged back into `main`, Git combines `C` and `E` and creates a merge commit `F` that `main` points to.

---

## Try It Yourself

This exercise takes you through a realistic branch-based workflow.

**Step 1.** Create a repository and make an initial commit:

**Windows (PowerShell):**

```powershell
mkdir branch-practice && cd branch-practice && git init
echo "# Branch Practice" > README.md
git add README.md
git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir branch-practice && cd branch-practice && git init
echo "# Branch Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Create and switch to a feature branch:

```bash
git switch -c feature/about-page
```

**Step 3.** Confirm you are on the new branch:

```bash
git branch
```

You should see `* feature/about-page` and `main`.

**Step 4.** Make a commit on the feature branch:

**Windows:**

```powershell
echo "# About" > about.md
git add about.md
git commit -m "add: about page"
```

**Mac and Linux:**

```bash
echo "# About" > about.md
git add about.md && git commit -m "add: about page"
```

**Step 5.** Switch back to main and notice the file is gone:

```bash
git switch main
ls
```

**Windows:**

```powershell
git switch main
dir
```

`about.md` does not exist on `main`. It only exists on `feature/about-page`. This is branches working exactly as intended.

**Step 6.** View the log with branch graph:

```bash
git log --oneline --graph --all
```

You should see both branches with their respective commits.

**Step 7.** Create another branch off main:

```bash
git switch -c fix/readme-typo
echo "# Branch Practice - Fixed" > README.md
git add README.md
git commit -m "fix: correct typo in README heading"
```

**Step 8.** View the full branch graph:

```bash
git log --oneline --graph --all
```

You now have three branches diverging from different points.

**Step 9.** Delete the fix branch (without merging):

```bash
git switch main
git branch -D fix/readme-typo
```

**Step 10.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q branch-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf branch-practice
```

---

## Common Mistakes

**Working directly on `main` instead of a branch.**

The most common beginner mistake. Even for a small change, create a branch. It costs nothing and keeps your history clean and your main branch stable.

**Creating a branch but forgetting to switch to it.**

`git branch feature/login` creates the branch but leaves you on your current branch. Any commits you make go to the current branch, not the new one. Use `git switch -c feature/login` to create and switch in one step.

**Switching branches with uncommitted changes.**

If you have modified files that conflict with the branch you are switching to, Git will refuse. Either commit your work or stash it with `git stash` before switching. Stashing is covered in [git/11-stash.md](11-stash.md).

**Deleting a branch before merging its commits.**

`git branch -d` protects you from this by refusing to delete unmerged branches. If you use `-D` to force delete, the commits become unreachable from any branch. They are recoverable via `git reflog` but only for 30-90 days.

**Pushing to the wrong branch.**

If you are on `feature/login` and run `git push origin main`, you push your feature branch's commits to the remote `main` branch. Always verify which branch you are on with `git branch` or `git status` before pushing.

**Not pulling before creating a new branch.**

If you create a branch off an outdated `main`, your branch starts from an old point in history. Always pull the latest changes on `main` before branching off it.

```bash
git switch main
git pull
git switch -c feature/my-new-feature
```

---

## Summary

- A branch is a lightweight pointer to a commit - not a copy of your code
- Create and switch to a new branch with `git switch -c branch-name`
- List branches with `git branch`, including remote branches with `git branch -a`
- Switch branches with `git switch branch-name`
- Rename a branch with `git branch -m new-name`
- Delete a merged branch with `git branch -d branch-name`, force delete with `-D`
- Delete a remote branch with `git push origin --delete branch-name`
- Push a new branch and set up tracking with `git push -u origin branch-name`
- Use descriptive names with a prefix: `feature/`, `fix/`, `hotfix/`, `release/`
- Always create a branch before starting work - never commit directly to `main`

---

## Sources and Further Reading

- [Official Git branching documentation](https://git-scm.com/docs/git-branch) - the complete reference for `git branch`
- [Pro Git book, Chapter 3: Git Branching](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell) - the definitive guide to branching in Git
- [git switch documentation](https://git-scm.com/docs/git-switch) - official docs for the `git switch` command
- [Atlassian - Using branches](https://www.atlassian.com/git/tutorials/using-branches) - clear visual guide to branching concepts and commands
- [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow) - GitHub's recommended lightweight branching workflow, covered in depth in [git/12-git-workflows.md](12-git-workflows.md)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

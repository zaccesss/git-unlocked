# Core Concepts

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Before you start running Git commands, you need to understand the vocabulary. Every Git tutorial assumes you know these terms. This file explains them clearly, with real examples, so nothing catches you off guard later.

---

## Table of Contents

- [Repository](#repository)
- [Commit](#commit)
- [The staging area](#the-staging-area)
- [Branch](#branch)
- [Remote](#remote)
- [Clone](#clone)
- [Fork](#fork)
- [Pull and push](#pull-and-push)
- [Merge](#merge)
- [Conflict](#conflict)
- [Tag](#tag)
- [How the concepts connect](#how-the-concepts-connect)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Repository

A **repository** (often shortened to **repo**) is a folder that Git is tracking. It contains your project files and a hidden `.git` folder where Git stores the entire history of every change ever made to those files.

There are two kinds of repository:

**Local repository** - the copy on your own computer. This is where you do your work. You can commit, branch, merge and view history entirely offline. Nothing leaves your machine until you push.

**Remote repository** - a copy hosted on a server, typically GitHub or GitLab. This is what you push to when you want to share your work or back it up online. It is also where collaborators pull from.

You create a local repository in one of two ways:

- **`git init`** - turn an existing folder into a Git repository
- **`git clone`** - download a repository from a remote and create a local copy

> [!NOTE]
> A repository is just a folder with a `.git` subfolder. If you delete the `.git` folder, Git stops tracking the project entirely and all history is lost. The project files themselves remain, but they are no longer version controlled.

---

## Commit

A **commit** is a saved snapshot of your project at a specific point in time. It is the fundamental unit of Git history.

Every commit records:

- The exact state of every tracked file at that moment
- Who made the commit (name and email)
- When it was made (timestamp)
- A message describing what changed and why
- A reference to the previous commit (its parent)

Commits are identified by a unique hash - a 40-character string like `a1b2c3d4e5f6...`. You will usually see the shortened 7-character version: `a1b2c3d`.

Think of commits like save points in a video game. Each save point captures your exact progress at that moment. If something goes wrong, you can load any previous save point and start again from there. Unlike most save systems, Git keeps every save point forever and lets you compare any two of them.

### What makes a good commit

A commit should represent one logical unit of work. Not a whole feature necessarily, but one coherent change that can be described in a single sentence.

**Poor commit messages:**
- `fix`
- `update stuff`
- `changes`
- `wip`
- `asdfgh`

**Good commit messages:**
- `fix: resolve login crash when email field is empty`
- `add: dark mode toggle to settings page`
- `update: improve error message for invalid API key`
- `remove: deprecated payment gateway integration`

A good message answers the question: "If applied, this commit will..." Complete that sentence and you have your message.

> [!TIP]
> Commit often. Small, frequent commits are far easier to work with than large, infrequent ones. If something breaks, a small commit is easier to identify as the cause. If you need to revert, you lose less work. If someone reviews your code, smaller commits are easier to understand.

---

## The Staging Area

The **staging area** (also called the **index**) is a holding area between your working directory and your commits. It lets you control exactly what goes into each commit.

When you edit a file, the change is in your **working directory**. It is not in Git's history yet and it has not been staged.

When you run `git add filename`, you copy that file's current state into the **staging area**. You are saying: "include this version of this file in my next commit."

When you run `git commit`, everything in the staging area is packaged into a new commit and saved permanently to Git's history. The staging area is then cleared.

### Why the staging area exists

Imagine you spent the afternoon working and changed eight files. Three of them are a complete feature. Two of them are a bug fix. The other three are half-finished experiments you are not ready to commit yet.

Without a staging area, you would have to commit all eight files together or none of them. With the staging area, you can:

1. Stage the three feature files and commit them with the message `add: user profile page`
2. Stage the two bug fix files and commit them with the message `fix: resolve session timeout on mobile`
3. Leave the three unfinished files in your working directory for later

This produces a clean, understandable history instead of one giant messy commit.

### Visualising the three areas

```
Working Directory          Staging Area             Repository (.git)
      |                         |                         |
  You edit                  git add                   git commit
  files here    --------->  files here   --------->  saves snapshot
                                                      to history
```

---

## Branch

A **branch** is an independent line of development. It lets you work on something - a new feature, a bug fix, an experiment - without affecting the main codebase.

As covered in [How Git Works](03-how-git-works.md), a branch is technically just a pointer to a commit. Creating a branch is nearly instantaneous and uses almost no storage.

The default branch in a new Git repository is called **`main`** (older repositories may use `master`).

### Why branches matter

Imagine a team of five developers all working on the same project. Without branches, every developer would be constantly stepping on each other's changes. Someone pushes a half-finished feature and breaks the build for everyone.

With branches, each developer works on their own branch. They can commit freely without affecting anyone else. When a feature is complete and tested, it gets merged back into `main`. The main branch stays stable.

This workflow - one branch per feature or fix - is called **feature branch workflow** and is the standard in professional development.

### Common branch names you will encounter

- `main` or `master` - the primary branch, usually the production-ready code
- `develop` or `dev` - a branch for integrating features before they go to main
- `feature/user-login` - a branch for a specific new feature
- `fix/login-crash` - a branch for a specific bug fix
- `release/v2.0` - a branch for preparing a release

Branch names are just labels. The convention above is common but not enforced by Git.

---

## Remote

A **remote** is a version of your repository hosted somewhere else - typically GitHub, GitLab or Bitbucket. It is where you push your work to share it or back it up, and where you pull from to get other people's work.

When you clone a repository, Git automatically creates a remote called **`origin`** pointing to the URL you cloned from. You can have multiple remotes, but `origin` is the conventional name for the primary one.

**`upstream`** is another common remote name. If you fork someone else's repository and want to pull in their latest changes, you add their repository as a remote called `upstream`.

You can see all configured remotes with:

```bash
git remote -v
```

---

## Clone

**Cloning** is the act of downloading a complete copy of a remote repository to your local machine.

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

This does three things:

1. Downloads the entire repository - every commit, every branch, every file in the history
2. Creates the folder `git-unlocked` on your computer with all the project files checked out
3. Sets up `origin` as a remote pointing back to the URL you cloned from

Cloning is how you start working on an existing project. You clone once, then pull to get updates as the project evolves.

> [!NOTE]
> When you clone a repository, you get a **complete independent copy** of all its history. You are not connected to the original in a way that requires a network to work. You can commit, branch and view history entirely offline. The network is only needed when you push or pull.

---

## Fork

A **fork** is a copy of someone else's repository made to your own GitHub or GitLab account. It is not a Git concept - it is a GitHub/GitLab feature.

Forking is the standard way to contribute to open source projects:

1. You fork the repository to your account (creates your own copy on GitHub/GitLab)
2. You clone your fork to your local machine
3. You make changes on a branch in your local clone
4. You push your branch to your fork on GitHub/GitLab
5. You open a pull request asking the original project to merge your changes

The difference between a fork and a clone:

- A **clone** is a local copy on your machine
- A **fork** is a remote copy on GitHub/GitLab under your account

You can fork without cloning (your fork exists only on GitHub/GitLab). You can clone without forking (you have a local copy but no remote copy under your account). In the open source contribution workflow, you typically do both.

---

## Pull and Push

**Push** sends your local commits to a remote repository.

```bash
git push
```

You push when you want to share your work - to back it up on GitHub, to let collaborators see your changes or to deploy to a server.

**Pull** downloads commits from a remote repository and merges them into your local branch.

```bash
git pull
```

You pull when you want to get the latest changes from a remote - to see what your collaborators have pushed, to update before starting new work or to get the latest version of a project.

> [!TIP]
> Always pull before you start working for the day. This ensures your local copy is up to date before you make new changes, reducing the likelihood of conflicts when you push later.

### Pull vs fetch

`git pull` is actually two operations combined: `git fetch` followed by `git merge`.

`git fetch` downloads the latest commits from the remote but does not apply them to your working branch. It updates your knowledge of what is on the remote without changing your files.

`git merge` then applies those downloaded commits to your current branch.

Most beginners use `git pull` and never need to separate these steps. The distinction matters in more advanced workflows, covered in [git/09-remote-repos.md](09-remote-repos.md).

---

## Merge

**Merging** combines the history of two branches into one. When a feature branch is complete, it gets merged back into `main`.

```bash
git merge feature/user-login
```

Git looks at the two branches, finds their common ancestor (the commit where they diverged) and combines the changes from both into a new merge commit.

If the two branches changed different files, or different parts of the same file, Git can merge them automatically. If they changed the same lines of the same file in different ways, Git cannot decide which version is correct - this is a conflict.

Merging is covered in depth in [git/07-merging.md](07-merging.md).

---

## Conflict

A **conflict** occurs when two branches have made different changes to the same part of the same file, and Git cannot automatically decide which version to keep.

Git stops the merge and marks the conflicting sections in the file:

```
<<<<<<< HEAD
This is the version from your current branch
=======
This is the version from the branch being merged
>>>>>>> feature/user-login
```

You resolve the conflict by editing the file to contain the correct final version, removing the conflict markers and then completing the merge.

Conflicts sound scary but they are normal and manageable. They are Git's way of asking: "I found two different versions of this - which one do you want?" The answer is always in your hands.

Merge conflicts are covered in full in [git/08-merge-conflicts.md](08-merge-conflicts.md).

---

## Tag

A **tag** is a named reference to a specific commit, typically used to mark release versions.

Where a branch pointer moves forward with every new commit, a tag stays permanently attached to one commit. When you release version 2.0 of your software, you tag that commit `v2.0`. Six months later, after dozens more commits, you can still check out `v2.0` exactly as it was.

Tags come in two types:

**Lightweight tags** - just a name pointing to a commit. Simple and quick to create.

**Annotated tags** - a full object in Git's database with a name, email, date, tagging message and optionally a GPG signature. Recommended for release tags because they contain more information and can be verified.

Tags are covered in [git/16-tags-and-releases.md](16-tags-and-releases.md).

---

## How the Concepts Connect

Here is how all these concepts fit together in a typical workflow:

```
GitHub/GitLab (remote)
       |
     clone          <- you download the project once
       |
Local repository
       |
  create branch     <- you make a safe space to work
       |
  edit files        <- working directory
       |
   git add          <- staging area
       |
  git commit        <- saves snapshot to local history
       |
  git commit        <- another snapshot
       |
   git push         <- sends your commits to GitHub/GitLab
       |
 pull request       <- you ask for your branch to be merged
       |
   git merge        <- your branch joins main
```

Every concept in this file maps to one step in this workflow. Once you understand this flow, Git starts to feel logical rather than arbitrary.

---

## Try It Yourself

This exercise introduces the core workflow using real Git commands. You will create a repository, make commits and look at the history.

**Step 1.** Create a new project folder and initialise Git:

**Windows (PowerShell):**

```powershell
mkdir my-first-repo
cd my-first-repo
git init
```

**Mac and Linux:**

```bash
mkdir my-first-repo && cd my-first-repo && git init
```

**Step 2.** Configure your identity for this repo (skip if you already configured globally in the setup guide):

```bash
git config user.name "Your Name"
git config user.email "your@email.com"
```

**Step 3.** Check the status - Git shows you the current state:

```bash
git status
```

You should see `On branch main` and `No commits yet`.

**Step 4.** Create a file:

**Windows:**

```powershell
echo "# My First Repo" > README.md
```

**Mac and Linux:**

```bash
echo "# My First Repo" > README.md
```

**Step 5.** Check the status again:

```bash
git status
```

You should now see `README.md` listed as an untracked file. It is in your working directory but not yet staged.

**Step 6.** Stage the file:

```bash
git add README.md
```

**Step 7.** Check the status again:

```bash
git status
```

`README.md` is now listed under "Changes to be committed". It is in the staging area.

**Step 8.** Make your first commit:

```bash
git commit -m "add: initial README"
```

**Step 9.** View the commit history:

```bash
git log --oneline
```

You should see one commit with your message and a short hash.

**Step 10.** Make a second change and commit it:

**Windows:**

```powershell
echo "This project is for learning Git." >> README.md
git add README.md
git commit -m "update: add project description to README"
```

**Mac and Linux:**

```bash
echo "This project is for learning Git." >> README.md
git add README.md
git commit -m "update: add project description to README"
```

**Step 11.** View the history again:

```bash
git log --oneline
```

You now have two commits. You can see the hash and message for each.

**Step 12.** See what changed between the two commits (replace the hashes with your actual ones):

```bash
git diff HEAD~1 HEAD
```

This shows the difference between the previous commit and the current one.

**Step 13.** Clean up:

**Windows:**

```powershell
cd ..
rmdir /s /q my-first-repo
```

**Mac and Linux:**

```bash
cd .. && rm -rf my-first-repo
```

You have just completed the core Git workflow: init, edit, stage, commit, view history, compare changes.

---

## Common Mistakes

**Committing to `main` directly instead of creating a branch.**

On a personal project, committing directly to `main` is fine. On a team project, it is a problem - it makes it hard to review changes, easy to break things for everyone and impossible to have parallel work in progress. Get into the habit of creating a branch for every piece of work.

**Writing vague commit messages.**

`update`, `fix`, `changes` and `wip` are not useful commit messages. Three months from now, you will not remember what you fixed. Neither will your collaborators. Write messages that describe what the commit does in one clear sentence.

**Staging everything with `git add .` without checking what you are staging.**

`git add .` stages all modified and new files in the current directory and all subdirectories. This is convenient but can accidentally include files you did not mean to commit - log files, environment files with passwords, compiled binaries or half-finished work. Run `git status` before `git add .` to see exactly what will be staged.

**Thinking `git push` and `git commit` are the same thing.**

`git commit` saves a snapshot to your _local_ repository. Nothing is shared with anyone. `git push` sends your local commits to the remote repository. You can commit a hundred times and if you never push, no one else will ever see those commits.

**Confusing fork and clone.**

A fork is a remote copy on GitHub or GitLab under your account. A clone is a local copy on your machine. In open source contribution, you fork first (on GitHub) and then clone your fork (to your machine). Cloning the original directly means you cannot push your changes back - you do not have write access to someone else's repository.

---

## Summary

- A **repository** is a folder Git is tracking, containing your files and a `.git` folder with all the history
- A **commit** is a saved snapshot of your project at a specific moment, with a message, author and timestamp
- The **staging area** is a preparation zone between your working directory and commits - `git add` moves files here, `git commit` packages them into history
- A **branch** is a lightweight pointer to a commit, enabling parallel lines of development
- A **remote** is a version of your repository hosted elsewhere, typically GitHub or GitLab
- **Cloning** downloads a complete copy of a remote repository to your machine
- **Forking** creates a copy of someone else's repository under your own GitHub or GitLab account
- **Push** sends your local commits to a remote; **pull** downloads remote commits to your local branch
- **Merging** combines two branches into one; a **conflict** occurs when Git cannot automatically resolve differences
- A **tag** is a permanent named reference to a specific commit, used for marking releases

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the complete reference for all Git commands and concepts
- [Pro Git book, Chapter 2: Git Basics](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository) - the official guide to the core Git workflow
- [Pro Git book, Chapter 3: Git Branching](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell) - how branches work in depth
- [GitHub glossary](https://docs.github.com/en/get-started/learning-about-github/github-glossary) - GitHub's official definitions for common terms
- [Atlassian Git tutorials](https://www.atlassian.com/git/tutorials) - well-written beginner to advanced tutorials covering all core concepts with diagrams

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

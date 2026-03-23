# How Git Works

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Most Git tutorials teach you commands. This file teaches you what Git is actually doing when you run them. Understanding this makes everything else click - you stop memorising commands and start understanding them.

You do not need to memorise everything in this file. Read it once, understand the concepts, and come back to it when something confuses you later.

---

## Table of Contents

- [Git is not what you think it is](#git-is-not-what-you-think-it-is)
- [The three states](#the-three-states)
- [The three areas](#the-three-areas)
- [How Git stores data](#how-git-stores-data)
- [What a commit actually is](#what-a-commit-actually-is)
- [What a branch actually is](#what-a-branch-actually-is)
- [What HEAD is](#what-head-is)
- [The .git folder](#the-git-folder)
- [The basic Git workflow](#the-basic-git-workflow)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Git is Not What You Think It Is

Most people assume Git works like a traditional backup system - that it saves the differences between file versions and reconstructs them when needed. This is how CVS and SVN work. Git does something fundamentally different.

**Git stores snapshots, not differences.**

Every time you commit, Git takes a picture of every file in your project at that exact moment and stores a reference to that picture. If a file has not changed since the last commit, Git does not store it again - it stores a link to the identical file it already has. If a file has changed, Git stores the new version in full.

This is why Git is so fast. Switching branches, viewing history and comparing versions do not require Git to reconstruct files by replaying a chain of changes. The complete state of every file at every commit is already stored and ready to retrieve.

This snapshot model is the foundation of everything Git does. Keep it in mind as you read the rest of this file.

---

## The Three States

Every file in a Git repository exists in one of three states. Understanding these states is fundamental to understanding what Git commands do.

### Modified

The file has been changed since the last commit but has not been told to Git yet. Git knows the file exists (because it was in the last commit) and it can see that something has changed, but it has not been instructed to include those changes in the next commit.

### Staged

The file has been marked to go into the next commit. You have told Git: "include this version of this file in the next snapshot." The staged version is an exact copy of the file at the moment you staged it. If you modify the file again after staging it, the staged version does not update automatically - you would need to stage it again.

### Committed

The file's current state has been safely stored in the Git database as part of a commit. It is in the history permanently (unless you deliberately rewrite history, which is covered in a later file).

The workflow moves in one direction: **modified -> staged -> committed**.

---

## The Three Areas

Corresponding to the three states, there are three areas where your files live during a Git workflow.

### Working directory (also called working tree)

This is the folder on your computer where you actually edit files. It is what you see in VS Code's file explorer, in Windows Explorer, in Finder on Mac or when you run `ls` in the terminal.

The working directory is your workspace. Git knows what is in it and can compare it to what was last committed, but nothing in your working directory is saved to Git's history until you explicitly stage and commit it.

### Staging area (also called the index)

This is a preparation zone that sits between your working directory and the Git repository. When you run `git add`, you are copying a snapshot of the file from your working directory into the staging area.

The staging area lets you be precise about what goes into each commit. If you changed five files but only three of them are ready to commit, you stage just those three. The other two stay in the working directory as modified files, ready to be staged in a future commit.

Think of the staging area like a loading dock. Your working directory is the warehouse full of goods. The staging area is where you select and pack exactly what goes onto the next truck. The commit is the truck leaving with its load.

### Git repository (the .git folder)

This is where Git permanently stores the history of your project. It lives in a hidden folder called `.git` at the root of your project. Every commit you have ever made, every branch, every tag - all of it is stored here as a series of objects in Git's database.

You rarely need to interact with the `.git` folder directly. Git commands read from and write to it on your behalf.

---

## How Git Stores Data

Git's internal storage is based on a simple but powerful concept: **content-addressable storage**.

Every piece of data Git stores - every file version, every commit, every tree - is stored as an **object** and given a name based on a cryptographic hash (SHA-1, moving to SHA-256 in Git 3.0) of its contents. This hash is a 40-character hexadecimal string like:

```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

You see shortened versions of these everywhere in Git - the 7-character abbreviations like `a1b2c3d` that appear in `git log --oneline`.

This has a profound implication: **the same content always produces the same hash**. If you have two files with identical contents in different folders, Git stores them once and references them from both locations. Storage is deduplicated automatically.

It also means **any corruption is immediately detectable**. If a file is changed - even a single bit flipped by a failing hard drive - its hash no longer matches. Git knows the data is corrupted.

Git uses four types of objects:

**Blob** - stores the contents of a file. A blob contains only the file data, not the filename or any metadata. Two files with identical content share one blob.

**Tree** - stores a directory listing. A tree contains pointers to blobs (for files) and other trees (for subdirectories), along with filenames and permissions. A tree represents the complete state of a directory at a specific moment.

**Commit** - stores a snapshot of the entire project. A commit points to a tree (the root directory of the project at that moment), one or more parent commits, and metadata: author name, author email, committer name, committer email, timestamp and the commit message.

**Tag** - stores a named reference to a specific commit, used for marking releases. Tags are covered in [git/15-tags-and-releases.md](15-tags-and-releases.md).

---

## What a Commit Actually Is

A commit is not a diff. It is not a set of changes. It is a **complete snapshot** of your entire project at a specific moment, along with metadata about who created it and why.

When you run `git commit`, Git:

1. Takes all the files currently in the staging area
2. Creates blob objects for any files that have changed (unchanged files already have blobs from previous commits)
3. Creates a tree object representing the current state of the project directory
4. Creates a commit object pointing to that tree, the previous commit (the parent) and your name, email, timestamp and message
5. Moves the current branch pointer to point at the new commit

A commit object looks roughly like this internally:

```
tree   4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
author Your Name <your@email.com> 1711234567 +0000
committer Your Name <your@email.com> 1711234567 +0000

add: initial project structure
```

Each commit points to its parent, which points to its parent, all the way back to the very first commit (which has no parent). This chain of commits is the history of your project.

This is why Git history is so reliable. You cannot change a commit without changing its hash. And if you change a commit's hash, every commit that descends from it also gets a different hash. The entire chain is cryptographically linked. This is also why "rewriting history" in Git is a real, deliberate operation that affects every commit downstream.

---

## What a Branch Actually Is

This surprises most people: **a branch in Git is just a pointer to a commit**.

Not a copy of your code. Not a separate folder. Just a lightweight, moveable pointer - a file containing a 40-character hash - that points to one specific commit.

When you create a branch, Git creates a new pointer file. That is almost literally all it does. Creating a branch in Git is nearly instantaneous and uses almost no disk space, regardless of how large your repository is.

When you make a new commit on a branch, Git creates the commit object and moves the branch pointer to point to the new commit. The old commit still exists - the branch pointer simply moved forward.

This is fundamentally different from older VCS tools like SVN, where creating a branch meant copying the entire project. That is why branching in SVN was expensive and slow. In Git, it is free.

---

## What HEAD Is

HEAD is a special pointer that tells Git which branch you are currently on - or more precisely, which commit your working directory is based on.

In most cases, HEAD points to a branch name, not directly to a commit. For example, if you are on the `main` branch, HEAD contains the text `ref: refs/heads/main`. When you make a new commit, the `main` pointer moves forward, and HEAD (which points to `main`) effectively moves forward too.

When HEAD points directly to a commit rather than a branch name, you are in what Git calls **detached HEAD state**. This happens when you check out a specific commit directly. You can look around and even make commits, but those commits will not be saved to any branch - they will eventually be lost unless you create a branch from them.

You can always see where HEAD is pointing:

```bash
git log --oneline -1
```

Or to see the full reference:

```bash
cat .git/HEAD
```

---

## The .git Folder

Every Git repository contains a hidden `.git` folder at its root. This folder is the entire Git repository - the history, the configuration, the staging area and everything else. The files outside `.git` are just your working directory.

> [!CAUTION]
> Never manually edit files inside `.git`. You can corrupt your repository in ways that are very difficult to recover from. Git commands exist for everything you need to do.

The most important contents of `.git`:

```
.git/
├── HEAD              - points to the current branch
├── config            - repository-specific configuration
├── description       - used by GitWeb (not important for everyday use)
├── COMMIT_EDITMSG    - the message from the last commit
├── index             - the staging area (binary format)
├── objects/          - all Git objects: blobs, trees, commits, tags
│   ├── pack/         - packed objects for efficiency
│   └── info/
└── refs/
    ├── heads/        - one file per branch, each containing a commit hash
    └── tags/         - one file per tag
```

The `objects/` folder is where everything lives. Every file version, every tree, every commit - all stored as objects named by their hash.

The `refs/heads/` folder contains one tiny file per branch. The file for `main` contains nothing but the hash of the commit that `main` currently points to. That is a branch.

---

## The Basic Git Workflow

Now that you understand the internals, the basic workflow makes much more sense:

**Step 1 - Modify files in your working directory.**

You edit files normally - in VS Code, in any editor, however you work. Git is watching but not recording anything yet.

**Step 2 - Stage the changes you want to include in the next commit.**

```bash
git add filename.md
```

Or to stage all changes:

```bash
git add .
```

This copies a snapshot of the specified files from your working directory into the staging area (the index). Git creates blob objects for the new file versions.

**Step 3 - Commit the staged changes.**

```bash
git commit -m "your commit message"
```

Git takes everything in the staging area, creates a tree object, creates a commit object pointing to that tree and the previous commit, and moves the current branch pointer to the new commit.

**Step 4 - Push to a remote repository (optional).**

```bash
git push
```

Git sends the new commits (and any referenced objects the remote does not already have) to the remote repository - GitHub, GitLab or wherever you have configured.

That is the complete cycle. Every `git add` copies a snapshot to the staging area. Every `git commit` packages the staging area into a permanent snapshot in the repository. Every `git push` shares those snapshots with others.

---

## Try It Yourself

This exercise creates a real repository and uses Git's low-level commands to see exactly what is stored internally.

> [!NOTE]
> The `git cat-file` command used below is a plumbing command - a low-level Git command used to inspect internal objects. You will not use it in everyday work, but it is invaluable for understanding what Git is actually doing.

**Step 1.** Create a test repository:

**Windows (PowerShell):**

```powershell
mkdir git-internals-test
cd git-internals-test
git init
```

**Mac and Linux:**

```bash
mkdir git-internals-test && cd git-internals-test && git init
```

**Step 2.** Create a file and stage it:

**Windows:**

```powershell
echo "hello git" > hello.txt
git add hello.txt
```

**Mac and Linux:**

```bash
echo "hello git" > hello.txt
git add hello.txt
```

**Step 3.** Look at what Git stored in the objects folder:

**Windows (PowerShell):**

```powershell
Get-ChildItem .git/objects -Recurse -File
```

**Mac and Linux:**

```bash
find .git/objects -type f
```

You should see one file. That is the blob object for `hello.txt`.

**Step 4.** Make a commit:

```bash
git config user.name "Test User"
git config user.email "test@test.com"
git commit -m "initial commit"
```

**Step 5.** Look at the objects folder again:

**Windows:**

```powershell
Get-ChildItem .git/objects -Recurse -File
```

**Mac and Linux:**

```bash
find .git/objects -type f
```

You should now see three objects: the blob (file contents), the tree (directory listing) and the commit.

**Step 6.** View the commit log to get the commit hash:

```bash
git log --oneline
```

Copy the 7-character hash shown.

**Step 7.** Inspect the commit object (replace `abc1234` with your actual hash):

```bash
git cat-file -p abc1234
```

You will see the commit object's contents: the tree hash, author, committer, timestamp and message.

**Step 8.** Clean up:

**Windows:**

```powershell
cd ..
rmdir /s /q git-internals-test
```

**Mac and Linux:**

```bash
cd .. && rm -rf git-internals-test
```

You have just looked inside Git's object database and seen exactly how a commit, tree and blob relate to each other.

---

## Common Mistakes

**Thinking `git add .` saves your work to Git history.**

`git add .` only moves files to the staging area. Nothing is saved to history until you run `git commit`. Many beginners run `git add .` and assume their work is backed up - it is not. You must commit.

**Thinking a branch is a copy of your code.**

A branch is a pointer - 40 bytes. Creating 10 branches does not use 10 times the storage. It uses 400 bytes plus one new commit object per branch. This is why branching in Git is encouraged, not avoided.

**Deleting the .git folder thinking it will "reset" the project.**

Deleting `.git` does not reset your project - it destroys your entire Git repository, including all history, all branches and all commits. Your working directory files remain, but all version control history is gone permanently. If you want to start fresh with Git, delete `.git` and run `git init` again, but understand you are losing all history.

**Editing files inside .git manually.**

Always use Git commands. The `.git` folder is a database. Manually editing files in it is like editing a database's binary files with a text editor - easy to corrupt, hard to fix.

**Confusing the working directory with the repository.**

The repository is inside `.git`. Your working directory is the files outside `.git` that you edit. They are related but separate. `git status` shows you the difference between them.

---

## Summary

- Git stores **snapshots**, not diffs - every commit is a complete picture of the project, not a list of changes
- Every file exists in one of three states: **modified** (changed but not staged), **staged** (ready to commit) or **committed** (safely in Git's history)
- The three areas are the **working directory** (where you edit), the **staging area** (where you prepare commits) and the **repository** (where history is stored, inside `.git`)
- Git uses **content-addressable storage**: every object is named by the SHA-1 hash of its contents, making corruption detectable and storage deduplicated
- The four object types are **blob** (file contents), **tree** (directory listing), **commit** (snapshot with metadata) and **tag** (named reference)
- A **branch** is just a lightweight pointer to a commit - not a copy of your code
- **HEAD** points to the current branch (or directly to a commit in detached HEAD state)
- The basic workflow is: modify files -> `git add` to stage -> `git commit` to save to history -> `git push` to share

---

## Sources and Further Reading

- [Pro Git book, Chapter 10: Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) - the definitive guide to how Git works internally, written by the Git team
- [Pro Git book, Chapter 1.3: What is Git?](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F) - the official introduction to Git's snapshot model vs delta model
- [Official Git documentation](https://git-scm.com/doc) - the complete reference for all Git commands and concepts
- [Git objects explained](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects) - deep dive into blobs, trees, commits and tags from the Pro Git book
- [Think like a Git](https://think-like-a-git.net) - a free guide focused specifically on building a mental model of how Git works, highly recommended for beginners who want to understand before memorising

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

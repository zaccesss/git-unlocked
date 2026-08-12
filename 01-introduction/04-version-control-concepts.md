# Version control concepts

**Difficulty:** 🟢 Beginner | **Time:** 15 minutes

What version control is, why it exists, how it evolved and how Git fits into all of it - before any commands.

---

## Table of contents

- [What is version control?](#what-is-version-control)
- [The problems it solves](#the-problems-it-solves)
- [A brief history of version control](#a-brief-history-of-version-control)
- [How Git is different](#how-git-is-different)
- [The core mental model](#the-core-mental-model)
- [Snapshots, not differences](#snapshots-not-differences)
- [Local, then remote](#local-then-remote)
- [Everything is addressable](#everything-is-addressable)
- [Git vs GitHub vs GitLab](#git-vs-github-vs-gitlab)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is version control?

Version control is a system that records changes to files over time so you can recall specific versions later. That is the whole idea. Everything else - branches, pull requests, CI/CD pipelines, GitOps - builds on top of that one idea.

You have almost certainly already used a primitive form of version control. Every time you saved a file as `essay_final.docx`, then `essay_final_v2.docx`, then `essay_final_ACTUALLY_FINAL.docx`, you were doing version control by hand. You kept a history of your work. You could go back. You just did it in the most painful possible way.

A proper version control system does this automatically, systematically and in a way that works for both individuals and large teams.

---

## The problems it solves

Version control systems were not created for fun. They exist because specific problems keep coming up whenever anyone works on anything non-trivial over time.

**"I broke something and I do not know what I changed."**
Without version control, fixing a regression means reading through your own code trying to remember what you changed. With version control, you can see exactly what changed and when, down to the line.

**"I want to try a risky change without affecting the working version."**
Version control lets you create a branch - a parallel copy of your work where you can experiment freely. If the experiment works, you merge it. If it does not, you throw it away. The main version is untouched throughout.

**"Two people changed the same file and now we have a conflict."**
When two developers make different changes to the same file, version control detects the conflict and helps you merge the two versions rather than silently overwriting one.

**"What did this file look like six months ago?"**
Version control stores the complete history of every change, who made it and when. Answering this question takes one command.

**"Who wrote this line and why?"**
Every line of code in a version-controlled project is annotated with the commit that introduced it, the author and the message explaining the reasoning.

---

## A brief history of version control

Understanding where Git came from explains why it works the way it does.

### Local version control (1970s-1980s)

The earliest systems kept a database of patches on a single machine. **RCS** (Revision Control System, 1982) stored the differences between file versions as a series of patches. To reconstruct a file at any point in history, it applied patches forward from the original. This worked well for single files on a single machine.

### Centralised version control (1980s-2000s)

As teams grew, local-only systems were not enough. **CVS** (Concurrent Versions System, 1986) and **Subversion** (SVN, 2000) introduced a central server that held the full history. Developers checked files out from the server, made changes and committed back.

This model has a serious weakness: the central server is a single point of failure. If the server goes down or the disk is lost, the entire history is gone. It also meant that every operation - viewing history, comparing versions, committing - required a network connection to the server.

**Perforce** and **ClearCase** dominated the enterprise through the 1990s and 2000s and are still used in industries like game development and defence where large binary assets are common.

### Distributed version control (2000s-present)

**BitKeeper** was a proprietary distributed VCS used by the Linux kernel project from 2002 to 2005. When the free licence was revoked, Linus Torvalds had a problem: the kernel had thousands of contributors submitting patches and the existing free tools could not handle the volume and complexity.

His solution was to write his own. **Git was created in April 2005** and took ten days to reach a state where it could host its own development. Linus's stated goals were speed, simple design, strong support for non-linear development (branches), fully distributed operation and the ability to handle the scale of the Linux kernel.

**Mercurial** was created at almost the same time and for similar reasons. It remains in use at Meta (Facebook) and Mozilla, though Git has become the dominant distributed VCS by a wide margin.

---

## How Git is different

Git makes different design choices from its predecessors and those choices affect how you use it.

**Git is distributed.** Every developer has a complete copy of the entire history on their local machine. You do not need a network connection to view history, create branches, commit changes or compare versions. The remote repository (on GitHub, GitLab or elsewhere) is just one more copy - an agreed-upon reference point for collaboration, not the single source of truth.

**Git is fast.** Because most operations are local, they are nearly instantaneous. Branching, merging, comparing history and committing do not require a round trip to a server. The data structures Git uses (a content-addressed object store) are optimised for the operations developers do most often.

**Git takes snapshots.** Most earlier systems stored changes as a series of differences from the previous version (delta encoding). Git stores a snapshot of every file at each commit. If a file does not change between two commits, Git stores a reference to the previous identical version rather than copying it. This makes it fast to reconstruct any version of the project without replaying a chain of patches.

**Git guarantees integrity.** Every object in Git is identified by a SHA-1 hash of its content. If a file is corrupted, the hash will not match and Git will know. You cannot quietly lose or corrupt data without Git detecting it.

---

## The core mental model

Before typing a single command, understanding this model will save you hours of confusion later.

A Git repository has three main areas:

**The working tree** is the directory of files you can actually see and edit. When you open a file in your editor, you are working in the working tree.

**The staging area** (also called the index) is an intermediate space where you assemble your next commit. You choose exactly which changes to include. This is what makes Git's commit model powerful - you can commit some of your changes without committing all of them.

**The repository** (the `.git` folder) is where Git stores the full history as a database of objects. Once something is committed, it is essentially permanent. Even if you later "delete" a commit, the data usually remains in the repository for at least 30 days.

The flow of changes moves in one direction: working tree → staging area → repository. The commands `git add` moves changes from the working tree to the staging area. `git commit` moves changes from the staging area into the repository.

Going the other direction - recovering old versions, checking out branches, resetting - is also possible and Git provides specific commands for each scenario.

---

## Snapshots, not differences

This is the most important conceptual shift when coming from SVN or CVS.

SVN stores your project as an original version plus a sequence of changes:

```
Version 1: original files
Version 2: +5 lines to auth.py, -2 lines to main.py
Version 3: +12 lines to database.py
```

To reconstruct version 3, SVN starts at version 1 and applies change 2, then change 3. For large histories this becomes slow.

Git stores a complete snapshot of every file at each commit:

```
Commit A: {auth.py: v1, main.py: v1, database.py: v1}
Commit B: {auth.py: v2, main.py: v1*, database.py: v1*}
Commit C: {auth.py: v2*, main.py: v1*, database.py: v2}
```

Files marked with `*` are pointers to the identical version from the previous commit - Git does not duplicate them. Reconstructing any commit is direct: Git reads the snapshot, fetching each file version from its hash. There is no chain to replay.

This is why branching in Git is nearly instant. Creating a branch in Git just creates a new pointer. No files are copied. No history needs to be replayed. The entire branch exists as a 41-byte file containing one SHA hash.

---

## Local, then remote

When you work with Git, almost everything happens locally first.

When you `git commit`, the commit goes into your local repository - not to GitHub or GitLab. You can make dozens of commits without any network access. When you are ready to share your work, `git push` sends those commits to the remote.

This means:

- You can work on a plane with no internet
- Committing is instantaneous (no waiting for a server)
- You can experiment, make mistakes and clean them up before anyone else sees your work
- Losing your internet connection does not interrupt your work

The flip side: changes other people push are not automatically visible to you. You need to `git fetch` (download their changes) explicitly. `git pull` combines fetch and merge in one step.

---

## Everything is addressable

Every object in Git has a unique identifier: a 40-character hexadecimal string derived from a SHA-1 hash of the object's content. These identifiers are called SHAs or hashes.

```
commit a3b7c2d1e4f5...
```

Because the hash is derived from content, the same content always produces the same hash. Two different commits with identical content produce identical hashes (though this is extraordinarily rare in practice). If content changes, the hash changes.

This property means you can always refer to any version of any file in your history by its SHA. Commands like `git show a3b7c2d`, `git checkout a3b7c2d -- path/to/file` and `git diff a3b7c2d HEAD` all use this addressing system.

You rarely need to type full 40-character SHAs. Git accepts any unambiguous prefix, usually 7-8 characters.

---

## Git vs GitHub vs GitLab

This distinction trips up almost every beginner. They are not the same thing.

**Git** is the version control software. It is a command-line tool that runs on your local machine. It has no web interface. It has no issue tracker. It was created by Linus Torvalds in 2005 and is developed by the open-source community. It is completely free.

**GitHub** is a web platform that hosts Git repositories. It adds a web interface for browsing history, a pull request system for code review, issue tracking, project boards, CI/CD (GitHub Actions), a marketplace and many other features. It was founded in 2008 and acquired by Microsoft in 2018.

**GitLab** is another web platform for hosting Git repositories. It includes similar features to GitHub plus a more integrated CI/CD pipeline (GitLab CI/CD), a container registry, security scanning and the ability to self-host the entire platform. Founded in 2011.

**Bitbucket**, **Azure DevOps**, **Gitea**, **Forgejo** and **Codeberg** are also platforms for hosting Git repositories, each with different focuses and audiences.

You can use Git without any of these platforms. You can host your own remote repository on any server. But in practice, almost every team uses one of these platforms because they add enormous value on top of raw Git.

Think of it this way: Git is the engine. GitHub, GitLab and the others are garages where you park the engine and where people can come see it, work on it together and leave notes on it.

---

## Try it yourself

These exercises require no Git installation. They are purely conceptual.

**Exercise 1: spot the version control**
Look at the folder structure on your computer. Find examples of informal version control you are already doing: files named `report_final`, `report_final_v2`, `report_backup`, folders with dates in their names. Count how many you find. This is the problem version control systems solve.

**Exercise 2: think about the distributed model**
Consider: if you use GitHub to host a repository and GitHub's servers were deleted overnight, what would happen to your project? Write down your answer. Then compare: with Git (distributed), every developer's clone contains the full history. The project could be reconstructed from any one of them.

**Exercise 3: the snapshot question**
If Git stores a complete snapshot of every file at every commit and a large project has 10,000 files and 5,000 commits, would the repository be enormous? Why or why not? (Hint: think about what happens when a file does not change between commits.)

---

## Common mistakes

**Thinking Git and GitHub are the same thing.**
Git is the tool. GitHub is one of many places you can store and share Git repositories. You can use Git entirely without GitHub. Many organisations use GitLab, Bitbucket, Azure DevOps or self-hosted solutions.

**Thinking commits go to GitHub automatically.**
`git commit` stores changes in your local repository only. `git push` is the separate command that sends them to a remote. This surprises many beginners who expect a commit to immediately appear on GitHub.

**Thinking a branch is a copy of all your files.**
A branch in Git is a pointer - a 41-byte file containing one SHA hash. Creating a branch takes microseconds and uses negligible disk space. This is completely different from how branch-like operations worked in SVN.

**Thinking the staging area is optional bookkeeping.**
The staging area is a deliberate design choice. It lets you craft a commit that contains exactly the changes you intend, nothing more. `git add -p` (interactive patch staging) lets you stage individual hunks within a file. This granularity is how professional developers produce clean, atomic commits even when they have made a mess of their working tree.

**Thinking "losing" a commit means it is gone forever.**
Git almost never permanently deletes data. Commits that appear lost because of a `reset --hard` or accidental branch deletion remain in the repository for at least 30 days and are recoverable via `git reflog`. True permanent deletion only happens when `git gc` runs after the retention period expires. The course covers recovery in detail in [real-world/06-recovering-from-disasters.md](../08-real-world/06-recovering-from-disasters.md).

---

## Summary

Version control systems record changes to files over time so you can recover any previous state. They evolved from local tools (RCS) to centralised servers (SVN) to the distributed model that Git represents, where every developer has a complete copy of the full history.

Git was created by Linus Torvalds in 2005 to handle the scale and complexity of Linux kernel development. Its key design choices - distributed operation, snapshot-based storage, content-addressed objects and near-instant branching - make it faster and more flexible than its predecessors.

The three areas to understand before typing any commands are the working tree (files you edit), the staging area (changes you are assembling into a commit) and the repository (the permanent history). Changes flow from working tree to staging area to repository via `git add` and `git commit`.

Git is the underlying software. GitHub, GitLab, Bitbucket and others are web platforms that host Git repositories and add collaboration features on top. They are entirely separate things.

---

## Sources and Further Reading

- [Pro Git - Getting Started: About Version Control](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)
- [Pro Git - Getting Started: A Short History of Git](https://git-scm.com/book/en/v2/Getting-Started-A-Short-History-of-Git)
- [Pro Git - Git Basics: What is Git?](https://git-scm.com/book/en/v2/Getting-Started-What-is-Git%3F)
- [MIT Missing Semester: Version Control (Git)](https://missing.csail.mit.edu/2020/version-control/)
- [Git turns 20: a Q&A with Linus Torvalds](https://github.blog/open-source/git/git-turns-20-a-qa-with-linus-torvalds/)
- [Atlassian: What is version control?](https://www.atlassian.com/git/tutorials/what-is-version-control)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

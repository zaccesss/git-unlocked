# What is Version Control?

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Version control is the foundation of everything in this course. Before you type a single Git command, you need to understand what version control actually is, why it exists and how it evolved into the system the entire software world relies on today.

---

## Table of Contents

- [The problem version control solves](#the-problem-version-control-solves)
- [What version control is](#what-version-control-is)
- [The three types of version control](#the-three-types-of-version-control)
- [A complete history of version control](#a-complete-history-of-version-control)
- [Why Git won](#why-git-won)
- [Git versus other systems today](#git-versus-other-systems-today)
- [Why version control matters beyond software](#why-version-control-matters-beyond-software)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## The Problem Version Control Solves

Before version control existed, developers managed code changes manually. If you have ever worked on a document or project without version control, you have experienced the chaos first-hand.

Folders like this were normal:

```
project_final.zip
project_final_v2.zip
project_final_REAL.zip
project_final_REAL_v2_USE_THIS_ONE.zip
project_backup_before_john_broke_everything.zip
```

This approach - known informally as _folder versioning_ or _zip backup versioning_ - causes real, serious problems:

- **No clear history.** Which version is the latest? What changed between them? Nobody knows without opening and comparing every file.
- **Collaboration is nearly impossible.** If two people edit the same file at the same time, one person's changes will overwrite the other's when they merge the files manually.
- **Mistakes are permanent.** If you delete something or introduce a bug, you may have no way to recover the previous working version.
- **No accountability.** When something breaks, there is no record of who changed what, when, or why.

These are not minor inconveniences. They cause real disasters.

### Real disaster: Knight Capital Group (2012)

On 1 August 2012, Knight Capital Group - at the time handling approximately 17% of all NYSE trading volume - **lost $440 million in 45 minutes** due to a deployment failure rooted in poor code management practices.

Developers had written a new trading algorithm but reused a feature flag that controlled old, deprecated code still sitting on production servers. The deployment script had to be run manually on eight servers. It failed silently on one of them. When markets opened, seven servers ran the new code correctly, but the eighth activated the old algorithm by mistake. The result was 4 million unintended orders in 45 minutes.

The deployment script itself had never been tested or tracked in version control. There was no reliable way to roll back. By the time developers realised what was happening and tried to fix it, they accidentally deployed the broken code to all eight servers.

Knight Capital's stock dropped 75% in two days. The company required a $400 million rescue and never recovered independently.

A proper version control and deployment workflow would not have prevented every aspect of this disaster, but it would have made rollback trivial and the history of changes auditable.

### Real disaster: Toy Story 2 near-deletion (1998)

During production of _Toy Story 2_, a Pixar employee accidentally ran `rm -rf` - a command that permanently deletes files - on the project directory. By the time the server was shut down, approximately 90% of the film had been deleted.

Worse, the backup system had been silently failing for a month. The tape drive had hit its storage limit and was overwriting old backups with new data without raising any alerts. Pixar had no usable backup.

The film was saved entirely by accident. Supervising Technical Director Galyn Susman had been working from home after giving birth and had a complete copy of the project on her personal computer. She and a colleague drove her desktop computer - buckled into the passenger seat of her car - back to Pixar.

In a modern Git workflow, **every developer's clone is a full backup of the entire repository including all history**. If the server had been lost, any team member's clone could have restored everything instantly.

---

## What Version Control Is

**Version control** is a system that records changes to files over time so that specific versions can be recalled later.

Think of it like an extremely detailed save history for your entire project. Not just the most recent version, but every version you have ever deliberately saved - who saved it, when they saved it, what they changed and why.

A good analogy: imagine you are writing a novel and every time you reach the end of a writing session, you make a photocopy of the entire manuscript and label it with the date and a note about what you wrote. If you decide tomorrow that you wrote the wrong ending, you can go back to any previous copy and start again from there. Version control does exactly this, but automatically, efficiently and for entire codebases rather than single documents.

More precisely, a version control system lets you:

- **Save snapshots** of your project at any point in time (called _commits_)
- **Compare** any two snapshots to see exactly what changed
- **Revert** to any previous snapshot if something goes wrong
- **Branch** - create a parallel version of your project to work on a feature or fix without affecting the main codebase
- **Merge** - combine changes from different branches back together
- **Collaborate** - allow multiple people to work on the same project simultaneously without overwriting each other's work
- **Track accountability** - see who changed what, when and why

---

## The Three Types of Version Control

Version control systems have evolved through three distinct architectural models. Understanding them helps you understand why Git works the way it does.

### Local version control

The simplest approach. Changes are tracked in a database stored entirely on your own computer. The most well-known example is RCS (Revision Control System), which stored changes as a series of patches - the differences between file versions.

**The problem:** If your computer fails, everything is lost. There is no way to collaborate with others.

Think of it like keeping a change diary on your desk. Useful for personal reference, but if the desk catches fire, the diary is gone.

### Centralised version control

A single server holds the entire repository. Developers check files out from the server, make changes and check them back in. Examples include CVS and Apache Subversion (SVN).

This model solved the collaboration problem - everyone works from the same central source of truth. Administrators can control who has access to what. You can see what everyone else is working on.

**The problem:** The central server is a single point of failure. If it goes down, nobody can work. If it is lost without a backup - as the _Toy Story 2_ story illustrates at scale - everything is gone. As the Pro Git book states: "If the hard disk the central database is on becomes corrupted... you lose absolutely everything."

Think of it like a library with one copy of every book. If the library burns down, every book is lost. Every reader had to return their copy before leaving.

### Distributed version control

Every developer clones the _entire_ repository - not just the latest snapshot, but the complete history of every commit ever made. Examples include Git and Mercurial.

When you clone a Git repository, you are not just downloading the current files. You are downloading a complete, independent copy of every commit, every branch and every version of every file in the history. If the server disappears, any developer's clone can restore it entirely.

Operations like viewing history, creating branches and committing changes happen locally - no network required. This makes Git extremely fast for everyday operations.

Think of it like every reader having a complete copy of the entire library, including the history of every edit ever made to every book. If any single copy is destroyed, every other copy survives intact.

**Git is a distributed version control system.** This is the architecture that makes it so powerful and so resilient.

---

## A Complete History of Version Control

### SCCS: where it all began (1972)

The **Source Code Control System** was created by **Marc J. Rochkind** at Bell Laboratories in late 1972. It is the first true version control system ever built.

Rochkind originally wrote SCCS in SNOBOL4 for an IBM System/370. He rewrote it in C for UNIX in 1973. The first public release came on 18 February 1977 as part of the Programmer's Workbench edition of UNIX.

SCCS managed files in isolation. Each file had its own separate history file. It used a **pessimistic locking model**: when one programmer checked out a file, no one else could edit it. Only one person could work on a file at a time.

Despite these limitations, SCCS established the foundational concepts every modern version control system still uses: check-in and check-out, revision history and delta-based storage. Rochkind received the IEEE Most Influential Paper Award in 1989 for his 1975 paper describing it.

### RCS: faster access, same limitations (1982)

**Walter F. Tichy** at Purdue University released the **Revision Control System** on 25 March 1982. His motivation was practical: his university could not afford the extra licence fee AT&T charged for SCCS.

RCS introduced **reverse deltas** - storing the most recent version in full and using edit instructions to reconstruct older versions. This made retrieving the latest version significantly faster than SCCS, since it was a simple copy rather than applying a chain of patches. Tichy claimed up to 60% faster performance for typical operations.

RCS remained a single-file, single-user system with locking. It added more flexible branching and symbolic revision names, but collaboration was still impossible in any meaningful sense. It became a GNU project in 1990 and remains available today.

### CVS: the concurrent editing revolution (1986)

**Dick Grune** at Vrije Universiteit Amsterdam began work on what would become the **Concurrent Versions System** in July 1984. He created it to collaborate with students on the Amsterdam Compiler Kit - the three had wildly different schedules and the locking model of RCS made joint work impossible.

Grune released the code publicly on 23 June 1986. In April 1989, **Brian Berliner** rewrote CVS as a proper C program. This codebase became modern CVS.

CVS introduced a paradigm shift: the **copy-modify-merge model** (also called optimistic concurrency). Multiple developers could edit the same file simultaneously. Conflicts - places where two people changed the same lines differently - were flagged for manual resolution during merging. No locking was required.

CVS also pioneered anonymous read access, allowing anyone to check out code without authentication. This was transformational for the open source movement. CVS became the dominant version control system for open source projects throughout the 1990s.

However, CVS had critical and well-documented flaws. It had **no atomic commits** - if a commit was interrupted midway, the repository could be left in a corrupt, inconsistent state. It had no directory versioning, no rename tracking and notoriously poor branching and merging. By the late 1990s, the phrase "CVS is broken" was a common frustration in the developer community.

### Subversion: CVS done right (2000)

In February 2000, **CollabNet** approached **Karl Fogel** about building a better VCS. Fogel was already discussing designs with **Jim Blandy**, who had conceived the name "Subversion" and the core repository architecture. CollabNet hired them both along with **Ben Collins-Sussman**. The explicit goal was to be "CVS, but better" - match CVS's features and workflow while fixing its worst flaws.

Subversion (SVN) delivered **atomic commits** - a commit either completes fully or does not happen at all, preventing the corruption issues that plagued CVS. It added directory versioning, rename tracking, native binary file support and global monotonically increasing revision numbers. Branching was implemented as a cheap copy operation - constant time regardless of project size.

SVN became self-hosting on 31 August 2001, after 14 months of development. It became an Apache Software Foundation top-level project in February 2010.

SVN dominated the 2000s as the CVS replacement. Major adopters included the Apache Software Foundation, FreeBSD, Python, Ruby and WordPress. However, SVN remained a **centralised** system. The central server was still a single point of failure, and most operations required a network connection.

### BitKeeper: the catalyst that created Git (2000-2005)

**Larry McVoy** developed BitKeeper through his company BitMover Inc. The first public release was 4 May 2000. BitKeeper offered something genuinely revolutionary: a **distributed version control model** where entire repositories could be forked and merged easily.

The Linux kernel project adopted BitKeeper in 2002. Before this, Linus Torvalds was effectively the version control system himself - manually applying patches submitted as tarballs via mailing lists. BitKeeper allowed sub-groups of kernel developers to collaborate independently and merge changes upstream. Linus later described BitKeeper as "the first source control system I ever felt was worth using at all."

The free community version came with restrictions: users could not participate in developing any competing VCS, and change metadata had to be stored on BitMover's servers. Many prominent developers refused to use it on principle. Richard Stallman was openly opposed.

**The crisis arrived in early 2005.** Andrew Tridgell - creator of Samba and rsync - began reverse-engineering the BitKeeper protocol to create an open-source alternative client. On 23 February 2005, McVoy learned of this and demanded it stop. The Linux community refused to comply. In April 2005, BitMover announced it would end the free community licence for Linux kernel developers, effective 1 July 2005.

BitKeeper was eventually open-sourced under the Apache 2.0 licence in May 2016 - but by then, Git had already taken over the world.

### Git: built in 10 days, changed everything (April 2005)

With the BitKeeper licence revoked, **Linus Torvalds** began writing Git on **3 April 2005**. He announced it publicly on **6 April**. Git became self-hosting on **7 April** - the very first commit (`e83c5163`) carries the message: _"Initial revision of 'git', the information manager from hell."_

In a 2025 interview marking Git's 20th anniversary, Torvalds stated: "It was about 10 days until I could use it for the kernel." The Linux kernel was imported into Git on 16 April 2005. By 29 April, Git was processing patches at **6.7 patches per second**.

Torvalds had precise, demanding goals:

- **Speed.** "Everything you ever do on a daily basis should take less than a second." Patching should take no more than 3 seconds, versus 30 in competing systems.
- **Distributed workflow.** Every clone is a full repository. No single point of failure.
- **Data integrity.** SHA-1 cryptographic hashing verifies every object against corruption, whether accidental or malicious.
- **Non-linear development.** Branching must be cheap, fast and core - not an afterthought.
- **Anti-CVS design philosophy.** Torvalds described his approach as WWCVSND - "What Would CVS Not Do?" If in doubt, make the exact opposite decision from CVS.

**The name "Git"** was a deliberate joke. Torvalds said: _"I'm an egotistical bastard, and I name all my projects after myself. First 'Linux', now 'git'."_ "Git" is British slang for an unpleasant or contemptible person. The official README offers multiple definitions: a random three-letter combination; "stupid, contemptible and despicable"; **"Global Information Tracker"** when it works; and something considerably more colourful when it does not.

On **26 July 2005**, Torvalds handed maintenance to **Junio C. Hamano**, who had been contributing since approximately one week after the first commit. **Junio Hamano remains Git's maintainer to this day.** Git 1.0 was released on 21 December 2005. Git 2.0 - the first intentionally backwards-incompatible release - arrived on 1 June 2014. The current stable release is **Git 2.53.0** (February 2026).

### Mercurial: the road not taken (April 2005)

**Matt Mackall** (now Olivia Mackall), a Linux kernel developer, announced Mercurial on the Linux Kernel Mailing List on **19 April 2005** - just 13 days after Torvalds announced Git. Both were direct responses to the BitKeeper crisis.

The name was inspired by a description of Larry McVoy as "mercurial" (meaning volatile or unpredictable). The command-line tool is `hg` - the chemical symbol for mercury. Written primarily in Python, Mercurial was cross-platform from day one - a significant advantage over early Git, which was difficult to use on Windows for years.

Mercurial was considered to have a gentler learning curve and more intuitive interface. Notable adopters included Facebook (2013), Mozilla Firefox (2007) and Google. However, its decline has been steep. Bitbucket dropped Mercurial support in July 2020. Mozilla began migrating Firefox to Git in 2023. Facebook's internal fork diverged so far it became **Sapling SCM** (open-sourced November 2022), which is now Git-compatible rather than Mercurial-compatible.

---

## Why Git Won

Git's dominance is not accidental. Five factors compounded to make it effectively universal.

**Technical superiority.** Git's distributed model gives every developer a complete repository clone - full history, all branches, all commits. Every clone is a backup. Local operations (commits, branching, merging, diffs) are fast with no network required. Branches are lightweight pointers (40 bytes), making them nearly free to create.

**The GitHub effect.** GitHub launched in April 2008 and was the single most important factor in Git's dominance. By making Git repositories social - with stars, forks, followers and pull requests - GitHub created a powerful network effect. Once major open source projects moved there, every developer needed Git to contribute. By December 2008, GitHub hosted approximately 27,000 public repositories. Competitors hosted fewer than 1,000.

**Open source gravitational pull.** The Linux kernel used Git from day one, giving it immediate credibility at the largest scale imaginable. Google adopted Git for Android in March 2009. Open source projects migrated en masse from SourceForge and CVS/SVN.

**Microsoft's endorsement.** Microsoft acquired GitHub in June 2018 for $7.5 billion. Microsoft itself migrated the Windows codebase to Git - the largest Git repository in the world. This legitimised Git in enterprise settings that had previously resisted it.

**The tipping point.** In 2009, SVN held 58% developer adoption versus Git's 2% (Eclipse annual survey). By 2014, Git surpassed SVN. By 2018, Git reached 87-88% in Stack Overflow surveys. By 2022, **93.87%**. Stack Overflow stopped asking about VCS preferences in 2019 because Git was so dominant - they only asked again in 2022 to confirm the question was still settled.

---

## Git Versus Other Systems Today

### Subversion (SVN)

SVN remains the second most popular VCS at approximately 5% developer adoption. It persists in manufacturing, chip design, government and financial services - industries where centralised access control with path-based permissions matters for compliance, and where large binary files are managed more predictably. The latest release is Apache Subversion 1.14.5 (2024). SVN is maintained but development is slow.

**When you might still encounter SVN:** Legacy enterprise codebases, regulated industries with specific compliance requirements, organisations with very large binary assets (3D models, audio files) that predate Git LFS.

### Mercurial

Mercurial sits at approximately 1% adoption and declining. Bitbucket dropped support in 2020. Mozilla migrated away in 2023. It has a small dedicated community but mainstream adoption is effectively over.

### Perforce (Helix Core)

Perforce is the standard in **game development and visual effects**. It claims usage by 19 of the top 20 AAA game studios. The reason is structural: game assets (3D models, textures, audio) are large binary files that cannot be meaningfully merged - they require exclusive file locking. Individual assets can be hundreds of megabytes and delta compression provides no benefit on files that change entirely between versions.

**When you will encounter Perforce:** AAA game studios (Epic Games, EA, Ubisoft, Activision), VFX studios (DNEG, Industrial Light & Magic), semiconductor companies and automotive manufacturers working with large binary assets.

### The rest

Microsoft's TFVC (Team Foundation Version Control) is effectively deprecated - new Azure DevOps projects no longer create TFVC repositories by default as of June 2024. IBM ClearCase persists in legacy defence and aerospace. Fossil SCM is used by the SQLite project, notable for bundling wiki and bug tracking in a single SQLite database.

**For 94% of developers, the answer is Git.** The question is not whether to use Git - it is how to use it well.

---

## Why Version Control Matters Beyond Software

Version control is not a software development tool. It is a tool for managing any files that change over time - and that description applies to almost every knowledge-work discipline.

**Data science.** Git tracks Jupyter notebooks, machine learning models, data pipelines and experiment results. Researchers can reproduce any experiment by checking out the exact code and configuration used at any point in time. This is why universities worldwide now teach Git as part of data science curricula.

**DevOps and infrastructure.** GitOps treats Git as the single source of truth for infrastructure configuration. Every change to a server, cloud resource or Kubernetes deployment goes through a Git pull request and is applied automatically. Over 64% of surveyed organisations had adopted GitOps practices by 2025.

**Academic research.** Git enables reproducible research. A researcher can publish not just their findings but the exact code, data and environment used to generate them - allowing anyone to verify or build on the work. Harvard, MIT, Johns Hopkins and universities worldwide now integrate GitHub into statistics and data science courses.

**Technical writing and documentation.** Documentation teams use Git branching and pull requests for collaborative writing and review. Every change is tracked, reviewed and auditable - the same workflow that makes code collaboration work applies directly to prose.

**Government.** The US federal government's code.gov policy requires agencies to inventory custom software and release at least 20% of it as open source. GitHub holds FedRAMP authorisation for government compliance. France has published its entire legal code as a GitHub repository.

---

## Try It Yourself

You do not need Git installed for this exercise. This is about understanding, not commands.

Think about a project you have worked on recently - a university assignment, a work document, a personal project or anything else. Answer these questions honestly:

1. How did you manage different versions of it? Numbered files, email attachments, cloud sync, something else?
2. How would you recover a version from three weeks ago if you needed to?
3. If two people had worked on it simultaneously, how would you have combined their changes?
4. If something broke, how would you identify exactly what changed and when?

Write down your answers. These are the exact problems Git solves. When you start using Git, revisit these answers - you will see how each Git feature maps directly to a problem you have personally experienced.

---

## Common Mistakes

**Thinking Git and GitHub are the same thing.**

Git is software installed on your computer. GitHub is a website. Git existed for three years before GitHub launched. You can use Git with no internet connection and no GitHub account at all. This distinction matters because it affects how you think about what each tool does and where your data lives.

**Thinking version control is only for code.**

Version control is for any files that change over time and where history matters. Configuration files, documentation, research notebooks, legal documents, design source files - all of these benefit from version control.

**Thinking you need to understand everything before you start.**

You do not. Start with the five most common commands and build from there. Every developer, including very experienced ones, continues learning Git throughout their career.

**Thinking mistakes in Git are permanent.**

Almost nothing in Git is permanent. Git is specifically designed to make recovery possible. Once something is committed, it is very difficult to lose permanently. The reflog - Git's internal safety net - keeps records of everything for 30 to 90 days even after deletions.

---

## Summary

- Version control is a system that records changes to files over time, enabling you to recall specific versions, compare changes, revert mistakes and collaborate with others
- The three types are local (one computer), centralised (one server, multiple clients) and distributed (every developer has the full repository)
- Version control evolved from SCCS (1972) through RCS (1982), CVS (1986) and SVN (2000) before Git (2005) changed everything
- Git was created by Linus Torvalds in approximately 10 days in April 2005, following the loss of access to BitKeeper for Linux kernel development
- Git dominates with approximately 94% developer adoption worldwide, driven by technical superiority, GitHub's network effect and Microsoft's endorsement
- SVN, Mercurial and Perforce persist in specific niches but Git is the default choice for the overwhelming majority of new projects
- Version control matters far beyond software - in data science, DevOps, academic research, technical writing and government

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the complete reference for all Git commands and concepts
- [Pro Git book](https://git-scm.com/book/en/v2) - the free official Git book. Chapter 1 covers the history of version control in depth
- [Git turns 20: A Q&A with Linus Torvalds](https://github.blog/open-source/git/git-turns-20-a-qa-with-linus-torvalds/) - Linus Torvalds on Git's creation in his own words (GitHub Blog, 2025)
- [Journey through Git's 20-year history](https://about.gitlab.com/blog/journey-through-gits-20-year-history/) - GitLab's detailed timeline of Git's development (2025)
- [A history of source control systems: SCCS and RCS](https://experimentalworks.net/posts/2024-03-18-a-history-of-vcs-part1/) - detailed technical history of the earliest VCS tools
- [Version control systems popularity in 2025](https://rhodecode.com/blog/156/version-control-systems-popularity-in-2025) - current adoption statistics across VCS tools (RhodeCode)
- [Beyond Git: The other version control systems developers use](https://stackoverflow.blog/2023/01/09/beyond-git-the-other-version-control-systems-developers-use/) - Stack Overflow's analysis of non-Git VCS usage
- [GitHub Octoverse 2025](https://github.blog/news-insights/octoverse/) - the annual report on developer trends and GitHub statistics
- [Apache Subversion project](https://subversion.apache.org) - the official SVN website and documentation

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

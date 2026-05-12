# Git Internals - Advanced

**Difficulty:** 🔴 Advanced | **Time:** 25 minutes

This file goes deeper into how Git works internally - packfiles, the reflog, garbage collection and the object model at a level that helps you understand why Git behaves the way it does, diagnose unusual situations and recover from scenarios that seem irreversible. Most developers never need this level of detail, but understanding it makes you genuinely authoritative about Git.

---

## Table of Contents

- [The object model revisited](#the-object-model-revisited)
- [Packfiles](#packfiles)
- [The reflog in depth](#the-reflog-in-depth)
- [Garbage collection](#garbage-collection)
- [Reachability](#reachability)
- [The index in depth](#the-index-in-depth)
- [Maintenance and optimisation](#maintenance-and-optimisation)
- [Recovering lost commits](#recovering-lost-commits)
- [Inspecting the object database](#inspecting-the-object-database)
- [The commit graph](#the-commit-graph)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## The Object Model Revisited

Git stores everything as **objects** in `.git/objects/`. Every object has a type and a 40-character SHA-1 hash as its name. The hash is computed from the object's type, size and content. The same content always produces the same hash. This is content-addressable storage.

The four object types:

**Blob** - stores file content. No filename, no permissions - just content. Two identical files in different directories share one blob.

**Tree** - stores a directory listing. Contains references to blobs (files) and other trees (subdirectories), along with names and permissions.

**Commit** - stores a snapshot. Points to a tree (the root directory state), one or more parent commits, author, committer, timestamps and the message.

**Tag** - stores an annotated tag. Points to a commit (or any other object), with tagger information and a message.

**Object storage location:**

Objects are stored at `.git/objects/XX/YYYYYY...` where `XX` is the first two characters of the hash and `YYYYYY...` is the remaining 38. For example, the object with hash `a1b2c3d4...` is at `.git/objects/a1/b2c3d4...`.

**View any object:**

```bash
git cat-file -t a1b2c3d    # show type
git cat-file -p a1b2c3d    # show contents
git cat-file -s a1b2c3d    # show size in bytes
```

---

## Packfiles

Initially, each Git object is stored as a separate compressed file (a **loose object**). A repository with many commits accumulates thousands of loose objects. This is inefficient for storage and network transfer.

Git periodically packs loose objects into a single **packfile** (`.pack`) accompanied by an **index file** (`.idx`). Packfiles use delta compression - instead of storing each version of a file in full, Git stores the differences between similar objects. This is how Git achieves dramatic compression ratios.

**View pack files:**

```bash
ls .git/objects/pack/
```

You will see `.pack` and `.idx` files.

**Inspect pack file contents:**

```bash
git verify-pack -v .git/objects/pack/pack-HASH.idx
```

This lists every object in the pack with its type, size, compressed size and delta depth.

**Manually trigger packing:**

```bash
git pack-objects --all
```

Or more commonly, let `git gc` handle it:

```bash
git gc
```

**Repacking for maximum compression:**

```bash
git repack -a -d --depth=250 --window=250
```

`--depth` is the maximum delta chain depth. `--window` is the number of objects to compare when finding deltas. Higher values produce smaller packs but take longer to compute.

---

## The Reflog in Depth

The **reflog** (reference log) records every movement of HEAD and every branch pointer change. It is Git's safety net - a local record of everything that has happened in the repository.

**View the reflog:**

```bash
git reflog
```

**View the reflog for a specific branch:**

```bash
git reflog show main
```

**View the reflog with timestamps:**

```bash
git reflog --date=iso
```

**Reflog entry format:**

```
a1b2c3d (HEAD -> main) HEAD@{0}: commit: add: new feature
e4f5g6h HEAD@{1}: reset: moving to HEAD~1
i7j8k9l HEAD@{2}: commit: wip: accidental commit
```

`HEAD@{0}` is the current position. `HEAD@{1}` is where HEAD was before the last operation. `HEAD@{2}` is before that, and so on.

**Time-based references:**

```bash
git reflog show HEAD@{2.days.ago}
git reflog show HEAD@{yesterday}
git reflog show HEAD@{1.hour.ago}
```

**Reflog expiry settings:**

```bash
git config --global gc.reflogExpire "90 days"
git config --global gc.reflogExpireUnreachable "30 days"
```

Reachable commits (those on a branch or tag) have their reflog entries kept for 90 days by default. Unreachable commits are kept for 30 days. After these periods, garbage collection may remove the entries and eventually the objects.

---

## Garbage Collection

Git's garbage collector (`git gc`) performs several maintenance tasks:

1. Packs loose objects into packfiles
2. Removes old packfiles that have been superseded
3. Expires old reflog entries beyond the configured retention period
4. Removes unreachable objects that have passed the reflog retention period
5. Updates the commit graph file
6. Runs `git prune` to remove loose unreachable objects

**Run garbage collection:**

```bash
git gc
```

**Aggressive garbage collection (slower but more thorough):**

```bash
git gc --aggressive
```

**Dry run - see what would be pruned without removing anything:**

```bash
git gc --dry-run
```

**Auto garbage collection:**

Git runs `git gc --auto` automatically after certain operations (fetch, merge, commit) when it detects that the repository has enough loose objects to benefit from packing. You can configure the thresholds:

```bash
git config gc.auto 256         # pack after 256 loose objects (default)
git config gc.autoPackLimit 50 # pack after 50 pack files (default)
```

Set to `0` to disable automatic gc:

```bash
git config gc.auto 0
```

---

## Reachability

An object is **reachable** if it can be found by following references from a named starting point - a branch, a tag or HEAD. Unreachable objects are those not reachable from any reference.

```
branches
  |
  v
commits -> trees -> blobs
  |
  v
parent commits -> trees -> blobs
  |
  v
...
```

Every blob and tree reachable from any commit on any branch or tag is reachable. Commits not on any branch (for example, after a `git reset --hard`) become unreachable - but they are kept in the reflog until the retention period expires.

This is why `git reset --hard` is recoverable: the commit still exists as an object, it is just unreachable from any branch. The reflog records where HEAD was, giving you the hash to reset back to.

After the reflog retention period expires and `git gc` runs, unreachable objects are permanently deleted.

**Find unreachable objects:**

```bash
git fsck --unreachable
```

This lists all unreachable objects. You can inspect them with `git cat-file -p HASH` to determine if any are worth recovering.

---

## The Index in Depth

The **index** (also called the staging area or cache) is a binary file at `.git/index`. It stores the state of the next commit - the list of files, their contents (as blob hashes) and their metadata (mode, timestamps, size).

The index has three conceptual slots per file:

- **Slot 1** - the base version (from the merge base, during a merge)
- **Slot 2** - the "ours" version (the current branch's version)
- **Slot 3** - the "theirs" version (the incoming branch's version)

During a normal (non-merge) operation, only slot 0 (the merged/normal state) is used. During a merge conflict, all three slots are populated for conflicted files. This is how Git tracks both versions of a conflict simultaneously.

**View the raw index contents:**

```bash
git ls-files --stage
```

Output:

```
100644 a1b2c3d4e5f6... 0       README.md
100644 e4f5g6h7i8j9... 0       src/main.js
```

The columns are: mode, blob hash, stage number, filename. Stage 0 means no conflict.

**During a conflict:**

```
100644 a1b2c3d4e5f6... 1       conflicted-file.txt  (base)
100644 e4f5g6h7i8j9... 2       conflicted-file.txt  (ours)
100644 i7j8k9l0m1n2... 3       conflicted-file.txt  (theirs)
```

---

## Maintenance and Optimisation

**View repository statistics:**

```bash
git count-objects -vH
```

Output:

```
count: 12
size: 48.00 KiB
in-pack: 3847
packs: 2
size-pack: 12.34 MiB
prune-packable: 0
garbage: 0
size-garbage: 0 bytes
```

**Run background maintenance (Git 2.29+):**

```bash
git maintenance start
```

This registers a system scheduler task (cron on Mac/Linux, Task Scheduler on Windows) to run maintenance operations in the background periodically. It performs incremental repack, commit graph updates and loose object packing without the user having to remember to run them.

**Configure maintenance schedule:**

```bash
git maintenance run --task=gc
git maintenance run --task=commit-graph
git maintenance run --task=prefetch
```

**Update the commit graph file:**

```bash
git commit-graph write --reachable
```

The commit graph file (`.git/objects/info/commit-graph`) caches commit metadata for faster `git log` and reachability computations. Git reads this cache instead of traversing all commits.

---

## Recovering Lost Commits

The reflog is your first stop when you lose a commit. But if the reflog does not have it, `git fsck` can find unreachable objects:

**Step 1.** Find unreachable commits:

```bash
git fsck --lost-found
```

This writes unreachable blobs and commits to `.git/lost-found/`. Commits go in `.git/lost-found/commit/`, blobs in `.git/lost-found/other/`.

**Step 2.** Inspect a recovered commit:

```bash
git cat-file -p HASH
```

**Step 3.** Recover by creating a branch pointing at the commit:

```bash
git switch -c recovery/lost-work HASH
```

**Recover a lost stash:**

If you accidentally cleared your stash with `git stash clear`, the stash commits are now unreachable but may still exist in the object database:

```bash
git fsck --unreachable | grep commit | cut -d' ' -f3 | xargs git log --merges --no-walk
```

This shows unreachable merge commits (stash entries are stored as merge commits internally). Find the one you want and apply it:

```bash
git stash apply HASH
```

---

## Inspecting the Object Database

**Show a commit's tree:**

```bash
git cat-file -p HEAD^{tree}
```

**Show the tree at a specific path:**

```bash
git ls-tree HEAD src/
```

**Show a file's contents at a specific commit:**

```bash
git show HEAD:src/main.js
```

**Find all commits that modified a specific file:**

```bash
git log --all --full-history -- src/main.js
```

**Find which commit introduced a specific line:**

```bash
git log -S "function validatePayment" --source --all
```

`-S` searches for commits that added or removed the specified string.

**Show the SHA-1 hash of a file:**

```bash
git hash-object src/main.js
```

**Compute what the hash would be without storing:**

```bash
git hash-object --stdin <<< "hello git"
```

---

## The Commit Graph

The commit graph is an optional but important optimisation file at `.git/objects/info/commit-graph`. It caches the commit data that Git needs for history traversal, making operations like `git log`, `git merge-base` and reachability checks significantly faster.

**Write the commit graph:**

```bash
git commit-graph write --reachable
```

**Update incrementally (faster for large repos):**

```bash
git commit-graph write --reachable --changed-paths --split
```

**Verify the commit graph is consistent:**

```bash
git commit-graph verify
```

Modern Git (2.29+) writes the commit graph automatically as part of `git maintenance` and `git fetch` if configured.

---

## Try It Yourself

**Step 1.** Create a repository and make several commits:

**Windows (PowerShell):**

```powershell
mkdir internals-practice && cd internals-practice && git init
for ($i=1; $i -le 5; $i++) { echo "commit $i" > file.txt; git add file.txt; git commit -m "update: commit $i" }
```

**Mac and Linux:**

```bash
mkdir internals-practice && cd internals-practice && git init
for i in 1 2 3 4 5; do echo "commit $i" > file.txt && git add file.txt && git commit -m "update: commit $i"; done
```

**Step 2.** Count objects:

```bash
git count-objects -vH
```

**Step 3.** Look at the raw objects:

```bash
find .git/objects -type f | head -10
```

**Step 4.** Inspect the latest commit object:

```bash
git cat-file -p HEAD
```

**Step 5.** Inspect the tree it points to:

```bash
git cat-file -p HEAD^{tree}
```

**Step 6.** Hard reset to lose a commit:

```bash
git reset --hard HEAD~2
git log --oneline
```

Two commits appear to be gone.

**Step 7.** Find them in the reflog:

```bash
git reflog
```

**Step 8.** Recover the lost commits:

```bash
git reset --hard HEAD@{1}
git log --oneline
```

All commits are back.

**Step 9.** Run garbage collection and inspect:

```bash
git gc
git count-objects -vH
```

**Step 10.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q internals-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf internals-practice
```

---

## Common Mistakes

**Running `git gc --aggressive` too frequently.**

`git gc --aggressive` is a very expensive operation. Running it routinely is unnecessary and counterproductive - Git's normal gc handles ongoing maintenance efficiently. Run `--aggressive` only when you are trying to dramatically reduce repository size (for example after removing large files from history).

**Assuming deleted branches are gone forever.**

Branches are just pointers. Deleting a branch does not delete any commits - it only removes the pointer. The commits remain in the object database and the reflog until the retention period expires. You can recover a deleted branch within 30-90 days using `git reflog` or `git fsck`.

**Not understanding the difference between unreachable and deleted.**

An object is unreachable when no branch, tag or reflog entry points to it. It is not immediately deleted - Git's gc removes it after the retention period. Understanding this is key to knowing when recovery is possible and when it is not.

**Manually editing files in `.git/objects/`.**

The object database uses a specific binary format. Editing files there manually almost always corrupts the repository. Use Git plumbing commands (`git cat-file`, `git hash-object`) to inspect and manipulate objects programmatically.

---

## Summary

- Git's object model uses content-addressable storage - objects are named by SHA-1 hashes of their content
- **Packfiles** store many objects in a single compressed file using delta compression for efficient storage and transfer
- The **reflog** records every HEAD movement and is your primary recovery tool for lost commits
- **Garbage collection** (`git gc`) packs loose objects, removes expired reflog entries and deletes unreachable objects
- An object is **reachable** if it can be found by following references from a branch, tag or HEAD
- **`git fsck`** finds unreachable objects that may be recoverable
- The **index** has three slots per file to track merge conflicts at the binary level
- The **commit graph** caches commit metadata for faster history traversal operations
- `git maintenance start` schedules background maintenance for large repositories

---

## Sources and Further Reading

- [Pro Git book, Chapter 10: Git Internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) - the definitive guide to Git's internal architecture
- [Official git cat-file documentation](https://git-scm.com/docs/git-cat-file) - the plumbing command for inspecting objects
- [Official git fsck documentation](https://git-scm.com/docs/git-fsck) - for finding and recovering unreachable objects
- [Official git gc documentation](https://git-scm.com/docs/git-gc) - garbage collection reference
- [Official git maintenance documentation](https://git-scm.com/docs/git-maintenance) - background maintenance operations
- [Git commit graph documentation](https://git-scm.com/docs/commit-graph) - technical details of the commit graph format

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

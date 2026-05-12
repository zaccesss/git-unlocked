# Large Repositories

**Difficulty:** 🔴 Advanced | **Time:** 25 minutes

Git works well for most repositories, but some projects push it to its limits - repositories with gigabytes of binary assets, millions of files or tens of thousands of commits. This file covers the tools Git provides for working efficiently with large repositories: Git LFS for large files, sparse checkout for working with subsets of large repositories and shallow and partial clones for reducing download size.

---

## Table of Contents

- [Why large repos are a problem](#why-large-repos-are-a-problem)
- [Git LFS](#git-lfs)
- [Installing Git LFS](#installing-git-lfs)
- [Using Git LFS](#using-git-lfs)
- [Migrating existing files to LFS](#migrating-existing-files-to-lfs)
- [Sparse checkout](#sparse-checkout)
- [Shallow clones](#shallow-clones)
- [Partial clones](#partial-clones)
- [Optimising a large repository](#optimising-a-large-repository)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Why Large Repos Are a Problem

Git is designed for text files - source code, configuration, documentation. It stores every version of every file in its object database and delta-compresses text efficiently. A 1MB source file that changes slightly between commits might only add a few kilobytes to the repository.

Binary files break this model. A 50MB texture file that changes slightly between commits adds 50MB to the repository for each version. A game project with thousands of binary assets can grow to hundreds of gigabytes over time. Operations like `git clone`, `git fetch` and `git checkout` become progressively slower as the repository grows.

The tools covered in this file address different aspects of this problem:

- **Git LFS** moves large files out of the main Git object database into a separate storage system
- **Sparse checkout** lets you check out only the files you need from a large repository
- **Shallow clones** download only recent history, not the entire commit history
- **Partial clones** download blobs (file content) on demand rather than all at once

---

## Git LFS

**Git Large File Storage (LFS)** replaces large files in your repository with small text pointers. The actual file content is stored on a separate LFS server. When you clone or checkout, Git downloads the pointer files. The actual large files are downloaded only when you access them.

```
Normal Git:          Git LFS:
repository           repository
├── code.js          ├── code.js
├── image.png        ├── image.png (pointer: sha256:abc123...)
└── video.mp4        └── video.mp4 (pointer: sha256:def456...)
    (50MB stored          (100 bytes stored in git,
     in git history)       50MB in LFS server)
```

GitHub, GitLab and Bitbucket all support Git LFS. GitHub provides 1GB of free LFS storage per account. GitLab provides 5GB.

---

## Installing Git LFS

**Windows:**

Download the installer from [git-lfs.com](https://git-lfs.com) or use winget:

```powershell
winget install GitHub.GitLFS
```

**Mac:**

```bash
brew install git-lfs
```

**Linux (Ubuntu/Debian):**

```bash
sudo apt install git-lfs
```

**After installing, run the one-time setup:**

```bash
git lfs install
```

This adds Git LFS hooks to your global Git configuration. You only need to do this once per machine.

---

## Using Git LFS

**Track a file type with LFS:**

```bash
git lfs track "*.psd"
git lfs track "*.mp4"
git lfs track "*.zip"
```

This creates or updates a `.gitattributes` file:

```
*.psd filter=lfs diff=lfs merge=lfs -text
*.mp4 filter=lfs diff=lfs merge=lfs -text
*.zip filter=lfs diff=lfs merge=lfs -text
```

**Commit the `.gitattributes` file:**

```bash
git add .gitattributes
git commit -m "add: configure Git LFS for binary assets"
```

**Add and commit files normally - LFS handles the rest:**

```bash
git add design.psd
git commit -m "add: hero image design"
git push
```

Git automatically stores the file in LFS and commits a pointer in its place.

**Check which files are tracked by LFS:**

```bash
git lfs ls-files
```

**Check LFS status:**

```bash
git lfs status
```

**View LFS environment and configuration:**

```bash
git lfs env
```

**Clone a repository with LFS files:**

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

LFS files download automatically when you clone. If you want to skip downloading LFS files initially:

```bash
GIT_LFS_SKIP_SMUDGE=1 git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

Then download specific files when needed:

```bash
git lfs pull --include="assets/hero.psd"
```

---

## Migrating Existing Files to LFS

If you have an existing repository with large files already committed in normal Git history, you need to rewrite the history to move them to LFS.

> [!WARNING]
> Migrating files to LFS rewrites Git history. All commit hashes change. Everyone who has cloned the repository will need to re-clone. Coordinate with your team before doing this.

**Migrate all files of a type to LFS (rewrites history):**

```bash
git lfs migrate import --include="*.psd" --everything
```

**Migrate specific files:**

```bash
git lfs migrate import --include="assets/large-file.zip" --everything
```

**After migration, force push all branches:**

```bash
git push origin --force --all
git push origin --force --tags
```

---

## Sparse Checkout

Sparse checkout lets you check out only a subset of a repository's files. Instead of downloading all files from a large monorepo, you check out only the directories you need.

This is particularly useful in monorepos where different teams work on different parts of the codebase and do not need the full tree.

**Enable sparse checkout on an existing clone:**

```bash
git sparse-checkout init
```

**Set the directories to check out:**

```bash
git sparse-checkout set src/frontend docs
```

Only `src/frontend/` and `docs/` are present in your working directory. All other directories are hidden (but still in the repository).

**Add more directories:**

```bash
git sparse-checkout add src/shared
```

**View currently checked-out patterns:**

```bash
git sparse-checkout list
```

**Disable sparse checkout (check out everything):**

```bash
git sparse-checkout disable
```

**Clone with sparse checkout from the start:**

```bash
git clone --no-checkout https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
git sparse-checkout init --cone
git sparse-checkout set src/frontend
git checkout main
```

The `--cone` mode is a simpler, faster variant of sparse checkout that works with directory paths rather than full patterns.

---

## Shallow Clones

A shallow clone downloads only the most recent N commits of history rather than the entire history. Useful when you only need the latest code and do not need to explore old history.

**Clone with only the last 1 commit:**

```bash
git clone --depth 1 https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

**Clone with the last N commits:**

```bash
git clone --depth 50 https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

**Clone a specific branch with limited depth:**

```bash
git clone --depth 1 --branch main https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

**Deepen a shallow clone after the fact:**

```bash
git fetch --deepen 100
```

**Convert a shallow clone to a full clone:**

```bash
git fetch --unshallow
```

> [!NOTE]
> Shallow clones have limitations. Some Git operations behave differently with incomplete history - `git bisect` needs full history, `git log --all` shows less information and merging from shallow history can cause issues. For CI/CD pipelines where you only need the latest code, shallow clones are ideal. For development work, a full clone is usually better.

---

## Partial Clones

Partial clones (introduced in Git 2.17, improved through subsequent versions) allow cloning without downloading all blob objects. Blobs (file contents) are fetched on demand when you actually need them.

**Clone without downloading any blob objects:**

```bash
git clone --filter=blob:none https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

Git downloads all commits and trees (directory structures) but no file contents. File contents are fetched lazily when you access them.

**Clone without downloading large blob objects (above a threshold):**

```bash
git clone --filter=blob:limit=1m https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

Files over 1MB are fetched on demand; files under 1MB are downloaded at clone time.

**Clone without downloading tree objects:**

```bash
git clone --filter=tree:0 https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

This is the most aggressive filtering - only the commits themselves are downloaded. Extremely fast initial clone but slower for many subsequent operations.

> [!NOTE]
> Partial clones require the server to support them. GitHub and GitLab both support partial clones. Self-hosted Git servers may not.

---

## Optimising a Large Repository

**Run garbage collection:**

```bash
git gc
```

Git's garbage collector removes unreachable objects, packs loose objects and optimises the repository's storage. Run this periodically on large repositories or after deleting many branches.

**Aggressive garbage collection:**

```bash
git gc --aggressive
```

Takes longer but produces a more compact repository. Run this infrequently.

**View repository size:**

```bash
git count-objects -vH
```

This shows the number of objects and their total disk usage.

**Find the largest files in history:**

```bash
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | sed -n 's/^blob //p' | sort -k2 -rn | head -20
```

This lists the 20 largest blobs in your repository's history by size. Useful for identifying candidates for LFS migration.

**Repack the repository:**

```bash
git repack -Ad
```

This repacks all objects into a single optimised pack file and removes loose objects.

---

## Try It Yourself

**Step 1.** Install Git LFS (if not already installed):

**Mac:**

```bash
brew install git-lfs && git lfs install
```

**Windows:**

```powershell
winget install GitHub.GitLFS
git lfs install
```

**Linux:**

```bash
sudo apt install git-lfs && git lfs install
```

**Step 2.** Create a repository and configure LFS:

**Windows (PowerShell):**

```powershell
mkdir lfs-practice && cd lfs-practice && git init
git lfs track "*.bin"
git add .gitattributes && git commit -m "add: configure Git LFS for .bin files"
```

**Mac and Linux:**

```bash
mkdir lfs-practice && cd lfs-practice && git init
git lfs track "*.bin"
git add .gitattributes && git commit -m "add: configure Git LFS for .bin files"
```

**Step 3.** Create a fake binary file and commit it:

**Windows:**

```powershell
[System.IO.File]::WriteAllBytes("data.bin", (New-Object byte[] 1024))
git add data.bin && git commit -m "add: binary data file"
```

**Mac and Linux:**

```bash
dd if=/dev/urandom of=data.bin bs=1024 count=1 2>/dev/null
git add data.bin && git commit -m "add: binary data file"
```

**Step 4.** Check LFS status:

```bash
git lfs ls-files
git lfs status
```

**Step 5.** Verify the pointer was stored in Git, not the file content:

```bash
git show HEAD:data.bin
```

You should see a small text pointer, not binary data.

**Step 6.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q lfs-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf lfs-practice
```

---

## Common Mistakes

**Not running `git lfs install` after installing Git LFS.**

The `git lfs install` command adds the necessary hooks to your global Git config. Without it, LFS does not intercept file operations. Always run it after installing LFS on a new machine.

**Not committing `.gitattributes` before adding LFS files.**

The `.gitattributes` file tells Git which files to treat as LFS. If you add a large file before committing `.gitattributes`, the file goes into normal Git storage. Commit `.gitattributes` first.

**Using shallow clones for development work.**

Shallow clones are ideal for CI/CD pipelines that just need to build the latest code. For development work where you need to bisect, explore history or create branches from old commits, use a full clone.

**Migrating to LFS without coordinating with the team.**

LFS migration rewrites history. Everyone who has cloned the repository needs to re-clone after migration. Not re-cloning causes confusing diverged history. Announce migrations and coordinate timing carefully.

**Expecting LFS files to be available in shallow clones without extra configuration.**

LFS files in shallow clones may not download automatically depending on the server configuration. Use `git lfs pull` explicitly if LFS files are missing.

---

## Summary

- **Git LFS** replaces large binary files with small pointer files and stores actual content separately - use it for images, videos, archives and other large binaries
- Track file types with `git lfs track "*.psd"` then commit `.gitattributes`
- **Sparse checkout** (`git sparse-checkout set path/`) lets you work with only a subset of a large repository's files
- **Shallow clones** (`git clone --depth 1`) download only recent history - ideal for CI/CD
- **Partial clones** (`git clone --filter=blob:none`) fetch file contents on demand - good for large repositories where you only access a subset of files
- `git gc` and `git repack` optimise repository storage
- Find large files in history with `git rev-list --objects --all` combined with `git cat-file`

---

## Sources and Further Reading

- [Git LFS website](https://git-lfs.com) - official documentation, installation and tutorials
- [GitHub - Git LFS](https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-git-large-file-storage) - GitHub's guide to LFS
- [Official git sparse-checkout documentation](https://git-scm.com/docs/git-sparse-checkout) - the complete reference
- [GitHub - Sparse checkout](https://github.blog/open-source/git/bring-your-monorepo-down-to-size-with-sparse-checkout/) - GitHub's guide to sparse checkout in monorepos
- [Git partial clone](https://git-scm.com/docs/partial-clone) - official documentation for partial clones
- [GitLab - Reducing repository size](https://docs.gitlab.com/user/project/repository/repository_size/) - GitLab's guide to managing large repositories

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

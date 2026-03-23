# Tags and Releases

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

Tags mark specific points in your repository's history as significant - typically version releases. Unlike branches, tags do not move. Once you tag a commit as `v2.0.0`, that tag stays on that commit forever. This file covers creating, managing and pushing tags, as well as creating releases on GitHub and GitLab.

---

## Table of Contents

- [What is a tag?](#what-is-a-tag)
- [Lightweight vs annotated tags](#lightweight-vs-annotated-tags)
- [Creating tags](#creating-tags)
- [Listing tags](#listing-tags)
- [Viewing tag information](#viewing-tag-information)
- [Tagging a past commit](#tagging-a-past-commit)
- [Pushing tags to a remote](#pushing-tags-to-a-remote)
- [Deleting tags](#deleting-tags)
- [Checking out a tag](#checking-out-a-tag)
- [Semantic versioning](#semantic-versioning)
- [Creating releases on GitHub](#creating-releases-on-github)
- [Creating releases on GitLab](#creating-releases-on-gitlab)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Tag?

A tag is a named, permanent reference to a specific commit. Where a branch pointer moves forward with every new commit, a tag is fixed. It always points to the same commit.

Tags are used to mark release versions. When you ship version 2.0.0 of your software, you tag that commit `v2.0.0`. Months later, after hundreds more commits, you can still check out exactly what the codebase looked like at that release.

---

## Lightweight vs Annotated Tags

Git has two types of tags.

**Lightweight tags** are just a name pointing to a commit. They store nothing beyond the commit reference - no tagger name, no date, no message. They are like a branch that never moves.

```bash
git tag v2.0.0
```

**Annotated tags** are full objects in Git's database. They store the tagger's name, email and date, a tagging message and optionally a GPG signature. They are the recommended type for releases.

```bash
git tag -a v2.0.0 -m "Release version 2.0.0"
```

**Use annotated tags for releases.** They contain more information, can be signed for verification and are treated as proper release objects by GitHub and GitLab. Use lightweight tags only for temporary or personal markers.

---

## Creating Tags

**Create an annotated tag on the current commit:**

```bash
git tag -a v2.0.0 -m "Release version 2.0.0"
```

**Create an annotated tag and open your editor for the message:**

```bash
git tag -a v2.0.0
```

Git opens your configured editor. Write the tag message, save and close.

**Create a lightweight tag:**

```bash
git tag v2.0.0
```

**Create a signed tag (requires GPG set up):**

```bash
git tag -s v2.0.0 -m "Release version 2.0.0"
```

Signed tags are covered in [git/20-signing-commits.md](20-signing-commits.md).

---

## Listing Tags

**List all tags:**

```bash
git tag
```

**List tags matching a pattern:**

```bash
git tag -l "v2.*"
```

This lists all tags starting with `v2.`.

**List tags with their messages (annotated tags only):**

```bash
git tag -n
```

**List tags sorted by version:**

```bash
git tag -l --sort=-version:refname
```

The `-` before `version:refname` sorts in descending order (newest first).

---

## Viewing Tag Information

**Show the tag object and the tagged commit:**

```bash
git show v2.0.0
```

For an annotated tag this shows the tagger name, date, message and then the commit details. For a lightweight tag it shows only the commit.

---

## Tagging a Past Commit

You can tag any commit in history, not just the current one. Find the hash with `git log --oneline` and specify it when creating the tag:

```bash
git log --oneline
git tag -a v1.9.0 a1b2c3d -m "Release version 1.9.0"
```

---

## Pushing Tags to a Remote

By default, `git push` does not push tags. You must push them explicitly.

**Push a specific tag:**

```bash
git push origin v2.0.0
```

**Push all tags:**

```bash
git push origin --tags
```

**Push all annotated tags only (not lightweight):**

```bash
git push origin --follow-tags
```

This is the recommended option. It pushes the current branch's commits and any annotated tags reachable from those commits, but not lightweight tags.

> [!TIP]
> Make `--follow-tags` the default so tags are always pushed with commits:
>
> ```bash
> git config --global push.followTags true
> ```

---

## Deleting Tags

**Delete a local tag:**

```bash
git tag -d v2.0.0
```

**Delete a remote tag:**

```bash
git push origin --delete v2.0.0
```

Or the older syntax:

```bash
git push origin :refs/tags/v2.0.0
```

> [!WARNING]
> Deleting a tag that has already been pulled by others will cause their local copies to still reference the tag. Coordinate with your team before deleting published tags. Deleting and recreating a release tag is a significant disruption.

---

## Checking out a Tag

You can check out a tag to inspect the repository at that point in history:

```bash
git checkout v2.0.0
```

This puts you in **detached HEAD state** - you are not on any branch. You can look around and run the code, but any commits you make will not be attached to a branch and may be lost.

If you want to make changes based on a tag (for example, to backport a fix to an old release), create a branch from it:

```bash
git switch -c hotfix/v2.0.1 v2.0.0
```

---

## Semantic Versioning

The overwhelming standard for version numbers in software is **Semantic Versioning** (SemVer): `MAJOR.MINOR.PATCH`.

| Part | When to increment | Example |
|---|---|---|
| `MAJOR` | Breaking changes - existing code may stop working | `1.0.0` to `2.0.0` |
| `MINOR` | New features added in a backwards-compatible way | `2.0.0` to `2.1.0` |
| `PATCH` | Backwards-compatible bug fixes | `2.1.0` to `2.1.1` |

**Pre-release versions** use a hyphen suffix: `2.0.0-alpha`, `2.0.0-beta.1`, `2.0.0-rc.1`.

**Build metadata** uses a plus suffix: `2.0.0+build.123`.

**Tag naming convention:**

Tags conventionally start with `v`: `v2.0.0`, `v2.1.0`, `v2.1.1`. The `v` prefix is not part of the version number itself - it is just a tag naming convention that helps Git distinguish version tags from other tags.

---

## Creating Releases on GitHub

A GitHub Release is a deployable package built from a Git tag. It gives users a clear download point with release notes, binary attachments and a changelog.

**Creating a release:**

1. Go to your repository on GitHub
2. Click **Releases** in the right sidebar (or go to `github.com/YOUR_USERNAME/YOUR_REPO/releases`)
3. Click **Create a new release** or **Draft a new release**
4. In the **Choose a tag** dropdown, either select an existing tag or type a new tag name (e.g. `v2.0.0`) and click **Create new tag**
5. Set the target branch (usually `main`)
6. Write a **release title** (e.g. `Version 2.0.0`)
7. Write **release notes** describing what is new, what is fixed and what has changed. GitHub can auto-generate these from pull request titles and commit messages
8. Optionally attach binary files - compiled executables, installers, zip archives
9. Check **Set as pre-release** if this is an alpha, beta or release candidate
10. Click **Publish release**

**Using GitHub's auto-generated release notes:**

Click **Generate release notes** and GitHub creates a changelog from merged pull request titles since the last release. The quality of these notes depends on how well your pull request titles are written.

**Releases via GitHub CLI:**

```bash
gh release create v2.0.0 --title "Version 2.0.0" --notes "Release notes here"
```

With a file as release notes:

```bash
gh release create v2.0.0 --title "Version 2.0.0" --notes-file CHANGELOG.md
```

---

## Creating Releases on GitLab

GitLab releases work similarly to GitHub releases.

**Creating a release:**

1. Go to your project on GitLab
2. Go to **Deploy -> Releases**
3. Click **Create a new release**
4. Select or create a tag
5. Fill in the release title and release notes
6. Add any links to binaries or documentation
7. Click **Create release**

**Releases via GitLab CLI or API:**

```bash
glab release create v2.0.0 --name "Version 2.0.0" --notes "Release notes"
```

---

## Try It Yourself

**Step 1.** Create a repository:

**Windows (PowerShell):**

```powershell
mkdir tags-practice && cd tags-practice && git init
echo "# My Project v1" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir tags-practice && cd tags-practice && git init
echo "# My Project v1" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Add a few more commits:

**Windows:**

```powershell
echo "Feature A" > feature-a.md
git add feature-a.md && git commit -m "add: feature A"
echo "Feature B" > feature-b.md
git add feature-b.md && git commit -m "add: feature B"
```

**Mac and Linux:**

```bash
echo "Feature A" > feature-a.md && git add feature-a.md && git commit -m "add: feature A"
echo "Feature B" > feature-b.md && git add feature-b.md && git commit -m "add: feature B"
```

**Step 3.** Tag the current state as v1.0.0:

```bash
git tag -a v1.0.0 -m "Release version 1.0.0 - initial stable release"
```

**Step 4.** Add more commits and tag again:

**Windows:**

```powershell
echo "Bug fix" > bugfix.md
git add bugfix.md && git commit -m "fix: critical bug"
```

**Mac and Linux:**

```bash
echo "Bug fix" > bugfix.md && git add bugfix.md && git commit -m "fix: critical bug"
```

```bash
git tag -a v1.0.1 -m "Release version 1.0.1 - bug fix"
```

**Step 5.** List all tags:

```bash
git tag -n
```

**Step 6.** View the v1.0.0 tag details:

```bash
git show v1.0.0
```

**Step 7.** Check out the v1.0.0 tag and see what the project looked like:

```bash
git checkout v1.0.0
ls
```

Only `README.md`, `feature-a.md` and `feature-b.md` are present. `bugfix.md` does not exist at this point in history.

**Step 8.** Return to main:

```bash
git switch main
```

**Step 9.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q tags-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf tags-practice
```

---

## Common Mistakes

**Not pushing tags after creating them.**

`git push` does not push tags. After creating a tag, explicitly push it with `git push origin v2.0.0` or `git push --follow-tags`. Many developers create tags locally and wonder why they do not appear on GitHub.

**Using lightweight tags for releases.**

Lightweight tags have no metadata. Use annotated tags (`-a`) for releases so there is a record of who tagged, when and why.

**Tagging the wrong commit.**

Check `git log --oneline` to confirm HEAD is at the correct commit before tagging. If you tag the wrong commit, delete the tag and recreate it on the correct one.

**Not following semantic versioning.**

Version numbers like `1.0`, `2`, `final` or `release-jan` are confusing and non-standard. Use `MAJOR.MINOR.PATCH` consistently. Prefix with `v` for the tag name: `v2.1.0`.

**Deleting and recreating published tags.**

If you published a tag and need to change what it points to, deleting and recreating causes confusion for anyone who has already pulled it. Communicate clearly with your team and consider creating a new tag instead.

---

## Summary

- A tag is a permanent named reference to a specific commit, used to mark releases
- **Annotated tags** (`-a`) store tagger metadata and messages - use these for releases
- **Lightweight tags** are simple name-to-commit pointers - use for temporary markers
- `git tag -a v2.0.0 -m "message"` creates an annotated tag on the current commit
- `git push origin --follow-tags` pushes commits and annotated tags together
- `git tag -d v2.0.0` deletes a local tag; `git push origin --delete v2.0.0` deletes a remote tag
- `git checkout v2.0.0` puts you in detached HEAD state at that tag
- Follow **semantic versioning**: `MAJOR.MINOR.PATCH` with a `v` prefix for tag names
- GitHub and GitLab releases are built on top of tags and allow attaching binaries and release notes

---

## Sources and Further Reading

- [Official git tag documentation](https://git-scm.com/docs/git-tag) - the complete reference for all tag options
- [Pro Git book, Chapter 2.6: Tagging](https://git-scm.com/book/en/v2/Git-Basics-Tagging) - the official guide to Git tags
- [Semantic Versioning specification](https://semver.org) - the full SemVer 2.0.0 specification
- [GitHub - Managing releases](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository) - GitHub's guide to creating and managing releases
- [GitLab - Releases](https://docs.gitlab.com/ee/user/project/releases/) - GitLab's guide to releases

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

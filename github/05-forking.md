# Forking

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Forking is how open source contribution works on GitHub. When you fork a repository, you get your own copy of it under your account where you can make changes freely, without affecting the original. When your changes are ready, you propose them back to the original project via a pull request. Understanding forking - and the relationship between a fork, its upstream and the pull request workflow - is essential for contributing to any open source project.

---

## Table of Contents

- [What is a fork?](#what-is-a-fork)
- [Fork vs clone](#fork-vs-clone)
- [When to fork](#when-to-fork)
- [How to fork a repository](#how-to-fork-a-repository)
- [The relationship between fork and upstream](#the-relationship-between-fork-and-upstream)
- [Keeping a fork up to date](#keeping-a-fork-up-to-date)
- [Contributing back via pull request](#contributing-back-via-pull-request)
- [Forking within an organisation](#forking-within-an-organisation)
- [Detached forks](#detached-forks)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Fork?

A fork is a **server-side copy** of a repository stored under your own GitHub account. It is linked to the original repository (called the **upstream**) and retains that connection - you can pull new changes from the upstream into your fork and propose your changes back to the upstream via a pull request.

Forking is a GitHub concept, not a Git concept. Underneath, a fork is just a Git repository that shares objects with the original (making the initial fork very fast - GitHub does not copy all the data, it shares it). The fork relationship, the ability to sync and the pull request workflow are GitHub features built on top of that.

**A fork gives you:**

- Your own copy of the entire repository including all history, branches and tags
- Full write access to make any changes you want
- The ability to propose changes back to the original via pull requests
- Independence - your fork does not affect the original project

---

## Fork vs Clone

This is one of the most common sources of confusion for new GitHub users.

| Feature | Fork | Clone |
|---|---|---|
| **Where it lives** | Server-side copy on GitHub under your account | Local copy on your machine |
| **Purpose** | Contribute to a project you do not have write access to | Work on a project locally |
| **Connection** | Linked to upstream; can sync and open PRs | Connected to `origin` (wherever you cloned from) |
| **Visibility** | Visible on your GitHub profile | Only on your machine |
| **GitHub relationship** | GitHub tracks the fork-upstream connection | No GitHub-level relationship |
| **When to use** | Contributing to someone else's open source project | Working on a project you have access to |

**The typical workflow combines both:**

1. Fork the repository on GitHub (server-side copy under your account)
2. Clone your fork to your local machine (local copy to work with)
3. Make changes locally
4. Push to your fork on GitHub
5. Open a pull request from your fork to the upstream

---

## When to Fork

**Fork when:**

- You want to contribute to an open source project but do not have write access
- You want to experiment with a project's code without risk of affecting it
- You want to build something new based on an existing project
- You want to maintain your own modified version of a project independently

**Do not fork when:**

- You have write access to the repository - just clone it and create a branch
- You just want to use the project - clone it without forking
- You want to create a new project inspired by another - use a template repository or just create a new repo

---

## How to Fork a Repository

**From the GitHub web interface:**

1. Navigate to the repository you want to fork
2. Click the **Fork** button in the top-right corner (next to Star and Watch)
3. GitHub opens a form:
   - **Owner:** Choose your account or an organisation you belong to
   - **Repository name:** Defaults to the upstream repository name. Change it if needed.
   - **Description:** Optional. Defaults to the upstream's description.
   - **Copy the DEFAULT branch only:** Checked by default. Uncheck to copy all branches.
4. Click **Create fork**

GitHub creates the fork in seconds and redirects you to your new forked repository.

**From the GitHub CLI:**

```bash
gh repo fork OWNER/REPO
```

This forks the repository to your account. Add `--clone` to also clone it locally:

```bash
gh repo fork OWNER/REPO --clone
```

The CLI automatically sets up the `upstream` remote pointing to the original repository.

**Fork and clone manually:**

```bash
# After forking on GitHub, clone your fork
git clone https://github.com/YOUR_USERNAME/REPO_NAME.git
cd REPO_NAME

# Add the upstream remote
git remote add upstream https://github.com/ORIGINAL_OWNER/REPO_NAME.git

# Verify both remotes
git remote -v
```

Output:

```
origin    https://github.com/YOUR_USERNAME/REPO_NAME.git (fetch)
origin    https://github.com/YOUR_USERNAME/REPO_NAME.git (push)
upstream  https://github.com/ORIGINAL_OWNER/REPO_NAME.git (fetch)
upstream  https://github.com/ORIGINAL_OWNER/REPO_NAME.git (push)
```

---

## The Relationship Between Fork and Upstream

**Origin** is your fork - the copy on your GitHub account. You have full write access.

**Upstream** is the original repository you forked from. You do not have write access (unless you are a collaborator).

```
upstream (original)
github.com/ORIGINAL_OWNER/REPO

         ^
         |  pull requests
         |  sync from upstream

origin (your fork)
github.com/YOUR_USERNAME/REPO

         ^
         |  push
         |

local clone
your machine
```

Changes flow:
- **upstream → your fork:** You sync/pull to keep your fork current
- **your fork → upstream:** Via pull request to propose your changes
- **your fork → local:** `git clone` and `git pull`
- **local → your fork:** `git push`

---

## Keeping a Fork Up to Date

The upstream repository continues to receive commits from other contributors after you fork it. Your fork does not automatically stay in sync. You need to periodically update it.

### Sync via GitHub web interface

GitHub shows a **"Sync fork"** button on your fork's homepage when the upstream has new commits.

1. Go to your fork on GitHub
2. Click **Sync fork** (appears when your fork is behind upstream)
3. Click **Update branch** to bring your fork's default branch up to date with upstream

This is the fastest approach for simple cases.

### Sync via GitHub CLI

```bash
gh repo sync YOUR_USERNAME/REPO_NAME
```

Or if you are inside the cloned repository:

```bash
gh repo sync
```

### Sync via Git locally

```bash
# Fetch new commits from upstream (does not change your local files)
git fetch upstream

# Switch to your main branch
git switch main

# Merge upstream changes into your local main
git merge upstream/main

# Push the updated main to your fork on GitHub
git push origin main
```

**Rebase approach (cleaner linear history):**

```bash
git fetch upstream
git switch main
git rebase upstream/main
git push origin main --force-with-lease
```

> [!TIP]
> Keep your fork's default branch (`main`) clean and always in sync with upstream. Do your work on feature branches, not on `main`. This makes syncing much easier and avoids messy conflicts.

---

## Contributing Back via Pull Request

Once you have made changes on a branch in your fork, you can propose them to the upstream repository via a pull request.

**Step 1.** Create a branch in your fork for your changes:

```bash
git switch -c fix/typo-in-readme
```

**Step 2.** Make your changes, commit and push to your fork:

```bash
git add .
git commit -m "fix: correct typo in README installation section"
git push origin fix/typo-in-readme
```

**Step 3.** GitHub automatically shows a banner at the top of your fork: **"Compare & pull request"**. Click it.

Or go to the **Pull requests** tab on the upstream repository and click **New pull request** → **Compare across forks**.

**Step 4.** Confirm the direction:

- **Base repository:** `ORIGINAL_OWNER/REPO` (upstream, where you want your change to go)
- **Base:** `main` (which branch in upstream to merge into)
- **Head repository:** `YOUR_USERNAME/REPO` (your fork)
- **Compare:** `fix/typo-in-readme` (your branch)

**Step 5.** Write a clear title and description. Explain what you changed, why and how to test it.

**Step 6.** Click **Create pull request**.

The maintainer of the upstream repository will review your pull request, request changes if needed and merge it when it is ready.

Pull requests from forks have **no access to upstream repository secrets**. This is a deliberate security measure to prevent malicious pull requests from reading sensitive tokens.

---

## Forking Within an Organisation

Members of a GitHub organisation can fork repositories within that organisation. Organisation settings control where forks can go:

- **Allow forking to any destination** (public repos default)
- **Restrict forking to within the organisation** (common in enterprise)
- **Disable forking entirely** (maximum restriction)

When a private repository is deleted in an organisation, all its private forks within the organisation are also deleted.

---

## Detached Forks

When you fork a public repository and the original is later made private or deleted, your fork becomes **detached**. A detached fork:

- Loses its connection to the upstream
- Can no longer sync from or pull request to the original
- Becomes a fully independent repository under your account
- Retains all the code and history from the point of forking

This is useful behaviour - it means your fork survives even if the original project disappears.

Similarly, if you fork a public repository that later becomes private, your fork remains public and retains all code that was public at the time of forking. You cannot fork a private repository unless you have explicit access to it.

---

## Try It Yourself

This exercise forks a real public repository, clones it, makes a change and prepares a pull request.

**Step 1.** Fork the `git-unlocked` course repository:

1. Go to `github.com/zaccessss/git-unlocked`
2. Click **Fork** in the top-right corner
3. Leave the defaults and click **Create fork**

**Step 2.** Clone your fork:

**Windows (PowerShell):**

```powershell
gh repo clone YOUR_USERNAME/git-unlocked
cd git-unlocked
```

**Mac and Linux:**

```bash
gh repo clone YOUR_USERNAME/git-unlocked
cd git-unlocked
```

Or without the CLI:

```bash
git clone https://github.com/YOUR_USERNAME/git-unlocked.git
cd git-unlocked
```

**Step 3.** Add the upstream remote:

```bash
git remote add upstream https://github.com/zaccessss/git-unlocked.git
git remote -v
```

You should see both `origin` (your fork) and `upstream` (the original).

**Step 4.** Create a branch for your change:

```bash
git switch -c improvement/YOUR_USERNAME-practice
```

**Step 5.** Make a small change - add your name to the HALL_OF_FAME.md if contributing is something you want to do, or simply note what you practised.

**Step 6.** Commit and push to your fork:

```bash
git add .
git commit -m "add: practice contribution from YOUR_USERNAME"
git push origin improvement/YOUR_USERNAME-practice
```

**Step 7.** Check the sync status of your fork:

```bash
git fetch upstream
git log --oneline HEAD..upstream/main
```

If there are commits listed, your fork is behind upstream. Sync it:

```bash
git switch main
git merge upstream/main
git push origin main
```

**Step 8.** Delete your practice branch when finished:

```bash
git switch main
git branch -d improvement/YOUR_USERNAME-practice
git push origin --delete improvement/YOUR_USERNAME-practice
```

---

## Common Mistakes

**Cloning the upstream instead of your fork.**

After forking, always clone your fork (`github.com/YOUR_USERNAME/REPO`), not the upstream (`github.com/ORIGINAL_OWNER/REPO`). If you clone upstream, you will not be able to push your changes.

**Working directly on the main branch of your fork.**

Keep `main` clean and in sync with upstream. Create a new branch for every change. If you work on `main` and then try to sync with upstream, you will have conflicts that are difficult to resolve.

**Not adding the upstream remote.**

If you cloned your fork manually, you need to add the upstream remote to be able to sync. `gh repo fork --clone` does this automatically. Without upstream, you have no way to pull new changes from the original project.

**Not syncing before starting work.**

If your fork is behind upstream and you start making changes, your pull request will include outdated code and may have conflicts with newer work. Always sync your fork before starting a new contribution.

**Opening a pull request from `main`.**

If you open a pull request from your fork's `main` branch to upstream's `main`, and the PR is rejected or you want to make changes, you have no clean branch to work from. Always use a dedicated feature branch for each contribution.

**Assuming fork gives you upstream write access.**

Forking does not give you write access to the upstream repository. You can only propose changes via pull requests. The upstream maintainer decides whether to accept them.

---

## Summary

- A fork is a server-side copy of a repository under your own GitHub account, linked to the original (upstream)
- Fork to contribute to projects you do not have write access to; clone to work on projects you do have access to
- After forking, clone your fork locally and add the upstream remote: `git remote add upstream <url>`
- `origin` = your fork; `upstream` = the original repository
- Keep your fork's `main` branch in sync with upstream using the **Sync fork** button, `gh repo sync` or `git fetch upstream && git merge`
- Contribute back by creating a branch, committing your changes and opening a pull request from your fork to the upstream
- Pull requests from forks cannot access upstream repository secrets
- Detached forks (when upstream goes private or is deleted) become independent repositories
- Always work on a dedicated branch, not on `main`, in your fork

---

## Sources and Further Reading

- [Fork a repository - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) - official guide to forking including CLI commands
- [Syncing a fork - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork) - all methods for keeping a fork up to date
- [Creating a pull request from a fork - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork) - step-by-step PR from fork guide
- [About forks - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks) - concept reference including detached forks

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

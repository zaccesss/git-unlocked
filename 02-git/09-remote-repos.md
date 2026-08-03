# Remote Repositories

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

A remote repository is a version of your project hosted on a server - GitHub, GitLab, Bitbucket or anywhere else. Understanding how to work with remotes is what turns Git from a personal backup tool into a collaboration platform. This file covers everything about adding, managing and working with remote repositories.

---

## Table of Contents

- [What is a remote?](#what-is-a-remote)
- [origin and upstream](#origin-and-upstream)
- [Viewing remotes](#viewing-remotes)
- [Adding a remote](#adding-a-remote)
- [Removing and renaming remotes](#removing-and-renaming-remotes)
- [Fetching from a remote](#fetching-from-a-remote)
- [Pulling from a remote](#pulling-from-a-remote)
- [Pushing to a remote](#pushing-to-a-remote)
- [Tracking branches](#tracking-branches)
- [Working with multiple remotes](#working-with-multiple-remotes)
- [The fork and upstream workflow](#the-fork-and-upstream-workflow)
- [Remote-tracking branches](#remote-tracking-branches)
- [Inspecting a remote](#inspecting-a-remote)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Remote?

A remote is a named reference to a repository hosted somewhere other than your local machine. It is essentially a bookmark - a short name that points to a URL.

When you run `git push` or `git pull`, Git looks up the remote's URL and communicates with that server to send or receive commits.

You can have as many remotes as you like. Each one has a name (used in commands) and a URL (where Git actually connects). Remote names are arbitrary - you can name them anything - but there are strong conventions that almost everyone follows.

---

## origin and upstream

**`origin`** is the conventional name for the primary remote - the one you cloned from or the one you push your work to. When you clone a repository, Git automatically creates a remote called `origin` pointing to the URL you cloned from.

**`upstream`** is the conventional name for the original repository when you are working from a fork. If you fork someone else's repository on GitHub and clone your fork, `origin` points to your fork and `upstream` points to the original repository you forked from.

These are just conventions. Nothing in Git enforces these names. But they are followed so universally that you should use them unless you have a specific reason not to.

---

## Viewing Remotes

**List all configured remotes:**

```bash
git remote
```

**List remotes with their URLs:**

```bash
git remote -v
```

Output:

```
origin  https://github.com/YOUR_USERNAME/YOUR_REPO.git (fetch)
origin  https://github.com/YOUR_USERNAME/YOUR_REPO.git (push)
```

Each remote shows two URLs - one for fetch and one for push. In most cases they are identical. You can configure them separately if you need to fetch from one URL and push to another, but this is rare.

---

## Adding a Remote

**Add a new remote:**

```bash
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

The syntax is `git remote add <name> <url>`.

**Add a remote using SSH:**

```bash
git remote add origin git@github.com:YOUR_USERNAME/YOUR_REPO.git
```

HTTPS and SSH are both valid. Use whichever authentication method you have configured. SSH is more convenient for regular use once set up.

**Typical scenario - starting a local project and pushing it to GitHub:**

1. Create a new repository on GitHub (do not initialise it with a README)
2. In your local project folder:

```bash
git init
git add .
git commit -m "add: initial commit"
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git branch -M main
git push -u origin main
```

The `git branch -M main` renames your current branch to `main` if it is not already called that. `-M` forces the rename even if a branch called `main` already exists.

---

## Removing and Renaming Remotes

**Remove a remote:**

```bash
git remote remove origin
```

Or the shorter alias:

```bash
git remote rm origin
```

This only removes the remote reference from your local configuration. It does not delete the remote repository itself.

**Rename a remote:**

```bash
git remote rename origin upstream
```

This renames the remote from `origin` to `upstream` in your local configuration. Useful when you have forked a project and want to add your fork as `origin` while keeping the original as `upstream`.

---

## Fetching from a Remote

`git fetch` downloads commits, branches and tags from a remote but does not apply them to your working branch. It updates your local knowledge of what exists on the remote without touching your files.

**Fetch from the default remote (origin):**

```bash
git fetch
```

**Fetch from a specific remote:**

```bash
git fetch upstream
```

**Fetch a specific branch:**

```bash
git fetch origin main
```

**Fetch all remotes:**

```bash
git fetch --all
```

**Fetch and prune stale remote-tracking branches:**

```bash
git fetch --prune
```

This removes references to remote branches that have been deleted on the server. Without `--prune`, your local list of remote branches accumulates stale entries over time.

### When to use fetch instead of pull

`git fetch` is useful when you want to see what is on the remote before merging it into your branch. Common scenarios:

- Checking what your colleagues have pushed before integrating it
- Reviewing changes on a CI branch before merging
- Updating your view of the remote without changing your working directory

After fetching, you can inspect what arrived:

```bash
git fetch origin
git log origin/main --oneline
git diff HEAD origin/main
```

Then merge when you are ready:

```bash
git merge origin/main
```

---

## Pulling from a Remote

`git pull` is `git fetch` followed by `git merge`. It downloads the latest commits and immediately merges them into your current branch.

**Pull from the tracked remote branch:**

```bash
git pull
```

**Pull from a specific remote and branch:**

```bash
git pull origin main
```

**Pull using rebase instead of merge:**

```bash
git pull --rebase
```

This rebases your local commits on top of the fetched commits instead of creating a merge commit. It keeps the history linear. Covered in depth in [git/13-rebase.md](13-rebase.md).

**Pull all remotes:**

```bash
git pull --all
```

> [!TIP]
> Always pull before starting new work for the day. If your local branch is behind the remote, pulling first reduces the chance of conflicts when you push later.

> [!NOTE]
> If `git pull` fails because your local branch has diverged from the remote, you need to decide how to reconcile them. Either `git pull --rebase` to rebase your commits on top of the remote, or `git pull` followed by resolving the merge conflict if there is one.

---

## Pushing to a Remote

`git push` sends your local commits to the remote repository.

**Push the current branch to its tracked remote:**

```bash
git push
```

**Push to a specific remote and branch:**

```bash
git push origin main
```

**Push a new branch and set up tracking:**

```bash
git push -u origin feature/user-login
```

The `-u` flag (long form: `--set-upstream`) tells Git to remember that this local branch corresponds to `origin/feature/user-login`. After this, `git push` and `git pull` on this branch work without specifying the remote and branch name.

**Push all local branches:**

```bash
git push --all
```

**Push tags:**

```bash
git push --tags
```

By default, `git push` does not push tags. Use `--tags` to push all tags, or push a specific tag:

```bash
git push origin v2.0.0
```

**Delete a remote branch:**

```bash
git push origin --delete feature/old-branch
```

> [!WARNING]
> Never force push to a shared branch without coordinating with your team. `git push --force` rewrites the remote history and will break the workflow of anyone who has already pulled. Use `git push --force-with-lease` instead - it fails safely if the remote has commits you do not have locally.

> [!CAUTION]
> Check what you are pushing before you push it. Once commits land on a public remote, removing sensitive data requires a full history rewrite and immediate credential rotation. Run `git log --oneline origin/main..HEAD` to see exactly which local commits will be pushed.

---

## Tracking Branches

A tracking branch is a local branch configured to follow a specific remote branch. When a local branch tracks a remote branch, `git push` and `git pull` know where to push to and pull from without you specifying.

**Set up tracking when pushing for the first time:**

```bash
git push -u origin feature/user-login
```

**Set up tracking for an existing branch:**

```bash
git branch -u origin/main main
```

Or from within the branch:

```bash
git branch -u origin/main
```

**View tracking relationships for all branches:**

```bash
git branch -vv
```

Output:

```
  feature/user-login  a1b2c3d [origin/feature/user-login: ahead 2] add: login validation
* main                e4f5g6h [origin/main] update: README
```

The `ahead 2` means your local branch has 2 commits that have not been pushed. `behind 3` would mean the remote has 3 commits you have not pulled.

---

## Working with Multiple Remotes

You can add as many remotes as you need. This is common when:

- You are contributing to an open source project (fork + upstream)
- You deploy to multiple environments (staging server + production server)
- You maintain mirrors of a repository
- Your team uses both GitHub and GitLab

**Add a second remote:**

```bash
git remote add staging https://git.example.com/project.git
```

**Push to a specific remote:**

```bash
git push staging main
```

**Fetch from all remotes:**

```bash
git fetch --all
```

**View all remote-tracking branches across all remotes:**

```bash
git branch -r
```

---

## The Fork and Upstream Workflow

The fork and upstream workflow is the standard way to contribute to open source projects on GitHub and GitLab.

**The setup:**

1. Fork the original repository on GitHub to your account
2. Clone your fork locally

```bash
git clone https://github.com/YOUR_USERNAME/original-project.git
cd original-project
```

3. Add the original repository as `upstream`

```bash
git remote add upstream https://github.com/ORIGINAL_OWNER/original-project.git
```

4. Verify your remotes

```bash
git remote -v
```

You should see:

```
origin    https://github.com/YOUR_USERNAME/original-project.git (fetch)
origin    https://github.com/YOUR_USERNAME/original-project.git (push)
upstream  https://github.com/ORIGINAL_OWNER/original-project.git (fetch)
upstream  https://github.com/ORIGINAL_OWNER/original-project.git (push)
```

**The ongoing workflow:**

Keep your fork up to date with the original:

```bash
git fetch upstream
git switch main
git merge upstream/main
git push origin main
```

Create a branch for your contribution:

```bash
git switch -c feature/my-contribution
```

Make your changes, commit and push to your fork:

```bash
git push -u origin feature/my-contribution
```

Open a pull request from your fork's branch to the original repository's `main`.

**Why this workflow exists:**

You do not have write access to the original repository. You can only push to your own fork. The pull request is your way of proposing that the original maintainer merge your changes. This is covered in detail in [github/08-pull-requests.md](../03-github/08-pull-requests.md).

---

## Remote-Tracking Branches

When you fetch from a remote, Git creates **remote-tracking branches** in your local repository. These are read-only snapshots of where the remote branches were the last time you fetched.

Remote-tracking branches are named `remote/branch` - for example `origin/main`, `origin/feature/user-login` or `upstream/main`.

**List all remote-tracking branches:**

```bash
git branch -r
```

**View the commit history of a remote-tracking branch:**

```bash
git log origin/main --oneline
```

**Compare your local branch with the remote-tracking branch:**

```bash
git diff main origin/main
```

**Check out a remote branch locally:**

```bash
git switch -c feature/their-work origin/feature/their-work
```

This creates a local branch `feature/their-work` that tracks `origin/feature/their-work`.

Or use the shorthand - Git is smart enough to set up tracking automatically if the branch name matches:

```bash
git switch feature/their-work
```

---

## Inspecting a Remote

**Show detailed information about a remote:**

```bash
git remote show origin
```

Output:

```
* remote origin
  Fetch URL: https://github.com/YOUR_USERNAME/YOUR_REPO.git
  Push  URL: https://github.com/YOUR_USERNAME/YOUR_REPO.git
  HEAD branch: main
  Remote branches:
    main                    tracked
    feature/user-login      tracked
  Local branch configured for 'git pull':
    main merges with remote main
  Local ref configured for 'git push':
    main pushes to main (up to date)
```

This shows the fetch and push URLs, all remote branches and the configured pull and push behaviour for your local branches.

---

## Try It Yourself

This exercise uses a real remote repository. You will need a GitHub account.

**Step 1.** Create a new repository on GitHub.

Go to github.com, click `+` -> **New repository**. Name it `remote-practice`. Leave it empty (no README, no .gitignore). Click **Create repository**.

**Step 2.** Create a local repository and connect it to GitHub:

**Windows (PowerShell):**

```powershell
mkdir remote-practice && cd remote-practice && git init
echo "# Remote Practice" > README.md
git add README.md && git commit -m "add: initial README"
git remote add origin https://github.com/YOUR_USERNAME/remote-practice.git
git branch -M main
git push -u origin main
```

**Mac and Linux:**

```bash
mkdir remote-practice && cd remote-practice && git init
echo "# Remote Practice" > README.md
git add README.md && git commit -m "add: initial README"
git remote add origin https://github.com/YOUR_USERNAME/remote-practice.git
git branch -M main
git push -u origin main
```

**Step 3.** Verify the remote is set up:

```bash
git remote -v
```

**Step 4.** Make a change on GitHub directly.

Go to your repository on GitHub, click `README.md`, click the pencil icon to edit, add a line and commit directly on GitHub.

**Step 5.** Fetch the change without merging:

```bash
git fetch origin
git log origin/main --oneline
```

You should see the commit you made on GitHub.

**Step 6.** See the difference between your local and remote:

```bash
git diff main origin/main
```

**Step 7.** Merge the remote change into your local branch:

```bash
git merge origin/main
```

**Step 8.** Make a local change and push it:

```bash
echo "Added locally." >> README.md
git add README.md && git commit -m "update: add local line to README"
git push
```

Go to GitHub and refresh - you should see your local commit appear.

**Step 9.** Check tracking status:

```bash
git branch -vv
```

**Step 10.** Clean up - delete the GitHub repository via Settings -> Delete this repository, then delete locally:

**Windows:**

```powershell
cd .. && rmdir /s /q remote-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf remote-practice
```

---

## Common Mistakes

**Pushing without setting upstream first.**

If you create a branch locally and run `git push` without the `-u` flag, Git does not know which remote branch to push to and returns an error. The fix is `git push -u origin branch-name` the first time.

**Confusing `origin` and `upstream` in a fork.**

In the fork workflow, `origin` is your fork and `upstream` is the original. A common mistake is fetching from `origin` expecting the original project's latest changes - but `origin` is your fork, which may be outdated. Always fetch from `upstream` to get the original project's changes.

**Not pruning stale remote-tracking branches.**

After branches are deleted on the remote, your local `git branch -r` still lists them until you prune. Run `git fetch --prune` regularly or configure Git to prune automatically:

```bash
git config --global fetch.prune true
```

**Force pushing to a shared branch.**

`git push --force` rewrites the remote history. If anyone else has pulled from that branch, their history now conflicts with the remote. Use `git push --force-with-lease` instead - it checks for remote commits you do not have and refuses to overwrite them.

**Running `git pull` on the wrong branch.**

If you are on `feature/login` and run `git pull`, Git fetches and merges the tracked remote branch for `feature/login` - not `main`. Always check which branch you are on before pulling.

**Cloning with HTTPS then trying to push with SSH (or vice versa).**

The URL format for HTTPS (`https://github.com/...`) and SSH (`git@github.com:...`) are different. If you cloned with HTTPS but have SSH set up, you can switch the remote URL:

```bash
git remote set-url origin git@github.com:YOUR_USERNAME/YOUR_REPO.git
```

---

## Summary

- A remote is a named reference to a repository hosted on a server
- `origin` is the conventional name for the primary remote; `upstream` is used for the original repository in a fork workflow
- `git remote -v` shows all configured remotes and their URLs
- `git remote add <name> <url>` adds a new remote
- `git fetch` downloads remote commits without merging; `git pull` downloads and merges
- `git push -u origin branch-name` pushes a new branch and sets up tracking
- Remote-tracking branches (`origin/main`, `upstream/main`) are read-only snapshots updated by `git fetch`
- The fork workflow uses two remotes: `origin` for your fork and `upstream` for the original repository
- Use `git push --force-with-lease` instead of `git push --force` on shared branches

---

## Sources and Further Reading

- [Official git remote documentation](https://git-scm.com/docs/git-remote) - the complete reference for remote management commands
- [Official git fetch documentation](https://git-scm.com/docs/git-fetch) - detailed reference for fetching from remotes
- [Pro Git book, Chapter 3.5: Remote Branches](https://git-scm.com/book/en/v2/Git-Branching-Remote-Branches) - the official guide to remote-tracking branches
- [GitHub - Working with forks](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks) - GitHub's guide to the fork and upstream workflow
- [Atlassian - Syncing](https://www.atlassian.com/git/tutorials/syncing) - clear guide to fetch, pull and push with visual diagrams

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

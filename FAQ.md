# ❓ Frequently Asked Questions

> "The only stupid question is the one you were afraid to ask." - Unknown

Every question here is one that real developers - beginners and seniors alike - have actually asked on Stack Overflow, Reddit, GitHub Discussions, freeCodeCamp forums and developer communities around the world. If you are confused about something, you are not alone. Git confuses everyone at first, and even experienced developers still Google some of these regularly.

If your question is not here, open a [Discussion](https://github.com/zaccessss/git-unlocked/discussions) and we will answer it and add it.

---

## 📋 Table of Contents

- [The Basics](#-the-basics)
- [Git vs GitHub vs GitLab](#-git-vs-github-vs-gitlab)
- [Everyday Git Operations](#-everyday-git-operations)
- [Undoing Things](#-undoing-things)
- [Branches and Merging](#-branches-and-merging)
- [Remote Repositories](#-remote-repositories)
- [Common Error Messages](#-common-error-messages)
- [GitHub Questions](#-github-questions)
- [GitLab Questions](#-gitlab-questions)
- [Security Questions](#-security-questions)
- [Advanced Git Questions](#-advanced-git-questions)
- [Mindset and Motivation](#-mindset-and-motivation)

---

## 🟢 The Basics

### What is Git?

Git is a version control system. It tracks every change you make to your files over time, so you can see what changed, when it changed, who changed it and go back to any earlier version if something breaks.

Think of it like this: imagine you are writing an essay and you save a new copy every hour - `essay_v1.docx`, `essay_v2.docx`, `essay_final.docx`, `essay_final_REAL.docx`. Git does that automatically, but in a much smarter and more organised way.

Git was created by Linus Torvalds in April 2005 - the same person who created the Linux operating system. He built it in just two weeks to manage contributions to the Linux kernel after the team's previous tool became unavailable. The name "git" is British slang for an unpleasant person. Torvalds joked: "I'm an egotistical bastard, and I name all my projects after myself. First Linux, now git."

### What is a repository?

A repository (or "repo") is a folder that Git is tracking. It contains all your project files and a hidden `.git` folder where Git stores the entire history of every change ever made.

When you run `git init` in a folder, that folder becomes a repository. When you `git clone` a project from GitHub, you are downloading a repository to your computer.

### What is a commit?

A commit is a saved snapshot of your project at a specific point in time. Every commit has a unique ID, a message describing what changed, the author's name and email, and a timestamp.

A commit is not the same as saving a file. When you save a file, only that file is updated on your computer. When you commit, Git takes a snapshot of everything you have staged and permanently records it in the project history.

### What is the staging area? Why do I have to `git add` before `git commit`?

This is one of the most common sources of confusion for beginners - and it is a fair one.

The staging area (also called the index or cache) is a middle step between your working files and your commit history. It lets you choose exactly which changes to include in your next commit.

For example, you might have changed five files but only want to commit three of them together because they are related. You `git add` those three files to the staging area, then `git commit` to save just those three as one snapshot. The other two stay as uncommitted changes.

```
Working directory  →  git add  →  Staging area  →  git commit  →  Repository history
```

### What is HEAD?

HEAD is simply a pointer to the commit you are currently looking at - almost always the latest commit on your current branch. Think of it as "where you are right now" in your project history.

When you switch branches, HEAD moves to point to the latest commit on that branch. When you check out an old commit, HEAD points directly to that commit and you are in what Git calls a "detached HEAD state."

### What is a branch?

A branch is an independent line of development. It lets you work on a new feature or fix a bug without affecting the main codebase until you are ready.

Technically, a branch is just a lightweight pointer to a specific commit. Creating a branch costs almost nothing in Git - it is just a tiny file. This is very different from older version control systems where branching was slow and expensive.

### What is .gitignore and what does it do?

`.gitignore` is a plain text file you place in your repository that tells Git which files and folders to ignore completely - they will never be tracked, staged or committed.

Common things to ignore:

- `node_modules/` - installed packages (huge, should never be committed)
- `.env` - environment files containing secrets and API keys
- `*.log` - log files
- `.DS_Store` - Mac system files
- `__pycache__/` - Python cache files
- `dist/` or `build/` - compiled output

⚠️ **Important:** If a file was already committed before you added it to `.gitignore`, Git will continue tracking it. You need to run `git rm --cached filename` to stop tracking it without deleting it from your computer.

---

## 🟢 Git vs GitHub vs GitLab

### What is the difference between Git and GitHub?

This is the most asked question about Git across every platform.

**Git** is the tool. It is free, open source software you install on your computer. It tracks changes to your files locally. It has nothing to do with the internet.

**GitHub** is a website. It hosts Git repositories online so you can back them up, share them and collaborate with others. GitHub is owned by Microsoft (acquired in 2018 for $7.5 billion).

The analogy: Git is like Microsoft Word - the software you use to write. GitHub is like Google Drive - the place you store and share your documents online.

You can use Git without GitHub. You can also use GitHub without knowing much Git (though you will be limited). Learning both together is the best approach.

### What is GitLab?

GitLab is a competitor to GitHub. It is also a website that hosts Git repositories but it was built with a stronger focus on the entire DevOps lifecycle - particularly CI/CD (automating testing and deployment).

GitLab was founded in 2011 by Dmitriy Zaporozhets and Sytse "Sid" Sijbrandij in Ukraine. Unlike GitHub, GitLab's core product is open source (Community Edition, MIT licenced) meaning you can download it and host it yourself for free on your own server. GitLab went public on the NASDAQ stock exchange in October 2021.

Key differences:

- GitLab calls pull requests "merge requests"
- GitLab has built-in CI/CD that many consider more powerful than GitHub Actions
- GitLab can be self-hosted for free
- GitHub has a larger open source community and more developers use it publicly
- Both are excellent - the choice usually comes down to your team or company's preference

### Is GitHub free?

Yes. GitHub has a generous free tier that includes unlimited public and private repositories, GitHub Actions minutes, GitHub Pages and more. Paid plans (Pro, Team, Enterprise) add features like advanced security, more Actions minutes and team management tools.

### Is GitLab free?

Yes. GitLab.com has a free tier and GitLab Community Edition is free to self-host. Paid tiers (Premium and Ultimate) add advanced features.

---

## 🟢 Everyday Git Operations

### How do I check which branch I am on?

```bash
git status
```

The first line will tell you which branch you are on. You can also run:

```bash
git branch
```

The branch with an asterisk `*` next to it is your current branch.

### How do I see my commit history?

```bash
git log
```

For a cleaner, more readable view:

```bash
git log --oneline
```

For a visual graph of branches:

```bash
git log --oneline --graph --all
```

⌨️ Press `q` to exit the log view in the terminal.

### What is the difference between `git fetch` and `git pull`?

This is one of the most searched Git questions of all time.

**`git fetch`** downloads changes from the remote repository to your computer but does NOT apply them to your working files. It updates your knowledge of what is on the remote without touching anything you are working on.

**`git pull`** downloads the changes AND immediately merges them into your current branch. It is essentially `git fetch` followed by `git merge`.

💡 **Tip:** Using `git fetch` first lets you inspect what changed before merging. Many experienced developers prefer this workflow to avoid surprises.

### How do I save my work without committing?

Use `git stash`. It temporarily shelves your changes so you can switch to something else, then come back later.

```bash
git stash          # save your changes
git stash pop      # bring them back
git stash list     # see all stashes
```

### How do I write a good commit message?

A good commit message explains what changed and why - not how (the code shows the how).

✅ Good:

```
fix: correct calculation error in invoice total
add: user profile image upload feature
update: improve error message clarity on login failure
```

❌ Bad:

```
fix stuff
update
asdfgh
changes
WIP
```

A widely used format is Conventional Commits:

```
type: short description

Optional longer description explaining why this change
was made and any relevant context.
```

Common types: `add`, `fix`, `update`, `remove`, `refactor`, `docs`, `test`

---

## 🟡 Undoing Things

> "In Git, almost nothing is truly lost. The reflog is your safety net." - Common developer wisdom

### How do I undo my last commit?

This is the single most searched Git question in history with over 14 million views on Stack Overflow.

It depends on what you want to do:

**Keep the changes but undo the commit (most common):**

```bash
git reset --soft HEAD~1
```

Your files stay exactly as they are. The commit is undone but your changes remain staged.

**Undo the commit and unstage the changes:**

```bash
git reset HEAD~1
```

Your files stay as they are but the changes are no longer staged.

**Undo the commit AND discard all the changes:**

```bash
git reset --hard HEAD~1
```

⚠️ **Warning:** This permanently deletes your changes. Use with caution.

### How do I undo `git add` before committing?

```bash
git restore --staged filename
```

Or to unstage everything:

```bash
git restore --staged .
```

### How do I discard changes to a file I have not committed?

```bash
git restore filename
```

⚠️ **Warning:** This permanently discards your uncommitted changes to that file. They cannot be recovered.

### What is the difference between `git reset` and `git revert`?

**`git reset`** moves the branch pointer backwards in history, effectively removing commits. It rewrites history. Never use it on commits that have already been pushed to a shared remote branch.

**`git revert`** creates a new commit that undoes the changes from a previous commit. It does not rewrite history - it adds to it. This is the safe way to undo a change that has already been pushed.

If the commit is only local: `git reset` is fine.
If the commit has been pushed and others may have pulled it: use `git revert`.

### How do I recover something I accidentally deleted with `git reset --hard`?

Use `git reflog`. Git keeps a local log of everywhere HEAD has pointed for about 30 days.

```bash
git reflog
```

Find the commit you want to recover, copy its hash, then:

```bash
git checkout -b recovery-branch abc1234
```

This creates a new branch from that commit so you can get your work back.

---

## 🟡 Branches and Merging

### How do I create a new branch?

```bash
git checkout -b branch-name
```

Or using the newer syntax:

```bash
git switch -c branch-name
```

### How do I delete a branch?

Locally:

```bash
git branch -d branch-name      # safe delete - only if merged
git branch -D branch-name      # force delete
```

On the remote:

```bash
git push origin --delete branch-name
```

### What is the difference between `git merge` and `git rebase`?

Both combine changes from one branch into another but they do it differently.

**`git merge`** creates a new "merge commit" that joins the histories of both branches. The history shows exactly what happened and when. It is non-destructive.

**`git rebase`** moves your commits to sit on top of another branch as if you had started your work from there. It creates a cleaner, linear history but rewrites commit history.

```
Merge result:
A --- B --- C --- M  (main)
          /
    D --- E  (feature)

Rebase result:
A --- B --- C --- D' --- E'  (feature, now on top of main)
```

💡 **General rule:** Use merge for shared branches. Use rebase to clean up your own local work before sharing it.

⚠️ **Never rebase a branch that others are working on.** It rewrites history and causes serious problems for everyone else.

### How do I resolve a merge conflict?

A merge conflict happens when two branches have changed the same part of the same file and Git cannot automatically decide which version to keep.

Git marks the conflict in the file like this:

```
<<<<<<< HEAD
This is the version from your current branch
=======
This is the version from the branch being merged
>>>>>>> feature-branch
```

To resolve it:

1. Open the file
2. Decide which version to keep (or combine them)
3. Delete the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
4. Save the file
5. Run `git add filename`
6. Run `git commit`

💡 VS Code has a built-in merge conflict editor that makes this much easier - it shows both versions side by side with buttons to accept one or both.

### I accidentally committed to main. How do I move that commit to a feature branch?

```bash
# Create a new branch pointing to your current commit
git branch feature-branch

# Move main back one commit
git reset --hard HEAD~1

# Switch to your feature branch
git checkout feature-branch
```

---

## 🟡 Remote Repositories

### What is "origin"?

`origin` is just the default name Git gives to the remote repository you cloned from. It is not a special word - it is just a nickname (called a "remote") that points to a URL.

When you clone a repository, Git automatically sets up `origin` to point to that URL. You can rename it, add other remotes or remove it.

```bash
git remote -v          # see all remotes and their URLs
git remote add upstream https://github.com/someone/repo.git  # add another remote
```

### What is the difference between `origin` and `upstream`?

**`origin`** is your fork - the copy of the repository on your GitHub account.

**`upstream`** is the original repository you forked from - the one maintained by the project owner.

This matters when contributing to open source. You pull updates from `upstream` to keep your fork current and push your changes to `origin` to then open a pull request.

### How do I push my code to GitHub for the first time?

```bash
git remote add origin https://github.com/YOUR_USERNAME/REPO_NAME.git
git branch -M main
git push -u origin main
```

The `-u` flag sets `origin main` as the default for future pushes so you can just type `git push` afterwards.

### How do I force push safely?

A regular `git push --force` can overwrite work that someone else has pushed since your last pull. Use `--force-with-lease` instead - it will only force push if nobody else has pushed since you last fetched:

```bash
git push --force-with-lease
```

⚠️ **Never force push to a shared branch like `main` or `develop`.** Only force push to your own personal branches.

---

## 🔴 Common Error Messages

### `fatal: not a git repository`

You are running a Git command in a folder that is not a Git repository. Either navigate to the correct folder or run `git init` to initialise one.

```bash
cd path/to/your/project
git init
```

### `fatal: Authentication failed`

GitHub removed password authentication in August 2021. You need to use either SSH keys or a Personal Access Token instead of your password.

- Set up SSH: `ssh-keygen -t ed25519 -C "your_email@example.com"` then add the public key to GitHub Settings
- Or create a Personal Access Token at github.com/settings/tokens and use it as your password

### `error: failed to push some refs`

Someone else has pushed to the remote since your last pull. You need to pull their changes first:

```bash
git pull --rebase origin main
git push
```

### `fatal: refusing to merge unrelated histories`

You are trying to merge two repositories that do not share a common commit history. This often happens when you initialise a local repo and also create one on GitHub separately.

```bash
git pull origin main --allow-unrelated-histories
```

Then resolve any conflicts and commit.

### `error: Your local changes would be overwritten by merge`

You have uncommitted changes that conflict with incoming changes. Either commit your changes first or stash them:

```bash
git stash
git pull
git stash pop
```

### `error: src refspec main does not match any`

You have not made any commits yet so the `main` branch does not exist. Make at least one commit first:

```bash
git add .
git commit -m "initial commit"
git push -u origin main
```

---

## 🟢 GitHub Questions

### How do I create a repository on GitHub?

1. Go to [github.com/new](https://github.com/new)
2. Give it a name
3. Choose public or private
4. Optionally add a README, .gitignore and licence
5. Click **Create repository**

### What is a pull request?

A pull request (PR) is a way of saying "I have made some changes on my branch - please review them and merge them into the main codebase."

It is not a Git feature - it is a GitHub (and GitLab) feature built on top of Git. GitLab calls them "merge requests" which is arguably a better name since that is literally what they do.

### What is forking?

Forking creates your own copy of someone else's repository on your GitHub account. You can make changes to your fork without affecting the original. When you want to contribute your changes back, you open a pull request from your fork to the original.

Forking is different from cloning. Cloning downloads a repository to your computer. Forking creates a server-side copy on GitHub.

### How do I keep my fork up to date?

```bash
# Add the original repo as upstream (only needed once)
git remote add upstream https://github.com/ORIGINAL_OWNER/REPO.git

# Fetch the latest changes from upstream
git fetch upstream

# Merge upstream changes into your main branch
git checkout main
git merge upstream/main

# Push the updated main to your fork
git push origin main
```

### How do I set up SSH for GitHub?

**Step 1 - Generate an SSH key:**

🪟 **Windows / 🍎 Mac / 🐧 Linux:**

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

Press Enter to accept the default location. Add a passphrase for extra security.

**Step 2 - Copy the public key:**

🪟 **Windows:**

```bash
cat ~/.ssh/id_ed25519.pub
```

🍎 **Mac:**

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

🐧 **Linux:**

```bash
cat ~/.ssh/id_ed25519.pub
```

**Step 3 - Add to GitHub:**

1. Go to github.com/settings/keys
2. Click **New SSH key**
3. Paste your public key
4. Click **Add SSH key**

**Step 4 - Test it:**

```bash
ssh -T git@github.com
```

You should see: `Hi USERNAME! You've successfully authenticated.`

### What is a Personal Access Token?

A Personal Access Token (PAT) is a password replacement for GitHub. Since GitHub removed support for password authentication in August 2021, you need a PAT to push over HTTPS.

To create one:

1. Go to github.com/settings/tokens
2. Click **Generate new token**
3. Select scopes (at minimum: `repo`)
4. Copy the token immediately - you will not see it again

⚠️ **Treat your PAT like a password.** Never commit it to a repository. Store it in a password manager.

### What is GitHub Pages?

GitHub Pages is a free hosting service built into GitHub. It serves static websites (HTML, CSS and JavaScript) directly from a repository. It is perfect for portfolios, project documentation and simple websites.

To enable it: go to your repository Settings → Pages → choose your branch → save.

Your site will be live at `https://USERNAME.github.io/REPO-NAME`.

---

## 🟢 GitLab Questions

### What is the difference between a pull request and a merge request?

They are the same thing. GitHub calls them pull requests. GitLab calls them merge requests. The name "merge request" is arguably clearer since that is exactly what you are asking someone to do - review your changes and merge them.

### What is GitLab CI/CD?

CI/CD stands for Continuous Integration and Continuous Delivery/Deployment. It is the practice of automatically running tests, checks and deployments every time you push code.

GitLab has CI/CD built directly into the platform. You define your pipeline in a file called `.gitlab-ci.yml` at the root of your repository. When you push, GitLab reads that file and runs your pipeline automatically.

GitHub has a similar feature called GitHub Actions, defined in `.github/workflows/`.

### What is a GitLab Runner?

A GitLab Runner is a programme that runs your CI/CD pipeline jobs. When you trigger a pipeline, GitLab sends the jobs to a runner which executes them.

GitLab.com provides shared runners for free. You can also install your own runner on your own machine or server for more control.

### Can I self-host GitLab?

Yes. GitLab Community Edition (CE) is open source and free to self-host. This means you can run your own private GitLab instance on your own server with no subscription cost. This is one of the main reasons companies choose GitLab over GitHub.

### What are the user roles in GitLab?

GitLab has five permission levels for project members:

| Role       | What they can do                             |
| ---------- | -------------------------------------------- |
| Guest      | View issues and code (if public)             |
| Reporter   | Clone repo, create issues, view CI/CD        |
| Developer  | Push code, create branches, run pipelines    |
| Maintainer | Manage members, protected branches, settings |
| Owner      | Full control including deletion              |

---

## 🔒 Security Questions

### I accidentally pushed a password or API key to GitHub. What do I do?

Act immediately - bots scan GitHub for exposed credentials within seconds of a push.

**Step 1 - Revoke the credential first.** Go to wherever the key was issued (AWS, GitHub, Google etc.) and invalidate it immediately. This is the most important step.

**Step 2 - Remove it from your repository:**

```bash
# Add the file to .gitignore
echo "filename" >> .gitignore

# Remove the file from tracking
git rm --cached filename

# Commit the change
git commit -m "remove: accidentally committed credential"

# Push
git push
```

**Step 3 - Remove it from history.** Simply deleting the file is not enough - the credential is still in your commit history. Use `git filter-repo` to permanently remove it:

```bash
# Install git-filter-repo first
pip install git-filter-repo

# Remove the file from all history
git filter-repo --path filename --invert-paths
```

Then force push the cleaned history:

```bash
git push --force-with-lease
```

⚠️ If the repository is public and even one person has cloned or forked it, assume the credential is compromised regardless. Always revoke it.

### What should I never commit to a repository?

Never commit:

- Passwords of any kind
- API keys and tokens (AWS, Google, Stripe, GitHub PATs etc.)
- Private SSH keys (files ending in no extension or `.pem`)
- `.env` files containing real values
- Database connection strings with credentials
- OAuth client secrets
- Certificate private keys

Always use environment variables or a secrets manager for sensitive values, and always add these files to `.gitignore` before writing any real values into them.

### What is a `.env` file and why must it be ignored?

A `.env` file stores environment variables - configuration values your application needs to run, like database URLs, API keys and secret keys. They are used so you do not hardcode these values into your source code.

Because `.env` files contain sensitive information, they must always be in `.gitignore`. A safe practice is to commit a `.env.example` file with placeholder values to show others what variables are needed, but never the actual values.

```bash
# .gitignore
.env
.env.local
.env.production
```

### How do I check if I have accidentally committed something sensitive?

Search your commit history:

```bash
git log --all --full-history -- "*.env"
git grep "API_KEY" $(git rev-list --all)
```

You can also use tools like `git-secrets` or `truffleHog` which scan repositories for accidentally committed credentials.

### Is it safe to commit to a private repository?

Private repositories are not visible to the public but that does not make them completely safe for storing credentials. Team members, GitHub staff and anyone with access to the repository can see the history. If the repository is ever made public or if an account is compromised, those credentials are exposed.

The rule is simple: never commit credentials anywhere, public or private.

---

## 🔴 Advanced Git Questions

### What is an interactive rebase?

Interactive rebase (`git rebase -i`) lets you rewrite your commit history before sharing it. You can squash multiple commits into one, reorder commits, edit commit messages or drop commits entirely.

```bash
git rebase -i HEAD~3    # interactively rebase the last 3 commits
```

This opens an editor where you can change the instruction before each commit:

- `pick` - keep the commit as is
- `reword` - keep the commit but edit the message
- `squash` - combine with the previous commit
- `drop` - remove the commit entirely

### What is `git cherry-pick`?

Cherry-pick lets you apply a specific commit from one branch onto another without merging the entire branch.

```bash
git cherry-pick abc1234
```

It is useful when you want one specific fix from a feature branch but are not ready to merge the whole thing.

### What is `git bisect`?

Bisect uses binary search to find which commit introduced a bug. You tell Git which commit is known-good and which is known-bad, and Git checks out the commit halfway between them. You test it, tell Git if it is good or bad, and Git narrows it down until it finds the exact commit that broke things.

```bash
git bisect start
git bisect bad              # current commit is broken
git bisect good v1.0        # version 1.0 was fine
# Git checks out a commit halfway between - test it, then:
git bisect good             # or git bisect bad
# Repeat until Git identifies the culprit
git bisect reset            # return to HEAD when done
```

### What is `git reflog`?

The reflog is Git's diary - it records every time HEAD has moved, including checkouts, resets and rebases. It keeps entries for about 30 days and is your best tool for recovering from mistakes.

```bash
git reflog
```

If you accidentally ran `git reset --hard` and lost commits, the reflog will show you the commit hash you were on before and you can recover your work.

### What is the difference between `HEAD^` and `HEAD~`?

Both refer to parent commits but they differ when a commit has more than one parent (i.e. a merge commit).

- `HEAD~1` = one commit back following the first parent
- `HEAD~2` = two commits back following first parents
- `HEAD^1` = same as `HEAD~1` (first parent)
- `HEAD^2` = the second parent of a merge commit

For everyday use `HEAD~1`, `HEAD~2` etc. is what you need. The `^` notation only matters when navigating merge commits.

---

## 💪 Mindset and Motivation

### Is Git really this confusing for everyone?

Yes - genuinely. "How do I undo my last commit" has been the most searched Git question on Stack Overflow for over a decade with more than 14 million views. Developers with 10 years of experience still Google it.

Git was originally built by Linus Torvalds to manage the Linux kernel - one of the most complex software projects in human history with thousands of contributors. It was not designed with beginners in mind. It was designed for power and flexibility at extreme scale.

The good news is you only need to know about 10 commands for 90% of daily work. The rest you can look up as you need it.

### Why does everyone seem to know Git except me?

They do not. Most developers know a small set of commands and Google everything else. The developer who looks most confident with Git in your team almost certainly has ohshitgit.com bookmarked.

As one developer put it: "Most developers don't actually know Git that well. They know 10-15 commands and Google the rest."

### I feel like I am going to break everything. Is that normal?

Completely normal. The fear of breaking things is one of the most common feelings beginners report. Here is the truth: Git makes it very hard to permanently lose work. Almost everything is recoverable using the reflog, stash or by simply checking out an older commit.

The worst thing you can do is avoid committing because you are afraid. Commit often. The more commits you have, the more checkpoints you have to go back to.

> "Every expert was once a beginner. Every professional was once an amateur." - Robin Sharma

### What is the minimum I need to know to use Git day to day?

These 10 commands cover the vast majority of everyday Git usage:

```bash
git init                    # start tracking a folder
git clone URL               # download a repository
git status                  # see what has changed
git add .                   # stage all changes
git commit -m "message"     # save a snapshot
git push                    # upload to remote
git pull                    # download and merge from remote
git branch                  # list branches
git checkout -b name        # create and switch to a branch
git merge branch-name       # merge a branch into current
```

Everything else - rebase, cherry-pick, bisect, hooks - you can learn as you need it.

### How long does it take to get comfortable with Git?

With daily practice, most people feel comfortable with the basics within two to four weeks. Getting confident with branching, merging and remote workflows takes a few months of real project use. Advanced features like interactive rebase and bisect come with time and specific need.

The fastest way to learn is to use it on a real project every day - even if it is just your own personal projects. That is exactly what this course is designed to help you do.

> "The expert in anything was once a beginner." - Helen Hayes

---

## 🔗 Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc)
- [GitHub official documentation](https://docs.github.com)
- [GitLab official documentation](https://docs.gitlab.com)
- [GitHub Security best practices](https://docs.github.com/en/code-security)
- [Oh Shit, Git!?! - Katie Sylor-Miller](https://ohshitgit.com) - honest, practical fixes for common Git mistakes
- [Confusing Git Terminology - Julia Evans](https://jvns.ca/blog/2023/11/01/confusing-git-terminology/) - a developer survey on what confuses people most
- [git-filter-repo](https://github.com/newren/git-filter-repo) - the recommended tool for removing sensitive data from history
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) - a simpler alternative for cleaning history
- [Conventional Commits](https://www.conventionalcommits.org) - a standard for writing commit messages
- [First Contributions](https://github.com/firstcontributions/first-contributions) - a beginner-friendly open source project to practise on
- [Git History on Wikipedia](https://en.wikipedia.org/wiki/Git) - full history of how Git was created

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

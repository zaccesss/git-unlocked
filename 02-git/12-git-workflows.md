# Git Workflows

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

A Git workflow is an agreed set of rules for how a team uses branches, commits and merges to collaborate on a project. Git itself does not enforce any particular workflow - it gives you the tools and lets you decide how to use them. But choosing the right workflow for your team and project makes collaboration significantly smoother.

This file covers the four most widely used workflows in professional software development, when to use each one and how to implement them.

---

## Table of Contents

- [Why workflows matter](#why-workflows-matter)
- [Centralised workflow](#centralised-workflow)
- [Feature branch workflow](#feature-branch-workflow)
- [GitHub Flow](#github-flow)
- [Gitflow](#gitflow)
- [Trunk-based development](#trunk-based-development)
- [Choosing the right workflow](#choosing-the-right-workflow)
- [Workflow comparison](#workflow-comparison)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Why Workflows Matter

Without an agreed workflow, teams run into the same problems repeatedly:

- Half-finished features land on `main` and break things for everyone
- Nobody knows which branch is production-ready
- Conflicts pile up because everyone is working on the same branch
- Releases are chaotic because code is not properly separated by readiness

A workflow solves these problems by giving everyone a shared understanding of how code moves from an idea to production. It answers questions like:

- Where do I start new work?
- How do I share work in progress?
- How does code get reviewed before it merges?
- How is a release prepared and tagged?
- How is an urgent hotfix handled?

The best workflow is the simplest one that solves your team's actual problems. A solo developer does not need Gitflow. A team releasing multiple versions simultaneously does not need GitHub Flow. Understanding all of them lets you make an informed choice.

---

## Centralised Workflow

The simplest possible workflow. Everyone commits directly to a single branch - usually `main`. There are no feature branches, no pull requests, no separate development branches.

### How it works

```
main
 |
 A -- B -- C -- D -- E -- F
      ^    ^    ^    ^    ^
   Alice  Bob Alice  Bob Alice
```

Everyone clones the repository, makes changes locally, and pushes directly to `main`. When conflicts arise, they are resolved locally before pushing.

### The workflow

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
# make changes
git add .
git commit -m "add: my changes"
git pull --rebase
git push
```

The `git pull --rebase` before pushing keeps the history linear by rebasing your local commits on top of any new remote commits rather than creating a merge commit.

### When to use it

- Solo projects where you are the only contributor
- Very small teams (two people) with excellent communication
- Simple projects with infrequent changes
- Migrating a team from SVN - it mirrors the SVN workflow closely

### When not to use it

- Any team larger than two or three people
- Projects with CI/CD pipelines that deploy from `main`
- Any project where code review before merging is required

---

## Feature Branch Workflow

Every piece of work - every feature, every bug fix, every experiment - gets its own branch. Nothing is committed directly to `main`. Work is reviewed and merged via pull requests.

This is the most widely used workflow in professional development and the foundation on which GitHub Flow and Gitflow are built.

### How it works

```
main
 |
 A -- B --------------------- M (merge commit)
           \                 /
            C -- D -- E -- F
                 |
          feature/user-login
```

1. Developer creates a branch from `main`
2. Commits work to that branch
3. Pushes the branch to the remote
4. Opens a pull request for review
5. Changes are reviewed, discussed and approved
6. Branch is merged into `main`
7. Branch is deleted

### The workflow

**Start new work:**

```bash
git switch main
git pull
git switch -c feature/your-feature-name
```

**Work and commit:**

```bash
git add .
git commit -m "add: implement feature"
```

**Push and open a pull request:**

```bash
git push -u origin feature/your-feature-name
```

Then open a pull request on GitHub or GitLab.

**After the PR is merged, clean up:**

```bash
git switch main
git pull
git branch -d feature/your-feature-name
```

### When to use it

- Teams of any size
- Projects where code review is required before merging
- Open source projects where contributors do not have direct write access
- Any project using GitHub or GitLab pull/merge requests

### When not to use it

- This workflow is almost universally applicable. It is rarely the wrong choice.

---

## GitHub Flow

GitHub Flow is a lightweight workflow built on the feature branch workflow. It was designed by GitHub for teams that deploy continuously - where `main` is always deployable and new features go live as soon as they are merged.

It has one rule that distinguishes it from the basic feature branch workflow: **`main` must always be in a deployable state.**

### How it works

```
main (always deployable)
 |
 A -- B ----------- M1 ----------- M2
       \           /  \           /
        C -- D -- E    F -- G -- H
        feature/login  feature/profile
```

There are no release branches, no develop branches, no version tags for routine releases. `main` is the release. When a pull request is merged, it deploys.

### The workflow

**Step 1.** Branch off `main`:

```bash
git switch main
git pull
git switch -c feature/descriptive-name
```

**Step 2.** Commit small, frequent changes with clear messages:

```bash
git add .
git commit -m "add: user login form"
git commit -m "add: form validation"
git commit -m "fix: handle empty email field"
```

**Step 3.** Push and open a pull request early, even before it is finished. Use GitHub's draft PR feature for work in progress. This invites early feedback and makes the review process visible.

```bash
git push -u origin feature/descriptive-name
```

**Step 4.** Discuss and iterate. Address review feedback with additional commits on the same branch.

**Step 5.** Deploy and test from the branch before merging. GitHub and many CI tools support deploying directly from a branch.

**Step 6.** Merge when approved. Delete the branch.

### When to use it

- Teams deploying continuously (multiple times per day)
- SaaS products with a single production version
- Small to medium teams
- Projects where simplicity is valued over ceremony

### When not to use it

- Software with multiple versions in production simultaneously (mobile apps, packaged software, APIs with versioning)
- Teams that need a stabilisation period between development and release
- Projects requiring long release cycles

---

## Gitflow

Gitflow is a more structured workflow designed for projects that release versioned software on a schedule. It was defined by Vincent Driessen in January 2010 and became extremely influential in enterprise software development.

It uses multiple long-lived branches with specific purposes, and a strict set of rules about how code flows between them.

### The branches

**`main`** - production code only. Every commit on `main` is a release. Tagged with version numbers.

**`develop`** - the integration branch. Completed features are merged here. This is the branch that is always ahead of `main`.

**`feature/*`** - one branch per feature. Branched from `develop`, merged back to `develop`.

**`release/*`** - release preparation. Branched from `develop` when enough features are ready for a release. Only bug fixes go here - no new features. Merged into both `main` and `develop` when the release is ready.

**`hotfix/*`** - urgent production fixes. Branched directly from `main`, merged back into both `main` and `develop`.

### How it works

```
main     A --------------------------------- H (v2.0) --- K (v2.0.1)
          \                                /               /
hotfix     \                              /         J-----/
            \                           /          /
develop      B --- C --- D --- E --- F --- G --- I --- L
                   |         /         /
feature            \---X---Y/         /
                             \       /
release                       Z-----/
```

### The workflow

**Starting a new feature:**

```bash
git switch develop
git pull
git switch -c feature/your-feature
# work and commit
git switch develop
git merge --no-ff feature/your-feature
git branch -d feature/your-feature
git push origin develop
```

**Preparing a release:**

```bash
git switch develop
git switch -c release/v2.0.0
# update version numbers, fix minor bugs
git switch main
git merge --no-ff release/v2.0.0
git tag -a v2.0.0 -m "Release version 2.0.0"
git switch develop
git merge --no-ff release/v2.0.0
git branch -d release/v2.0.0
git push origin main develop --tags
```

**Fixing a production bug urgently:**

```bash
git switch main
git switch -c hotfix/critical-payment-bug
# fix the bug
git switch main
git merge --no-ff hotfix/critical-payment-bug
git tag -a v2.0.1 -m "Hotfix: payment bug"
git switch develop
git merge --no-ff hotfix/critical-payment-bug
git branch -d hotfix/critical-payment-bug
git push origin main develop --tags
```

### The git-flow tool

Managing Gitflow manually is error-prone. The `git-flow` CLI tool automates the branch creation, merging and deletion steps.

**Install:**

**Windows (Git Bash):**

```bash
git flow init
```

Git Bash includes git-flow. Run `git flow init` in a repository to set it up.

**Mac:**

```bash
brew install git-flow-avh
```

**Linux (Ubuntu/Debian):**

```bash
sudo apt install git-flow
```

**Use:**

```bash
git flow feature start user-login
git flow feature finish user-login
git flow release start 2.0.0
git flow release finish 2.0.0
git flow hotfix start critical-bug
git flow hotfix finish critical-bug
```

### When to use Gitflow

- Desktop applications, mobile apps or packaged software released in versions
- Projects maintaining multiple versions simultaneously (v1.x and v2.x in production)
- Teams with a defined QA and release cycle
- Enterprise software with scheduled releases
- Any project where `main` cannot always be in a deployable state during development

### When not to use Gitflow

- Continuously deployed web applications - it adds complexity with no benefit
- Small teams - the overhead outweighs the structure
- Projects where speed of delivery is more important than release ceremony

> [!NOTE]
> Gitflow has attracted criticism in recent years for being overly complex for modern continuous delivery practices. Vincent Driessen himself added a note to his original 2010 post acknowledging that for web applications that do not need to support multiple versions, GitHub Flow is often a better fit. Use Gitflow when you genuinely need versioned releases, not as a default.

---

## Trunk-Based Development

Trunk-based development (TBD) is at the opposite end of the spectrum from Gitflow. Everyone commits to a single shared branch - the trunk, usually called `main` - either directly or through very short-lived feature branches that last no more than a couple of days.

It is the workflow that underlies continuous integration and continuous delivery at scale. Google, Facebook and Netflix all use trunk-based development.

### Core principles

**The trunk is always releasable.** Every commit must leave the codebase in a working state. Broken commits are fixed immediately or reverted.

**Branches are short-lived.** If you use branches at all, they exist for hours or at most a day or two - never weeks.

**Feature flags control release.** Incomplete features are hidden behind feature flags (also called feature toggles) rather than living on a separate branch. The code is deployed but the feature is off for users until it is ready.

**High test coverage is essential.** With everyone committing to the same branch constantly, automated tests are the safety net. A comprehensive CI pipeline runs on every commit.

### How it works

**Small teams committing directly to main:**

```bash
git switch main
git pull
# make a small, complete change
git add .
git commit -m "add: email validation to signup form"
git pull --rebase
git push
```

**Larger teams using short-lived branches:**

```bash
git switch main
git pull
git switch -c feature/email-validation
# complete the work in 1-2 days maximum
git push -u origin feature/email-validation
# open PR, get fast review, merge same day
git switch main
git pull
git branch -d feature/email-validation
```

### Feature flags example

```python
# The feature is deployed but hidden until the flag is enabled
if feature_flags.is_enabled("new_checkout_flow", user):
    show_new_checkout()
else:
    show_old_checkout()
```

The flag is turned on for internal testers first, then a percentage of users, then everyone. If something is wrong, the flag is turned off instantly without a code deployment.

### When to use trunk-based development

- Teams with strong automated test coverage
- Continuous delivery pipelines that deploy multiple times per day
- Large engineering organisations (Google, Meta, Amazon all use it)
- Teams experienced with feature flags

### When not to use it

- Teams without good automated testing - the safety net is not there
- Teams new to Git - the discipline required is high
- Projects that cannot tolerate any instability on main

---

## Choosing the Right Workflow

Use this as a starting point:

| Situation | Recommended workflow |
|---|---|
| Solo project | Centralised or feature branch |
| Small team, web app, continuous deployment | GitHub Flow |
| Medium to large team, web app | Feature branch workflow |
| Versioned software with release cycles | Gitflow |
| Large engineering team with strong CI/CD | Trunk-based development |
| Open source project | Feature branch workflow |
| Learning Git | Feature branch workflow |

When in doubt, start with the **feature branch workflow**. It is flexible enough for most situations, easy to understand and scales well as your team grows.

---

## Workflow Comparison

| Workflow | Branches | Complexity | Deployment | Best for |
|---|---|---|---|---|
| Centralised | 1 (main) | Very low | Manual | Solo, tiny teams |
| Feature branch | main + feature/* | Low | On merge | Most teams |
| GitHub Flow | main + feature/* | Low | Continuous | Web apps, SaaS |
| Gitflow | main, develop, feature/*, release/*, hotfix/* | High | Scheduled | Versioned software |
| Trunk-based | 1 (main) + short feature/* | Medium | Continuous | Large teams, CI/CD |

---

## Try It Yourself

This exercise practises the feature branch workflow - the most universally applicable one.

**Step 1.** Set up a repository:

**Windows (PowerShell):**

```powershell
mkdir workflow-practice && cd workflow-practice && git init
echo "# My Project" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir workflow-practice && cd workflow-practice && git init
echo "# My Project" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Start a new feature the right way:

```bash
git switch main
git switch -c feature/about-section
```

**Step 3.** Work on the feature with multiple commits:

**Windows:**

```powershell
echo "# About" > about.md
git add about.md && git commit -m "add: about page skeleton"
echo "We build great software." >> about.md
git add about.md && git commit -m "update: add company description to about page"
```

**Mac and Linux:**

```bash
echo "# About" > about.md
git add about.md && git commit -m "add: about page skeleton"
echo "We build great software." >> about.md
git add about.md && git commit -m "update: add company description to about page"
```

**Step 4.** Check the log on the feature branch:

```bash
git log --oneline
```

**Step 5.** Switch to main and notice the feature is not there:

```bash
git switch main
git log --oneline
```

**Step 6.** Merge the feature branch into main:

```bash
git merge --no-ff feature/about-section -m "merge: add about section"
```

**Step 7.** View the full graph:

```bash
git log --oneline --graph --all
```

**Step 8.** Delete the feature branch:

```bash
git branch -d feature/about-section
```

**Step 9.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q workflow-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf workflow-practice
```

---

## Common Mistakes

**Choosing Gitflow for a simple web app.**

Gitflow adds significant complexity. If your application has one version in production and you deploy continuously, GitHub Flow or the feature branch workflow is almost certainly a better fit. Only adopt Gitflow if you genuinely need parallel version support or scheduled releases.

**Long-lived feature branches.**

The longer a branch lives, the more it diverges from `main` and the harder it is to merge. Aim to merge feature branches within days, not weeks. If a feature takes weeks to build, use feature flags to merge the code in stages while keeping the feature hidden until it is ready.

**Committing directly to `main` in a team environment.**

Even if your team uses the simplest workflow, unreviewed code on `main` causes problems. At minimum, use feature branches and pull requests so at least one other person sees every change before it merges.

**Not pulling `main` before branching.**

If you branch from an outdated `main`, your feature branch starts from old code. Always pull the latest `main` before creating a branch.

**Inconsistent workflow across the team.**

A workflow only works if everyone follows it. If some team members use feature branches and others commit directly to `main`, the benefits disappear. Document your workflow (a short section in your `CONTRIBUTING.md` is enough) and make sure everyone understands it.

**Treating workflow rules as absolute.**

Workflows are guidelines, not laws. A workflow that makes sense for a 50-person team may be overkill for a team of three. Adapt as needed. The goal is smooth collaboration, not adherence to a methodology.

---

## Summary

- A Git workflow is an agreed set of rules for how a team uses branches to collaborate
- **Centralised workflow** - everyone commits to `main`. Simple but does not scale
- **Feature branch workflow** - one branch per feature, merged via pull requests. The most widely applicable workflow
- **GitHub Flow** - feature branches + continuous deployment from `main`. Ideal for web apps deploying continuously
- **Gitflow** - multiple long-lived branches for complex versioned releases. Use only when you genuinely need it
- **Trunk-based development** - everyone commits to `main` with short-lived branches and feature flags. Used by large engineering organisations with strong CI/CD
- When in doubt, start with the feature branch workflow
- Document your chosen workflow in `CONTRIBUTING.md` so every contributor understands it

---

## Sources and Further Reading

- [A successful Git branching model](https://nvie.com/posts/a-successful-git-branching-model/) - Vincent Driessen's original 2010 Gitflow post, including his 2020 reflection note
- [GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow) - GitHub's official guide to their recommended workflow
- [Trunk Based Development](https://trunkbaseddevelopment.com) - the comprehensive reference site for trunk-based development
- [Atlassian - Comparing workflows](https://www.atlassian.com/git/tutorials/comparing-workflows) - side-by-side comparison of all major Git workflows with diagrams
- [git-flow cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/) - quick reference for all git-flow commands
- [Feature flags (Martin Fowler)](https://martinfowler.com/articles/feature-toggles.html) - the definitive guide to feature flags, essential for trunk-based development

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

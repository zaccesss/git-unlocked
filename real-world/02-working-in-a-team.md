# Working in a team

🟡 Intermediate - 🔴 Advanced

---

## Table of contents

- [Introduction](#introduction)
- [Branching strategies](#branching-strategies)
- [Commit message conventions](#commit-message-conventions)
- [Code review best practices](#code-review-best-practices)
- [CODEOWNERS](#codeowners)
- [Protected branches and rulesets](#protected-branches-and-rulesets)
- [Merge queues](#merge-queues)
- [How the giants actually work](#how-the-giants-actually-work)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources](#sources)

---

## Introduction

When you work alone, Git is a backup and history tool. When you work in a team, Git becomes the coordination layer for everything: who changed what, when, and why; which features are safe to deploy; how to untangle two people's conflicting changes; and how to ensure nothing lands on `main` without passing tests.

This file covers the decisions every team needs to make - which branching strategy fits your release model, how to set up protected branches and rulesets, how code review works in practice, and how merge queues prevent the CI-passing-on-my-branch-but-broken-on-main problem. It draws on published engineering practices from Google, Microsoft, Shopify and Netflix.

**What you need:**

- A GitHub or GitLab account with admin access to a repository (for the protected branches section)
- Familiarity with branching (`git switch -c`), merging and rebasing

---

## Branching strategies

A branching strategy is a shared agreement about which branches exist, what they represent, and how changes flow between them. The right choice depends on how often you ship and whether you need to support multiple live versions simultaneously.

### GitFlow 🔴

GitFlow was defined by Vincent Driessen in 2010 and dominated the 2010s. It uses five branch types:

- `main` - production-ready code only, tagged for each release
- `develop` - integration branch where features merge
- `feature/*` - one branch per feature, branched from `develop`
- `release/*` - branched from `develop` when a release is prepared; only bug fixes allowed
- `hotfix/*` - branched from `main` for emergency production fixes; merged into both `main` and `develop`

```bash
# Start a feature
git switch -c feature/dark-mode develop

# Finish a feature (merge into develop, delete branch)
git switch develop
git merge --no-ff feature/dark-mode
git branch -d feature/dark-mode

# Start a release
git switch -c release/2.1.0 develop

# Finish a release (merge into main AND develop)
git switch main
git merge --no-ff release/2.1.0
git tag -a v2.1.0 -m "Release 2.1.0"
git switch develop
git merge --no-ff release/2.1.0
git branch -d release/2.1.0
```

> [!NOTE]
> In March 2020, Vincent Driessen added a note to his original post: "If your team is doing continuous delivery of software, I would suggest to adopt a much simpler workflow (like GitHub Flow) instead of trying to shorten a Git Flow workflow." GitFlow is still appropriate for mobile apps, firmware, desktop software and any project that ships versioned releases and needs to maintain multiple live versions simultaneously.

**When to use it:** versioned software, mobile apps, libraries with LTS branches, teams that cannot deploy continuously.

**When not to use it:** SaaS teams deploying multiple times per day; the long-lived branches become a source of merge pain.

### GitHub Flow 🟡

GitHub Flow has one rule: `main` is always deployable. Everything else is a short-lived feature branch.

```
main ─────────────────────────────────────────────────────
         \                         /
          feat/login ──────────────
                   \              /
                    fix/typo ─────
```

1. Branch from `main`: `git switch -c feat/login`
2. Commit and push regularly
3. Open a PR when ready for review (or as a draft earlier)
4. After review, merge into `main`
5. Deploy immediately (or automatically via CI/CD)

**When to use it:** SaaS products, web apps, APIs, any team deploying frequently from a single version.

### GitLab Flow 🟡

GitLab Flow extends GitHub Flow with environment branches or release branches for teams that need staged deployments.

**Environment branch variant:**

```
main ──── pre-production ──── production
  \           |                   |
   feat ──>  (cherry-pick)       (cherry-pick)
```

Changes merge into `main` first (upstream-first), then are promoted to `pre-production` and `production` via cherry-pick or merge. This preserves the "nothing deploys without passing `main`" rule.

**Release branch variant (for versioned software without the full GitFlow overhead):**

```
main
  \── 14-9-stable ── (only bug fixes cherry-picked from main)
  \── 15-0-stable ── (only bug fixes cherry-picked from main)
```

Docs: [docs.gitlab.com/topics/gitlab_flow](https://docs.gitlab.com/topics/gitlab_flow/).

### Trunk-based development 🔴

All developers commit to `main` (the "trunk") directly or via very short-lived branches (lifetime: hours to 1-2 days maximum). Incomplete work is hidden behind **feature flags**, not long-lived branches.

```bash
# Short-lived branch variant (most common in practice)
git switch -c feat/JIRA-456-payment-retry
# ...work for a few hours...
git push -u origin feat/JIRA-456-payment-retry
gh pr create --fill      # reviewed and merged same day
```

Feature flags let teams merge code that is not yet user-visible:

```python
if feature_flags.is_enabled("new-payment-retry", user):
    return new_payment_retry_flow(order)
return legacy_payment_flow(order)
```

DORA (DevOps Research and Assessment) research consistently correlates trunk-based development with elite delivery performance - faster lead times, higher deployment frequency, lower change failure rates. Reference: [trunkbaseddevelopment.com](https://trunkbaseddevelopment.com/).

**When to use it:** SaaS teams deploying continuously, mature CI/CD pipelines, teams comfortable with feature flags.

**When not to use it:** teams without automated testing, software that ships versioned releases to end users who manage their own upgrades.

### Choosing a strategy

| Question | Answer | Strategy |
|---|---|---|
| Do you ship continuously (multiple times per day)? | Yes | Trunk-based or GitHub Flow |
| Do you maintain multiple live versions? | Yes | GitFlow or GitLab Flow (release branches) |
| Do you have staged environments (dev/staging/prod)? | Yes | GitLab Flow |
| Are you a small team moving fast? | Yes | GitHub Flow |
| Do you ship versioned installers or mobile apps? | Yes | GitFlow |

---

## Commit message conventions

Good commit messages are the single most underrated practice in team Git. They make `git log`, `git blame` and `git bisect` dramatically more useful, and they enable automatic CHANGELOG generation.

### Conventional Commits

[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) is the dominant convention, used by Angular, Vue, Nx, Turborepo and thousands of other projects.

Format: `<type>[optional scope][optional !]: <description>`

```
feat(auth): add OAuth2 login support
fix(parser): handle empty input without crashing
docs(readme): update installation instructions for Windows
refactor(api): extract response normalisation into helper
perf(db): add index on user_id in orders table
test(auth): add edge case for expired tokens
ci(github-actions): pin Node.js version to 20.x
chore(deps): bump express from 4.18.2 to 4.19.2
```

The `!` suffix or a `BREAKING CHANGE:` footer marks a breaking change (triggers a MAJOR version bump in semantic versioning):

```
feat(api)!: remove deprecated v1 endpoints

BREAKING CHANGE: /api/v1/* routes have been removed. Use /api/v2/*.
```

**Tools:**

- **commitlint** - validates commit messages in CI or as a `commit-msg` hook
- **commitizen** (`cz`) - interactive CLI for writing conformant messages
- **semantic-release** - reads Conventional Commits to automate versioning and release notes
- **git-cliff** - generates CHANGELOGs from Conventional Commits

```bash
# Install commitlint globally
npm install -g @commitlint/cli @commitlint/config-conventional

# Validate a message
echo "feat: add dark mode" | commitlint
```

---

## Code review best practices

Code review is where team Git slows down or speeds up. The research is clear on what works.

### Size

**Keep PRs under 400 lines of diff.** Google's internal standard targets 200 lines. Every study on code review finds that reviewers catch fewer bugs as PR size grows. A reviewer can meaningfully engage with 200 lines in 20 minutes; 1,000 lines gets a rubber stamp.

If a feature requires 2,000 lines of change, split it into a **stacked PR** series: each PR builds on the previous one, and each is independently reviewable.

### Response time

**Review within one working day.** Unreviewed PRs are the most common source of developer frustration in team Git. They block the author, cause branch drift (requiring rebases), and waste the context the reviewer built up from the PR description. Set a team SLA and hold to it.

### What to look for

A good review checks:

1. Does the code do what the PR description says it does?
2. Are edge cases handled?
3. Is the code readable and maintainable?
4. Are there test gaps?
5. Does it follow the project's patterns and conventions?

A good review does **not** bikeshed on formatting or style that your linter should catch automatically. If your CI does not enforce formatting, add it - that is a tooling problem, not a review problem.

### Tone

Code review feedback should be about the code, not the person. "This function is doing too many things - could we split it?" is constructive. "Why would you write it like this?" is not.

Google's engineering practices documentation ([google.github.io/eng-practices/review](https://google.github.io/eng-practices/review/)) is the most widely cited public reference on this topic and is worth reading in full.

---

## CODEOWNERS

`CODEOWNERS` is a file that maps paths in your repository to the GitHub users or teams responsible for reviewing changes to those paths. When a PR touches a CODEOWNERS-listed path, the listed owners are automatically added as reviewers.

### GitHub

Place the file at `.github/CODEOWNERS`, `CODEOWNERS` or `docs/CODEOWNERS`.

```
# Syntax: <pattern> <owner> [<owner2> ...]

# Default owner for everything not matched below
*                          @org/core-team

# Frontend code requires a frontend team review
/frontend/                 @org/frontend-team

# Authentication requires a specific engineer
/src/auth/                 @alice @bob

# All Markdown requires the docs team
*.md                       @org/docs-team

# GitHub Actions workflows require DevOps review
/.github/workflows/        @org/devops-team
```

Docs: [docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners).

### GitLab

GitLab supports `CODEOWNERS` with the same syntax plus sections and role-based syntax (available from GitLab 17.9):

```
# Required section: both owners must approve
[Backend][2] @backend-team @alice

# Optional section (^ prefix): at least one approval requested but not required
^[Documentation]
docs/         @docs-team

# Sections with role-based assignment
[Security]
/secrets/     @@maintainer
```

Docs: [docs.gitlab.com/user/project/codeowners](https://docs.gitlab.com/user/project/codeowners/).

---

## Protected branches and rulesets

### GitHub Rulesets (the current standard)

> [!IMPORTANT]
> GitHub has two systems for branch protection: **classic branch protection rules** (legacy) and **Rulesets** (current standard since 2023). Use Rulesets for new setups. Classic rules still work but cannot stack, cannot apply at the organisation level, and cannot enforce commit metadata. Rulesets do all three.

Rulesets are created at **Settings > Rules > Rulesets** in your repository (or at the organisation level to apply across many repos).

**Key controls available in a ruleset:**

- Require a pull request before merging (minimum N approvals, require CODEOWNERS review, dismiss stale reviews on new push)
- Require status checks to pass (list specific CI job names)
- Require branches to be up to date before merging
- Require signed commits
- Require linear history (no merge commits)
- Block force pushes
- Block branch deletion
- Require deployments to succeed in specific environments
- Require a merge queue
- Restrict who can bypass the ruleset (via a bypass list)
- Restrict commit metadata (message pattern, author email pattern)

Rulesets also apply to **tags** and cover the **fork network** for push rules - classic branch protection did not.

Docs: [docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets).

**Recommended ruleset for a production `main` branch:**

```
Target: main
Rules:
  - Require a pull request: minimum 1 approval, require CODEOWNERS review
  - Require status checks: ci/build, ci/test
  - Require branches to be up to date: yes
  - Block force pushes: yes
  - Require linear history: yes (optional, depends on team preference)
  - Require signed commits: yes (optional, but recommended)
Bypass list: (leave empty, or add a release automation service account)
```

### GitLab protected branches

In GitLab, go to **Settings > Repository > Protected branches**.

```
Branch: main
Allowed to merge: Maintainers
Allowed to push: No one
Allowed to force push: No
```

GitLab's **push rules** (Premium) add commit-message regex enforcement, author-email restrictions, maximum file size, and rejection of unsigned commits. **MR approval rules** define how many approvals are required and which users or groups can provide them.

---

## Merge queues

### The problem merge queues solve

Imagine two PRs: PR-A and PR-B. Both branch from `main` at commit X. Both pass CI. If you merge PR-A first, `main` is now at commit Y. PR-B was tested against X, not Y. If PR-A and PR-B both modify the same function, PR-B could introduce a regression that neither PR tested.

A merge queue solves this by testing each PR against the actual state it will land in.

### GitHub Merge Queue (GA July 2023)

Enable it in your Ruleset: add the **Require merge queue** rule to your `main` branch. Add a GitHub Actions workflow to handle the `merge_group` trigger:

```yaml
# .github/workflows/ci.yml
on:
  push:
    branches: [main]
  pull_request:
  merge_group:           # this line enables merge queue CI

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test
```

When a PR is ready to merge, click **Merge when ready** (not the green Merge button). GitHub creates a temporary branch `gh-readonly-queue/main/pr-123-<sha>` that combines `main` + any earlier queued PRs + your PR. CI runs against this combined state. If it passes, the PR merges. If it fails, only the failing PR is ejected from the queue.

Docs: [docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue).

### GitLab Merge Trains (Premium)

GitLab's equivalent is **Merge Trains**, available since GitLab 12.0. Enable it at **Settings > Merge requests > Merge trains**. When enabled, MRs join a sequential train; GitLab runs a combined pipeline for each train position and fast-forwards on success.

---

## How the giants actually work

### Google

Google does not use Git for its primary codebase. **Piper** (Google's internal VCS) holds approximately 2 billion lines of code across 86 TB, with around 40,000 commits per day (roughly 16,000 from humans, 24,000 from automated systems). The workflow is strict trunk-based development with no long-lived branches. Google developed **CitC** (Clients in the Cloud), a FUSE-based workspace that makes the entire codebase appear local without requiring a clone. Build tooling is **Blaze** (the internal version of Bazel). Chrome and Android are the notable exceptions - they live in separate Git repositories.

Reference: [cacm.acm.org/research/why-google-stores-billions-of-lines-of-code-in-a-single-repository](https://cacm.acm.org/research/why-google-stores-billions-of-lines-of-code-in-a-single-repository/).

### Shopify

Shopify's core Rails monolith has over 2.8 million lines of Ruby across 500,000+ commits. The team ships approximately 40 times per day on trunk with a custom continuous deployment tool called **Shipit** plus a merge queue. About 400 commits land per day from around 1,000 PRs. Shopify reports that after introducing a merge queue they saw a 33% improvement in PR throughput per developer.

Reference: [shopify.engineering/successfully-merging-work-1000-developers](https://shopify.engineering/successfully-merging-work-1000-developers).

### Microsoft (Windows)

The Windows repository is approximately 3.5 million files at 300 GB - far beyond what standard Git handles. Microsoft built **VFS for Git** (now **Scalar**), which virtualises the working tree so only files you touch are materialised locally. Scalar was contributed to upstream Git (bundled since Git 2.38) and uses partial clone, sparse-checkout, commit-graph, multi-pack index, fsmonitor and background maintenance. About 4,000 engineers commit to the Windows repo, generating roughly 1,760 builds per day.

Reference: [devblogs.microsoft.com/bharry/the-largest-git-repo-on-the-planet](https://devblogs.microsoft.com/bharry/the-largest-git-repo-on-the-planet/).

### Netflix

Netflix uses the **full-cycle developer** model: teams own their services from development through deployment and on-call. Each microservice lives in its own repository. Rather than a monorepo, Netflix invests in a **paved road** - centrally supported tooling (Spinnaker for CD, the Netflix OSS ecosystem) that makes the right path the easy path. Deployment frequency is high; teams deploy whenever they want via Spinnaker pipelines triggered by merges to `main`.

Reference: [netflixtechblog.com/full-cycle-developers-at-netflix-a08c31f83249](https://netflixtechblog.com/full-cycle-developers-at-netflix-a08c31f83249).

---

## Try it yourself

This exercise requires a GitHub repository where you have admin access. Create a new repository for this exercise if needed.

### Exercise: set up a production-grade `main` branch

1. Create a repository on GitHub with a README.
2. Go to **Settings > Rules > Rulesets > New ruleset > New branch ruleset**.
3. Name it `main protection`.
4. Set target: `main`.
5. Enable: Require a pull request (1 approval), Require status checks, Block force pushes.
6. Save.
7. On your local machine: `git clone` the repo, create a branch (`git switch -c test-protection`), make a change, commit and push.
8. Try to push directly to `main`: `git push origin HEAD:main` - observe the rejection.
9. Open a PR from your branch and observe the required checks and approval requirement.

**Bonus:** Add a `.github/CODEOWNERS` file that assigns yourself as the owner of all files (`* @YOUR_USERNAME`), add the "Require CODEOWNERS review" rule, and observe that you are auto-requested as a reviewer on the PR.

---

## Common mistakes

**Using classic branch protection rules on new repositories.**
Classic rules are being superseded by Rulesets. They cannot stack, cannot apply across the fork network, and have a smaller feature set. If you are setting up protection for the first time, use Rulesets from the start.

**Choosing GitFlow for a team that deploys continuously.**
GitFlow was designed for software that ships versioned releases. If your team deploys to production every day or multiple times per day, GitFlow's release branches create unnecessary overhead and long-running divergence. Use GitHub Flow or trunk-based development instead.

**Long-lived feature branches.**
The longer a branch lives, the more it diverges from `main`, and the harder the eventual merge. Aim for branches that live hours to a few days, not weeks. If a feature takes weeks, use feature flags to hide incomplete work and merge incrementally.

**Not enforcing commit message conventions in CI.**
Team commit conventions only work if they are enforced automatically. A `commitlint` check in CI or a `commit-msg` hook (via Husky or pre-commit) is the only reliable way to keep a consistent history.

**No merge queue on a busy `main` branch.**
Without a merge queue, two PRs that each pass CI individually can combine to break `main`. Once your team merges more than a handful of PRs per day, a merge queue pays for itself immediately.

**Forgetting the `merge_group` trigger in GitHub Actions.**
If you enable the GitHub Merge Queue but forget to add `merge_group:` to your CI workflow trigger, the queue will time out waiting for checks that never run. The PR will be ejected from the queue every time.

---

## Summary

The branching strategy question collapses to a single question: do you deploy continuously from one version, or do you ship versioned releases? Continuous deployment points toward trunk-based development or GitHub Flow; versioned releases point toward GitFlow or GitLab Flow (release branches).

Beyond the branching strategy, three practices define high-performance team Git: small PRs reviewed within one working day, CODEOWNERS to route reviews automatically, and Rulesets (not classic branch protection) to enforce the rules. For teams with busy `main` branches, a merge queue eliminates the class of CI failures that happen when two individually-green PRs combine unexpectedly.

---

## Sources

- [GitHub: about rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)
- [GitHub: managing a merge queue](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue)
- [GitHub: about code owners](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners)
- [GitLab: GitLab Flow](https://docs.gitlab.com/topics/gitlab_flow/)
- [GitLab: code owners](https://docs.gitlab.com/user/project/codeowners/)
- [GitLab: push rules](https://docs.gitlab.com/user/project/repository/push_rules/)
- [Trunk Based Development](https://trunkbaseddevelopment.com/)
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
- [Google Engineering Practices: code review](https://google.github.io/eng-practices/review/)
- [Shopify: successfully merging the work of 1000+ developers](https://shopify.engineering/successfully-merging-work-1000-developers)
- [Netflix: full cycle developers](https://netflixtechblog.com/full-cycle-developers-at-netflix-a08c31f83249)
- [ACM: why Google stores billions of lines of code in a single repository](https://cacm.acm.org/research/why-google-stores-billions-of-lines-of-code-in-a-single-repository/)

---

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

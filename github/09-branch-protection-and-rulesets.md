# Branch Protection and Rulesets

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 25 minutes

Branch protection rules and rulesets are how you enforce quality and safety standards on your repository's important branches. They prevent direct pushes to `main`, require code reviews before merging, enforce passing tests and control who can do what. Without them, anyone with write access can push broken code directly to production. This file covers both the classic branch protection rules system and the newer, more powerful rulesets system that is gradually replacing it.

---

## Table of Contents

- [Why protect branches?](#why-protect-branches)
- [Branch protection rules vs rulesets](#branch-protection-rules-vs-rulesets)
- [Setting up branch protection rules](#setting-up-branch-protection-rules)
- [Branch protection rule options in detail](#branch-protection-rule-options-in-detail)
- [Setting up rulesets](#setting-up-rulesets)
- [Ruleset options in detail](#ruleset-options-in-detail)
- [Targeting branches and tags with patterns](#targeting-branches-and-tags-with-patterns)
- [Bypass lists](#bypass-lists)
- [Organisation-level rulesets](#organisation-level-rulesets)
- [Evaluate mode](#evaluate-mode)
- [CODEOWNERS and required reviews](#codeowners-and-required-reviews)
- [Required status checks](#required-status-checks)
- [Monitoring and auditing](#monitoring-and-auditing)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Why Protect Branches?

Without branch protection, a developer with write access can:

- Push directly to `main` bypassing code review
- Force-push and rewrite history that others have already pulled
- Delete `main` accidentally
- Merge code that fails all tests
- Introduce secrets or malicious code without anyone seeing it

Branch protection creates guardrails that prevent these mistakes. They are not about distrust - they are about protecting the team and the codebase from accidents and oversights that happen to everyone.

**The most important protection for almost every repository:**

Requiring a pull request before merging to `main`. This single rule ensures that every change to your default branch has been reviewed by at least one other person and passed whatever automated checks you have configured.

---

## Branch Protection Rules vs Rulesets

GitHub has two systems for protecting branches. Both work and can coexist, but rulesets are the newer, more powerful system.

| Feature | Branch Protection Rules | Rulesets |
|---|---|---|
| Multiple rules per branch | No (one rule set per pattern) | Yes (multiple rulesets stack) |
| Organisation-level rules | No | Yes |
| Target tags | No | Yes |
| Target pushes (fork network) | No | Yes (push rulesets) |
| Evaluate/dry-run mode | No | Yes |
| Pre-emptive rules (before branch exists) | No | Yes |
| File size/path restrictions | No | Yes |
| Branch naming enforcement | No | Yes |
| Import/export | No | Yes |
| Bypass with granular control | Admins only | Specific roles, teams, apps |

**Recommendation:** Use rulesets for new repositories. If you have existing branch protection rules, they continue to work alongside rulesets. Migrate gradually when convenient.

---

## Setting up Branch Protection Rules

**Navigate to:** Repository → Settings → Branches → Add branch ruleset (classic branch protection).

Or directly: **Settings → Branches → Add branch protection rule**.

**Specify the branch name pattern:**

Patterns use `fnmatch` glob syntax:
- `main` - matches only the `main` branch
- `release/*` - matches all branches starting with `release/`
- `v*` - matches all branches starting with `v`
- `*` - matches all branches

Tick **Applies to all branches including future branches** to automatically protect new branches matching the pattern.

---

## Branch Protection Rule Options in Detail

### Require a pull request before merging

No one can push directly to this branch. All changes must go through a pull request. Sub-options:

**Required number of approvals before merging:** 1 to 6. For most repositories, 1 is sufficient. Critical production repositories or security-sensitive code may warrant 2+.

**Dismiss stale pull request approvals when new commits are pushed:** If the author pushes new commits after a reviewer approves, the approval is dismissed and review must happen again. Prevents the scenario where someone approves a PR, the author makes significant changes, and the PR is merged without the changes being reviewed.

**Require review from code owners:** If the changed files have a CODEOWNERS entry, those designated owners must approve before merging. Ensures domain experts review relevant changes.

**Restrict who can dismiss pull request reviews:** By default, any admin can dismiss reviews. Enable this to restrict dismissal to specific users or roles.

**Allow specified actors to bypass required pull requests:** Specific users, teams or apps can be whitelisted to push directly without a PR. Use sparingly.

### Require status checks to pass before merging

Specific checks must succeed before a PR can be merged. You add checks by name - these names come from your GitHub Actions workflow jobs or third-party CI tools.

**Require branches to be up to date before merging:** The PR branch must be current with the base branch before merging. Prevents merging code that has not been tested against the latest commits on `main`. This can slow down busy repositories - use merge queues as an alternative.

### Require conversation resolution before merging

All review discussion threads must be marked as resolved. Prevents merging with open, unaddressed feedback.

### Require signed commits

Every commit to this branch must have a verified GPG or SSH signature. Covered in [git/20-signing-commits.md](../git/20-signing-commits.md).

### Require linear history

No merge commits allowed. Pull requests must use squash merge or rebase merge. Keeps history clean and linear, making `git bisect` and `git log` easier to use.

### Require merge queue

Pull requests join a merge queue and are merged in order after CI passes against the actual merge result. Covered in [08-pull-requests.md](08-pull-requests.md).

### Require deployments to succeed before merging

A deployment to a specific environment must succeed. Useful for staging environments where you want to see the change deployed and verified before it goes to production.

### Lock branch

Makes the branch read-only for everyone, including administrators. Useful for archiving a release branch that should never change.

### Do not allow bypassing the above settings

By default, repository administrators can bypass branch protection rules. Enabling this option means even admins must follow the rules. Useful for compliance or security requirements.

### Allow force pushes

Force pushes are blocked by default on protected branches. Enabling this allows history rewriting on the branch - generally something you do not want on `main`. Only enable for specific branches where this is intentional (e.g., `gh-pages`).

### Allow deletions

Branch deletion is blocked on protected branches. Enable only if branch deletion is part of your workflow for this branch pattern.

---

## Setting up Rulesets

**Navigate to:** Repository → Settings → Rules → Rulesets → New ruleset.

Three ruleset types:
- **Branch ruleset** - protects specific branches
- **Tag ruleset** - protects specific tags
- **Push ruleset** - applies to pushes across the entire fork network

**Configure the ruleset:**

**Ruleset name:** A descriptive name. Examples: `Protect main branch`, `Require PR for all releases`, `Block force pushes`.

**Enforcement status:**

- **Active:** Rules are enforced
- **Evaluate:** Rules are monitored but not enforced (dry run)
- **Disabled:** Rules are not enforced or evaluated

**Bypass list:** Who can bypass these rules (see [Bypass lists](#bypass-lists)).

**Targets:** Which branches, tags or pushes this ruleset applies to (see [Targeting](#targeting-branches-and-tags-with-patterns)).

**Rules:** The specific protections to apply (see [Ruleset options in detail](#ruleset-options-in-detail)).

---

## Ruleset Options in Detail

Most options mirror branch protection rules, with additions:

### Restrict creations

Only bypass actors can create refs (branches or tags) matching the target pattern.

### Restrict updates

Only bypass actors can update refs matching the target pattern.

### Restrict deletions

Only bypass actors can delete refs matching the target pattern. Unlike branch protection, this applies at the ruleset level and can be combined with other rules.

### Require linear history

Same as branch protection - no merge commits.

### Require merge queue

Same as branch protection.

### Require deployments to succeed

Same as branch protection.

### Require signed commits

Same as branch protection.

### Require a pull request before merging

Same core options as branch protection rules.

### Require status checks to pass

Same core options as branch protection rules.

### Block force pushes

Prevent force pushes to matching refs.

### Require code scanning results

A new ruleset-only rule. Requires code scanning (CodeQL) to complete and meet a minimum severity threshold before merging. Configure: which tools must report, the maximum allowed severity for blocking alerts.

### Metadata restrictions (branch and tag naming)

Enforce naming conventions on branches and tags:

```
# Require feature branches to follow a pattern
pattern: refs/heads/feature/*

# Require tags to follow semver
pattern: refs/tags/v[0-9]+.[0-9]+.[0-9]+
```

### File path restrictions

Block pushes that add or modify specific file paths. Useful for preventing accidental modification of critical configuration files.

```
# Block modification of the main Actions workflow
restricted_file_paths:
  - .github/workflows/deploy.yml
```

### File extension restrictions

Block pushes that add files with specific extensions:

```
# Block committing private keys
restricted_file_extensions:
  - .pem
  - .key
  - .p12
```

### File size restrictions

Block pushes with files larger than a specified size (in megabytes). Prevents large files from being committed accidentally.

### Commit author email restrictions

Require commit authors to use a specific email pattern:

```
# Require corporate email for all commits
author_email_pattern: .*@company\.com
```

### Commit message patterns

Require commit messages to match a pattern:

```
# Enforce Conventional Commits
commit_message_pattern: ^(add|fix|update|remove|docs|test|chore):.+
```

---

## Targeting Branches and Tags with Patterns

Rulesets use `fnmatch` glob patterns. A target can match on:

**Includes:** Branches/tags matching these patterns are targeted.
**Excludes:** Branches/tags matching these patterns are excluded (even if they match an include pattern).

**Pattern examples:**

| Pattern | Matches |
|---|---|
| `~DEFAULT_BRANCH` | The default branch (`main`) |
| `~ALL` | All branches or tags |
| `main` | Only `main` |
| `release/*` | Any branch starting with `release/` |
| `v*` | Any tag starting with `v` |
| `feature/**` | Any branch with `feature/` anywhere in the path |
| `!hotfix/*` | Exclude hotfix branches (use in Excludes) |

**The `~DEFAULT_BRANCH` special pattern** is particularly useful - it always targets whatever the default branch is called, even if you rename `main` to `trunk` or something else.

---

## Bypass Lists

Bypass lists specify which actors can bypass a ruleset's rules. Actors can be:

- **Roles:** Repository admins, repository maintainers, repository writers, organisation admins
- **Teams:** Specific teams in the organisation
- **GitHub Apps:** Specific apps (e.g., your deployment bot)

**Bypass modes:**

- **Always:** The actor can always bypass the rules
- **Pull request only:** The actor can only bypass when pushing via a pull request (not direct pushes). Useful for automation that needs to merge PRs but should not be able to force push.

**Principle of least privilege:** Give bypass access only to the minimum set of actors that genuinely need it. A deployment automation app may need to push to `main` directly - a regular developer should not.

---

## Organisation-Level Rulesets

Organisation admins can create rulesets that apply across all repositories in the organisation, or to a specific subset of repositories.

**Create an organisation ruleset:**

Organisation settings → Rulesets → New ruleset.

**Repository targeting:**

- **All repositories:** Applies to every repository in the organisation
- **By name pattern:** Applies to repositories matching a name pattern
- **By property:** Applies to repositories with specific custom properties

Organisation rulesets layer on top of repository rulesets. If an organisation ruleset requires 2 approvals and a repository ruleset requires 1, the more restrictive rule (2 approvals) applies.

**Use cases for organisation rulesets:**

- Require signed commits across all repositories
- Block force pushes to the default branch everywhere
- Require specific security checks on all production repositories
- Enforce branch naming conventions organisation-wide

---

## Evaluate Mode

Rulesets have an **Evaluate** mode that logs rule violations without actually enforcing them. This lets you test a ruleset's impact before activating it.

**Enable evaluate mode:**

When creating or editing a ruleset, set **Enforcement status** to **Evaluate**.

**View evaluation results:**

Repository → Insights → Rule Insights. Shows a log of every action that would have been blocked, who performed it and which rule would have triggered.

This is invaluable before activating a new strict ruleset on a busy repository. You can see exactly which developers and workflows would be affected and communicate the change before it breaks anyone's workflow.

---

## CODEOWNERS and Required Reviews

CODEOWNERS (covered in [04-repositories.md](04-repositories.md)) works with branch protection to automatically require reviews from code owners when matching files change.

**For CODEOWNERS to trigger required reviews:**

1. Create a `CODEOWNERS` file in `.github/`, the repo root or `docs/`
2. Enable "Require review from code owners" in branch protection or rulesets
3. The code owners must have read access to the repository at minimum

When a PR modifies a file matching a CODEOWNERS pattern, those owners are automatically added as required reviewers. The PR cannot merge until they approve.

**Example `CODEOWNERS`:**

```
# All files - requires approval from the repository owner
* @YOUR_USERNAME

# Backend code requires a backend team member
/backend/ @YOUR_ORG/backend-team

# Security-critical files require a security review
/auth/ @YOUR_ORG/security-team
*.env.example @YOUR_USERNAME
```

---

## Required Status Checks

Status checks are results reported to GitHub by external services - most commonly GitHub Actions workflows but also third-party CI tools, code quality scanners, security tools and deployment systems.

**Setting up required status checks:**

In branch protection or rulesets, add check names. The name must exactly match the job name in your workflow.

**Example workflow with a named check:**

```yaml
name: CI

on: [push, pull_request]

jobs:
  run-tests:          # <-- This name is the check name
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test

  lint:               # <-- Another check name
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run lint
```

Add `run-tests` and `lint` as required status checks. Both must pass before merging.

**"Require branches to be up to date":**

This sub-option requires the PR branch to be current with the target branch before merging. It ensures tests ran against the actual merge result, not an outdated version of the branch. Can create bottlenecks in busy repositories - merge queues are a better solution at scale.

---

## Monitoring and Auditing

**Insights → Rule Insights:**

Shows which ruleset evaluations occurred, which were bypassed and by whom. Available for both active and evaluate-mode rulesets.

**Organisation audit log:**

Organisation owners can see all branch protection and ruleset changes, including who changed what and when. Available at Organisation → Settings → Logs → Audit log.

Filter by event type: `protected_branch.*`, `repository_ruleset.*`.

---

## Try It Yourself

**Step 1.** Create a test repository with at least one commit (or use an existing one).

**Step 2.** Set up a basic ruleset on the default branch:

1. Go to **Settings → Rules → Rulesets → New ruleset → New branch ruleset**
2. Name: `Protect default branch`
3. Enforcement: **Evaluate** (we will test without enforcing first)
4. Bypass list: **Add bypass → Role → Repository admin** (so you can still work)
5. Target: **Include default branch** (`~DEFAULT_BRANCH`)
6. Rules: Enable:
   - Restrict deletions
   - Require a pull request before merging (1 required approval)
   - Block force pushes
7. Click **Create**

**Step 3.** Try to push directly to main to see the evaluation:

```bash
echo "test" >> README.md
git add README.md && git commit -m "test: direct push to main"
git push origin main
```

The push succeeds (evaluate mode does not block). Go to **Insights → Rule Insights** to see that the direct push was logged as a bypass.

**Step 4.** Switch to Active enforcement:

Go back to the ruleset → change enforcement to **Active** → Save.

**Step 5.** Try to push directly again:

```bash
echo "another test" >> README.md
git add README.md && git commit -m "test: this should be blocked"
git push origin main
```

This should be rejected:

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - Changes must be made through a pull request.
```

**Step 6.** Create a PR instead:

```bash
git switch -c test/follow-the-rules
git push -u origin test/follow-the-rules
gh pr create --title "test: follow the rules" --body "Testing branch protection"
```

Since you are a repository admin in the bypass list, you can still approve and merge your own PR.

**Step 7.** Clean up - delete the test branch and reset the ruleset to evaluate mode if needed.

---

## Common Mistakes

**Not protecting `main` at all on team repositories.**

The most common mistake. Without branch protection, anyone with write access can push directly to `main`, rewrite history, delete the branch or merge broken code. Enable protection early, before the first collaborator is added.

**Setting required approvals too high.**

Requiring 4 approvals on every PR for a 3-person team means every PR needs everyone to review. This creates bottlenecks and frustration. 1-2 required approvals is appropriate for most teams.

**Not using evaluate mode before activating strict rules.**

A strict ruleset can immediately block workflows that were working fine before - especially CI/CD automation that pushes directly to branches. Use evaluate mode, review the insights, communicate changes and then activate.

**Requiring CI checks that are flaky.**

If a required check fails intermittently for reasons unrelated to the code (network timeouts, third-party service outages), it blocks every PR until someone re-runs the check. Keep required checks reliable and fast. Optional checks for flakier tests are better.

**Giving too many actors bypass access.**

Bypass lists should be minimal. If everyone is a bypass actor, branch protection provides no benefit. Reserve bypass access for automation that genuinely needs it.

**Forgetting CODEOWNERS after a team restructure.**

If team members leave and are no longer in the repository, their CODEOWNERS entries may block PRs from merging because GitHub cannot request a review from someone without access. Keep CODEOWNERS up to date with your current team.

---

## Summary

- Branch protection and rulesets prevent direct pushes, enforce reviews and require passing checks on important branches
- **Branch protection rules** are the classic system; **rulesets** are the modern replacement with more features
- Rulesets can stack multiple rules, apply organisation-wide, target tags as well as branches and support dry-run evaluation mode
- Key protections: require pull requests (and approvals), require status checks, block force pushes, restrict deletions
- Ruleset-only features: file size/path/extension restrictions, branch naming enforcement, commit message patterns, code scanning requirements
- Target branches using `fnmatch` patterns: `main`, `release/*`, `~DEFAULT_BRANCH`, `~ALL`
- Bypass lists control which actors can skip rules - keep them minimal
- Organisation-level rulesets apply across all repositories and layer with repository rulesets (most restrictive wins)
- Use evaluate mode before activating new strict rulesets to see impact without breaking workflows
- CODEOWNERS works with branch protection to auto-request reviews from domain experts when relevant files change

---

## Sources and Further Reading

- [About protected branches - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches) - complete branch protection reference
- [About rulesets - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) - rulesets overview and comparison with branch protection
- [Creating rulesets - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/creating-rulesets-for-a-repository) - step-by-step guide
- [Available rules for rulesets - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets) - complete reference for all ruleset options
- [About code owners - GitHub Docs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) - CODEOWNERS syntax and requirements

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

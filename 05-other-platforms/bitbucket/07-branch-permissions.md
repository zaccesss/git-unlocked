# Branch Permissions in Bitbucket

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Branch permissions (also called branch restrictions) are Bitbucket's way of protecting important branches from uncontrolled changes. They define who can push to a branch, who can delete it, who can merge into it and what conditions must be met before a merge is allowed. Without branch permissions, anyone with write access to a repository can push directly to `main`, delete branches accidentally or merge unreviewed code.

This file covers the full branch permission system: branch restrictions, merge checks, required builds, default reviewer rules and how all of these work together to create a production-grade code review and deployment gate.

---

## Table of Contents

1. [Why branch permissions matter](#why-branch-permissions-matter)
2. [Accessing branch permission settings](#accessing-branch-permission-settings)
3. [Branch restrictions](#branch-restrictions)
4. [Merge checks](#merge-checks)
5. [Required builds](#required-builds)
6. [Default reviewers](#default-reviewers)
7. [Combining restrictions into a complete workflow](#combining-restrictions-into-a-complete-workflow)
8. [Branch permission inheritance and priority](#branch-permission-inheritance-and-priority)
9. [Common branch permission patterns](#common-branch-permission-patterns)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## Why branch permissions matter

Consider what happens on a repository with no branch permissions:

- Any developer with write access can push directly to `main`, bypassing code review entirely
- A developer can accidentally delete a branch with `git push origin --delete main`
- A pull request can be merged with zero approvals, failing tests and unresolved comments
- There is no audit trail of who approved what before production code changed

Branch permissions address all of these. They are the enforcement layer that makes code review policies real rather than advisory. On the free plan, some merge checks are advisory (Bitbucket shows a warning but does not block the merge). On the Premium plan, merge checks are server-enforced - the merge button is disabled until all conditions are met.

---

## Accessing branch permission settings

Branch permissions are configured at the repository level.

1. Open your repository in Bitbucket
2. Click **Repository settings** in the left sidebar
3. Click **Branch permissions** under the Code section

You can also access them from a specific branch: go to **Branches**, click the `...` menu next to a branch, and select **Edit permissions**.

---

## Branch restrictions

Branch restrictions control who can push, delete or merge to a branch. They are separate from merge checks (which control the conditions for merging a pull request).

### Creating a branch restriction

1. Go to **Repository settings** > **Branch permissions**
2. Click **Add a branch permission**
3. Fill in the restriction details

### Branch pattern matching

The branch name field accepts exact names or glob patterns:

| Pattern      | Matches                                                            |
| ------------ | ------------------------------------------------------------------ |
| `main`       | Only the branch named `main`                                       |
| `release/*`  | Any branch starting with `release/`: `release/1.0`, `release/2.3`  |
| `feature/**` | Any branch under `feature/`: `feature/login`, `feature/auth/oauth` |
| `*`          | Every branch                                                       |
| `v*`         | Any branch starting with `v`: `v1.0`, `v2-stable`                  |

Patterns are evaluated from most specific to least specific. A restriction on `main` takes precedence over a restriction on `*`.

### Restriction types

**Write access restriction**

Controls who can push commits directly to the branch (bypassing pull requests).

Options:

- **No one** - nobody can push directly, all changes must go through a pull request
- **Administrators** - only repository administrators can push directly
- **Specific users or groups** - named individuals or groups can push directly; everyone else must use PRs

For production branches like `main` and `release/*`, the recommended setting is **No one** or **Administrators only**. This forces all changes through the pull request workflow.

**Delete restriction**

Controls who can delete the branch.

For permanent branches (`main`, `develop`, `release/*`), restrict deletion to **No one** or **Administrators**. Accidental branch deletion is a common mistake - a restriction prevents it.

**Merge restriction (via pull requests)**

Controls who can merge pull requests into the branch. By default, anyone who can write to the repository can merge a PR. Restricting merge means only specific users can click the Merge button, regardless of approvals.

This is useful when release managers or leads should be the only ones who merge to `main`.

### Rewrite history restriction

Prevents force pushes to the branch (`git push --force`). Force pushes rewrite history and can destroy commits that others have based work on. Restrict force pushes on any shared branch:

- **No one** can force push (recommended for `main`, `develop`, all release branches)
- **Administrators only** can force push (useful for emergency situations)

```bash
# This command is blocked if force pushes are restricted:
git push --force origin main

# Use --force-with-lease instead (safer, but still blocked by restrictions):
git push --force-with-lease origin main
```

---

## Merge checks

Merge checks are conditions that must be satisfied before a pull request can be merged. They are configured within a branch restriction rule.

> [!IMPORTANT]
> On the **Free and Standard plans**, merge checks are advisory - Bitbucket shows warnings but the merge button remains active. On the **Premium plan**, merge checks are enforced server-side - the merge button is disabled until all conditions are met.

### Available merge checks

**Minimum approvals**

Requires a minimum number of approvers to have approved the PR before merging.

```
Minimum approvals: 2
```

With this setting, the PR cannot be merged (Premium) or shows a warning (Free/Standard) until at least 2 reviewers have clicked Approve.

**No unresolved tasks**

Blocks merging while any PR task (created from a comment) remains incomplete. This ensures that all action items raised during review have been addressed.

**No failed or unfinished builds**

Blocks merging if any linked CI/CD build has failed or is still running. This prevents merging code that does not pass automated tests.

When combined with Bitbucket Pipelines, the build status of the latest pipeline run on the PR's source branch is evaluated. If the pipeline failed, the check fails. If the pipeline is running, the check is in a pending state.

**Author cannot approve**

Prevents the PR author from approving their own pull request. This is important for teams that require a minimum number of approvals - without this setting, an author could approve their own PR to meet the count.

**Require passing default reviewer approvals**

Requires all default reviewers (see below) to have approved, not just any N reviewers. This ensures specific people sign off rather than any combination of reviewers reaching the count.

**Reset approvals on new commits**

Revokes all existing approvals when new commits are pushed to the PR. This prevents the pattern where code is reviewed and approved, more changes are pushed (perhaps to address review comments), and the PR is merged without the new code being reviewed.

This setting is off by default. Enabling it creates more review work but catches a common source of quality issues.

---

## Required builds

Required builds are an extension of merge checks specifically for CI/CD build status. They are configured separately from other merge checks.

### Configuring required builds

1. Go to **Repository settings** > **Branch permissions**
2. Edit the restriction for your target branch
3. Under **Merge checks**, enable **No failed or unfinished builds**

For Bitbucket Pipelines, this automatically picks up the pipeline run status. For external CI systems (Jenkins, CircleCI), you need to configure those systems to post build status to the Bitbucket commit status API.

### Build status posting via API

External CI systems post status to:

```
POST /2.0/repositories/{workspace}/{repo_slug}/commit/{commit}/statuses/build
```

Request body:

```json
{
  "state": "SUCCESSFUL",
  "key": "my-ci-system",
  "name": "Build and Test",
  "url": "https://jenkins.example.com/job/123",
  "description": "All tests passed"
}
```

States: `INPROGRESS`, `SUCCESSFUL`, `FAILED`, `STOPPED`.

Most popular CI systems have Bitbucket plugins that handle this automatically.

### Multiple required builds

You can require that builds from multiple systems all pass before merging. For example: the Bitbucket Pipeline must pass AND a separate security scanning pipeline must pass. Each build status has a unique `key` - you can specify which keys are required.

---

## Default reviewers

Default reviewers are automatically added to pull requests targeting specific branches. This ensures the right people are always included in code review without relying on authors to remember who to add.

Default reviewers are configured in **Repository settings** > **Default reviewers** (separate from branch permissions, but complementary).

### Configuring default reviewers

1. Go to **Repository settings** > **Default reviewers**
2. Click **Add default reviewer rule**
3. Set the target branch pattern (exact name or glob)
4. Add the users who should automatically be added as reviewers
5. Optionally set a minimum number of approvals required from default reviewers

### Default reviewer rules

```
Target branch: main
Default reviewers: [alice, bob]
Minimum approvals from default reviewers: 1

Target branch: release/*
Default reviewers: [release-manager]
Minimum approvals from default reviewers: 1

Target branch: **
Default reviewers: [senior-dev]
Minimum approvals from default reviewers: 0  (added but not required)
```

### Interactions with branch restrictions

When you set a minimum approval count in a default reviewer rule, it interacts with the minimum approvals merge check:

- Branch restriction: minimum 2 approvals overall
- Default reviewer rule: minimum 1 approval from [alice]

Both conditions must be satisfied: at least 2 total approvals AND alice must be one of them.

### Who can be a default reviewer

Any Bitbucket user with at least **read access** to the repository can be a default reviewer. They do not need write access. Reviewers are notified when a PR is created and added to their reviewer list automatically.

If a default reviewer is also the PR author, they are not added as a reviewer for their own PR (you cannot review your own work).

---

## Combining restrictions into a complete workflow

Branch permissions work best when multiple restrictions are combined to create a coherent workflow. Here is a complete configuration for a production repository:

### The `main` branch

Goal: All code merged to main has been reviewed, tested and explicitly approved.

```
Branch: main

Write access restriction:    No one (all changes via PR)
Force push restriction:      No one
Delete restriction:          No one

Merge checks:
  - Minimum approvals: 2
  - Author cannot approve their own PR: yes
  - No unresolved tasks: yes
  - No failed or unfinished builds: yes
  - Reset approvals on new commits: yes
```

### The `develop` branch

Goal: Code is tested before reaching main, but the process is lighter.

```
Branch: develop

Write access restriction:    No one (all changes via PR)
Force push restriction:      Administrators only
Delete restriction:          No one

Merge checks:
  - Minimum approvals: 1
  - No failed builds: yes
```

### The `release/*` branches

Goal: Release branches are protected from accidental changes.

```
Branch: release/*

Write access restriction:    Administrators only
Force push restriction:      No one
Delete restriction:          Administrators only

Merge checks:
  - Minimum approvals: 2
  - Release manager must approve (default reviewer)
```

### Feature and bugfix branches

Feature branches (`feature/*`, `bugfix/*`) typically have no restrictions. Developers need to push freely to their own branches. The restrictions on `main` and `develop` protect the shared history.

---

## Branch permission inheritance and priority

### Workspace-level vs repository-level

Branch permissions are set at the repository level. There is no workspace-level branch permission that applies across all repositories. Each repository manages its own branch permissions independently.

### Priority

When multiple restrictions match a branch, they are all applied. There is no single "most specific wins" rule for restrictions - all matching restrictions stack.

Example: If you have a restriction on `*` (all branches) requiring 1 approval, and a restriction on `main` requiring 2 approvals, pushes to `main` must satisfy both: at least 2 approvals (from the `main` restriction) and the conditions from the `*` restriction. In practice, the stricter rule dominates.

### Administrative bypass

Repository administrators can bypass some branch restrictions. In the Bitbucket UI, administrators can merge PRs that do not meet merge checks (Free/Standard plan). On Premium, server-enforced checks cannot be bypassed even by administrators unless they first modify the branch permission settings.

This is an important distinction: Free plan merge checks are advisory for everyone including admins. Premium plan merge checks are enforced for everyone.

---

## Common branch permission patterns

### Gitflow pattern

Teams using Gitflow typically set:

- `main`: write-restricted, 2 approvals required, builds must pass
- `develop`: write-restricted, 1 approval required, builds must pass
- `release/*`: write-restricted (admins only), 2 approvals required
- `hotfix/*`: write-restricted, 1 approval required, fast-tracked builds

### Trunk-based development pattern

Teams doing trunk-based development push small, frequent changes to `main` (or `trunk`):

- `main`/`trunk`: write-restricted, 1 approval required, fast builds required (kept under 10 minutes)
- Feature branches: no restrictions
- No `develop` or `release` branches

### Solo developer pattern

A solo developer still benefits from branch permissions as a safety net:

- `main`: write-restricted to administrators (yourself), delete restricted
- No approval requirements (you are the only reviewer anyway)
- Builds must pass before merge

This prevents accidental direct pushes and catches build failures before they reach the primary branch.

---

## Try It Yourself

**Exercise 1 - Create a basic branch restriction**

1. Go to **Repository settings** > **Branch permissions**
2. Click **Add a branch permission**
3. Enter `main` as the branch name
4. Under **Write access**, select **No one**
5. Save the restriction

Now try pushing directly to main:

```bash
git checkout main
echo "test" >> test.txt
git add test.txt
git commit -m "Direct push test"
git push origin main
# Should be rejected
```

**Exercise 2 - Add a merge check**

1. Edit the restriction for `main`
2. Under **Merge checks**, enable **Minimum approvals: 1**
3. Create a pull request from a feature branch to main
4. Observe that the merge button shows a warning about required approvals

**Exercise 3 - Explore default reviewers**

1. Go to **Repository settings** > **Default reviewers**
2. Click **Add default reviewer rule**
3. Set the target branch to `main`
4. Add yourself as a default reviewer
5. Create a new pull request to main
6. Observe that you are automatically added as a reviewer

**Exercise 4 - Test force push protection**

1. Edit the `main` branch restriction
2. Enable **Prevent rewriting history (no force pushes)**
3. Try a force push:

```bash
git checkout main
git commit --amend -m "Amended commit" --no-edit
git push --force origin main
# Should be rejected
```

---

## Common Mistakes

**Setting restrictions without testing them**

After setting up branch permissions, test them with a real PR before relying on them for production workflows. Some settings (like "minimum approvals") behave differently on Free vs Premium plans.

**Forgetting to restrict force pushes**

Many teams remember to restrict direct pushes but forget to restrict force pushes. A force push can overwrite the branch history even when direct pushes are blocked. Always enable the "Prevent rewriting history" restriction on protected branches.

**Not enabling "author cannot approve" when requiring minimum approvals**

If you require 2 approvals but do not prevent the author from approving their own PR, the author can approve to reduce the requirement to 1 from others. Always pair minimum approval counts with the author approval restriction.

**Relying on advisory checks on the Free plan**

On Free and Standard plans, merge checks are advisory. They show warnings but do not prevent merging. If you need enforced merge checks, you need the Premium plan. Do not rely on advisory checks for critical quality gates.

**Applying overly strict restrictions to feature branches**

Feature branches are personal working areas. Requiring approvals or builds to pass on `feature/*` branches slows development unnecessarily. Save restrictions for the branches that represent shared state: `main`, `develop`, `release/*`.

**Not updating restrictions when the team changes**

If a default reviewer leaves the team or changes role, update the default reviewer rules. A departed reviewer on a default reviewer rule who is required to approve creates a permanent blocker - no PRs can merge until the rule is updated.

---

## Summary

Branch permissions protect important branches through two complementary mechanisms: restrictions (who can push, delete or force-push) and merge checks (conditions that must be met before a pull request can be merged).

Restrictions are configured in **Repository settings** > **Branch permissions**. They use glob patterns to match branch names and can be stacked (multiple restrictions applying to the same branch). Key restrictions: write access (no direct pushes to main), delete restriction (prevent accidental deletion), force push restriction (protect history).

Merge checks require: minimum approvals, no unresolved tasks, passing builds and optionally reset approvals when new commits are pushed. On Free and Standard plans these are advisory warnings; on Premium they are server-enforced blocks.

Default reviewers auto-assign team members to PRs targeting specific branches, ensuring the right people always review important changes.

Combine restrictions and default reviewers to build a complete workflow: no direct pushes to `main`, 2 approvals required including the lead developer, all builds passing, no unresolved tasks. This is the configuration that makes code review policies real rather than aspirational.

---

## Sources

- [Atlassian: Branch permissions](https://support.atlassian.com/bitbucket-cloud/docs/use-branch-permissions/)
- [Atlassian: Default reviewers](https://support.atlassian.com/bitbucket-cloud/docs/set-up-default-reviewers/)
- [Atlassian: Merge checks](https://support.atlassian.com/bitbucket-cloud/docs/suggest-or-require-checks-before-a-merge/)
- [Atlassian: Commit status API](https://developer.atlassian.com/cloud/bitbucket/rest/api-group-commit-statuses/)
- [Atlassian: Protected branches overview](https://support.atlassian.com/bitbucket-cloud/docs/use-branch-permissions/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

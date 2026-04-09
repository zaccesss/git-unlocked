# Branch Policies in Azure Repos

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Branch policies in Azure Repos enforce quality and process requirements on specific branches. They prevent direct pushes to important branches, require pull requests, mandate build validation before merges, control who must approve and enforce comment resolution. Branch policies are the enforcement layer that turns your team's code review guidelines from suggestions into requirements.

This file covers every branch policy type available in Azure Repos, how to configure them, how they interact with pull requests, how to manage bypass permissions and the common policy combinations used by professional teams.

---

## Table of Contents

1. [How branch policies work](#how-branch-policies-work)
2. [Accessing branch policy settings](#accessing-branch-policy-settings)
3. [Require a minimum number of reviewers](#require-a-minimum-number-of-reviewers)
4. [Check for linked work items](#check-for-linked-work-items)
5. [Check for comment resolution](#check-for-comment-resolution)
6. [Limit merge types](#limit-merge-types)
7. [Build validation](#build-validation)
8. [Status checks](#status-checks)
9. [Automatically include reviewers](#automatically-include-reviewers)
10. [Branch locks](#branch-locks)
11. [Cross-repository policies](#cross-repository-policies)
12. [Bypassing policies](#bypassing-policies)
13. [Policy combinations for common scenarios](#policy-combinations-for-common-scenarios)
14. [Try It Yourself](#try-it-yourself)
15. [Common Mistakes](#common-mistakes)
16. [Summary](#summary)
17. [Sources](#sources)

---

## How branch policies work

When one or more branch policies are configured on a branch:

1. **Direct pushes are blocked** - you cannot `git push` directly to a policy-protected branch. All changes must go through a pull request.
2. **The pull request Complete button is disabled** until all active policies are satisfied.
3. **Policy bypass requires elevated permissions** - even administrators cannot bypass policies without the explicit "Bypass policies when completing pull requests" permission.

Policies are **additive** - you can enable multiple policies on the same branch and all must be satisfied before the PR can be completed.

Branch policies in Azure Repos are enforced server-side, not just as UI hints. If you attempt a direct push to a policy-protected branch, the server rejects the push:

```
remote: TF402455: Pushes to this branch are not permitted; you must use a pull request to update this branch.
```

---

## Accessing branch policy settings

Branch policies are configured at the repository or branch level.

### For a specific branch

1. Go to **Project settings** > **Repositories**
2. Select the repository
3. Click the **Policies** tab
4. Under **Branch policies**, find the branch (or click **+** to add a new branch)
5. Click the branch name to configure its policies

Or from within Repos:
1. Go to **Repos** > **Branches**
2. Find the branch
3. Click the **...** menu > **Branch policies**

### For all branches matching a pattern

1. Go to **Project settings** > **Repositories** > select a repository > **Policies**
2. Under **Branch policies**, click **+**
3. Enter a branch name pattern using wildcards: `release/*`, `feature/*`, `*` (all branches)

Wildcard policies apply to any branch matching the pattern. A policy on `*` applies to all branches. A policy on `release/*` applies to all branches starting with `release/`.

---

## Require a minimum number of reviewers

This policy requires a pull request to have at least N approvals from reviewers before it can be completed.

### Configuration options

**Minimum number of reviewers**: The count of required approvals. Common values: 1 (one peer review required) or 2 (two reviewers for critical branches).

**Allow requestors to approve their own changes**: When disabled (recommended), the pull request author's approval does not count toward the minimum. If enabled, a developer can approve their own PR - defeating the purpose of the review requirement.

**Prohibit the most recent pusher from approving their own changes**: If a reviewer approves a PR and then the author pushes new commits, this option prevents the most recent pusher from counting as an approver. Prevents approving changes you just pushed.

**Reset all approval votes when there are new pushes**: When enabled, every new commit pushed to the PR resets all existing approvals. Reviewers must re-approve after any code change. Prevents the pattern: get approvals, then push additional (unreviewed) changes, then merge.

**Reset approval votes when there are new pushes (only for non-opt-out voters)**: A softer version - approvals from reviewers who explicitly opted out of resetting are preserved. For reviewers who trust the author to make small changes without re-review.

**Require approval from each team member**: When enabled, every member of the team must approve. For very small, high-trust teams doing mob review.

### Example configuration for a production branch

```
Minimum number of reviewers: 2
Allow requestors to approve their own changes: Off
Prohibit the most recent pusher from approving: On
Reset approval votes on new pushes: On
```

This configuration means: two different people must review the code, neither of which is the author, and both must have reviewed the final state of the code (no late pushes after approval).

---

## Check for linked work items

This policy requires that a pull request links to at least one Azure Boards work item before it can be completed.

### Why this matters

Linking PRs to work items creates traceability. Six months later, you can look at a commit and see exactly what requirement, bug or task it was implementing. You can look at a work item and see what code was written for it.

### Configuration

**Required**: The PR cannot be completed without a linked work item.

**Optional**: The policy generates a warning but does not block completion. Useful as a gentle reminder rather than a hard requirement.

### Linking a work item to a PR

In the pull request, click **+ Add a work item** in the **Work Items** section. Type `#` followed by the work item ID or keywords to search.

Alternatively, mention the work item ID in your commit message with `AB#ID` syntax: `git commit -m "Fix null reference exception AB#1234"`. Azure DevOps automatically links the commit (and its PR) to work item 1234.

---

## Check for comment resolution

This policy prevents completing a pull request while any comments are marked as **Active** (unresolved).

### How it works

Every comment in a PR starts as Active. When the author addresses the feedback (makes the change or explains why they disagree), they resolve the comment. If any comments remain Active when someone tries to complete the PR, the Complete button is blocked.

### Configuration

**Required**: All comments must be resolved.

**Optional**: Warning but not blocking.

### The resolution workflow

1. Reviewer adds a comment
2. Author reads the comment and addresses it (code change, explanation or disagreement)
3. Either the reviewer or the author marks the comment as **Resolved**
4. Or the reviewer marks it **Won't Fix** (the comment is acknowledged but not addressed)
5. Resolved and Won't Fix comments do not block PR completion

### Why this matters

Without this policy, comments can be ignored. The author can acknowledge the feedback by pressing Complete before addressing any of it. This policy ensures every review comment receives a response.

---

## Limit merge types

This policy restricts which merge strategies can be used when completing pull requests. This enforces consistent history management across the team.

### Merge strategies

**Basic merge (no fast-forward)**: Creates a merge commit. Full branch history preserved. The merge commit shows when the feature branch was integrated.

**Squash merge**: All commits from the PR are squashed into a single commit on the target branch. Clean linear history. Individual commit history from the branch is lost.

**Rebase and fast-forward**: The PR's commits are replayed on top of the target branch. Linear history without a merge commit.

**Rebase with merge commit**: Commits are rebased onto the target branch and then a merge commit is created.

### Choosing a strategy

| Strategy | Best for |
|---|---|
| Merge commit | Teams that value full branch history and clear merge points |
| Squash | Teams that want clean, linear history (one commit per feature) |
| Rebase | Teams that value linear history with all individual commits preserved |

Most teams choose **either merge commit or squash** and stick to it consistently. Allowing all merge types leads to inconsistent history.

### Example: Squash-only policy

Enable only "Squash merge". Disable all others. Every PR creates exactly one commit on the target branch regardless of how many commits were in the PR. `git log` on main reads cleanly: one commit per feature.

---

## Build validation

Build validation runs a pipeline automatically when a PR is created or updated, and uses the pipeline result as a policy requirement.

### Configuration

1. In branch policy settings, under **Build validation**, click **+**
2. Select the **Build pipeline** to run
3. Set the **trigger**:
   - **Automatic (whenever the source branch is updated)**: runs on every commit pushed to the PR
   - **Manual**: a reviewer must manually trigger the build
4. Set the **policy requirement**:
   - **Required**: the PR cannot be completed if the build fails or is not run
   - **Optional**: failure generates a warning but does not block
5. Set the **build expiration**:
   - **Immediately**: any new commit invalidates the build result (must rerun)
   - **After N hours**: the build result is valid for N hours even if new commits are pushed
   - **Never**: the build result is valid until the PR is completed (dangerous - allows stale results)

### What build validation does

When a PR triggers build validation:
1. Azure Pipelines creates a temporary merge of the PR's source branch into the target branch
2. The pipeline runs against this merge result
3. If the pipeline passes, the policy is satisfied
4. If the pipeline fails, the PR cannot be merged until the failure is fixed
5. The pipeline result appears on the PR as a status check

This is the critical integration between Azure Repos and Azure Pipelines. It ensures that code is tested not just on the feature branch in isolation, but as it will actually appear after merging.

### Multiple build validations

You can add multiple build validations to a branch. For example:
- **Required**: Unit tests pipeline (fast, must always pass)
- **Required**: Integration tests pipeline (slower, must pass)
- **Optional**: Performance benchmarks (informational, not blocking)

---

## Status checks

Beyond build validation (which is tied to Azure Pipelines), **status checks** allow any external service to post a pass/fail status to a PR and have it enforced as a policy.

### How status checks work

An external service (a third-party CI system, a security scanner, a code quality tool) posts a status to the Azure DevOps REST API:

```
POST https://dev.azure.com/{org}/{project}/_apis/git/repositories/{repoId}/commits/{commitId}/statuses
```

With a status object containing:
- `state`: `succeeded`, `failed`, `pending`, `notSet`
- `genre`: the category of the check (e.g. "security", "quality")
- `name`: the specific check name
- `targetUrl`: a link to the detailed results

### Configuring status check policies

1. In branch policy settings, under **Status checks**, click **+**
2. Enter the `genre` and `name` of the expected status
3. Set as required or optional

When configured, the PR completion is blocked until the specified external service posts a `succeeded` status.

---

## Automatically include reviewers

This policy automatically adds specific users or groups as reviewers on every PR targeting the branch.

### Configuration

1. In branch policy settings, under **Automatically included reviewers**, click **+**
2. Add the users or groups
3. Configure their requirement:
   - **Required**: these reviewers must approve before the PR can be completed
   - **Optional**: they are notified and added but their approval is not required
4. Set **minimum reviewer count** specifically for this group (separate from the overall minimum)

### Use cases

**Security team review**: Add the security team as required reviewers for PRs targeting the production branch. Every production change gets security eyes.

**Tech lead sign-off**: Add the tech lead as a required reviewer for PRs to the main branch.

**CODEOWNERS equivalent**: While Azure Repos does not have a CODEOWNERS file with automatic per-path enforcement, you can simulate it with file filter policies (see below) or manually configure reviewers per team.

### File filter for automatic reviewer inclusion

When adding automatic reviewers, you can optionally add file path filters. The reviewer is only added if the PR touches files matching the filter:

- `src/backend/**` - adds the backend team lead as reviewer only if backend files changed
- `infrastructure/**` - adds the DevOps team as reviewer only if infrastructure code changed
- `*.sql` - adds the DBA team if any SQL files changed

---

## Branch locks

**Branch locks** prevent anyone from pushing to the branch, including administrators. Unlike branch policies (which redirect you to use PRs), a lock blocks all pushes.

### Locking a branch

1. Go to **Repos** > **Branches**
2. Find the branch
3. Click the **...** menu > **Lock**

Or via the API/CLI.

### Use cases

- **Pre-release freeze**: lock the release branch while the final build is being tested
- **Archive**: lock an old release branch to prevent accidental changes
- **Incident response**: lock main while investigating a production incident

### Unlocking

The same way you locked it - click the lock icon again or use the **...** menu > **Unlock**.

---

## Cross-repository policies

**Cross-repository policies** apply the same set of policies to all repositories in a project simultaneously, rather than configuring them per repository.

### Configuring cross-repository policies

1. Go to **Project settings** > **Repositories**
2. Click **Policies** at the top (not for a specific repository)
3. Configure policies here - they apply to all repositories in the project for the matching branch pattern

This is useful for enforcing consistent standards across many repositories: "all repositories in this project must have build validation on the main branch."

---

## Bypassing policies

Sometimes urgent situations require bypassing policies - a critical hotfix that must deploy immediately, or a repository administrator doing emergency maintenance.

### Bypass permissions

The permission "Bypass policies when completing pull requests" allows a user to complete a PR even when policies are not satisfied. It also allows direct pushes to policy-protected branches.

To grant bypass permission:
1. Go to **Project settings** > **Repositories** > select a repository > **Security**
2. Find the user or group
3. Set "Bypass policies when completing pull requests" to **Allow**

> [!WARNING]
> Granting bypass permission widely defeats the purpose of branch policies. Limit bypass permissions to project administrators and document that it is for emergency use only. Every bypass should be reviewed in the audit log.

### Bypassing in practice

When a user with bypass permission completes a PR that fails policies, Azure DevOps:
1. Records the bypass in the audit log
2. Adds a note to the PR that policies were bypassed
3. Identifies which policies were not satisfied at the time of completion

The bypass is tracked and visible - it is an emergency valve, not a silent override.

---

## Policy combinations for common scenarios

### Simple team (2-5 developers, no compliance requirements)

```
Require minimum reviewers: 1
Allow requestors to approve own changes: Off
Check for comment resolution: Required
Build validation: Required (main test pipeline)
Merge types: Squash only
```

### Mid-size team with Jira/Boards integration

```
Require minimum reviewers: 2
Allow requestors to approve own changes: Off
Prohibit most recent pusher from approving: On
Reset approvals on new pushes: On
Check for linked work items: Required
Check for comment resolution: Required
Build validation: Required (unit tests pipeline)
Build validation: Required (integration tests pipeline)
Merge types: Squash only
```

### Enterprise / regulated environment

```
Require minimum reviewers: 2
Allow requestors to approve own changes: Off
Prohibit most recent pusher from approving: On
Reset approvals on new pushes: On
Check for linked work items: Required
Check for comment resolution: Required
Build validation: Required (full test suite)
Build validation: Required (security scan pipeline)
Status checks: Required (SAST tool result)
Automatically include reviewers: Security team (required)
Automatically include reviewers: Tech lead (required)
Limit merge types: Squash only
```

### Release branch (additional protection)

```
All of the above plus:
Require minimum reviewers: 3
Automatically include reviewers: Release manager (required)
Branch lock: enabled during release freeze window
```

---

## Try It Yourself

**Exercise 1 - Enable reviewer requirements on main**

1. Go to **Project settings** > **Repositories** > your repository > **Policies**
2. Find the `main` branch policy section
3. Click **+** next to **Branch Policies**
4. Enable **Require a minimum number of reviewers**: set to 1, disable "allow requestors to approve own"
5. Save

Now try to push directly to main:
```bash
git checkout main
echo "test" >> test.txt
git add test.txt
git commit -m "Direct push test"
git push origin main
# Should be rejected with TF402455
```

**Exercise 2 - Add build validation**

1. Create a simple pipeline (`azure-pipelines-pr.yml`):
```yaml
trigger: none
pr:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - script: echo "PR build succeeded"
    displayName: 'PR validation'
```

2. Commit and push this file to a branch and create a PR
3. Go to branch policies for `main`
4. Add Build validation: select your pipeline, set to Required
5. Create a new PR targeting main
6. Watch the build validation run automatically

**Exercise 3 - Add comment resolution requirement**

1. Enable **Check for comment resolution**: Required on `main`
2. Open a pull request
3. Add a comment on the diff
4. Try to complete the PR - the Complete button should reference the unresolved comment
5. Resolve the comment
6. Complete the PR now works

**Exercise 4 - Test automatic reviewer inclusion**

1. Under **Automatically included reviewers**, click **+**
2. Add yourself as a required reviewer with an email notification
3. Create a new PR targeting main
4. You should be automatically added as a required reviewer

---

## Common Mistakes

**Enabling branch policies without communicating to the team**

Branch policies change how developers work. A developer who does not know that direct pushes are now blocked gets a confusing error. Announce policy changes before enabling them and explain the new workflow.

**Not resetting approvals on new pushes**

Without resetting approvals, a developer can: get approvals, push more code changes, then immediately complete the PR. The reviewers approved the old code, not the final state. Always enable approval reset for any branch that requires review.

**Allowing requestors to approve their own changes**

This effectively allows a single developer to self-merge. Even with a minimum reviewer count of 2, the author can approve and one other person can approve. Disable this option for any branch where independent review is the point.

**Setting bypass permissions too broadly**

If every developer has bypass permission, branch policies are meaningless. Reserve bypass for project administrators and document its use. Review the audit log regularly to catch unnecessary bypasses.

**Using "Never" for build validation expiry**

Setting build validation to never expire means a build result from 2 weeks ago can be used to satisfy the policy when completing the PR. During those 2 weeks, the target branch may have received dozens of commits. Always use "Immediately" or a reasonable time window (e.g. 24 hours).

**Not testing policies before a team depends on them**

Test every policy in a non-critical repository before applying to production repositories. Make sure you understand the exact behaviour before it affects your team's workflow.

---

## Summary

Branch policies in Azure Repos enforce quality gates on protected branches. When any policy is configured on a branch, direct pushes are blocked and all changes must go through pull requests. The PR Complete button is disabled until all required policies are satisfied.

The key policies are: minimum reviewer count (with options for resetting on new pushes and prohibiting author self-approval), linked work items, comment resolution, merge type restrictions and build validation.

Build validation runs a pipeline automatically on every PR update and uses the result as a policy requirement - the core integration between Azure Repos and Azure Pipelines for enforcing quality before merge.

Automatically included reviewers add specific people or teams to every PR targeting a branch, optionally filtered by which files changed. Status checks extend policy enforcement to external tools and services.

Bypass permissions allow emergency overrides but every bypass is logged. Limit bypass access to administrators and treat it as an audit event.

---

## Sources

- [Microsoft Learn: Branch policies](https://learn.microsoft.com/azure/devops/repos/git/branch-policies)
- [Microsoft Learn: Require a minimum number of reviewers](https://learn.microsoft.com/azure/devops/repos/git/branch-policies#require-a-minimum-number-of-reviewers)
- [Microsoft Learn: Build validation](https://learn.microsoft.com/azure/devops/repos/git/branch-policies#build-validation)
- [Microsoft Learn: Automatically include reviewers](https://learn.microsoft.com/azure/devops/repos/git/branch-policies#automatically-include-code-reviewers)
- [Microsoft Learn: Cross-repository policies](https://learn.microsoft.com/azure/devops/repos/git/branch-policies#cross-repo-policies)
- [Microsoft Learn: Bypass branch policies](https://learn.microsoft.com/azure/devops/repos/git/branch-policies#bypass-branch-policies)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

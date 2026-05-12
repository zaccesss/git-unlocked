# Pull Requests in Bitbucket

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

A pull request (PR) is a proposal to merge code from one branch into another. It is the mechanism through which code review happens, team feedback is gathered and quality gates are enforced before changes reach your main branch. In Bitbucket, pull requests are tightly integrated with branch permissions, Pipelines build status, Jira issues and the default reviewer system - making them the central workflow event in professional development with Bitbucket.

This file covers the full pull request lifecycle: creating them, reviewing them, managing feedback, choosing the right merge strategy, automating reviewer assignment and writing PR templates that capture the information reviewers need.

---

## Table of Contents

1. [What pull requests are for](#what-pull-requests-are-for)
2. [Creating a pull request](#creating-a-pull-request)
3. [The pull request interface](#the-pull-request-interface)
4. [Reviewing a pull request](#reviewing-a-pull-request)
5. [Inline comments and threads](#inline-comments-and-threads)
6. [Pull request approvals](#pull-request-approvals)
7. [Merge strategies](#merge-strategies)
8. [Default reviewers](#default-reviewers)
9. [Pull request templates](#pull-request-templates)
10. [Linking pull requests to Jira issues](#linking-pull-requests-to-jira-issues)
11. [Build status and merge checks](#build-status-and-merge-checks)
12. [Draft pull requests](#draft-pull-requests)
13. [Closing and declining pull requests](#closing-and-declining-pull-requests)
14. [Pull request notifications](#pull-request-notifications)
15. [Try It Yourself](#try-it-yourself)
16. [Common Mistakes](#common-mistakes)
17. [Summary](#summary)
18. [Sources](#sources)

---

## What pull requests are for

A pull request does several things simultaneously:

**It communicates intent.** The title and description explain what the change is, why it was made and how to test it. This context is preserved in the repository's history long after the code is merged.

**It enables code review.** Team members read the diff, leave comments, ask questions and suggest improvements before the code becomes part of the shared codebase. This catches bugs, spreads knowledge and maintains code quality standards.

**It enforces gates.** Through branch permissions and merge checks, a PR can be blocked from merging until specific conditions are met: a minimum number of approvals, passing CI/CD builds, no unresolved comments, or specific named reviewers having signed off.

**It creates a traceable audit trail.** Every comment, approval, build result and merge event is recorded against the PR. Six months later you can read the PR and understand why a particular decision was made, who approved it and whether there was debate about the approach.

---

## Creating a pull request

### From the Bitbucket web interface

**Method 1 - from the branch**

1. Navigate to your repository
2. Click **Branches** in the left sidebar
3. Find your branch
4. Click **Create pull request** next to it

**Method 2 - from the pull request list**

1. Navigate to your repository
2. Click **Pull requests** in the left sidebar
3. Click **Create pull request** in the top right

**Method 3 - after a push**

When you push a new branch or new commits to an existing branch, Bitbucket shows a banner on the repository page suggesting you create a pull request. Click the banner.

### Filling in the PR form

**Source** - the branch containing your changes. Select the branch you want to merge.

**Destination** - the branch you want to merge into. Defaults to the repository's default branch (`main`). Change this for PRs targeting feature branches or release branches.

**Title** - a concise summary of what the change does. Good PR titles are specific: "Fix login redirect loop for OAuth users" rather than "Fix bug". The title appears in commit history and in Jira's development panel if linked.

**Description** - the context and details reviewers need. At minimum:

- What does this change do?
- Why is this change needed?
- How can reviewers test it?
- Are there any risks or side effects?

**Reviewers** - add the team members you want to review this PR. They receive a notification and must approve before merging (depending on branch permission settings). Default reviewers are added automatically if configured.

**Close source branch** - tick this to automatically delete the source branch after the PR is merged. Useful for keeping the branch list tidy. Safe to enable for short-lived feature branches; do not enable for branches meant to persist (like `develop` or `release`).

### Creating a PR from the command line

Bitbucket does not have an official CLI tool, but you can open the PR creation page directly from the terminal output after a push. When you push a branch to Bitbucket, the output includes a URL:

```
remote:
remote: Create pull request for my-feature:
remote:   https://bitbucket.org/workspace/repo/pull-requests/new?source=my-feature
remote:
```

Copy that URL and open it in your browser to jump directly to the PR creation form.

---

## The pull request interface

Once created, a PR has several tabs and panels:

### Overview tab

The default view. Shows:

- PR title, description and metadata
- Reviewer list and their approval status
- Build status from Bitbucket Pipelines
- Activity feed: all comments, commits, approvals and status changes in chronological order
- The Jira issue link (if the branch name or PR title contains a Jira issue key)

### Commits tab

A list of all commits included in this PR. Each commit links to its diff. This view is useful for understanding the individual commit history if the PR contains many commits with meaningful messages.

### Diff tab

The full diff of all changes in the PR. This is where most code review happens:

- Green lines were added
- Red lines were removed
- Unchanged lines provide context
- File-level comments appear at the top of each file
- Line-level comments appear inline at the relevant line

You can switch between **unified diff** (additions and deletions in one view) and **side-by-side diff** (old code on the left, new code on the right) using the view toggle.

### Files changed

A file tree showing which files were modified, added or deleted. Click any file to jump to its diff. Files can be marked as reviewed to track progress through a large PR.

---

## Reviewing a pull request

### Starting a review

1. Open the pull request
2. Go to the **Diff** tab
3. Read through the changes file by file

There is no formal "start review" step in Bitbucket - you comment and approve directly. This differs from GitHub's review model where you submit all comments together at the end.

### Reading the diff effectively

Focus on:

- **Logic and correctness** - does the code do what the PR description says it does?
- **Edge cases** - does the code handle unusual inputs, empty values, concurrent access?
- **Security** - is any user input sanitised? Are any secrets or credentials hardcoded?
- **Performance** - are there obvious inefficiencies (N+1 queries, unnecessary loops)?
- **Tests** - are there tests for the new behaviour? Do existing tests still pass?
- **Code style** - does the code follow the team's conventions and readability standards?

---

## Inline comments and threads

### Adding a line comment

1. Hover over any line in the diff
2. A comment icon (+) appears on the left
3. Click it to open a comment box
4. Write your comment
5. Click **Save**

Line comments appear in the diff at the exact line they reference. They also appear in the PR's activity feed.

### Adding a file comment

Click the comment icon at the top of a file's diff section to add a comment about the file as a whole rather than a specific line.

### Comment formatting

Bitbucket comments support Markdown:

````markdown
**This is bold** and _this is italic_

Code inline: `const x = 5`

Code block:

```javascript
function greet(name) {
  return `Hello, ${name}`;
}
```
````

- List item one
- List item two

```

### Resolving comment threads

When a reviewer leaves a comment and the author addresses it (by making a change or explaining why no change is needed), either party can mark the thread as **Resolved**. Resolved threads collapse in the UI but remain visible if you need to reference them.

Whether all threads must be resolved before merging is controlled by branch permission settings. Enabling **No unresolved tasks** as a merge check prevents merging while open threads exist.

### Tasks

Comments can be converted to **tasks** - specific action items for the author. A task is a checkbox that can be ticked when completed. Tasks are visible in the PR overview and can be used as a merge check requirement.

To create a task from a comment, click the task checkbox icon when writing or editing a comment. Tasks can also be created directly from the PR overview without being tied to a specific line.

---

## Pull request approvals

### Approving a pull request

After reviewing the changes:
1. Click the **Approve** button at the top of the PR (or in the reviewer list)
2. Optionally leave a summary comment

Approvals are shown in the reviewer list with a green checkmark. An approval signals that you have reviewed the code and consider it ready to merge.

### Requesting changes

If the code needs work before it can be approved, leave comments explaining what needs to change. In Bitbucket, there is no formal "Request changes" status like GitHub's - you leave comments and do not approve. The author sees the unresolved comments and the absent approval.

### Revoking an approval

If new commits are pushed to the PR after you approved, you may want to re-review. You can revoke your approval:
1. Find your name in the reviewer list
2. Click the dropdown next to your approval status
3. Select **Revoke approval**

Branch permissions can be configured to automatically revoke approvals when new commits are pushed (see branch permissions in file 07). This prevents a pattern where code is approved, more changes are added and the PR is merged without the new code being reviewed.

### Required approvals

The minimum number of approvals required before merging is configured in **branch permissions** (repository settings > Branch permissions). A branch permission targeting `main` might require:
- At least 2 approvals
- The author is not allowed to approve their own PR
- Approvals are reset when new commits are pushed

This is covered in detail in [07-branch-permissions.md](07-branch-permissions.md).

---

## Merge strategies

When a PR is ready to merge, Bitbucket offers up to three strategies depending on which are enabled in repository settings.

### Merge commit

```

Before merge:
main: A --- B --- C
feature: D --- E --- F

After merge commit:
main: A --- B --- C --- M (merge commit)
\ /
feature: D --- E --- F

```

A merge commit preserves the complete branch history. The merge commit has two parents - the tip of the destination branch and the tip of the source branch. The full context of when the feature branch diverged and when it was merged is visible.

**When to use:** When you want to preserve the full context of feature development. Good for long-running feature branches where the individual commits tell a meaningful story.

**Disadvantage:** `git log` on main becomes harder to read as it contains the full branching and merging history. The `git log --graph` view becomes complex on large projects.

### Squash merge

```

Before squash:
main: A --- B --- C
feature: D --- E --- F

After squash:
main: A --- B --- C --- S (squashed commit, contains D+E+F combined)

```

All commits from the feature branch are combined into a single commit on the destination branch. The feature branch's individual commits disappear from the main history. The new squashed commit has a single parent - the previous tip of main.

**When to use:** When the individual commits on the feature branch are messy, represent work-in-progress steps or have unclear messages. Teams with a culture of clean linear history often enforce squash merges.

**Advantage:** `git log` on main reads clearly - one commit per feature.

**Disadvantage:** Granular commit history from the feature branch is lost. If the squashed commit introduces a bug, `git bisect` cannot narrow it down to a specific step within the original feature branch.

### Fast-forward merge

```

Before fast-forward:
main: A --- B --- C
feature: D --- E --- F

After fast-forward (only possible if feature is directly ahead of main):
main: A --- B --- C --- D --- E --- F

```

A fast-forward merge is only possible when the destination branch has not moved since the feature branch was created. Git simply moves the destination branch pointer forward to where the feature branch is - no new commit is created.

**When to use:** For very short-lived feature branches on a team with discipline about rebasing. Produces the cleanest possible history.

**Disadvantage:** Only works if there has been no divergence. If main has new commits since the feature branch was created, the feature branch must be rebased before a fast-forward merge is possible.

### Choosing a strategy

| Scenario | Recommended strategy |
|---|---|
| Large feature with meaningful commit history | Merge commit |
| Small fix with messy commits | Squash |
| Team enforces linear history | Squash or fast-forward |
| Automated release from PRs | Squash (clean changelog) |
| Open source contribution review | Merge commit (preserve attribution) |

You can restrict which strategies are available in repository settings > **Merging strategies**. Enforcing squash-only across all PRs creates consistent, clean history without relying on individual contributors to make the right choice.

---

## Default reviewers

Default reviewers are automatically added to pull requests targeting specific branches. This ensures the right people always review the right code without the PR author having to remember who to add.

### Configuring default reviewers

1. Go to **Repository settings** > **Default reviewers**
2. Click **Add default reviewer rule**
3. Set the target branch (supports exact names or patterns like `release/*`)
4. Add the users who should automatically be reviewers for PRs to that branch
5. Optionally set the minimum number of approvals required from the default reviewers

### Example configurations

**Require senior review for main:**
- Target branch: `main`
- Default reviewers: [senior-developer, tech-lead]
- Minimum approvals: 1

**Require security review for release branches:**
- Target branch: `release/*`
- Default reviewers: [security-team-lead]
- Minimum approvals: 1

**Require team review for any branch:**
- Target branch: `**` (all branches)
- Default reviewers: [team-member-1, team-member-2]
- Minimum approvals: 1

### Default reviewers vs CODEOWNERS

GitHub and GitLab use CODEOWNERS files to assign reviewers based on file paths - the right person is assigned based on which files changed. Bitbucket's default reviewers work at the branch level, not the file level. This is simpler but less granular.

For file-level ownership, some teams combine Bitbucket's default reviewers (for branch-level gatekeeping) with a documented CODEOWNERS file (as a convention rather than an enforced system) and manual review assignment for specific file changes.

---

## Pull request templates

A PR template is a pre-filled description that appears when a new pull request is created. It prompts the author to include specific information, resulting in more consistent, useful PR descriptions.

### Creating a PR template

Create a file at this exact path in your repository:

```

.bitbucket/pull-request-description-template.md

````

Example template:

```markdown
## What does this PR do?

<!-- Describe the changes in this PR. What problem does it solve? -->

## How has this been tested?

<!-- Describe how you tested these changes. Include any relevant test steps. -->

## Checklist

- [ ] I have added/updated unit tests
- [ ] I have updated documentation if relevant
- [ ] I have checked for security implications
- [ ] I have tested on the staging environment

## Related Jira issues

<!-- Add issue keys: PROJ-123, PROJ-456 -->

## Screenshots (if relevant)

<!-- Attach before/after screenshots for UI changes -->
````

### Template best practices

**Keep it short enough to be useful.** A template with 20 sections that nobody fills in is worse than no template. Start with 3-5 key questions.

**Use comments for instructions.** HTML comments (`<!-- like this -->`) appear in the editing interface but not when the description is rendered. Use them to guide authors without cluttering the final PR description.

**Include a checklist.** Checkboxes prompt authors to confirm important steps before submitting. They are also useful for reviewers to see at a glance whether the author has covered the basics.

**Add a Jira section.** Even if Bitbucket auto-links Jira issues from the branch name, explicitly listing related issues in the description helps reviewers who may not be familiar with the Jira project.

### Multiple templates

Bitbucket supports only one PR template per repository. For teams that work on different types of PRs (features, hotfixes, dependency updates) with different review requirements, include sections for all types with instructions for the author to delete what is not relevant.

---

## Linking pull requests to Jira issues

The Jira integration is one of Bitbucket's strongest features, and pull requests are central to it.

### Automatic linking via branch name

If your branch name contains a Jira issue key, Bitbucket automatically links the PR to that Jira issue. The link appears in:

- The PR overview in Bitbucket
- The Development panel on the Jira issue
- Jira's sprint board (the code icon appears on the issue card)

Branch naming convention that triggers automatic linking:

```
feature/PROJ-123-user-authentication
bugfix/PROJ-456-fix-login-redirect
PROJ-789-update-dependencies
```

Any branch name containing a valid Jira issue key (project key in uppercase, hyphen, number) triggers the link.

### Linking via PR title or description

Mentioning a Jira issue key in the PR title or description also creates the link. This is useful if the branch name does not contain the issue key.

### What appears in Jira

Once linked, the Jira issue's Development panel shows:

- Branch: the branch name and status (open, merged)
- Commits: how many commits are in the PR, with links
- Pull request: the PR title, status (open, approved, merged, declined) and link
- Build: the Pipelines build status (pass, fail, in progress)

This information gives project managers, QA engineers and other non-developers full visibility into the development status of any Jira issue without needing to navigate Bitbucket directly.

### Smart commits from pull requests

When merging a PR, if the commit message or PR description contains smart commit keywords, Jira actions are triggered on merge. See [08-jira-integration.md](08-jira-integration.md) for the full smart commit reference.

---

## Build status and merge checks

### Bitbucket Pipelines integration

When a Bitbucket Pipeline is triggered by a push to the PR's source branch, the build status appears in the PR overview:

- A yellow spinner while the build is running
- A green checkmark if all steps pass
- A red X if any step fails

Reviewers can see at a glance whether the code passes automated tests. Merging can be blocked until the build passes using branch permissions (see [07-branch-permissions.md](07-branch-permissions.md)).

### External build statuses

Build results from external CI systems (Jenkins, CircleCI, Travis CI) can also be posted to a Bitbucket PR using the Bitbucket REST API. This allows teams using CI systems other than Bitbucket Pipelines to still display and enforce build status on PRs.

The API endpoint for posting build status:

```
POST /2.0/repositories/{workspace}/{repo_slug}/commit/{commit}/statuses/build
```

Most CI systems have Bitbucket plugins or steps that handle this automatically.

### Merge checks (Premium plan)

Branch permissions on the Premium plan can enforce **merge checks** - conditions that must be met before the Merge button becomes active:

- Minimum number of approvals
- No unresolved tasks or comments
- Successful build status
- No incomplete tasks
- Author cannot approve their own PR

Merge checks are enforced server-side - even workspace administrators cannot bypass them (unless they modify the branch permissions themselves). This makes them reliable quality gates rather than advisory suggestions.

---

## Draft pull requests

A **draft pull request** signals that the work is in progress and not ready for review. Drafts are visible in the PR list but clearly marked. Reviewers are notified when the PR is created but know not to do a full review yet.

### Creating a draft PR

When creating a PR, look for the **Mark as draft** option (or the dropdown arrow next to the Create button). Select **Create draft pull request**.

### When to use draft PRs

- You want early feedback on an approach before finishing the implementation
- You want the PR to exist in Bitbucket (and appear in Jira's development panel) while work continues
- You want to trigger CI/CD builds on the branch but are not ready for review
- You are working on a long-running feature and want to open the PR early to capture context

### Converting a draft to a ready PR

1. Open the draft PR
2. Click **Mark as ready for review** in the PR overview
3. Reviewers receive a notification that the PR is now ready

Draft PRs cannot be merged while they are in draft status. The Merge button is disabled.

---

## Closing and declining pull requests

### Declining a pull request

If a PR is not going to be merged (the approach was wrong, the feature is cancelled, a different PR superseded it), decline it:

1. Open the PR
2. Click the dropdown next to the Merge button
3. Select **Decline**
4. Enter a reason for declining (optional but considerate)

Declined PRs are not deleted - they remain in the PR list as closed. The branch is not automatically deleted when declining (use the Close source branch option or delete manually).

### Reopening a declined PR

Declined PRs can be reopened if the work becomes relevant again:

1. Find the declined PR (filter by "Declined" status in the PR list)
2. Click **Reopen**

### Closing a PR by merging the branch manually

If you merge the source branch into the target branch directly via the command line without using Bitbucket's merge button, Bitbucket detects the merge and automatically marks the PR as merged. This is not recommended for team workflows because it bypasses any configured merge checks, but it can be useful in recovery situations.

---

## Pull request notifications

Bitbucket sends email notifications and in-app notifications for PR activity. The events that trigger notifications are configurable.

### Default notification triggers

You receive notifications for:

- PRs where you are a reviewer
- PRs where you are the author (for approvals, comments and status changes)
- PRs where you have left a comment (for subsequent activity in those PRs)

### Configuring notifications

1. Click your avatar > **Personal settings**
2. Click **Email notifications**
3. Adjust which events trigger email notifications

For busy repositories, reduce notification noise by:

- Only receiving emails for PRs where you are explicitly a reviewer
- Disabling notifications for repos you watch passively
- Using Bitbucket's in-app notification center instead of email for lower-priority events

---

## Try It Yourself

**Exercise 1 - Create a pull request**

1. In your practice repository, create a new branch:

```bash
git checkout -b feature/add-contributing-guide
```

2. Create a file:

```bash
echo "# Contributing\n\nThank you for considering contributing to this project." > CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "Add contributing guide"
git push origin feature/add-contributing-guide
```

3. Click the Bitbucket URL shown in the push output to create a PR
4. Fill in a title and description
5. Submit the PR

**Exercise 2 - Review your own PR**

1. Open the PR you just created
2. Click the **Diff** tab
3. Hover over a line and click the comment icon
4. Leave a comment on the new file
5. Mark the comment as a task
6. Resolve the task

**Exercise 3 - Create a PR template**

1. In your repository, create the directory `.bitbucket/`
2. Create the file `.bitbucket/pull-request-description-template.md` with:

```markdown
## Summary

<!-- What does this PR change? -->

## Testing

<!-- How have you tested these changes? -->

## Checklist

- [ ] Tests added or updated
- [ ] Documentation updated if needed
```

3. Commit and push this file
4. Create a new PR to see the template pre-filled in the description

**Exercise 4 - Explore merge strategies**

1. Go to **Repository settings** > **Merging strategies**
2. Note which strategies are enabled by default
3. Try disabling merge commits and enabling squash only
4. Create a new PR and check which merge options are available

---

## Common Mistakes

**Not filling in the PR description**

An empty or one-line PR description saves the author seconds but costs reviewers minutes. Reviewers need context to review effectively. A good description reduces back-and-forth comments asking "what is this for?" Write the description as if explaining the change to a colleague who has not seen the code.

**Submitting a PR without checking if the tests pass first**

Push your branch, wait for the Bitbucket Pipeline to run, and check the result before asking colleagues to review. Asking for a code review on code that does not pass its own tests wastes everyone's time.

**Approving a PR without reading the diff**

An approval means you have reviewed the code and consider it ready to merge. Approving without reading is meaningless at best and negligent at worst if the PR contains a bug or security issue. If you do not have time to review, say so and do not approve.

**Not resolving your own comments**

After leaving a comment and the author addresses it (either by making the change or explaining why they did not), mark the thread resolved. Leaving dozens of resolved-in-practice but technically unresolved comment threads makes it hard to see which concerns are genuinely outstanding.

**Creating huge PRs**

A PR with 50 changed files and 2,000 line additions is very difficult to review effectively. Break large changes into smaller, sequenced PRs where possible. A general guideline: if a PR takes more than 30 minutes to review properly, consider whether it can be split.

**Merging without waiting for approvals**

If your team has configured required approvals, the merge button is blocked until conditions are met. But on free plans where branch permission merge checks are not enforced, it is tempting to merge without waiting for approvals. Respect the team's review process even when it is not technically enforced.

**Leaving the source branch after merging**

Enable **Close source branch** when merging, or manually delete the source branch afterwards. Stale merged branches accumulate and make the branch list messy. A branch that has been merged serves no ongoing purpose.

---

## Summary

Pull requests in Bitbucket are the central workflow event for team code collaboration. They combine code diffs, inline comments, approval tracking, build status and Jira issue linking in a single interface.

Create PRs with clear titles and descriptions. Add relevant reviewers (or configure default reviewers to do it automatically). Choose the merge strategy appropriate for your team's history philosophy: merge commit preserves full branch history, squash produces clean linear history, fast-forward requires rebasing but produces the simplest history.

Inline comments and tasks communicate specific feedback. Approvals signal that a reviewer has read the code and considers it ready. Build status from Bitbucket Pipelines provides automated quality feedback. Merge checks (Premium plan) enforce all of this server-side.

PR templates ensure consistent, useful descriptions. Jira linking makes the development status of any issue visible without leaving Jira. Draft PRs allow work-in-progress branches to exist in Bitbucket without triggering a full review prematurely.

---

## Sources

- [Atlassian: Create a pull request](https://support.atlassian.com/bitbucket-cloud/docs/create-a-pull-request/)
- [Atlassian: Review a pull request](https://support.atlassian.com/bitbucket-cloud/docs/review-a-pull-request/)
- [Atlassian: Default reviewers](https://support.atlassian.com/bitbucket-cloud/docs/set-up-default-reviewers/)
- [Atlassian: Pull request templates](https://support.atlassian.com/bitbucket-cloud/docs/add-pull-request-templates/)
- [Atlassian: Merge strategies](https://support.atlassian.com/bitbucket-cloud/docs/merge-a-pull-request/)
- [Atlassian: Draft pull requests](https://support.atlassian.com/bitbucket-cloud/docs/create-draft-pull-requests/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

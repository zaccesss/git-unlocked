# Jira Integration in Bitbucket

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

The Jira integration is Bitbucket's single greatest differentiator. Every major Git platform offers some form of Jira connection via API or third-party apps - but only Bitbucket, being made by the same company, delivers a native, deep integration that works bidirectionally without setup complexity. Code activity in Bitbucket automatically surfaces in Jira. Jira actions can be triggered from Git commit messages. Developers stay in their editor while project managers stay in their issue tracker, and both see the same connected picture of what is happening.

This file covers the complete Jira-Bitbucket integration: how to connect the two products, how the development panel works, smart commits and their full syntax, automating Jira transitions, linking branches and PRs to issues and using the integration effectively in real team workflows.

---

## Table of Contents

1. [How the integration works architecturally](#how-the-integration-works-architecturally)
2. [Connecting Bitbucket to Jira](#connecting-bitbucket-to-jira)
3. [The Jira development panel](#the-jira-development-panel)
4. [Smart commits](#smart-commits)
5. [Smart commit syntax reference](#smart-commit-syntax-reference)
6. [Creating branches from Jira](#creating-branches-from-jira)
7. [Linking pull requests to Jira issues](#linking-pull-requests-to-jira-issues)
8. [Build and deployment status in Jira](#build-and-deployment-status-in-jira)
9. [Jira automation triggered by Bitbucket events](#jira-automation-triggered-by-bitbucket-events)
10. [Jira Software sprint boards and code status](#jira-software-sprint-boards-and-code-status)
11. [Integration with Jira Service Management](#integration-with-jira-service-management)
12. [Troubleshooting the integration](#troubleshooting-the-integration)
13. [Try It Yourself](#try-it-yourself)
14. [Common Mistakes](#common-mistakes)
15. [Summary](#summary)
16. [Sources](#sources)

---

## How the integration works architecturally

Bitbucket and Jira are both Atlassian products that share the same identity layer (Atlassian accounts), the same organisation structure (Atlassian organisations containing multiple products), and the same underlying platform APIs. This shared foundation is what makes the integration deep rather than surface-level.

When you connect a Bitbucket workspace to a Jira site:

1. Bitbucket listens for events: commits, branch creation, pull request events, pipeline results
2. When an event contains a Jira issue key (e.g. `PROJ-123`), Bitbucket sends that event to Jira via the shared platform API
3. Jira records the development activity against the matching issue
4. The Jira development panel displays this activity without any additional polling or manual refresh

The issue key is the link between the two systems. An issue key consists of a project key (2-10 uppercase letters) followed by a hyphen and a number: `PROJ-123`, `BACKEND-456`, `AUTH-7`. This format is how Bitbucket identifies which Jira issue a commit, branch or PR relates to.

---

## Connecting Bitbucket to Jira

The connection between a Bitbucket workspace and a Jira site is configured at the workspace level, not the repository level. Once connected, all repositories in the workspace automatically participate in the integration.

### Connecting from Bitbucket

1. Go to your Bitbucket workspace
2. Click **Workspace settings** (gear icon) in the left sidebar
3. Under **Integrations**, click **Jira Software**
4. Click **Connect Jira Software**
5. Select the Jira site you want to connect to (must be in the same Atlassian organisation)
6. Click **Connect**

The connection requires that both the Bitbucket workspace and the Jira site belong to the same Atlassian organisation. If you have separate Atlassian accounts for Bitbucket and Jira, you will need to consolidate them or use the API-based connection instead.

### Connecting from Jira

The connection can also be initiated from Jira:

1. In Jira, go to **Project settings** or **Jira settings**
2. Find the **Bitbucket** or **Application Links** section
3. Add the Bitbucket workspace URL as a linked application

### Verifying the connection

After connecting, create a test commit with a Jira issue key in the message and check whether it appears in the Jira development panel within a few seconds. If it does, the integration is working.

---

## The Jira development panel

The Jira development panel is the most visible element of the integration. It appears on the right side of every Jira issue and shows all code activity associated with that issue.

### What the development panel shows

**Branches** - any Git branches whose name contains this issue's key. Shows the branch name, creation date and current status (open, merged, deleted).

**Commits** - all commits whose message contains this issue's key. Shows the commit hash, author, date, message and repository.

**Pull requests** - any pull requests whose source branch name, title or description contains this issue's key. Shows PR title, status (open, approved, merged, declined), number of approvals and number of comments.

**Builds** - the CI/CD build status for commits and branches associated with this issue. Shows pass/fail, build number and a link to the pipeline run.

**Deployments** - deployment status from Bitbucket Pipelines deployment environments (requires Jira Software and properly configured deployment steps).

### Navigating from the development panel

Every item in the development panel is a link. Clicking a commit opens that commit's diff in Bitbucket. Clicking a pull request opens the PR. Clicking a build opens the pipeline run. Developers and project managers can navigate between Jira and Bitbucket seamlessly.

### Development panel visibility

The development panel is visible to all Jira users who have permission to view the issue. Project managers, QA engineers and product owners can see the code status of any issue without needing a Bitbucket account or any knowledge of Git.

---

## Smart commits

Smart commits allow developers to trigger Jira actions directly from Git commit messages. When you push a commit containing a smart commit command, Jira executes that command against the linked issue automatically.

Smart commits are the most powerful aspect of the Jira-Bitbucket integration. They eliminate the "context switch tax" of opening Jira to update an issue status after completing a code change.

### How smart commits are processed

1. Developer writes a commit message containing a Jira issue key and smart commit commands
2. Developer pushes the commit to Bitbucket
3. Bitbucket parses the commit message and identifies smart commit syntax
4. Bitbucket sends the commands to Jira via the integration
5. Jira executes the commands against the specified issue
6. The Jira issue is updated without the developer having touched Jira

This happens asynchronously - the push completes immediately and Jira processes the command in the background (typically within seconds).

### Smart commit processing requirements

For smart commits to work:

- The Bitbucket workspace must be connected to the Jira site
- The author of the Git commit must have a Bitbucket account linked to a Jira account in the same Atlassian organisation
- The Jira issue key in the commit message must be a valid issue in the connected Jira site
- The committer must have permission to perform the action in Jira (e.g. adding comments or transitioning issues)

If any of these conditions are not met, the smart commit is silently ignored - the code push succeeds but the Jira action does not execute.

---

## Smart commit syntax reference

Smart commit commands appear in the commit message after the issue key. Multiple commands can be combined in a single message.

### Comment (`#comment`)

Adds a comment to the Jira issue:

```bash
git commit -m "PROJ-123 #comment Fixed the null pointer exception in the payment module"
```

The text after `#comment` (until the next smart commit keyword or end of message) becomes a comment on the Jira issue PROJ-123.

```bash
# Multi-word comment
git commit -m "PROJ-123 #comment Refactored the authentication service to use JWT tokens instead of session cookies. This reduces database load by eliminating session lookups."
```

### Transition (`#<status>`)

Transitions the Jira issue to a different workflow status. The keyword after `#` must match a transition name configured in the Jira project's workflow (case-insensitive):

```bash
# Transition to "In Progress"
git commit -m "PROJ-123 #in-progress Starting implementation of OAuth login"

# Transition to "In Review"
git commit -m "PROJ-123 #in-review Ready for code review"

# Transition to "Done"
git commit -m "PROJ-123 #done Completed feature implementation"

# Transition to "Resolved"
git commit -m "PROJ-456 #resolved Fixed null pointer in payment service"
```

The exact transition names available depend on the Jira project's workflow configuration. Common defaults:

- `#to-do` - moves to To Do
- `#in-progress` - moves to In Progress
- `#done` - moves to Done (for simple workflows)

> [!NOTE]
> The transition name must match a valid transition from the issue's current status in the Jira workflow. If the transition is not valid from the current status (e.g. trying to transition directly from To Do to Done in a workflow that requires going through In Progress), the smart commit is ignored.

### Time logging (`#time`)

Logs time spent on the issue in Jira's time tracking system (requires time tracking to be enabled in the Jira project):

```bash
# Log 2 hours 30 minutes
git commit -m "PROJ-123 #time 2h 30m Implemented OAuth provider abstraction layer"

# Log 45 minutes
git commit -m "PROJ-456 #time 45m #comment Fixed edge case in input validation"

# Log 1 day
git commit -m "PROJ-789 #time 1d Completed full database migration"
```

Time format: `Xw Xd Xh Xm` (weeks, days, hours, minutes). You can use any combination.

### Combining multiple commands

Multiple smart commit commands can appear in a single commit message:

```bash
# Transition AND comment
git commit -m "PROJ-123 #done #comment All unit tests passing, ready for QA"

# Transition, comment AND time
git commit -m "PROJ-456 #in-review #time 3h #comment Refactored authentication service - see PR for details"

# Multiple issues in one commit
git commit -m "PROJ-123 PROJ-456 #comment Fixed both issues with single authentication refactor"
```

When you reference multiple issue keys, the commands apply to all referenced issues.

### What smart commits cannot do

Smart commits are useful but have limits:

- They cannot assign issues to people
- They cannot set priority, due dates or custom fields
- They cannot create new issues
- They cannot delete issues or comments
- They only work for issues in the connected Jira site

For anything beyond commenting, transitioning and time logging, update Jira directly or use Jira Automation (see the automation section below).

---

## Creating branches from Jira

One of the most useful workflow features is creating branches directly from a Jira issue. This ensures branch names automatically contain the issue key and the integration link is established from the start.

### Creating a branch from a Jira issue

1. Open the Jira issue you are about to work on
2. In the development panel, click **Create branch**
3. Choose the Bitbucket repository (if multiple are connected)
4. Select the source branch (what to branch off from - typically `main` or `develop`)
5. Review the auto-generated branch name (e.g. `feature/PROJ-123-fix-login-redirect`)
6. Customise the branch name if needed
7. Click **Create branch**

Jira creates the branch in Bitbucket directly. The branch name automatically includes the issue key, which means:

- The Jira development panel immediately shows the branch
- Any future commits pushed to this branch are linked to the issue without needing smart commit syntax

### Branch naming convention

Jira generates branch names following this pattern:

```
{branch-type}/{issue-key}-{issue-summary-slug}
```

Example: Issue `PROJ-123` with summary "Fix login redirect loop" generates:

```
feature/PROJ-123-fix-login-redirect-loop
```

The issue summary is truncated and slugified (spaces replaced with hyphens, special characters removed). The branch type (feature, bugfix, hotfix) can be selected from a dropdown when creating the branch.

### Checking out the branch locally

After creating the branch in Jira:

```bash
git fetch origin
git checkout feature/PROJ-123-fix-login-redirect-loop

# Or in one command:
git fetch origin && git checkout feature/PROJ-123-fix-login-redirect-loop
```

---

## Linking pull requests to Jira issues

Pull requests are linked to Jira issues when the PR's source branch name, title or description contains a valid Jira issue key.

### Automatic linking via branch name

If you created the branch from Jira (or named it with the issue key), the PR is linked automatically when created:

```
Branch: feature/PROJ-123-fix-login-redirect
```

When you open a pull request from this branch, Bitbucket detects `PROJ-123` and links the PR to that issue. No additional action required.

### Manual linking via PR title or description

If your branch name does not contain the issue key, add the key to the PR title or description:

```
PR Title: Fix login redirect loop for OAuth users [PROJ-123]
```

or in the description:

```
Fixes PROJ-123 - users were being redirected to the home page instead of their original destination after OAuth login.
```

### What the Jira development panel shows for PRs

Once linked, the Jira issue development panel shows:

- PR title and status (Open, Approved, Merged, Declined)
- Number of comments
- Number of approvals
- Build status of the PR's pipeline
- Direct link to the PR in Bitbucket

Project managers and product owners can see whether code for a feature is in review, whether it has been approved and whether it has been merged - all from within Jira.

---

## Build and deployment status in Jira

Bitbucket Pipelines automatically reports build and deployment status to connected Jira issues.

### Build status

When a pipeline runs on a commit or branch linked to a Jira issue, the pipeline result (pass/fail) appears in the Jira development panel under **Builds**.

This shows:

- Whether the latest build passed or failed
- The pipeline build number
- A direct link to the pipeline run in Bitbucket

QA teams and release managers can see whether the code for any issue is currently passing CI/CD checks without leaving Jira.

### Deployment status

When a Bitbucket Pipelines step uses the `deployment:` key (covered in [06-bitbucket-pipelines.md](06-bitbucket-pipelines.md)), deployment events are sent to Jira. The issue development panel shows which environments the code has been deployed to and when.

```yaml
- step:
    name: Deploy to production
    deployment: production
    script:
      - ./deploy.sh production
```

This step's completion creates a deployment record in Jira showing that code linked to related issues has been deployed to production.

---

## Jira automation triggered by Bitbucket events

Jira Automation (available in Jira Software and Jira Work Management) can trigger automated actions based on Bitbucket events. This goes beyond smart commits and enables sophisticated workflow automation.

### Setting up Jira automation

1. In Jira, go to **Project settings** > **Automation** (or **Jira settings** > **Automation** for global rules)
2. Click **Create rule**
3. Choose a Bitbucket trigger (listed under the DevOps or Bitbucket section)

### Available Bitbucket triggers in Jira Automation

**Branch created** - triggers when a branch containing an issue key is created in Bitbucket. Use this to automatically transition an issue to "In Progress" when a developer starts a branch.

**Commit created** - triggers when a commit referencing an issue is pushed.

**Pull request created** - triggers when a pull request referencing an issue is opened.

**Pull request approved** - triggers when all required approvals are given.

**Pull request merged** - triggers when a pull request is merged.

**Build status changed** - triggers when a Bitbucket Pipeline run completes (pass or fail).

**Deployment status changed** - triggers when code is deployed to an environment.

### Example automations

**Auto-transition to "In Progress" when a branch is created:**

```
Trigger:    Branch created
Condition:  Issue status is "To Do"
Action:     Transition issue to "In Progress"
            Add comment: "Development started - branch created"
```

**Auto-transition to "In Review" when a PR is opened:**

```
Trigger:    Pull request created
Condition:  Issue status is "In Progress"
Action:     Transition issue to "In Review"
            Assign issue to PR reviewer
```

**Auto-transition to "Done" when a PR is merged:**

```
Trigger:    Pull request merged
Condition:  Issue status is "In Review"
Action:     Transition issue to "Done"
            Add comment: "Code merged. Awaiting deployment."
```

**Notify QA when code is deployed to staging:**

```
Trigger:    Deployment status changed to "Successful"
            Environment: "Staging"
Condition:  Issue has label "needs-qa"
Action:     Assign issue to QA team lead
            Add comment: "Code deployed to staging - ready for QA testing"
            Transition issue to "In QA"
```

**Alert when a build fails:**

```
Trigger:    Build status changed to "Failed"
Condition:  Always
Action:     Add comment with @mention of the commit author: "Build failed on your latest commit"
            Transition issue back to "In Progress"
```

### Why automations are more powerful than smart commits

Smart commits require the developer to remember to include the right keywords in every commit message. Automation rules trigger automatically based on events, with no developer action required. The developer pushes code; Jira updates itself. This is particularly valuable for:

- Teams who do not want to enforce smart commit discipline
- Automating transitions that should always happen (PR opened = move to review)
- Sending notifications to non-developers who do not monitor Bitbucket

---

## Jira Software sprint boards and code status

Jira Software's sprint boards show code development status directly on issue cards when the Bitbucket integration is active.

### Code icon on issue cards

On a Jira sprint board, each issue card can display a small code icon showing the development status:

- No icon: no code activity linked
- Branch icon: a branch exists but no PR yet
- PR icon: a pull request exists
- Merged icon: PR has been merged
- Failed icon: build failure linked to this issue

This gives the entire team - developers, QA, product managers - a visual overview of where every issue stands in the development pipeline without opening any individual issue.

### Filtering by code status

In Jira Software, you can filter the sprint board by code status: "show only issues with open PRs" or "show only issues where the build is failing." This is useful during sprint reviews or release preparation to quickly identify what is blocking deployment.

---

## Integration with Jira Service Management

For teams using Jira Service Management (formerly Jira Service Desk) alongside Bitbucket, the integration extends to service request tracking:

- Customer service requests in JSM can be linked to Bitbucket fix branches
- The development panel on service requests shows the same code activity
- Smart commits can comment on and transition JSM tickets using the same syntax
- Deployments linked to service request fixes are visible in the ticket

This is useful for support engineering teams who use JSM for customer tickets and Bitbucket for the code fixes - the customer service agent can see exactly what code was written to fix their customer's problem and whether it has been deployed.

---

## Troubleshooting the integration

### Commits not appearing in Jira

**Check the issue key format.** The key must be uppercase, e.g. `PROJ-123` not `proj-123` or `Proj-123`. Bitbucket pattern matches case-sensitively.

**Check the workspace connection.** Go to Bitbucket workspace settings > Jira Software and verify the connection status is "Connected."

**Check the committer's email.** The Git commit author email must match an Atlassian account that is a member of the connected Jira site. If you commit with a personal email not linked to your Atlassian account, smart commits and development panel links will not appear.

**Check that the project key exists.** If `PROJ-123` does not correspond to a real Jira project in the connected site, nothing will link. Verify the project key in Jira.

### Smart commits not executing

**Check permissions.** The committer must have permission in Jira to perform the action (add comments, transition issues). If they can do it manually in Jira, smart commits should work.

**Check the transition name.** The `#done` keyword must exactly match a workflow transition name in the Jira project's workflow. If the transition is called "Complete" not "Done", use `#complete`.

**Check for invalid transitions.** If the issue is already Done, transitioning it to Done again is a no-op and may silently fail depending on the workflow configuration.

### Development panel showing "no branches"

If a branch exists in Bitbucket but does not appear in the Jira development panel, check that the branch name contains the issue key. A branch named `feature/login-fix` without `PROJ-123` in the name will not link. Rename the branch or add the issue key.

---

## Try It Yourself

**Exercise 1 - Connect Bitbucket to Jira**

If you have a Jira account (free tier available at [atlassian.com/software/jira](https://www.atlassian.com/software/jira)):

1. Make sure both your Bitbucket workspace and Jira site are in the same Atlassian organisation
2. Go to Bitbucket workspace settings > Integrations > Jira Software
3. Connect your Jira site
4. Create a simple Jira project with key `TEST`

**Exercise 2 - Test smart commits**

1. Create a test issue in your Jira project (e.g. `TEST-1`)
2. In your Bitbucket repository:

```bash
git checkout -b feature/TEST-1-smart-commit-test
echo "testing smart commits" >> test.txt
git add test.txt
git commit -m "TEST-1 #comment Testing smart commit integration from Bitbucket"
git push origin feature/TEST-1-smart-commit-test
```

3. Open `TEST-1` in Jira and check the development panel and comments

**Exercise 3 - Create a branch from Jira**

1. Create another test issue: `TEST-2`
2. In the issue's development panel, click **Create branch**
3. Select your Bitbucket repository and source branch
4. Accept the auto-generated branch name
5. Check Bitbucket to confirm the branch was created
6. Check the Jira development panel to confirm the branch appears

**Exercise 4 - Create automation**

If you have access to Jira Automation:

1. Go to Project settings > Automation
2. Create a rule: When PR is merged > Transition issue to Done
3. Create a branch, open a PR and merge it
4. Confirm the Jira issue moves to Done automatically

---

## Common Mistakes

**Using lowercase issue keys in commit messages**

`proj-123` does not trigger the integration. Always use uppercase: `PROJ-123`. The issue key format is strictly uppercase letters, hyphen, number.

**Committing with a different email than your Atlassian account**

Smart commits and development panel links are matched by committer identity. If your Git `user.email` is `personal@gmail.com` but your Atlassian account is `work@company.com`, and the Jira site is linked to your work account, smart commits from your personal email address will not execute.

Set your Git email for work repositories:

```bash
# Per-repository setting (does not affect other repos)
git config user.email "work@company.com"
```

**Using smart commit transition names that do not match the workflow**

`#done` only works if the Jira project has a transition called "Done" from the current issue status. Different Jira projects can have completely different workflow transitions. Check the project's workflow configuration before using transition smart commits.

**Expecting the development panel to update instantly**

The development panel update is near-real-time but not instant. After pushing a commit, wait a few seconds for the Jira development panel to reflect the activity. If it does not appear after 30 seconds, investigate the connection and email matching.

**Connecting the wrong Jira site**

If your organisation has multiple Jira instances (e.g. a development Jira and a production Jira), make sure you connect the Bitbucket workspace to the correct one. Commits referencing issue keys from the wrong Jira instance will not link.

---

## Summary

The Jira-Bitbucket integration connects code activity in Bitbucket to issue tracking in Jira without any manual effort from developers once the connection is configured.

Connect a Bitbucket workspace to a Jira site via workspace settings > Integrations. Once connected, all repositories in the workspace participate automatically. The link between code and issues is the Jira issue key embedded in branch names, commit messages and pull request titles.

The Jira development panel on each issue shows all linked branches, commits, pull requests, build status and deployment status. Project managers can track development progress without touching Bitbucket.

Smart commits let developers trigger Jira actions from commit messages: `#comment` adds a comment, `#done` (or any transition name) transitions the issue, `#time 2h 30m` logs time. Smart commits are processed when the commit is pushed to Bitbucket.

Jira Automation extends the integration to automated workflows triggered by Bitbucket events: branch created, PR opened, PR merged, build status changed, deployment completed. Automation rules eliminate the need for manual Jira updates in response to development events.

Create branches from Jira issues to ensure the issue key is always in the branch name and the development panel link is established from the start.

---

## Sources

- [Atlassian: Connect Bitbucket to Jira](https://support.atlassian.com/jira-cloud-administration/docs/integrate-with-bitbucket-cloud/)
- [Atlassian: Smart commits](https://support.atlassian.com/bitbucket-cloud/docs/use-smart-commits/)
- [Atlassian: Jira development panel](https://support.atlassian.com/jira-software-cloud/docs/view-development-information-for-an-issue/)
- [Atlassian: Jira automation triggers](https://support.atlassian.com/cloud-automation/docs/jira-automation-triggers/)
- [Atlassian: Create a branch from Jira](https://support.atlassian.com/jira-software-cloud/docs/create-a-branch-from-jira-software/)
- [Atlassian: Build and deployment status in Jira](https://support.atlassian.com/jira-software-cloud/docs/view-build-and-deployment-information/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

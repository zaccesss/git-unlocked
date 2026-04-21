# Merge Requests

**Difficulty:** 🟡 Intermediate | **Time:** 40 minutes

A merge request (MR) is how code gets reviewed and merged on GitLab. If you are coming from GitHub, the concept is identical to a pull request - you push a branch, open a merge request, a colleague reviews it, and it gets merged into the target branch. The name "merge request" is actually more accurate than "pull request": you are requesting that your changes be merged, not pulling anything.

But GitLab's merge requests go significantly beyond the basics. They integrate directly with CI/CD pipelines, support multiple merge methods including fast-forward and squash, have merge trains for high-traffic repositories, support required approvals (Premium), connect to CODEOWNERS for automatic reviewer assignment (Premium) and can be converted to and from issues with a single click. This file covers everything from opening your first MR to the most advanced merge workflows.

---

## Table of Contents

- [What is a merge request?](#what-is-a-merge-request)
- [Creating a merge request](#creating-a-merge-request)
- [The merge request interface](#the-merge-request-interface)
- [Draft merge requests](#draft-merge-requests)
- [Reviewing a merge request](#reviewing-a-merge-request)
- [Suggested changes](#suggested-changes)
- [Resolving threads](#resolving-threads)
- [Merge request approvals](#merge-request-approvals)
- [CODEOWNERS](#codeowners)
- [Merge methods](#merge-methods)
- [Auto-merge](#auto-merge)
- [Merged results pipelines](#merged-results-pipelines)
- [Merge trains](#merge-trains)
- [MR templates](#mr-templates)
- [Closing issues from merge requests](#closing-issues-from-merge-requests)
- [MR dependencies](#mr-dependencies)
- [Reverting a merge request](#reverting-a-merge-request)
- [The glab CLI for merge requests](#the-glab-cli-for-merge-requests)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Merge Request?

A merge request is a proposal to merge one branch into another. You do your work on a feature branch, push it to GitLab, then open a merge request to say: "I have made these changes. Please review them and merge them into main."

The merge request is where everything comes together:

- **Code diff**: every changed file, every changed line, side by side or unified
- **Pipeline status**: whether your CI/CD tests are passing or failing, linked directly to this branch
- **Review comments**: inline comments on specific lines, general discussion threads, suggestions that can be applied with one click
- **Approval status**: who has approved, how many more are needed (Premium)
- **Merge controls**: buttons to merge, set auto-merge, rebase or close
- **Linked issues**: issues that will automatically close when this MR merges

The result is that a merge request is not just a code review. It is the complete record of why a change was made, who reviewed it, what concerns were raised, how they were resolved and when it landed. That history lives permanently in the project even after the branch is deleted.

> [!NOTE]
> If you are coming from GitHub: a GitLab merge request is exactly a GitHub pull request. Every concept maps directly - branch → branch, PR → MR, reviewer → reviewer, check → pipeline. The main differences are in features: some things are more capable on GitLab (merge trains, merge methods), some are paywalled (approvals, CODEOWNERS enforcement).

---

## Creating a Merge Request

There are several ways to create a merge request, depending on where you are in your workflow.

### From the terminal after a push

When you push a branch to GitLab, the output in your terminal includes a direct URL to create a merge request:

```bash
git push origin feature/add-dark-mode

# Output includes:
# remote:
# remote: To create a merge request for feature/add-dark-mode, visit:
# remote:   https://gitlab.com/NAMESPACE/PROJECT/-/merge_requests/new?merge_request[source_branch]=feature/add-dark-mode
# remote:
```

Click or copy that URL. It opens a pre-filled MR creation form with your branch already selected as the source.

### From the GitLab UI

**Method 1 - from the merge requests list:**
Left sidebar → **Code → Merge requests** → **New merge request**

Select source branch (your feature branch) and target branch (usually `main` or `develop`) → **Compare branches and continue**.

**Method 2 - from the repository branch list:**
Left sidebar → **Code → Repository** → click the **Branches** tab → find your branch → click **Create merge request**.

**Method 3 - from a branch push notification:**
After pushing a branch, a yellow banner appears at the top of the project page: "You pushed to branch-name. Create merge request?" Click the button.

### From an issue

If an issue describes the work you have done, you can create a branch and MR directly from the issue:

On any issue page → **Create merge request** (dropdown button on the right side).

Options:

- **Create merge request and branch**: creates a new branch named after the issue (e.g. `1-add-dark-mode`) and opens a draft MR. The branch name includes the issue number so GitLab auto-links them.
- **Create branch**: creates the branch only, no MR yet

This is the recommended workflow for issue-driven development. The MR is automatically linked to the issue, and merging the MR automatically closes the issue.

### From the glab CLI

```bash
# Create with interactive prompts
glab mr create

# Create with all options specified
glab mr create \
  --title "feat: add dark mode toggle" \
  --description "Implements dark mode across the settings page. Closes #42." \
  --target-branch main \
  --assignee zaccess \
  --label "feature,frontend"

# Create from the current branch, filling title from last commit
glab mr create --fill
```

### The MR creation form

When creating an MR, the form contains:

**Title**: a clear, specific description of what this MR does. Follow Conventional Commits style: `feat: add dark mode toggle`, `fix: resolve null pointer on login`, `docs: update contributing guide`.

**Description**: explain the _why_ behind the change, any context reviewers need, screenshots for UI changes, testing instructions. GitLab supports full Markdown including tables, code blocks and images. If your project has an MR template, the description is pre-filled from it.

**Assignees**: who is responsible for this MR (usually yourself - the author). Not the same as reviewers.

**Reviewers**: who you are asking to review the code. Add reviewers before submitting. They receive a notification.

**Labels**: categorise the MR for filtering and reporting. Apply the same labels you would use on the related issue.

**Milestone**: associate the MR with a sprint or release milestone.

**Due date**: optional deadline for the review.

**Delete source branch when merge request is accepted**: highly recommended - keeps the branch list clean.

**Squash commits**: choose whether to squash all commits in this branch into one when merging.

---

## The Merge Request Interface

Once created, the MR has four tabs:

### Overview tab

The main tab. Shows:

- The MR description
- All comments and discussion threads in chronological order
- Pipeline status (with a direct link to the pipeline and each job)
- Approval status - who has approved and how many are required (Premium)
- The merge request widget at the bottom - the big button that controls merging

The widget at the bottom of the Overview shows the current merge status: green if everything is ready, yellow if there are warnings (unresolved threads, missing approvals) and red if there are blockers (failing pipeline, merge conflicts).

### Commits tab

A list of all commits in the source branch that are not in the target branch. Click any commit to see its individual diff. This view is useful for understanding the history of changes made during development.

### Pipelines tab

Every pipeline that has run against this MR, including the current status, when it started and how long it took. Click any pipeline to drill into its stages, jobs and logs.

### Changes tab

The full diff of every changed file. This is where code review happens.

Navigation:

- File list on the left shows every changed file with `+` and `-` counts
- Click a filename to jump directly to that diff
- Toggle between **side-by-side** (two-column) and **inline** (single-column) diff view
- Use **Previous/next file** buttons to walk through files in order
- Mark files as **Viewed** to track your review progress

Comment on any line by hovering over the line number and clicking the blue speech bubble that appears. Commenting starts a thread.

---

## Draft Merge Requests

A draft MR signals that the work is not yet ready for review. Reviewers can see the changes and leave early feedback, but the MR cannot be merged until the draft status is removed.

### Marking as draft

**In the title**: prefix the MR title with any of:

- `Draft:` (current standard)
- `[Draft]`
- `(Draft)`
- `WIP:` (legacy, still works but `Draft:` is preferred)

**Via quick action**: add `/draft` in any comment or the description.

**Via the MR action menu**: in the open MR, click **Mark as draft** from the action dropdown.

### Removing draft status

- Remove the prefix from the title manually
- Click **Mark as ready** button (appears at the top of the MR when it is in draft)
- Use the `/ready` quick action in a comment

> [!TIP]
> Opening MRs as draft immediately after pushing is a good habit. It signals "I am still working on this - no review needed yet" while still giving teammates visibility into what is in progress. Mark it ready when you want eyes on it.

---

## Reviewing a Merge Request

### Inline comments

On the **Changes** tab, hover over any line number and click the blue speech bubble icon to start a thread on that line. You can also click and drag across multiple lines to comment on a range.

Write your comment in Markdown. You can:

- Ask a question: `What happens if this value is null?`
- Request a change: "This should use the existing `formatDate` utility rather than reimplementing it"
- Approve a specific approach: "This is cleaner than the previous implementation - nice"
- Suggest a specific fix (see [Suggested changes](#suggested-changes) below)

After writing, choose **Start a review** (queues your comment as part of a batch review, invisible to the author until you submit) or **Add comment now** (immediately visible).

### Batch reviews

When you click **Start a review** instead of **Add comment now**, your comments are held privately until you are ready to submit the whole review at once. The author receives one notification for the full review rather than a notification per comment.

When you are finished reviewing, click **Finish review** → write an optional summary → **Submit review**. All your queued comments become visible simultaneously.

### Requesting changes vs commenting

GitLab does not have GitHub's explicit "Request changes" / "Approve" / "Comment" three-way review state per comment. On GitLab, the equivalent is:

- **Approval**: use the **Approve** button (or `/approve` quick action) to formally approve the MR
- **Requesting changes**: leave comments and threads. An MR with unresolved threads can optionally be blocked from merging (configurable in project settings)
- **No objection**: approve even with open comments if you trust the author to resolve them

---

## Suggested Changes

Suggested changes let reviewers propose exact code replacements directly in the diff. The MR author can apply the suggestion with one click - no need to switch to their editor, make the change, commit and push.

### Creating a suggestion

In a review comment, use the suggestion code fence:

````
```suggestion
const darkModeEnabled = userPreferences.darkMode ?? false;
```
````

GitLab renders this as a diff showing the original line(s) crossed out and the suggestion below it, with an **Apply suggestion** button.

### Multi-line suggestions

To suggest a replacement for multiple lines, start the comment on the first line and drag to the last before clicking the comment icon:

````
```suggestion:-0+2
const darkModeEnabled = userPreferences.darkMode ?? false;
const fontSize = userPreferences.fontSize ?? 'medium';
```
````

The `-0+2` means "replace 0 lines above the selected line and 2 lines below" - adjust the numbers to cover the lines you want to replace.

### Applying suggestions

**Individually**: click **Apply suggestion** on any single suggestion. GitLab commits the change directly to the source branch with an auto-generated commit message.

**As a batch**: tick the checkbox on each suggestion you want to apply → **Apply suggestions** button → write a custom commit message → commit all selected suggestions in one commit.

> [!NOTE]
> Suggestions can only be applied by someone with push access to the source branch (typically the MR author). If you are reviewing a fork, you cannot apply suggestions directly - the author must do it.

---

## Resolving Threads

Every inline comment starts a thread. Threads stay open until explicitly resolved. On busy MRs, tracking which concerns have been addressed and which are still open is important.

### Resolving a thread

Click **Resolve thread** on any comment. This collapses the thread and removes it from the unresolved count. Only the MR author or project Maintainers+ can resolve threads.

To reopen a resolved thread: click **Unresolve thread**.

### Requiring all threads to be resolved before merge

Configure this in project settings: **Settings → Merge requests → Merge checks → All threads must be resolved**.

When enabled, the merge button is disabled until every thread is resolved. The MR widget shows a count of unresolved threads and a link to each one.

This is a good default for team projects - it prevents code from merging with unanswered review questions.

### Navigating unresolved threads

In the MR Overview, unresolved threads are listed below the description. Click any thread to jump to it in the Changes tab. Use the **Previous unresolved thread / Next unresolved thread** navigation buttons in the Changes tab to walk through them in order.

---

## Merge Request Approvals

> [!IMPORTANT]
> Required merge request approvals are a **Premium feature** ($29/user/month on GitLab.com). On the Free tier, approvals are optional and advisory only - any Developer can click Approve but it does not block merging.

### How approvals work

With Premium, you can configure rules that require a minimum number of approvals from specific users or groups before a merge is allowed.

**Configure approval rules**: Settings → Merge requests → Merge request approvals.

**Rule types:**

- **Any approver**: N approvals from anyone with Developer+ access
- **Specific users**: N approvals from a named list of specific users
- **Group**: N approvals from members of a specific group
- **CODEOWNERS**: approvals required from owners of the files changed (see [CODEOWNERS](#codeowners) below)

You can have multiple rules - for example: "2 approvals from the backend team, AND 1 approval from the security team for changes touching `/auth/`".

### Approval settings (Premium)

**Prevent MR approvals by the author**: the person who opened the MR cannot approve their own work. Enabled by default in most compliance-focused setups.

**Prevent approvals by users who add commits**: if someone pushing commits to the source branch is listed as an approver, their approval is removed when they push. Prevents circumventing review by adding commits after approval.

**Reset approvals on push**: when new commits are pushed to the source branch after an approval, all approvals are removed and the MR must be re-approved. Ensures approvals reflect the final state of the code, not an earlier version.

**Remove all approvals when commits are added to the source branch**: same as above, applies to all approvals rather than just the requester's.

### Approving and unapproving

To approve: click the **Approve** button in the MR widget on the Overview tab, or use the quick action `/approve` in a comment.

To remove your approval: click **Revoke approval**, or use `/unapprove`.

The approval status shows who has approved, when, and whether the required threshold has been met.

---

## CODEOWNERS

> [!IMPORTANT]
> CODEOWNERS **enforcement** - automatically requiring code owners to approve changes to their files - is a **Premium feature**. The CODEOWNERS file itself can exist on any tier, but enforcement as a merge requirement costs money.

### What CODEOWNERS does

The CODEOWNERS file maps file paths and patterns to users or groups who "own" those files. When a merge request changes a file, the designated owners are automatically added as required reviewers.

This is powerful for large codebases where different teams are responsible for different parts of the code. Instead of manually adding the right reviewers every time, GitLab looks at which files changed and adds the owners automatically.

### File location

GitLab checks for the CODEOWNERS file in this order (uses the first one found):

1. `CODEOWNERS` (repository root)
2. `docs/CODEOWNERS`
3. `.gitlab/CODEOWNERS` (recommended location - keeps it with other GitLab configuration)

### CODEOWNERS syntax

```
# Default owner for everything not matched by a more specific rule
*                           @default-owner

# Specific directories
/docs/                      @docs-team
/src/auth/                  @security-team @backend-lead

# File patterns
*.rb                        @ruby-team
*.go                        @go-team
Dockerfile                  @devops-team

# Multiple owners - all must approve (or any, depending on settings)
/src/payments/              @payments-team @cto

# GitLab groups (using group path)
/infrastructure/            @acme-corp/devops-team

# Sections with required approval count (Premium)
[Frontend][2] @frontend-team
*.vue
*.js
*.css

# Optional sections (not blocking - ^ prefix)
^[Documentation] @docs-team
/docs/
```

**Key rules:**

- Patterns follow `.gitignore` syntax
- More specific rules take precedence over less specific ones
- Paths starting with `/` are relative to the repository root
- `@username` references a specific user by their GitLab username
- `@group/subgroup` references a GitLab group
- `[Section name]` groups related rules (Premium)
- `[Section][N]` requires N approvals from the section's owners (Premium)
- `^[Optional section]` makes the entire section optional - it shows in the approvals panel but does not block merging

### Enforcing CODEOWNERS

For CODEOWNERS rules to block merging, they must be attached to a protected branch:

Settings → Repository → Protected branches → your branch → enable **Require approval from Code Owners**.

With this enabled, any MR targeting that branch must receive approval from every applicable CODEOWNERS entry before it can merge.

---

## Merge Methods

GitLab supports four merge strategies, configured at: **Settings → General → Merge requests → Merge method**.

### Merge commit (default)

Creates a merge commit joining the source branch into the target branch. Always creates a commit even if the source branch is only one commit ahead.

```
*   abc123 Merge branch 'feature/dark-mode' into 'main'
|\
| * def456 feat: add dark mode toggle
| * ghi789 feat: add dark mode CSS variables
|/
* jkl012 Previous commit on main
```

**Pro**: preserves exact history of when branches diverged and merged. Every merge is clearly visible.
**Con**: creates extra merge commits that some find noisy. `git log --oneline` becomes harder to read on active repositories.

**Best for**: teams that value a complete, unambiguous history of merges.

### Merge commit with semi-linear history

A hybrid: requires the source branch to be up-to-date with the target before merging. If the target has moved ahead since the branch was created, GitLab rejects the merge and requires a rebase first. Once up-to-date, creates a merge commit.

```
*   abc123 Merge branch 'feature/dark-mode' into 'main'
|\
| * def456 feat: add dark mode toggle (rebased onto current main)
|/
* jkl012 Previous commit on main
```

**Pro**: cleaner history than pure merge commits. No criss-crossing merge lines.
**Con**: requires rebasing before merging - more steps for contributors.

**Best for**: teams that want visible merge commits but also want clean, linear feature branch histories.

### Fast-forward merge

No merge commit is ever created. The target branch pointer simply advances to the tip of the source branch. Produces a perfectly linear history.

```
* def456 feat: add dark mode toggle
* ghi789 feat: add dark mode CSS variables
* jkl012 Previous commit on main
```

GitLab rejects the merge if a fast-forward is not possible (if the target has diverged). Contributors must rebase their branch onto the latest target before merging.

**Pro**: cleanest possible history. `git log` reads like a single timeline.
**Con**: no visual indication of when a feature branch was merged. Requires strict discipline around rebasing.

**Best for**: teams with strong Git discipline who prioritise a linear, readable history.

### Squash and merge

Available in combination with any of the above methods. Combines all commits on the source branch into a single commit before merging.

```
* abc123 feat: add dark mode toggle  ← all 7 commits squashed into one
* jkl012 Previous commit on main
```

The squashed commit's message is typically the MR title plus description. GitLab can suggest a commit message based on the MR title.

**Squash options** (configurable per project):

- **Do not allow**: squashing is never done
- **Allow**: author decides per MR (checkbox on the MR creation form)
- **Encourage**: default is on, author can opt out
- **Require**: always squash - individual commits are never preserved in the target branch

**Pro**: keeps the target branch history clean - one commit per feature.
**Con**: individual commit messages are lost. If a feature introduced a bug in commit 4 of 7, `git bisect` cannot identify it after squashing.

**Best for**: teams that use the MR title as the unit of history and want a clean, one-feature-one-commit main branch.

---

## Auto-merge

Auto-merge tells GitLab: "merge this MR as soon as all the conditions are met, without me having to come back and click the button."

Conditions checked before auto-merge executes:

- All required pipelines pass
- All required approvals are present (Premium)
- All threads are resolved (if configured)
- No merge conflicts

### Enabling auto-merge

In the MR widget at the bottom of the Overview tab, click the dropdown arrow next to the **Merge** button → **Set to auto-merge**.

Once set, the MR widget shows "Auto-merge enabled" with the conditions that are still pending. As each condition is met, it is checked off. When all conditions pass, GitLab merges the MR automatically - no further action needed.

### Cancelling auto-merge

Click **Cancel auto-merge** in the MR widget at any time before the conditions are all met.

Auto-merge is cancelled automatically if new commits are pushed to the source branch after auto-merge was set.

---

## Merged Results Pipelines

By default, an MR pipeline runs against the source branch alone - it tests your feature branch as it currently exists, not as it will exist after merging.

**Merged results pipelines** test the hypothetical merge result: what the target branch would look like if your MR were merged right now. This catches integration problems that would not be visible if only your branch is tested.

**How it works**: GitLab creates a temporary commit that is the result of merging your branch into the current target. The pipeline runs against this temporary merged result.

**Enable**: Settings → Merge requests → **Enable merged results pipelines**.

> [!NOTE]
> Merged results pipelines require slightly more compute resources since they rerun pipelines even when only the target branch has changed (because the merged result has changed). Monitor your pipeline minutes usage after enabling this.

If a merge conflict exists between the source and target branches, GitLab falls back to a standard MR pipeline on the source branch alone.

---

## Merge Trains

> [!IMPORTANT]
> Merge trains are a **Premium feature**.

### The problem merge trains solve

In a busy repository, multiple developers merge code to `main` constantly. Without merge trains, this scenario is common:

1. Developer A opens MR-1. Pipeline passes. MR-1 is approved.
2. Developer B opens MR-2. Pipeline passes. MR-2 is approved.
3. Developer A merges MR-1. `main` now has new commits.
4. Developer B merges MR-2. But MR-2's pipeline ran against the old `main` - it never tested with MR-1's changes. If MR-1 and MR-2 interact badly, `main` is now broken.

Merge trains solve this by queueing MRs and testing each one against the result of all the MRs ahead of it in the queue.

### How merge trains work

```
Train queue:
Position 1 - MR-1: tests [current main + MR-1]
Position 2 - MR-2: tests [current main + MR-1 + MR-2]
Position 3 - MR-3: tests [current main + MR-1 + MR-2 + MR-3]
```

Each pipeline runs in parallel. If MR-2's pipeline passes, it merges immediately after MR-1 (which has already tested against `main`). If MR-2 fails, it is removed from the train. MR-3 is rebuilt from the queue without MR-2.

### Enabling merge trains

1. Enable merged results pipelines first (Settings → Merge requests → Enable merged results pipelines)
2. Enable merge trains: Settings → Merge requests → **Enable merge trains**

### Adding an MR to the merge train

In the MR widget: click **Start merge train** instead of **Merge**. The MR joins the train.

View all MRs in the train: Code → Merge requests → **Merge trains** tab.

### When to use merge trains

Merge trains are valuable when:

- Your default branch deploys to production automatically after every merge
- Your team merges many MRs per day
- Merge conflicts or breaking interactions between concurrent MRs are a real problem

They add pipeline cost (each MR triggers an additional pipeline) and queue time. For teams merging once or twice a day, the overhead is not worth it.

---

## MR Templates

MR templates pre-fill the description field when a new merge request is created, prompting authors to provide consistent information: what changed, why, how to test it, any relevant links.

### Creating a template

Create a Markdown file in `.gitlab/merge_request_templates/` on the default branch.

**`.gitlab/merge_request_templates/Default.md`** (auto-applied to all new MRs):

```markdown
## What does this MR do?

<!-- Describe the change in 1-2 sentences -->

## Why is it needed?

<!-- Link to the issue or explain the motivation -->

Closes #

## How to test

<!-- Steps for the reviewer to verify the change works -->

1.
2.
3.

## Screenshots (if UI changes)

<!-- Before / after screenshots for any visual changes -->

## Checklist

- [ ] Tests added or updated
- [ ] Documentation updated
- [ ] No sensitive data committed
- [ ] Tested on all relevant operating systems
```

**Multiple templates**: create additional files in the same directory. The MR creation form shows a template selector when multiple templates exist.

```
.gitlab/merge_request_templates/
├── Default.md          ← auto-applied
├── Bug fix.md
├── Feature.md
└── Hotfix.md
```

Authors choose the appropriate template from the **Choose a template** dropdown on the MR creation form.

### Quick actions in templates

You can embed quick actions in templates to auto-assign labels, milestones or reviewers:

```markdown
/label ~"needs-review"
/milestone %"Sprint 12"
/assign_reviewer @senior-developer
```

These quick actions execute when the MR is created, before the author even submits the form.

---

## Closing Issues from Merge Requests

When a merge request is merged into the **default branch**, GitLab automatically closes any issues referenced in the MR description using specific keywords.

### Supported keywords

Any of these phrases, followed by `#ISSUE_NUMBER`:

```
Closes #42
Fixes #42
Resolves #42
Implements #42

# Also works with full URLs:
Closes https://gitlab.com/namespace/project/-/issues/42

# Cross-project (close an issue in a different project):
Closes namespace/other-project#42
```

### Multiple issues

Close multiple issues with comma separation or by repeating the keyword:

```
Closes #42, #43, #44
Fixes #50 and resolves #51
```

### When issues close

Issues referenced with closing keywords close **only when the MR merges into the default branch**. If you merge into a non-default branch (e.g. `develop`), the issue stays open. This is intentional - issues should only be closed when the work lands in production-ready code.

If you merge into a non-default branch and the work is complete, close the issue manually or use `/close` in a comment.

---

## MR Dependencies

> [!IMPORTANT]
> MR dependencies are a **Premium feature**.

MR dependencies let you mark one merge request as blocked by another. An MR cannot be merged until all its blocking MRs have been merged first.

**Use case**: you are building a feature that spans three MRs - a database migration, an API change and a frontend update. The API cannot merge until the migration is merged. The frontend cannot merge until the API is merged. MR dependencies enforce this order.

### Setting a dependency

In the MR sidebar → **Merge request dependencies** → **Add dependency**.

Enter the MR number or URL. The MR is now blocked. The merge button shows "Merge blocked: Waiting for merge request !23 to be merged."

---

## Reverting a Merge Request

After an MR is merged, you can create a revert MR that undoes all its changes in a single click.

On the merged MR page, click **Revert** (in the merged MR timeline). GitLab creates a new branch and a new MR that is the exact inverse of the original - every addition becomes a deletion and vice versa.

This is safer than `git revert` on the command line because:

- GitLab creates the revert MR ready for review - the revert goes through your normal code review process
- The new MR is linked to the original, providing clear audit trail: "This reverts MR !42 because..."
- No risk of reverting the wrong commits or making a mistake with commit ranges

---

## The glab CLI for Merge Requests

The `glab` CLI gives you full merge request management from the terminal. See [12-gitlab-cli.md](12-gitlab-cli.md) for installation.

```bash
# Create an MR from the current branch (interactive)
glab mr create

# Create with all options in one command
glab mr create \
  --title "feat: add dark mode" \
  --description "Implements dark mode. Closes #42." \
  --target-branch main \
  --label "feature" \
  --assignee "@me"

# Auto-fill title from the last commit message
glab mr create --fill

# List open MRs
glab mr list

# List MRs assigned to you
glab mr list --assignee @me

# View an MR (opens in browser by default)
glab mr view 42

# View in terminal (no browser)
glab mr view 42 --no-browser

# Approve an MR
glab mr approve 42

# Check out an MR's branch locally (useful for testing before approving)
glab mr checkout 42

# Merge an MR
glab mr merge 42

# Merge with squash and delete source branch
glab mr merge 42 --squash --remove-source-branch

# Close an MR without merging
glab mr close 42

# Reopen a closed MR
glab mr reopen 42

# Add a note/comment to an MR
glab mr note 42 --message "Looks good to me, just fix the typo on line 42"
```

---

## Try It Yourself

**Exercise: Full MR workflow from branch to merge**

**Step 1.** Create a feature branch and make a change:

```bash
git checkout -b feature/update-readme
echo "## Contributing" >> README.md
echo "See CONTRIBUTING.md for guidelines." >> README.md
git add README.md
git commit -m "docs: add contributing section to README"
git push origin feature/update-readme
```

**Step 2.** Create the MR - use the URL printed in the terminal output, or:

```bash
glab mr create --fill --target-branch main
```

**Step 3.** On the MR page:

- Go to the **Changes** tab and review the diff
- Click the speech bubble on a line to add an inline comment
- Try a suggested change: in your comment, write ` ```suggestion ` on a new line, add your suggested replacement text, then close with ` ``` `
- Click **Apply suggestion** to apply it

**Step 4.** Mark the MR as draft: add `Draft:` to the title, then remove it.

**Step 5.** Merge the MR: click **Merge** in the MR widget. Ensure **Delete source branch** is checked.

**Step 6.** Confirm on the repository page that `main` now has your changes and the feature branch is gone.

---

## Common Mistakes

**Not using draft MRs during development.** Opening a final-state MR and immediately asking for review is fine. But if you push a branch mid-development to get early feedback, always mark it as draft. Reviewers seeing a non-draft MR assume it is ready for final review and may leave detailed comments on code you are still changing.

**Confusing assignee and reviewer.** The assignee is the person responsible for the MR - usually the author. The reviewer is who you are asking to review the code. They are different people. Many teams use assignee as "whoever is working on this" and reviewer as "whoever should approve it".

**Not resolving threads before merging.** Even if your project does not require thread resolution before merge, leaving comment threads unresolved is poor hygiene. Future readers of the MR cannot tell whether the concern was addressed or forgotten. Resolve threads when you have addressed the feedback, or reply explaining why you chose a different approach.

**Approving your own MR (Free tier).** On the Free tier, any Developer can click Approve including the MR author. This is meaningless - it just increments a number. On Premium, you can configure the project to prevent self-approval. On Free, simply do not approve your own MRs.

**Force-pushing to a branch with an open MR.** Force-pushing (rewriting history on a branch that has an open MR) confuses reviewers. Their comments reference specific commit SHAs that no longer exist. GitLab handles this gracefully but it is disorienting. If you need to rebase interactively, do it before opening the MR or after communicating with reviewers.

**Not understanding that issue closing only works on the default branch.** A very common confusion: you merge a branch into `develop`, expect the linked issue to close, and it does not. Issues with `Closes #N` in the MR description only close when the MR merges into the **default branch**. If your workflow uses a `develop` branch as an integration branch before `main`, issues stay open until they eventually land in `main`.

**Using squash when you should not.** Squashing is excellent for cleaning up "WIP commit", "fix typo", "oops" commits. It is harmful when the individual commits tell an important story - if each commit is a meaningful, atomic change, squashing destroys that information. Use squash selectively based on whether the commit history is worth preserving.

---

## Summary

- A merge request is a proposal to merge one branch into another, and the complete record of code review, discussion and pipeline results
- **Create** MRs from the terminal URL in push output, from the UI, from an issue, or with `glab mr create --fill`
- **Draft MRs** signal work-in-progress. Prefix the title with `Draft:` or use `/draft`. Remove with **Mark as ready** or `/ready`
- **Review** happens in the Changes tab: inline comments, batch reviews, suggested changes that can be applied with one click
- **Suggested changes** use the `suggestion` code fence and can be batch-applied in a single commit
- **Thread resolution** tracks which review concerns have been addressed. Optionally required before merge
- **Approvals** (Premium): configure minimum required approvals, from specific users or groups. Settings for self-approval prevention and reset-on-push
- **CODEOWNERS** (enforcement Premium): `.gitlab/CODEOWNERS` maps file paths to responsible owners who are auto-added as reviewers
- **Merge methods**: merge commit (default), semi-linear, fast-forward (cleanest history), squash. Squash options: Do not allow, Allow, Encourage, Require
- **Auto-merge**: merge automatically when all conditions are met - pipeline, approvals, resolved threads
- **Merged results pipelines**: test the hypothetical merge result rather than just the source branch
- **Merge trains** (Premium): queue MRs and test each against all ahead of it in the queue - prevents broken main branches from concurrent MR interactions
- **MR templates**: store in `.gitlab/merge_request_templates/`. `Default.md` auto-applies. Multiple templates shown in a selector
- **Closing issues**: use `Closes #N` in the MR description. Issues close when the MR merges into the default branch only
- **Reverting**: click **Revert** on any merged MR to create a revert MR that undoes all its changes

---

## Sources and Further Reading

- [Merge requests documentation](https://docs.gitlab.com/user/project/merge_requests/) - complete reference
- [Creating merge requests](https://docs.gitlab.com/user/project/merge_requests/creating_merge_requests/) - all creation methods
- [Merge request approvals](https://docs.gitlab.com/user/project/merge_requests/approvals/) - Premium approval configuration
- [Code Owners](https://docs.gitlab.com/user/project/codeowners/) - CODEOWNERS file syntax and enforcement
- [Merge trains](https://docs.gitlab.com/ci/pipelines/merge_trains/) - enabling and using merge trains
- [Merged results pipelines](https://docs.gitlab.com/ci/pipelines/merged_results_pipelines/) - testing the merge result
- [Suggested changes](https://docs.gitlab.com/user/project/merge_requests/reviews/suggestions/) - applying reviewer suggestions
- [Quick actions](https://docs.gitlab.com/user/project/quick_actions/) - full list of `/command` shortcuts
- [glab CLI merge request commands](https://gitlab.com/gitlab-org/cli/-/tree/main/docs/source/mr) - full glab MR reference

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

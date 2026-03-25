# Pull Requests

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Pull requests are the heart of collaboration on GitHub. They are the formal process for proposing, reviewing and merging code changes. A pull request is not just a merge request - it is a structured conversation about code where changes can be discussed, improved, reviewed line by line and approved before they reach the main codebase. Understanding pull requests thoroughly is essential for working effectively on any shared project.

---

## Table of Contents

- [What is a pull request?](#what-is-a-pull-request)
- [The pull request workflow](#the-pull-request-workflow)
- [Creating a pull request](#creating-a-pull-request)
- [Pull request fields in detail](#pull-request-fields-in-detail)
- [Draft pull requests](#draft-pull-requests)
- [The pull request interface](#the-pull-request-interface)
- [Reviewing a pull request](#reviewing-a-pull-request)
- [Suggested changes](#suggested-changes)
- [Requesting changes](#requesting-changes)
- [Resolving review comments](#resolving-review-comments)
- [Merge methods](#merge-methods)
- [Auto-merge](#auto-merge)
- [Checking out a PR locally](#checking-out-a-pr-locally)
- [Pull request templates](#pull-request-templates)
- [Status checks and required reviews](#status-checks-and-required-reviews)
- [Merge queues](#merge-queues)
- [Reverting a merged pull request](#reverting-a-merged-pull-request)
- [Pull requests from forks](#pull-requests-from-forks)
- [GitHub CLI for pull requests](#github-cli-for-pull-requests)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Pull Request?

A pull request (PR) is a proposal to merge a set of commits from one branch into another. The name comes from the original Git concept of asking a project maintainer to "pull" your changes into their repository.

On GitHub, pull requests do more than just propose a merge. They:

- Show a visual diff of every changed file
- Provide a conversation thread on the overall change and on individual lines
- Trigger automated checks (CI, tests, linters) via GitHub Actions
- Require reviews and approvals before merging (when configured)
- Link to related issues
- Keep a permanent record of what was changed, why and who approved it

Every pull request has a number (`#42`) that is shared with the issue tracker - pull requests and issues are numbered from the same sequence in a repository.

---

## The Pull Request Workflow

The typical pull request workflow follows these steps:

```
1. Create a feature branch
         |
         v
2. Make commits on the branch
         |
         v
3. Push the branch to GitHub
         |
         v
4. Open a pull request
         |
         v
5. Automated checks run (CI, tests, linting)
         |
         v
6. Reviewer(s) review the code
         |
         v
7. Author addresses feedback and pushes new commits
         |
         v
8. Reviewer approves
         |
         v
9. Merge the pull request
         |
         v
10. Delete the feature branch
```

In small personal projects steps 5-8 may be skipped. In professional teams all steps are typically required.

---

## Creating a Pull Request

**From GitHub.com after pushing a branch:**

When you push a new branch to GitHub, a yellow banner appears at the top of the repository: **"YOUR_BRANCH had recent pushes. Compare & pull request."** Click it to open the PR form pre-filled with your branch.

**Manually:**

1. Go to the repository on GitHub
2. Click the **Pull requests** tab
3. Click **New pull request**
4. Set **base** (where you want to merge into, usually `main`) and **compare** (your branch)
5. Review the diff
6. Click **Create pull request**
7. Fill in the form and submit

**From GitHub Desktop:**

After committing and pushing a branch, GitHub Desktop shows a **Create pull request** button or a banner. Clicking it opens the PR form on GitHub.com.

**From GitHub CLI:**

```bash
gh pr create
```

Interactive prompts guide you through the title, body, reviewers and labels. Or specify everything:

```bash
gh pr create --title "add: dark mode support" --body "Closes #42" --reviewer colleague-username --label enhancement
```

Open the PR in your browser after creating:

```bash
gh pr create --web
```

---

## Pull Request Fields in Detail

### Title

Clear, concise and descriptive. Use the same commit message convention as your project: `add: dark mode support`, `fix: login redirect on mobile`, `update: upgrade React to v19`.

### Description

Full Markdown. This is where you explain:

- **What** changed and **why**
- How to test the changes
- Screenshots or screen recordings for UI changes
- Any breaking changes or migration notes
- Links to related issues using closing keywords

A good PR description is as important as the code. Reviewers need context to review effectively.

### Reviewers

Request specific team members to review the code. Requested reviewers receive a notification. On repositories with CODEOWNERS, reviewers are auto-requested based on which files changed.

You can request reviews from individuals, teams or both.

### Assignees

Who is responsible for the pull request. Usually the author assigns themselves.

### Labels

Same labels as issues. Useful for categorising PRs in the same way - `bug`, `enhancement`, `docs`, `breaking change` etc.

### Projects

Add the PR to a GitHub Project for tracking alongside related issues.

### Milestone

Associate the PR with a version milestone. Merging the PR contributes to milestone completion percentage.

### Linked issues

Use **Development** in the right sidebar to manually link an issue without an automatic close. Or use closing keywords in the description.

---

## Draft Pull Requests

A draft pull request signals that it is work in progress and not ready for review. CODEOWNERS are not automatically requested on drafts, and many teams configure branch protection to prevent merging draft PRs.

**Create a draft:**

- On the PR form, click the dropdown arrow next to **Create pull request** → **Create draft pull request**
- Via CLI: `gh pr create --draft`

**Convert to ready:**

- Click **Ready for review** at the bottom of the PR on GitHub.com
- Via CLI: `gh pr ready`

**When to use drafts:**

- You want to open a PR early to trigger CI and get visibility before the work is complete
- You are working in progress and want to share context with teammates
- You want the PR to appear in the repository's pull request list for planning purposes without it being up for review yet

---

## The Pull Request Interface

A pull request has several tabs:

**Conversation:** The main discussion thread. Shows the PR description, all review comments, status checks, labels, milestone and other metadata. New timeline events (commits pushed, reviews submitted, checks passed) appear here in chronological order.

**Commits:** Lists every commit included in the pull request. Click any commit to see its individual diff. Useful for understanding the history of how the change evolved.

**Checks:** Shows the results of all automated checks - GitHub Actions workflows, third-party CI tools and any other status checks configured for the repository.

**Files changed:** The full diff of every file modified by the pull request. This is where code review happens. Features:

- **Diff view vs Rich diff:** Toggle between the standard line diff and a rendered view for Markdown and images
- **Split vs Unified view:** Split shows old and new side by side; unified shows them interleaved
- **File filter:** Hide specific file types (e.g., hide all `*.lock` files to focus on meaningful changes)
- **Viewed toggle:** Mark files as viewed to track your review progress
- **Review comments:** Click any line to add a comment. Click and drag to select multiple lines

---

## Reviewing a Pull Request

Any user with read access to a repository can comment on a PR. Users with write access can submit formal reviews (approve, comment or request changes).

**Starting a review:**

1. Go to the **Files changed** tab
2. Read through the diff
3. To comment on a specific line: hover over the line number until a blue `+` appears, click it
4. To comment on a range: click the first line number and drag to the last
5. Write your comment
6. Click **Start a review** (to batch comments) or **Add single comment** (to comment immediately)

**Batching review comments:**

Use **Start a review** rather than **Add single comment** for all your comments except the last one. This prevents the author receiving a notification for every single line comment. All batched comments are submitted together when you finalise the review.

**Finalising a review:**

Click **Review changes** (top right of Files changed):
- Write an overall review summary
- Choose: **Comment** (general feedback, not approving or blocking), **Approve** (this is good to merge), or **Request changes** (there are issues that must be addressed before merge)
- Click **Submit review**

---

## Suggested Changes

When reviewing code, you can suggest specific text changes instead of just describing what should change. The author can accept your suggestion with a single click - GitHub automatically commits the change.

**Creating a suggestion:**

In a review comment, click the **±** icon (or use the keyboard shortcut `Ctrl+G` / `Cmd+G`) to insert a suggestion block:

````markdown
```suggestion
The replacement code goes here
```
````

The suggestion shows a diff of the current code vs your proposed replacement.

**Accepting a suggestion:**

The PR author clicks **Commit suggestion** on any suggestion comment. To accept multiple suggestions at once, click **Add suggestion to batch** on each, then **Commit suggestions** to apply them all in one commit.

Suggestions can only modify the lines shown in the diff - you cannot suggest adding lines in a different part of the file through this mechanism.

---

## Requesting Changes

When you submit a review with **Request changes**, the pull request is blocked from merging (if branch protection requires approvals and dismisses stale reviews). The author receives a notification that changes are needed.

After the author pushes new commits addressing the feedback:

- The reviewer revisits the PR and reviews the new commits
- If satisfied, submits an **Approve** review
- The previous **Request changes** review is superseded

**Dismissing a review:**

Repository admins and maintainers can dismiss stale reviews (with a reason) if the reviewer is unavailable or the review is no longer relevant.

---

## Resolving Review Comments

As the pull request author, you address each review comment by either:

1. **Making the requested change** - push a new commit with the fix
2. **Explaining why you disagree** - reply to the comment with your reasoning
3. **Accepting a suggestion** - click Commit suggestion

Once a discussion thread is resolved, click **Resolve conversation** to collapse and mark it done. Resolved conversations can be reopened if needed.

> [!NOTE]
> Branch protection can require all conversations to be resolved before merging. Reviewers can mark conversations as resolved as well as authors.

---

## Merge Methods

GitHub supports three merge methods. Repository admins can enable or disable each in Settings → General.

### Merge commit (Create a merge commit)

```
Before:         After:
main: A-B-C     main: A-B-C-M
feature:   D-E        |     ↑
                      D-E---
```

Creates a merge commit `M` that has two parents - the last commit on `main` and the last commit on the feature branch. The feature branch commits (`D`, `E`) are preserved with their original hashes and timestamps. The history shows the exact branching and merging that occurred.

**Use when:** You want to preserve the full, accurate history of when branches diverged and merged. Good for long-lived feature branches and when the individual commits are meaningful.

### Squash and merge

```
Before:         After:
main: A-B-C     main: A-B-C-S
feature:   D-E
```

All commits from the feature branch are squashed into a single new commit `S` on `main`. The individual commits `D` and `E` are discarded. The new squashed commit contains all the changes but appears as one entry in the history.

**Use when:** The feature branch has many messy work-in-progress commits that do not need to be preserved. The final result is a clean linear history where each PR becomes one logical commit.

### Rebase and merge

```
Before:         After:
main: A-B-C     main: A-B-C-D'-E'
feature:   D-E
```

Each commit from the feature branch is replayed onto `main` as new commits (`D'`, `E'`) with new hashes. Linear history, individual commits preserved, but no merge commit.

**Use when:** You want a clean linear history AND you want to preserve the individual commits from the feature branch. Note: commits get new hashes, so you lose the original commit timestamps.

**Choosing a default merge method:**

Set in **Settings → General → Pull Requests**. You can enable multiple methods and let authors choose per PR.

---

## Auto-merge

Auto-merge automatically merges a pull request when all required status checks pass and all required reviews are approved - without anyone having to come back and click merge.

**Enable auto-merge on a PR:**

1. The repository must have auto-merge enabled in **Settings → General**
2. At least one branch protection rule must exist (auto-merge requires something to wait for)
3. You must have write access
4. Click **Enable auto-merge** in the PR's merge section
5. Choose the merge method and confirm

Once enabled, the PR merges automatically the moment all requirements are satisfied. A banner on the PR shows "Auto-merge enabled" and the conditions being waited for.

**Disable auto-merge:**

Click **Disable auto-merge** on the PR. The PR reverts to requiring manual merging.

---

## Checking out a PR Locally

To test or build a pull request's code on your local machine:

**Via GitHub CLI (recommended):**

```bash
gh pr checkout 42
```

This creates a local branch with the PR's code and switches to it. When done:

```bash
git switch main
```

**Via Git directly:**

```bash
git fetch origin pull/42/head:pr-42
git switch pr-42
```

This fetches the PR's commits into a new local branch called `pr-42`.

**Via GitHub Desktop:**

From the **Current Branch** dropdown → **Pull requests** tab → click the PR to check it out.

---

## Pull Request Templates

A pull request template pre-fills the description field when a new PR is created.

**Default template location:** `.github/pull_request_template.md`

**Example template:**

```markdown
## Summary
<!-- What does this PR do? Why is it needed? -->

## Changes
<!-- List the specific changes made -->
- 

## Testing
<!-- How have you tested this? What should reviewers test? -->
- [ ] Manual testing completed
- [ ] Unit tests pass
- [ ] No new warnings

## Screenshots
<!-- For UI changes, include before/after screenshots -->

## Related issues
<!-- Link issues using: Closes #N, Fixes #N, Resolves #N -->
```

**Multiple templates:**

Create `.github/PULL_REQUEST_TEMPLATE/` directory with multiple `.md` files. Select a template by adding `?template=filename.md` to the PR URL - there is no built-in dropdown for selecting among multiple templates.

---

## Status Checks and Required Reviews

Branch protection rules and rulesets can require:

**Required status checks:** Specific GitHub Actions workflows, third-party CI tools or other checks must pass before the PR can be merged. If a check fails, the merge button is greyed out.

Configure which checks are required in **Settings → Branches → [ruleset or branch protection rule] → Require status checks to pass**.

**Required reviews:** A minimum number of approvals from specified reviewers before merging. Options include:

- Number of required approvals (1-6)
- Require review from code owners
- Dismiss stale approvals when new commits are pushed
- Require review from someone other than the author

**Required conversation resolution:** All review discussion threads must be marked as resolved before merging.

**Require signed commits:** All commits in the PR must have verified GPG or SSH signatures.

When any of these requirements are not met, GitHub shows the merge section as blocked with a clear explanation of what is still needed.

---

## Merge Queues

Merge queues (available on Team and Enterprise plans) solve a specific problem in high-throughput repositories: the race condition where multiple PRs are approved and pass CI against the current `main`, but merging them in quick succession breaks `main` because they conflict with each other.

With a merge queue, approved PRs join a queue instead of merging immediately. The queue merges them one at a time after running CI against the actual merge result (the PR combined with everything already in the queue ahead of it).

**Enable a merge queue:**

Add a "Merge Queue" rule to a branch's ruleset or branch protection in repository settings.

**Using the merge queue:**

Authors click **Merge when ready** (instead of **Merge**). The PR joins the queue and is merged automatically when it reaches the front and passes CI.

---

## Reverting a Merged Pull Request

If a merged pull request caused a problem, you can create a revert PR directly from GitHub.

**How to revert:**

1. Open the merged pull request
2. Click **Revert** at the bottom of the conversation
3. GitHub creates a new branch with a revert commit and opens a pre-filled PR
4. Review and merge the revert PR

The revert creates a new commit that undoes all the changes from the original PR. The original PR's history is preserved.

> [!NOTE]
> If the original PR was merged with squash and merge, the revert undoes the single squash commit. If merged with rebase and merge, individual revert commits are created for each original commit.

---

## Pull Requests from Forks

Pull requests from forks work the same way as branch-to-branch PRs but with one important security difference: **workflows triggered by `pull_request` events from forks do not have access to repository secrets.**

This prevents a malicious contributor from submitting a PR that reads your deployment keys, API tokens or other sensitive values via Actions.

For workflows that need secrets (e.g., to run integration tests against a real service), use the `pull_request_target` event instead - but be very careful, as this runs in the context of the base repository and does have access to secrets. Thoroughly validate inputs before using them.

Fork PRs behave slightly differently:

- GitHub Actions workflows show **"Require approval"** for first-time contributors - a maintainer must approve the workflow run before it executes
- The **Files changed** tab shows the diff against the base branch
- Merging creates a commit in the upstream repository

---

## GitHub CLI for Pull Requests

The GitHub CLI provides full pull request management from the terminal.

**Create:**

```bash
gh pr create --title "add: feature" --body "Description" --reviewer USERNAME
gh pr create --draft
gh pr create --web    # open in browser
```

**List and view:**

```bash
gh pr list                         # list open PRs
gh pr list --state merged          # list merged PRs
gh pr list --author YOUR_USERNAME  # your PRs
gh pr view 42                      # view PR #42
gh pr view 42 --web                # open in browser
```

**Review:**

```bash
gh pr review 42 --approve
gh pr review 42 --request-changes --body "Please fix the tests"
gh pr review 42 --comment --body "Looks good, one small thing..."
```

**Merge:**

```bash
gh pr merge 42
gh pr merge 42 --merge      # merge commit
gh pr merge 42 --squash     # squash and merge
gh pr merge 42 --rebase     # rebase and merge
gh pr merge 42 --delete-branch  # delete branch after merge
gh pr merge 42 --auto           # enable auto-merge
```

**Check out locally:**

```bash
gh pr checkout 42
```

**Status checks:**

```bash
gh pr checks 42    # show all check results
```

**Mark ready or close:**

```bash
gh pr ready 42     # mark draft as ready for review
gh pr close 42     # close without merging
```

---

## Try It Yourself

**Step 1.** Set up a practice repository with at least two commits:

```bash
git switch -c feature/greeting
echo "Hello from the feature branch!" >> README.md
git add README.md && git commit -m "update: add greeting to README"
git push -u origin feature/greeting
```

**Step 2.** Create the pull request on GitHub:

1. Go to your repository on GitHub
2. Click **Compare & pull request** in the yellow banner
3. Title: `update: add greeting to README`
4. Body:

```markdown
## Summary
Adds a greeting message to the README.

## Related issues
<!-- No related issues for this practice PR -->

## Testing
- [x] Manually verified text appears correctly in the README preview
```

5. Assign yourself as the reviewer and assignee
6. Click **Create pull request**

**Step 3.** Review the pull request:

1. Click the **Files changed** tab
2. Click the `+` on the changed line to add a comment: `"Looks good - clear and welcoming."`
3. Click **Review changes** → select **Approve** → **Submit review**

**Step 4.** Merge the pull request:

1. Click **Merge pull request** → **Confirm merge**
2. Click **Delete branch**

**Step 5.** Verify the merge:

```bash
git switch main
git pull
cat README.md
```

Your greeting should appear.

**Step 6 (Optional) - Practice reverting:**

1. Go to the merged PR on GitHub
2. Click **Revert**
3. Review the automatically created revert PR
4. Merge the revert PR
5. Pull locally and verify the greeting is gone

---

## Common Mistakes

**Opening PRs directly from `main`.**

If you push commits to `main` and then open a PR from `main` to `main`, GitHub will show nothing to merge. Always create a feature branch before starting work, even for small changes.

**Not writing a meaningful description.**

A PR description with only the commit message title gives reviewers no context. Explain what changed, why it changed and how to test it. Screenshots for UI changes are especially valuable.

**Requesting too many reviewers.**

Requesting 6 reviewers for a one-line change delays the PR unnecessarily. Request the minimum number of people who need to see the change. For most PRs, 1-2 reviewers is appropriate.

**Leaving review conversations unresolved.**

If branch protection requires resolved conversations, unresolved threads block merging. After addressing feedback, mark conversations as resolved. If you disagree with a comment, explain why in the thread before resolving.

**Merging without waiting for CI to complete.**

Bypassing required status checks by merging before they finish or dismissing failing checks leads to broken code on `main`. If a check is failing, understand why before merging.

**Not deleting the branch after merging.**

After a PR is merged, the branch serves no further purpose. Leaving dozens of merged branches makes the repository messy. Enable "Automatically delete head branches" in Settings → General, or delete manually after each merge.

**Large pull requests that are impossible to review.**

A PR with 3,000 changed lines across 40 files is extremely difficult to review well. Break large changes into smaller, focused PRs. A good rule of thumb: if a PR takes more than one hour to review, it is probably too large.

---

## Summary

- A pull request proposes merging commits from one branch into another, enabling code review and discussion before the merge
- Create PRs from GitHub.com, GitHub Desktop or `gh pr create`
- Draft PRs signal work in progress and do not request reviews from CODEOWNERS
- The **Files changed** tab shows the full diff where inline review comments can be added
- Review types: Comment (no vote), Approve (ready to merge), Request changes (blocking)
- Suggested changes let reviewers propose exact code replacements that authors can accept with one click
- Three merge methods: merge commit (preserves all history), squash (one clean commit), rebase (linear history with individual commits)
- Auto-merge merges automatically when all requirements are satisfied
- Closing keywords (`Closes #N`, `Fixes #N`, `Resolves #N`) in the PR description auto-close linked issues on merge
- PR templates pre-fill the description for consistency across contributions
- Branch protection rules enforce required reviews, status checks and signed commits before merging

---

## Sources and Further Reading

- [About pull requests - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) - official pull requests reference
- [Reviewing changes in pull requests - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests) - complete review workflow
- [About merge methods - GitHub Docs](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges) - detailed comparison of all three merge methods
- [Creating a pull request template - GitHub Docs](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository) - template setup guide
- [Merge queue - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue) - merge queue setup and usage
- [gh pr - GitHub CLI manual](https://cli.github.com/manual/gh_pr) - complete CLI reference for pull requests

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

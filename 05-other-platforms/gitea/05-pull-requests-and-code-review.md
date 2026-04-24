# Pull Requests and Code Review in Gitea

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Pull requests in Gitea are the primary mechanism for proposing, reviewing and merging code changes. They provide a structured workflow where changes are examined before they enter shared branches. Gitea's pull request system is modelled closely on GitHub's - if you have used GitHub PRs, Gitea will feel immediately familiar. This file covers the full lifecycle: creating pull requests, reviewing code, managing feedback, protected branches and merge strategies.

---

## Table of Contents

1. [Creating a pull request](#creating-a-pull-request)
2. [The pull request interface](#the-pull-request-interface)
3. [Code review: reading diffs and commenting](#code-review-reading-diffs-and-commenting)
4. [Reviews and approvals](#reviews-and-approvals)
5. [Merge strategies](#merge-strategies)
6. [Protected branches](#protected-branches)
7. [Pull request templates](#pull-request-templates)
8. [Auto-merge](#auto-merge)
9. [Closing and abandoning pull requests](#closing-and-abandoning-pull-requests)
10. [Cross-repository pull requests (forks)](#cross-repository-pull-requests-forks)
11. [Try It Yourself](#try-it-yourself)
12. [Common Mistakes](#common-mistakes)
13. [Summary](#summary)
14. [Sources](#sources)

---

## Creating a pull request

### From the web interface

**After pushing a branch**, Gitea shows a banner on the repository page suggesting you create a pull request. Click it.

**From the Branches list:**

1. Go to **Code** > **Branches**
2. Find your branch
3. Click **New Pull Request**

**From the Pull Requests page:**

1. Click **Pull Requests** in the repository navigation
2. Click **New Pull Request**
3. Select the base branch (what to merge into) and the compare branch (your changes)

### The pull request form

**Title**: A concise description of what the PR does. This becomes the merge commit message for squash merges. Write it in the present tense: "Add OAuth2 login" not "Added OAuth2 login".

**Content / Description**: Context for reviewers. Explain:

- What the change does
- Why it is needed
- How to test it
- Any trade-offs or known issues

Markdown is fully supported including code blocks, images and task lists (`- [ ]` checkboxes).

**Reviewers**: Add users to notify and request review from.

**Assignees**: Who is responsible for completing this PR (usually the author).

**Labels**: Tags for categorising the PR (e.g. `bug`, `enhancement`, `breaking-change`).

**Milestone**: Link to a project milestone.

**Projects**: Associate with a Gitea project board.

**Draft**: Mark as draft to signal it is not ready for review.

### Creating via the CLI or API

```bash
# Via the Gitea API
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: token YOUR_TOKEN" \
  -d '{
    "title": "Add user authentication",
    "body": "Implements JWT-based authentication for all API endpoints.\n\n## Testing\n- Run `make test-auth`\n- Manually test login flow",
    "head": "feature/user-auth",
    "base": "main",
    "assignees": ["yourusername"],
    "labels": [1, 2]
  }' \
  https://gitea.example.com/api/v1/repos/username/repo/pulls
```

---

## The pull request interface

### Overview tab

The default view showing:

- PR title, description and metadata
- Reviewer list and their review status
- CI/CD build status (if Gitea Actions is configured)
- The activity feed: all comments, commits and status changes in chronological order
- Merge status and any conflicts
- The **Merge Pull Request** button (active when all requirements are met)

### Commits tab

All commits included in this PR. Each links to the commit's diff. Useful for reviewing the history of how the feature was built.

### Files Changed tab

The diff of all changes. This is the primary code review workspace:

- Green lines: additions
- Red lines: deletions
- Context lines: unchanged surrounding code

Toggle between **Unified diff** (additions and deletions in one column) and **Split diff** (old on the left, new on the right) using the view selector.

### Conflict indicator

If the PR branch has diverged from the base branch and cannot be merged automatically, Gitea shows a conflict warning. The PR author must resolve conflicts before the PR can be merged:

```bash
git checkout feature/my-feature
git fetch origin
git merge origin/main     # or: git rebase origin/main
# resolve conflicts in your editor
git add resolved-file.txt
git commit -m "Resolve merge conflicts"
git push origin feature/my-feature
```

---

## Code review: reading diffs and commenting

### Line-level comments

To comment on a specific line:

1. Hover over the line in the **Files Changed** tab
2. Click the blue **+** icon that appears on the left
3. Write your comment
4. Click **Comment** (immediate) or add to a pending review batch

Line comments are the standard way to give specific, localised feedback. They appear in the diff at the relevant line and in the activity feed.

### Multi-line comments

Click and drag across multiple lines to select a range, then click **+** to comment on the entire selection. Useful for commenting on blocks of code rather than individual lines.

### Markdown in comments

All comments support full Markdown:

````markdown
**This needs a null check:**

```go
if user == nil {
    return ErrNotFound
}
```
````

See the [error handling docs](https://docs.example.com/errors) for the pattern we use.

`````

### Suggestions

Gitea supports **code suggestions** - propose specific code changes that the author can apply with one click:

````markdown
```suggestion
func validateUser(user *User) error {
    if user == nil {
        return ErrNilUser
    }
    return nil
}
`````

`````

The suggestion appears as a diff in the review comment. The PR author sees an **Apply suggestion** button that commits the change directly.

### Replying to comments

Click **Reply** on any comment to start a threaded discussion. This keeps related feedback in one thread rather than creating separate top-level comments.

### Resolving conversations

When a review comment has been addressed (change made, explanation given or declined with reasoning), mark it as resolved:
1. Open the comment
2. Click **Resolve Conversation**

Resolved conversations collapse in the diff but remain visible. They show as resolved in the reviewer's view, signalling the feedback was handled.

---

## Reviews and approvals

### Submitting a review

1. In the **Files Changed** tab, click **Review changes**
2. Add an optional summary comment
3. Select your verdict:
   - **Comment**: general feedback without a verdict
   - **Approve**: the code is ready to merge
   - **Request changes**: the code needs work before merging
4. Click **Submit review**

Your review status appears in the PR sidebar next to your name.

### Required reviewers

In Gitea, you can configure **required reviewers** on protected branches - specific users or teams who must approve before a PR can be merged. This is configured in repository settings under **Protected Branches**.

### Review states

| State | Meaning |
|---|---|
| Review requested | Reviewer has been asked but not responded |
| Commented | Reviewer left comments but no verdict |
| Approved | Reviewer approves the PR |
| Changes requested | Reviewer wants changes before merging |

A PR can be merged when:
- All required reviewers have approved
- No reviewer has an active "changes requested" status (or that reviewer has been dismissed)
- All configured protected branch requirements are met

### Dismissing a review

If a reviewer's "Changes requested" is no longer relevant (they left the team, the concern was addressed differently), an admin can dismiss the review:
1. Find the reviewer in the PR sidebar
2. Click the dismiss icon next to their status
3. Provide a reason for dismissal

The review is marked as dismissed and no longer blocks the merge.

---

## Merge strategies

When completing a pull request, Gitea offers up to three merge strategies (enabled by repository settings):

### Merge commit (Create a merge commit)

```
Before:
main:     A --- B --- C
feature:              D --- E --- F

After:
main:     A --- B --- C --- M (merge commit, two parents)
```

Preserves the complete branch history. The merge commit shows when the feature was integrated. `git log --graph` shows the full branching structure.

**Best for:** Feature branches where the individual commits tell a meaningful story.

### Squash and merge

```
Before:
main:     A --- B --- C
feature:              D --- E --- F

After:
main:     A --- B --- C --- S (one squashed commit)
```

All commits from the PR become a single commit on the base branch. Clean linear history. Individual commit history from the feature branch is lost after merge (though the PR preserves the discussion).

**Best for:** Teams wanting clean linear history or PRs with many "work in progress" commits that do not merit preservation.

### Rebase and merge

```
Before:
main:     A --- B --- C
feature:              D --- E --- F

After (all commits replayed on top of main):
main:     A --- B --- C --- D' --- E' --- F'
```

Each commit from the PR is replayed on top of the base branch. Produces linear history while preserving all individual commits. The commits get new SHAs (they are replayed, not moved).

**Best for:** Teams with a discipline of clean, meaningful commits who want full history without merge commits.

### Configuring allowed merge types

1. Go to repository **Settings** > **Repository**
2. Under **Pull Request Settings**, enable or disable merge strategies
3. Set the default merge strategy

---

## Protected branches

Protected branches prevent direct pushes and enforce review requirements before merging.

### Configuring branch protection

1. Go to repository **Settings** > **Protected Branches**
2. Enter the branch name pattern:
   - `main` - protects exactly the `main` branch
   - `release/*` - protects all branches starting with `release/`
3. Configure the rules:

**Enable push protection**: blocks direct pushes to the branch. All changes must go through pull requests.

**Enable merge protection**: blocks merging PRs unless the configured requirements are met.

**Required approvals**: minimum number of approvals before merging is allowed.

**Required reviewers**: specific users who must approve. Enter usernames or team names.

**Dismiss stale approvals**: when new commits are pushed, existing approvals are reset. Prevents approving then making unreviewed changes.

**Require status checks**: CI builds must pass before merging. Enter the status check names expected from Gitea Actions or external CI systems.

**Restrict push to**: only specific users or teams can push to this branch (even without PR protection, other users are blocked from pushing).

**Restrict force push**: prevents `git push --force` on this branch.

### Effective protection example

```
Branch: main

Push allowed: only Admins
Merge allowed: yes
Required approvals: 2
Dismiss stale approvals: yes
Required status checks: [gitea-ci/test, gitea-ci/build]
Restrict force push: yes
```

This configuration means: nobody can push directly to `main`, PRs need 2 approvals, approvals reset when code changes and all CI checks must pass.

---

## Pull request templates

A PR template pre-fills the description when creating a new pull request. This prompts authors to include relevant context consistently.

### Creating a PR template

Create a file at `.gitea/PULL_REQUEST_TEMPLATE.md` in your repository:

```markdown
## Summary

<!-- What does this PR do? -->

## Type of change

- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing

<!-- How has this been tested? -->

- [ ] Unit tests added or updated
- [ ] Manual testing completed
- [ ] Tested on staging environment

## Checklist

- [ ] Code follows the project style guidelines
- [ ] Self-review completed
- [ ] Documentation updated if needed
- [ ] No new security vulnerabilities introduced

## Related issues

<!-- Link any related issues: Closes #123 -->
```

Commit and push this file to the default branch. When anyone creates a PR on this repository, the template text appears in the description box.

### Multiple templates

Gitea supports multiple PR templates. Create a directory `.gitea/PULL_REQUEST_TEMPLATE/` and add multiple `.md` files. When creating a PR, contributors choose from the available templates.

---

## Auto-merge

Auto-merge enables a PR to be merged automatically as soon as all required conditions are met: approvals received, status checks passed, conflicts resolved.

### Enabling auto-merge on a PR

1. Open the PR
2. Click the dropdown next to the **Merge Pull Request** button
3. Select **Auto-merge**
4. Choose the merge strategy
5. Confirm

The PR now shows an "Auto-merge enabled" indicator. When the last required condition is satisfied, Gitea merges the PR automatically.

**Use case**: You have submitted a PR, it has been approved, and you are waiting for CI to finish. Enable auto-merge and go do something else. When CI passes, the PR merges without you needing to return.

---

## Closing and abandoning pull requests

### Closing a PR without merging

If a PR is not going to be merged (wrong approach, feature cancelled, superseded by another PR):

1. Open the PR
2. Scroll to the bottom
3. Click **Close Pull Request**
4. Optionally leave a comment explaining why

Closed PRs remain visible with "Closed" status. They are not deleted. The branch is not automatically deleted.

### Reopening a closed PR

A closed PR can be reopened if the source branch still exists:
1. Open the closed PR (filter by "Closed" in the PR list)
2. Click **Reopen Pull Request**

### Automatic closure

PRs close automatically when:
- The source branch is deleted (the PR is closed as abandoned)
- The PR is merged

---

## Cross-repository pull requests (forks)

When contributing to a project you do not have write access to, fork the repository, make your changes and submit a PR from your fork to the original.

### Creating a cross-fork PR

1. Fork the repository (click **Fork** on the original)
2. Clone your fork and make changes:
```bash
git clone https://gitea.example.com/your-username/original-repo.git
cd original-repo
git checkout -b feature/my-contribution
# make changes
git push origin feature/my-contribution
```
3. Go to the **original** repository (not your fork)
4. Click **New Pull Request**
5. Click **compare across forks**
6. Set:
   - **Base repository**: original repo, `main` branch
   - **Head repository**: your fork, `feature/my-contribution` branch
7. Submit

The original repository's maintainers review and can merge your PR.

---

## Try It Yourself

**Exercise 1 - Create a pull request**

```bash
# In your practice repository
git checkout -b feature/add-contributing-guide
echo "# Contributing\n\nWelcome contributors!" > CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "Add contributing guide"
git push origin feature/add-contributing-guide
```

1. Go to your repository in Gitea
2. Click the banner suggesting you create a PR
3. Fill in a title and description
4. Submit the PR

**Exercise 2 - Add a review comment with a suggestion**

1. Open the PR you created
2. Go to **Files Changed**
3. Hover over a line and click **+**
4. Write a comment with a code suggestion:
````markdown
I suggest updating this to:

```suggestion
# Contributing Guide
Welcome contributors! Please read this first.
```
`````

5. Submit the comment

**Exercise 3 - Configure branch protection**

1. Go to **Settings** > **Protected Branches**
2. Enter `main` as the branch name
3. Enable **Enable push protection**
4. Set **Required approvals** to 1
5. Save

Now try to push directly to main:

```bash
git checkout main
echo "direct push test" >> test.txt
git add test.txt
git commit -m "Direct push test"
git push origin main
# Should be rejected
```

**Exercise 4 - Add a PR template**

1. Create `.gitea/PULL_REQUEST_TEMPLATE.md` with a basic template
2. Commit and push to main
3. Create a new PR and see the template pre-filled in the description

---

## Common Mistakes

**Not explaining the reason for changes in the PR description**

An empty description or just "fixes bug" forces reviewers to infer the context from the diff alone. A good description saves everyone time. Write at least a sentence explaining what changed and why.

**Merging with unresolved conflicts without rebasing**

Gitea blocks merging when there are conflicts. Some teams try to resolve conflicts directly in the merge commit - this is harder to review and error-prone. Instead, rebase or merge the base branch into the feature branch, resolve conflicts locally, test, then push.

**Not keeping the PR focused**

A PR that changes the authentication system, adds a new UI component and fixes three unrelated bugs is hard to review well. Keep PRs focused on one concern. If you notice something to fix while working, make a separate PR.

**Ignoring review feedback**

When a reviewer requests changes, address each comment explicitly. Either make the change, explain why you disagree (and come to consensus), or mark the discussion as won't fix with a reason. Do not close comments without engaging with them.

**Forgetting to delete the source branch after merging**

Merged branches pile up. Enable the "delete branch after merge" option when completing a PR, or manually delete merged branches regularly.

---

## Summary

Gitea pull requests provide a structured code review workflow from branch creation to merge. Create PRs from the web interface after pushing a branch. The PR interface shows the diff, commit history, reviewer status, CI results and a conversation thread.

Code review happens in the **Files Changed** tab. Reviewers can comment on specific lines, suggest code changes and submit an overall verdict (approve, comment or request changes). Required reviewers and minimum approval counts are enforced by branch protection rules.

Three merge strategies are available: merge commit (preserves full history), squash (clean linear history) and rebase (linear with all commits). Configure which strategies are allowed in repository settings.

Protected branches block direct pushes and enforce review requirements: minimum approvals, specific required reviewers, required CI status checks and stale approval dismissal. PR templates pre-fill the description to prompt for consistent information.

Auto-merge queues a merge to happen automatically when all conditions are satisfied. Cross-fork PRs enable external contributors to submit changes without repository write access.

---

## Sources

- [Gitea: Pull requests documentation](https://docs.gitea.com/usage/pull-request)
- [Gitea: Protected branches](https://docs.gitea.com/usage/protected-branches)
- [Gitea: Code review](https://docs.gitea.com/usage/review)
- [Gitea: PR templates](https://docs.gitea.com/usage/pull-request-template)
- [Gitea: Auto-merge](https://docs.gitea.com/usage/auto-merge)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

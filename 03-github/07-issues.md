# Issues

**Difficulty:** 🟢 Beginner to 🟡 Intermediate | **Time:** 25 minutes

Issues are how work gets tracked on GitHub. Every bug report, feature request, task, question and improvement starts as an issue. They are the backbone of project management on GitHub - linking code changes to the problems they solve, organising work into milestones, assigning ownership and providing a permanent record of every decision made about a project.

---

## Table of Contents

- [What is an issue?](#what-is-an-issue)
- [Creating an issue](#creating-an-issue)
- [Issue fields in detail](#issue-fields-in-detail)
- [Writing good issues](#writing-good-issues)
- [Labels](#labels)
- [Assignees](#assignees)
- [Milestones](#milestones)
- [Issue types](#issue-types)
- [Sub-issues](#sub-issues)
- [Issue templates](#issue-templates)
- [Issue forms](#issue-forms)
- [Linking issues to pull requests](#linking-issues-to-pull-requests)
- [Closing issues via commit messages](#closing-issues-via-commit-messages)
- [Pinning issues](#pinning-issues)
- [Transferring issues](#transferring-issues)
- [Locking conversations](#locking-conversations)
- [Searching and filtering issues](#searching-and-filtering-issues)
- [Issues vs Discussions vs Projects](#issues-vs-discussions-vs-projects)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is an Issue?

An issue is a conversation thread attached to a repository that tracks a specific piece of work - a bug, a feature, a question, a task or anything else that needs attention. Issues are numbered sequentially (`#1`, `#2`, `#3`...) and that number never changes or gets reused.

Every issue has a state: **open** (work is not done) or **closed** (work is done or the issue is no longer relevant). Closing an issue does not delete it - it remains permanently accessible in the repository's history.

Issues are visible to everyone on a public repository. On private repositories, only collaborators with at least read access can see them.

---

## Creating an Issue

**From the GitHub web interface:**

1. Go to any repository
2. Click the **Issues** tab
3. Click **New issue**
4. Fill in the title and description
5. Set any additional fields in the right sidebar
6. Click **Submit new issue**

**From the GitHub CLI:**

```bash
gh issue create
```

This opens an interactive prompt. Or specify everything inline:

```bash
gh issue create --title "Fix: login redirect broken on mobile" --body "Steps to reproduce..." --label bug --assignee YOUR_USERNAME
```

**From a commit or pull request:**

Any `#NUMBER` reference in a commit message or pull request body automatically becomes a clickable link to that issue. You can also create a new issue from highlighted text in an existing issue or pull request - select text and a tooltip appears with "Create issue" as an option.

---

## Issue Fields in Detail

### Title

The title is the most important part of an issue. It should be specific enough that someone reading the issue list immediately understands what it is about.

**Good titles:**
- `Login button unresponsive on Safari 17 when cookies disabled`
- `Add dark mode support to the settings page`
- `Performance: dashboard takes 8+ seconds to load with 1000+ items`

**Poor titles:**
- `Bug`
- `Not working`
- `Feature request`
- `Help`

### Description (body)

Supports full **GitHub Flavoured Markdown** - headings, bold, italic, code blocks, tables, images, task lists, links and more. Write as much context as the issue needs.

For **bug reports**, include: steps to reproduce, expected behaviour, actual behaviour, environment (OS, browser version, app version) and screenshots or screen recordings if relevant.

For **feature requests**, include: the problem you are trying to solve, the proposed solution, alternatives you considered and any mockups.

**Task lists in issue bodies:**

```markdown
- [x] Research existing approaches
- [ ] Write the implementation
- [ ] Add tests
- [ ] Update documentation
```

Checkboxes are interactive - anyone with write access can tick them. The issue list shows completion progress (e.g., "2 of 4 tasks").

### Labels

Categorise the issue with one or more labels. See [Labels](#labels) section below.

### Assignees

Specify who is responsible for working on the issue. See [Assignees](#assignees) section below.

### Projects

Add the issue to a GitHub Project board for planning and tracking. The issue appears in the project's board, table or roadmap view.

### Milestones

Group the issue with a milestone (e.g., `v2.0 Release`). See [Milestones](#milestones) below.

### Issue type

A structured category beyond labels - Bug, Feature, Task etc. Available at the organisation level. See [Issue types](#issue-types) below.

---

## Writing Good Issues

Good issues are specific, actionable and self-contained. Anyone reading the issue should be able to understand the problem and context without needing to ask follow-up questions.

**For bug reports, use this structure:**

```markdown
## Description
A clear and concise description of what the bug is.

## Steps to reproduce
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

## Expected behaviour
A clear description of what you expected to happen.

## Actual behaviour
What actually happens instead.

## Environment
- OS: Windows 11 / macOS 14 / Ubuntu 22.04
- Browser: Chrome 124 / Firefox 125 / Safari 17
- App version: v2.1.3

## Screenshots
If applicable, add screenshots here.
```

**For feature requests:**

```markdown
## Problem
Describe the problem this feature would solve. Focus on the user need,
not the solution. Example: "I always have to manually export data to CSV
before I can use it in Excel."

## Proposed solution
Describe your preferred solution. Be as specific as possible.

## Alternatives considered
Describe any alternative solutions or features you have considered.

## Additional context
Any mockups, examples from other tools, or related issues.
```

---

## Labels

Labels are coloured tags that categorise and filter issues and pull requests. They appear in the issue list and make it easy to find all issues of a particular type.

**Default labels on every new GitHub repository:**

| Label | Colour | Meaning |
|---|---|---|
| `bug` | Red | Something is not working correctly |
| `documentation` | Blue | Improvements or additions to documentation |
| `duplicate` | Grey | This issue already exists |
| `enhancement` | Purple | New feature or improvement request |
| `good first issue` | Green | Good for contributors new to the project |
| `help wanted` | Teal | Extra attention or contribution is needed |
| `invalid` | Yellow | This does not seem right or is out of scope |
| `question` | Pink | Further information is requested |
| `wontfix` | White | This will not be worked on |

**Creating custom labels:**

Go to **Issues → Labels → New label**. Give it a name, description and hex colour. Custom labels can represent anything useful for your workflow - `priority: high`, `area: backend`, `status: blocked`, `type: refactor` etc.

**Applying labels:**

Click the gear icon next to **Labels** in the issue's right sidebar. You can apply multiple labels to a single issue.

**Filtering by label:**

In the issue list, click **Labels** in the filters bar and select one or more labels. Or use the search bar:

```
label:bug label:"help wanted"
```

---

## Assignees

Assignees indicate who is responsible for working on the issue. A single issue can have multiple assignees.

**Assigning from the sidebar:** Click the gear icon next to **Assignees** and type a username or select from the list.

**Self-assigning:** Click **assign yourself** below the Assignees section.

**Assigning via CLI:**

```bash
gh issue edit 42 --add-assignee YOUR_USERNAME
```

**Who can be assigned:**

On public repositories, anyone with repository access can be assigned. On private repositories, only collaborators. On organisation repositories, organisation members and outside collaborators with access.

Assignees receive notifications about issue activity.

---

## Milestones

Milestones group related issues and pull requests around a shared goal or deadline - typically a version release or a sprint.

**Creating a milestone:**

1. Go to **Issues → Milestones → New milestone**
2. Set a **title** (e.g., `v2.0 Release`)
3. Set a **due date** (optional)
4. Add a **description** (optional)
5. Click **Create milestone**

**Associating issues with a milestone:**

Click the gear icon next to **Milestone** in the issue sidebar and select the milestone.

**Milestone progress:**

Each milestone shows a completion percentage based on how many of its issues and pull requests are closed vs open. Navigate to **Issues → Milestones → [milestone name]** to see the full list and progress bar.

**Filtering by milestone:**

In the issue list, click **Milestones** in the filter bar or use the search:

```
milestone:"v2.0 Release"
```

---

## Issue Types

Issue types are organisation-level categories that provide a structured way to classify issues beyond labels. They were made generally available in April 2025.

**Default issue types** (at the organisation level): Bug, Feature, Task - though organisation admins can create custom types.

**How types differ from labels:** Labels are per-repository and free-form. Issue types are organisation-wide, consistent and enforce a common vocabulary across all repositories in the organisation. Types also integrate with Projects and dashboards for organisation-level reporting.

**Adding a type to an issue:** Click the gear icon next to **Type** in the issue sidebar and select the appropriate type.

---

## Sub-issues

Sub-issues allow you to break a large issue into smaller, trackable child issues. This replaced the older Tasklists feature which was sunset in April 2025.

**Sub-issues became generally available in April 2025.**

- A parent issue can have up to **100 sub-issues**
- Sub-issues can nest up to **8 levels deep**
- The parent issue shows a progress indicator: `3/7 sub-issues completed`
- Each sub-issue is a full issue with its own number, labels, assignees and milestones
- The sub-issue hierarchy is visible in GitHub Projects

**Creating sub-issues:**

1. Open a parent issue
2. Click **Add sub-issue** in the issue's right sidebar or body
3. Either create a new issue or link an existing one

**Viewing the hierarchy:**

GitHub Projects shows parent-child relationships in the table and board views, with the ability to expand/collapse hierarchies.

---

## Issue Templates

Issue templates provide pre-filled structure for issue creators. When a repository has templates, the **New issue** button shows a template chooser rather than a blank form.

**Creating templates:**

Templates live in `.github/ISSUE_TEMPLATE/`. Create one or more `.md` files with YAML front matter:

```markdown
---
name: Bug report
about: Report a bug or unexpected behaviour
title: '[BUG] '
labels: bug
assignees: ''
---

## Description
<!-- A clear and concise description of the bug -->

## Steps to reproduce
1.
2.
3.

## Expected behaviour

## Actual behaviour

## Environment
- OS:
- Browser:
- Version:
```

**Template config file:**

Create `.github/ISSUE_TEMPLATE/config.yml` to customise the template chooser:

```yaml
blank_issues_enabled: false
contact_links:
  - name: Community Support
    url: https://github.com/YOUR_ORG/YOUR_REPO/discussions
    about: Ask questions and get help from the community
  - name: Security Vulnerabilities
    url: https://github.com/YOUR_ORG/YOUR_REPO/security/advisories/new
    about: Report security vulnerabilities privately
```

Setting `blank_issues_enabled: false` forces users to choose a template. Adding `contact_links` directs users to external resources for specific use cases.

---

## Issue Forms

Issue forms (`.yml` files in `.github/ISSUE_TEMPLATE/`) replace Markdown templates with structured web forms. They produce consistent, machine-readable data and validate inputs before submission.

**Example issue form:**

```yaml
name: Bug Report
description: Report a bug or unexpected behaviour
title: "[Bug]: "
labels: ["bug"]
assignees: []
body:
  - type: markdown
    attributes:
      value: |
        Thanks for taking the time to fill out this bug report.

  - type: input
    id: version
    attributes:
      label: Version
      description: Which version of the app are you using?
      placeholder: "e.g. 2.1.3"
    validations:
      required: true

  - type: textarea
    id: description
    attributes:
      label: Description
      description: A clear description of the bug
      placeholder: Tell us what happened
    validations:
      required: true

  - type: dropdown
    id: os
    attributes:
      label: Operating system
      options:
        - Windows
        - macOS
        - Linux
    validations:
      required: true

  - type: checkboxes
    id: terms
    attributes:
      label: Code of conduct
      options:
        - label: I have read and agree to the Code of Conduct
          required: true
```

**Available field types:** `input`, `textarea`, `dropdown`, `checkboxes`, `markdown` (static text/instructions).

The rendered form validates required fields before submission, preventing empty or incomplete bug reports.

---

## Linking Issues to Pull Requests

Issues and pull requests can be linked so that merging the pull request automatically closes the linked issue.

**Automatic closing with keywords:**

Add a keyword followed by the issue reference in the pull request's **description** (not the title):

```
Fixes #42
Closes #42
Resolves #42
```

Variations (all work identically):
- `fix`, `fixes`, `fixed`
- `close`, `closes`, `closed`
- `resolve`, `resolves`, `resolved`

When the pull request is merged into the **default branch**, all linked issues are automatically closed.

**Cross-repository references:**

```
Fixes OWNER/REPO#42
```

**Linking multiple issues:**

```
Closes #42, fixes #43, resolves #44
```

**Manual linking (without auto-close):**

Use `#42` without a keyword to create a reference link without triggering auto-close. The issue and PR will show each other in their timelines.

**Linking from the sidebar:**

In a pull request, click the gear icon next to **Development** in the right sidebar and search for the issue number or title to link it manually.

---

## Closing Issues via Commit Messages

You can also close issues with keywords in **commit messages** - not just pull request descriptions.

```bash
git commit -m "fix: resolve login redirect issue, closes #42"
```

This works when the commit is pushed to the **default branch** directly. If pushed to a non-default branch, the issue closes when that branch is merged.

---

## Pinning Issues

Pin up to **3 issues** to the top of the issue list. Pinned issues are always visible regardless of filters, making them ideal for: known issues users frequently ask about, issues that are critical priorities, contribution guides or welcome issues for new contributors.

**How to pin:**

Open an issue → three-dot menu (top right of the issue) → **Pin issue**.

**Unpin:** Same menu → **Unpin issue**.

---

## Transferring Issues

Move an issue from one repository to another within the same GitHub account or organisation.

**When to transfer:**

- You created an issue in the wrong repository
- A bug turned out to be in a dependency's repository rather than your own
- Splitting a monorepo and moving issues to the new repos

**How to transfer:**

Open the issue → three-dot menu → **Transfer issue** → search for the target repository → **Transfer issue**.

Transferred issues:
- Keep their content and all comments
- Get a new number in the destination repository
- Create a redirect from the original URL
- **Do not** keep labels, milestones or projects (these must be re-applied in the new repository)

---

## Locking Conversations

Lock a conversation to prevent new comments from non-collaborators. Useful for:

- Resolved issues getting reopened by unrelated comments
- Discussions that have become heated or off-topic
- Old issues where comments are no longer relevant

**How to lock:**

Open the issue → three-dot menu → **Lock conversation** → select a reason: **Off-topic**, **Too heated**, **Resolved** or **Spam** → **Lock**.

Collaborators with write access can still comment on locked issues. Non-collaborators see a notice and cannot comment.

**Unlock:** Same menu → **Unlock conversation**.

---

## Searching and Filtering Issues

The issue list supports powerful filtering and search.

**Filter bar options:**

- **Assignee:** `assignee:YOUR_USERNAME` or `no:assignee`
- **Label:** `label:bug` or `label:"help wanted"`
- **Milestone:** `milestone:"v2.0"`
- **Author:** `author:SOMEONE`
- **State:** `is:open` or `is:closed`
- **Type:** `type:bug`
- **No milestone:** `no:milestone`
- **Mentioned you:** `mentions:YOUR_USERNAME`
- **Review requested:** `review-requested:YOUR_USERNAME`

**Combining filters:**

```
is:open label:bug assignee:YOUR_USERNAME
```

**Search within issues:**

Type any text in the search bar to search issue titles and bodies:

```
is:open login redirect mobile
```

**Sort options:** Newest, oldest, most commented, least commented, recently updated, least recently updated, most reactions, least reactions.

**GitHub CLI filtering:**

```bash
gh issue list --label bug --assignee YOUR_USERNAME --state open
gh issue list --milestone "v2.0 Release"
```

---

## Issues vs Discussions vs Projects

These three tools overlap in purpose. Understanding which to use prevents confusion.

**Issues** are for: specific, actionable work items with a clear resolution path. A bug to fix, a feature to build, a task to complete. Issues are closed when the work is done.

**Discussions** are for: open-ended conversations that may not have a defined resolution. Q&A, brainstorming, community announcements, ideas that are not yet ready to become issues. Discussions can be converted to issues when they crystallise into actionable work.

**Projects** are for: planning and organising work across multiple issues and pull requests. Projects group issues into boards, tables and roadmaps with custom fields for tracking status, priority, team, sprint and more. Issues live inside repositories; Projects provide a cross-repository view.

The typical flow: an idea starts as a **Discussion** → becomes an **Issue** when it is well-defined → is tracked in a **Project** alongside other related issues.

---

## Try It Yourself

**Step 1.** Go to any repository you own on GitHub.

**Step 2.** Create a bug report issue:

- Click **Issues → New issue**
- Title: `Bug: README link to installation guide returns 404`
- Body: Write a short bug report following the structure from the Writing good issues section
- Add the label `bug`
- Assign it to yourself
- Click **Submit new issue**
- Note the issue number (e.g., `#1`)

**Step 3.** Create a feature request:

- Click **New issue**
- Title: `Feature: add search functionality to the project`
- Add the label `enhancement`
- Click **Submit new issue**

**Step 4.** Create a milestone:

- Go to **Issues → Milestones → New milestone**
- Title: `v1.0 Release`
- Due date: one month from today
- Click **Create milestone**
- Go back to your first issue and assign it to the `v1.0 Release` milestone

**Step 5.** Create an issue template:

Create a file in your repository at `.github/ISSUE_TEMPLATE/bug_report.md`:

```markdown
---
name: Bug report
about: Report a bug
title: '[BUG] '
labels: bug
---

## Description

## Steps to reproduce
1.
2.

## Expected behaviour

## Actual behaviour
```

Commit it and push. Now when you click **New issue**, the template chooser appears.

**Step 6.** Close an issue via a commit:

Make any change to a file, then commit with:

```bash
git commit -m "fix: update README link, closes #1"
git push
```

Go back to issue #1 - it should now be closed with a reference to the commit.

---

## Common Mistakes

**Creating vague issues.**

"It doesn't work" is not an issue - it is a description of frustration. Issues need to be specific enough that someone unfamiliar with the codebase can understand what needs to happen. Vague issues get ignored, closed as invalid or sit open indefinitely.

**Using issues for questions that belong in Discussions.**

If someone is asking how to use a feature rather than reporting a bug or requesting a feature, Discussions is the better place. Issues that are really questions clutter the tracker and rarely get resolved cleanly.

**Not using labels.**

Unlabelled issues make the tracker hard to navigate. Even in a solo project, applying a handful of consistent labels (`bug`, `enhancement`, `docs`) makes it easy to find related issues and see what kind of work is pending.

**Forgetting to link issues to pull requests.**

Without `Closes #N` in a pull request, the issue stays open even after the fix is merged. You then have to manually close it or - worse - forget about it and end up with dozens of open issues for work that was already done.

**Reopening closed issues instead of creating new ones.**

If a bug returns after being fixed, create a new issue and reference the old one. Reopening and re-closing the same issue makes the history confusing and loses the clear record of when things were fixed.

**Not using templates for open source projects.**

Without templates, contributors open issues with insufficient information, leading to back-and-forth requests for clarification. Templates ensure you get everything you need the first time.

---

## Summary

- Issues track bugs, features, tasks and questions in a repository with a sequential number that never changes
- Create issues from the web UI, GitHub CLI or by referencing them in commits and pull requests
- Fields: title, body (Markdown), labels, assignees, milestones, projects, issue types and sub-issues
- Default labels: `bug`, `documentation`, `duplicate`, `enhancement`, `good first issue`, `help wanted`, `invalid`, `question`, `wontfix`
- Milestones group issues around a goal or version, showing completion percentage
- Sub-issues (GA April 2025) allow hierarchical breakdown with up to 100 per parent and 8 levels of nesting
- Issue templates (`.md`) and issue forms (`.yml`) provide structured input for contributors
- Closing keywords (`Closes #N`, `Fixes #N`, `Resolves #N`) in pull request descriptions automatically close issues on merge
- Use issues for actionable work, Discussions for open-ended conversations, Projects for planning across issues
- Pin up to 3 issues; transfer between repos; lock conversations to prevent unwanted comments

---

## Sources and Further Reading

- [About issues - GitHub Docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/about-issues) - official issues reference
- [Creating an issue - GitHub Docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/creating-an-issue) - step-by-step guide
- [Issue templates - GitHub Docs](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository) - setting up Markdown templates
- [Issue forms syntax - GitHub Docs](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/syntax-for-issue-forms) - complete YAML form field reference
- [Linking a pull request to an issue - GitHub Docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue) - closing keywords reference
- [Sub-issues - GitHub Docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/adding-sub-issues) - creating and managing issue hierarchies

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

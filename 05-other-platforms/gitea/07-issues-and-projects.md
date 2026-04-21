# Issues and Projects in Gitea

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Gitea issues are the unit of tracked work - bugs, feature requests, tasks, questions and discussions. They live alongside the code they relate to, link to commits and pull requests, and feed into Kanban project boards for visual workflow management. This file covers the full issues system: creating and managing issues, labels, milestones, project boards and issue templates.

---

## Table of Contents

1. [Issues](#issues)
2. [Labels](#labels)
3. [Milestones](#milestones)
4. [Assignees and mentions](#assignees-and-mentions)
5. [Issue templates](#issue-templates)
6. [Closing issues automatically](#closing-issues-automatically)
7. [Pinned issues](#pinned-issues)
8. [Reactions](#reactions)
9. [Projects (Kanban boards)](#projects-kanban-boards)
10. [Issue dependencies](#issue-dependencies)
11. [Cross-repository references](#cross-repository-references)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## Issues

### Creating an issue

1. Go to a repository and click **Issues** in the navigation
2. Click **New Issue**
3. Fill in:
   - **Title**: a concise description of the problem or request
   - **Content**: detailed description using Markdown
4. Set metadata in the right sidebar (labels, assignees, milestone, project)
5. Click **Submit New Issue**

### Issue content and Markdown

Issue descriptions and comments support full GitHub-flavoured Markdown:

```markdown
## Problem

When a user logs in with an expired token, the server returns 500 instead of 401.

## Steps to reproduce

1. Create a user account
2. Generate a token
3. Wait for the token to expire (or manually set expiry to the past)
4. Make an API call with the expired token

## Expected behaviour

HTTP 401 Unauthorized with a clear error message.

## Actual behaviour

HTTP 500 Internal Server Error.

## Environment

- Gitea version: 1.25.5
- OS: Ubuntu 22.04
- Browser: Firefox 124
```

### Commenting on issues

Any user with access to the repository can comment on issues. Comments support:
- Full Markdown
- `@mentions` to notify specific users
- `#123` to reference other issues or PRs
- Code blocks with syntax highlighting
- Image uploads (drag and drop)
- Task lists (`- [ ]` and `- [x]`)

### Editing and deleting comments

The author of a comment (or a repository admin) can edit or delete it using the pencil and trash icons that appear on hover.

### Locking issues

Repository maintainers can lock an issue to prevent further comments:
1. Click **Lock** in the issue's right sidebar
2. Select a reason (off-topic, spam, too heated, resolved)

Locked issues accept comments only from collaborators and maintainers.

### Issue states

Issues are either **Open** or **Closed**. Open issues need attention. Close an issue when:
- The bug is fixed
- The feature is implemented
- The question is answered
- The request is declined

Click **Close Issue** at the bottom of the issue. Reopening is always possible.

---

## Labels

Labels categorise issues and pull requests. They appear as coloured badges on the issue list.

### Creating labels

1. Go to **Issues** > **Labels** (or the gear icon next to Labels in the sidebar)
2. Click **Create Label**
3. Enter a name, choose a colour (or click the dice for random), add an optional description
4. Click **Create Label**

### Default label suggestions

Gitea does not pre-create labels on new repositories. Consider creating these standard sets:

**Type labels:**
- `bug` (red) - confirmed bugs
- `enhancement` (blue) - feature requests
- `documentation` (teal) - documentation improvements
- `question` (purple) - needs clarification

**Status labels:**
- `needs-investigation` (orange) - needs more information
- `blocked` (dark red) - waiting on an external dependency
- `in-progress` (yellow) - actively being worked on
- `duplicate` (grey) - duplicate of another issue

**Priority labels:**
- `priority: critical` (dark red)
- `priority: high` (red)
- `priority: low` (light grey)

### Applying labels

In any issue or PR, click **Labels** in the right sidebar and select the applicable labels. Multiple labels can be applied.

### Filtering by label

On the Issues list, click a label in the sidebar to filter issues to only those with that label. Combine with other filters (state, assignee, milestone) for precise views.

### Organisation-level labels

Labels can be created at the organisation level and will automatically appear in all repositories within the organisation. Go to the organisation's settings to manage organisation labels.

---

## Milestones

Milestones group issues and pull requests toward a goal - typically a version release, a sprint end or a feature completion. They provide progress tracking and deadline visibility.

### Creating a milestone

1. Go to **Issues** > **Milestones**
2. Click **New Milestone**
3. Enter:
   - **Title**: e.g. `v2.0.0` or `Sprint 5` or `Q2 Launch`
   - **Description**: what this milestone represents
   - **Due date**: optional target date
4. Click **Create Milestone**

### Assigning issues to milestones

In any issue, click **Milestone** in the right sidebar and select the milestone.

### Milestone progress

The milestone list shows a progress bar: open vs closed issues in that milestone. When all issues are closed, the milestone is complete. Close the milestone manually when the goal is reached.

### Milestone views

Click on a milestone name to see all issues assigned to it, filtered by open/closed status.

---

## Assignees and mentions

### Assigning issues

Click **Assignees** in the right sidebar to assign one or more users to an issue. Assignees are notified by email and the issue appears in their assigned issues list.

### @mentions

Mentioning a user with `@username` in any issue comment or PR description:
- Sends them an email notification
- Creates a notification in their Gitea notification centre
- Links their username to their profile

Mentions work for users, teams and organisations:
- `@username` - specific user
- `@org/team` - all members of a team within an organisation

### Watching and ignoring

Control your notification level for an issue:
- **Watch**: receive notifications for all activity
- **Unwatch**: only receive notifications when directly mentioned or assigned

Click the eye icon at the top right of any issue to toggle.

---

## Issue templates

Issue templates pre-fill the new issue form with a structured format, prompting reporters to include relevant information. This dramatically improves issue quality, particularly for bug reports.

### Creating issue templates

Create a directory `.gitea/ISSUE_TEMPLATE/` in your repository root. Add `.md` files for each template.

**Bug report template (`.gitea/ISSUE_TEMPLATE/bug_report.md`):**

```markdown
---
name: Bug Report
about: Report a bug or unexpected behaviour
labels: bug
---

## Describe the bug

<!-- A clear description of what the bug is -->

## Steps to reproduce

1. Go to '...'
2. Click on '...'
3. See error

## Expected behaviour

<!-- What you expected to happen -->

## Actual behaviour

<!-- What actually happened -->

## Screenshots

<!-- If applicable, add screenshots -->

## Environment

- Version: <!-- e.g. 1.25.5 -->
- OS: <!-- e.g. Ubuntu 22.04 -->
- Browser (if applicable): <!-- e.g. Chrome 122 -->

## Additional context

<!-- Any other relevant information -->
```

**Feature request template (`.gitea/ISSUE_TEMPLATE/feature_request.md`):**

```markdown
---
name: Feature Request
about: Suggest a new feature or improvement
labels: enhancement
---

## Problem statement

<!-- What problem does this feature solve? -->

## Proposed solution

<!-- How should this work? -->

## Alternatives considered

<!-- Other approaches you have thought about -->

## Additional context

<!-- Mockups, examples or references -->
```

### Template front matter

The `---` block at the top of a template is YAML front matter:

| Field | Purpose |
|---|---|
| `name` | The template name shown in the template selector |
| `about` | A brief description shown under the name |
| `labels` | Labels automatically applied when using this template |
| `assignees` | Users automatically assigned |

### Blank issue option

If you have templates but still want to allow blank issues, create a file `.gitea/ISSUE_TEMPLATE/config.yml`:

```yaml
blank_issues_enabled: true
contact_links:
  - name: Security Vulnerabilities
    url: https://example.com/security
    about: Please report security issues privately
```

---

## Closing issues automatically

Reference issues in commit messages and pull request descriptions to close them automatically when merged.

### Closing keywords

These keywords followed by an issue reference close the issue when the commit lands on the default branch (or when the PR is merged into the default branch):

- `Closes #123`
- `Fixes #123`
- `Resolves #123`

**In a commit message:**
```
Fix null pointer in user authentication

The login handler was not checking for nil user before
accessing user.Email.

Fixes #456
```

**In a pull request description:**
```markdown
This PR implements password reset via email.

Closes #123
Closes #124
```

Both issues 123 and 124 are automatically closed when the PR is merged.

### Cross-repository closing

Close an issue in another repository with the full reference:
```
Fixes owner/other-repo#89
```

---

## Pinned issues

Pin up to 3 issues to the top of the issues list for visibility. Pinned issues appear before all other issues regardless of sort order.

Use pinning for:
- Announcements (project status, deprecation notices)
- Contribution guidelines ("read this before opening an issue")
- Known blocking issues affecting many users

To pin: open an issue > click **Pin Issue** in the right sidebar (available to maintainers and admins).

---

## Reactions

Any comment or issue body can receive emoji reactions. Reactions are a lightweight way to express agreement or acknowledgement without a comment.

Click the smiley face icon on any comment to react. Common reactions:
- 👍 +1 / agree
- 👎 -1 / disagree
- ❤️ love
- 🎉 celebrate
- 😕 confused
- 👀 watching

Reactions are aggregated and shown as counts under the content.

---

## Projects (Kanban boards)

Gitea Projects provide visual Kanban boards for managing issues and pull requests. Projects can belong to a repository or an organisation.

### Creating a project

**Repository project:**
1. Go to the repository > **Projects**
2. Click **New Project**
3. Enter a name and optional description
4. Choose a template or start blank
5. Click **Create Project**

**Organisation project:**
1. Go to the organisation page > **Projects**
2. Follow the same steps

### Board columns

Each project has columns representing workflow stages. Default templates include columns like To Do, In Progress and Done. Add, rename and reorder columns to match your workflow:

1. Click **Add column** on the right of the board
2. Name it (e.g. "In Review", "Blocked", "Ready to Deploy")

### Adding issues to a project

**From an issue:**
1. Open an issue
2. Click **Projects** in the right sidebar
3. Select the project and optionally the column

**From the board:**
1. Click **+** in a column
2. Search for existing issues or create a new one

**Bulk add:**
1. From the project board, click **Add existing issues**
2. Search and select multiple issues

### Moving cards

Drag and drop issue cards between columns to update their status. The move is recorded in the issue's activity feed.

### Filtering the board

Filter the board by:
- Assignee
- Label
- Milestone
- Type (issues only, PRs only, both)

### Closing a project

When a project is complete, archive it (hides from the active list but preserves history) or delete it.

---

## Issue dependencies

Gitea supports explicit dependencies between issues: "this issue is blocked by that issue" or "this issue depends on that one".

### Adding dependencies

1. Open an issue
2. Click **Dependencies** in the right sidebar
3. Click **Add dependency** or **Is blocked by**
4. Search for and select the blocking issue

### Effect of dependencies

Issues with open dependencies show a warning in the issue. The dependency is visible in both issues. No hard enforcement (you can still close a dependent issue with open dependencies) but the relationship is clearly visible.

---

## Cross-repository references

Reference issues and PRs from other repositories anywhere in Gitea using:

```
owner/repo#123        # reference in another repo
owner/repo!456        # PR reference in another repo
```

In a comment:
```markdown
This duplicates owner/other-repo#89 which was reported last month.

Fixed by the changes in owner/other-repo!456.
```

The reference is auto-linked to the other issue or PR.

---

## Try It Yourself

**Exercise 1 - Create and organise issues**

1. In your repository, go to **Issues**
2. Create three issues:
   - "Fix login redirect bug" (label: bug)
   - "Add dark mode support" (label: enhancement)
   - "Update README with installation steps" (label: documentation)
3. Create a label called `priority: high` in red
4. Apply it to the first issue

**Exercise 2 - Set up a milestone**

1. Go to **Issues** > **Milestones** > **New Milestone**
2. Create "v1.0.0" with a due date two weeks from today
3. Assign your three issues to this milestone
4. View the milestone progress

**Exercise 3 - Create issue templates**

1. Create `.gitea/ISSUE_TEMPLATE/bug_report.md` with the template from this file
2. Commit to main
3. Click **New Issue** - you should see the template selection screen
4. Choose Bug Report and see the pre-filled form

**Exercise 4 - Create a project board**

1. Go to **Projects** > **New Project**
2. Name it "v1.0.0 Sprint"
3. Create columns: Backlog, In Progress, In Review, Done
4. Add your three issues to the board
5. Move them between columns

---

## Common Mistakes

**Not using labels consistently**

Labels are only valuable when applied consistently. If bugs sometimes get the `bug` label and sometimes do not, label-based filtering is unreliable. Define your label conventions and apply them to every issue.

**Creating milestones but never closing issues**

Milestones with high numbers of open issues that never decrease are demotivating and misleading. Either close resolved issues promptly or remove them from the milestone if they are not part of the planned scope.

**Writing issue titles that are too vague**

"It doesn't work" or "Bug in login" forces readers to open the issue to understand it. Write self-contained titles: "Login redirect sends users to homepage instead of intended page after OAuth sign-in". The title should describe the problem completely.

**Forgetting issue templates in the git root vs .gitea**

Issue templates must be in `.gitea/ISSUE_TEMPLATE/` (or `.github/ISSUE_TEMPLATE/` for GitHub compatibility). Templates placed in the repository root or in other directories are ignored.

**Not linking issues to pull requests**

The connection between "what we planned to fix" (issues) and "what we actually changed" (PRs) is the audit trail of your project's development. Use `Closes #123` in PR descriptions to create this link automatically.

---

## Summary

Gitea issues track bugs, features, tasks and discussions. They support Markdown content, labels, milestones, assignees and reactions. Labels categorise and filter issues; milestones group related issues toward a deadline; assignees route responsibility.

Issue templates in `.gitea/ISSUE_TEMPLATE/` pre-fill the new issue form with structured prompts, improving consistency and information quality.

Closing keywords (`Closes #123`, `Fixes #123`, `Resolves #123`) in commit messages and PR descriptions automatically close linked issues when merged to the default branch.

Gitea Projects provide Kanban boards for visual workflow management. Add issues to columns, drag between columns to update state and filter by assignee, label or milestone.

Issue dependencies allow explicit "blocked by" relationships. Cross-repository references with `owner/repo#123` link to issues in other repositories.

---

## Sources

- [Gitea: Issues documentation](https://docs.gitea.com/usage/issue-and-pull-request-tracking)
- [Gitea: Labels](https://docs.gitea.com/usage/labels)
- [Gitea: Milestones](https://docs.gitea.com/usage/milestones)
- [Gitea: Projects](https://docs.gitea.com/usage/projects)
- [Gitea: Issue templates](https://docs.gitea.com/usage/issue-pull-request-templates)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# Issues and Planning

**Difficulty:** 🟡 Intermediate | **Time:** 40 minutes

GitLab's planning tools are built directly into the same platform as your code. There is no separate Jira instance to configure, no Trello board disconnected from your repository, no project management tool with its own login that your developers ignore. Issues, boards, milestones, labels, epics, roadmaps, iterations and the Service Desk all live inside GitLab, linked to the same merge requests, commits and pipelines that produce your software.

This file covers the complete picture: creating and managing issues, the quick action system that powers advanced workflows without leaving a comment box, labels and scoped labels, milestones, Kanban boards, epics and roadmaps (Premium), iterations for sprint-based teams (Premium), Service Desk for customer support, GitLab Wiki and GitLab Snippets.

---

## Table of Contents

- [Issues](#issues)
- [Creating an issue](#creating-an-issue)
- [Issue fields and metadata](#issue-fields-and-metadata)
- [Confidential issues](#confidential-issues)
- [Sub-issues and task lists](#sub-issues-and-task-lists)
- [Linked issues and blocking relationships](#linked-issues-and-blocking-relationships)
- [Time tracking](#time-tracking)
- [Quick actions](#quick-actions)
- [Labels](#labels)
- [Scoped labels](#scoped-labels)
- [Milestones](#milestones)
- [Issue boards](#issue-boards)
- [Epics](#epics)
- [Roadmaps](#roadmaps)
- [Iterations](#iterations)
- [Issue and MR templates](#issue-and-mr-templates)
- [Service Desk](#service-desk)
- [GitLab Wiki](#gitlab-wiki)
- [GitLab Snippets](#gitlab-snippets)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Issues

Issues are the primary unit of work tracking in GitLab. They represent features, bugs, tasks, questions, improvement ideas - any discrete unit of work that needs to be planned, discussed, tracked and completed.

Every issue lives in a project and has a project-scoped number (IID) - `#1`, `#2`, `#42`. These numbers are used in references throughout GitLab: mention `#42` in a commit message, a merge request description or another issue and GitLab automatically creates a hyperlink.

Issues are part of GitLab's unified **work items** framework (GA in GitLab 18.7), which provides a consistent interface across different work item types: Issues, Tasks, Incidents, Epics, Objectives and Key Results.

**Navigate to issues**: Left sidebar → **Plan → Work items** (filter by Type = Issue) or the legacy path **Plan → Issues**.

---

## Creating an Issue

### From the UI

Left sidebar → **Plan → Work items** → **New work item** (button, top right) → select **Issue** from the type dropdown.

Or use the keyboard shortcut: press `i` anywhere in a project to open the new issue form.

Or: navigate to an existing issue list and click **New issue**.

### From a commit or merge request

In a commit message or MR description, write `Closes #` followed by an issue number to link a new MR to an existing issue. To create a branch and MR directly from an issue, go to the issue page and click **Create merge request**.

### From the glab CLI

```bash
# Interactive creation
glab issue create

# With all fields specified
glab issue create \
  --title "Bug: login fails on Safari 17" \
  --description "Steps to reproduce: 1. Open Safari 17. 2. Navigate to login. 3. Enter credentials. Expected: login succeeds. Actual: page hangs." \
  --label "bug,frontend,priority::high" \
  --assignee zaccess \
  --milestone "Sprint 14"
```

### From email (Service Desk)

Service Desk turns incoming emails into issues automatically. Covered fully in [Service Desk](#service-desk) below.

---

## Issue Fields and Metadata

Every issue has the following fields, all accessible from the right sidebar on the issue page.

### Title and description

The title should be specific and action-oriented. "Login fails on Safari 17" is better than "Bug with login". The description supports full Markdown: headings, code blocks, tables, checklists, images.

### Assignee

Who is responsible for working on this issue. Multiple assignees are supported - useful when an issue requires collaboration between two team members.

Assign from the right sidebar → **Assignees** → search for a username. Or use the quick action `/assign @username`.

### Labels

Colour-coded tags for categorisation. Applied from the right sidebar → **Labels**. Multiple labels can be applied to a single issue. See [Labels](#labels) for full details.

### Milestone

Associates the issue with a sprint, release or project phase. The milestone tracks progress toward a goal and shows a burndown chart. See [Milestones](#milestones) for details.

### Due date

An optional deadline for the issue. Overdue issues are highlighted in listings. Set via right sidebar or `/due YYYY-MM-DD` quick action.

### Weight (Premium)

A numeric value representing the complexity or effort required. Teams using story points or effort estimations use this field. Set from right sidebar → **Weight** or `/weight N`.

### Health status

Indicates whether the issue is on track: On track, Needs attention or At risk. Set via right sidebar → **Health status** or `/health_status on_track` quick action. Visible on boards and in epic views.

### Iteration (Premium)

Associates the issue with a time-boxed sprint/iteration. See [Iterations](#iterations).

### Epic (Premium)

Associates the issue with a larger initiative. See [Epics](#epics).

### Participants

GitLab automatically tracks everyone who has interacted with the issue (commented, been mentioned, changed fields). Participants receive notifications for activity on the issue unless they unsubscribe.

---

## Confidential Issues

Confidential issues are visible only to project members with Reporter access or higher and to the issue creator. They are invisible to Guest role members, external collaborators and the public even on public projects.

**Create confidential**: on the issue creation form, check **This issue is confidential**. Or use the quick action `/confidential` on an existing issue.

**Use cases:**

- Security vulnerability reports (before a fix is ready and disclosed)
- Internal HR-related issues
- Customer complaint investigations
- Pre-announcement feature planning

> [!CAUTION]
> Confidential issues can be referenced in comments on non-confidential issues, but the title and content remain hidden from users without sufficient access. Be careful about inadvertently revealing confidential issue titles in public discussions.

---

## Sub-issues and Task Lists

### Sub-issues (child work items)

Issues can have child issues (sub-issues), creating a hierarchical relationship. The parent issue shows progress as a percentage based on how many child issues are closed.

**Add a child issue**: on the issue page, look for **Child items** section → **Add** → search for existing issues or create a new one.

Sub-issues are useful for breaking a large feature into trackable pieces:

```
Issue #42: Implement user authentication
├── #43: Design login form UI
├── #44: Implement password hashing
├── #45: Add email verification flow
├── #46: Write authentication tests
└── #47: Update API documentation
```

### Markdown task lists

Add interactive checkboxes directly in the issue description using Markdown:

```markdown
## Tasks

- [ ] Design mockups approved
- [ ] Backend API endpoint created
- [x] Database schema updated
- [ ] Frontend component built
- [ ] Tests written
- [ ] Documentation updated
```

In the rendered issue, each checkbox is clickable and updates the Markdown source automatically. The issue list shows a progress counter (e.g. "2 of 6 tasks completed").

---

## Linked Issues and Blocking Relationships

Issues can be related to each other in several ways. Set from the right sidebar → **Linked items** section.

**Relationship types:**

| Type                     | Meaning                                                              |
| ------------------------ | -------------------------------------------------------------------- |
| **Relates to**           | General relationship - these issues are connected                    |
| **Blocks**               | This issue must be resolved before the linked issue can be worked on |
| **Is blocked by**        | This issue cannot proceed until the linked issue is resolved         |
| **Cloned from / clones** | This issue is a copy of another                                      |

**Quick actions for relationships:**

```
/relate #42          - add a "relates to" relationship
/blocks #42          - mark this issue as blocking #42
/blocked_by #42      - mark this issue as blocked by #42
```

Blocked issues show a visual indicator in issue lists and boards, making it immediately clear which items cannot start yet.

---

## Time Tracking

GitLab's built-in time tracking lets you log time spent on issues and merge requests and compare against estimates.

### Setting an estimate

```
/estimate 2w 3d 4h 30m    # 2 weeks, 3 days, 4 hours, 30 minutes
/estimate 8h               # 8 hours
/estimate 1mo              # 1 month (4 weeks)
```

The estimate appears in the right sidebar as "Time tracking: 0h / 8h".

### Logging time spent

```
/spend 2h 30m             # log 2.5 hours spent now
/spend 1h 15m 2026-04-01  # log time for a specific date
/spend -30m               # subtract 30 minutes (correction)
```

Multiple `/spend` entries accumulate. GitLab shows total time spent vs estimate in the sidebar.

### Removing time tracking

```
/remove_estimate          # clear the time estimate
/remove_time_spent        # clear all logged time
```

> [!NOTE]
> Time tracking data is visible on the issue page and aggregated in milestone and group reporting. It is purely informational - GitLab does not enforce time limits or trigger actions based on logged time.

---

## Quick Actions

Quick actions are slash commands that perform operations on issues and merge requests from the comment box, without navigating to separate settings panels. They execute when you submit the comment - the quick action itself is not visible in the comment thread, only the result is shown.

You can combine multiple quick actions in a single comment:

```
/label ~"priority::high" ~bug ~frontend
/assign @alice @bob
/milestone %"Sprint 14"
/due 2026-04-30

This issue has been reviewed and categorised. Assigning to the team for sprint planning.
```

### State actions

| Action    | Effect                               |
| --------- | ------------------------------------ |
| `/close`  | Close the issue or MR                |
| `/reopen` | Reopen a closed issue or MR          |
| `/merge`  | Merge the MR (if conditions are met) |
| `/draft`  | Mark the MR as draft                 |
| `/ready`  | Remove draft status from MR          |

### Assignment

| Action                     | Effect                                   |
| -------------------------- | ---------------------------------------- |
| `/assign @user`            | Assign user (adds to existing assignees) |
| `/assign @me`              | Assign yourself                          |
| `/assign_reviewer @user`   | Add reviewer to an MR                    |
| `/unassign @user`          | Remove a specific assignee               |
| `/reassign @user`          | Replace all assignees with this user     |
| `/unassign_reviewer @user` | Remove a reviewer from an MR             |

### Labels

| Action           | Effect                           |
| ---------------- | -------------------------------- |
| `/label ~name`   | Add a label                      |
| `/unlabel ~name` | Remove a specific label          |
| `/relabel ~name` | Replace all labels with this one |

### Planning

| Action                              | Effect                                                                |
| ----------------------------------- | --------------------------------------------------------------------- |
| `/milestone %"Sprint 14"`           | Set milestone                                                         |
| `/remove_milestone`                 | Clear milestone                                                       |
| `/due 2026-05-01`                   | Set due date                                                          |
| `/remove_due_date`                  | Clear due date                                                        |
| `/weight 5`                         | Set weight (Premium)                                                  |
| `/clear_weight`                     | Clear weight (Premium)                                                |
| `/iteration *iteration:"Sprint 14"` | Set iteration (Premium)                                               |
| `/remove_iteration`                 | Clear iteration (Premium)                                             |
| `/health_status on_track`           | Set health status (options: `on_track`, `needs_attention`, `at_risk`) |
| `/clear_health_status`              | Clear health status                                                   |

### Relationships

| Action             | Effect                                  |
| ------------------ | --------------------------------------- |
| `/relate #42`      | Mark as related to issue #42            |
| `/blocks #42`      | Mark this as blocking #42               |
| `/blocked_by #42`  | Mark this as blocked by #42             |
| `/add_child #42`   | Add #42 as a child issue                |
| `/set_parent #42`  | Set #42 as this issue's parent          |
| `/promote_to Epic` | Convert this issue to an epic (Premium) |

### Time tracking

| Action               | Effect                |
| -------------------- | --------------------- |
| `/estimate 8h`       | Set time estimate     |
| `/spend 2h 30m`      | Log time spent        |
| `/remove_estimate`   | Clear estimate        |
| `/remove_time_spent` | Clear all logged time |

### Issue management

| Action                     | Effect                                                     |
| -------------------------- | ---------------------------------------------------------- |
| `/confidential`            | Make the issue confidential                                |
| `/lock`                    | Lock the issue (prevent new comments except from members)  |
| `/unlock`                  | Unlock the issue                                           |
| `/subscribe`               | Subscribe to notifications                                 |
| `/unsubscribe`             | Unsubscribe from notifications                             |
| `/todo`                    | Add to your To-Do list                                     |
| `/done`                    | Mark your To-Do as done                                    |
| `/copy_metadata #42`       | Copy labels, milestone and assignees from issue #42        |
| `/duplicate #42`           | Mark this issue as a duplicate of #42 and close it         |
| `/move namespace/project`  | Move the issue to another project                          |
| `/clone namespace/project` | Clone the issue to another project (keeping this one open) |

### MR-specific

| Action                | Effect                                 |
| --------------------- | -------------------------------------- |
| `/approve`            | Approve the MR                         |
| `/unapprove`          | Remove your approval                   |
| `/rebase`             | Rebase the source branch on the target |
| `/target_branch main` | Change the MR target branch            |

> [!TIP]
> Quick actions work in **issue descriptions**, **MR descriptions** and **any comment**. They also work in **issue and MR templates** - quick actions embedded in a template execute automatically when the issue or MR is created from that template.

---

## Labels

Labels are colour-coded tags used to categorise and filter issues and merge requests. They are one of the most important organisational tools for any team using GitLab.

### Label types

**Project labels**: created within a specific project. Visible and applicable only to issues and MRs in that project.

**Group labels**: created at the group level. Available across all projects in the group and its subgroups. Create via: Group → **Manage → Labels** → **New label**.

Use group labels for organisation-wide categorisation: `bug`, `feature`, `documentation`, `needs-review`. Use project labels for project-specific categories that only make sense in context.

### Creating a label

Project: **Manage → Labels** → **New label**
Group: Group → **Manage → Labels** → **New label**

Fields:

- **Title**: the label name (e.g. `bug`, `priority::high`, `workflow::in-review`)
- **Description**: optional explanation of when to use this label
- **Colour**: pick from presets or enter a hex code. GitLab chooses a text colour automatically for contrast.

### Predefined default labels

GitLab creates these default labels in every new project:
`bug`, `confirmed`, `critical`, `discussion`, `documentation`, `enhancement`, `suggestion`, `support`

These can be edited or deleted and additional labels can be added freely.

### Using labels

Apply labels when creating an issue or from the right sidebar on an existing issue. In the issue list view, click a label to filter to all issues with that label.

Labels on an MR are separate from labels on the related issue. Applying a label to the MR does not apply it to the issue and vice versa.

---

## Scoped Labels

> [!IMPORTANT]
> Scoped labels are a **Premium feature**.

Scoped labels use a `key::value` format with a double colon separator. Within the same scope (same key), only one value can be applied at a time. Applying a new value in the same scope automatically removes the previous one.

### Syntax

```
scope::value
priority::low
priority::medium
priority::high
workflow::planning
workflow::in-progress
workflow::in-review
workflow::done
type::bug
type::feature
type::documentation
```

### How they work

Apply `priority::low` to an issue. Later, apply `priority::high` to the same issue. GitLab automatically removes `priority::low` - you cannot have both `priority::low` and `priority::high` simultaneously. This makes scoped labels behave like enum/radio-button fields rather than free-form tags.

Without scoped labels, a team might accidentally apply both `priority::low` and `priority::high` to the same issue. With scoped labels, this is impossible - the second one replaces the first.

### Creating scoped labels

In the label creation form, simply name your label with `::` in the title: `priority::high`. GitLab recognises the `::` separator and treats it as a scoped label automatically.

### Common scoped label schemas

**Workflow state:**

```
workflow::backlog
workflow::planning
workflow::in-progress
workflow::in-review
workflow::blocked
workflow::done
```

**Priority:**

```
priority::critical
priority::high
priority::medium
priority::low
```

**Type:**

```
type::bug
type::feature
type::chore
type::documentation
type::security
```

**Team:**

```
team::frontend
team::backend
team::devops
team::design
```

---

## Milestones

Milestones represent a goal with a deadline - a sprint, a release version, a project phase. Issues and merge requests are assigned to milestones and GitLab tracks progress.

### Creating a milestone

**Project milestone**: Plan → **Milestones** → **New milestone**
**Group milestone**: Group → **Plan → Milestones** → **New milestone**

Fields:

- **Title**: the milestone name (e.g. "Sprint 14", "v2.0.0 Release", "Q2 2026")
- **Description**: optional context or goals
- **Start date**: optional start date
- **Due date**: optional deadline. Milestones with no due date never expire.

### Milestone progress

The milestone detail page shows:

- **Progress bar**: percentage of assigned issues that are closed
- **Burndown chart**: issues closed per day over the milestone's duration - shows whether the team is on track to complete all work by the deadline
- **Burnup chart**: shows total scope (issues added) vs completed issues - useful when scope changes during a milestone
- **Open issues and MRs**: list of all open work still assigned to the milestone
- **Closed issues and MRs**: completed work in this milestone

### Milestone use cases

**Sprint milestones**: "Sprint 14 (Apr 7-21)". Each sprint gets a milestone. Issues planned for the sprint are assigned to it. The burndown chart shows whether the sprint is on track.

**Release milestones**: "v2.0.0". Every issue that must be done before the release is assigned to this milestone. When it reaches 100% closed, the release is ready.

**Phase milestones**: "Phase 1 - Foundation". Organisational phases of a larger project.

---

## Issue Boards

Issue boards give you a Kanban-style view of issues, with drag-and-drop movement between columns.

**Navigate**: Plan → **Boards**

### How boards work

A board has a set of **lists** (columns). Each list displays issues that match its criteria. Moving a card between columns changes the issue's metadata to match the target column's criteria.

The default board has three lists: **Open**, **In Progress** and **Closed**. This is functional but not very customisable.

### List types

| List type               | Column contains                                                                     |
| ----------------------- | ----------------------------------------------------------------------------------- |
| **Label**               | Issues with a specific label. Moving a card adds that label; moving out removes it. |
| **Assignee**            | Issues assigned to a specific person                                                |
| **Milestone**           | Issues in a specific milestone                                                      |
| **Iteration** (Premium) | Issues in a specific iteration                                                      |

### Customising the board

Add a list: **Create list** button at the top of the board → choose the list type and value.

**Example: workflow board using scoped labels:**

```
Backlog | In Progress | In Review | Done
(workflow::backlog) | (workflow::in-progress) | (workflow::in-review) | (closed)
```

Moving a card from **Backlog** to **In Progress** automatically adds the `workflow::in-progress` label and removes `workflow::backlog`.

### Board scope

Boards can be scoped to filter which issues appear:

- **Milestone**: only show issues from a specific milestone (useful for sprint boards)
- **Iteration**: only show issues from a specific iteration (Premium)
- **Label**: only show issues with a specific label (useful for per-team boards)
- **Assignee**: only show issues assigned to a specific person

Set scope: on the board page, click **Edit board** → configure scope filters.

### Multiple boards

Projects and groups can have multiple boards. For example:

- "Sprint Board" - scoped to the current iteration, workflow state columns
- "Bug Triage Board" - only issues labelled `bug`, severity columns
- "Feature Backlog" - only issues labelled `feature`, priority columns

Create additional boards: Plan → Boards → the dropdown showing current board name → **Create new board**.

---

## Epics

> [!IMPORTANT]
> Epics are a **Premium feature**.

Epics are large work items that span multiple issues and potentially multiple milestones. They represent initiatives, themes or user journeys that are too large to be a single issue.

**Navigate to epics**: Plan → **Work items** → filter by Type = Epic or legacy path Plan → **Epics**.

### Creating an epic

Group → Plan → Work items → **New work item** → select **Epic**

Fields:

- **Title**: the initiative name
- **Description**: context, goals, success criteria
- **Start date**: optional
- **Due date**: optional
- **Labels**: for filtering and categorisation
- **Health status**: On track, Needs attention, At risk

### Epic hierarchy

Epics can be nested - child epics under parent epics - creating a full hierarchy:

```
Epic: Improve developer experience (Q2 2026)
├── Epic: Reduce pipeline build times
│   ├── Issue #101: Investigate caching options
│   ├── Issue #102: Implement Docker layer caching
│   └── Issue #103: Add parallel test execution
├── Epic: Improve local development setup
│   ├── Issue #110: Create dev container config
│   └── Issue #111: Document setup process
└── Epic: Improve deployment reliability
    ├── Issue #120: Implement blue-green deployments
    └── Issue #121: Add deployment health checks
```

### Adding issues to an epic

From the epic: scroll to **Child items** → **Add** → search for issues by title or number → **Add selected**.

From an issue: right sidebar → **Epic** → search and select.

From quick action in a comment: `/relate` then link manually or set the parent directly.

### Epic progress

The epic page shows:

- A progress bar based on percentage of child issues closed
- List of all child issues and child epics with their status
- Combined milestone and date tracking

---

## Roadmaps

> [!IMPORTANT]
> Roadmaps are a **Premium feature**.

The Roadmap is a timeline (Gantt-style) view of epics. It shows which epics are active during which time periods, giving teams and stakeholders a visual overview of the plan.

**Navigate**: Plan → **Roadmap** (group level only)

### Configuring the roadmap view

Timeline presets - choose what each column represents:

- **Quarters** (3-month periods)
- **Months** (one column per month)
- **Weeks** (one column per week)
- **Days** (for short-range tactical planning)

Filter options:

- Filter by label: show only epics with specific labels (e.g. show only `team::frontend` epics)
- Filter by milestone: show only epics associated with a specific milestone
- Filter by assignee: show epics assigned to a specific user
- Show open epics, closed epics or all

### Roadmap bars

Each epic appears as a horizontal bar spanning its start date to due date. The bar colour can be customised via labels. Epics with no dates are shown in a list below the timeline.

Click any epic bar to open a summary panel with description, child progress and links.

---

## Iterations

> [!IMPORTANT]
> Iterations are a **Premium feature**.

Iterations are GitLab's sprint system - time-boxed periods of work with defined start and end dates. They are organised into **iteration cadences** (a sequence of iterations following a regular cadence: weekly, bi-weekly, monthly).

**Navigate**: Plan → **Iterations** (project level)

### Creating an iteration cadence

Group → Plan → **Iterations** → **New iteration cadence**

Configure:

- **Title**: e.g. "Engineering sprints"
- **Start date**: when the first iteration begins
- **Duration**: 1 week, 2 weeks, 3 weeks or 4 weeks
- **Number to auto-create**: how many future iterations to generate automatically
- **Description**: optional context

GitLab automatically creates iterations based on the cadence, naming them by date range: "Sprint Apr 7-21, 2026".

### Assigning issues to an iteration

From an issue: right sidebar → **Iteration** → select the iteration.

From a board: add an **Iteration** list to the board and move cards between iteration columns.

From a quick action: `/iteration *iteration:"Sprint Apr 7-21, 2026"`

### Iteration reports

Each iteration has:

- **Burndown chart**: issues closed per day, showing whether the team is on track
- **Burnup chart**: scope vs completed
- **Open/closed issue list**: what remains and what is done

---

## Issue and MR Templates

Templates pre-fill the description field when creating issues or merge requests, prompting consistent information.

### Issue templates

Create Markdown files in `.gitlab/issue_templates/` on the default branch.

**`.gitlab/issue_templates/Bug Report.md`:**

```markdown
## Summary

<!-- Describe the bug in one sentence -->

## Steps to reproduce

1.
2.
3.

## Expected behaviour

<!-- What should happen -->

## Actual behaviour

<!-- What actually happens -->

## Environment

- **OS**:
- **Browser**:
- **GitLab version** (if self-managed):

## Screenshots or logs

<!-- Attach screenshots, error messages or relevant logs -->

## Additional context

<!-- Any other context that might be relevant -->

/label ~bug
/label ~"needs-triage"
```

**`.gitlab/issue_templates/Feature Request.md`:**

```markdown
## Problem statement

<!-- What problem does this solve? Who experiences it? -->

## Proposed solution

<!-- Describe the feature you would like -->

## Acceptance criteria

- [ ]
- [ ]
- [ ]

## Alternatives considered

<!-- Other ways you thought about solving this -->

/label ~feature
/label ~"needs-discussion"
```

**`.gitlab/issue_templates/Default.md`** - automatically applied to all new issues unless another template is selected.

### MR templates

Create Markdown files in `.gitlab/merge_request_templates/`.

**`.gitlab/merge_request_templates/Default.md`:**

```markdown
## What does this MR do?

<!-- Describe the change in 1-2 sentences -->

## Why?

<!-- Motivation or link to issue -->

Closes #

## How to test

1.
2.
3.

## Screenshots (for UI changes)

| Before | After |
| ------ | ----- |
|        |       |

## Checklist

- [ ] Tests added or updated
- [ ] Documentation updated
- [ ] No debugging code left in
- [ ] Tested on relevant operating systems

/label ~"needs-review"
```

### Using templates

When creating an issue or MR, a **Choose a template** dropdown appears above the description field if templates exist. Select a template to pre-fill the description. The `Default.md` template applies automatically without requiring selection.

---

## Service Desk

Service Desk is a built-in customer support system. Customers email a unique project address and their emails automatically become confidential issues. Your team responds through issue comments and the customer receives those responses by email - all without the customer needing a GitLab account.

**Tier**: Free (all plans)

### Setting up Service Desk

Settings → General → **Service Desk** → toggle **Enable Service Desk** → save.

After enabling, GitLab generates a unique email address:

```
your-project-abcd1234@incoming.gitlab.com
```

Share this address with customers as your support contact.

### How it works

1. Customer emails `your-project-abcd1234@incoming.gitlab.com`
2. GitLab creates a new **confidential issue** with the email subject as the title and email body as the description
3. The issue is assigned the `Service Desk` label automatically
4. Your team sees the issue in the issue tracker, works on it and responds by leaving a comment on the issue
5. GitLab emails the customer with your comment (they see a clean email, not the GitLab UI)
6. If the customer replies to the email, their reply becomes another comment on the issue
7. When resolved, close the issue - GitLab notifies the customer that their request is closed

### Service Desk templates

Create a Markdown file at `.gitlab/service_desk_template.md` to provide a consistent auto-reply that customers receive when their issue is created:

```markdown
Thank you for contacting support. We have received your request and will respond within 1 business day.

**Your request number:** {{ ISSUE_ID }}

If you have additional information to share, simply reply to this email.

Best regards,
The Support Team
```

### Customising the Service Desk email

By default, responses come from `gitlab@mg.gitlab.com`. You can configure a custom email address (e.g. `support@yourcompany.com`) to make the communication feel more professional.

### Converting issues to Service Desk tickets

Any existing issue can be linked to a customer email using the quick action:

```
/convert_to_ticket user@example.com
```

This links the issue to the customer's email and enables the email-based reply workflow.

---

## GitLab Wiki

Every project has a built-in wiki for documentation. The wiki is a separate Git repository linked to the project - it can be cloned, edited locally and pushed just like any other repository.

**Navigate**: Plan → **Wiki**

### Creating wiki pages

Click **New page** → enter a page title → write content in Markdown, AsciiDoc or other supported formats → **Create page**.

Page titles become the URL slug automatically. Nested pages can be created using `/` in the title: "Architecture/Database Design" creates a page at `wiki/Architecture/Database-Design`.

### Wiki sidebar

Add a custom sidebar by creating a page titled `_sidebar`. This appears in the right sidebar of all wiki pages and can contain navigation links to important pages.

### Wiki footer

Create a page titled `_footer` to add content that appears at the bottom of every wiki page - useful for copyright notices or contact information.

### Cloning and editing the wiki locally

```bash
# Clone the wiki repository
git clone git@gitlab.com:NAMESPACE/PROJECT.wiki.git

# Edit pages locally, then push
cd PROJECT.wiki
# Edit files (each page is a .md file)
git add .
git commit -m "update: add architecture overview"
git push
```

### Group wikis (Premium)

Groups can have their own wiki, shared across all projects in the group. Navigate: Group → **Plan → Wiki**. Useful for team-level or organisation-wide documentation that is not specific to a single project.

---

## GitLab Snippets

Snippets are versioned, shareable code fragments. They are GitLab's equivalent of GitHub Gists.

**Navigate**: Code → **Snippets** (project snippets) or Your work → **Snippets** (personal snippets)

### Creating a snippet

Click **New snippet** → add title, description, one or more files → set visibility → **Create snippet**.

**Visibility options**: Public (anyone can see), Internal (self-managed only - authenticated users), Private (only you and explicitly shared users).

### Multi-file snippets

Unlike GitHub Gists which are single-file, GitLab Snippets support up to **10 files** per snippet. This is useful for sharing a config file alongside its documentation or a short script with a requirements file.

### Snippet features

- **Version history**: snippets are backed by a Git repository. Every edit creates a commit. You can view the full edit history and revert to previous versions.
- **Syntax highlighting**: automatic based on file extension
- **Embed on external sites**: each snippet has an embed code you can use to display it in a README, blog post or documentation page with syntax highlighting
- **Clone as a repository**: `git clone https://gitlab.com/-/snippets/SNIPPET_ID`
- **Raw view**: direct URL to the raw file content - useful for `curl` scripting

### Snippet size limits

Maximum 50 MB total per snippet. Maximum 10 files. Files with binary content are not supported.

---

## Try It Yourself

**Exercise 1: Create an issue with labels and a template**

1. Create these labels in your project: `bug`, `feature`, `priority::high`, `priority::low`, `workflow::backlog`, `workflow::in-progress`
2. Create an issue template at `.gitlab/issue_templates/Default.md` with at minimum a Summary and Steps sections
3. Create a new issue - notice the template auto-populates the description
4. Apply labels: `bug` and `priority::high`
5. Set a due date one week from today
6. In a comment, use quick actions: `/assign @me` and `/label ~"workflow::in-progress"`

**Exercise 2: Set up a Kanban board**

1. Navigate to Plan → Boards
2. Click **Create list** → choose Label → select `workflow::backlog`
3. Create lists for `workflow::in-progress` and `workflow::in-review`
4. Create three test issues and assign labels to place them in different columns
5. Drag a card from **Backlog** to **In Progress** - observe the label update on the issue

**Exercise 3: Create and use a wiki page**

1. Navigate to Plan → Wiki → New page
2. Title it "Getting Started" and write a short guide
3. Create another page titled "\_sidebar" with links to important pages
4. Notice the sidebar now appears on all wiki pages

---

## Common Mistakes

**Not using group labels for shared vocabulary.** Teams create duplicate labels in every project: `bug` in project A, `bug` in project B, each a different shade of red. Create labels at the group level once and they appear consistently across all projects.

**Applying conflicting labels without scoped labels.** Without scoped labels, nothing prevents `priority::high` and `priority::low` coexisting on the same issue. Upgrade to scoped labels (Premium) or establish a team convention and communicate clearly which label should replace which.

**Creating issues in the wrong project.** In a multi-project group, issues should live in the project they are most related to. A UX issue should live in the frontend project, not in the backend project. Incorrectly placed issues break filtering, board views and milestone reporting.

**Ignoring the templates.** Teams create issue templates but developers open new issues without selecting one because the template selection is not obvious. Make `Default.md` the go-to that covers the most common case and add a note in CONTRIBUTING.md that templates exist.

**Not closing related issues when an MR merges.** An MR that fixes a bug should close the bug report issue automatically. This requires either using `Closes #N` in the MR description (closes when merged to default branch) or manually closing the issue. Leaving resolved issues open pollutes the tracker.

**Treating milestones as tags rather than deadlines.** Milestones with no due date never expire and accumulate endlessly. Every milestone should have a due date that represents a real deadline. Archive or close milestones after they are complete.

**Using the wiki for process documentation that belongs in the repository.** `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md` belong in the repository because they are versioned with the code. The wiki is better for living documentation that changes frequently and independently of the codebase: architecture decisions, team conventions, meeting notes, onboarding guides.

---

## Summary

- **Issues** are the primary work tracking unit. Create from the UI, the glab CLI or via Service Desk. Navigate via Plan → Work items
- **Fields**: title, description, assignees (multiple), labels, milestone, due date, weight (Premium), health status, iteration (Premium), epic (Premium)
- **Confidential issues**: visible only to Reporter+ members. Use for security vulnerabilities and sensitive topics
- **Sub-issues**: nest issues hierarchically; parent shows progress as percentage of closed children
- **Linked issues**: relate, blocks, blocked-by relationships expressed in the sidebar and quick actions
- **Time tracking**: `/estimate` to set estimate; `/spend` to log time; visible in sidebar and reporting
- **Quick actions**: slash commands in any comment that execute operations without navigating separate UI panels. Dozens available covering assignment, labels, planning, time tracking, state and relationships
- **Labels**: project or group scope, colour-coded, freely applied. Group labels ensure consistent vocabulary
- **Scoped labels** (Premium): `key::value` format; only one value per scope can be active at a time - behave like enum fields
- **Milestones**: goals with optional dates. Burndown and burnup charts. Use for sprints and releases
- **Boards**: Kanban view with draggable cards. Lists based on labels, assignees, milestones or iterations. Board scope filters which issues appear
- **Epics** (Premium): large initiatives spanning multiple issues. Hierarchical (epics within epics). Progress tracking
- **Roadmaps** (Premium): Gantt timeline of epics at the group level
- **Iterations** (Premium): sprint cadences with burndown/burnup. Issues assigned to iterations appear on iteration boards
- **Templates**: `.gitlab/issue_templates/` and `.gitlab/merge_request_templates/`. `Default.md` auto-applies. Quick actions in templates execute on creation
- **Service Desk**: email-to-issue system. Free. Customers email a unique address; replies become issue comments; team responses are emailed back
- **Wiki**: separate Git repository per project (and group, Premium). Clone and edit locally. `_sidebar` and `_footer` special pages
- **Snippets**: versioned multi-file (up to 10 files) code fragments. Version history, embeddable, clonable

---

## Sources and Further Reading

- [Issues documentation](https://docs.gitlab.com/user/project/issues/) - complete issues reference
- [Quick actions](https://docs.gitlab.com/user/project/quick_actions/) - full list of all quick action commands
- [Labels](https://docs.gitlab.com/user/project/labels/) - creating and managing labels
- [Milestones](https://docs.gitlab.com/user/project/milestones/) - milestone setup and burndown charts
- [Issue boards](https://docs.gitlab.com/user/project/issue_board/) - board configuration and usage
- [Epics](https://docs.gitlab.com/user/group/epics/) - creating and managing epics (Premium)
- [Roadmaps](https://docs.gitlab.com/user/group/roadmap/) - roadmap timeline view (Premium)
- [Iterations](https://docs.gitlab.com/user/project/iterations/) - sprint cadences (Premium)
- [Service Desk](https://docs.gitlab.com/user/project/service_desk/) - email-to-issue customer support
- [GitLab Wiki](https://docs.gitlab.com/user/project/wiki/) - wiki setup and usage
- [Snippets](https://docs.gitlab.com/user/snippets/) - code fragment sharing

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

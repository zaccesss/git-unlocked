# GitHub Projects and Boards

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

GitHub Projects is a flexible planning and tracking tool built into GitHub. It brings issues and pull requests from across multiple repositories into a single, customisable workspace - with boards, tables, roadmaps and automations. If you have ever used Trello, Jira or Linear, Projects fills a similar role while staying natively integrated with your code.

---

## Table of Contents

- [What is GitHub Projects?](#what-is-github-projects)
- [Projects v2 vs classic projects](#projects-v2-vs-classic-projects)
- [Creating a project](#creating-a-project)
- [Adding items to a project](#adding-items-to-a-project)
- [Views](#views)
- [Custom fields](#custom-fields)
- [Grouping, filtering and sorting](#grouping-filtering-and-sorting)
- [The roadmap view](#the-roadmap-view)
- [Automations](#automations)
- [Sub-issues in Projects](#sub-issues-in-projects)
- [Linking repositories](#linking-repositories)
- [Insights and charts](#insights-and-charts)
- [Project settings and access](#project-settings-and-access)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitHub Projects?

GitHub Projects is a planning tool that organises issues and pull requests into customisable views. A project can contain items from multiple repositories, making it possible to track work across an entire product or organisation in one place.

**What Projects gives you:**

- Board view for Kanban-style workflows
- Table view for spreadsheet-style management
- Roadmap view for timeline planning
- Custom fields (status, priority, size, dates, team, iteration)
- Automations that move items as their status changes
- Filtered views that show exactly what you need
- Charts and insights for progress tracking
- Up to 50,000 items per project

Projects live at the user or organisation level - not inside a single repository. A single project can pull in issues from any repository you have access to.

---

## Projects v2 vs Classic Projects

GitHub had an older "classic projects" system (sometimes called Projects v1) that was simpler but more limited. It was fully deprecated in August 2024. **All classic projects have been migrated or archived.** Everything in this file refers to the current Projects (v2) system.

If you see references to "classic projects" in older documentation or tutorials, they no longer apply.

---

## Creating a Project

**From your personal profile:**

1. Go to `github.com/YOUR_USERNAME`
2. Click the **Projects** tab
3. Click **New project**
4. Choose a template or start blank:
   - **Board** - Kanban columns
   - **Table** - spreadsheet rows
   - **Roadmap** - timeline view
   - **Team backlog** - table with status, priority and size fields
   - **Bug tracker** - table with bug-specific fields
   - **Feature** - iterative feature development template
5. Give the project a name
6. Click **Create project**

**From an organisation:**

Go to `github.com/ORG_NAME` → **Projects** tab → **New project**.

Organisation projects can be shared with all organisation members or kept private to specific teams.

**From GitHub CLI:**

```bash
gh project create --owner YOUR_USERNAME --title "My Project"
gh project create --owner YOUR_ORG --title "Sprint 14"
```

---

## Adding Items to a Project

A project starts empty. Items are issues, pull requests or draft items.

### Add existing issues or pull requests

**From the project:**

1. Click **+ Add item** at the bottom of any view
2. Type `#` to search for issues and PRs by number or title
3. Select items to add

**From an issue or pull request:**

1. Open the issue or PR
2. In the right sidebar, click the gear icon next to **Projects**
3. Select the project

**Bulk add from a repository:**

In the project, click **+ Add item** → **Add item from repository** → select a repository → filter by label, milestone or assignee → select all matching items.

### Draft items

Draft items are lightweight cards you can add quickly without creating a full issue. They have a title and optional body but no labels, assignees or milestone. Convert a draft to an issue when it is ready:

Click the draft → **Convert to issue** → select the repository.

### Add via GitHub CLI

```bash
# Add an issue to a project
gh project item-add PROJECT_NUMBER --owner YOUR_USERNAME --url https://github.com/OWNER/REPO/issues/42
```

---

## Views

A project can have multiple views, each showing the same underlying data in a different way. Views are tabs at the top of the project.

### Board view

Kanban-style columns. Items appear as cards that can be dragged between columns. Default columns: No status, Todo, In Progress, Done.

**Customise:**

- Add, rename or delete columns
- Set column limits (WIP limits)
- Group by any single-select field (Status, Priority, Team etc.)

**When to use:** Sprint planning, tracking active work in progress, visual status overview.

### Table view

Spreadsheet-style rows and columns. Every item is a row. Every field is a column.

**Features:**

- Add, hide or reorder columns
- Sort by any field
- Filter rows by any field value
- Group rows by field value
- Slice data to create focused sub-views

**When to use:** Bulk editing, planning, reporting, searching across all items.

### Roadmap view

Timeline-based view showing items on a horizontal calendar. Requires date fields (start date and end date or just a target date).

**Features:**

- Zoom levels: day, week, month, quarter, year
- Items appear as bars on the timeline
- Group by field (team, priority, iteration etc.)
- Show vertical markers for milestones

**When to use:** Release planning, quarterly OKRs, timeline communication to stakeholders.

### Adding new views

Click **+ New view** to add a view. Set the layout (Board, Table or Roadmap). Each view can have its own filters, grouping and column configuration. Changes to a view only affect that view - the underlying data is shared.

---

## Custom Fields

Custom fields extend projects with your own metadata beyond the defaults. You can have up to **50 fields** per project.

**Built-in fields (always available):**

Title, Assignees, Status, Labels, Milestone, Repository, Linked pull requests, Reviewers, Parent issue, Sub-issue progress.

**Field types you can add:**

**Text** - free-form text. Examples: notes, links, acceptance criteria.

**Number** - numeric value. Examples: story points, effort estimate, priority score.

**Date** - a calendar date. Examples: due date, target date, start date. Required for Roadmap view.

**Single select** - choose one option from a predefined list with optional colours. Examples: Priority (🔴 High, 🟡 Medium, 🟢 Low), Team (Frontend, Backend, Design), Size (XS, S, M, L, XL).

**Iteration** - a time-boxed sprint or cycle. Set a start date and duration. Items are assigned to specific iterations (Sprint 1, Sprint 2 etc.). GitHub automatically advances to the next iteration.

**Creating a custom field:**

Click the **+** at the right end of the column headers in Table view → **New field** → choose type → configure options.

---

## Grouping, Filtering and Sorting

Each view supports powerful data manipulation.

### Filtering

Show only items matching specific criteria.

**Filter bar (top of the view):**

```
status:In Progress
assignee:YOUR_USERNAME
label:bug
-label:wontfix
iteration:@current
milestone:"v2.0 Release"
no:assignee
```

Filters stack - multiple filters show items matching all conditions.

### Grouping

Organise items into collapsible groups based on a field.

In Table view: click **Group** → select a field. Items are grouped by that field's value.

In Board view: columns automatically represent groups by the field the board is organised by.

### Sorting

Order items within a view.

In Table view: click a column header to sort ascending or descending. Click again to reverse.

### Slicing

A **slice** creates a persistent side panel that filters items by a specific field value, keeping the main view visible. Useful for quickly switching focus between teams or priorities without creating separate views.

---

## The Roadmap View

The Roadmap view requires at least one date field. Items without dates are shown in the "No dates" section below the timeline.

**Setting up a roadmap:**

1. Add date fields: click **+** → **New field** → **Date** → name it "Start date" and another "End date"
2. Switch to Roadmap view
3. Click **Date fields** → set which fields represent the start and end of each item's bar

**Zooming:**

Use the zoom controls to switch between day, week, month, quarter or year granularity.

**Adding milestones:**

In roadmap settings, you can add vertical milestone markers from your repository milestones to show target dates on the timeline.

---

## Automations

Automations move items between statuses or perform other actions automatically when predefined conditions are met.

**Built-in automations:**

GitHub provides default automations for each project. Find them at **Project → Settings → Workflows**:

| Trigger | Action |
|---|---|
| Item added to project | Set status to Todo |
| Issue closed | Set status to Done |
| Issue reopened | Set status to In Progress |
| Pull request merged | Set status to Done |
| Pull request closed (not merged) | Set status to Todo |
| Code changes requested | Set status to In Progress |
| Code review approved | Set status to In Review |

**Enable/disable automations:**

Settings → Workflows → toggle each automation on or off.

**Custom automations with GitHub Actions:**

For more complex automations (add to project when a label is applied, assign based on file changed, notify on Slack when moved to a column), use GitHub Actions with the GraphQL API.

Example - add to project when `bug` label is applied:

```yaml
name: Add to bug tracker project

on:
  issues:
    types: [labeled]

jobs:
  add-to-project:
    if: github.event.label.name == 'bug'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/add-to-project@v1
        with:
          project-url: https://github.com/orgs/YOUR_ORG/projects/1
          github-token: ${{ secrets.ADD_TO_PROJECT_PAT }}
```

> [!NOTE]
> The `GITHUB_TOKEN` cannot access Projects via the GraphQL API. You need a Personal Access Token (PAT) with `project` scope or a GitHub App token for project automations.

---

## Sub-issues in Projects

GitHub Projects shows sub-issue hierarchies from April 2025 onwards.

**In Table view:**

Parent issues show a sub-issue progress indicator (e.g., `3/7`). Click the expand icon next to a parent issue to see its sub-issues inline as nested rows.

**In Board view:**

Parent issues show progress badges. Sub-issues are not shown separately on the board by default - they appear when you click the parent.

**Filtering by hierarchy:**

Use `parent-issue:` filter to show only sub-issues of a specific parent.

---

## Linking Repositories

A project can pull in issues and PRs from multiple repositories. Link additional repositories to make their items searchable when adding to the project.

**Link a repository:**

Settings → Linked repositories → Add a repository → select it.

Linked repositories also enable the "Auto-add items" automation - automatically adding all new issues from that repository to the project.

---

## Insights and Charts

Projects provides built-in charts for tracking progress and velocity.

**Navigate to Insights:** Click **Insights** in the top-right of the project.

**Available chart types:**

**Burn up:** Shows work completed over time vs total scope. Useful for release tracking.

**Burn down:** Shows remaining work over time. Classic sprint tracking chart.

**Column chart:** Items grouped and counted by any field value. Good for seeing distribution by status, priority or team.

**Stacked bar chart:** Shows proportion of items in each field value over time.

**Configuring charts:**

Each chart is configurable - choose x-axis (usually time), y-axis (count of items), group-by field and filter conditions.

---

## Project Settings and Access

**Access:** Click **Settings** (three-dot menu) in the project.

**Visibility:**

- **Public:** Anyone can see the project (no GitHub account required)
- **Private:** Only people with explicit access can see it

**Manage access:**

- Add collaborators by username
- Set their role: Read, Write or Admin

**For organisation projects:**

- Base role sets the default access for all organisation members
- Teams can be added with specific roles
- Outside collaborators can be added individually

**Closing a project:**

Projects can be closed (archived) rather than deleted. A closed project is read-only and hidden from the default projects list but can be accessed and reopened.

**Deleting a project:**

Settings → Delete project. This permanently removes the project and all its views, fields and automations. Items (issues and PRs) are not deleted - they still exist in their repositories.

---

## Try It Yourself

**Step 1.** Create a project:

1. Go to `github.com/YOUR_USERNAME` → Projects → New project
2. Choose **Team backlog** template
3. Name it `Practice Project`
4. Click **Create project**

**Step 2.** Add items:

- Click **+ Add item** → type a title for a draft item → press Enter
- Add 4-5 draft items representing small tasks

**Step 3.** Convert a draft to an issue:

- Click on any draft item
- Click **Convert to issue**
- Select your repository

**Step 4.** Set field values:

- In the table view, click the Status field on each item and set: Todo, In Progress, Done for different items
- Set Priority to High, Medium or Low for each

**Step 5.** Switch views:

- Click **+ New view**
- Set layout to **Board**
- See your items arranged by status in columns

**Step 6.** Create a filtered view:

- Click **+ New view**
- Name it `My tasks`
- In the filter bar, type: `assignee:YOUR_USERNAME`
- Now this view only shows items assigned to you

**Step 7.** Enable automations:

- Go to Settings → Workflows
- Enable: "Item closed → set status to Done"

**Step 8.** Go to one of your linked issues and close it. Come back to the project and check that it moved to Done automatically.

---

## Common Mistakes

**Confusing Projects with repository issue lists.**

The repository Issues tab shows only issues in that repository. A Project is a cross-repository planning tool. If you are tracking work across multiple repos, use a Project. If you just want to see all issues in one repo, use the Issues tab.

**Not setting up automations.**

Without automations, project boards require constant manual maintenance - dragging items between columns as they progress. Enable the basic built-in automations (closed → Done, PR merged → Done) at minimum.

**Creating too many views.**

Every view adds cognitive overhead. Start with two or three focused views (All items, My work, This sprint) and add more only when you have a clear need.

**Not using custom fields for your workflow.**

The default Status field is coarse. Add fields that match your actual workflow - Priority, Team, Estimate, Sprint. A project that reflects how your team actually works is far more useful than a generic one.

**Using Projects for a solo repository.**

For a single-developer project on a single repository, the Issues list with labels and milestones is usually sufficient. Projects adds value when you have multiple contributors, multiple repositories or complex planning needs.

**Forgetting to link repositories.**

If you add issues from a repository that is not linked to the project, those issues will not be automatically suggested when using the Add item flow. Link all relevant repositories in project Settings.

---

## Summary

- GitHub Projects is a cross-repository planning tool with Board, Table and Roadmap views
- Classic projects were deprecated in August 2024 - all current projects are v2
- Items in a project are issues, pull requests or draft items
- Custom fields add metadata: Text, Number, Date, Single Select, Iteration - up to 50 per project
- Board view is Kanban-style; Table view is spreadsheet-style; Roadmap view shows items on a timeline
- Built-in automations move items when issues are closed, PRs are merged and reviews are requested
- Custom automations require GitHub Actions with a PAT (the GITHUB_TOKEN cannot access Projects)
- Sub-issues from April 2025 show hierarchically in Table view
- Insights provides burn up, burn down and distribution charts
- Projects are at user or organisation level - not inside a single repository

---

## Sources and Further Reading

- [About GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects) - official overview
- [Quickstart for GitHub Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/quickstart-for-projects) - create your first project
- [Automating projects with Actions](https://docs.github.com/en/issues/planning-and-tracking-with-projects/automating-your-project/automating-projects-using-actions) - custom automation guide
- [Insights for Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/viewing-insights-from-your-project/about-insights-for-projects) - chart configuration reference

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

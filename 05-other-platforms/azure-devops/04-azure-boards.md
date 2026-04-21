# Azure Boards

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Azure Boards is the project management service in Azure DevOps. It provides work items (the atomic unit of tracked work), Kanban boards, sprint planning, backlogs, queries, delivery plans and reporting. It is built for software development teams doing agile delivery - particularly Scrum and SAFe - but it is flexible enough for any team tracking work alongside their code.

Azure Boards' main strength over tools like GitHub Projects or Trello is depth: proper sprint capacity planning, burndown charts, velocity tracking, delivery plans spanning multiple teams and deep integration with Azure Repos and Pipelines. Where GitHub Projects is simple and lightweight, Azure Boards is full-featured and enterprise-grade.

---

## Table of Contents

1. [Process templates and work item hierarchy](#process-templates-and-work-item-hierarchy)
2. [Work items in depth](#work-items-in-depth)
3. [The Boards view (Kanban)](#the-boards-view-kanban)
4. [The Backlogs view](#the-backlogs-view)
5. [Sprints](#sprints)
6. [Queries](#queries)
7. [Delivery plans](#delivery-plans)
8. [Linking work to code](#linking-work-to-code)
9. [Reporting and dashboards](#reporting-and-dashboards)
10. [Teams within a project](#teams-within-a-project)
11. [Try It Yourself](#try-it-yourself)
12. [Common Mistakes](#common-mistakes)
13. [Summary](#summary)
14. [Sources](#sources)

---

## Process templates and work item hierarchy

When you create an Azure DevOps project, you choose a **process template**. This determines the types of work items available and their workflow states. Choose carefully - changing the process later is possible but non-trivial.

### Agile (most common)

```
Epic
  └── Feature
        └── User Story
              ├── Task
              └── Bug
```

Workflow states for User Stories: **New → Active → Resolved → Closed**

Use Agile if your team uses user stories and iterative delivery. It is the most commonly used template and the most flexible.

### Scrum

```
Epic
  └── Feature
        └── Product Backlog Item (PBI)
              ├── Task
              └── Bug
```

Workflow states for PBIs: **New → Approved → Committed → Done**

Use Scrum if your team follows formal Scrum with sprint commitments. PBIs map directly to Scrum backlog items.

### CMMI (Capability Maturity Model Integration)

```
Epic
  └── Feature
        └── Requirement
              ├── Task
              └── Bug
              └── Change Request
              └── Review
              └── Risk
              └── Issue
```

Use CMMI for regulated environments requiring formal change management, review records and risk tracking.

### Basic (simplest)

```
Epic
  └── Issue
        └── Task
```

Use Basic for small teams who want the simplest possible setup, closest to GitHub Issues.

### Changing the process template

You can change the process template for a project after creation, but it requires planning: custom work item types may need mapping, existing work items get migrated to new types and some fields may be lost. Do not change lightly on a project with significant existing work item history.

---

## Work items in depth

A **work item** is the fundamental unit of tracked work in Azure Boards. Every task, bug, feature, story or epic is a work item.

### Creating a work item

**From the Boards view:**
1. Click **+ Add work item** at the bottom of any column
2. Type a title and press Enter

**From the Backlogs view:**
1. Click **+ New Work Item** at the top of the backlog
2. Select the type (Epic, Feature, Story, etc.)

**From the work item form:**
1. Click **Work Items** in the Boards sidebar
2. Click **+ New Work Item**
3. Select type and open the full form

### The work item form

The full work item form contains:

**Title**: The headline for the work item. Make it specific and actionable: "Add password reset flow for OAuth users" not "Password reset".

**State**: The current status in the workflow. States vary by process template and work item type.

**Assigned to**: Who is responsible for completing this item.

**Area**: The area path, used to categorise work by feature area or team. Area paths are configured in project settings.

**Iteration**: The sprint or iteration this item is assigned to. Unassigned items sit in the backlog.

**Description**: Detailed context for the work. Supports rich text, images and attachments.

**Acceptance criteria**: For user stories, what conditions must be true for the story to be considered complete.

**Story points / Effort / Remaining work**: Estimation fields. The field name varies by process template. Used for velocity and capacity calculations.

**Tags**: Freeform labels for filtering and grouping. Unlike GitHub labels, Azure DevOps tags are not pre-defined - any text becomes a tag.

**Links**: Connect this work item to other work items (parent/child, related, duplicate, blocked by) or to external items (Git commits, PRs, builds, test cases).

**Attachments**: Upload files (logs, screenshots, mockups) directly to the work item.

**History**: Every change to the work item is recorded with timestamp and user. The full audit trail of who changed what and when.

**Discussion**: A threaded comment section for conversation about the work item. Supports @mentions, which send email notifications.

### Work item states

States represent the lifecycle of a work item. The available states depend on the process template and work item type. Teams can customise states in process settings. Common patterns:

| State | Meaning |
|---|---|
| New / To Do | Created, not yet started |
| Active / In Progress | Being worked on |
| Resolved / In Review | Complete, awaiting review or verification |
| Closed / Done | Verified complete, no further action |

### Work item types and hierarchy

Work items can be linked in a parent-child hierarchy. A Feature can be a parent of multiple User Stories. A User Story can be a parent of multiple Tasks.

This hierarchy enables planning at different levels: leadership tracks Epics and Features while developers track User Stories and Tasks. The hierarchy rolls up: when all child Tasks are closed, the parent User Story shows as fully complete.

### Bulk editing

Select multiple work items in any list view using the checkboxes. Bulk operations available:
- Change state
- Assign to a different person
- Move to a different area or iteration
- Add tags
- Delete

---

## The Boards view (Kanban)

The Boards view is a Kanban-style visual board. Work items appear as cards in columns representing workflow states. Drag cards between columns to change their state.

### Accessing the board

Click **Boards** in the left sidebar of Azure Boards.

### Board columns

Each column represents a workflow state. The default columns for Agile are: **New**, **Active**, **Resolved**, **Closed**. Teams commonly customise these to match their actual workflow (e.g. "Ready for Review", "In QA", "Done").

### Customising columns

1. Click the **Settings** gear on the board
2. Click **Columns**
3. Add, rename or reorder columns
4. Map columns to workflow states (multiple states can map to one column, or columns can map to sub-states)
5. Set WIP (Work In Progress) limits per column

**WIP limits** display a warning (or hard stop, depending on configuration) when a column exceeds the specified number of cards. WIP limits are a core Kanban practice - they surface bottlenecks and encourage completing work before starting new work.

### Card information

Each card on the board shows by default:
- Work item ID and title
- Assigned person's avatar
- Tags
- Child item completion (2/5 tasks done)
- Blocked indicator (if linked to a "Blocks" work item)

Click any card to open the full work item form.

### Swimlanes

Swimlanes divide the board horizontally to separate different categories of work. Common uses:
- **Priority swimlane**: "Expedite" (urgent), "Standard", "Low priority"
- **Type swimlane**: "Features", "Bugs"
- **Team swimlane**: different rows per team member

Swimlanes are configured in board settings.

---

## The Backlogs view

The Backlogs view is a list-based view of work items organised hierarchically. It is where sprint planning, backlog grooming and priority ordering happen.

### Accessing the backlog

Click **Backlogs** in the left sidebar.

### Backlog levels

The backlog has multiple levels corresponding to the work item hierarchy:
- **Epics backlog**: Epics list with Features as children
- **Features backlog**: Features list with User Stories as children
- **Stories/PBIs backlog**: The sprint-level backlog - Stories or PBIs with Tasks as children

Switch between levels using the tabs at the top of the backlog.

### Priority ordering

Items in the backlog are ordered top-to-bottom by priority. Drag items to reorder. The top of the backlog is highest priority.

The ordering in the backlog is the recommended order for the team to work. Sprint planning pulls from the top of the backlog into the sprint.

### Backlog filters

Filter the backlog by:
- Assigned to
- Area path
- Tags
- Work item type
- Iteration

This allows a team lead to see only their team's items, or a developer to see only items assigned to them.

### Backlog settings

Click the gear icon on the backlog to configure:
- Which work item types appear at each level
- Whether to show "In Progress" items (items already in a sprint)
- Whether to show child items from all iterations or just the current sprint

---

## Sprints

A **sprint** (called an **iteration** in Azure DevOps terminology) is a time-boxed period for completing a set of work. Sprint planning assigns work items from the backlog to the sprint. At the end of the sprint, completed items are reviewed and remaining items are moved back to the backlog or carried forward.

### Setting up iterations

1. Go to **Project settings** > **Boards** > **Project configuration** > **Iterations**
2. Create iterations with start and end dates
3. Nest iterations under a root (e.g. "2026" > "Sprint 1", "Sprint 2")
4. Teams can have their own iteration schedules within the project

### The sprint board

Click **Sprints** in the left sidebar, then select the current sprint. The sprint view shows:

- **Backlog**: all items assigned to this sprint
- **Board**: Kanban view filtered to items in this sprint
- **Capacity**: team capacity for this sprint
- **Taskboard**: tasks within stories for the sprint

### Sprint planning

1. Go to **Backlogs** > select the upcoming sprint from the **Sprints** dropdown
2. The sprint backlog is initially empty
3. From the **Backlog** tab (showing unassigned items), drag items into the sprint
4. Or right-click an item > **Move to iteration** > select the sprint

### Capacity planning

Azure Boards allows teams to enter capacity per person per sprint. This enables honest sprint planning - knowing that a developer is only available for 30 hours in a 2-week sprint affects how many story points should be committed.

1. Go to **Sprints** > select the sprint > **Capacity** tab
2. For each team member, enter days per sprint and hours per day
3. Set days off for holidays or leave
4. The capacity bar shows remaining capacity as work is assigned

### Burndown charts

The sprint burndown chart shows remaining work over time. A healthy burndown trends downward toward zero by the end of the sprint. The chart automatically updates as tasks are completed and work is added or removed.

Access from **Sprints** > the sprint > **Analytics** tab or via the **Burndown** widget on a dashboard.

### Velocity

**Velocity** is the average story points completed per sprint over recent sprints. Azure Boards calculates this automatically and displays it on the velocity chart. Velocity helps plan future sprints - if a team averages 40 story points per sprint, planning 60 points for a sprint is unrealistic.

Access velocity from **Analytics** views or the **Velocity** widget.

---

## Queries

Queries let you search, filter and save views of work items. Any question you can ask about work items can be answered with a query: "What bugs are unassigned?", "What did we complete last sprint?", "What is blocked right now?".

### Creating a query

1. Click **Queries** in the Boards sidebar
2. Click **New query**
3. Use the query builder to set conditions

### Query operators

| Condition | Example |
|---|---|
| Field = Value | State = Active |
| Field Contains | Title Contains "authentication" |
| Field In (list) | Assigned To In (Alice, Bob) |
| Field Was Ever | State Was Ever "In Review" |
| Field Changed | Changed Date >= @Today - 7 |
| @Me | Assigned To = @Me |
| @Today | Changed Date >= @Today - 7 |
| @CurrentIteration | Iteration Path = @CurrentIteration |

### Work Item Query Language (WIQL)

For complex queries, switch to the **Editor** (WIQL) view:

```sql
SELECT [System.Id], [System.Title], [System.State], [System.AssignedTo]
FROM WorkItems
WHERE [System.TeamProject] = @project
  AND [System.WorkItemType] = 'User Story'
  AND [System.State] NOT IN ('Closed', 'Resolved')
  AND [System.AssignedTo] = @me
ORDER BY [Microsoft.VSTS.Common.Priority] ASC, [System.CreatedDate] DESC
```

### Saving and sharing queries

Queries can be saved as **My queries** (visible only to you) or **Shared queries** (visible to the team). Saved queries can be:
- Pinned to the sidebar for quick access
- Added as a widget on dashboards
- Used in email notifications (trigger an email when a query returns new results)
- Exported to CSV for reporting

---

## Delivery plans

**Delivery plans** provide a timeline view of work across multiple teams and iterations. They answer the question: "What is each team delivering, when, and how do they align?"

This is particularly valuable for organisations running SAFe (Scaled Agile Framework) or program-level planning where multiple teams need to coordinate deliveries.

### Creating a delivery plan

1. Click **Delivery Plans** in the sidebar (or find it under **Boards** > **Delivery Plans**)
2. Click **New plan**
3. Give the plan a name
4. Add teams to the plan
5. Select which backlog level to show (Epics, Features, Stories)
6. Configure the date range

### Reading a delivery plan

The plan shows a horizontal timeline with teams as rows and sprints as columns. Work items appear as cards on the timeline spanning their planned sprint dates. Dependencies between items are shown as connecting lines.

### Dependency tracking

Mark work items as dependent on other items. Dependencies appear in the delivery plan and on individual work items. When a dependency is at risk (the blocking item is not progressing), Azure Boards can surface warnings.

---

## Linking work to code

The connection between Azure Boards work items and Azure Repos code is one of the platform's most valuable features. Every commit, pull request, branch and build can be linked to one or more work items.

### From commits

Include the work item ID in your commit message:

```bash
git commit -m "Implement password validation #123"

# Or mention multiple items:
git commit -m "Fix OAuth callback handling AB#456 AB#789"
```

The `AB#` prefix explicitly references an Azure Boards item by ID. Azure DevOps automatically links the commit to the work item. The work item shows the commit in its Development section.

### From pull requests

When creating or editing a pull request, search for work items in the **Work Items** section. Type `#` and the item ID or title to link.

When a PR is merged and was linked to a work item, the work item automatically transitions (if configured in the project settings) to a completed state.

### From branches

Creating a branch from a work item automatically names the branch with the work item ID and links the branch to the item:

1. Open a work item
2. Click **Create branch** in the Development section
3. Name the branch (defaults to `{id}-{title-slug}`)
4. Select the repository and base branch
5. Click **Create branch**

The work item tracks the branch and any PRs from that branch automatically.

### Transitions triggered by PR completion

Azure DevOps can be configured to automatically close or resolve linked work items when a pull request is merged. This is configured in **Project settings** > **Boards** > **Project configuration** > **Automatic transitions**.

---

## Reporting and dashboards

### Dashboards

Dashboards are customisable pages of widgets showing team metrics. Each team can have multiple dashboards.

1. Click **Overview** > **Dashboards**
2. Click **+ New dashboard** to create one
3. Click **Edit** > **+ Widget** to add widgets

Popular widgets:
- **Burndown chart**: sprint or release burndown
- **Velocity**: points completed per sprint over time
- **Cumulative flow diagram**: tracks items moving through workflow states
- **Lead time/cycle time**: how long items take from creation to completion
- **Query results**: show a live view of any saved query
- **Work item chart**: donut or bar chart of items by state, type or assignee
- **Pipeline status**: build status for specific pipelines
- **Sprint capacity**: current sprint capacity bar

### Analytics views

The **Analytics** section provides more powerful reporting using Azure DevOps Analytics (based on OData). You can build custom views and export data to Power BI for advanced reporting.

### Azure DevOps and Power BI

Azure DevOps has a native Power BI connector. In Power BI Desktop, connect to Azure DevOps using the Azure DevOps connector, authenticate, and pull work item data into Power BI reports. This is the most powerful reporting option for teams that need custom metrics, trend analysis or executive dashboards.

---

## Teams within a project

A large project may have multiple teams working in the same Azure DevOps project. Azure DevOps supports this with the **Teams** concept.

### Default team

Every project has a default team with the same name as the project. This team owns the default area path and the default board.

### Creating additional teams

1. Go to **Project settings** > **Teams**
2. Click **New team**
3. Give the team a name
4. Add members
5. Configure the team's area path (the subset of work items this team owns)
6. Configure the team's iteration schedule

### Area paths

Area paths categorise work items by feature area or team. Each team is associated with one or more area paths. Work items in a team's area path appear on that team's board and backlog.

Example structure:
```
MyProject                    (root area)
  ├── Frontend                (Frontend team's area)
  ├── Backend                 (Backend team's area)
  │     ├── API
  │     └── Database
  └── Infrastructure          (DevOps team's area)
```

Configure area paths in **Project settings** > **Boards** > **Project configuration** > **Areas**.

---

## Try It Yourself

**Exercise 1 - Create work items**

1. Go to **Boards** in your Azure DevOps project
2. Click **+ Add work item** in the "New" column
3. Title: "Set up project repository structure"
4. Press Enter to create
5. Click the card to open the full form
6. Add a description, assign to yourself, add a tag

**Exercise 2 - Create a sprint**

1. Go to **Project settings** > **Boards** > **Project configuration** > **Iterations**
2. Add a child iteration called "Sprint 1" with start and end dates 2 weeks apart
3. Go to **Boards** > **Sprints**
4. You should see Sprint 1 in the dropdown

**Exercise 3 - Plan a sprint**

1. Go to **Backlogs**
2. Create 3-5 work items (User Stories or Backlog Items)
3. From the sprint backlog view, drag items from the backlog into Sprint 1
4. Go to the **Sprint** view and see the items assigned

**Exercise 4 - Write a query**

1. Go to **Queries** > **New query**
2. Set conditions:
   - Work Item Type = User Story
   - State = Active
   - Assigned To = @Me
3. Click **Run query**
4. Save as "My active stories"

**Exercise 5 - Link a commit to a work item**

1. Note the ID of a work item (e.g. 42)
2. In your local repository:

```bash
echo "# Sprint 1" >> sprint-notes.txt
git add sprint-notes.txt
git commit -m "Add sprint notes AB#42"
git push
```

3. Open work item 42 in Azure Boards and check the Development section - the commit should appear

---

## Common Mistakes

**Choosing the wrong process template and not changing it**

The process template affects all work item types and workflows. If you choose Scrum but your team uses User Stories (Agile terminology), you will work around the wrong work item types constantly. Spend 5 minutes choosing the right template. If you chose wrong, change it early before the project accumulates work items.

**Not using area paths for team separation**

In a multi-team project, all teams see all work items unless area paths separate them. Set up area paths immediately when multiple teams start working in the same project. Without them, each team's board shows every team's work.

**Not linking commits to work items**

The link between code and work items is one of Azure Boards' strongest features. If developers do not include work item references in commits, the traceability chain breaks. Add this to your team's commit message convention from day one.

**Overcomplicating the board**

Teams new to Azure Boards often create many swimlanes, many custom columns and complex WIP rules. Start simple: default columns, no swimlanes. Add complexity only when a specific problem demands it.

**Not closing work items when PRs are merged**

Work items linger in Active state long after the code is merged. Configure automatic transitions in project settings, or make it a team habit to close work items when PRs are completed.

**Using only the Kanban board and ignoring the backlog**

The board is for in-flight work. The backlog is for future work and sprint planning. Teams that only use the board lose the prioritisation and sprint planning capabilities that make Azure Boards valuable.

---

## Summary

Azure Boards provides work items, Kanban boards, sprint planning, backlogs, queries, delivery plans and dashboards. Choose a process template (Agile is recommended for most teams) when creating the project - this determines work item types and workflow states.

Work items are the fundamental unit. They have states, assignments, iterations, area paths, descriptions, acceptance criteria and links to code. The hierarchy (Epics → Features → Stories → Tasks) enables planning at multiple levels simultaneously.

The board provides a visual Kanban view. Customise columns to match your workflow and set WIP limits to surface bottlenecks. The backlog provides list-based management with drag-to-prioritise and sprint assignment.

Sprints (iterations) are time-boxed delivery periods. Capacity planning, burndown charts and velocity tracking are built in. Queries answer arbitrary questions about work items. Delivery plans show multi-team, multi-sprint timelines for programme-level planning.

Link code to work items via commit message IDs (`AB#42`), PR work item references and branch creation from work items. This traceability from requirement to code to deployment is Azure Boards' core value proposition.

---

## Sources

- [Microsoft Learn: Azure Boards documentation](https://learn.microsoft.com/azure/devops/boards/)
- [Microsoft Learn: Choose a process](https://learn.microsoft.com/azure/devops/boards/work-items/guidance/choose-process)
- [Microsoft Learn: Work items](https://learn.microsoft.com/azure/devops/boards/work-items/)
- [Microsoft Learn: Sprints and Scrum](https://learn.microsoft.com/azure/devops/boards/sprints/)
- [Microsoft Learn: Queries](https://learn.microsoft.com/azure/devops/boards/queries/)
- [Microsoft Learn: Delivery plans](https://learn.microsoft.com/azure/devops/boards/plans/)
- [Microsoft Learn: Dashboards](https://learn.microsoft.com/azure/devops/report/dashboards/)
- [Microsoft Learn: Link work items to development](https://learn.microsoft.com/azure/devops/boards/backlogs/add-link)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

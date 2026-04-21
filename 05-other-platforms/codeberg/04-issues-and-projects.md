# Issues and Projects on Codeberg

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Codeberg's issue tracker and project boards are Forgejo's, unchanged. All the features covered in [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md) apply directly on Codeberg. This file focuses on practical patterns for Codeberg's specific context: open source project management, community contribution workflows and the norms that shape how Codeberg projects typically organise their work.

---

## Table of Contents

1. [Issues on Codeberg](#issues-on-codeberg)
2. [Labels and milestones](#labels-and-milestones)
3. [Issue templates for open source projects](#issue-templates-for-open-source-projects)
4. [Project boards](#project-boards)
5. [Community contribution workflows](#community-contribution-workflows)
6. [Cross-repository references and federation](#cross-repository-references-and-federation)
7. [Try It Yourself](#try-it-yourself)
8. [Common Mistakes](#common-mistakes)
9. [Summary](#summary)
10. [Sources](#sources)

---

## Issues on Codeberg

Issues on Codeberg function identically to Forgejo issues. Create them from **Issues** > **New Issue**, use Markdown for formatting, mention users with `@username`, reference other issues with `#number` and close them automatically with `Closes #number` in commit messages or PR descriptions.

### Public issues on public repositories

For public repositories, anyone with a Codeberg account can open issues. This is core to the open source contribution model - users report bugs, request features and ask questions without needing write access to the repository.

### Managing incoming issues as a maintainer

Open source maintainers on Codeberg commonly use these patterns:

**Triage labels**: a `needs-triage` or `unconfirmed` label applied automatically by a template or manually on creation. Maintainers review and remove it when the issue is confirmed.

**Duplicate tracking**: use the issue body to link to the canonical issue (`Duplicate of #42`). Close the duplicate as "duplicate" rather than just "won't fix".

**Upstream label**: for projects that package other software, `upstream` labels issues that need to be fixed in the dependency, not in your project. Link to the upstream issue tracker.

**Stale management**: if an issue lacks a response from the reporter for weeks, comment asking for more information. Codeberg does not have an automated stale bot like GitHub's stale action, so this is manual.

### Issue reactions

Users can react to issues and comments without cluttering the thread with "+1" comments. Common reactions:
- 👍 agree / want this feature
- 👎 disagree / do not want this
- ❤️ appreciate the report
- 🎉 celebrating a fix

Encourage voters to use reactions rather than "+1 comments" to keep issue threads clean.

---

## Labels and milestones

### Setting up labels for an open source project

Create these label sets when starting a project:

**Type:**
- `bug` (red) - something is broken
- `enhancement` (blue) - new feature or improvement
- `documentation` (teal) - documentation improvements
- `question` (purple) - needs clarification
- `security` (dark red) - security issues (consider making these private initially)

**Status:**
- `needs-triage` (orange) - new, not yet evaluated
- `needs-info` (yellow) - waiting for more information from the reporter
- `confirmed` (green) - bug confirmed, feature accepted
- `in-progress` (light blue) - someone is working on it
- `blocked` (dark red) - waiting on something external
- `wontfix` (grey) - valid but will not be addressed
- `duplicate` (grey) - same as another issue

**Difficulty (for contribution onboarding):**
- `good first issue` (green) - suitable for first-time contributors
- `help wanted` (teal) - maintainer needs assistance

The `good first issue` label is particularly valuable on Codeberg. New contributors browse this label to find approachable entry points to projects.

### Milestones for release planning

Use milestones to track what is planned for each release:

1. **Issues** > **Milestones** > **New Milestone**
2. Name: `v1.0.0`, `v1.1.0`, `Next release` etc.
3. Set a due date
4. Assign issues

The milestone progress bar shows how many issues are resolved. Closing all issues in a milestone signals readiness for a release.

---

## Issue templates for open source projects

Issue templates are especially valuable for public projects that receive issues from users of varying technical depth.

### Bug report template

Create `.forgejo/ISSUE_TEMPLATE/bug_report.md`:

```markdown
---
name: Bug Report
about: Something is not working as expected
labels: bug, needs-triage
---

## What happened?

<!-- A clear description of the problem -->

## What did you expect to happen?

<!-- What should have happened instead -->

## Steps to reproduce

1. <!-- First step -->
2. <!-- Second step -->
3. <!-- etc. -->

## Environment

- Version: <!-- the version of this software (check --version or the release page) -->
- OS: <!-- e.g. Ubuntu 22.04, Windows 11, macOS 14 -->
- Any relevant configuration:

## Additional context

<!-- Screenshots, logs, or any other helpful information -->

<!-- For security vulnerabilities, please do NOT open a public issue. -->
<!-- Instead, email the maintainers or use private issue reporting if available. -->
```

### Feature request template

Create `.forgejo/ISSUE_TEMPLATE/feature_request.md`:

```markdown
---
name: Feature Request
about: Suggest a new feature or improvement
labels: enhancement, needs-triage
---

## Problem

<!-- What problem are you trying to solve? What is frustrating or missing? -->

## Proposed solution

<!-- How would you like this to work? -->

## Alternatives considered

<!-- Other approaches you have thought about -->

## Who would benefit from this?

<!-- Describe who this helps and how commonly this situation occurs -->
```

### Security vulnerability template

For projects where security issues are possible, configure private reporting:

1. Repository **Settings** > **Security** > enable **Private vulnerability reporting**
2. Add a note in your public bug template directing security issues to the private channel

```markdown
<!-- .forgejo/ISSUE_TEMPLATE/config.yml -->
blank_issues_enabled: true
contact_links:
  - name: Security Vulnerability
    url: https://codeberg.org/username/repo/security/advisories/new
    about: Please report security issues privately, not as public issues
```

---

## Project boards

Codeberg project boards (Kanban-style) are Forgejo project boards. Create them from **Projects** > **New Project**.

### Suggested column setup for open source projects

| Column | Purpose |
|---|---|
| **Backlog** | Accepted issues not yet scheduled for work |
| **Planned** | Scheduled for the current release or sprint |
| **In Progress** | Someone is actively working on it |
| **In Review** | PR submitted, awaiting review |
| **Done** | Merged or resolved |

### Organisation-level projects

If you maintain multiple related repositories under an organisation, use organisation-level projects to track work across all repositories:

1. Go to your organisation page
2. Click **Projects** > **New Project**
3. Add issues from any repository in the organisation

This gives a unified view of all work across a project family (e.g. a library and its CLI tool, both under the same org).

---

## Community contribution workflows

### The standard open source PR flow

For projects receiving contributions from external contributors:

1. **Contributor forks the repository** on Codeberg
2. **Contributor creates a branch** in their fork
3. **Contributor pushes** and opens a PR from their fork to your repository
4. **Maintainer reviews** using inline comments and the review system
5. **Maintainer requests changes or approves**
6. **Maintainer merges** when ready

This fork-and-PR model means you do not need to grant write access to contributors. They can contribute via PRs without any special permissions.

### CONTRIBUTING.md

A well-written `CONTRIBUTING.md` reduces friction for contributors and reduces maintainer workload answering the same questions repeatedly.

Essential sections:
- How to report bugs (link to issue templates)
- How to suggest features
- How to set up the development environment
- Code style and standards
- How to run the test suite
- The PR review process and what maintainers look for
- How to ask questions (issues, discussions, matrix/IRC if available)

### Good first issues for onboarding

Tag issues `good first issue` that are:
- Self-contained (do not require deep knowledge of the codebase)
- Well-specified (clear acceptance criteria)
- Mentored (maintainer is available to guide the contributor)

Codeberg users looking to contribute to open source browse this label. Clear `good first issue` issues attract first-time contributors and grow your contributor base.

---

## Cross-repository references and federation

### Cross-repository issue references

Reference issues in other Codeberg repositories:

```
This duplicates username/other-repo#42
Fixed by username/library!18 (that PR number)
```

These become clickable links in Codeberg's interface.

### Federation and issues

Forgejo's ActivityPub federation is in active development. On Codeberg today:
- Users on other Forgejo instances can follow your repositories and receive notifications of new releases
- Your issue activity on public repositories may be relayed to federated followers
- Cross-instance issue commenting and PR submission are in development

For practical purposes in 2026, treat Codeberg issues as operating within `codeberg.org` - cross-instance issue participation is not yet production-ready.

---

## Try It Yourself

**Exercise 1 - Set up labels**

In your `hello-codeberg` repository:
1. Go to **Issues** > **Labels**
2. Create the standard label set: `bug`, `enhancement`, `documentation`, `good first issue`
3. Give each an appropriate colour

**Exercise 2 - Create your first issue**

1. **Issues** > **New Issue**
2. Title: "Add installation instructions to README"
3. Body: "The README currently has no installation instructions. Add a section covering pip install, brew install and manual installation from source."
4. Label: `documentation`, `good first issue`
5. Submit

**Exercise 3 - Add issue templates**

1. Create `.forgejo/ISSUE_TEMPLATE/bug_report.md` with the template from this file
2. Create `.forgejo/ISSUE_TEMPLATE/feature_request.md`
3. Commit and push to main
4. Click **New Issue** - the template selection screen appears

**Exercise 4 - Create a project board**

1. Go to **Projects** > **New Project**
2. Name: "v1.0.0 Roadmap"
3. Create columns: Backlog, In Progress, Done
4. Add your documentation issue to the Backlog

---

## Common Mistakes

**Not using `good first issue` labels**

Open source projects on Codeberg often miss contributor opportunities because they do not label approachable issues. Every project has tasks suitable for newcomers. Label them and check them are actually approachable (clear description, bounded scope, mentoring available).

**Writing issue templates that are too long**

An issue template with 15 required fields discourages reporters from filing issues. Keep templates focused on the information you actually need to reproduce or evaluate the issue. Bug reports need reproduction steps and environment. Feature requests need the problem statement.

**Not having a CONTRIBUTING.md**

New contributors asking "how do I contribute?" in an issue takes maintainer time to answer individually. A `CONTRIBUTING.md` answers this once for everyone. It is one of the highest-value documentation documents for an open source project.

**Using issues as a support forum**

If users consistently open issues asking "how do I do X?", consider adding a GitHub Discussions equivalent or pointing users to a forum, Matrix room or mailing list for support questions. Keep the issue tracker focused on bugs and feature requests.

---

## Summary

Codeberg issues, labels, milestones and project boards are Forgejo's features, working identically to Forgejo. The focus on Codeberg is the open source contribution workflow: public issues from the community, fork-and-PR contribution model, `good first issue` labelling for onboarding, and CONTRIBUTING.md documentation.

Issue templates for bug reports and feature requests reduce friction and improve issue quality. Organisation-level project boards provide unified tracking across multi-repository projects.

Cross-repository references link issues and PRs across Codeberg repositories. ActivityPub federation allows followers on other Forgejo instances to receive notifications about your public repositories.

---

## Sources

- [Forgejo: Issues documentation](https://forgejo.org/docs/latest/user/issue-and-pull-request-tracking/)
- [Forgejo: Issue templates](https://forgejo.org/docs/latest/user/issue-pull-request-templates/)
- [Forgejo: Projects](https://forgejo.org/docs/latest/user/projects/)
- [Codeberg: Community wiki](https://codeberg.org/Codeberg/Community/wiki)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

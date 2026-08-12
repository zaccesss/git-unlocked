# Issues, Packages and Federation in Forgejo

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

This file covers three distinct but related topics: issues and project management (identical to Gitea), the package registry (identical to Gitea) and ActivityPub federation - Forgejo's distinctive feature that does not exist in Gitea. Issues and packages are covered concisely since they match the Gitea behaviour; federation gets the depth it deserves as the feature that sets Forgejo apart.

---

## Table of Contents

1. [Issues and project management](#issues-and-project-management)
2. [Labels, milestones and assignees](#labels-milestones-and-assignees)
3. [Issue templates](#issue-templates)
4. [Kanban project boards](#kanban-project-boards)
5. [The package registry](#the-package-registry)
6. [ActivityPub federation](#activitypub-federation)
7. [Try It Yourself](#try-it-yourself)
8. [Common Mistakes](#common-mistakes)
9. [Summary](#summary)
10. [Sources](#sources)

---

## Issues and project management

Forgejo issues are identical to Gitea issues: create, comment, label, assign, milestone, react and close. All the same keyboard shortcuts, Markdown support and cross-reference syntax apply.

### Creating an issue

1. Repository > **Issues** > **New Issue**
2. Fill in title and description (Markdown supported)
3. Set metadata in the sidebar: labels, assignees, milestone, project
4. Click **Submit New Issue**

### Commenting and mentions

All comments support full Markdown, `@username` mentions, `#issue-number` cross-references and reactions (👍 👎 ❤️ 🎉 😕 👀).

### Closing issues via commits

Include closing keywords in commit messages or PR descriptions:

```
Fixes #42
Closes #43
Resolves #44
```

Issues close automatically when the commit lands on the default branch.

### Issue dependencies

Link issues as dependent:
1. Open an issue
2. **Dependencies** in the sidebar > **Add dependency**
3. Select the blocking issue

The dependent issue shows a warning when its dependency is still open.

### Pinned issues

Pin up to 3 issues to the top of the issue list. Click **Pin Issue** in the issue sidebar (available to maintainers). Use for announcements, contribution guidelines or known critical bugs.

---

## Labels, milestones and assignees

### Labels

Create project-specific labels at **Issues** > **Labels** > **Create Label**. Set name, colour and description. Apply to issues by clicking **Labels** in the sidebar.

Organisation-level labels apply to all repositories in the organisation.

**Suggested label sets:**

Type: `bug`, `enhancement`, `documentation`, `question`

Status: `needs-investigation`, `blocked`, `in-progress`, `duplicate`, `wontfix`

Priority: `priority: critical`, `priority: high`, `priority: low`

### Milestones

Group issues toward a release or sprint:

1. **Issues** > **Milestones** > **New Milestone**
2. Set name, description and due date
3. Assign issues from any issue's sidebar

The milestone list shows progress bars (open vs closed issues). Close the milestone when the goal is reached.

### Assignees

Assign issues and PRs to responsible team members from the sidebar. Assignees receive email notifications and the issue appears in their assigned view.

---

## Issue templates

Issue templates pre-fill the new issue form to prompt reporters for relevant information.

### Creating templates

Create `.forgejo/ISSUE_TEMPLATE/bug_report.md`:

```markdown
---
name: Bug Report
about: Report a bug or unexpected behaviour
labels: bug
---

## Describe the bug

<!-- A clear description of the issue -->

## Steps to reproduce

1. Go to...
2. Click on...
3. See error

## Expected behaviour

<!-- What should happen -->

## Actual behaviour

<!-- What actually happens -->

## Environment

- Forgejo version: <!-- e.g. 9.0.1 -->
- OS: <!-- e.g. Ubuntu 22.04 -->
- Browser: <!-- e.g. Firefox 124 -->
```

Create `.forgejo/ISSUE_TEMPLATE/feature_request.md`:

```markdown
---
name: Feature Request
about: Suggest a new feature or improvement
labels: enhancement
---

## Problem statement

<!-- What problem does this solve? -->

## Proposed solution

<!-- How should this work? -->

## Alternatives considered

<!-- Other approaches -->
```

### Template front matter

| Field | Purpose |
|---|---|
| `name` | Template name in the selector |
| `about` | Short description shown below the name |
| `labels` | Labels automatically applied |
| `assignees` | Users automatically assigned |

---

## Kanban project boards

Forgejo project boards provide visual workflow management.

### Creating a project

Repository > **Projects** > **New Project**. Set a name and create columns matching your workflow (e.g. Backlog, In Progress, In Review, Done).

Organisation projects (visible across all org repositories): Organisation > **Projects**.

### Managing boards

- **Add issues**: from the project board > **+ Add existing issues** or from any issue > **Projects** in sidebar
- **Move cards**: drag and drop between columns
- **Filter**: by assignee, label or milestone
- **Archive**: when a project is complete, archive it to hide from the active list

---

## The package registry

Forgejo's package registry is identical to Gitea's - the same 23+ formats, the same URL structure, the same authentication model. The registry endpoint uses the Forgejo domain:

```
https://forgejo.example.com/api/packages/{owner}/{type}/
```

All package types are supported: Container/Docker (OCI), npm, PyPI, NuGet, Maven, Cargo, Helm, Debian, Alpine, RPM, RubyGems, Go, Generic and more.

See [../gitea/08-packages.md](../gitea/08-packages.md) for complete configuration examples for each package type. The commands are identical - replace `gitea.example.com` with `forgejo.example.com`.

### Key points for Forgejo

**Docker/OCI:**
```bash
docker login forgejo.example.com -u username -p YOUR_TOKEN
docker build -t forgejo.example.com/username/my-app:1.0.0 .
docker push forgejo.example.com/username/my-app:1.0.0
```

**npm:**
```
@your-scope:registry=https://forgejo.example.com/api/packages/username/npm/
//forgejo.example.com/api/packages/username/npm/:_authToken=YOUR_TOKEN
```

**Generic packages:**
```bash
curl -X PUT \
  -H "Authorization: token YOUR_TOKEN" \
  --upload-file my-tool \
  "https://forgejo.example.com/api/packages/username/generic/my-tool/1.0.0/my-tool-linux-amd64"
```

---

## ActivityPub federation

ActivityPub federation is the feature that makes Forgejo unique. No other Git hosting platform in active development is building this with the same commitment. This section explains what federation is, what it currently enables and where it is going.

### What is ActivityPub?

ActivityPub is an open protocol standardised by the W3C. It powers the Fediverse: Mastodon (microblogging), PeerTube (video), Pixelfed (photos), Lemmy (link aggregation) and others. All these platforms can interact because they speak the same protocol.

In the ActivityPub model, each server has an **actor** (a user or application identity) that can **follow** other actors on remote servers, send **activities** (like creating a post, following someone, liking content) and receive activities from others. The protocol is federated - there is no central server.

### ForgeFed: ActivityPub for Git forges

**ForgeFed** is an ActivityPub extension specification designed for software development platforms. It defines how Git forges (platforms like Forgejo) communicate with each other. The ForgeFed working group includes Forgejo contributors as primary implementers.

ForgeFed adds concepts like:
- **Repository actor**: a repository has its own ActivityPub identity
- **Issue activity**: creating an issue on one instance can notify followers on other instances
- **Fork activity**: forking a repository across instances
- **PR/MR activity**: opening a pull request to a remote repository

### What federation currently enables (2026)

Federation in Forgejo is actively developed but not all use cases are production-ready. The current stable capabilities:

**Following repositories across instances:**
A user on `forgejo-a.example.com` can follow a repository on `forgejo-b.example.com`. When the remote repository has new releases, commits or other activity, the follower receives notifications.

**User federation identity:**
Each Forgejo user has a federated identity: `@username@forgejo.example.com`. This is discoverable by other ActivityPub platforms and appears in users' profiles.

**Notification delivery:**
Notifications about followed repositories on remote instances are delivered to the user's notification feed.

**In development / not yet production-stable:**
- Cross-instance forking (fork a repository from another Forgejo instance to your own)
- Cross-instance issue and PR commenting (participate in discussions on remote instances)
- Cross-instance PR submission (submit a pull request from your instance to another)

### Enabling federation

In `app.ini`:

```ini
[federation]
ENABLED = true
```

Restart Forgejo. Once enabled:
- User profiles show their ActivityPub identity
- The `/.well-known/webfinger` endpoint becomes available
- The `/.well-known/nodeinfo` endpoint is updated to indicate federation capability

### Following a repository on another instance

1. Go to a repository on another Forgejo instance
2. Copy the repository's full URL (`https://other-forgejo.example.com/username/repo`)
3. On your home Forgejo instance: navigate to the remote repository via your instance's **Explore** or search
4. Click **Follow** to subscribe to updates

Or programmatically via the ActivityPub API:

```bash
# Your Forgejo instance notifies the remote that you follow it
curl -X POST \
  -H "Content-Type: application/activity+json" \
  -H "Authorization: token YOUR_TOKEN" \
  -d '{
    "@context": "https://www.w3.org/ns/activitystreams",
    "type": "Follow",
    "actor": "https://your-forgejo.example.com/api/v1/activitypub/user-id/YOUR_USER_ID",
    "object": "https://other-forgejo.example.com/api/v1/activitypub/repository-id/REPO_ID"
  }' \
  https://your-forgejo.example.com/api/v1/activitypub/follow
```

### The federated identity

When federation is enabled, each user's profile page shows their ActivityPub identity. For `alice` on `forgejo.example.com`:

```
@alice@forgejo.example.com
```

This identity is discoverable by any ActivityPub-aware application. A Mastodon user could, in principle, follow `@alice@forgejo.example.com` and receive updates about Alice's public Forgejo activity in their Mastodon timeline.

### NodeInfo

Forgejo exposes a `nodeinfo` endpoint that federation-aware tools use to discover the instance's capabilities:

```bash
curl https://forgejo.example.com/.well-known/nodeinfo
curl https://forgejo.example.com/api/v1/nodeinfo
```

The response includes the software name (`forgejo`), version and whether federation is enabled.

### Federation and privacy

Federation means your public activity on a Forgejo instance may be relayed to other instances. Consider:

- **Public repositories**: activity (issues, releases) on public repos may be federated to followers on other instances
- **Private repositories**: private repository activity is not federated
- **User privacy**: your federated identity is derived from your username and instance domain - it is public

For instances where all repositories are internal and federation is not needed, leave `ENABLED = false` in the federation configuration.

### The long-term vision

The Forgejo and ForgeFed teams envision a future where:

- A developer on `university.forgejo.example` can participate in issues on `github.com/some-project` without a GitHub account (once GitHub supports ActivityPub, which is not planned)
- A developer can fork a repository from any Forgejo instance to their home instance and submit pull requests back
- A developer has a single federated identity that represents their contributions across any platform in the Fediverse

This is ambitious and long-term. The current reality is basic notification federation between Forgejo instances. The trajectory is clear and the work is ongoing.

---

## Try It Yourself

**Exercise 1 - Set up issues and labels**

On Codeberg or your own Forgejo instance:
1. Create a repository
2. Create 5 labels: `bug` (red), `enhancement` (blue), `documentation` (teal), `priority: high` (orange), `blocked` (dark red)
3. Create 3 issues with different labels and assign a milestone

**Exercise 2 - Create a project board**

1. Repository > **Projects** > **New Project**
2. Name it "Sprint 1"
3. Create columns: Backlog, In Progress, Review, Done
4. Add your 3 issues to the board
5. Move them between columns

**Exercise 3 - Set up issue templates**

1. Create `.forgejo/ISSUE_TEMPLATE/bug_report.md` with a template
2. Commit to main
3. Click **New Issue** - the template selection appears
4. Use the bug report template and see the pre-filled form

**Exercise 4 - Explore federation (if available)**

On Codeberg:
1. Go to your profile settings
2. Look for your federated identity (`@username@codeberg.org`)
3. Try searching for it in a Mastodon instance to see if it is discoverable
4. Browse to another Forgejo instance and look for a repository with federation enabled

---

## Common Mistakes

**Expecting cross-instance PRs to work in 2026**

Cross-instance pull requests are not yet production-ready. If your use case requires submitting PRs from one Forgejo instance to another, you need to create accounts on both instances (the traditional approach). Check the current state of federation features at the time of your evaluation.

**Enabling federation without understanding the privacy implications**

When federation is enabled, your public repository activity is potentially relayed to subscribers on other instances. For internal corporate Forgejo instances, this is likely undesirable. Only enable federation on instances intended for public-facing open source work.

**Assuming the package registry needs separate configuration vs Gitea**

The package registry is enabled by default and works identically to Gitea. There is nothing Forgejo-specific to configure - just replace the domain name in the registry URLs.

**Not creating issue templates before the project is active**

Issue templates are much more valuable when established early. Retrofitting them after many issues have been filed does not improve the quality of existing reports. Set up templates when creating a repository.

---

## Summary

Forgejo issues, labels, milestones, project boards and templates are identical to Gitea's. Create issues from the web UI, use closing keywords in commits (`Closes #123`), pin important issues and track work on Kanban boards.

The package registry supports 23+ formats with the same URL structure as Gitea. Replace `gitea.example.com` with your Forgejo domain in any Gitea package configuration.

ActivityPub federation is Forgejo's distinctive feature. When enabled, users have federated identities (`@user@instance`) discoverable across the Fediverse. Following repositories across Forgejo instances delivers notifications. Cross-instance forking and PR submission are in development. Federation is enabled with `ENABLED = true` under `[federation]` in `app.ini`.

The long-term vision is a fully federated forge network where developers contribute to projects across instances without needing accounts everywhere.

---

## Sources

- [Forgejo: Issues documentation](https://forgejo.org/docs/latest/user/issue-and-pull-request-tracking/)
- [Forgejo: Package registry](https://forgejo.org/docs/latest/user/packages/)
- [Forgejo: Federation documentation](https://forgejo.org/docs/latest/user/federation/)
- [ForgeFed specification](https://forgefed.org)
- [W3C: ActivityPub specification](https://www.w3.org/TR/activitypub/)
- [Forgejo: Federation design document](https://codeberg.org/forgejo/forgejo/issues/59)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

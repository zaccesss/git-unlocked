# Projects and Namespaces

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

On GitLab, the word "repository" barely scratches the surface of what you are actually working with. GitLab organises everything around **projects** - and a project is much more than a Git repository. It is the container for your code, your issues, your merge requests, your CI/CD pipelines, your wiki, your container images and your packages. Understanding how projects work, how they sit inside namespaces, and how groups and subgroups structure an organisation is foundational to using GitLab effectively.

This file covers the full picture: the difference between personal and group namespaces, creating and configuring projects, visibility levels, forking, project templates, the Web IDE, importing from GitHub and how GitLab's group hierarchy compares to GitHub's organisations and teams.

---

## Table of Contents

- [What is a GitLab project?](#what-is-a-gitlab-project)
- [What is a namespace?](#what-is-a-namespace)
- [Personal namespaces](#personal-namespaces)
- [Groups](#groups)
- [Subgroups](#subgroups)
- [Creating a project](#creating-a-project)
- [Project settings and configuration](#project-settings-and-configuration)
- [Visibility levels](#visibility-levels)
- [Forking projects](#forking-projects)
- [Project templates](#project-templates)
- [The Web IDE](#the-web-ide)
- [Importing and migrating projects](#importing-and-migrating-projects)
- [Archiving, transferring and deleting projects](#archiving-transferring-and-deleting-projects)
- [How this compares to GitHub](#how-this-compares-to-github)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a GitLab Project?

In GitHub, a repository is mostly a Git repository with some attached features bolted on: issues, pull requests, Actions, a wiki. The repository is the core object, and everything else orbits it.

In GitLab, a **project** is a richer, more unified object. It bundles everything related to building and shipping software into one place:

**Source control:**

- A Git repository with full history, branches and tags
- Protected branch and tag rules
- Deploy keys and deploy tokens for automated access
- Repository mirroring (push or pull from an external Git source like GitHub or Bitbucket)

**Code review and collaboration:**

- Merge requests with inline comments, approval workflows and direct pipeline integration
- Code ownership definitions via the CODEOWNERS file
- Issue tracker with labels, milestones, sub-issues, time tracking and boards
- Wikis for project documentation (separate Git repository under the hood)
- Snippets for versioned code fragments

**Automation:**

- CI/CD pipelines defined in `.gitlab-ci.yml`
- Scheduled pipelines for recurring jobs (nightly builds, weekly reports)
- Pipeline triggers for API-driven automation
- Environments and deployment tracking (who deployed what, when and where)
- Review apps - live per-branch deployments for every merge request

**Publishing and distribution:**

- GitLab Pages for free static site hosting
- Container Registry for Docker/OCI images
- Package Registry for language-specific packages (npm, PyPI, Maven, NuGet, Cargo, Composer, Helm and more)
- Release management with versioned downloadable assets

**Security:**

- SAST, secret detection, dependency scanning and container scanning (Free tier)
- DAST and fuzz testing (Ultimate)
- Vulnerability report directly linked to the merge request that introduced each finding
- Security policies and compliance frameworks (Ultimate)

**Project management:**

- Issues, labels, milestones, boards
- Service Desk - inbound email from customers automatically creates confidential issues
- Time tracking and estimates on issues and merge requests
- Feature flags for controlled feature rollouts

All of these features are individually toggleable. If your project is just a library with no need for an issue tracker, turn it off. If you do not want Pages, leave it disabled. The project is your workspace, shaped to your actual needs.

> [!NOTE]
> When GitLab documentation or UI says "repository", it almost always means the Git portion of the project specifically. When it says "project", it means the whole container including all the features above. These are not interchangeable terms.

---

## What is a Namespace?

A namespace in GitLab is the container that holds projects. It determines the URL structure of your projects and controls who can access them.

Every project URL on GitLab follows this pattern:

```
https://gitlab.com/NAMESPACE/project-name
```

There are two kinds of namespace:

**Personal namespace**: your username. When you create a project without selecting a group, it lives under your personal namespace.

```
https://gitlab.com/zaccess/my-project
```

**Group namespace**: the name of a group you belong to. Projects in a group live under that group's path, which can be nested through subgroups.

```
https://gitlab.com/my-company/frontend/my-project
```

Understanding namespaces matters for practical reasons:

- Access control cascades through the namespace hierarchy - members of a parent group inherit access to all projects inside it
- CI/CD variables defined at the group level are automatically inherited by all projects in that group and all subgroups below it
- GitLab Runner registration can be scoped to a group, making the runner available to every project in the group automatically
- Free tier limits (five users per private group, three top-level groups) apply to group namespaces, not personal namespaces
- Package and container registry URLs include the full namespace path, so the namespace you choose affects every image tag and package URL you publish

---

## Personal Namespaces

Your personal namespace is created automatically when you sign up. It is simply your username.

Every project you create in your personal namespace lives at:

```
https://gitlab.com/your-username/project-name
```

**Key characteristics of personal namespaces:**

**No user limit.** The five-user group limit on the Free tier does not apply to personal namespaces. You can invite unlimited collaborators to projects in your personal namespace. This makes your personal namespace the right home for open source projects or collaboration with many contributors.

**No subgroups.** You cannot create sub-namespaces within a personal namespace. All your personal projects live at the same level, one layer deep. If you need to organise projects hierarchically, you need a group.

**You are always Owner.** You have the Owner role on every project in your personal namespace. This cannot be changed - you cannot demote yourself below Owner in your own namespace.

**Per-project visibility.** Each project in your personal namespace can be individually set to Public or Private (Internal on self-managed). There is no group-level visibility override.

**Your profile README.** The special profile project - created by making a public project whose name exactly matches your username - lives in your personal namespace. If your username is `zaccess`, the project at `gitlab.com/zaccess/zaccess` provides the README that appears on your profile page.

**Best for:**

- Individual side projects and experiments
- Open source work where you are the sole maintainer
- Prototyping before a project moves to a team group
- Your profile showcase project

**Not ideal for:**

- Team projects (no subgroup organisation, no shared runners or variables at the namespace level)
- Projects you expect to grow into a team effort (transferring to a group later changes all URLs)
- Anything requiring group-level settings like SAML SSO or group-level CI/CD variable inheritance

---

## Groups

A group is a named namespace that holds projects and members together, with shared settings that apply across everything inside it.

Groups are the GitLab equivalent of a GitHub organisation - but with important additional capabilities, primarily the ability to nest groups inside each other through subgroups.

### Creating a group

Click **Create new (+)** in the top navigation bar → **New group** → **Create group**.

Fill in:

- **Group name**: the display name shown in the UI (can contain spaces and special characters)
- **Group URL**: the slug used in all URLs. Choose this with care - changing it later breaks every existing link to every project in the group, and invalidates any webhooks or integrations pointing to those URLs
- **Visibility level**: Public (anyone can see the group and its public projects without signing in), Internal (self-managed only - visible to all authenticated users on the instance), or Private (only group members can access anything inside)
- **Your role**: you are set as Owner automatically

After creation, navigate to **Manage → Members** to invite other users. When you add someone to a group, they immediately gain access to every project inside the group at their assigned role level.

### Why use groups instead of personal namespaces?

Groups give you things personal namespaces cannot:

**Shared CI/CD variables.** Define a variable once at the group level and every project and subgroup inside it inherits it automatically. Perfect for organisation-wide secrets like a shared deployment key, a cloud provider access token or a container registry password. Without groups, you would manually add the same variable to every project.

**Shared runners.** Register a GitLab Runner to a group and it becomes available to execute pipelines for every project in that group and every subgroup beneath it. You do not need to register the runner separately to each project.

**Centralised access management.** Add a developer to the group once and they immediately have access to every project inside it. Remove them once and they lose all access. At scale this is dramatically more manageable than adding and removing individuals per project.

**Group labels and milestones.** Labels and milestones defined at the group level appear in the issue trackers of every project in the group. This enables consistent categorisation across a multi-project effort - for example, a `priority::high` scoped label that applies the same way in the frontend, backend and infrastructure projects.

**Group-level wikis (Premium).** A single wiki shared across the entire group, not tied to a specific project. Useful for team documentation, architecture decisions and onboarding guides.

**Analytics across projects.** Group-level analytics aggregate CI/CD metrics, security findings, merge request data and value stream information from every project in the group into a single dashboard.

**SAML SSO (Premium).** Configure single sign-on at the group level so all members authenticate through your company's identity provider (Okta, Azure AD, Entra ID, Google Workspace etc.).

### Group roles

When you add a member to a group, you assign them one of these roles:

| Role           | What they can do                                                                                                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Guest**      | View the project page, view and comment on issues and merge requests (on public or internal projects). Cannot clone private projects.                                                                  |
| **Reporter**   | Everything Guest can do, plus: clone the repository, download artifacts, view pipeline results, create issues, manage labels                                                                           |
| **Developer**  | Everything Reporter can do, plus: push to non-protected branches, create and delete branches, create merge requests, create tags, run pipelines, create environments                                   |
| **Maintainer** | Everything Developer can do, plus: push to protected branches (if allowed), manage branch protection rules, manage project members, edit project settings, enable and disable features, manage runners |
| **Owner**      | Everything Maintainer can do, plus: delete the group or project, change visibility, manage billing, configure SAML SSO, transfer projects, manage all members including other Owners                   |

Two additional roles introduced in recent versions:

| Role                              | What they can do                                                                                                                                                                                      |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Planner** (GitLab 17.7+)        | Create and manage issues, epics, milestones, iterations, roadmap items. A project management focused role that gives planning access without code repository access. Non-billable on GitLab.com.      |
| **Minimal Access** (GitLab 18.9+) | View the group in the navigation sidebar but access almost nothing inside it. Useful when a user needs to be in a group for SSO or billing purposes without needing any project access. Non-billable. |

**How role inheritance works:**

A role granted at the group level flows down to every project in every subgroup, no matter how deeply nested. If you are Developer in `my-company`, you are Developer in every project under `my-company/frontend/`, `my-company/backend/` and `my-company/data/` and everything below those.

You can increase (but not decrease) a role at a lower level. Adding someone as Maintainer on one specific project within a group where they are Developer gives them Maintainer on that project and Developer everywhere else. You cannot grant them Reporter on one project when they are Developer at the group level - the group role is the floor.

---

## Subgroups

Subgroups are groups nested inside other groups. They let you mirror your organisation's real-world team structure inside GitLab, with access control and settings that cascade through the hierarchy.

GitLab supports **up to 20 levels of subgroup nesting**. In practice, three to four levels is the sensible limit before navigation and URL paths become unwieldy.

### Example real-world structure

```
acme-corp/                           (top-level group)
├── engineering/                     (subgroup: the whole eng org)
│   ├── platform/                    (sub-subgroup: platform team)
│   │   ├── kubernetes-configs       (project)
│   │   ├── terraform-modules        (project)
│   │   └── observability-stack      (project)
│   ├── backend/                     (sub-subgroup: backend team)
│   │   ├── payments-service         (project)
│   │   ├── user-service             (project)
│   │   └── notifications-service    (project)
│   └── frontend/                    (sub-subgroup: frontend team)
│       ├── web-app                  (project)
│       └── design-system            (project)
├── data/                            (subgroup: data engineering)
│   ├── etl-pipelines                (project)
│   └── analytics-dashboard          (project)
└── security/                        (subgroup: security team)
    ├── vulnerability-reports        (project)
    └── compliance-docs              (project)
```

In this structure:

- A user added to `acme-corp` with Developer gets Developer access to every project in the entire company
- A user added to `acme-corp/engineering/backend` with Maintainer gets Maintainer access to all three backend projects only, and inherits their `acme-corp` role everywhere else
- CI/CD variables set at `acme-corp` flow down to every project in the entire company
- CI/CD variables set at `acme-corp/engineering/backend` flow only to the three backend projects

### Creating a subgroup

Navigate to the parent group → click **Create new (+)** → **New subgroup** → **Create group**.

Fill in the name, URL slug and visibility. The URL of the new subgroup is automatically prefixed with the parent path:

```
https://gitlab.com/acme-corp/engineering/backend
```

### Subgroup visibility rules

A subgroup's visibility **cannot exceed** its parent group's visibility:

- Inside a public top-level group, subgroups can be public or private
- Inside a private top-level group, all subgroups must also be private
- If you change a parent group from public to private, all its subgroups and projects become private automatically

### Who can create subgroups

By default, Maintainer and Owner roles can create subgroups. This is configurable per group:
Group Settings → General → Permissions → **Allowed to create subgroups**: Owners only, or Maintainers and Owners.

---

## Creating a Project

### From the UI

Click **Create new (+)** in the top navigation → **New project/repository**.

You have three options:

---

**Option 1: Create blank project**

The most common starting point. Configure:

- **Project name**: the display name (can include spaces and special characters)
- **Project URL**: select the namespace (your personal namespace or any group where you have at least Developer access), then set the project slug - the URL-safe identifier. The slug is automatically generated from the name but you can customise it
- **Visibility level**: Public, Private, or Internal (self-managed only)
- **Initialize repository with a README**: creates an initial commit so the repository is not empty. Tick this if starting fresh. Skip it if you are pushing an existing local repository
- **Default branch name**: defaults to `main`. Configurable at the instance, group or project level

After creating the project, GitLab shows you setup commands:

```bash
# Clone via SSH (recommended)
git clone git@gitlab.com:NAMESPACE/project-name.git

# Clone via HTTPS (uses personal access token as password)
git clone https://gitlab.com/NAMESPACE/project-name.git

# Push an existing local repository
cd existing-repo/
git remote add origin git@gitlab.com:NAMESPACE/project-name.git
git branch -M main
git push -uf origin main
```

---

**Option 2: Create from template**

GitLab provides built-in templates for popular use cases. Templates include:

_Pages templates_ - each includes a working `.gitlab-ci.yml` for Pages deployment:

- Jekyll, Hugo, Gatsby, MkDocs, Middleman, Hexo, Pelican, Sphinx, Eleventy, plain HTML

_Framework/language templates_ - starter projects with common structure:

- Ruby on Rails, Spring (Java), Node.js Express, Django (Python), Android, iOS (Swift), Flutter, NestJS

_GitLab-specific templates_:

- GitLab CI/CD example project with annotated `.gitlab-ci.yml`
- Cluster management project template (Kubernetes)

To use a built-in template: **Create from template** → select the **Built-in** tab → choose a template → fill in project name, namespace and visibility → **Use template**.

---

**Option 3: Import project**

Import from an external source. Supported importers:

- **GitHub** (most common migration path)
- **Bitbucket Cloud** and **Bitbucket Server**
- **Gitea**
- **FogBugz**
- **Manifest file** (Android repository structure)
- **Another GitLab instance** (via export/import)
- **Repository by URL** (any accessible Git remote)

Details on the GitHub importer in [Importing and migrating projects](#importing-and-migrating-projects) below.

---

## Project Settings and Configuration

Once a project exists, all its settings live in the left sidebar under **Settings**. Here is what matters in each section.

### Settings → General

**Project name and description**: shown on the project page, in group listings and in search results. A clear description helps collaborators and makes the project discoverable.

**Visibility, project features, permissions**: the most important section. You can:

- Change the project's visibility level (Public/Private)
- Toggle individual features on or off: Issues, Repository, Merge requests, Forks, CI/CD, Analytics, Wiki, Snippets, Pages, Packages and registries, Security and compliance
- Set who can access each feature: Only project members, or Everyone with access

**Merge request settings** (in General → Merge requests):

- **Default merge method**: merge commit (creates a merge commit every time), squash and merge (combines all commits into one), or fast-forward only (requires a linear history - no merge commits ever)
- **Squash commits when merging**: options are Do not allow, Allow (user can choose), Encourage (default is on but user can opt out), or Require (always squash)
- **Merge request approvals**: configure how many approvals are required before a merge is allowed (Premium feature)
- **Status checks**: require external services to report green before merging

**Naming, topics, avatar**: set a project avatar image and add topics (similar to GitHub repository topics) for discoverability.

### Settings → Repository

**Protected branches**: rules that restrict what can happen on specific branches.

```
Branch name pattern: main
Allowed to merge: Maintainers
Allowed to push and merge: No one
Require approval from CODEOWNERS: Yes (Premium)
Require a pipeline to succeed: Yes
```

You can protect by exact name (`main`) or wildcard pattern (`release/*`). Default branch is protected automatically.

**Protected tags**: prevent deletion of tags matching a pattern, or restrict who can create them. Useful for release tags that should not be deleted.

**Deploy keys**: SSH public keys that grant read-only (or read-write) repository access without being tied to a user account. Used by deployment servers, build systems and any automation that needs to clone the repository. Deploy keys do not consume a licence seat.

**Deploy tokens**: similar to deploy keys but token-based. Can grant access to the container registry and package registry in addition to the repository. Also not tied to a user account.

**Repository mirrors**: configure the project to mirror to or from an external Git repository. Pull mirroring keeps the GitLab project in sync with an upstream GitHub repository. Push mirroring keeps a GitHub repository in sync with changes made on GitLab. Useful during migrations or for teams that need presence on both platforms.

### Settings → CI/CD

**General pipelines**:

- **CI/CD configuration file**: by default `.gitlab-ci.yml` at the repository root. You can change this path - useful for monorepos where different services have their own pipeline files
- **Git strategy**: how the runner fetches the repository before each job (`git clone` each time vs `git fetch` and reuse)
- **Git shallow clone depth**: how many commits to include in the clone. Default is 20. Increase for jobs that need full history (e.g. generating changelogs)
- **Timeout**: maximum duration for a pipeline job before it is cancelled (default 60 minutes)

**Runners**: view which runners are available to the project, enable or disable specific runners, and enable or disable group and instance-level runners for this project.

**Variables**: project-level CI/CD variables. These are key-value pairs available as environment variables in every pipeline job. Mark them as **Protected** (only available on protected branches/tags) and/or **Masked** (value never shown in job logs). Never hardcode secrets in `.gitlab-ci.yml` - always use variables.

**Pipeline triggers**: create trigger tokens that allow external systems to start a pipeline via API call:

```bash
curl --request POST \
  --form token=TRIGGER_TOKEN \
  --form ref=main \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/trigger/pipeline"
```

**Token access**: controls whether other projects can use their `CI_JOB_TOKEN` to authenticate against this project's API. By default restricted; explicitly allow specific projects to access yours.

---

## Visibility Levels

Every project and group in GitLab has a visibility level that controls who can see and interact with it.

### Public

The project is accessible to anyone on the internet without signing in. Any visitor can:

- Browse the repository, view files, view commit history, view branches and tags
- View issues and merge requests (unless restricted in project settings)
- View CI/CD pipeline results and job logs
- Clone the repository over HTTPS without authentication

They cannot push code, create issues, open merge requests or otherwise write to the project unless they have an explicit member role.

Public projects on GitLab.com do **not** count toward the five-user group limit. Open source projects should always be public.

### Internal

**Only available on self-managed GitLab** - disabled for new projects on GitLab.com since July 2019.

Internal projects are visible to any authenticated user on the GitLab instance. Nobody outside the instance can see them. This is ideal in corporate environments where you want all employees to be able to see a project and potentially contribute without explicitly adding each person as a member. Common uses: internal documentation, shared tooling, company standards repositories.

### Private

Only users who have been explicitly added as members (or who inherit membership through a group) can see the project at all. Visiting the URL without access shows a 404 page - the project does not even appear to exist.

Private projects in a private group count toward the five-user Free tier limit.

### Visibility inheritance

A project's visibility **cannot exceed** its containing group's visibility. If a group is private, all projects in it must be private - you cannot make one project public inside a private group. If a group is public, projects in it can be independently public or private.

When you change a group from public to private, all projects in that group automatically become private.

---

## Forking Projects

Forking creates an independent copy of a project in a namespace you control. The fork maintains a connection to the original, allowing you to:

- Make changes independently without affecting the original
- Create merge requests from your fork's branches back to the original project
- Stay in sync with upstream changes via the fork relationship

### How to fork

1. Navigate to the project you want to fork
2. Click the **Fork** button (top-right area of the project page, next to Star)
3. Select the target namespace - your personal namespace or any group where you have at least Developer access
4. Optionally customise the project name, slug and description
5. Choose which branches to fork: **All branches** or **Only the default branch**
6. Click **Fork project**

The fork appears immediately in the selected namespace. GitLab shows a banner on the fork noting which project it was forked from.

### Fork visibility restrictions

Your fork's visibility **cannot exceed** the original project's visibility. If the original project is private, your fork will always be private regardless of what you select. You cannot fork a private project and make the fork public.

### Syncing your fork with upstream

Over time, the original project will receive commits that your fork does not have. To bring your fork up to date:

```bash
# Add the original project as a remote called "upstream"
git remote add upstream git@gitlab.com:ORIGINAL_NAMESPACE/project.git

# Verify both remotes exist
git remote -v
# origin    git@gitlab.com:YOUR_NAMESPACE/project.git (fetch)
# origin    git@gitlab.com:YOUR_NAMESPACE/project.git (push)
# upstream  git@gitlab.com:ORIGINAL_NAMESPACE/project.git (fetch)
# upstream  git@gitlab.com:ORIGINAL_NAMESPACE/project.git (push)

# Fetch all upstream changes
git fetch upstream

# Merge upstream's default branch into your fork's default branch
git checkout main
git merge upstream/main

# Push the updated branch to your fork
git push origin main
```

In the GitLab web UI, your fork's project page shows a **Sync fork** button when the original project has commits your fork does not. Click it to sync without touching the terminal.

### Contributing back to the original via fork

The fork-based contribution workflow on GitLab mirrors GitHub's fork and pull request model:

1. Fork the project to your namespace
2. Clone your fork locally
3. Create a feature branch: `git checkout -b fix/typo-in-readme`
4. Make changes, commit and push to your fork
5. In GitLab, navigate to your fork and click **Create merge request** from the banner that appears after a push
6. Set the source branch (your fork's branch) and the target branch (the original project's default branch)
7. Write a description, assign reviewers and submit
8. The original project's maintainers review and merge your contribution

### Preventing forks outside the group

Project owners and group maintainers can restrict forking to within the group hierarchy:
Settings → General → Permissions → **Prevent project forking outside of the current top-level group**.

When enabled, members can only fork a project to a namespace within the same top-level group. No forks to personal namespaces or other groups. Useful for proprietary code where you want to prevent accidental public disclosure via a fork.

---

## Project Templates

Templates let you create new projects from a predefined starting point - with code structure, CI/CD configuration, issue templates and merge request templates already in place.

### GitLab built-in templates

GitLab maintains a curated set of templates. Available when creating a project from template → **Built-in** tab.

**Pages templates** (each includes a working `.gitlab-ci.yml`):

- Jekyll, Hugo, Gatsby, MkDocs (Material), Hexo, Pelican, Middleman, Sphinx, Eleventy, plain HTML

**Framework templates**:

- Ruby on Rails (with RSpec and Postgres), Spring Boot (Java), Node.js Express, Django (Python + PostgreSQL), Android, iOS (Swift + XCTest), Flutter, NestJS, Spring (Kotlin)

### Custom group templates

Any project in a group can serve as a template for that group. New projects created in the group can start from this template.

**Setting up a custom group template:**

1. Create a "template project" in your group with the desired structure: directory layout, `.gitlab-ci.yml`, `.gitlab/issue_templates/`, `.gitlab/merge_request_templates/`, README, CONTRIBUTING guide, linting config, Dockerfile - whatever your team's standard starting point is
2. Navigate to Group Settings → General → Custom project templates
3. Under **Select a template source**, choose the project you created
4. Save

**Using a group template:**

Create new project → **Create from template** → click the **Group** tab → your template appears in the list → select it and fill in the project details.

**What gets copied from a template:**

- All repository content (files, directory structure)
- CI/CD configuration (`.gitlab-ci.yml` and any included files)
- Issue and merge request description templates (`.gitlab/issue_templates/`, `.gitlab/merge_request_templates/`)
- Branch protection rules (configurable)

**What does NOT get copied:**

- CI/CD variables (sensitive, must be set per project or at group level)
- Pipeline history
- Issues, merge requests and other project data from the template itself
- Webhooks and integrations (configured separately per project)

---

## The Web IDE

The Web IDE is a browser-based code editor built into GitLab - a full VS Code-like experience without installing anything locally.

### Opening the Web IDE

From any file or the repository root, click the **Edit** dropdown → **Web IDE**.

Keyboard shortcut on GitLab.com: press the `.` (period) key on any repository page to open the Web IDE immediately, identical to GitHub's `github.dev` shortcut.

Alternatively: from the project page, click the blue **Edit** button on a file.

### What the Web IDE can do

**Multi-file editing**: open and edit multiple files simultaneously in a tabbed interface. Navigate the full file tree in the left sidebar. Changes across multiple files are tracked together and committed in one operation.

**Git diff view**: before committing, switch to the Source Control panel (left sidebar, same icon as VS Code) to see a diff of every change you have made. Review exactly what will go into the commit.

**Commit and branch**: commit your changes directly from the Web IDE to a new branch (recommended) or to an existing branch. If you commit to a new branch, GitLab immediately offers to open a merge request for review.

**Extensions (limited)**: the Web IDE supports a subset of VS Code extensions. Language services (syntax highlighting, IntelliSense for many languages) work. Most UI-heavy or system-level extensions do not.

**GitLab Duo Chat integration**: open the Duo Chat panel inside the Web IDE to ask AI questions about the code you are editing - explain a function, suggest a refactor, generate a test, find potential bugs - without leaving the editor.

**Remote terminal (Premium)**: open a terminal connected to a remote development environment. Run `npm install`, execute test suites, start a local server - all from the browser. This requires a configured remote development environment (GitLab Workspaces).

### Web IDE vs local development

The Web IDE is excellent for:

- Quick edits to documentation, configuration files, README updates
- Reviewing and editing code suggested in a merge request
- Contributing to projects on a machine where you cannot install a full development environment
- Pair programming over screen share without needing local setup

It is not a replacement for local development when:

- You need to run the application locally to test changes
- Your workflow requires tools not available in the browser (specific compilers, local services, databases)
- You are making large, complex changes across many files that benefit from your local IDE setup

---

## Importing and Migrating Projects

### Importing from GitHub

GitLab's GitHub importer transfers a GitHub repository to a GitLab project, including:

- All repository content (code, full commit history)
- Issues (converted to GitLab issues with comments)
- Pull requests (converted to merge requests with comments, review threads and status)
- Labels (colours are preserved)
- Milestones
- Wiki (if enabled on the GitHub repository)
- Releases
- Repository topics

**Steps to import from GitHub:**

1. Create new project → **Import project** → **GitHub**
2. Authorise GitLab to access your GitHub account via OAuth (or use a GitHub PAT for finer control)
3. GitLab shows a list of your GitHub repositories
4. Select the ones to import; choose the destination namespace
5. Click **Import** - the import runs in the background

**After the import:**

- Update any CI/CD configuration that references GitHub-specific features (Actions syntax, GitHub-specific secrets)
- Update webhooks and integrations that pointed at the GitHub repository
- Update SSH remotes in local clones: `git remote set-url origin git@gitlab.com:NAMESPACE/project.git`
- Notify collaborators of the new URL

> [!NOTE]
> GitHub's rate limits can slow large imports. A repository with thousands of issues and pull requests may take hours to import fully. The import continues in the background even if you close the browser.

### Importing by URL

For any publicly accessible Git repository, or one accessible with credentials:

Create new project → **Import project** → **Repository by URL** → enter the remote URL → optionally provide a username and password/token for private repositories.

This copies only the Git repository content (all branches and tags). Issues, pull requests, wikis and other platform-specific data are not transferred.

### Exporting and importing between GitLab instances

Projects can be exported as a compressed archive (`.tar.gz`) and imported to another GitLab instance.

**Export**: Settings → General → Advanced → Export project. GitLab generates an archive that includes the repository, issues, merge requests, labels, milestones, wiki, snippets and more.

**Import**: Create new project → Import project → GitLab export → upload the archive.

This is useful for migrating projects from a self-managed instance to GitLab.com, or between self-managed instances.

---

## Archiving, Transferring and Deleting Projects

### Archiving

Archiving makes a project read-only without deleting it. The repository, issues and all data remain accessible and searchable, but no new pushes, commits, issues or merge requests are accepted.

**Archive**: Settings → General → Advanced → Archive project → **Archive project**.

Archived projects show an "Archived" badge on their page. Unarchive via the same path.

Use archiving for: deprecated projects that you want to keep for historical reference but no longer actively maintain.

### Transferring

Transfer moves a project to a different namespace (your personal namespace or a group where you have Owner access).

**Transfer**: Settings → General → Advanced → Transfer project → select the new namespace → confirm by typing the project name.

> [!WARNING]
> Transferring a project changes its URL. All existing links, webhook URLs, SSH remotes, CI/CD integrations and package registry references break immediately. GitLab provides redirects for a time, but these are temporary. Plan transfers carefully and update all references after completing the transfer.

### Deleting

**Delete**: Settings → General → Advanced → Delete project → confirm by typing the project name.

On GitLab.com, deletion is **delayed by 7 days** by default. The project is marked as "pending deletion" and is inaccessible, but it can be restored within that window. After 7 days, deletion is permanent and no recovery is possible.

Owners can restore a pending-deletion project: Admin area (self-managed) or by contacting GitLab support (GitLab.com) within the 7-day window.

> [!CAUTION]
> Permanently deleted projects cannot be recovered. The 7-day delay exists precisely to prevent accidental permanent data loss. Do not force-delete unless you are absolutely certain.

---

## How This Compares to GitHub

| Concept                      | GitHub                             | GitLab                                                |
| ---------------------------- | ---------------------------------- | ----------------------------------------------------- |
| Primary unit                 | Repository                         | Project (repository + much more)                      |
| Namespace types              | Personal, Organisation             | Personal, Group (with unlimited subgroup nesting)     |
| Organisation nesting         | Flat (no sub-orgs)                 | Groups → Subgroups (up to 20 levels)                  |
| Team access management       | Teams within orgs (flat structure) | Roles at group/subgroup level (hierarchical)          |
| Shared CI/CD secrets         | Organisation secrets               | Group-level CI/CD variables (cascade to all projects) |
| Shared runners               | Organisation runners               | Group runners (cascade to all projects and subgroups) |
| Fork restrictions            | No built-in restriction            | Can prevent forks outside the group hierarchy         |
| Required reviews (free tier) | ✓ Free                             | ✗ Premium only                                        |
| Project templates            | Repository templates               | Project templates (group-level custom templates)      |
| Cloud IDE                    | github.dev, Codespaces             | Web IDE, Workspaces (beta)                            |
| Import from GitHub           | N/A (it is GitHub)                 | Full importer: issues, PRs, labels, wiki, releases    |

The most significant structural difference is group nesting. GitHub organisations are flat - there is no equivalent of subgroups. GitHub Teams exist within organisations but they control access, not URL structure. In GitLab, the group hierarchy directly determines the URL namespace, the access inheritance chain and the settings cascade. This makes GitLab considerably more flexible for large organisations with complex team structures.

---

## Try It Yourself

**Exercise 1: Create a group and a project inside it**

1. Click **Create new (+)** → **New group** → **Create group**
2. Choose a name and set visibility to **Public**
3. Inside the group, click **Create new (+)** → **New project/repository** → **Create blank project**
4. Name it `hello-gitlab`, enable **Initialize repository with a README**
5. Notice the project URL: `https://gitlab.com/YOUR_GROUP/hello-gitlab`

**Exercise 2: Edit a file using the Web IDE**

1. In your new project, click any file (e.g. `README.md`)
2. Click **Edit** → **Web IDE**
3. Add a line to the README
4. Click **Source Control** in the left sidebar (the git icon)
5. Write a commit message and click **Commit to main** (or create a new branch)
6. Go back to the project - your change is live

**Exercise 3: Change project visibility**

1. In your project: **Settings → General → Visibility, project features, permissions**
2. Change **Project visibility** to **Private**
3. Open an incognito window and try to visit your project URL
4. You should see a 404 - the project is invisible without authentication
5. Change it back to **Public**

**Exercise 4: Fork a public project**

1. Navigate to `https://gitlab.com/gitlab-org/gitlab-foss` - the GitLab Community Edition
2. Click **Fork**
3. Select your personal namespace as the destination
4. Wait for the fork to complete (may take a minute due to repository size)
5. Explore your fork - notice the "Forked from" banner at the top

---

## Common Mistakes

**Choosing a group URL then wanting to change it.** The URL slug of a group or project is hard to change safely. Every existing link, webhook, SSH remote and package registry reference breaks when you rename. Spend time choosing the right slug at creation - you will rarely want to change it.

**Creating projects in a personal namespace when you need a group.** Personal namespaces have no group-level settings, no shared runners and no shared CI/CD variables. If three people are collaborating on a project, put it in a group - otherwise each person needs the same variables added to each project individually.

**Not understanding the five-user limit scope.** The limit counts all unique users across an entire top-level group namespace - not per project. A group with five projects, each with five members, but only six unique people, is over the limit.

**Making a private fork of a private project and assuming it stays private.** The fork inherits the private visibility, which is correct. But be careful about changing the fork's visibility later - GitLab prevents you from making a fork more public than the original, but always double-check after any visibility change.

**Deleting a project when you meant to archive it.** Archiving keeps data intact and searchable. Deletion destroys data (after 7 days). For any project that might be useful as historical reference, archive rather than delete.

**Not updating remotes and webhooks after a transfer.** After transferring a project to a new namespace, every local clone's `origin` remote still points to the old URL. Every webhook and integration that references the old URL silently fails. Update all of these immediately after a transfer.

**Skipping protected branch setup.** A project with no protected branches allows any Developer to push directly to `main`. Set up branch protection on your default branch as soon as the project is created: Settings → Repository → Protected branches.

---

## Summary

- A GitLab **project** is much more than a Git repository - it bundles code hosting, CI/CD, issue tracking, security scanning, registries, Pages and project management into one object
- A **namespace** is the container for projects. Personal namespaces use your username. Group namespaces use the group's slug and support unlimited subgroup nesting (up to 20 levels)
- **Groups** provide shared CI/CD variables, shared runners, centralised access management and group-level analytics across all their projects
- **Roles**: Guest, Reporter, Developer, Maintainer, Owner - cascade from parent group to all subgroups and projects. You can elevate but never reduce a role at a lower level
- New roles: **Planner** (17.7+ - project management without code access) and **Minimal Access** (18.9+ - visibility only, non-billable)
- **Visibility levels**: Public (anyone), Internal (self-managed only - authenticated users), Private (members only). A project cannot be more visible than its containing group
- **Forking** creates an independent copy in your namespace. The fork's visibility cannot exceed the original's. Sync via `git fetch upstream` or the **Sync fork** UI button
- **Project templates** (built-in or group-level custom) let teams start new projects from a consistent baseline
- The **Web IDE** is a full browser-based VS Code environment - open with the `.` shortcut on any repository page
- Import from GitHub with full transfer of issues, pull requests, labels, milestones and wiki via the built-in GitHub importer
- **Archive** projects to make them read-only without deleting. **Transfer** changes the URL and breaks all existing references. **Delete** is delayed 7 days on GitLab.com - after that it is permanent

---

## Sources and Further Reading

- [GitLab projects documentation](https://docs.gitlab.com/user/project/) - complete project management reference
- [Namespaces](https://docs.gitlab.com/user/namespace/) - personal vs group namespace details
- [Groups](https://docs.gitlab.com/user/group/) - creating and managing groups
- [Subgroups](https://docs.gitlab.com/user/group/subgroups/) - nesting groups and access inheritance
- [Visibility and access controls](https://docs.gitlab.com/user/public_access/) - Public, Internal and Private explained
- [Forking a project](https://docs.gitlab.com/user/project/repository/forking_workflow/) - fork workflow and syncing
- [Web IDE](https://docs.gitlab.com/user/project/web_ide/) - browser-based editor guide
- [Import from GitHub](https://docs.gitlab.com/user/project/import/github/) - full import documentation
- [Protected branches](https://docs.gitlab.com/user/project/protected_branches/) - branch protection rules

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

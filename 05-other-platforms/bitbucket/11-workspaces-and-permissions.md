# Workspaces and Permissions in Bitbucket

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

A workspace is the top-level organisational container in Bitbucket. Everything lives inside a workspace: repositories, projects, members, billing and settings. Understanding how workspaces are structured and how permissions cascade through them is essential for anyone administering Bitbucket for a team - whether that team has five developers or five hundred.

This file covers the complete workspace and permissions model: how workspaces are created and configured, how groups simplify access management, how roles work at workspace and repository level, how permissions cascade from workspace to project to repository and how to structure access control for common team configurations.

---

## Table of Contents

1. [The workspace model](#the-workspace-model)
2. [Creating and configuring a workspace](#creating-and-configuring-a-workspace)
3. [Workspace roles](#workspace-roles)
4. [Groups](#groups)
5. [Projects and project permissions](#projects-and-project-permissions)
6. [Repository permissions](#repository-permissions)
7. [Permission inheritance and override](#permission-inheritance-and-override)
8. [Managing members](#managing-members)
9. [Service accounts and automation users](#service-accounts-and-automation-users)
10. [Workspace settings reference](#workspace-settings-reference)
11. [Access control patterns for common team structures](#access-control-patterns-for-common-team-structures)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## The workspace model

Bitbucket's organisational model has three layers:

```
Workspace
  ├── Members (with workspace roles: Owner, Admin, Member)
  ├── Groups (collections of members)
  ├── Projects (optional repository grouping)
  │     ├── Project permissions (applied to all repos in the project)
  │     └── Repositories
  │           └── Repository permissions (can override project permissions)
  └── Repositories (without a project)
        └── Repository permissions
```

**Workspace** is the root. All billing, member management and workspace-level settings live here. A workspace is identified by its slug (the short name appearing in all repository URLs: `bitbucket.org/workspace-slug/repo-name`).

**Members** are Atlassian account holders who have been added to the workspace. They have a workspace role (Owner, Admin or Member) and may have additional permissions via groups or direct repository/project access.

**Groups** are named collections of workspace members. Granting a group access to a repository or project grants that access to all group members at once. Groups are the right way to manage access at scale.

**Projects** are optional grouping containers for repositories within a workspace. A project permission applies to all repositories within the project. Individual repositories can override project permissions.

**Repositories** are the Git repositories. They inherit permissions from the project they belong to (if any) and can have additional direct permissions for specific users or groups.

---

## Creating and configuring a workspace

### Creating a new workspace

Every Atlassian account gets a personal workspace automatically. Additional workspaces are created for teams and organisations.

1. Click your avatar in the bottom left of any Bitbucket page
2. Click **Create workspace**
3. Enter a workspace name (the display name) and a workspace slug (the URL identifier)
4. Click **Create**

The workspace slug is permanent in practice - changing it breaks all repository URLs. Choose it carefully. For a company named "Acme Corp", a slug like `acme-corp` or `acmecorp` is appropriate.

### Workspace settings

Access workspace settings by clicking the workspace name in the left sidebar, then **Workspace settings** (or the settings gear icon).

Key settings sections:

**Overview** - workspace name, slug, avatar, description. Changing the slug here is possible but breaks all repository URLs.

**Members** - add, remove and manage workspace members and their roles.

**Groups** - create and manage groups of members.

**Plans and billing** - manage the Bitbucket Cloud plan (Free, Standard, Premium), view usage, manage payment.

**Security** - 2FA enforcement, IP allowlisting (Premium), audit log (Premium).

**Integrations** - connect Jira, Slack and other tools at the workspace level.

**Installed apps** - Atlassian Marketplace apps installed for this workspace.

### Workspace avatar and branding

The workspace avatar appears next to repository names and in search results. Use a recognisable logo or icon. Avatars support PNG, JPG and GIF formats up to 2 MB.

---

## Workspace roles

Every member of a workspace has one of three workspace roles. These roles control what the member can do at the workspace level, independent of any repository-specific permissions.

### Owner

The most privileged role. There must be at least one Owner per workspace. Owners can:

- Delete the workspace
- Change billing and plan
- Manage all workspace settings
- Add and remove members with any role including other owners
- Create, delete and transfer repositories
- Access all repositories in the workspace regardless of repository-level permissions
- View the audit log (Premium)

> [!WARNING]
> Workspace Owners have access to all repositories regardless of repository-level permissions. If you grant Owner role to protect against accidental lockout, be aware that this person can read and write all code in the workspace.

### Admin

Admin is the standard administrative role for day-to-day workspace management. Admins can:

- Add and remove members (but cannot add Owners)
- Create groups and manage group membership
- Create and delete repositories
- Change workspace settings (except billing and plan changes)
- Access all repositories in the workspace

Admins cannot delete the workspace, change billing or promote other members to Owner.

### Member

The default role for developers. Members can:

- Access repositories they have been explicitly granted access to (via direct permission, group membership or project permissions)
- Create repositories (by default; this can be restricted)
- View the member list

Members cannot access repositories they have not been granted access to. They cannot change workspace settings, manage other members or create groups.

### Choosing between roles

For a typical software team:

- **Owner**: CTO, VP of Engineering, or designated account administrator
- **Admin**: Engineering managers, team leads, DevOps engineers who manage repository setup
- **Member**: All developers, QA engineers, designers, anyone who needs repository access

---

## Groups

Groups are named collections of members. They are the right tool for managing access to multiple repositories for a set of people. Instead of granting 10 developers access to 20 repositories individually (200 permission entries), create a group, add 10 developers and grant the group access to 20 repositories (30 entries).

### Creating a group

1. Go to **Workspace settings** > **Groups**
2. Click **Create group**
3. Enter a group name (e.g. "Backend Team", "Mobile Team", "DevOps")
4. Add members to the group
5. Click **Create**

### Group slugs

Like workspaces and repositories, groups have slugs used in API calls. The slug is derived from the group name (lowercase, hyphens). A group named "Backend Team" gets the slug `backend-team`.

### Adding members to groups

1. Go to **Workspace settings** > **Groups**
2. Click the group name
3. Click **Add member**
4. Search for workspace members by name or email
5. Click **Add**

Members added to a group inherit all permissions that group has been granted on repositories and projects. Removing a member from a group removes all permissions granted via that group (though they retain any directly granted permissions).

### Granting group access to repositories

Groups are granted access at the repository level or project level:

**Repository level:**

1. Open the repository
2. Go to **Repository settings** > **User and group access**
3. Under **Group access**, click **Add a group**
4. Select the group and choose the permission level (Read, Write, Admin)

**Project level:**

1. Open the project
2. Go to **Project settings** > **User and group access**
3. Add the group with the appropriate permission level
4. All repositories in the project inherit this permission

### Built-in groups

Bitbucket automatically creates two groups in every workspace:

**Everyone** - contains all current and future workspace members. Granting "Everyone" access to a repository means any workspace member can access it. Use with care on sensitive repositories.

**Administrators** - contains all workspace Admins and Owners. Granting this group write access to all repositories is a common pattern for giving administrators a consistent access path.

---

## Projects and project permissions

Projects are an optional grouping layer between the workspace and individual repositories. They are most useful when a workspace contains many repositories that logically belong to different teams or product areas.

### Project roles

When you grant a user or group access to a project, every repository in that project inherits that permission. The permission levels mirror repository permissions:

| Project permission | Equivalent to    |
| ------------------ | ---------------- |
| Read               | Repository Read  |
| Write              | Repository Write |
| Admin              | Repository Admin |

A group granted **Write** on a project can push to, pull from and create pull requests on every repository in that project, without needing individual repository permissions.

### Project-level settings

Projects have their own settings accessible from the project page > **Project settings**:

- **User and group access** - manage who has access to the project and at what level
- **Repository settings** - default settings applied to new repositories created in the project
- **Avatar** - project branding

### When to use projects

**Use projects when:**

- Your workspace has 20+ repositories
- Multiple teams work in the same workspace and need separate access scopes
- You want to grant a team access to all their repositories at once
- You want consistent default settings across a group of related repositories

**Skip projects when:**

- Your workspace has fewer than 10 repositories
- All repositories are accessed by the same team
- The overhead of managing an extra layer is not justified

---

## Repository permissions

Repository permissions are the most granular level of access control. They can be set directly on a repository and can override or extend project and workspace-level permissions.

### Repository permission levels

| Level     | Can do                                                                                     |
| --------- | ------------------------------------------------------------------------------------------ |
| **Read**  | View repository, clone, fetch, view pull requests and issues                               |
| **Write** | Everything in Read, plus push commits, create branches, create pull requests               |
| **Admin** | Everything in Write, plus manage repository settings, delete the repository, manage access |

### Granting direct repository access

1. Open the repository
2. Go to **Repository settings** > **User and group access**
3. Under **User access**, click **Add a user**
4. Search for the user and set their permission level
5. Or under **Group access**, add a group

### Access via different paths

A user's effective permission on a repository is the highest permission from any of these sources:

1. **Workspace role** - Owners and Admins have implicit admin access to all repositories
2. **Project permission** - if the repository is in a project and the user has a project permission
3. **Group access** - if the user is in a group that has repository or project access
4. **Direct user access** - a permission granted directly to the user on this repository

If a user has Read via a group and Write via direct access, they have Write. The most permissive access wins.

### Removing access

To remove a user's access to a specific repository:

1. Go to **Repository settings** > **User and group access**
2. Find the user or group
3. Click **Remove** or change the permission level

Note: If the user's access comes from a group or project permission rather than direct access, removing them from the direct access list does not remove their group-based access. You must remove them from the group or remove the group's project/repository permission.

---

## Permission inheritance and override

Understanding how permissions cascade is essential for avoiding unexpected access situations.

### Cascade order

```
Workspace role (Owner/Admin) → full access to all repos
    ↓
Project permission → applies to all repos in the project
    ↓
Repository permission → applies to this specific repo only
```

Higher-level permissions are not overridden by lower-level restrictions. If a user is a workspace Admin, they have Admin access to all repositories regardless of repository-level settings. Repository-level permissions can grant additional access (e.g. granting Write to a Member on a repository they would otherwise have no access to) but cannot restrict workspace-level access.

### Example: restricting a repo within a project

**Scenario:** A workspace has a "Backend Team" group with Write access to the "Backend" project. One repository in the project contains highly sensitive cryptographic code and should only be accessible to two senior developers.

**Problem:** Because the Backend Team group has project-level Write, all backend team members can access the sensitive repository.

**Solution options:**

1. Move the sensitive repository out of the "Backend" project to no project, and grant only the two seniors direct access. Remove the repo from the project so it no longer inherits project permissions.
2. Create a separate project for the sensitive repository with its own access controls.

There is no way to "deny" access to a subset of a project's members for a specific repository within that project. The only option is to remove the repository from the project's scope.

### Fork permissions

When a repository is forked, the fork is a separate repository with its own permissions. Access to the original repository does not grant access to the fork, and vice versa. The fork owner (or the workspace the fork lives in) controls access to the fork independently.

---

## Managing members

### Inviting a new member

1. Go to **Workspace settings** > **Members**
2. Click **Invite member**
3. Enter their email address
4. Select their workspace role (Owner, Admin, or Member)
5. Optionally add them to groups immediately
6. Click **Send invite**

The invitee receives an email. If they already have an Atlassian account, they can accept immediately. If not, they are prompted to create one first.

### Pending invitations

Invited users who have not yet accepted appear in a "Pending" section of the members list. You can resend or cancel invitations. A pending invite does not consume a billing seat on paid plans - the seat is consumed when the invite is accepted.

### Removing a member

1. Go to **Workspace settings** > **Members**
2. Find the member
3. Click the **...** menu > **Remove from workspace**

Removing a member:

- Revokes all their access to workspace repositories immediately
- Removes them from all groups
- Does not delete any code they committed (Git history is preserved)
- Does not delete any pull request comments they made
- Does not affect their Atlassian account itself - only their membership in this workspace

### Changing a member's role

1. Go to **Workspace settings** > **Members**
2. Find the member
3. Click the role dropdown next to their name
4. Select the new role

### Member limits on the free plan

The free plan limits workspaces to **5 members**. This is a hard limit. Attempting to invite a sixth member prompts you to upgrade to Standard or Premium. The 5-member count includes the workspace owner.

---

## Service accounts and automation users

Automated systems (CI/CD pipelines, deployment scripts, bots) that need to access Bitbucket repositories should use dedicated service accounts rather than a real developer's account.

### Why service accounts matter

If CI/CD uses a developer's personal API token:

- When the developer leaves and their account is deactivated, the CI/CD breaks
- The developer's token has access to everything they can access personally, not just what CI/CD needs
- There is no clear audit trail separating the developer's actions from the automation's actions

A dedicated service account solves all three problems.

### Creating a service account

1. Create a new Atlassian account with a shared team email (e.g. `bitbucket-ci@yourcompany.com`)
2. Add this account to the workspace as a Member
3. Grant the account access only to the repositories it needs, with only the permissions it needs (typically Write for pipelines that push, Read for pipelines that only clone)
4. Create an API token under this account for each integration
5. Store the API tokens in your CI/CD system's secret management

### Repository access keys as an alternative

For read-only CI/CD access (cloning for builds), repository **access keys** are a better option than service accounts. An access key is an SSH key tied to a specific repository, not to a user account. It does not consume a workspace seat and has explicit repository scope.

See [09-security-features.md](09-security-features.md) for access key setup.

---

## Workspace settings reference

A complete reference of workspace settings and what they control:

### Overview settings

| Setting        | What it controls                          |
| -------------- | ----------------------------------------- |
| Workspace name | The display name shown in the UI          |
| Workspace slug | The URL identifier (bitbucket.org/slug)   |
| Avatar         | The workspace logo or icon                |
| Description    | A short text description of the workspace |

### Member settings

| Setting         | What it controls                                            |
| --------------- | ----------------------------------------------------------- |
| Member list     | All current members and their roles                         |
| Pending invites | Invitations awaiting acceptance                             |
| Default role    | Role assigned to new members (always Member; cannot change) |

### Group settings

| Setting       | What it controls                           |
| ------------- | ------------------------------------------ |
| Group list    | All groups in the workspace                |
| Group members | Members in each group                      |
| Group access  | Which repos/projects each group can access |

### Plan and billing

| Setting        | What it controls                               |
| -------------- | ---------------------------------------------- |
| Current plan   | Free, Standard or Premium                      |
| Billing cycle  | Monthly or annual (annual is discounted)       |
| Payment method | Credit card or invoice                         |
| Usage          | Current month's pipeline minutes, storage used |
| User count     | Number of active workspace members             |

### Security settings

| Setting                           | Plan required | What it controls                      |
| --------------------------------- | ------------- | ------------------------------------- |
| Two-step verification enforcement | Free          | Require all members to have 2FA       |
| IP allowlisting                   | Premium       | Restrict access to specific IP ranges |
| Audit log                         | Premium       | Full event history for compliance     |

### Integration settings

| Setting            | What it controls                                         |
| ------------------ | -------------------------------------------------------- |
| Jira Software      | Connect to a Jira site for development panel integration |
| Slack              | Connect Slack for notifications                          |
| Identity providers | SAML SSO configuration (via Guard)                       |

---

## Access control patterns for common team structures

### Small startup (5-15 developers, single team)

All developers need access to all repositories. Simple flat structure.

```
Workspace members: all developers as Members
Groups: none needed
Permissions: grant "Everyone" group Write access to all repos
            (or add all to a "Developers" group with Write)
Branch permissions on main: require 1 approval, builds must pass
```

### Mid-size company (50-200 developers, multiple teams)

Teams have responsibility for their own services but may need read access across the organisation.

```
Groups:
  - backend-team (Write on backend repos)
  - frontend-team (Write on frontend repos)
  - mobile-team (Write on mobile repos)
  - platform-team (Admin on infrastructure repos, Read on all others)
  - everyone (Read on all repos)

Projects:
  - Backend (backend-team: Write)
  - Frontend (frontend-team: Write)
  - Mobile (mobile-team: Write)
  - Infrastructure (platform-team: Admin)
```

### Enterprise with sensitive code (500+ developers)

Some repositories require restricted access; most are open to the relevant teams.

```
Workspace roles:
  - Owners: 2-3 senior engineering leaders
  - Admins: Engineering managers, DevOps leads
  - Members: All engineers

Projects with access controls:
  - Core Platform (platform-engineers: Write, all-engineers: Read)
  - Security Tools (security-team: Admin, others: no access - not in project)
  - Customer Data (data-team: Write, compliance: Read, others: no access)
  - Internal Tools (all-engineers: Write)

Special handling for sensitive repos:
  - Not in any project
  - Direct access only for named individuals
  - Audit log monitored for access events
```

### Open source project

Public repositories with external contributors.

```
Repository: Public (no authentication required to read/clone)
Fork policy: Allow forks
Branch permissions on main: require 2 approvals, builds must pass

Internal contributors: Added as workspace Members with Write
External contributors: Fork → PR workflow (no workspace membership needed)
Maintainers: Repository Admin role
```

---

## Try It Yourself

**Exercise 1 - Create a group and manage access**

1. Go to **Workspace settings** > **Groups**
2. Create a group called `developers`
3. Add yourself to the group
4. Create a test repository
5. Go to the repository's **User and group access** settings
6. Grant the `developers` group Write access
7. Verify the access appears in the group's access list

**Exercise 2 - Create a project and move a repository**

1. Go to your workspace and click **Projects**
2. Create a project called `Test Project` with key `TP`
3. Go to an existing repository's settings
4. Under **Repository details**, change the Project field to `Test Project`
5. Navigate to the project and confirm the repository appears

**Exercise 3 - Test permission inheritance**

1. Create two repositories: `repo-a` and `repo-b`
2. Create a project and add both repositories to it
3. Grant a group Read access to the project
4. Check that both repositories show the group in their access list (inherited from project)
5. Give `repo-a` direct Write access for the same group
6. Verify `repo-a` shows Write (overrides project's Read) while `repo-b` still shows Read

**Exercise 4 - Invite a collaborator**

1. Go to **Workspace settings** > **Members**
2. Click **Invite member**
3. Enter a colleague's email
4. Select the Member role
5. Note the pending invitation in the members list

---

## Common Mistakes

**Using a developer's personal account for CI/CD**

When the developer leaves and their Atlassian account is deactivated or removed from the workspace, every CI/CD pipeline using their token breaks simultaneously. Use a dedicated service account or repository access keys for automation.

**Granting workspace Admin role too liberally**

Workspace Admins have implicit access to all repositories in the workspace. Giving Admin to a developer who just needs access to a few repositories is more access than they need. Use the Member role and grant specific repository or project access instead.

**Not using groups for team access**

Granting access to 20 repositories individually for 15 developers creates 300 permission entries. When someone joins the team, you manually grant 20 accesses. When someone leaves, you manually remove 20. Groups reduce this to: add to group (join) or remove from group (leave).

**Forgetting that removing a user from a repo does not remove group access**

If a user has access via a group, removing their direct repository access entry does not affect their group-based access. To remove all access, remove them from the workspace or remove them from the relevant groups.

**Relying on "Everyone" for sensitive repositories**

The built-in "Everyone" group includes all current and all future workspace members. Granting "Everyone" access to a repository containing sensitive code means every new team member automatically gets access to it. Use specific groups for sensitive repositories.

**Not removing departed members immediately**

When someone leaves the organisation, their workspace membership (and all associated access) should be removed immediately. On free plans, removing a member frees up one of the 5 slots. On paid plans, it stops billing for that seat at the next billing cycle. On the security side, a former employee retaining access is a serious risk.

---

## Summary

Workspaces are the top-level containers in Bitbucket. Every repository, member, group and project belongs to a workspace. Workspaces have three roles: Owner (full access including billing), Admin (management without billing), and Member (access only to granted resources).

Groups collect members and are the primary tool for scaling access management. Instead of granting repository access to individuals, grant it to groups. Adding someone to a group gives them all the access the group has; removing them takes it away.

Projects provide an optional grouping layer between workspace and repositories. Project permissions cascade to all repositories in the project. Repositories can have direct permissions that extend (but not restrict) project-level access.

Effective permission is the highest level from any source: workspace role, project permission, group access or direct user access. There is no deny mechanism - you can only grant access, not explicitly block it below the workspace role level.

Service accounts (or repository access keys for read-only automation) should be used for CI/CD and automated processes instead of individual developer accounts.

---

## Sources

- [Atlassian: Workspace overview](https://support.atlassian.com/bitbucket-cloud/docs/what-is-a-workspace/)
- [Atlassian: Manage workspace members](https://support.atlassian.com/bitbucket-cloud/docs/manage-workspace-members/)
- [Atlassian: Groups in Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/organize-members-into-groups/)
- [Atlassian: Repository permissions](https://support.atlassian.com/bitbucket-cloud/docs/set-repository-access-permissions/)
- [Atlassian: Projects in Bitbucket](https://support.atlassian.com/bitbucket-cloud/docs/what-are-workspace-projects/)
- [Atlassian: Repository access keys](https://support.atlassian.com/bitbucket-cloud/docs/set-up-access-keys/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

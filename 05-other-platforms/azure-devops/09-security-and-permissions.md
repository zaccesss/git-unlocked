# Security and Permissions in Azure DevOps

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Azure DevOps has a layered permission model with four levels: organisation, project, team and repository. Permissions cascade downward - access granted at the organisation level flows to all projects within it. Access can be granted through security groups (the primary mechanism) or directly to individual users. Understanding this model is essential for anyone administering Azure DevOps for a team or organisation.

This file covers the complete permission model: the default security groups, how permissions cascade and override, repository-level security, service account management, Azure Active Directory integration and the audit log for tracking security events.

---

## Table of Contents

1. [The permission model](#the-permission-model)
2. [Organisation-level security](#organisation-level-security)
3. [Project-level security](#project-level-security)
4. [Team-level access](#team-level-access)
5. [Repository-level security](#repository-level-security)
6. [Security groups](#security-groups)
7. [Permission inheritance and override](#permission-inheritance-and-override)
8. [Azure Active Directory integration](#azure-active-directory-integration)
9. [Conditional access and MFA](#conditional-access-and-mfa)
10. [Service accounts and service principals](#service-accounts-and-service-principals)
11. [Personal access tokens at scale](#personal-access-tokens-at-scale)
12. [The audit log](#the-audit-log)
13. [Pipeline security](#pipeline-security)
14. [Try It Yourself](#try-it-yourself)
15. [Common Mistakes](#common-mistakes)
16. [Summary](#summary)
17. [Sources](#sources)

---

## The permission model

Azure DevOps permissions are **allow/deny** decisions evaluated at multiple levels. The hierarchy is:

```
Organisation
  └── Project
        ├── Teams (cross-cutting, not strictly hierarchical)
        ├── Repositories (within the project)
        ├── Pipelines
        ├── Boards areas and iterations
        └── Artifacts feeds
```

Each level has **security groups** with predefined permissions. Users are added to groups; the group's permissions apply to the user. Direct user permissions can also be set but group-based management scales better.

### The allow/deny evaluation

For any permission, Azure DevOps evaluates:
1. Is the permission explicitly **Denied** for this user (directly or via any group they are in)? → Deny wins
2. Is the permission explicitly **Allowed** (directly or via any group)? → Allow wins
3. Is the permission **Not Set** everywhere? → Effectively denied (no access)

**Deny is absolute.** If a user is in a group where a permission is denied, that deny cannot be overridden by an allow in another group. This is different from some permission systems where the most permissive rule wins.

---

## Organisation-level security

### Organisation administrators

The **Project Collection Administrators** group has full control over the entire Azure DevOps organisation:
- Create and delete projects
- Manage organisation-level security
- Configure billing
- Manage agent pools
- Install and manage extensions

Membership in this group should be extremely limited - typically 2-3 people maximum.

### Organisation-level groups

| Group | Default members | Permissions |
|---|---|---|
| **Project Collection Administrators** | First account owner | Full organisation control |
| **Project Collection Build Service Accounts** | Pipeline service accounts | Run pipelines |
| **Project Collection Build Administrators** | Project collection admins | Manage build resources |
| **Project Collection Service Accounts** | Service identities | Internal service operations |

### Organisation settings

Access organisation-level security from the gear icon (bottom left) > **Organisation settings** > **Security** > **Permissions**.

---

## Project-level security

### Default project security groups

Every Azure DevOps project has four built-in security groups:

**Project Administrators**
- Manage project-level settings
- Add and remove project members
- Create and manage teams
- Manage repository settings
- Cannot change organisation settings

**Contributors**
- Clone, push, pull code
- Create branches
- Create and comment on pull requests
- Create and update work items
- Trigger pipeline runs
- The standard role for all developers

**Readers**
- View code, work items, dashboards and pipelines (read-only)
- Cannot push code, create PRs or run pipelines
- Useful for stakeholders, auditors or anyone who needs visibility without participation

**Build Administrators**
- Manage build pipelines
- Manage release pipelines
- Access agent pools
- Administer project build resources

### Managing project members

1. Go to **Project settings** (gear icon, bottom left)
2. Click **Permissions** (under Security)
3. Click a group to manage its members
4. Click **Members** tab > **+ Add** to add users

### Project-level permissions reference

Some permissions worth knowing at the project level:

| Permission | What it controls |
|---|---|
| Create repository | Create new repositories in the project |
| Delete repository | Delete repositories |
| Rename repository | Change repository name |
| Manage permissions | Change security settings |
| Force push (rewrite history) | Allow force pushes |
| Bypass policies when completing PRs | Complete PRs ignoring branch policies |
| Bypass policies when pushing | Push directly to policy-protected branches |

---

## Team-level access

Teams in Azure DevOps are overlapping collections of project members. A project can have multiple teams. Teams share the same repositories and pipelines but have separate boards and backlogs filtered by area path.

### How teams relate to permissions

Teams are **not a security boundary** in Azure DevOps. Team membership does not grant or restrict access to repositories. Access is controlled by security groups. Teams are an organisational concept for work item management (each team has its own boards and backlogs filtered to their area path).

A developer can be on the "Backend Team" and the "Platform Team" simultaneously, with access to repositories determined by their membership in Contributors or other security groups - not by which teams they belong to.

### Team membership management

1. Go to **Project settings** > **Teams**
2. Select a team
3. Click **Members** > **+ Add** to add users

Adding a user to a team does not automatically grant them permissions to do anything. You must also add them to the appropriate security group (typically Contributors).

---

## Repository-level security

Repository permissions allow fine-grained control over what different users can do with a specific repository. They override (or extend) project-level permissions for that repository.

### Accessing repository permissions

1. Go to **Project settings** > **Repositories**
2. Select the repository
3. Click **Security**

### Repository permission reference

| Permission | What it controls |
|---|---|
| **Read** | Clone the repository, fetch, view code |
| **Contribute** | Push commits, create branches, create pull requests |
| **Create branch** | Create new branches (subset of Contribute) |
| **Create tag** | Create tags (subset of Contribute) |
| **Delete or disable repository** | Delete the repository permanently |
| **Edit policies** | Change branch policies |
| **Force push (rewrite history)** | `git push --force` to any branch |
| **Manage notes** | Manage Git notes |
| **Bypass policies when completing pull requests** | Complete PRs despite branch policy failures |
| **Bypass policies when pushing** | Push directly to policy-protected branches |

### Restricting a repository to a subset of users

If a project has sensitive repositories that only specific people should access:

1. Go to repository security
2. For the **Contributors** group (or **Readers** or any other group): set **Read** to **Deny**
3. For the specific users who should have access: set **Read** to **Allow** and **Contribute** to **Allow**

> [!WARNING]
> Deny permissions override all allows. Be very careful when setting Deny on a group. If you deny Read to Contributors and then forget to explicitly grant access to a new developer, they cannot access the repository at all.

### Using explicit allows instead of denies

A safer pattern than using Deny is to not inherit permissions from groups:

1. For the repository, change **Inheritance** to **Off** (at the top of the Security settings)
2. With inheritance off, no permissions flow down from the project level
3. Explicitly grant access to specific groups or users

This is cleaner than setting denies because there is no risk of deny cascades affecting unexpected users.

---

## Security groups

Security groups are the primary mechanism for managing permissions at scale. Instead of managing 50 developers' permissions individually, add them to a group and manage the group.

### Creating a custom security group

1. Go to **Project settings** > **Permissions**
2. Click **New group**
3. Enter a name and description
4. Click **Create group**
5. Add permissions and members to the group

### Common custom group patterns

**Backend Team - Full Access**
- Permissions: Contribute on backend repos, Create branch, Create tag
- Members: all backend developers

**Security Team - Read All**
- Permissions: Read on all repositories
- Members: security engineers who need visibility for audits

**Release Managers**
- Permissions: Bypass policies when completing PRs (on release branches only)
- Members: designated release managers

**External Contractors**
- Permissions: Read on specific repositories, Contribute on specific branches
- Members: contractor accounts
- Time-limited: set a reminder to remove when contract ends

### Group nesting

Azure DevOps security groups can be nested: you can add a group as a member of another group. All permissions of the parent group apply to members of the nested group.

Example:
```
Project Administrators
  └── (contains) Leads Group
        └── (contains) alice, bob

Contributors
  └── (contains) Backend Team
        └── (contains) charlie, dave, eve
```

Alice and Bob are Leads, which are Project Administrators. Charlie, Dave and Eve are in Backend Team, which is a Contributor. All permissions cascade.

---

## Permission inheritance and override

### How inheritance works

Permissions at the project level flow down to repositories, pipelines and boards by default. When you add a user to the Contributors group, they get Contributor permissions on all repositories in the project automatically.

### Turning off inheritance

For specific resources (repositories, pipelines), you can disable inheritance:

1. Go to the resource's Security settings
2. At the top, find the **Inheritance** toggle
3. Set to **Off**

With inheritance off, only explicitly granted permissions apply. No project-level permissions flow to this resource.

### Explicit grants override inheritance

Even with inheritance on, explicit permissions on a resource override inherited permissions. This is used to elevate access (allow a specific user to force push even though the project-level permission is denied) or restrict access (deny read to a specific user on a specific repository even though they have project-level read).

---

## Azure Active Directory integration

Connecting Azure DevOps to Azure Active Directory (Azure AD / Microsoft Entra ID) provides:

### Benefits

**Single sign-on**: Users sign in once with their corporate credentials. No separate Azure DevOps password to manage.

**Automatic deprovisioning**: When an employee is disabled in Azure AD (on their last day), their Azure DevOps access is revoked automatically. No manual cleanup required.

**Group synchronisation**: Azure AD groups can be added to Azure DevOps security groups. When someone is added to an Azure AD group (when they join a team), they automatically get the corresponding Azure DevOps permissions.

**Multi-factor authentication enforcement**: MFA policies in Azure AD apply to Azure DevOps sign-ins.

**Conditional access**: Azure AD conditional access policies can require specific conditions for Azure DevOps access (e.g. "only from corporate network or managed device").

### Connecting to Azure AD

This is done at the organisation level. See [02-creating-an-account.md](02-creating-an-account.md) for the connection process.

### Using Azure AD groups in Azure DevOps

Once connected to Azure AD:
1. Go to **Project settings** > **Permissions**
2. When adding members to a security group, search for Azure AD groups by name
3. Add the Azure AD group
4. All members of the Azure AD group automatically get the permissions

When membership in the Azure AD group changes (new hire added, departed employee removed), the Azure DevOps permissions update automatically.

---

## Conditional access and MFA

### MFA for Azure DevOps

Multi-factor authentication for Azure DevOps is controlled at the Microsoft account or Azure AD level:

- **Personal Microsoft accounts**: enable MFA at [account.microsoft.com/security](https://account.microsoft.com/security)
- **Azure AD accounts**: MFA is managed by Azure AD administrators via Conditional Access policies

When MFA is required, users must complete the second factor when signing in to Azure DevOps. PAT creation also triggers MFA challenges.

### Conditional access policies

Azure AD Conditional Access can enforce requirements for Azure DevOps access:

- **Require MFA**: always require a second factor for Azure DevOps
- **Compliant device**: only allow access from Intune-managed, compliant devices
- **Network location**: only allow access from the corporate network or VPN
- **Sign-in risk**: block high-risk sign-in attempts automatically

Configure Conditional Access in the Azure AD admin portal. The policies apply automatically to all Azure AD-backed Azure DevOps sign-ins.

### Impact on PATs

When Conditional Access policies require managed devices or network location, PAT-based API access (which does not go through the browser sign-in flow) may be exempt depending on how the policy is configured. Review your CA policies to ensure they cover both interactive sign-ins and PAT-based access.

---

## Service accounts and service principals

Automated processes (pipelines, third-party tools, scripts) that need to access Azure DevOps should use dedicated identities rather than human accounts.

### Pipeline service identity

Every Azure DevOps project has a build service account: `{Project Name} Build Service ({Organisation})`. This account is used automatically by all pipelines in the project. It is added to relevant security groups during project creation.

When a pipeline needs to access a repository, Artifacts feed or other resource, it authenticates as this service identity. You can grant or restrict the service identity's permissions like any other user.

### Service principals (Azure AD app registrations)

For external applications and scripts that need to access Azure DevOps programmatically, create an Azure AD app registration (service principal) instead of using a human account's PAT:

1. Create an app registration in Azure AD
2. Grant the app access to Azure DevOps (via the Azure DevOps REST API permissions or via organisation settings)
3. Use client credentials (certificate or secret) to authenticate, obtaining an OAuth token
4. Use the token for Azure DevOps API calls

Service principals have no interactive sign-in, support proper credential rotation and can be governed through Azure AD.

### Managed identities

For Azure-hosted workloads (virtual machines, Azure Functions, AKS) that need to access Azure DevOps, **managed identities** provide a credential-free authentication option. The Azure resource has an identity in Azure AD, and you grant that identity access to Azure DevOps without any secrets to manage.

---

## Personal access tokens at scale

At scale, managing PATs becomes a security challenge. Developers create PATs for various integrations and forget about them. Long-lived, high-scope PATs sitting in scripts or CI systems are security risks.

### Organisation-wide PAT policies

Administrators can set policies for all PATs in the organisation:

1. Go to **Organisation settings** > **Policies**
2. Under **Personal access token policies**:
   - **Maximum token lifetime**: cap how long PATs can be valid (e.g. 90 days maximum)
   - **Restrict full-scoped tokens**: prevent users from creating PATs with "Full access" scope
   - **Restrict creation**: specific Azure AD groups can be exempted from PAT creation entirely

### Auditing PAT usage

The organisation audit log records PAT creation, use and deletion. You can identify:
- Unused PATs (created but never used)
- PATs approaching expiry
- PATs with unexpectedly broad scope

Review the audit log regularly and require developers to justify long-lived or broad-scoped tokens.

---

## The audit log

The Azure DevOps audit log records security-relevant events across the organisation. It is a critical tool for compliance, incident response and security monitoring.

### Accessing the audit log

1. Go to **Organisation settings** (gear icon, bottom left)
2. Click **Audit log** under Security

### What is logged

The audit log records:
- User sign-ins and sign-outs
- PAT creation, modification and deletion
- Permission changes (who granted what to whom)
- Group membership changes
- Repository creation and deletion
- Branch policy changes
- Project creation and deletion
- Agent pool changes
- Extension installation and removal
- Policy bypass events (completing PRs despite failed policies)
- Pipeline run actions (create, queue, cancel)

### Searching and filtering

The audit log supports filtering by:
- Date range
- Event area (Git, Security, Pipelines, etc.)
- Actor (which user performed the action)
- Operation type

### Exporting the audit log

Export audit log data for external analysis or SIEM (Security Information and Event Management) integration:

1. In the audit log, set your filters
2. Click **Download** to export as CSV
3. Or configure a **Stream** to continuously forward events to an external service (Azure Event Hub, Splunk, etc.)

### Streaming audit events

For real-time security monitoring:
1. Go to **Organisation settings** > **Audit log** > **Streams**
2. Click **New stream**
3. Choose the destination:
   - **Azure Monitor Log Analytics** - for Azure-native SIEM
   - **Azure Event Hubs** - for custom processing pipelines
   - **Splunk** - direct Splunk integration
   - **AzureDevOps.Audit** REST endpoint - generic webhook

---

## Pipeline security

Pipelines have their own security model within Azure DevOps.

### Protected resources

Mark the following as **protected resources** to require approval before any pipeline can use them:

- **Environments**: require approval before deploying
- **Service connections**: require approval before a pipeline can authenticate with an external service
- **Variable groups**: require approval before a pipeline can access secrets
- **Agent pools**: require approval before a pipeline can run on specific agent pools
- **Repositories**: require approval before a pipeline can check out another repository

Configure protection from the resource's settings page > **Approvals and checks**.

### Pipeline permissions

Control which pipelines can use specific resources:

1. For a service connection: **Project settings** > **Service connections** > select > **Security** > restrict to specific pipelines
2. For a variable group: **Library** > select group > **Pipeline permissions** > limit to specific pipelines
3. For an environment: **Environments** > select > **Security**

### YAML pipeline validation

Azure Pipelines requires that YAML pipeline changes in certain sensitive situations go through review:

1. A fork submitting a PR cannot access the organisation's secrets (protected variables, service connections) - even if the pipeline uses them in its YAML
2. Pipelines that access protected resources require the protected resource administrator to approve new pipelines before first use

This prevents a PR from an external contributor from running arbitrary code with access to your production service connections.

---

## Try It Yourself

**Exercise 1 - Explore default security groups**

1. Go to **Project settings** > **Permissions**
2. Click through each default group: **Readers**, **Contributors**, **Project Administrators**
3. Click the **Permissions** tab for each group to see what is allowed
4. Click the **Members** tab to see who is in each group

**Exercise 2 - Create a custom security group**

1. Go to **Project settings** > **Permissions**
2. Click **New group**
3. Name it "Senior Developers"
4. Click **Create group**
5. Under **Members**, add yourself
6. Under **Permissions**, grant **Bypass policies when completing pull requests**: Allow

**Exercise 3 - Set repository-specific permissions**

1. Go to **Project settings** > **Repositories** > select your repository
2. Click **Security**
3. Find the **Contributors** group
4. Look at the current permissions
5. Change **Force push (rewrite history)** to **Deny** for Contributors
6. Check that you (as a project administrator) can still force push

**Exercise 4 - Review the audit log**

1. Go to **Organisation settings** > **Audit log**
2. Filter by the last 7 days
3. Find your recent actions (permission changes, logins, PAT operations)
4. Try filtering by a specific area (e.g. "Git")

---

## Common Mistakes

**Adding individual users to many security groups instead of managing through custom groups**

When users are added directly to multiple groups with different permissions, it becomes hard to audit what a person can do and impossible to update consistently. Create meaningful custom groups ("Backend Leads", "Release Managers", "External Contractors") and manage membership in those groups.

**Using Deny permissions unnecessarily**

Deny permissions cascade and override all allows. If you deny Read to Contributors on a repository, then add a new developer to Contributors, they also get denied - which you may not have intended. Use deny sparingly. Prefer removing from groups or using inheritance off.

**Not auditing PAT usage**

Long-lived PATs created for "temporary" integrations get forgotten. Set a maximum token lifetime in organisation policies and review the audit log for PATs that have not been used recently.

**Giving Project Administrators to everyone who manages a team**

Project Administrators can do things like delete repositories and bypass branch policies. For team leads who need to manage work items and team membership but not touch repository security, the Contributors group plus Boards admin rights is sufficient. Don't over-grant.

**Not setting up Azure AD groups for team management**

Without Azure AD group sync, every new hire requires manual Azure DevOps permission grants and every departure requires manual revocation. Setting up Azure AD group sync eliminates this maintenance burden and ensures access is always in sync with HR systems.

**Forgetting service account permissions after policy changes**

When you add a new security restriction (e.g. requiring all pipelines to use specific service connections), the pipeline's build service account may suddenly lack permissions. Check the build service account's permissions when you change repository or pipeline security settings.

---

## Summary

Azure DevOps permissions use an allow/deny model with four layers: organisation, project, team and repository. Deny always wins over allow. Permissions are managed through security groups rather than individual user grants.

Default project groups are: Project Administrators (full project control), Contributors (developers, standard access), Readers (view only) and Build Administrators (pipeline management). Custom groups can be created for any specific permission combination.

Repository-level security can restrict or extend project permissions for a specific repository. Disabling inheritance provides the cleanest access control for sensitive repositories.

Azure Active Directory integration provides SSO, automatic deprovisioning and group synchronisation - essential for enterprise organisations. Conditional Access policies can enforce MFA and device compliance for Azure DevOps sign-ins.

Pipeline security protects service connections, variable groups and environments as protected resources requiring approval before pipelines can use them.

The audit log records all security-relevant events and can be streamed to SIEM systems for continuous monitoring.

---

## Sources

- [Microsoft Learn: Security and permissions overview](https://learn.microsoft.com/azure/devops/organizations/security/about-permissions)
- [Microsoft Learn: Default permissions quick reference](https://learn.microsoft.com/azure/devops/organizations/security/permissions-access)
- [Microsoft Learn: Security groups](https://learn.microsoft.com/azure/devops/organizations/security/add-users-team-project)
- [Microsoft Learn: Repository permissions](https://learn.microsoft.com/azure/devops/repos/git/set-git-repository-permissions)
- [Microsoft Learn: Connect to Azure AD](https://learn.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad)
- [Microsoft Learn: Audit log](https://learn.microsoft.com/azure/devops/organizations/audit/azure-devops-auditing)
- [Microsoft Learn: Pipeline security](https://learn.microsoft.com/azure/devops/pipelines/security/overview)
- [Microsoft Learn: PAT policies](https://learn.microsoft.com/azure/devops/organizations/accounts/manage-pats-with-policies-for-administrators)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

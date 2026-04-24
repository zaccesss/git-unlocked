# Groups and Permissions

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

GitLab's permission system is built around groups, roles and inheritance. Get it right and your team has exactly the access they need with no friction. Get it wrong and you end up with developers unable to push code, managers unable to read reports, or contractors with more access than they should have. Understanding how roles cascade through the group hierarchy, how access tokens scope to groups and projects and how SAML SSO locks down authentication is foundational for anyone administering a GitLab team or organisation.

This file covers the full picture: creating and managing groups, every role and what it actually allows, the subgroup hierarchy and how access inherits through it, group and project access tokens, deploy tokens, SAML SSO and SCIM provisioning, group-level CI/CD variables and how they cascade, and how to structure a real organisation in GitLab's namespace hierarchy.

---

## Table of Contents

- [Groups overview](#groups-overview)
- [Creating and managing groups](#creating-and-managing-groups)
- [Roles and what they allow](#roles-and-what-they-allow)
- [Role inheritance through subgroups](#role-inheritance-through-subgroups)
- [Adding and removing members](#adding-and-removing-members)
- [Membership expiry](#membership-expiry)
- [Group access tokens](#group-access-tokens)
- [Project access tokens](#project-access-tokens)
- [Deploy tokens](#deploy-tokens)
- [Deploy keys](#deploy-keys)
- [SAML SSO](#saml-sso)
- [SCIM provisioning](#scim-provisioning)
- [Group-level CI/CD variables](#group-level-cicd-variables)
- [Group-level runners](#group-level-runners)
- [Visibility levels and their implications](#visibility-levels-and-their-implications)
- [Audit events](#audit-events)
- [Structuring a real organisation](#structuring-a-real-organisation)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Groups Overview

A group in GitLab is a named namespace that contains projects and members. Groups serve three purposes simultaneously:

**URL namespace**: every project in a group inherits the group's URL path. The group `acme-corp` with a project `payments-service` gives that project the URL `gitlab.com/acme-corp/payments-service`. Adding a subgroup `backend` gives `gitlab.com/acme-corp/backend/payments-service`.

**Access container**: members added to a group automatically gain access to every project inside it at their assigned role level. Removing someone from the group removes their access to every project simultaneously.

**Settings container**: CI/CD variables, runner registrations, webhooks and security policies defined at the group level apply to every project in the group and every subgroup below it. Change a deployment token in one place and it propagates to hundreds of projects.

Without groups, each of these would require per-project configuration - adding members to each project, defining variables in each project, registering runners to each project. Groups make management at scale practical.

---

## Creating and Managing Groups

### Creating a group

Click **Create new (+)** in the top navigation bar -> **New group** -> **Create group**.

Fill in:

- **Group name**: the display name shown in the UI. Can contain spaces and special characters. Different from the URL slug.
- **Group URL (slug)**: the URL-safe identifier used in all project URLs within this group. Choose carefully - renaming later breaks every URL that references this group and every project inside it.
- **Visibility level**: Public (anyone can see the group page and its public projects), Internal (self-managed only - any authenticated user on the instance), Private (only members can see anything inside)
- **Who can create projects in this group**: Maintainer and Owner (default), Developers, or No one

After creation, the group page shows an empty projects list. The left sidebar navigation for a group is:

```
Group overview
Plan
  Work items (issues across all projects)
  Boards
  Milestones
  Roadmap (Premium)
  Iterations (Premium)
  Wiki (Premium)
Code
  Merge requests (across all projects)
Build
  Runners
  Package registry
Operate
  Kubernetes clusters
Analyse
  Analytics
Secure
  Security dashboard (Ultimate)
  Vulnerability report (Ultimate)
Manage
  Members
  Labels
Settings
  General
  Integrations
  Access tokens
  CI/CD
  SAML SSO (Premium)
  Webhooks
  Billing
```

### Renaming a group

Settings -> General -> Path, transfer, remove -> **Change group URL**.

Enter the new URL slug and confirm. GitLab warns you that all existing links, remote URLs, webhooks and integrations will break immediately. GitLab provides automatic redirects from the old URL for a limited time, but these are not permanent.

After renaming, every member with a local clone must update their remote:

```bash
git remote set-url origin git@gitlab.com:NEW-GROUP-NAME/project-name.git
```

### Transferring a group

Move an entire group (with all its projects and subgroups) to a different parent group: Settings -> General -> Path, transfer, remove -> **Transfer group**.

This changes the URL of every project and subgroup within the transferred group.

### Deleting a group

Settings -> General -> Path, transfer, remove -> **Remove group**.

Type the group name to confirm. On GitLab.com, deletion is delayed by 7 days (same as project deletion). During this window, the group can be restored by contacting support. After 7 days, deletion is permanent and irreversible.

> [!CAUTION]
> Deleting a group deletes every project, every repository, every issue, every merge request and every CI/CD artifact inside it. This cannot be undone after the 7-day window. Archive the group instead if you want to keep the data accessible but read-only.

---

## Roles and What They Allow

GitLab has six predefined roles. Each role is a superset of the one below it - a Maintainer can do everything a Developer can do, plus more.

### Guest

The minimum role. Intended for external stakeholders who need visibility without contribution rights.

**Can do:**

- View and comment on public and internal issues and merge requests
- Create issues and edit their own issues
- View project statistics
- View releases and environments
- Download project artifacts (if allowed by project settings)

**Cannot do:**

- Clone private repositories
- Push any code
- Create or delete branches
- View confidential issues (unless they created them)
- Access the repository browser for private projects

### Reporter

For team members who need to read code and data but not contribute changes.

**Everything Guest can do, plus:**

- Clone the repository (including private projects)
- View pipelines, job logs and artifacts
- Create new labels
- View and download code
- View repository branches, tags and commits
- View project settings (read-only)

### Developer

The standard role for active contributors. Most of the team working on a project day-to-day is at this level.

**Everything Reporter can do, plus:**

- Push to non-protected branches
- Create, edit and delete branches
- Create and delete tags (if not protected)
- Open merge requests and set assignees and labels
- Run and cancel pipelines
- Create and manage environments
- Push and pull from the container registry
- Create project runners (cannot manage instance runners)
- Create releases

### Maintainer

For team leads, senior developers or anyone responsible for the project's configuration.

**Everything Developer can do, plus:**

- Push to protected branches (if the protection rule allows Maintainers)
- Merge merge requests
- Manage branch protection rules
- Add and remove project members (up to Maintainer level - cannot add Owners)
- Edit project settings
- Enable and disable project features (issues, MRs, Pages, etc.)
- Manage group and project runners
- Manage CI/CD variables
- Enable review apps
- Trigger a pipeline for a protected branch
- Manage integrations and webhooks

### Owner

The highest standard role. Typically limited to team leads, engineering managers or the person responsible for the group.

**Everything Maintainer can do, plus:**

- Delete the group or project
- Change the group or project URL (rename)
- Change group or project visibility
- Transfer the group or project to another namespace
- Manage all members including other Owners
- Configure SAML SSO
- Manage billing and subscriptions
- Add deploy keys and deploy tokens
- View and restore deleted projects

### Planner (GitLab 17.7+)

A role specifically for project managers and product owners who need planning access without code access. Non-billable on GitLab.com - does not count toward paid seat limits.

**Can do:**

- Everything Reporter can do
- Create and manage issues, epics, milestones, iterations
- Create and manage labels
- Manage roadmaps and boards
- Access planning and analytics features

**Cannot do:**

- Clone repositories
- View or run pipelines
- Access the container or package registry

### Minimal Access (GitLab 18.9+)

The lowest possible role, designed for users who need to be a group member for technical reasons (SSO, billing, appearing in @mentions) without actually accessing project content. Non-billable.

**Can do:**

- View the group name in the navigation sidebar
- See that the group exists

**Cannot do:**

- View any projects in the group
- View any issues, MRs, wikis or code
- Almost everything else

Use Minimal Access for: external auditors who need to authenticate via group SSO but should not see code, or for billing compliance where a user must technically be in a group.

### Security Manager

A special non-hierarchical role (not part of the Guest-Owner ladder). Available at group level. Inherits from Reporter and adds security-specific permissions.

**Can do (beyond Reporter):**

- View vulnerability reports and security dashboards
- Dismiss and manage vulnerabilities
- Access security policies
- View security audit logs
- Access compliance features

Use Security Manager for dedicated security team members who need vulnerability visibility across multiple projects without needing Developer or Maintainer access to the codebase.

---

## Role Inheritance Through Subgroups

This is the most important concept to understand when administering a multi-level group hierarchy.

### How inheritance works

Roles granted at a parent group flow downward to every subgroup and every project within that hierarchy. A user added to the top-level group `acme-corp` with the Developer role has Developer access to:

- Every project directly in `acme-corp/`
- Every project in `acme-corp/backend/`
- Every project in `acme-corp/backend/payments/`
- Every project in `acme-corp/frontend/`
- And so on, to any depth

### Elevating access at a lower level

You can grant a higher role at a subgroup or project level without changing the parent group role.

Example:

- Alice is Developer in `acme-corp` (the top-level group)
- She therefore has Developer access to everything
- For the specific project `acme-corp/backend/payments-service`, she is added as Maintainer
- Result: Alice has Maintainer access to `payments-service`, Developer access to everything else

The higher role wins on any specific resource. A user's effective role on a project is the highest role they hold, whether from the project directly, from the immediate parent group or from any ancestor group.

### You cannot downgrade at a lower level

If Alice is Maintainer in `acme-corp`, you cannot add her to a specific subgroup or project as Reporter. The top-level Maintainer role cannot be overridden downward. She will always have at least Maintainer access to everything in that group.

This is by design: if a top-level owner should not have access to a specific project, that project should not be in the same group hierarchy.

### Checking a user's effective access

Manage -> Members shows all members including inherited ones. The "Source" column shows whether membership is direct (added to this group/project specifically) or inherited (from a parent group).

Project -> Settings -> Members -> search for a user -> see their effective role and where it comes from.

---

## Adding and Removing Members

### Adding members to a group

Group -> **Manage -> Members** -> **Invite members**

Fill in:

- **Username or email**: search by GitLab username, or enter an email address to invite someone who does not have a GitLab account yet
- **Role**: the role to assign (Guest, Reporter, Developer, Maintainer, Owner, Planner)
- **Access expiry date**: optional date after which the membership automatically expires (see below)

You can invite multiple users at once by entering multiple usernames or emails separated by commas.

### Adding members by email invitation

If the person does not have a GitLab account yet, entering their email address sends an invitation email. When they create an account and accept the invitation, they are added to the group at the specified role. The invitation expires after a configurable period (default 3 days).

### Removing members

Manage -> Members -> find the member -> click the vertical dots menu -> **Remove member**.

Removing a member from a group removes their access to every project in the group simultaneously, unless they were also directly added to specific projects at a different level.

### Viewing inherited members

Manage -> Members shows two tabs: **Direct** (added to this specific group) and **Indirect** (inherited from parent groups). The Indirect tab shows who has access because of parent group membership, even if they were not directly added here.

---

## Membership Expiry

When adding a member, you can set an expiry date. On that date at midnight UTC, the membership is automatically removed. This is invaluable for:

- **Contractors**: grant access for the duration of a contract, it expires automatically
- **Auditors**: temporary read-only access for a compliance review
- **Open source contributors**: grant temporary elevated access for a specific contribution period
- **Interns**: access that expires at the end of the internship

Set expiry when inviting: **Access expiry date** field in the invite form.

Modify expiry on an existing member: Manage -> Members -> find the member -> click the pencil icon next to their expiry date.

Members approaching expiry receive email notifications (at 7 days and 1 day before). The group Owner also receives notifications.

> [!TIP]
> Use membership expiry as a default for all external users. A contractor with no expiry date set is a forgotten access risk. Setting 30-day expiry and explicitly extending it when needed is better than indefinite access you have to remember to revoke.

---

## Group Access Tokens

Group access tokens are credentials that authenticate as a bot user scoped to a specific group. They are used for automation, CI/CD pipelines, scripts and integrations that need API access across a group without being tied to a personal user account.

**Availability:**

- On GitLab.com: Premium and above only
- On self-managed GitLab: any licence (including CE)

**Advantages over personal access tokens (PATs):**

- Not tied to a specific person's account - the token still works if that person leaves the organisation
- Scoped to the group - cannot accidentally access resources outside the group
- Creates a dedicated bot user that is separate from real user accounts
- Does not consume a billable seat

### Creating a group access token

Group -> Settings -> **Access tokens** -> **Add new token**

Fields:

- **Token name**: descriptive name indicating the purpose (e.g. "CI/CD deployment pipeline", "Terraform automation")
- **Expiry date**: maximum 365 days from creation. Required.
- **Role**: the role the token's bot user has in the group. Choose the minimum needed.
- **Scopes**: what the token can access. Same scopes as personal access tokens.

After creation, copy the token immediately - it is only shown once.

### Managing the bot user

The group access token creates a system-managed "bot" user (visible in Manage -> Members as "Group bot"). This user:

- Does not count toward the billable seat limit
- Cannot sign in to the web interface
- Can only authenticate via the token value
- Has the role you assigned when creating the token

### Use cases

```bash
# Use in CI/CD pipeline (store as a masked CI/CD variable)
curl --header "PRIVATE-TOKEN: $GROUP_ACCESS_TOKEN" \
  "https://gitlab.com/api/v4/groups/my-group/projects"

# Use in a Terraform provider
provider "gitlab" {
  token    = var.gitlab_token  # group access token stored in Vault/env
  base_url = "https://gitlab.com/"
}

# Use to clone repositories in automation
git clone https://oauth2:$GROUP_ACCESS_TOKEN@gitlab.com/my-group/my-project.git
```

---

## Project Access Tokens

Project access tokens work identically to group access tokens but are scoped to a single project. Available on all plans for self-managed GitLab, Premium+ on GitLab.com.

Project -> Settings -> **Access tokens** -> **Add new token**

Use project access tokens when:

- The automation only needs to access one specific project
- You want the narrowest possible scope for security
- The project's CI/CD pipeline needs API access to its own project

```yaml
# In .gitlab-ci.yml, use the built-in CI_JOB_TOKEN for most in-project API calls
# Use a project access token for operations the job token cannot do
release:
  script:
    - |
      curl --header "PRIVATE-TOKEN: $PROJECT_TOKEN" \
        --request POST \
        "${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/releases" \
        --data "tag_name=${CI_COMMIT_TAG}"
```

---

## Deploy Tokens

Deploy tokens are lightweight credentials for read-only (or read-write) access to specific resources without a user account. Unlike access tokens (which have the full API scope capability), deploy tokens have limited, specific scopes.

**Deploy token scopes:**

| Scope                    | What it allows                                 |
| ------------------------ | ---------------------------------------------- |
| `read_repository`        | Clone the repository via HTTP                  |
| `read_registry`          | Pull Docker images from the container registry |
| `write_registry`         | Push Docker images to the container registry   |
| `read_package_registry`  | Download packages from the package registry    |
| `write_package_registry` | Upload packages to the package registry        |

Deploy tokens cannot access the GitLab REST API - they are purely for Git and registry operations.

### Creating a deploy token

**Project level**: Settings -> Repository -> **Deploy tokens** -> **Add new token**

**Group level** (applies to all projects in the group): Group -> Settings -> Repository -> **Deploy tokens** -> **Add new token**

Fields:

- **Name**: descriptive identifier
- **Expiry date**: optional (deploy tokens can be non-expiring)
- **Username**: the username the token authenticates as (auto-generated if left blank)
- **Scopes**: tick the required scopes

### Using a deploy token

```bash
# Clone a repository using a deploy token
git clone https://DEPLOY_TOKEN_USERNAME:DEPLOY_TOKEN_VALUE@gitlab.com/namespace/project.git

# Pull a container image using a deploy token
docker login registry.gitlab.com -u DEPLOY_TOKEN_USERNAME -p DEPLOY_TOKEN_VALUE
docker pull registry.gitlab.com/namespace/project:latest

# In Kubernetes, create an image pull secret using a deploy token
kubectl create secret docker-registry gitlab-registry \
  --docker-server=registry.gitlab.com \
  --docker-username=DEPLOY_TOKEN_USERNAME \
  --docker-password=DEPLOY_TOKEN_VALUE
```

### Deploy tokens vs access tokens

|                    | Deploy token      | Access token            |
| ------------------ | ----------------- | ----------------------- |
| API access         | No                | Yes                     |
| Git clone          | Yes               | Yes                     |
| Registry pull/push | Yes (with scope)  | Yes (with scope)        |
| Expiry             | Optional          | Required (max 365 days) |
| Billable seat      | No                | No                      |
| User association   | None (standalone) | Creates a bot user      |
| Revocation         | Delete the token  | Delete the token        |

Use deploy tokens for production deployment pipelines that only need to pull container images or clone repositories. Use access tokens when you need API access.

---

## Deploy Keys

Deploy keys are SSH public keys that grant read-only (or read-write) access to a repository without a user account. They are an alternative to deploy tokens for Git-over-SSH access.

**Create a deploy key:**

```bash
# Generate a dedicated key pair for the deploy key
ssh-keygen -t ed25519 -C "deploy-key-production" -f ~/.ssh/deploy_key

# The public key goes into GitLab
# The private key stays on the deployment server
```

Project -> Settings -> Repository -> **Deploy keys** -> **Add new deploy key**

Paste the public key content, give it a name and optionally enable write access (disabled by default - read-only is safer for most deployments).

On the deployment server:

```bash
# Configure SSH to use the deploy key for this specific host/repo
# Add to ~/.ssh/config:
Host gitlab.com-deploy
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/deploy_key

# Clone using the configured host alias
git clone git@gitlab.com-deploy:namespace/project.git
```

### Shared deploy keys (instance-wide)

On self-managed GitLab, administrators can create instance-wide deploy keys that any project can enable. Admin Area -> Deploy keys -> New deploy key.

Project owners enable a shared deploy key: Settings -> Repository -> Deploy keys -> **Publicly accessible deploy keys** tab -> enable.

---

## SAML SSO

SAML (Security Assertion Markup Language) SSO lets group members authenticate using your organisation's existing identity provider (IdP) - Okta, Microsoft Entra ID (Azure AD), Google Workspace, OneLogin, Ping Identity and others.

**Availability:**

- GitLab.com: Premium and above, configured per top-level group
- Self-managed GitLab: any licence, configured at the instance level in `gitlab.rb`

### Why use SAML SSO

- Single password across all corporate tools (users authenticate with their corporate credentials)
- Centralised access revocation (removing someone from the IdP removes their GitLab access)
- Enforcement (you can require all members to authenticate via SSO, blocking personal GitLab.com accounts)
- Compliance (authentication events are logged in both your IdP and GitLab)

### Setting up SAML SSO on GitLab.com (group level)

Group -> Settings -> **SAML SSO** -> Enable SAML authentication

GitLab shows you three values to configure in your IdP:

- **Identifier (Entity ID)**: `https://gitlab.com/groups/YOUR_GROUP/-/saml/metadata`
- **Reply URL (Assertion Consumer Service URL)**: `https://gitlab.com/groups/YOUR_GROUP/-/saml/callback`
- **GitLab sign-on URL**: `https://gitlab.com/groups/YOUR_GROUP/-/saml/sso`

In your IdP, configure a new SAML application with these values.

Back in GitLab, enter:

- **Identity provider SSO URL**: the URL your IdP provides for SSO
- **Certificate fingerprint**: SHA-1 or SHA-256 fingerprint of the IdP's certificate

Save and test by clicking **Verify SAML configuration**.

### SAML SSO enforcement options

**Default membership role**: the role new members receive when joining via SSO (default: Guest - they can be manually elevated after joining).

**Enforce SSO-only authentication**: when enabled, members of this group can only access it via SAML. Direct GitLab.com sign-in is blocked for this group's resources.

**Prevent members from managing their own membership**: blocks members from adding themselves to the group directly - all membership must flow through the IdP.

### Attribute mapping

Configure your IdP to send the following SAML attributes:

| SAML attribute | GitLab uses it for                               |
| -------------- | ------------------------------------------------ |
| `email`        | User email address (required)                    |
| `name`         | Display name                                     |
| `username`     | GitLab username (if not already taken)           |
| `first_name`   | First name                                       |
| `last_name`    | Last name                                        |
| `groups`       | Group membership (for automatic role assignment) |

### Setting up SAML on self-managed GitLab

On self-managed, SAML is configured in `/etc/gitlab/gitlab.rb` at the instance level (affects all users):

```ruby
gitlab_rails['omniauth_enabled'] = true
gitlab_rails['omniauth_allow_single_sign_on'] = ['saml']
gitlab_rails['omniauth_block_auto_created_users'] = false

gitlab_rails['omniauth_providers'] = [
  {
    name: 'saml',
    label: 'Corporate SSO',
    args: {
      assertion_consumer_service_url: 'https://gitlab.example.com/users/auth/saml/callback',
      idp_cert_fingerprint: 'YOUR_IDP_FINGERPRINT',
      idp_sso_target_url: 'https://your-idp.example.com/sso',
      issuer: 'https://gitlab.example.com',
      name_identifier_format: 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent',
    }
  }
]
```

---

## SCIM Provisioning

SCIM (System for Cross-domain Identity Management) automates user provisioning and deprovisioning. When someone joins your company and is added to GitLab in the IdP, SCIM creates their GitLab account and adds them to the group automatically. When they leave and their IdP account is deactivated, SCIM removes their GitLab access.

**Availability**: Premium and above, with SAML SSO already configured.

**Supported IdPs**: Entra ID (Azure AD), Okta, OneLogin.

### How SCIM works with GitLab

1. An administrator adds a new employee to the GitLab group in the IdP (Okta/Entra ID)
2. The IdP sends a SCIM API call to GitLab
3. GitLab creates the account (or links an existing account) and adds the user to the group
4. When the employee leaves and is removed in the IdP, the IdP sends another SCIM call
5. GitLab removes the user's group membership (they lose access to all group projects)

### Enabling SCIM

Group -> Settings -> SAML SSO -> **Generate SCIM token**

Copy the base URL and token. In your IdP's application configuration for the GitLab SAML app, find the SCIM provisioning settings and enter:

- **SCIM base URL**: `https://gitlab.com/api/scim/v2/groups/YOUR_GROUP`
- **API token**: the token generated in GitLab

Configure attribute mapping in the IdP to match the SCIM fields GitLab expects.

---

## Group-Level CI/CD Variables

CI/CD variables defined at the group level are automatically available to every pipeline job in every project in the group and every subgroup below it. This is one of the most powerful group features for managing shared configuration and secrets.

### Creating group-level variables

Group -> Settings -> **CI/CD** -> **Variables** -> **Add variable**

Configure each variable:

- **Key**: the variable name (e.g. `AWS_ACCESS_KEY_ID`, `DEPLOY_TOKEN`, `REGISTRY_URL`)
- **Value**: the value (e.g. the actual AWS key, token value, or URL)
- **Type**: Variable (default) or File (value is written to a temp file; `$KEY` gives the file path)
- **Environment scope**: `*` for all environments, or specific environment names for environment-restricted values
- **Protected**: only available in pipelines running on protected branches or tags
- **Masked**: value is hidden in all job logs. Must meet masking requirements (at least 8 chars, no newlines, specific character requirements)
- **Expanded**: whether `$OTHER_VAR` references inside the value are substituted

### Variable inheritance and precedence

Variables cascade through the hierarchy. A project's pipeline has access to:

1. Variables defined in the project's `.gitlab-ci.yml`
2. Variables defined in the project's CI/CD settings
3. Variables defined in the immediate parent group's CI/CD settings
4. Variables defined in all ancestor groups up to the top-level group

When the same variable name is defined at multiple levels, the most specific level wins:

- Project-level variable beats group-level variable
- Closer group beats more distant ancestor group

### Environment-scoped variables

A powerful pattern: define the same variable name at different values for different environments.

```
DEPLOY_URL = https://staging.example.com   (environment scope: staging)
DEPLOY_URL = https://example.com           (environment scope: production)
```

In `.gitlab-ci.yml`:

```yaml
deploy-staging:
  environment: staging
  script:
    - echo "Deploying to $DEPLOY_URL" # uses staging value

deploy-production:
  environment: production
  script:
    - echo "Deploying to $DEPLOY_URL" # uses production value
```

This lets you use the same variable name across environments while automatically getting the right value based on which environment the job is running in.

### Common group-level variable patterns

```
# Registry credentials (available to all projects for image builds)
CI_REGISTRY = registry.gitlab.com
DOCKER_AUTH_CONFIG = {"auths":{"registry.gitlab.com":{"auth":"..."}}}

# Cloud provider credentials
AWS_ACCESS_KEY_ID = AKIAIOSFODNN7EXAMPLE    (masked, protected)
AWS_SECRET_ACCESS_KEY = wJalrXUtn...         (masked, protected)
AWS_DEFAULT_REGION = eu-west-1

# Notification webhooks
SLACK_WEBHOOK_URL = https://hooks.slack.com/...   (masked)

# Shared configuration
NODE_ENV = production
DEPLOY_TIMEOUT = 300
```

---

## Group-Level Runners

Runners registered at the group level are available to every project in the group and every subgroup below it, without needing to register the runner separately to each project.

### Why use group runners

Imagine a group with 20 projects, each running CI/CD pipelines. Without group runners, you would need to register each runner to all 20 projects (or use instance runners on GitLab.com). With a group runner, register once and all 20 projects can use it.

### Managing group runners

Group -> **CI/CD -> Runners**

This page shows:

- All runners registered to this group
- Status (online, offline, paused)
- Tags
- Most recent jobs
- Pipeline consumption

To register a new runner to the group, click **New group runner** -> configure the runner settings -> copy the authentication token -> register on your machine:

```bash
gitlab-runner register \
  --url "https://gitlab.com/" \
  --token "GROUP_RUNNER_AUTHENTICATION_TOKEN" \
  --executor "docker" \
  --docker-image "alpine:latest" \
  --description "Group Docker runner" \
  --tag-list "docker,linux"
```

### Runner inheritance for subgroups

A runner registered to `acme-corp` is available to all projects in:

- `acme-corp/` directly
- `acme-corp/backend/`
- `acme-corp/backend/api/`
- `acme-corp/frontend/`
- And any other subgroup or project in the hierarchy

Runners registered to `acme-corp/backend` are only available to projects within `acme-corp/backend/` and below - not to projects directly in `acme-corp/`.

### Controlling runner access

Disable instance runners (GitLab.com shared runners) for a specific project: Project -> Settings -> CI/CD -> Runners -> disable "Enable instance runners for this project".

Disable group runners for a specific project: same location, disable "Enable group runners for this project".

This gives you fine-grained control: some projects can use the shared runners, others are restricted to specific tagged runners.

---

## Visibility Levels and Their Implications

Visibility applies to both groups and projects. The visibility of a project cannot exceed the visibility of its containing group.

### Public

- Group page and its public projects are visible to anyone without signing in
- Anyone can clone public repositories
- Anyone can view issues, MRs and wiki pages (if not restricted by project settings)
- Public projects do not count toward the five-user free tier limit on GitLab.com
- Open source projects should be public

### Internal (self-managed only)

- Visible to any authenticated user on the GitLab instance
- Not visible to users not signed in to that GitLab instance
- Not available for new projects on GitLab.com (disabled July 2019)
- Useful on corporate self-managed instances for "company-wide visible" resources

### Private

- Visible only to members of the group or project (direct or inherited)
- Anyone without membership sees a 404 page
- Private projects in a private group count toward the five-user free tier limit on GitLab.com

### Changing visibility

Changing a group from public to private immediately makes all its subgroups and projects private. Members without explicit invitation can no longer access anything. This is not reversible from a user-experience standpoint - those users lose access immediately.

---

## Audit Events

Audit events record security-relevant actions taken by users within a group. They provide an immutable log of who did what and when.

**Navigate**: Group -> Security & Compliance -> **Audit events**

### What is logged

- Member additions, removals and role changes
- Group settings changes (visibility, SSO configuration)
- Project creation, deletion and transfers
- CI/CD variable changes (key names only, not values)
- SSH key and PAT additions and removals
- Failed login attempts (Premium)
- Repository operations (Premium: push, force push, protect/unprotect branch)
- Access token creation and revocation
- Runner registration changes

### Filtering audit events

Filter by:

- Date range
- User (who performed the action)
- Author type (User, DeployToken, etc.)
- Event type
- IP address

### Exporting audit events

Audit events can be exported as CSV for compliance reporting or ingested into a SIEM (Security Information and Event Management) system via the GitLab API or webhook streaming.

Group -> Security & Compliance -> Audit events -> **Export as CSV**

---

## Structuring a Real Organisation

The way you structure your GitLab namespace hierarchy shapes your access management, URL structure and settings inheritance for years. Getting it right from the start matters.

### Recommended patterns

**Pattern 1: Mirror your org chart**

```
company-name/
├── engineering/
│   ├── platform/
│   │   ├── infrastructure-as-code
│   │   └── developer-tooling
│   ├── backend/
│   │   ├── payments-service
│   │   ├── user-service
│   │   └── notification-service
│   └── frontend/
│       ├── web-app
│       └── mobile-app
├── data/
│   ├── etl-pipelines
│   └── analytics
└── security/
    ├── vulnerability-reports
    └── compliance-docs
```

**Advantages**: access naturally follows team membership, group runners and variables match team needs, URL paths are intuitive.

**Disadvantages**: if the org chart changes, the namespace hierarchy becomes outdated. Reorganising a deeply nested group hierarchy is painful.

**Pattern 2: Organise by product**

```
company-name/
├── product-payments/
│   ├── payments-backend
│   ├── payments-frontend
│   ├── payments-infrastructure
│   └── payments-docs
├── product-analytics/
│   ├── analytics-backend
│   ├── analytics-dashboard
│   └── analytics-pipeline
└── shared/
    ├── design-system
    ├── shared-libraries
    └── internal-tools
```

**Advantages**: each product team owns its complete namespace, making it easier to see all code for a product together.

**Disadvantages**: engineers working across products need access to multiple top-level groups.

**Pattern 3: Flat with project naming conventions**

```
company-name/
├── frontend-web-app
├── frontend-mobile-app
├── backend-payments
├── backend-users
├── infra-kubernetes
├── infra-terraform
└── data-pipelines
```

**Advantages**: simple, flat, easy to understand. No subgroup complexity.

**Disadvantages**: no hierarchy for settings inheritance, all runners must be registered at the top level or per-project.

### Rules of thumb

- Keep the top-level group flat if possible. Deep nesting adds cognitive overhead.
- Match the hierarchy to how your CI/CD variables and runners should be scoped.
- Use at most three levels of nesting for most organisations: company -> team -> project.
- Avoid putting a single project in its own group just for a namespace - use personal namespaces or flatten.
- Create groups for collections of related projects, not for individual projects.

---

## Try It Yourself

**Exercise 1: Create a group and add members with different roles**

1. Create a new group: Create new (+) -> New group -> set name, URL and visibility Public -> Create group
2. Navigate to Manage -> Members -> Invite members
3. Invite yourself with the Reporter role and a 7-day expiry
4. Notice you appear in both the Direct tab and in the overview
5. Change the role to Developer by clicking the role dropdown

**Exercise 2: Create a group-level CI/CD variable**

1. In your group: Settings -> CI/CD -> Variables -> Add variable
2. Key: `TEST_GROUP_VAR`, Value: `hello-from-group`, check Masked
3. Create a project inside this group
4. In the project, create `.gitlab-ci.yml`:

```yaml
test-variable:
  script:
    - echo "Group variable value is $TEST_GROUP_VAR"
```

5. Push and run the pipeline. The variable from the group appears in the project's job without being defined in the project.

**Exercise 3: Create a deploy token**

1. Project -> Settings -> Repository -> Deploy tokens -> Add new token
2. Name: "test-deploy-token", tick `read_repository`
3. Note the username and token value
4. Test cloning using the token:

```bash
git clone https://DEPLOY_TOKEN_USERNAME:DEPLOY_TOKEN_VALUE@gitlab.com/NAMESPACE/PROJECT.git
```

---

## Common Mistakes

**Putting all developers in the Owner role.** Owner is the admin role - it allows deletion, visibility changes, billing management and removing other Owners. Most active contributors need Developer. Leads who manage the project configuration need Maintainer. Only those responsible for the group's governance need Owner.

**Adding individuals to every project instead of managing at the group level.** If you have 15 projects and add each developer to each project separately, you have 15 places to update when someone joins, leaves or changes role. Add people to the group once and let inheritance do the work.

**Not using access token expiry.** Group and project access tokens with no expiry are a security risk that accumulates over time. Old tokens for departed contractors, legacy integrations and forgotten automation scripts accumulate silently. Set expiry on all tokens. Put rotation dates in your team calendar.

**Confusing group access tokens with personal access tokens.** PATs are tied to a person's account. If that person leaves and their account is deactivated, their PAT stops working, breaking any automation that used it. Group access tokens are independent of any personal account and continue working regardless of team changes.

**Not setting up SCIM when using SAML.** Teams configure SAML SSO but forget SCIM. The result: users authenticate via SSO, but must still be manually added and removed from GitLab. When someone leaves the company and their IdP account is deactivated, their GitLab access persists indefinitely. SCIM automates the full lifecycle.

**Creating one group per project.** This gives each project its own URL namespace but eliminates all the group benefits - no shared variables, no shared runners, no inheritance. Create groups for collections of related projects, not for individual projects.

**Not reviewing inherited access periodically.** Manage -> Members shows both direct and inherited members. As team structures change, people accumulate access to things they no longer work on. Schedule a quarterly access review: look at who has what access, remove stale memberships, adjust roles that have not been reviewed.

---

## Summary

- **Groups** provide three things simultaneously: a URL namespace for projects, an access container where membership cascades to all projects inside and a settings container where variables and runners apply to all projects below
- **Six standard roles** in ascending order: Guest (view and comment), Reporter (clone and read), Developer (push and create MRs), Maintainer (manage project configuration), Owner (full control including delete and billing). Non-hierarchical additions: Planner (planning access only, non-billable), Minimal Access (visibility only, non-billable), Security Manager (vulnerability access, non-billable)
- **Inheritance**: roles granted at a parent group flow downward to all subgroups and projects. You can grant a higher role at a lower level. You cannot grant a lower role at a lower level than the parent group provides.
- **Group access tokens** (Premium on GitLab.com, any licence on self-managed): credentials for automation not tied to a personal account. Create bot users. Scoped to the group. Expiry required (max 365 days).
- **Project access tokens**: same as group access tokens but scoped to one project. Available on all plans on self-managed.
- **Deploy tokens**: limited-scope credentials for Git clone and registry pull/push only. No API access. Can be non-expiring. Use for production deployment infrastructure.
- **Deploy keys**: SSH public keys for repository-level read (or read-write) access. No API access. Good for deployment servers that clone via SSH.
- **SAML SSO** (Premium on GitLab.com, any licence on self-managed): authenticate group members via your corporate IdP. Configured per top-level group on GitLab.com, at instance level on self-managed.
- **SCIM**: automates user provisioning and deprovisioning when combined with SAML. Supported IdPs: Entra ID, Okta, OneLogin.
- **Group-level CI/CD variables**: defined once, inherited by every project in the group and every subgroup below. Use for shared secrets, shared configuration and environment-specific values.
- **Group-level runners**: registered once, available to all projects in the group and below.
- **Audit events**: immutable log of security-relevant actions. Filter, export as CSV, stream to SIEM.
- **Visibility**: Public (anyone), Internal (self-managed - authenticated users), Private (members only). Projects cannot be more public than their containing group.

---

## Sources and Further Reading

- [GitLab groups documentation](https://docs.gitlab.com/user/group/) - complete group management reference
- [Permissions and roles](https://docs.gitlab.com/user/permissions/) - full role permission matrix
- [Group access tokens](https://docs.gitlab.com/user/group/settings/group_access_tokens/) - creating and managing group tokens
- [Deploy tokens](https://docs.gitlab.com/user/project/deploy_tokens/) - deploy token scopes and usage
- [Deploy keys](https://docs.gitlab.com/user/project/deploy_keys/) - SSH key-based repository access
- [SAML SSO documentation](https://docs.gitlab.com/user/group/saml_sso/) - GitLab.com group SAML setup
- [SCIM provisioning](https://docs.gitlab.com/user/group/saml_sso/scim_setup/) - automated user lifecycle
- [Audit events](https://docs.gitlab.com/administration/audit_events/) - what is logged and how to access it
- [CI/CD variables](https://docs.gitlab.com/ci/variables/) - variable types, scopes and precedence

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

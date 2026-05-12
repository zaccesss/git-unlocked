# Collaborators, Teams and Organisations

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

When you are working with other people on GitHub, you need to understand three layers: how to add individual collaborators to a repository, how teams work within an organisation and how organisations themselves are structured. This file covers all three in full, including every permission level, outside collaborators, team management, nested teams, CODEOWNERS, deploy keys, organisation-wide settings, converting personal repositories to organisations and practical patterns for student groups, open source projects and professional teams.

---

## Table of Contents

- [Personal repositories vs organisation repositories](#personal-repositories-vs-organisation-repositories)
- [Adding collaborators to a personal repository](#adding-collaborators-to-a-personal-repository)
- [Permission levels for repository collaborators](#permission-levels-for-repository-collaborators)
- [Managing pending invitations](#managing-pending-invitations)
- [Deploy keys](#deploy-keys)
- [What is a GitHub organisation](#what-is-a-github-organisation)
- [Creating an organisation](#creating-an-organisation)
- [Inviting members to an organisation](#inviting-members-to-an-organisation)
- [Organisation roles](#organisation-roles)
- [Outside collaborators](#outside-collaborators)
- [Converting a personal repository to an organisation repository](#converting-a-personal-repository-to-an-organisation-repository)
- [Teams](#teams)
- [Creating and managing teams](#creating-and-managing-teams)
- [Team permissions on repositories](#team-permissions-on-repositories)
- [Nested teams](#nested-teams)
- [CODEOWNERS](#codeowners)
- [Organisation-level settings](#organisation-level-settings)
- [Organisation-level Actions policies](#organisation-level-actions-policies)
- [Verified and approved domains](#verified-and-approved-domains)
- [Audit log](#audit-log)
- [GitHub Enterprise managed users](#github-enterprise-managed-users)
- [Practical patterns](#practical-patterns)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Personal Repositories vs Organisation Repositories

**Personal repositories** live under your user account: `github.com/YOUR_USERNAME/repo-name`. You own them, you control access and they are tied to your account. If you change your username, the old URLs break (GitHub redirects for a while, but not permanently). If your account is deleted, the repositories are gone.

**Organisation repositories** live under an organisation account: `github.com/ORG_NAME/repo-name`. The organisation owns them. Multiple people can be administrators. Repositories persist even if the original creator leaves or deletes their account. This makes organisations the correct choice for any work that involves a group of people or needs to outlast an individual contributor.

**When to use which:**

| Situation | Recommended |
|---|---|
| Personal projects and portfolio | Personal repository |
| University group project | Organisation |
| Open source project with multiple maintainers | Organisation |
| Company or team codebase | Organisation |
| Contract work you own | Personal repository |
| Contract work the client owns | Organisation owned by the client |

---

## Adding Collaborators to a Personal Repository

For small projects or working with one or two people, adding direct collaborators to a personal repository is the simplest option.

**Step 1.** Go to your repository on GitHub.

**Step 2.** Click the **Settings** tab (gear icon in the repository tab bar).

**Step 3.** In the left sidebar, click **Collaborators** (on personal repos) or **Collaborators and teams** (you may see this label).

**Step 4.** Click **Add people**.

**Step 5.** Type the collaborator's GitHub username or email address. GitHub suggests matches as you type.

**Step 6.** Select the permission level (see below).

**Step 7.** Click **Add USERNAME to this repository**.

The collaborator receives an email invitation. They must accept it before gaining access. Invitations expire after 7 days.

**Removing a collaborator:**

Go to **Settings → Collaborators**, find the person and click **Remove**. They immediately lose access to the repository. Any issues or pull requests they opened remain.

---

## Permission Levels for Repository Collaborators

| Level | Read | Manage issues/PRs | Push | Merge PRs | Manage releases | Manage webhooks/topics | Repo settings | Delete repo |
|---|---|---|---|---|---|---|---|---|
| **Read** | Yes | Open only | No | No | No | No | No | No |
| **Triage** | Yes | Yes | No | No | No | No | No | No |
| **Write** | Yes | Yes | Yes | Yes | Yes | No | No | No |
| **Maintain** | Yes | Yes | Yes | Yes | Yes | Yes | No | No |
| **Admin** | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes |

**In plain English:**

- **Read** - view code, clone, open issues and PRs. Cannot push or modify the repository.
- **Triage** - Read plus the ability to label, assign, close, reopen and respond to issues and PRs. Used for community managers and people who manage the issue tracker but do not write code.
- **Write** - Triage plus push to non-protected branches, create branches, manage releases and edit the wiki. This is the right level for active contributors.
- **Maintain** - Write plus the ability to manage repository topics, archive the repository, manage webhooks and push protection bypass. Used for project leads who need more control but should not be able to delete the repository.
- **Admin** - Full access including repository settings, branch protection rules, adding/removing collaborators and deleting the repository. Only give this to people you trust completely.

For most collaborators on a personal project, **Write** is correct. Use **Admin** only when someone genuinely needs to change repository settings.

---

## Managing Pending Invitations

When you invite a collaborator, they receive an email. If they have not accepted after 7 days, the invitation expires.

**View pending invitations:**

Go to **Settings → Collaborators**. Pending invitations appear in a separate list above accepted collaborators.

**Resend or cancel an invitation:**

Click the three-dot menu next to any pending invitation and choose **Resend invitation** or **Cancel invitation**.

**If a collaborator says they did not receive the email:**

Ask them to check their spam folder. Alternatively, they can go directly to `github.com/settings/notifications` to check pending organisation and repository invitations. Or share the repository URL and ask them to visit it - if the invitation is pending they may see an option to accept on the page.

---

## Deploy Keys

Deploy keys are SSH keys that give access to a single repository. They are used for deployment pipelines and server automation that need to pull or push code without using a personal account's credentials.

**Why deploy keys instead of a personal access token:**

- Scoped to one repository only - a compromised deploy key affects only that repository
- No user account required - the CI server does not need a GitHub account
- Read-only by default - can optionally be granted write access if the pipeline needs to push

**Adding a deploy key:**

1. Generate an SSH key pair on the server that will use it:

   ```bash
   ssh-keygen -t ed25519 -C "deploy key for production server" -f deploy_key
   ```

   This creates `deploy_key` (private key) and `deploy_key.pub` (public key).

2. Go to your repository → **Settings → Deploy keys**

3. Click **Add deploy key**

4. Paste the contents of `deploy_key.pub` into the Key field

5. Give it a title (e.g. "Production Server Deploy Key")

6. Tick **Allow write access** only if the pipeline needs to push commits or tags

7. Click **Add key**

8. On the server, configure SSH to use the private key when connecting to GitHub:

   ```bash
   # ~/.ssh/config
   Host github.com
     IdentityFile /path/to/deploy_key
     User git
   ```

**Rotating a deploy key:**

Generate a new SSH key pair, add the new public key as a new deploy key, update the server to use the new private key, then remove the old deploy key from the repository.

---

## What is a GitHub Organisation

A GitHub organisation is a shared account that groups repositories and people under a single namespace.

**What organisations have:**

- Their own profile page at `github.com/ORG_NAME` with a bio, website and repositories
- Repositories that belong to the organisation rather than any individual
- A membership roster with roles (Owner, Member, Billing Manager, Security Manager)
- Teams for grouping members and managing repository access at scale
- Organisation-wide settings for Actions, security policies and member permissions
- An audit log of all significant actions by members and owners

**Pricing:**

- **Free:** Unlimited public repositories, unlimited private repositories with full features, up to 3 collaborators per private repository, GitHub Actions (2000 minutes/month), GitHub Packages (500 MB)
- **Team ($4/user/month):** Unlimited collaborators on private repositories, GitHub Actions (3000 minutes/month), branch protection, required reviewers, Insights
- **Enterprise ($21/user/month):** SSO, SAML, audit log streaming, compliance features, 50000 Actions minutes

Students with the GitHub Student Developer Pack get the **Team plan free** for their organisations.

---

## Creating an Organisation

**Step 1.** Click the **+** icon in the top-right corner of any GitHub page.

**Step 2.** Click **New organisation**.

**Step 3.** Choose a plan. Click **Create a free organisation** to start with the Free plan. You can upgrade later.

**Step 4.** Enter an organisation name. This becomes part of all repository URLs: `github.com/YOUR_ORG_NAME/repo`. Choose the name carefully. It can be changed later but renaming breaks all existing repository URLs (GitHub creates redirects but they are not permanent).

**Step 5.** Enter a contact email address for billing and important notifications.

**Step 6.** Select whether this organisation belongs to you personally or to a business/institution.

**Step 7.** Optionally invite initial members on the next screen, or skip and invite later.

**Step 8.** Click **Complete setup**.

You are now the sole owner of the organisation. The organisation page is live at `github.com/YOUR_ORG_NAME`.

---

## Inviting Members to an Organisation

**Step 1.** Go to your organisation page at `github.com/ORG_NAME`.

**Step 2.** Click the **People** tab.

**Step 3.** Click **Invite member**.

**Step 4.** Type the GitHub username or email address of the person to invite.

**Step 5.** Select their role:
- **Member** - regular organisation membership, access limited to what teams grant them
- **Owner** - full administrative access to the organisation

**Step 6.** Optionally add them to one or more teams during the invite process.

**Step 7.** Click **Send invitation**.

The invited person receives an email invitation. Once they accept, they appear in the People list.

**Removing a member:**

Go to **People**, click the three-dot menu next to the member and select **Remove from organisation**. This removes their organisation membership and all access granted through membership and teams. Their forked copies of organisation repositories remain on their personal account.

---

## Organisation Roles

| Role | Who has it | Key capabilities |
|---|---|---|
| **Owner** | Assigned manually; always at least one | Full admin access - manage members, teams, settings, billing, repositories, delete the organisation |
| **Member** | All regular members | Access repositories according to team membership and base permissions |
| **Billing manager** | Assigned manually | Manage billing settings only. Cannot access repositories or member data. |
| **Security manager** | Assigned manually (a team is granted this role) | View and manage security alerts and settings across all repositories. Read access to all repositories. |
| **Outside collaborator** | External people given repo access | Access to specific repositories only - not a member of the organisation |

**Every organisation should have at least two owners.** If the sole owner loses access to their GitHub account, the organisation becomes unrecoverable without GitHub Support. Add a trusted second owner immediately after creating an organisation.

---

## Outside Collaborators

Outside collaborators have access to one or more specific repositories in an organisation but are not members of the organisation itself. This is the correct model for:

- Freelancers and contractors who need access to one project
- Open source contributors who need write access to a specific repository
- Client or partner employees who need to see one repository
- Alumni or graduated students who should keep contributing to a project

**Adding an outside collaborator:**

1. Go to the specific repository in the organisation
2. Go to **Settings → Collaborators and teams**
3. Under **Manage access**, click **Add people**
4. Search for their GitHub username
5. Select a permission level (Read, Triage, Write, Maintain, Admin)
6. Click **Add to repository**

An invitation is sent to them. They accept it to gain access.

**Key difference from full members:**

- Outside collaborators are not visible in the organisation People list (only in individual repository access)
- They do not get any access granted by organisation-wide base permissions
- They do not receive organisation-level notifications
- They cannot be added to teams

**Managing all outside collaborators:**

Go to **Organisation Settings → People → Outside collaborators** to see and manage every outside collaborator across all repositories.

**Limits on the Free plan:**

The GitHub Free organisation plan allows up to 3 collaborators on private repositories. The Team and Enterprise plans have no limit. Public repositories have no collaborator limit on any plan.

---

## Converting a Personal Repository to an Organisation Repository

If you built a project under your personal account and now want it to be owned by an organisation, you can transfer it.

**Transfer a personal repository to an organisation:**

1. Go to the repository
2. Click **Settings**
3. Scroll to the **Danger Zone** at the bottom
4. Click **Transfer repository**
5. Type the repository name to confirm
6. Enter the organisation name as the new owner
7. Click **I understand, transfer this repository**

**What happens:**

- The repository moves to `github.com/ORG_NAME/repo-name`
- GitHub creates a redirect from the old URL for a limited time
- All issues, pull requests, milestones, stars and forks transfer with the repository
- Collaborator access is removed (you need to re-grant access through the organisation's teams or collaborator settings)
- GitHub Actions secrets are removed and need to be recreated
- Any webhook URLs pointing to the old repository URL may need updating

> [!IMPORTANT]
> Always update the `remote` URL in any local clones after a transfer. Run `git remote set-url origin https://github.com/ORG_NAME/repo-name.git` in each local copy.

---

## Teams

Teams are named groups of organisation members. You manage repository access by granting it to a team rather than to individuals. Every member of the team inherits the team's repository access.

**Why teams matter:**

Without teams, if you have 15 developers and 20 repositories, you would need to manage 300 individual access grants. With teams, you manage 4 or 5 teams and grant each team access to the relevant repositories. Adding a new developer means adding them to a team - they immediately inherit access to all the right repositories.

**Teams as notification targets:**

Teams can be mentioned in issues, PRs and comments with `@ORG_NAME/TEAM_NAME`. This sends a notification to all team members. Useful for: "hey @acmecorp/backend-team, can someone review this?"

---

## Creating and Managing Teams

**Create a team:**

1. Go to your organisation page at `github.com/ORG_NAME`
2. Click the **Teams** tab
3. Click **New team**
4. Enter a **team name** (e.g. `frontend`, `backend`, `design`, `contributors`)
5. Enter an optional description
6. Choose visibility:
   - **Visible** - any organisation member can see the team, its members and its repositories
   - **Secret** - only team members and organisation owners can see the team. Useful for sensitive teams like `security-response` or `leadership`.
7. Optionally set a parent team (see Nested Teams below)
8. Click **Create team**

**Add members to a team:**

1. Go to the team page (click the team name in the Teams list)
2. Click the **Members** tab
3. Click **Add a member**
4. Search by GitHub username and add them
5. Set their team role: **Member** or **Maintainer**

**Team maintainers:**

A team maintainer can add and remove members from that team without needing to be an organisation owner. Assign this role to team leads.

**Team discussions:**

Teams have their own discussion page at the team's GitHub page. Team discussions are only visible to team members and organisation owners. Useful for team-specific announcements and internal conversations.

---

## Team Permissions on Repositories

**Grant a team access to a repository (from the team side):**

1. Go to the team page
2. Click the **Repositories** tab
3. Click **Add repository**
4. Search for the repository by name and select it
5. Choose the permission level: Read, Triage, Write, Maintain or Admin

**Grant a team access to a repository (from the repository side):**

1. Go to the repository
2. Click **Settings → Collaborators and teams**
3. Under **Manage access**, click **Add teams**
4. Search for the team name and select it
5. Choose the permission level

**Changing a team's access level:**

Go to the repository's **Settings → Collaborators and teams**. Find the team and use the dropdown to change the permission level. Or go to the team's Repositories tab and update it there.

**Removing a team's access:**

Go to **Settings → Collaborators and teams**, find the team and click **Remove** next to it. All team members immediately lose the access granted by this team (unless they have access through another team or as a direct collaborator).

---

## Nested Teams

Teams can have parent teams. A child team inherits all the repository permissions of its parent team, plus any additional permissions you grant the child team directly.

**Example hierarchy:**

```
engineering                   (Write to all 8 engineering repositories)
├── frontend                  (Write to website + web-app)
│   ├── frontend-leads        (Maintain on website + web-app)
│   └── design-system         (Write to design-system repo only)
├── backend                   (Write to api + worker-services)
└── devops                    (Admin on infrastructure + ci-config)
```

In this structure:
- A member of `frontend` gets Write to `website` and `web-app` plus Write to all 8 engineering repositories (inherited from `engineering`)
- A member of `frontend-leads` gets all of `frontend`'s permissions plus Maintain on `website` and `web-app`
- A member of `devops` gets Admin on `infrastructure` and `ci-config` plus Write to all 8 engineering repositories
- Members of `engineering` only get Write to all 8 repositories with no team-specific additions

**Creating a child team:**

When creating a team, set the **Parent team** field to an existing team. The new team immediately inherits the parent's repository access.

**When to use nested teams:**

Nested teams work well for large organisations with hierarchical structures. For smaller teams (under 20 people), flat teams are usually simpler and easier to understand.

---

## CODEOWNERS

The `CODEOWNERS` file defines which individuals or teams are automatically requested for review whenever a pull request modifies files they are listed as owning. It ensures the right person always reviews the right code, without the PR author needing to manually select reviewers.

**Create the file** at one of these paths (GitHub checks in this order):

1. `CODEOWNERS` (root of the repository)
2. `.github/CODEOWNERS`
3. `docs/CODEOWNERS`

**Syntax:**

```
# Each line: a pattern followed by one or more owners
# Patterns follow .gitignore rules
# Owners can be @username or @ORG/TEAM_NAME
# Later rules override earlier ones for the same file

# Default owner for everything
*                        @isaacadjei

# JavaScript files are owned by the frontend team
*.js                     @acmecorp/frontend
*.jsx                    @acmecorp/frontend
*.ts                     @acmecorp/frontend
*.tsx                    @acmecorp/frontend

# Python files are owned by the backend team
*.py                     @acmecorp/backend

# The entire docs directory is owned by the technical writers team
docs/                    @acmecorp/technical-writers

# The README is always owned by the project lead personally
README.md                @isaacadjei

# Infrastructure files require both a DevOps review and a senior engineer
/infrastructure/         @acmecorp/devops @seniorengineer

# Security-critical files require the security team
src/auth/                @acmecorp/security @isaacadjei

# This file itself - changes need the project lead to review
.github/CODEOWNERS       @isaacadjei
```

**How ownership resolution works:**

The last matching rule wins. If `README.md` is listed both under `*` (all files) and under a specific rule, the specific rule applies.

**Enabling required CODEOWNER reviews:**

By default, CODEOWNERS auto-requests reviewers but does not require them to approve before merging. To make reviews mandatory:

1. Go to **Settings → Branches → Branch protection rules**
2. Edit the rule for `main`
3. Tick **Require review from Code Owners**
4. Set the minimum number of approvals required
5. Save

With this enabled, a PR cannot be merged until all automatically-requested code owners have approved it.

**CODEOWNERS errors:**

GitHub validates the CODEOWNERS file and shows errors if owners do not exist, patterns are invalid or team names are wrong. Check the file for errors in: **Insights → CODEOWNERS** (shown if GitHub detects errors).

---

## Organisation-Level Settings

Go to your organisation page and click **Settings** to access these.

**Member privileges:**

These settings control what organisation members can do by default:

- **Base permissions** - the minimum permission all members have on all repositories. Options: None, Read, Write, Admin. Set to **None** or **Read** for most organisations and manage actual access through teams. Setting this to **Write** means every member can push to every repository, which is almost never what you want.
- **Repository creation** - whether members can create public, private or both types of repositories. Disable public creation if you do not want members creating public repositories under the organisation name without review.
- **Repository forking** - whether members can fork private organisation repositories to their personal accounts. Disable this if you need to prevent internal code from leaving organisation-owned repositories.
- **Repository deletion and transfer** - whether members with Admin on a repository can delete or transfer it. Disable to require Owner-level action for destructive operations.
- **Pages creation** - whether members can create GitHub Pages sites from organisation repositories. Can be restricted to members with Admin on the repository.

**Two-factor authentication:**

Go to **Settings → Authentication security → Require two-factor authentication**.

When enabled, any organisation member who has not set up 2FA is automatically removed from the organisation (they are notified and can rejoin after enabling 2FA). They do not lose their GitHub account, only their organisation membership.

This is one of the most important security settings for any organisation with sensitive code.

---

## Organisation-Level Actions Settings

Control how GitHub Actions operates across all repositories in your organisation.

**Go to:** Organisation **Settings → Actions → General**

**Actions permissions:**

Choose which actions can be used across all repositories:

- **Allow all actions** - any action from any source can be used (least restrictive)
- **Allow local actions and reusable workflows only** - only actions defined within the organisation's own repositories. Prevents supply-chain attacks via malicious third-party actions.
- **Allow local actions and select non-local actions** - allow specific external actions by pattern (e.g. `actions/*` for GitHub's own actions, `docker/*` etc.). Most balanced option for security.
- **Disable Actions for all repositories** - turns off Actions entirely

**Runner groups:**

Create runner groups to control which repositories can use self-hosted runners. Go to **Settings → Actions → Runner groups**.

**Default GITHUB_TOKEN permissions:**

Set whether the `GITHUB_TOKEN` in workflows defaults to read-only or read-write across all repositories. Read-only is the more secure default - workflows explicitly request write permissions only when needed.

**Required workflows:**

On GitHub Enterprise, you can configure required workflows that must pass on all PRs in the organisation, regardless of which repository. Useful for mandatory security scanning or compliance checks.

---

## Verified and Approved Domains

Organisations can verify that they control specific domain names. This has two benefits:

1. **Visible verification badge** on the organisation profile showing the verified domain
2. **Email restriction** - restrict membership invitations to email addresses on the verified domain

**Verify a domain:**

1. Go to **Organisation Settings → Verified and approved domains**
2. Click **Add a domain**
3. Enter your domain (e.g. `acmecorp.com`)
4. GitHub provides a DNS TXT record to add to your domain's DNS settings
5. After adding the TXT record, click **Verify**
6. DNS propagation can take up to 24 hours

**Restrict invitations to verified domains:**

After verifying a domain, go back to **Verified and approved domains** and enable **Restrict email notifications and invitations to verified domains only**. Any invitation to an email address not on a verified domain will be blocked.

---

## Audit Log

The organisation audit log is a complete record of every significant action taken by organisation members and owners.

**Access:** Organisation **Settings → Audit log**

**What is logged:**

- Member additions, removals and role changes
- Repository access grants and removals
- Repository creation, deletion and visibility changes
- Team creation, membership changes and deletion
- Branch protection rule changes
- Organisation settings changes
- GitHub Actions workflow runs and changes
- OAuth and GitHub App authorisations

**Searching the audit log:**

The audit log supports filtering by actor, event type, date range and action. Examples:

```
actor:USERNAME                 # Actions by a specific person
action:repo.create            # All repository creations
action:member.add             # All member additions
created:>2025-01-01           # Actions after a date
```

**Exporting:**

The audit log can be exported as JSON or CSV for compliance reporting. On GitHub Enterprise, audit log streaming is available to push logs in real time to Azure Event Hubs, Amazon S3, Google Cloud Storage or Datadog.

**Retention:**

Audit log events are retained for 3 months on GitHub Free and Team plans. GitHub Enterprise plans have longer retention options.

---

## GitHub Enterprise Managed Users

GitHub Enterprise with **managed users (EMU)** is a model where the organisation's identity provider (IdP) - such as Azure Active Directory, Okta or OneLogin - controls all GitHub accounts.

**In an EMU organisation:**

- All member accounts are provisioned and de-provisioned by the IdP via SCIM
- Members cannot create personal repositories - all repositories must be under the organisation
- Members cannot interact with content outside the organisation (no starring external repos, no public gists)
- The IdP controls username format (typically `username_ORG` pattern)
- Access is automatically revoked when an employee leaves and their IdP account is deactivated

**Why this matters:**

If you join a company that uses GitHub EMU, your work GitHub account is a company-managed account separate from your personal account. You cannot use it outside the organisation. You need a completely separate personal account for personal projects and open source contributions.

EMU is not available on Free, Pro or Team plans - it requires GitHub Enterprise Cloud.

---

## Practical Patterns

**Pattern 1 - University group project (3 to 6 people):**

- Create a free GitHub organisation named after the project (e.g. `eecs2025-team-3`)
- All team members are organisation members
- Create a single team called `contributors` with Write access to all repositories
- Add 2 people as organisation owners
- Use branch protection on `main` requiring at least one other team member to approve PRs
- Create a `CODEOWNERS` file if different people own different subsystems

**Pattern 2 - Open source project:**

- Organisation owns the repository
- Core maintainers are in a team with Write or Maintain access
- Outside contributors fork and submit PRs (they never need write access to the main repository)
- `CODEOWNERS` file auto-requests appropriate reviewers
- Branch protection on `main` requires 1 approval and all status checks to pass
- Set base permissions to Read so contributors who become members cannot accidentally push directly

**Pattern 3 - Small company or startup (5 to 30 people):**

- Organisation per company
- Teams per department: `engineering`, `design`, `devops`
- Nested teams where appropriate: `engineering/frontend`, `engineering/backend`
- Require 2FA for all members
- Base permissions set to None - all access via teams only
- Required code owner reviews on protected branches
- Audit log reviewed monthly

**Pattern 4 - Large enterprise (100+ people):**

- Multiple organisations or a single organisation with many teams
- Required workflows enforce security scanning and compliance checks
- Managed Users (EMU) for tighter identity control
- Deploy keys or GitHub Apps (never personal tokens) for all CI/CD
- Organisation-level Actions policy restricts to approved actions only
- Audit log streaming to SIEM for real-time security monitoring

---

## Try It Yourself

**Step 1 - Create a free organisation:**

1. Click **+** → **New organisation** → **Create a free organisation**
2. Name it something you will remember (e.g. `git-unlocked-practice-YOUR_USERNAME`)
3. Complete the setup

**Step 2 - Create a repository inside it:**

1. From the organisation page, click **New repository**
2. Name it `practice-repo`
3. Tick **Add a README file** and click **Create repository**

**Step 3 - Create a team and give it access:**

1. Click the **Teams** tab on the organisation page
2. Click **New team**, name it `contributors`, set visibility to Visible, click **Create team**
3. Go to the team's **Members** tab, add yourself as a member
4. Go to the team's **Repositories** tab, add `practice-repo` with **Write** permission

**Step 4 - Create a CODEOWNERS file:**

1. In `practice-repo`, click **Add file → Create new file**
2. Type `.github/CODEOWNERS` as the file name (the slash creates the folder automatically)
3. Add:

   ```
   # Default owner
   *          @YOUR_USERNAME
   README.md  @YOUR_USERNAME
   ```

4. Commit it

**Step 5 - Enable branch protection:**

1. Go to the repository **Settings → Branches**
2. Click **Add rule**
3. Set Branch name pattern to `main`
4. Tick **Require a pull request before merging** and set required approvals to 1
5. Tick **Require review from Code Owners**
6. Click **Create**

---

## Common Mistakes

**Adding everyone as Admin.** Admins can delete the repository, change all settings and remove other collaborators. Most contributors only need Write. Reserve Admin for people who genuinely need to manage repository settings.

**Using personal repositories for group work.** If the owner leaves the course, changes their username or deletes their account, all URLs break and the repository may be inaccessible. Use an organisation for any collaborative work from the start.

**Setting base permissions too high.** If you set base permissions to Write, every organisation member can push to every repository. Set it to None and manage all access through teams.

**Forgetting outside collaborators exist.** If a contractor or open source contributor needs access to one specific repository, use outside collaborator access. Do not make them a full organisation member.

**Having only one owner.** If the sole owner loses access to their GitHub account and there is no second owner, the organisation is effectively unmanaged. GitHub Support can help but the process is slow. Add a second owner immediately.

**Not using CODEOWNERS.** Without CODEOWNERS, reviewers must be manually selected for every PR. This creates inconsistency and means PRs get merged without review from the people who know the relevant code. A CODEOWNERS file makes code review automatic and consistent.

**Forgetting that pending invitations expire.** Organisation and repository invitations expire after 7 days. If a team member says they never got access, check **People → Pending invitations** first.

**Using personal PATs for CI/CD automation.** If the person who created the token leaves the organisation or rotates their token, all automated pipelines that use it break. Use deploy keys for repository-level automation or GitHub Apps for organisation-level automation.

---

## Summary

- **Collaborators** on personal repos get Read, Triage, Write, Maintain or Admin; Write is correct for most contributors
- **Deploy keys** give a single repository SSH access for automation without a user account
- **Organisations** are shared accounts better than personal repos for any group work; they persist beyond any individual
- **Outside collaborators** give specific repository access to non-members; correct for contractors and external contributors
- **Organisation roles:** Owner (full control), Member (team-based access), Billing Manager (billing only), Security Manager (security alerts), Outside Collaborator (specific repos only)
- **Teams** group members and grant repository access in bulk; adding someone to a team gives them all the team's repository permissions
- **Nested teams** inherit parent team permissions; useful for hierarchical organisations
- **CODEOWNERS** auto-requests the right reviewer based on which files changed; combine with required code owner reviews in branch protection
- **Base permissions** should be None or Read for most organisations; manage actual access through teams
- **Require 2FA** for all organisation members in any security-sensitive environment
- **Organisation-level Actions settings** control which actions workflows can use; restrict to local and approved actions
- **Audit log** records every significant action; export it for compliance reporting
- **Always have at least two organisation owners** to avoid lock-out

---

## Sources and Further Reading

- [Managing access to your organisation's repositories](https://docs.github.com/en/organizations/managing-user-access-to-your-organizations-repositories) - official GitHub docs
- [About teams](https://docs.github.com/en/organizations/organizing-members-into-teams/about-teams) - teams overview and nested teams
- [About CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) - complete CODEOWNERS syntax reference
- [Managing deploy keys](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/managing-deploy-keys) - deploy keys vs machine users
- [About outside collaborators](https://docs.github.com/en/organizations/managing-user-access-to-your-organizations-repositories/managing-outside-collaborators/about-outside-collaborators) - outside collaborator permissions
- [Organisation audit log](https://docs.github.com/en/organizations/keeping-your-organization-secure/managing-security-settings-for-your-organization/reviewing-the-audit-log-for-your-organization) - audit log events and search
- [Verifying your organisation's domain](https://docs.github.com/en/organizations/managing-organization-settings/verifying-or-approving-a-domain-for-your-organization) - domain verification steps
- [About GitHub Enterprise Managed Users](https://docs.github.com/en/enterprise-cloud@latest/admin/identity-and-access-management/understanding-iam-for-enterprises/about-enterprise-managed-users) - EMU overview
- [GitHub pricing](https://github.com/pricing) - Free vs Team vs Enterprise plan comparison

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

# Collaborators, Teams and Organisations

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

When you are working with other people on GitHub, you need to understand three things: how to add individual collaborators to a repository, how teams work within an organisation and how organisations themselves are structured. This file covers all three, including permission levels, team management, organisation settings and practical patterns for student groups, open source projects and professional teams.

---

## Table of Contents

- [Personal repositories vs organisation repositories](#personal-repositories-vs-organisation-repositories)
- [Adding collaborators to a personal repository](#adding-collaborators-to-a-personal-repository)
- [Permission levels for collaborators](#permission-levels-for-collaborators)
- [What is a GitHub organisation](#what-is-a-github-organisation)
- [Creating an organisation](#creating-an-organisation)
- [Inviting members to an organisation](#inviting-members-to-an-organisation)
- [Organisation roles](#organisation-roles)
- [Teams](#teams)
- [Creating and managing teams](#creating-and-managing-teams)
- [Team permissions on repositories](#team-permissions-on-repositories)
- [Nested teams](#nested-teams)
- [CODEOWNERS](#codeowners)
- [Organisation-level settings worth knowing](#organisation-level-settings-worth-knowing)
- [Practical patterns](#practical-patterns)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Personal Repositories vs Organisation Repositories

**Personal repositories** live under your user account at `github.com/YOUR_USERNAME/repo`. You own them. You can add collaborators, but the repository is tied to you as an individual.

**Organisation repositories** live under an organisation at `github.com/ORG_NAME/repo`. The organisation owns them. Multiple people can be admins. Repositories persist even if the original creator leaves. This makes organisations better for teams, companies and open source projects.

---

## Adding Collaborators to a Personal Repository

For small projects or working with one or two people, adding direct collaborators to a personal repository is the simplest option.

**Step 1.** Go to your repository on GitHub.

**Step 2.** Click **Settings** (the gear icon, in the top tab bar).

**Step 3.** In the left sidebar, click **Collaborators** (or **Collaborators and teams** if you see that label).

**Step 4.** Click **Add people**.

**Step 5.** Type the collaborator's GitHub username or email address.

**Step 6.** Select the permission level (see below).

**Step 7.** Click **Add NAME to this repository**.

The collaborator receives an email invitation. They must accept it before they can access the repository. Pending invitations expire after 7 days.

---

## Permission Levels for Collaborators

| Level | What they can do |
|---|---|
| **Read** | View and clone the repository. Open issues and PRs. Cannot push. |
| **Triage** | Read plus: manage issues and PRs (label, assign, close, reopen). Cannot push. |
| **Write** | Triage plus: push to non-protected branches, manage releases, edit the wiki. |
| **Maintain** | Write plus: manage topics, archive the repo, manage webhooks. Cannot delete. |
| **Admin** | Full control including repository settings, branch protection and deletion. |

For most collaborators on a personal project, **Write** is appropriate. Use **Admin** only for people you trust completely with the repository settings.

---

## What is a GitHub Organisation

A GitHub organisation is a shared account that groups repositories and people. Organisations have:

- Their own profile page at `github.com/ORG_NAME`
- Their own repositories (not tied to any individual account)
- A member roster with defined roles
- Teams for grouping members and granting repository access
- Organisation-wide settings for security, Actions and billing

Organisations are free for public repositories. For private repositories and advanced features, organisations need a GitHub Team ($4/user/month) or Enterprise plan. Students with the Student Developer Pack get the Team plan free for their organisations.

---

## Creating an Organisation

**Step 1.** Click the **+** icon in the top right of any GitHub page.

**Step 2.** Click **New organisation**.

**Step 3.** Choose a plan. Select **Free** to start.

**Step 4.** Enter an organisation name. This becomes part of all URLs for the organisation's repositories (`github.com/YOUR_ORG`). Choose carefully - it can be changed but it breaks existing URLs.

**Step 5.** Enter a contact email address.

**Step 6.** Indicate whether this is a personal or business organisation.

**Step 7.** Click **Next** and complete the setup.

You are now the owner of the organisation. Invite members to join.

---

## Inviting Members to an Organisation

**Step 1.** Go to your organisation's page at `github.com/ORG_NAME`.

**Step 2.** Click the **People** tab.

**Step 3.** Click **Invite member**.

**Step 4.** Type the GitHub username or email address of the person you want to invite.

**Step 5.** Choose their role: **Member** or **Owner**.

**Step 6.** Click **Send invitation**.

The invited person receives an email and must accept. Once accepted, they appear in the People list.

**Removing a member:**

Go to **People**, find the member and click the three-dot menu next to their name, then **Remove from organisation**. Their forked repositories and issues remain, but they lose access to private repositories.

---

## Organisation Roles

| Role | Capabilities |
|---|---|
| **Member** | Access to repositories they are given permission for. Can create repositories if allowed by org settings. |
| **Owner** | Full admin access to the entire organisation - members, teams, settings, billing, repository management. |
| **Billing manager** | Can only manage billing settings. No repository access. |
| **Security manager** | Can manage security alerts and settings across all repositories. |
| **Outside collaborator** | Not a member of the organisation but has access to specific repositories. Used for contractors and external contributors. |

Every organisation should have at least two owners to avoid being locked out if one person loses account access.

---

## Teams

Teams are groups of organisation members. You grant repository access to a team, and every member of that team inherits that access. This is far more manageable than granting access to individuals one by one.

**Example:** A team called `frontend` has 8 members. Grant `frontend` Write access to the `website` repository. All 8 members can now push to `website`. If a new developer joins the frontend team, they automatically inherit access. If someone leaves, removing them from the team revokes access to all team repositories.

Teams can also be mentioned in issues and PRs with `@ORG/TEAM_NAME`, which notifies all team members.

---

## Creating and Managing Teams

**Create a team:**

1. Go to your organisation page
2. Click the **Teams** tab
3. Click **New team**
4. Enter a team name and optional description
5. Set visibility: **Visible** (anyone in the org can see the team and its members) or **Secret** (only team members and owners can see it)
6. Click **Create team**

**Add members to a team:**

1. Go to the team page (click the team name from the Teams tab)
2. Click **Members**
3. Click **Add a member**
4. Search by username and add them

**Team maintainers:**

Each team has a role of **Team maintainer** which allows a member to manage the team's membership without being an organisation owner. Assign this to team leads.

---

## Team Permissions on Repositories

**Grant a team access to a repository:**

1. Go to the team page
2. Click **Repositories**
3. Click **Add repository**
4. Search for the repository name and select it
5. Choose the permission level: Read, Triage, Write, Maintain or Admin

Or do it from the repository side:

1. Go to the repository
2. Click **Settings → Collaborators and teams**
3. Under **Teams**, click **Add teams**
4. Search for the team and select a permission level

---

## Nested Teams

Teams can have parent teams. A child team inherits the repository permissions of its parent.

**Example structure:**

```
engineering (Write to all engineering repos)
├── frontend (Write to website + app)
├── backend (Write to api + services)
└── devops (Admin to infrastructure)
```

A member of `frontend` gets all the permissions of `frontend` but not automatically those of `backend` or `devops`. A member of `engineering` gets Write access to all engineering repos.

Nested teams are useful for large organisations where different sub-teams share some permissions but not others.

---

## CODEOWNERS

The `CODEOWNERS` file defines which people or teams are automatically requested for review when a PR changes files they own. This ensures the right person always reviews the right code.

**Create the file** at one of:

- `CODEOWNERS`
- `.github/CODEOWNERS`
- `docs/CODEOWNERS`

**Syntax:**

```
# Pattern                Owner(s)
*.js                     @ORG/frontend-team
*.py                     @ORG/backend-team
/docs/                   @ORG/technical-writers @isaacadjei
/infrastructure/         @ORG/devops-team
README.md                @isaacadjei
```

Patterns follow the same rules as `.gitignore`. Owners can be individual GitHub usernames (`@username`) or teams (`@ORG/team-name`).

When a PR modifies `*.js` files, GitHub automatically adds `@ORG/frontend-team` as a required reviewer (if branch protection is configured to require review from code owners).

**Enable code owner reviews in branch protection:**

Go to **Settings → Branches → Branch protection rules** and tick **Require review from Code Owners**.

---

## Organisation-Level Settings Worth Knowing

Go to **Settings** on your organisation page to access these.

**Member privileges:**

- **Base permissions** - the minimum permission every member has on all repositories (None, Read, Write or Admin). For most orgs, set this to Read or None and manage access explicitly through teams
- **Repository creation** - whether members can create public, private or both types of repositories
- **Repository forking** - whether members can fork private repositories
- **Repository deletion** - whether members can delete repositories

**Two-factor authentication:**

Go to **Authentication security** to require 2FA for all organisation members. Members who have not enabled 2FA will be removed from the organisation (but not GitHub itself) until they enable it.

**Verified domain:**

Organisations can verify a domain (e.g. `yourcompany.com`) to restrict membership invitations to email addresses on that domain.

**Audit log:**

The organisation audit log records every significant action taken by members and owners - repository access changes, member additions/removals, settings changes. Available under **Settings → Audit log**.

---

## Practical Patterns

**Pattern 1 - Student group project:**

- Create an organisation named after your project or module
- Add all team members as members of the organisation
- Create a single repository in the organisation
- All members get Write access via a team called `contributors`
- Add one or two people as organisation owners

**Pattern 2 - Open source project:**

- Core team gets Write or Maintain
- Outside contributors fork and submit PRs (they never need repository write access)
- Use a `CODEOWNERS` file to auto-request reviews
- Use branch protection on `main` - require at least one approval before merging

**Pattern 3 - Company/professional:**

- Create teams per department or sub-team
- Use nested teams for hierarchical permissions
- Require 2FA for all members
- Enable required code owner reviews on protected branches
- Set base permissions to None and manage all access explicitly

---

## Try It Yourself

**Step 1.** Create a free GitHub organisation:

1. Click **+** → **New organisation** → **Free**
2. Name it `git-unlocked-practice` or something similar
3. Complete the setup

**Step 2.** Create a repository inside the organisation:

1. From the organisation page, click **New repository**
2. Name it `practice-repo`
3. Initialise with a README

**Step 3.** Create a team:

1. Go to the **Teams** tab in the organisation
2. Create a team called `contributors`
3. Add yourself as a member

**Step 4.** Give the team access:

1. Go to the team's **Repositories** tab
2. Add `practice-repo` with **Write** permission

**Step 5.** Create a CODEOWNERS file:

1. In `practice-repo`, create `.github/CODEOWNERS`
2. Add: `README.md @YOUR_USERNAME`
3. Commit it

---

## Common Mistakes

**Adding everyone as Admin.** Admins can delete the repository and change all settings. Most collaborators only need Write. Reserve Admin for people who genuinely need it.

**Using personal repositories for team projects.** If the owner leaves or changes their username, all URLs break. Use an organisation for anything involving a group.

**Forgetting outside collaborators.** If you want to give a contractor access to one specific repository without making them an organisation member, use the Outside Collaborator role rather than full membership.

**Not setting base permissions.** By default, organisation members may have Read access to all repositories. If your organisation has sensitive private repositories, set base permissions to None and manage access explicitly through teams.

**Inviting people with incorrect permissions.** An invitation expired? They never accepted it? Check **People → Pending invitations** on the organisation page.

---

## Summary

- **Collaborators** are individuals added to a personal repository with Read, Triage, Write, Maintain or Admin permissions
- **Organisations** are shared accounts that group repositories and people; better than personal repos for any team work
- **Teams** group organisation members; granting a team repository access is more maintainable than individual grants
- **Nested teams** inherit parent team permissions - useful for large organisations
- **CODEOWNERS** automatically requests the right reviewer when specific files are changed
- Set base permissions to **None** or **Read** and manage detailed access through teams
- Require **2FA** for all organisation members in security-sensitive environments
- Every organisation should have at least **two owners**

---

## Sources and Further Reading

- [Managing access to your organisation's repositories](https://docs.github.com/en/organisations/managing-user-access-to-your-organizations-repositories) - official GitHub docs
- [Creating a team](https://docs.github.com/en/organisations/organising-members-into-teams/creating-a-team) - team setup guide
- [About CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customising-your-repository/about-code-owners) - CODEOWNERS syntax reference
- [Organisation audit log](https://docs.github.com/en/organisations/keeping-your-organisation-secure/managing-security-settings-for-your-organisation/reviewing-the-audit-log-for-your-organisation) - understanding the audit log
- [GitHub Team plan](https://github.com/pricing) - pricing for private organisation repositories

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

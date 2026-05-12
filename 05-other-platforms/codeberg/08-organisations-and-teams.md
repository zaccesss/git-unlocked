# Organisations and Teams on Codeberg

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Codeberg organisations provide a shared namespace for teams and projects. An organisation can own repositories, have members with different permission levels and manage access through teams. This is the standard model for open source projects maintained by multiple people: the project lives under an organisation name rather than an individual's account, making it resilient to maintainer turnover and easier to manage collaboratively.

---

## Table of Contents

1. [Creating an organisation](#creating-an-organisation)
2. [Organisation settings](#organisation-settings)
3. [Members and roles](#members-and-roles)
4. [Teams](#teams)
5. [Organisation repositories](#organisation-repositories)
6. [Forks and contributions](#forks-and-contributions)
7. [Organisation-level features](#organisation-level-features)
8. [Considerations for Codeberg organisations](#considerations-for-codeberg-organisations)
9. [Try It Yourself](#try-it-yourself)
10. [Common Mistakes](#common-mistakes)
11. [Summary](#summary)
12. [Sources](#sources)

---

## Creating an organisation

1. Click **+** in the top navigation > **New Organisation**
2. Fill in:
   - **Organisation name**: the URL slug (`codeberg.org/org-name`). Choose carefully - renaming later breaks all repository URLs.
   - **Visibility**: Public (anyone can see the org and its public repos) or Private
3. Click **Create Organisation**

You are automatically made the organisation owner.

### Organisation name guidelines

- Must be unique across all Codeberg usernames and organisation names
- Letters, numbers, hyphens and underscores only
- Cannot start with a hyphen
- Becomes your federated identity: `@org-name@codeberg.org`

---

## Organisation settings

Access from the organisation profile > **Settings** (visible to owners and admins only).

### General settings

- **Organisation name**: rename (breaks all existing URLs - do this early, not after the org is established)
- **Display name**: a human-readable name shown in the interface (separate from the URL slug)
- **Website**: link to the project's external site
- **Description**: shown on the organisation profile page
- **Visibility**: Public or Private

### Default repository settings

Configure defaults for new repositories created under the organisation:

- Default visibility (public or private)
- Default branch name

### Webhooks

Organisation-level webhooks fire for events across all repositories in the organisation. Useful for:

- Posting all repository activity to a team chat
- Triggering organisation-wide CI or monitoring

### Danger zone

- **Transfer organisation**: move to a different owner. The organisation URL does not change, but the owner account does.
- **Delete organisation**: permanently deletes the organisation. All repositories must be transferred or deleted first.

---

## Members and roles

### Adding members

1. Go to the organisation profile > **Members**
2. Click **Invite member** (or **Add team member** depending on the context)
3. Enter the username of the person to invite
4. They receive an invitation notification

### Organisation roles

| Role       | Permissions                                              |
| ---------- | -------------------------------------------------------- |
| **Owner**  | Full control: manage members, teams, settings, all repos |
| **Member** | Access based on team membership only                     |

Owners can do everything. Members' actual permissions are determined by the teams they belong to.

### Ownership transfer

For bus factor resilience, ensure at least two people have owner access. If a project has a single owner who becomes unavailable, recovering ownership is difficult. Add a trusted co-maintainer as owner.

To add an owner:

1. Go to **Members**
2. Find the member
3. Change their role to Owner

---

## Teams

Teams are named groups of members with specific permissions applied to specific repositories. They are the mechanism for granular access control within an organisation.

### Default teams

When you create an organisation, Codeberg creates a default **Owners** team with all permissions. You are automatically in this team.

### Creating a team

1. Go to organisation > **Teams** > **New Team**
2. Configure:

**Team name**: a descriptive name (e.g. `maintainers`, `contributors`, `ci-bots`).

**Permission level**:

- **None**: no access to any repository unless specifically granted
- **Read**: clone and view all team repositories
- **Write**: read + push code, create branches, create PRs
- **Admin**: write + manage repository settings and branch protection

**Repository access**:

- **All repositories**: the team automatically has access to every repository in the organisation, including future ones
- **Specific repositories**: explicitly add repositories to the team

3. Add members to the team
4. Add repositories the team can access

### Common team structures

**For a small open source project (2-5 maintainers):**

```
Owners team: all maintainers (Write + Admin)
```

**For a larger project:**

```
Maintainers team: core maintainers (Admin permission, all repos)
Contributors team: active contributors (Write permission, main repos)
Bots team: CI service accounts (Write permission, specific repos)
Docs team: documentation contributors (Write permission, docs repo only)
```

**For an organisation with multiple sub-projects:**

```
Core team: all repos, Admin
Project A team: project-a/* repos, Write
Project B team: project-b/* repos, Write
Infrastructure team: infra repos, Admin
```

### Service account teams

CI systems (Woodpecker, ArgoCD) need access tokens with repository write permission. Instead of using a human's token, create a dedicated bot account on Codeberg, add it to a `ci-bots` team with appropriate permissions and use that account's token in CI secrets.

This way:

- CI tokens are not tied to a specific person's account
- The bot account can be managed separately
- Revoking CI access does not affect any human's account

---

## Organisation repositories

### Creating a repository under an organisation

When creating a new repository, change the **Owner** dropdown from your username to the organisation name. All other settings are identical.

```bash
# Clone an organisation repository
git clone git@codeberg.org:org-name/repo-name.git

# API: create a repository under an org
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "my-project", "private": false, "auto_init": true}' \
  "https://codeberg.org/api/v1/orgs/org-name/repos"
```

### Transferring existing repositories to an organisation

Move a repository from your personal account to an organisation:

1. Repository **Settings** > **Danger Zone** > **Transfer**
2. Enter the organisation name as the new owner
3. Type the repository name to confirm
4. Click **Transfer**

All existing Git remotes break after transfer. Update with:

```bash
git remote set-url origin git@codeberg.org:org-name/repo-name.git
```

### Repository visibility in organisations

A repository in an organisation can be:

- **Public**: visible to anyone
- **Private**: visible only to organisation members with access (requires Codeberg financial support for orgs on the free tier)
- **Internal**: visible to all authenticated Codeberg users (if enabled by the instance)

---

## Forks and contributions

### External contributor workflow

Contributors without organisation membership fork the repository, make changes and submit PRs:

1. Contributor visits the organisation repository
2. Clicks **Fork** to create a copy under their own account
3. Pushes changes to their fork
4. Opens a PR from their fork to the organisation's repository
5. Maintainers review and merge

This is the standard open source model. Contributors need no special permissions - anyone with a Codeberg account can fork a public repository and submit a PR.

### Maintaining the contributor funnel

For healthy open source projects, actively maintain the contribution pathway:

- Keep `CONTRIBUTING.md` current
- Label issues `good first issue` for newcomers
- Respond to PRs promptly (even with "we will look at this next week")
- Credit contributors in release notes
- Welcome new contributors explicitly in PR comments

### Organisation project boards

Create project boards at the organisation level (visible across all repositories) for high-level roadmap tracking:

1. Organisation profile > **Projects** > **New Project**
2. Add issues from any repository in the organisation
3. Share the board URL with contributors and stakeholders

---

## Organisation-level features

### Shared labels

Labels created at the organisation level appear in all repositories within the organisation. Create your standard label set once and it is available everywhere:

1. Organisation > **Issues** > **Labels**
2. Create labels

These propagate to new repositories automatically.

### Organisation-level webhooks

Fire across all repositories in the organisation:

1. Organisation **Settings** > **Webhooks** > **Add Webhook**
2. Configure the endpoint and events

### Organisation packages

Packages published to an organisation namespace (`/api/packages/org-name/...`) are accessible to all repositories in the organisation. Useful for internal shared packages:

```bash
# Publish to org namespace
curl -X PUT \
  -H "Authorization: token YOUR_TOKEN" \
  --upload-file internal-lib-1.0.0.tar.gz \
  "https://codeberg.org/api/packages/org-name/generic/internal-lib/1.0.0/internal-lib-1.0.0.tar.gz"
```

### Organisation secrets in Woodpecker CI

Secrets defined at the organisation level in Woodpecker CI are available to all repositories in the organisation. Set organisation-wide secrets once (deploy keys, shared API tokens) rather than duplicating them per repository:

1. In Woodpecker CI, go to the organisation
2. **Settings** > **Secrets** > **Add secret**
3. Mark as available to all repositories in the org

---

## Considerations for Codeberg organisations

### Private repositories require financial support

Free-tier organisations cannot create private repositories. If your project has sensitive code, internal documentation or pre-release work that needs to be private before public release, either:

- Use a personal account with private repositories
- Support Codeberg financially (the funds go directly to keeping the platform running)
- Consider whether the content truly needs to be private on a code hosting platform

### Bot accounts and multiple owners

Codeberg terms of service allow service accounts (bot accounts) for CI and automation. Create a dedicated account (e.g. `myproject-bot`), add it to a restricted-permission team and use its token in CI systems. Do not share human account credentials with CI systems.

### Organisation name and federated identity

Your organisation name becomes an ActivityPub identity: `@org-name@codeberg.org`. This is discoverable across the Fediverse. Organisation names with dashes or unusual characters work but may be awkward in federated contexts. Choose a clean, recognisable name.

---

## Try It Yourself

**Exercise 1 - Create an organisation**

1. Click **+** > **New Organisation**
2. Name it with your username + `-projects` (e.g. `zaccesss-projects`)
3. Leave visibility as Public
4. Click **Create Organisation**

**Exercise 2 - Create teams**

1. In your new organisation, click **Teams**
2. Create a team named `maintainers` with Admin permission on all repositories
3. Add yourself as a member
4. Create a team named `contributors` with Write permission

**Exercise 3 - Transfer a repository to the org**

1. Go to your `hello-codeberg` repository
2. Settings > Transfer > enter your organisation name
3. Type the repo name to confirm and transfer
4. Update your local remote:

```bash
cd hello-codeberg
git remote set-url origin git@codeberg.org:your-org-name/hello-codeberg.git
git push
```

**Exercise 4 - Invite a collaborator**

1. Go to your organisation > **Members**
2. Invite a friend or colleague (or create a second test account)
3. Add them to the `contributors` team
4. Verify they can see the repositories

---

## Common Mistakes

**Using a single owner for a long-term project**

Projects with one owner are fragile. If the owner becomes unavailable, nobody can manage the organisation. Add at least one other trusted person as Owner from the beginning.

**Giving all contributors Admin permission**

Admin permission allows changing branch protection rules, repository settings and webhooks. Most contributors only need Write permission. Reserve Admin for maintainers who actively manage the project infrastructure.

**Using a personal access token for CI instead of a bot account**

If a maintainer generates a personal token for CI and later leaves the project, their token needs to be revoked and all CI systems updated. A dedicated bot account's token can be transferred or regenerated independently.

**Transferring repositories without updating all references**

After transferring a repository to an organisation, all git remotes, CI configurations, documentation links and webhook URLs pointing to the old personal URL break. Make a list before transferring and update everything systematically.

**Creating an organisation before the name is confirmed**

Organisation names cannot be changed without breaking all repository URLs. If the project or team name is not yet settled, wait. Use a personal account in the meantime.

---

## Summary

Codeberg organisations provide a shared namespace for teams, repositories and packages. Create an organisation with a permanent URL-slug name; add members as Owners or regular Members; structure access through teams with specific permission levels (Read, Write, Admin) applied to specific repositories.

The standard contribution model for open source is the fork-and-PR workflow: external contributors fork, push and submit PRs without needing organisation membership. Internal contributors get Write access through team membership.

Organisation-level features include shared labels, webhooks across all repositories, organisation-scoped packages and (via Woodpecker CI) organisation-level secrets available to all pipelines.

Free-tier organisations cannot have private repositories. Dedicated bot accounts for CI automation are allowed and recommended over sharing human access tokens with automated systems.

---

## Sources

- [Forgejo: Organisations documentation](https://forgejo.org/docs/latest/user/organisations/)
- [Forgejo: Teams](https://forgejo.org/docs/latest/user/team/)
- [Codeberg: Community wiki](https://codeberg.org/Codeberg/Community/wiki)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

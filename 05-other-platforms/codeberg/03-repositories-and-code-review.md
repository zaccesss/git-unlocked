# Repositories and Code Review on Codeberg

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Codeberg repositories and pull requests work identically to Forgejo - the same creation flow, the same pull request interface, the same branch protection model. This file focuses on the Codeberg-specific aspects: importing from GitHub, the Codeberg-specific URLs and clone patterns, and considerations specific to the hosted service. For the complete code review and protected branches reference, see [../forgejo/04-repositories-and-code-review.md](../forgejo/04-repositories-and-code-review.md).

---

## Table of Contents

1. [Creating a repository](#creating-a-repository)
2. [Cloning and pushing](#cloning-and-pushing)
3. [Importing from GitHub and other platforms](#importing-from-github-and-other-platforms)
4. [Pull requests and code review](#pull-requests-and-code-review)
5. [Branch protection](#branch-protection)
6. [Repository topics and discoverability](#repository-topics-and-discoverability)
7. [Wikis](#wikis)
8. [Codeberg-specific repository limits](#codeberg-specific-repository-limits)
9. [Try It Yourself](#try-it-yourself)
10. [Common Mistakes](#common-mistakes)
11. [Summary](#summary)
12. [Sources](#sources)

---

## Creating a repository

### Via the web interface

1. Click **+** in the top navigation > **New Repository**
2. Set:
   - **Owner**: your account or an organisation you belong to
   - **Repository name**: appears in all URLs (`codeberg.org/owner/name`)
   - **Visibility**: Public (anyone can view) or Private
   - **Initialise with**: README, `.gitignore` template, licence
   - **Default branch**: `main` recommended
3. Click **Create Repository**

For open source projects, choose **Public** and select a licence. Codeberg's community norms favour copyleft or permissive free software licences.

### Via the API

```bash
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-project",
    "description": "My open source project",
    "private": false,
    "auto_init": true,
    "default_branch": "main",
    "license": "GPL-3.0"
  }' \
  https://codeberg.org/api/v1/user/repos
```

### Pushing an existing repository

```bash
cd my-existing-project
git remote add codeberg https://codeberg.org/username/my-project.git
git push codeberg main

# Or if migrating from GitHub:
git remote add codeberg https://codeberg.org/username/my-project.git
git push --mirror codeberg
```

---

## Cloning and pushing

### HTTPS clone

```bash
git clone https://codeberg.org/username/repository.git
# Username: your Codeberg username
# Password: your access token
```

### SSH clone

```bash
git clone git@codeberg.org:username/repository.git
```

Requires SSH public key added to your account (Settings > SSH / GPG Keys).

### Codeberg clone URL format

Codeberg SSH uses the standard format:

```
git@codeberg.org:OWNER/REPO.git
```

Note the colon (`:`) between the host and owner - this is standard SSH Git syntax, identical to GitHub's format.

### Credential storage

🪟 **Windows**: Git Credential Manager (included with Git for Windows) stores credentials automatically after first use.

🍎 **Mac**: Git Credential Manager stores in macOS Keychain automatically.

🐧 **Linux**:

```bash
# Cache in memory for 1 hour
git config --global credential.helper 'cache --timeout=3600'
```

---

## Importing from GitHub and other platforms

Codeberg makes it straightforward to bring existing projects from GitHub, GitLab and other platforms.

### Migrate from GitHub (with issues and PRs)

This is the recommended path for a full migration - code, issues, pull requests, labels and milestones:

1. Click **+** > **New Migration** > **GitHub**
2. Enter the GitHub repository URL: `https://github.com/username/repo`
3. For private repositories, enter a GitHub personal access token
4. Configure what to migrate:
   - **Migrate repository**: Yes (always)
   - **Migrate wiki**: optional
   - **Migrate labels**: recommended
   - **Migrate milestones**: recommended
   - **Migrate issues**: recommended
   - **Migrate pull requests**: recommended
   - **Migrate releases**: recommended
5. Set the Codeberg repository name and visibility
6. Click **Migrate Repository**

Codeberg maps GitHub issues and PRs to Forgejo issues and pull requests. Assignees are mapped where usernames match. Issues from users without Codeberg accounts are attributed to a generic account with the original username in the description.

### Migrate via git push --mirror

For just the code history (no issues/PRs):

```bash
# Clone the source repository as a bare mirror
git clone --mirror https://github.com/username/repo.git

# Enter the mirrored repo
cd repo.git

# Push everything to Codeberg
git remote add codeberg https://codeberg.org/username/repo.git
git push --mirror codeberg
```

This transfers all branches, tags and the full commit history.

### Set up a mirror (ongoing sync)

Keep a Codeberg repository in sync with an upstream GitHub repository:

1. **+** > **New Migration** > **GitHub**
2. Enable **Mirror**: Codeberg periodically pulls from GitHub
3. The Codeberg repository is read-only (you cannot push to it)

Use this for:

- Mirroring popular open source projects for air-gapped access
- Keeping a Codeberg copy of a project that primarily lives on GitHub
- Providing fast European access to US-hosted repositories

---

## Pull requests and code review

The pull request workflow on Codeberg is identical to Forgejo and very similar to GitHub:

### Creating a PR

After pushing a branch, Codeberg shows a banner suggesting you create a PR. Click it, or go to **Pull Requests** > **New Pull Request**.

Fill in:

- **Title**: what the change does
- **Description**: why it is needed and how to test it (Markdown supported)
- **Reviewers**, **Assignees**, **Labels**, **Milestone**

### Code review

In the **Files Changed** tab:

- Hover over any line and click **+** to add an inline comment
- Drag across multiple lines to comment on a block
- Add code suggestions (changes the reviewer can apply with one click) using the suggestion syntax in a fenced code block marked `suggestion`

Submit a review with **Approve**, **Request changes** or **Comment** from the **Review changes** button.

### Merge strategies

Codeberg supports three merge strategies (configure which are available in repository settings):

- **Merge commit**: preserves full branch history
- **Squash and merge**: one commit per PR on the target branch
- **Rebase and merge**: replays commits linearly

### PR templates

Create `.forgejo/PULL_REQUEST_TEMPLATE.md` in your repository to pre-fill PR descriptions:

```markdown
## Summary

<!-- What does this PR do? -->

## Checklist

- [ ] Tests added or updated
- [ ] Documentation updated
- [ ] Tested locally

## Related issues

<!-- Closes #123 -->
```

---

## Branch protection

Protect your main branch from direct pushes and enforce review requirements.

### Setting up branch protection

Repository **Settings** > **Protected Branches** > enter `main`:

- **Enable push protection**: no direct pushes, all changes via PRs
- **Required approvals**: minimum number of approvals before merging
- **Dismiss stale approvals**: approvals reset on new commits
- **Required status checks**: CI must pass before merging
- **Restrict force push**: prevent rewriting history

Example configuration for a well-managed open source project:

```
Branch: main
Push protection: enabled
Required approvals: 1
Dismiss stale approvals: enabled
```

This ensures every change to main has been seen by at least one other contributor.

---

## Repository topics and discoverability

Topics are searchable tags that help people find your project on Codeberg.

### Adding topics

On your repository page, click the gear icon next to **Topics** and add relevant tags:

- Language: `python`, `go`, `rust`, `typescript`
- Category: `library`, `cli`, `web`, `desktop`, `mobile`
- Domain: `machine-learning`, `cryptography`, `devtools`, `embedded`

Codeberg's **Explore** page allows filtering by topic, so well-tagged projects are easier to discover.

### Discoverability limitations

Codeberg's explore and trending features are more limited than GitHub's. Projects on Codeberg generally get less organic discovery than equivalent projects on GitHub. If you need broad visibility, consider maintaining a GitHub mirror (using Codeberg's push mirror feature to keep them in sync) while keeping Codeberg as the primary development home.

---

## Wikis

Each repository can have a wiki for documentation.

### Enabling and using the wiki

Wikis are enabled by default. Click **Wiki** in the repository navigation.

Create pages in Markdown with internal links between pages:

```markdown
See the [[Installation Guide]] for setup instructions.
```

### The wiki as a Git repository

The wiki is a separate Git repository you can clone:

```bash
git clone https://codeberg.org/username/repository.wiki.git
```

This allows bulk editing, offline writing and migration from other wiki systems.

---

## Codeberg-specific repository limits

Codeberg operates on fair use principles rather than hard limits. Current considerations:

**Repository size**: Very large repositories (hundreds of GiB of Git history) are discouraged. Codeberg's infrastructure is donation-funded; extremely large repositories consume shared resources.

**LFS storage**: Git LFS works on Codeberg. Limit LFS usage to genuinely binary files that do not diff (images, compiled assets, datasets). Excessive LFS storage is a shared resource issue.

**Private repositories**: Personal accounts can have private repositories. Organisation private repositories require the organisation to support Codeberg financially.

**Forks**: Forking public repositories is unlimited and encouraged - it is core to the open source workflow.

Check the [Codeberg Community wiki](https://codeberg.org/Codeberg/Community/wiki) for current policies, as these evolve.

---

## Try It Yourself

**Exercise 1 - Create a public repository**

1. Click **+** > **New Repository**
2. Name: `hello-codeberg`
3. Visibility: Public
4. Initialise with README and MIT licence
5. Add topics: your language of choice and `hello-world`

**Exercise 2 - Clone and push**

```bash
git clone git@codeberg.org:your-username/hello-codeberg.git
cd hello-codeberg
echo "## Getting Started" >> README.md
git add README.md
git commit -m "Add getting started section"
git push
```

**Exercise 3 - Create a pull request**

```bash
git checkout -b feature/add-contributing-guide
echo "# Contributing\n\nWe welcome contributions!" > CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "Add contributing guide"
git push origin feature/add-contributing-guide
```

Go to the repository on Codeberg and create a PR from the banner that appears.

**Exercise 4 - Import from GitHub**

If you have a GitHub repository to migrate:

1. **+** > **New Migration** > **GitHub**
2. Enter the repository URL
3. Enable migration of issues, PRs and releases
4. Complete the migration and verify the content transferred correctly

---

## Common Mistakes

**Pushing sensitive data to a public repository**

Public repositories on Codeberg are publicly readable by anyone. API keys, passwords, private keys and personal data in a public repository are compromised immediately. Always verify repository visibility before pushing.

**Not using SSH for regular development**

HTTPS requires entering credentials for every push (unless a credential manager is configured). SSH is zero-friction after initial setup. Set up SSH keys before starting development.

**Expecting GitHub-identical import**

Issue migration maps GitHub assignees to Codeberg accounts by username. If your contributors do not have Codeberg accounts with the same usernames, their issues and PR comments are attributed to a generic account. The content is preserved, but authorship links break. Mention this to your contributors when migrating.

**Creating a private organisation repository on the free tier**

Organisation private repositories require financial support of Codeberg. Attempting to create a private org repo without this results in an error. Personal private repositories work without restrictions.

---

## Summary

Codeberg repositories work identically to Forgejo repositories. Create public repositories for open source projects, initialise with a README and a licence and push with SSH for the best experience.

Import existing projects from GitHub via **+ > New Migration > GitHub** to bring over code, issues, PRs, labels, milestones and releases. For code-only migrations, use `git push --mirror`. Set up pull mirrors to keep a Codeberg repository in sync with an upstream source.

Pull requests, code review, branch protection and PR templates all work the same as Forgejo. The key branch protection options are push protection, required approvals and required status checks.

Repository topics improve discoverability on Codeberg's Explore page. Organisation private repositories require financial support of Codeberg; personal private repositories have no such requirement.

---

## Sources

- [Codeberg: Repository documentation](https://codeberg.org/Codeberg/Community/wiki)
- [Forgejo: Repository documentation](https://forgejo.org/docs/latest/user/repos/)
- [Forgejo: Migrations](https://forgejo.org/docs/latest/user/migration/)
- [Forgejo: Pull requests](https://forgejo.org/docs/latest/user/pull-request/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

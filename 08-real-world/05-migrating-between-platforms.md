# Migrating between platforms

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 45 minutes

Moving a repository between Git platforms without losing history, issues, pull requests or LFS objects.

---

## Table of contents

- [Introduction](#introduction)
- [What migrates and what does not](#what-migrates-and-what-does-not)
- [The canonical mirror move](#the-canonical-mirror-move)
- [Git LFS migration](#git-lfs-migration)
- [GitHub to GitLab](#github-to-gitlab)
- [GitLab to Gitea or Forgejo](#gitlab-to-gitea-or-forgejo)
- [GitHub Enterprise Importer](#github-enterprise-importer)
- [Bitbucket migrations](#bitbucket-migrations)
- [Setting up a permanent mirror](#setting-up-a-permanent-mirror)
- [The migration checklist](#the-migration-checklist)
- [War story: the GitLab database outage of 2017](#war-story-the-gitlab-database-outage-of-2017)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Introduction

Migrating a repository between platforms sounds straightforward: clone it, push it somewhere else, done. In practice, it is one of the most data-loss-prone operations in Git work. The Git repository itself transfers cleanly. Everything else - issues, pull requests, merge requests, comments, labels, milestones, webhooks, deploy keys, CI secrets, branch protection rules and Git LFS objects - does not come along automatically, and some of it cannot be moved at all.

This file covers the safe migration sequence for every common platform pair, the Git LFS trap that silently loses file content, and the checklist every migration needs before you delete the old repository.

**What you need:**

- Git installed with LFS support (`git lfs version` to check)
- Access tokens for both the source and destination platforms
- The GitHub CLI (`gh`) for GitHub-involved migrations
- Time - do not rush a migration

---

## What migrates and what does not

Before planning a migration, map out everything your repository relies on. Separate it into what Git carries and what the platform carries.

| Item                           | Travels with Git?                     | Notes                                                        |
| ------------------------------ | ------------------------------------- | ------------------------------------------------------------ |
| Commits, branches, tags        | Yes                                   | `git push --mirror` transfers all of these                   |
| Git notes                      | Yes                                   | Included in `refs/notes/*`                                   |
| Git LFS objects                | No                                    | Separate store - requires explicit transfer                  |
| Issues                         | No                                    | Platform database - use platform importer                    |
| Pull requests / merge requests | No                                    | Platform database - use platform importer                    |
| PR/MR comments                 | No                                    | Platform database - partial via importer                     |
| Labels and milestones          | No                                    | Platform database - via importer                             |
| Releases                       | No                                    | Platform database - via importer                             |
| Wiki                           | Partial                               | Wiki is a separate Git repo - mirror it separately           |
| Webhooks                       | No                                    | Recreate manually or via Terraform/API                       |
| Deploy keys                    | No                                    | Recreate and rotate                                          |
| CI/CD secrets                  | No                                    | Recreate and rotate - never copy old values                  |
| Branch protection / Rulesets   | No                                    | Recreate from documentation                                  |
| CI/CD pipeline definitions     | Yes                                   | `.github/workflows/`, `.gitlab-ci.yml` etc. are files in Git |
| Submodules                     | Partial                               | Pointers transfer; you must update the URLs                  |
| GitHub Actions                 | Yes (definitions) / No (logs, caches) | Workflow files transfer; run history does not                |

> [!CAUTION]
> CI/CD secrets must be rotated, not copied. A secret copied from one platform to another was valid on the old platform and may still be valid. Rotation ensures the old value is invalidated and only the new platform holds a working credential.

---

## The canonical mirror move

`git clone --mirror` combined with `git push --mirror` is the safest and most complete way to transfer Git history. It copies every ref under `refs/*` - branches, tags, notes and remote-tracking refs - not just the default branch.

### Step by step

```bash
# Step 1: mirror-clone the source repository
# 🪟 Windows / 🍎 Mac / 🐧 Linux
git clone --mirror https://source.example.com/user/repo.git
cd repo.git

# Step 2: verify what you have
git show-ref | head -20

# Step 3: create the destination repository on the new platform
# (do this via the web UI or CLI before pushing)

# Step 4: push everything to the destination
git push --mirror https://dest.example.com/user/repo.git

# Step 5: verify the destination has the correct branches and tags
git ls-remote https://dest.example.com/user/repo.git
```

> [!CAUTION]
> `git push --mirror` is destructive. It deletes any refs on the remote that do not exist in your local mirror. Never run it against a repository that already has independent commits or branches - it will destroy them. Only use it against a freshly created empty repository.

> [!NOTE]
> When mirroring from GitHub, you will see errors like `! [remote rejected] refs/pull/1/head -> refs/pull/1/head (deny updating a hidden ref)`. These are harmless. GitHub's pull request refs are read-only and cannot be pushed to another GitHub repository. The error does not affect branches, tags or your actual commits.

### Using SSH instead of HTTPS

For large repositories or those with many refs, SSH is more reliable than HTTPS for the push step:

```bash
git clone --mirror git@source.example.com:user/repo.git
cd repo.git
git push --mirror git@dest.example.com:user/repo.git
```

---

## Git LFS migration

Git LFS is the most common source of silent data loss in platform migrations. LFS objects live in a separate content-addressable store keyed by SHA-256. They are not part of the Git object database and are not transferred by `git push --mirror`.

### The correct LFS migration sequence

```bash
# Step 1: mirror-clone as normal
git clone --mirror https://source.example.com/user/repo.git
cd repo.git

# Step 2: fetch ALL LFS objects from the source
git lfs fetch --all origin

# Step 3: push Git history to destination
git push --mirror https://dest.example.com/user/repo.git

# Step 4: push LFS objects to destination (MUST be a separate step)
git lfs push --all https://dest.example.com/user/repo.git
```

> [!WARNING]
> There is a known issue (git-lfs issue #4899) where `git lfs fetch --all` walks `git rev-list --all` (which includes remote-tracking refs) but `git lfs push --all` only walks local `refs/heads/` and `refs/tags/`. Commits referenced only by remote-tracking refs can silently lose their LFS blobs.
>
> **Mitigation:** before pushing LFS, ensure every commit you care about is reachable from a local branch or tag, not just a remote-tracking ref.

```bash
# Check for commits only reachable via remote-tracking refs
git log --oneline refs/remotes/ ^refs/heads/ ^refs/tags/

# If any appear, create local branches for them
git branch backup/remote-only-branch refs/remotes/origin/some-branch
```

### Checking whether a repository uses LFS

```bash
# Check for LFS pointers in the working tree
git lfs ls-files

# Check the .gitattributes for LFS patterns
cat .gitattributes | grep lfs

# Check if LFS is installed
git lfs version
```

If `git lfs ls-files` returns output, the repository uses LFS and you must follow the LFS migration sequence above.

---

## GitHub to GitLab

GitLab has the strongest built-in importer for GitHub repositories. It transfers significantly more than just Git history.

### What the GitLab importer transfers from GitHub

- Repository (all branches and tags)
- Issues (with labels, milestones, assignees)
- Pull requests as merge requests (with labels, milestones, assignees, review comments)
- Comments on issues and PRs
- Labels
- Milestones
- Releases (GitLab 18.3+)
- Wiki (as a separate Git repository)
- Protected branch rules (GitLab 15.4+)
- Optional: collaborators (as GitLab members)
- Optional: Markdown attachments (images embedded in issues/comments)

### Known limitations

- PR reviewers and approvals become descriptive comments, not GitLab approval records
- PR comments from before 2017 may split into separate discussion threads due to a GitHub API change
- Images attached to private repository issues before 2023-05-09 cannot be imported (GitHub changed the URL format)

### Running the import

1. On GitLab, go to **New project > Import project > GitHub**
2. Authenticate with a GitHub personal access token (scopes needed: `repo`, `read:org`, `read:user`)
3. Select the repositories to import
4. Choose member mapping (map GitHub usernames to GitLab usernames)
5. Start the import

GitLab shows import progress in real time. For large repositories, the import can take hours.

**Via the GitLab API:**

```bash
curl --request POST \
  --url "https://gitlab.com/api/v4/projects" \
  --header "PRIVATE-TOKEN: $GITLAB_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "import_url": "https://github.com/OWNER/REPO",
    "name": "REPO",
    "namespace_id": 12345
  }'
```

Docs: [docs.gitlab.com/user/project/import/github](https://docs.gitlab.com/user/project/import/github/).

### After the import

- Update any CI/CD pipeline files that reference GitHub-specific syntax (e.g. `uses: actions/checkout@v4` becomes GitLab CI `include:` or a native clone step)
- Recreate webhooks pointing to external services
- Recreate deploy keys and rotate the credentials
- Set up GitLab CI/CD variables to replace GitHub Actions secrets
- Update any external services that reference the old GitHub repository URL
- Set up GitLab branch protection and push rules to match your previous GitHub Rulesets

---

## GitLab to Gitea or Forgejo

Gitea and Forgejo both have a built-in migration tool that handles most GitLab content.

### Using the Gitea/Forgejo web importer

1. Go to **+ > New Migration > GitLab**
2. Enter the GitLab instance URL and a personal access token (scopes: `read_api`, `read_repository`)
3. Select what to migrate: repository, issues, pull requests, labels, milestones, releases, wiki, comments, reviews
4. Click **Migrate Repository**

### Using the Gitea API

```bash
curl -X POST "https://gitea.example.com/api/v1/repos/migrate" \
  -H "Content-Type: application/json" \
  -H "Authorization: token $GITEA_TOKEN" \
  -d '{
    "clone_addr": "https://gitlab.com/user/repo",
    "auth_token": "'$GITLAB_TOKEN'",
    "uid": 1,
    "repo_name": "repo",
    "service": "gitlab",
    "issues": true,
    "pull_requests": true,
    "labels": true,
    "milestones": true,
    "releases": true,
    "wiki": true,
    "comments": true,
    "reviews": true
  }'
```

### Community migration scripts

For cases where the built-in importer does not cover your needs:

- `cornelk/gitlab2gitea` - Go-based tool for GitLab to Gitea migration
- `h44z/gitlab_to_gitea` - Python-based alternative

Both are community-maintained and may lag behind API changes on either platform.

---

## GitHub Enterprise Importer

The **GitHub Enterprise Importer (GEI)** ([github.com/github/gh-gei](https://github.com/github/gh-gei)) is GitHub's official CLI tool for moving repositories between GitHub instances and from other platforms to GitHub.

### Supported migration paths

| Source                          | Tool          |
| ------------------------------- | ------------- |
| GitHub.com or GitHub Enterprise | `gh gei`      |
| Azure DevOps                    | `gh ado2gh`   |
| Bitbucket Server / Data Center  | `gh bbs2gh`   |
| GitLab                          | Not supported |

> [!IMPORTANT]
> GEI does not support GitLab to GitHub migration. This is the most commonly missed limitation. Workarounds: use `git push --mirror` for the repository, then manually recreate issues and PRs; or stage through another platform first.

### Installing GEI

```bash
# 🍎 Mac / 🐧 Linux
gh extension install github/gh-gei

# 🪟 Windows
gh extension install github/gh-gei
```

### GitHub to GitHub migration

```bash
# Authenticate
gh auth login

export GH_SOURCE_PAT=<source-pat>
export GH_TARGET_PAT=<target-pat>

# Migrate a single repository
gh gei migrate-repo \
  --github-source-org SOURCE_ORG \
  --source-repo REPO_NAME \
  --github-target-org TARGET_ORG \
  --target-repo REPO_NAME

# Generate a migration script for an entire organisation
gh gei generate-script \
  --github-source-org SOURCE_ORG \
  --output migrate.sh
```

### What GEI transfers

- Repository (all branches, tags, commits)
- Pull requests with comments, reviews and review comments
- Issues with comments
- Labels and milestones
- Releases and release assets
- GitHub Actions workflow run history (partial)
- Projects (v2)

### What GEI does not transfer

- Webhooks
- Deploy keys
- Actions secrets and variables
- Packages (GitHub Packages)
- Rulesets and branch protection (as of April 2026)
- Pages configuration
- Environments and deployment history

> [!NOTE]
> As of November 2025, GEI uses GitHub-owned blob storage by default. You no longer need to provision your own Azure Blob Storage or AWS S3 bucket to stage migration data. Tutorials written before November 2025 that instruct you to set up cloud storage accounts for GEI are out of date.

---

## Bitbucket migrations

### Bitbucket Cloud to GitHub

There is no first-party importer for Bitbucket Cloud to GitHub. Options:

1. **GitHub's "Import a repository" feature** - handles Git history only, no issues or PRs. Go to github.com/new/import, paste the Bitbucket HTTPS clone URL.
2. **`git push --mirror`** - Git history only, manual.
3. **Stage through Bitbucket Server** - use `gh bbs2gh` to migrate from Bitbucket Server to GitHub, which transfers more metadata.

### Bitbucket Server / Data Center to GitHub

Use `gh bbs2gh` from the GEI toolkit:

```bash
gh extension install github/gh-gei

# Generate migration script
gh bbs2gh generate-script \
  --bbs-server-url https://bitbucket.company.com \
  --bbs-username admin \
  --bbs-password $BBS_PASSWORD \
  --github-target-org TARGET_ORG \
  --output migrate.sh

# Run the migration
bash migrate.sh
```

> [!NOTE]
> Atlassian ended support for Bitbucket Server and Data Center on **15 February 2024**. If you are still running Bitbucket Server, migration to another platform is now urgent from a security standpoint. GEI is the recommended path to GitHub.

---

## Setting up a permanent mirror

If you need to run two platforms in parallel during a transition, or maintain a read-only backup mirror, set up automatic mirroring.

### GitHub to GitLab mirror (GitLab pull mirror)

In GitLab, go to **Settings > Repository > Mirroring repositories > Add new**.

- Mirror direction: Pull
- Git repository URL: the GitHub HTTPS clone URL
- Authentication: personal access token
- Mirror protected branches only: optional
- Trigger pipelines for mirror updates: optional
- Sync interval: every 5 minutes (minimum on GitLab.com)

### GitLab to GitHub mirror (GitLab push mirror)

In GitLab, go to **Settings > Repository > Mirroring repositories > Add new**.

- Mirror direction: Push
- Git repository URL: the GitHub HTTPS clone URL
- Authentication: GitHub personal access token

### Manual cron mirror

```bash
# Set up a bare mirror clone
git clone --mirror https://source.example.com/user/repo.git /srv/mirrors/repo.git
cd /srv/mirrors/repo.git
git remote set-url --push origin https://dest.example.com/user/repo.git

# Sync script (add to crontab)
#!/bin/bash
cd /srv/mirrors/repo.git
git fetch --prune origin
git push --mirror
```

```bash
# Add to crontab (runs every 15 minutes)
*/15 * * * * /srv/mirrors/sync-repo.sh >> /var/log/mirror.log 2>&1
```

---

## The migration checklist

Work through this checklist before and after every platform migration. Do not skip items.

### Before migration

- [ ] Document all webhooks (URLs, events, secrets)
- [ ] Document all deploy keys and which services use them
- [ ] Document branch protection rules and Rulesets
- [ ] Document CI/CD secrets (names only - never write down values)
- [ ] List all external services that reference the repository URL
- [ ] Check whether the repository uses Git LFS (`git lfs ls-files`)
- [ ] Check whether the repository has submodules (`cat .gitmodules`)
- [ ] Announce the migration to all contributors with the cutover date and new URL
- [ ] Set the source repository to read-only if possible, to prevent new commits during migration

### During migration

- [ ] Mirror-clone the source: `git clone --mirror`
- [ ] If LFS: `git lfs fetch --all origin`
- [ ] Create the destination repository (empty, no README)
- [ ] Push Git history: `git push --mirror`
- [ ] If LFS: `git lfs push --all`
- [ ] Run the platform-specific importer for issues, PRs, labels, milestones
- [ ] Verify branch and tag counts match between source and destination
- [ ] Verify LFS objects are accessible on the destination

### After migration

- [ ] Recreate webhooks on the destination platform
- [ ] Recreate deploy keys and rotate all credentials
- [ ] Recreate CI/CD secrets with new values
- [ ] Recreate branch protection rules and Rulesets
- [ ] Update submodule URLs in `.gitmodules`
- [ ] Update any hardcoded repository URLs in CI/CD pipelines, README badges and documentation
- [ ] Update team members' local remotes: `git remote set-url origin <new-url>`
- [ ] Set up a redirect or archive notice on the old repository
- [ ] Test CI/CD pipelines on the new platform
- [ ] Monitor for broken links in external services

---

## War story: the GitLab database outage of 2017

On 31 January 2017, a GitLab.com administrator was responding to a spam attack and accidentally ran `rm -rf` on the **primary** PostgreSQL data directory, mistaking it for the replica they intended to clean.

Approximately 300 GB of production database data was destroyed. What followed was a live incident broadcast on YouTube while the team worked through recovery options:

- The nightly database backup via `pg_dump` had been failing silently for months due to a version mismatch
- Disk snapshots for database servers had been disabled (they were enabled for app servers)
- The database replica had fallen 4.5 GB behind the primary and its WAL logs had been purged when the primary was deleted
- A staging sync that used LVM snapshots (taken every 24 hours) was the only surviving backup

**Git repositories were on NFS and were unaffected.** Only the database (issues, comments, users, CI configuration) was lost.

The team restored from the 24-hour-old LVM snapshot, losing approximately 6 hours of data: around 5,000 projects, 700 new user accounts, 1,500 comments and 700 snippets.

**The lessons applied directly to migrations:**

- Verify backups before you need them - not after
- Separate your backup verification from your backup creation
- Database content (issues, PRs, comments) is not the same as Git content (commits, files) - back up both
- A read-only replica that has fallen behind is not a backup

Full postmortem: [about.gitlab.com/blog/postmortem-of-database-outage-of-january-31](https://about.gitlab.com/blog/postmortem-of-database-outage-of-january-31/).

---

## Try it yourself

This exercise migrates a public repository from GitHub to a local Gitea instance using Docker.

```bash
# Step 1: start a local Gitea instance
docker run -d \
  --name gitea \
  -p 3000:3000 \
  -p 2222:22 \
  -v gitea-data:/data \
  gitea/gitea:latest

# Step 2: open http://localhost:3000 in your browser
# Complete the Gitea setup wizard (use SQLite for this exercise)
# Create an admin account

# Step 3: get a Gitea API token
# Go to http://localhost:3000/user/settings/applications
# Generate a token with Repository and Issue scopes

# Step 4: mirror-clone a small public GitHub repository
git clone --mirror https://github.com/zaccesss/git-unlocked.git
cd git-unlocked.git

# Step 5: create the destination repo on Gitea via API
curl -X POST "http://localhost:3000/api/v1/user/repos" \
  -H "Authorization: token $GITEA_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "git-unlocked", "private": false}'

# Step 6: push everything
git push --mirror http://localhost:3000/YOUR_USERNAME/git-unlocked.git

# Step 7: verify
git ls-remote http://localhost:3000/YOUR_USERNAME/git-unlocked.git | head -10
```

**Bonus:** use Gitea's web importer to import the same repository again (New Migration > GitHub) and compare what the importer transfers versus the raw mirror push.

---

## Common mistakes

**Running `git push --mirror` against a repository that already has commits.**
`--mirror` deletes remote refs that do not exist locally. If the destination repository was initialised with a README or has any commits, they will be permanently deleted. Always push to a freshly created empty repository.

**Forgetting to transfer LFS objects.**
`git push --mirror` transfers Git objects. LFS blobs are not Git objects. If your repository uses LFS and you skip `git lfs fetch --all` and `git lfs push --all`, every LFS-tracked file on the destination will be a pointer with no corresponding blob. The files will be unreachable.

**Copying CI/CD secrets instead of rotating them.**
Never copy a secret value from one platform to another. Generate a new secret on the destination and revoke the old one. A copied secret was valid on the old platform; until revoked, it creates a window where both platforms hold a working credential.

**Expecting GitLab to GitHub migration to work with GEI.**
GEI supports Azure DevOps to GitHub and Bitbucket Server to GitHub. It does not support GitLab to GitHub. This gap catches many teams by surprise. Use `git push --mirror` for the Git history and recreate issues and PRs manually or via API scripting.

**Not updating submodule URLs.**
Submodule entries in `.gitmodules` contain absolute URLs pointing to the old platform. After migration, those URLs still point to the old repository. Update them with `git submodule set-url <name> <new-url>` and commit the change.

**Deleting the source repository before verifying the destination.**
Keep the source repository in read-only mode for at least two weeks after migration. Redirect its URL, pin a notice, but do not delete it. Teams will have local clones with the old remote URL; external services may still reference it; there may be issues or PR comments you forgot to migrate. Deletion is permanent.

---

## Summary

Migrating between Git platforms has two distinct layers: the Git layer (commits, branches, tags, LFS objects) and the platform layer (issues, PRs, comments, labels, webhooks, secrets). The Git layer transfers cleanly with `git clone --mirror` and `git push --mirror`. The platform layer requires a platform-specific importer, manual recreation or API scripting.

Git LFS is the most common source of silent data loss. Always check whether the repository uses LFS, always run `git lfs fetch --all` before pushing, and always run `git lfs push --all` separately after the mirror push.

The strongest platform importers are GitLab's GitHub importer (transfers issues, PRs, labels, milestones, releases and comments) and GitHub's GEI toolkit (transfers from GitHub, Azure DevOps and Bitbucket Server, but not GitLab). Work through the migration checklist before, during and after every migration, keep the source repository for at least two weeks after cutover and rotate all credentials rather than copying them.

---

## Sources and Further Reading

- [GitLab: import a project from GitHub](https://docs.gitlab.com/user/project/import/github/)
- [GitLab: repository mirroring](https://docs.gitlab.com/user/project/repository/mirror/)
- [GitHub Enterprise Importer](https://github.com/github/gh-gei)
- [GitHub: import a repository](https://docs.github.com/en/migrations/importing-source-code/using-github-importer/importing-a-repository-with-github-importer)
- [Gitea: migrations](https://docs.gitea.com/usage/migration)
- [git-lfs: migrate](https://github.com/git-lfs/git-lfs/blob/main/docs/man/git-lfs-migrate.adoc)
- [GitLab: postmortem of the database outage of January 31](https://about.gitlab.com/blog/postmortem-of-database-outage-of-january-31/)
- [GitHub: about large files - GH001 error](https://docs.github.com/en/repositories/working-with-files/managing-large-files/about-large-files-on-github)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

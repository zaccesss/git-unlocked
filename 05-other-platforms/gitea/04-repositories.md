# Repositories in Gitea

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

Repositories in Gitea work like repositories on any Git platform - they store your code, history, branches and tags. What makes Gitea interesting is the set of features layered on top: built-in mirroring from GitHub, GitLab or any Git remote, Git LFS for large files, releases with asset attachments and a topic-based discovery system. This file covers creating, configuring and working with Gitea repositories.

---

## Table of Contents

1. [Creating a repository](#creating-a-repository)
2. [Repository settings](#repository-settings)
3. [Cloning and pushing](#cloning-and-pushing)
4. [Branches and the default branch](#branches-and-the-default-branch)
5. [Mirroring from GitHub, GitLab and others](#mirroring-from-github-gitlab-and-others)
6. [Repository topics](#repository-topics)
7. [Releases](#releases)
8. [Git Large File Storage](#git-large-file-storage)
9. [Wikis](#wikis)
10. [Visibility levels](#visibility-levels)
11. [Forking](#forking)
12. [Repository transfer and deletion](#repository-transfer-and-deletion)
13. [Try It Yourself](#try-it-yourself)
14. [Common Mistakes](#common-mistakes)
15. [Summary](#summary)
16. [Sources](#sources)

---

## Creating a repository

### Via the web interface

1. Click the **+** icon in the top navigation bar
2. Select **New Repository**
3. Fill in the details:

**Owner**: yourself or an organisation you belong to. The owner determines the repository URL and who can manage it.

**Repository name**: the slug used in the URL. Must be unique within the owner's namespace. Use lowercase letters, numbers, hyphens and underscores.

**Description**: optional but useful. Shown on the repository list page.

**Visibility**:
- **Public**: anyone can view and clone
- **Private**: only invited collaborators can see it
- **Internal** (if enabled by admin): visible to all authenticated users on the instance

**Initialise this repository**: creates an initial commit with the files you select. Check this for new projects. Leave unchecked when pushing an existing local repository.

**Default branch**: the branch Gitea shows by default. `main` is the current convention.

**.gitignore template**: pre-populated `.gitignore` for common languages and frameworks.

**Licence**: adds a `LICENSE` file with the chosen licence text.

4. Click **Create Repository**

### Via the API

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  -d '{
    "name": "my-new-project",
    "description": "A new project",
    "private": true,
    "auto_init": true,
    "default_branch": "main"
  }' \
  https://gitea.example.com/api/v1/user/repos
```

To create within an organisation:
```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  -d '{"name": "org-project", "private": true}' \
  https://gitea.example.com/api/v1/orgs/my-org/repos
```

### Pushing an existing local repository

```bash
cd my-existing-project
git init                              # if not already a Git repo
git add .
git commit -m "Initial commit"
git remote add origin https://gitea.example.com/username/repo-name.git
git push -u origin main
```

---

## Repository settings

Access repository settings from the **Settings** tab in any repository (visible only to owners and users with admin permission).

### General settings

**Repository name**: rename the repository. This changes the URL - update all Git remotes.

**Description**: the text shown below the repo name on list pages.

**Website**: a link to the project's external site, shown on the repository page.

**Visibility**: change between public, private and internal.

**Topics**: tags for discoverability (covered below).

### Dangerous zone

The danger zone at the bottom of settings contains:

**Archive repository**: makes the repository read-only. Archived repositories show a banner and cannot receive pushes. Useful for preserving historical code without encouraging contributions.

**Transfer repository**: move the repository to a different user or organisation. All Git remotes pointing to the old URL break - they must be updated.

**Delete repository**: permanently deletes the repository including all code, issues, pull requests, wikis and releases. No recovery. Type the repository name to confirm.

---

## Cloning and pushing

### Clone via HTTPS

```bash
git clone https://gitea.example.com/username/repository.git
```

For private repositories, you need credentials:
- **Username**: your Gitea username
- **Password**: your access token (not your account password)

### Clone via SSH

```bash
git clone git@gitea.example.com:username/repository.git
```

Requires your SSH public key to be added to Gitea (Settings > SSH / GPG Keys).

### Updating a remote URL

If the repository URL changes (renamed, transferred, instance moved):

```bash
git remote set-url origin https://new-gitea.example.com/username/repository.git
# or for SSH:
git remote set-url origin git@new-gitea.example.com:username/repository.git
```

### Shallow clone (for large repositories)

```bash
git clone --depth 1 git@gitea.example.com:username/large-repo.git
```

Downloads only the latest commit, not the full history. Useful for CI builds.

---

## Branches and the default branch

### Viewing branches

Click **Branches** in the repository navigation to see all branches with:
- Last commit message and author
- Date of last commit
- How many commits ahead/behind the default branch
- Whether a pull request exists for the branch

### Creating a branch

Via the web:
1. Click the branch dropdown (shows the current branch name)
2. Type a new branch name in the search box
3. Click **Create branch: branch-name from current-branch**

Via the command line:
```bash
git checkout -b feature/my-feature
git push origin feature/my-feature
```

### Changing the default branch

1. Go to repository **Settings** > **Repository**
2. Under **Default branch**, select the new default from the dropdown
3. Click **Update**

> [!NOTE]
> Changing the default branch affects the branch shown when visiting the repository, the target for pull requests by default and the branch checked out by `git clone`. Update any CI/CD configurations, documentation and processes that reference the old default branch name.

### Deleting branches

After a pull request is merged, delete the source branch to keep the branch list tidy:

Via the web: go to **Branches** > find the branch > click the delete icon.

Via the command line:
```bash
git push origin --delete feature/my-feature
```

---

## Mirroring from GitHub, GitLab and others

Gitea's **mirror** feature creates a one-way sync from an external repository. Gitea periodically pulls updates from the upstream repository, keeping the mirror in sync.

### Use cases for mirrors

- **Backup**: keep a local copy of critical external repositories
- **Offline access**: mirror frequently-used open source repositories for air-gapped environments
- **Internal proxy**: provide fast internal access to external repos on slow links
- **Archive**: track and preserve external projects

### Creating a mirror

1. Click **+** > **New Migration**
2. Choose the source platform:
   - GitHub
   - GitLab
   - Bitbucket
   - Gitea
   - Gogs
   - OneDev
   - Forgejo
   - Any Git URL
3. Enter the repository URL
4. For private repositories, provide authentication (access token or username/password)
5. Configure mirror settings:
   - **Mirror interval**: how often to pull (default: 8 hours, minimum: 10 minutes)
   - **LFS**: whether to mirror LFS objects
   - **Mirror releases**: whether to mirror GitHub/GitLab releases
6. Configure the Gitea repository settings (name, visibility, etc.)
7. Click **Migrate Repository**

### Mirror indicator

Mirrored repositories show a small sync icon on the repository page. The last sync time is shown. You can also trigger a manual sync from the repository Settings > Mirror.

### Push mirror

Gitea also supports **push mirrors**: Gitea pushes changes to an external repository. This creates a live backup or allows you to maintain a repository on multiple hosts simultaneously.

Configure push mirrors from repository Settings > Mirror > **Push Mirrors** section.

### Import vs mirror

- **Import**: a one-time copy of a repository. Creates a new Gitea repository with the imported history. No ongoing sync.
- **Mirror**: creates a repository that periodically syncs from the upstream. Read-only on the Gitea side.

For a one-time migration, use import. For ongoing synchronisation, use mirror.

---

## Repository topics

Topics are searchable tags that categorise a repository. They appear on the repository page and allow discovery through Gitea's explore/search functionality.

### Adding topics

1. On the repository page, click the **gear icon** next to "Topics"
2. Type a topic and press Enter or Tab
3. Add multiple topics
4. Click **Done**

### Topic conventions

Use short, lowercase slugs: `python`, `web-api`, `machine-learning`, `docker`, `kubernetes`, `work-in-progress`.

Topics work best when you use established terminology that others search for. Gitea's explore page allows filtering by topic.

---

## Releases

Releases provide versioned snapshots of your repository with attached binary assets. They are distinct from Git tags, though a release is always associated with a tag.

### Creating a release

1. Go to **Releases** in the repository navigation (or **Code** > releases count)
2. Click **Draft a new release**
3. Fill in:
   - **Tag**: the Git tag for this release. Type a new tag (e.g. `v1.2.0`) to create it, or select an existing tag.
   - **Target**: the branch or commit the tag points to
   - **Release title**: the name of the release (e.g. "v1.2.0 - Performance improvements")
   - **Description**: what changed. Supports Markdown. Include a changelog.
   - **Attachments**: upload binary files (installers, compiled executables, checksums)
4. Choose **Pre-release** if this is a release candidate or beta
5. Click **Publish release** (or **Save draft** to continue editing)

### Release assets

Binary files attached to a release are stored in Gitea's data directory (not in the Git repository itself). The maximum file size per attachment is configurable by the administrator. Each asset is downloadable individually or as a ZIP of all assets.

### Listing releases

The **Releases** page shows all releases sorted by date, with download counts for each asset. This is useful for tracking which versions are being downloaded.

### Release API

```bash
# List releases
curl -H "Authorization: token YOUR_TOKEN" \
  https://gitea.example.com/api/v1/repos/username/repo/releases

# Create a release
curl -X POST \
  -H "Content-Type: application/json" \
  -H "Authorization: token YOUR_TOKEN" \
  -d '{
    "tag_name": "v1.3.0",
    "name": "v1.3.0 - Bug fixes",
    "body": "Fixed critical authentication bug",
    "draft": false,
    "prerelease": false
  }' \
  https://gitea.example.com/api/v1/repos/username/repo/releases
```

---

## Git Large File Storage

Git LFS (Large File Storage) replaces large files in the repository with pointer files. The actual content is stored separately. This keeps the repository's Git history small while still versioning large assets.

### Prerequisites

Install Git LFS:

🪟 **Windows**: Git LFS is included with Git for Windows. Verify: `git lfs version`

🍎 **Mac**:
```bash
brew install git-lfs
git lfs install
```

🐧 **Linux** (Debian/Ubuntu):
```bash
sudo apt install git-lfs
git lfs install
```

### Configuring LFS tracking

```bash
# Track PNG image files
git lfs track "*.png"

# Track files in an assets directory
git lfs track "assets/**"

# Track specific file types common in game development
git lfs track "*.fbx" "*.obj" "*.blend" "*.psd"

# View current tracking patterns
git lfs track
```

This updates `.gitattributes`. Commit it:

```bash
git add .gitattributes
git commit -m "Configure Git LFS tracking"
git push
```

### Gitea LFS configuration

LFS must be enabled in the Gitea instance's `app.ini`:

```ini
[server]
LFS_START_SERVER = true

[lfs]
PATH = /var/lib/gitea/data/lfs
```

This is typically enabled by default in modern Gitea installations but may need to be confirmed with your instance administrator.

### LFS storage limits

LFS objects are stored separately from the Git repository. The total LFS storage is limited only by available disk space on the server. There is no per-repository LFS quota in Gitea Community Edition (unlike GitHub's 1 GB free / paid plans).

---

## Wikis

Every Gitea repository can have a built-in wiki for documentation.

### Enabling the wiki

Wikis are enabled by default. To disable:
1. Go to **Settings** > **Repository**
2. Uncheck **Enable Repository Wiki**

### Creating wiki pages

1. Click **Wiki** in the repository navigation
2. Click **New Wiki Page** (or edit the existing Home page)
3. Write content in Markdown
4. Click **Save Page**

Wiki pages support:
- Full Markdown including tables, code blocks and images
- Internal links to other pages: `[[Page Name]]`
- GitHub-compatible anchor links
- File attachments

### The wiki as a Git repository

Behind the scenes, the wiki is a separate Git repository. You can clone it:

```bash
git clone https://gitea.example.com/username/repository.wiki.git
```

This allows offline editing, bulk content updates and migration from other wiki systems.

---

## Visibility levels

Gitea has three visibility levels:

**Public**: anyone can view and clone the repository without authentication. This is the appropriate level for open source projects.

**Private**: only the owner and explicitly added collaborators can view the repository. All Git operations require authentication.

**Internal**: visible to any authenticated user on the Gitea instance. Not visible to the public internet. Useful for company internal repositories where all employees should have read access without needing individual invitations.

> [!NOTE]
> **Internal visibility** must be enabled by the instance administrator in `app.ini`. It is not available on all Gitea instances, and it does not exist in GitHub or GitLab.com equivalents. It maps conceptually to GitLab's "Internal" visibility level on self-managed instances.

---

## Forking

A fork is an independent copy of a repository in a different user or organisation namespace. Forks are used for:
- Contributing to a project you do not have write access to
- Experimenting with changes without affecting the original
- Creating a personalised version of a project

### Creating a fork

1. Navigate to the repository to fork
2. Click the **Fork** button in the top right
3. Select the destination owner (your account or an organisation)
4. Click **Fork Repository**

The fork is independent from the original. Changes in the fork do not automatically appear in the original and vice versa.

### Keeping a fork up to date

```bash
# Add the original as an upstream remote
git remote add upstream https://gitea.example.com/original-owner/original-repo.git

# Fetch upstream changes
git fetch upstream

# Merge upstream main into your local main
git checkout main
git merge upstream/main

# Push to your fork
git push origin main
```

### Pull requests from forks

To contribute changes back to the original repository:
1. Push your changes to your fork
2. Go to the **original** repository
3. Click **New Pull Request**
4. Click **compare across forks**
5. Select your fork and branch as the source
6. Submit the pull request

---

## Repository transfer and deletion

### Transferring a repository

Transfers move a repository to a different owner (user or organisation).

1. Go to **Settings** > scroll to **Danger Zone**
2. Click **Transfer**
3. Type the new owner's username
4. Type the repository name to confirm
5. Click **Transfer**

After transfer:
- The repository URL changes
- All existing Git remotes pointing to the old URL break
- Forks of the repository retain their old URL (they do not automatically follow the transfer)
- Issues, pull requests and releases move with the repository
- Collaborator access is reset to the new owner's defaults

### Deleting a repository

1. Go to **Settings** > scroll to **Danger Zone**
2. Click **Delete This Repository**
3. Type the repository name to confirm
4. Click **Delete**

Deletion is permanent. There is no trash or recycle bin. If you might need the code, clone it first.

---

## Try It Yourself

**Exercise 1 - Create a repository and push code**

1. Create a new private repository called `practice-repo` in your Gitea account
2. Initialise with a README
3. Clone it:
```bash
git clone https://gitea.example.com/your-username/practice-repo.git
cd practice-repo
```
4. Make a change and push:
```bash
echo "# Practice Repository" >> NOTES.md
git add NOTES.md
git commit -m "Add notes file"
git push
```

**Exercise 2 - Create a mirror**

1. Click **+** > **New Migration** > **GitHub**
2. Enter a public GitHub repository URL (e.g. `https://github.com/git/git`)
3. Set mirror interval to 24 hours
4. Create the mirror and watch Gitea import the repository

**Exercise 3 - Set up LFS**

```bash
cd practice-repo
git lfs install
git lfs track "*.png"
git add .gitattributes
git commit -m "Configure LFS"

# Add a large-ish file (or any file)
cp /path/to/any/image.png test-image.png
git add test-image.png
git commit -m "Add test image via LFS"
git push
```

Check the repository on Gitea - the image should show the LFS pointer in the file view.

**Exercise 4 - Create a release**

1. In your practice repository, go to **Releases**
2. Click **Draft a new release**
3. Create tag `v0.1.0` on the main branch
4. Title: "Initial release"
5. Description: "First practice release"
6. Click **Publish release**

---

## Common Mistakes

**Creating a repository with a README when you plan to push an existing repo**

If you initialise a repository with a README and then try to push your local repository, Git rejects the push because the histories diverge. Either: initialise empty (no README), or fetch and merge the README before pushing.

**Not committing .gitattributes after `git lfs track`**

`git lfs track` writes to `.gitattributes`. If you forget to commit this file, LFS tracking only applies to your local clone. Other team members or CI systems do not have LFS tracking configured.

**Using mirrors for repositories you plan to modify**

Mirrors are one-way (upstream → Gitea). You cannot push to a mirrored repository. If you want a copy you can work with, use migration (import) instead.

**Forgetting that transfers break all existing remote URLs**

After transferring a repository, all existing `git remote` URLs in local clones are stale. Every developer and every CI/CD pipeline that references the old URL must be updated. Announce transfers in advance.

**Treating internal visibility as a security boundary**

Internal repositories are visible to all authenticated users on the instance. If an instance allows external users or contractors to register accounts, internal repositories are visible to them. For genuinely sensitive code, use private visibility with explicit collaborator access.

---

## Summary

Gitea repositories provide core Git hosting with pull requests, issues, wikis, releases and a package registry. Create repositories via the web interface or API. Clone with HTTPS (using an access token as the password) or SSH.

Mirroring syncs from external repositories (GitHub, GitLab, any Git URL) on a configurable schedule. Use this for backups, air-gapped access or tracking upstream dependencies. Migrations create a one-time copy without ongoing sync.

Repository topics add searchable tags for discoverability. Releases provide versioned snapshots with binary asset attachments. Git LFS handles large binary files by replacing them with pointer files and storing content separately.

Visibility levels are public (anyone), private (invited collaborators only) and internal (all authenticated instance users). Internal visibility must be enabled by the administrator and is useful for company-internal repositories.

---

## Sources

- [Gitea: Repository documentation](https://docs.gitea.com/usage/repos)
- [Gitea: Migrations](https://docs.gitea.com/usage/repo-migration)
- [Gitea: Git LFS](https://docs.gitea.com/usage/git-lfs)
- [Gitea: Releases](https://docs.gitea.com/usage/releases)
- [Gitea: Wiki](https://docs.gitea.com/usage/wiki)
- [Git LFS: Official documentation](https://git-lfs.com)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# Repositories in Bitbucket

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

A repository in Bitbucket is where your code lives. Everything else - pull requests, pipelines, branch permissions, integrations - is built around the repository. Understanding how to create, configure and manage repositories in Bitbucket is foundational to everything that follows in this folder.

Bitbucket repositories work the same way as repositories on any other Git platform at the protocol level - Git is Git. What differs is how Bitbucket organises repositories inside workspaces and projects, what configuration options are available, how Large File Storage works, and how forking interacts with the workspace structure. This file covers all of it.

---

## Table of Contents

1. [How repositories are organised in Bitbucket](#how-repositories-are-organised-in-bitbucket)
2. [Creating a repository](#creating-a-repository)
3. [Repository settings and configuration](#repository-settings-and-configuration)
4. [Cloning a repository](#cloning-a-repository)
5. [Projects: grouping repositories](#projects-grouping-repositories)
6. [Repository visibility](#repository-visibility)
7. [Forking](#forking)
8. [Large File Storage](#large-file-storage)
9. [Repository transfers and deletion](#repository-transfers-and-deletion)
10. [Import a repository from another platform](#import-a-repository-from-another-platform)
11. [Repository variables and deployment environments](#repository-variables-and-deployment-environments)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## How repositories are organised in Bitbucket

Before creating a repository, it helps to understand the three-level structure Bitbucket uses:

```
Workspace
  └── Project (optional grouping layer)
        └── Repository
```

**Workspace** is the top-level container. Every repository belongs to a workspace. A workspace has members, billing and its own settings. Your personal workspace uses your username as the slug. Team or company workspaces are created separately.

**Project** is an optional intermediate layer between the workspace and individual repositories. Projects group related repositories together. In a workspace with many repositories, projects make navigation and organisation manageable. Examples: a `Backend` project containing all backend service repositories, a `Mobile` project containing iOS and Android repositories, a `Infrastructure` project containing Terraform and Ansible repositories.

**Repository** is where the actual code lives. Every repository belongs to exactly one workspace and can optionally belong to one project within that workspace.

This structure differs from GitHub (where there is no grouping layer between organisations and repositories) and from GitLab (where groups can nest inside other groups creating deep hierarchies). Bitbucket's approach is a middle ground: one optional grouping level within a workspace.

> [!NOTE]
> Projects in Bitbucket are not the same concept as GitHub Projects (which are Kanban boards). Bitbucket Projects are purely a structural grouping mechanism for repositories - they do not have issue boards, roadmaps or any planning features. Those live in Jira.

---

## Creating a repository

### Via the web interface

1. Sign in to **bitbucket.org**
2. Click the **+** icon in the left sidebar, or navigate to your workspace and click **Create repository**
3. Fill in the repository details:

**Workspace** - choose which workspace owns this repository. You can move it later but it is easier to place it correctly from the start.

**Project** - optionally assign it to a project within the workspace. If no projects exist yet, you can create one here.

**Repository name** - the name appears in the URL and in the Bitbucket interface. Rules:

- Letters, numbers, hyphens and underscores
- No spaces (spaces become hyphens in the URL)
- Case-insensitive in the URL
- Must be unique within the workspace

**Access level** - Private or Public (see the visibility section below for details).

**Include a README** - creates a `README.md` file in the default branch. Recommended if you are starting fresh. Skip this if you are importing existing code (an initial commit will conflict with your import).

**Default branch name** - Bitbucket defaults to `main`. Some older workflows expect `master`. Set this to match your team's convention. You can rename the default branch later but it requires updating any CI/CD configurations and local clones.

**Include .gitignore** - Bitbucket offers language-specific `.gitignore` templates. Select your primary language and it creates an appropriate file.

4. Click **Create repository**

### Via the Bitbucket CLI

Bitbucket does not have an official CLI tool equivalent to GitHub's `gh` or GitLab's `glab`. You can create repositories using the Bitbucket REST API directly:

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "your-email@example.com:your-api-token" \
  "https://api.bitbucket.org/2.0/repositories/your-workspace/new-repo-name" \
  -d '{
    "scm": "git",
    "is_private": true,
    "project": {
      "key": "PROJ"
    }
  }'
```

Replace `your-workspace` with your workspace slug, `new-repo-name` with the repository name, and `PROJ` with your project key if using a project.

### From the command line with git init

If you already have a local Git repository and want to push it to Bitbucket:

1. Create the repository on Bitbucket with **no README and no .gitignore** (empty repository)
2. Copy the repository URL from the Clone button
3. Add it as a remote and push:

```bash
cd your-local-project

# If not already a Git repository
git init
git add .
git commit -m "Initial commit"

# Add the Bitbucket remote (use SSH or HTTPS)
git remote add origin git@bitbucket.org:your-workspace/your-repo.git

# Push your code
git push -u origin main
```

> [!TIP]
> The `-u` flag on `git push -u origin main` sets the upstream tracking relationship. After this first push, you can use `git push` and `git pull` without specifying the remote and branch name - Git remembers.

---

## Repository settings and configuration

After creating a repository, the settings page controls everything about how it behaves. Access repository settings from the left sidebar in any repository view.

### General settings

**Repository name** - rename the repository. This changes the URL. All existing Git remotes pointing to the old URL will break. Update them in every local clone and any CI/CD configurations.

**Repository slug** - the URL-safe identifier. Changing this has the same impact as renaming.

**Description** - a short summary shown on the repository listing page. Useful for navigation in large workspaces.

**Fork policy** - control whether users can fork this repository:

- Allow forks (default)
- Restrict forks to the same workspace only
- No forks allowed

**Default branch** - set which branch is treated as the primary branch. Pull requests default to targeting this branch. It is also the branch shown when someone views the repository without specifying a branch.

**Language** - the primary programming language. Used for repository categorisation and display.

### Merging strategies

Under repository settings, you can restrict which merge strategies are available for pull requests:

- **Merge commit** - creates a merge commit preserving full branch history
- **Squash** - collapses all commits from the PR into a single commit on the target branch
- **Fast-forward** - only allowed if no merge commit would be created (linear history)

You can enable or disable each strategy. Restricting to squash-only enforces a clean linear history. Restricting to fast-forward prevents merge commits entirely.

### Branching model

Bitbucket has a built-in **branching model** configuration that structures how branches are named and categorised. When enabled, it creates conventions for:

- **Production branch** - typically `main` or `master`
- **Development branch** - typically `develop`
- **Feature branches** - prefixed with `feature/`
- **Bugfix branches** - prefixed with `bugfix/`
- **Release branches** - prefixed with `release/`
- **Hotfix branches** - prefixed with `hotfix/`

This is an implementation of **Gitflow** - a popular branching strategy. When the branching model is configured, Bitbucket's UI offers shortcuts for creating correctly-named branches from the web interface, and it categorises branches in the branch listing page.

The branching model is optional. Teams that use trunk-based development or GitHub Flow (simpler, single main branch) should leave it disabled.

### Watchers

Anyone can watch a repository to receive notifications for activity (pushes, pull requests, comments). Repository administrators can see who is watching and can remove watchers if needed.

---

## Cloning a repository

The **Clone** button on any Bitbucket repository page provides the clone URL. You can choose between SSH and HTTPS.

### SSH clone (recommended for local development)

```bash
git clone git@bitbucket.org:workspace-slug/repository-name.git
```

Requires your SSH key to be added to Bitbucket. After setup, no authentication prompt appears for subsequent operations.

### HTTPS clone

```bash
git clone https://bitbucket.org/workspace-slug/repository-name.git
```

When prompted for credentials:

- **Username**: your Atlassian email address
- **Password**: your API token (not your Atlassian account password)

🪟 **Windows**: Git Credential Manager (bundled with Git for Windows) stores the API token automatically after the first use.

🍎 **Mac**: The macOS Keychain stores credentials automatically via Git Credential Manager.

🐧 **Linux**: Use Git's credential store or cache helper:

```bash
git config --global credential.helper store
```

### Cloning a specific branch

```bash
git clone -b branch-name git@bitbucket.org:workspace/repo.git
```

### Shallow clone (for large repositories)

A shallow clone downloads only the most recent commits rather than the full history. Useful for CI/CD pipelines where history is not needed:

```bash
git clone --depth 1 git@bitbucket.org:workspace/repo.git
```

---

## Projects: grouping repositories

Projects are Bitbucket's way of organising multiple repositories within a workspace. They are particularly useful when a workspace has many repositories that logically belong to different teams, products or domains.

### Creating a project

1. Navigate to your workspace
2. Click **Projects** in the left sidebar
3. Click **Create project**
4. Give the project a name and a key (2-10 uppercase letters, unique within the workspace)
5. Set visibility (Public or Private)
6. Optionally add a description and avatar

The **project key** appears in Jira-style notation: a project with key `BACK` would have repositories listed under that key. This key is also what you reference when creating repositories via the API.

### Moving repositories into projects

You can assign an existing repository to a project from the repository's settings page under **Repository details** > **Project**.

### Project permissions

Project permissions allow you to grant access at the project level rather than configuring each repository individually. A user granted **write access** to a project automatically has write access to all repositories within that project. This is a significant time-saver for large teams.

Permissions cascade: workspace-level permissions override project-level permissions, which override repository-level permissions.

### When to use projects

**Use projects when:**

- Your workspace has more than 10-15 repositories
- Multiple teams work in the same workspace and need logical separation
- You want to grant access to a group of related repositories at once
- You want to keep the workspace repository listing navigable

**Skip projects when:**

- Your workspace has only a few repositories
- All repositories are managed by the same small team
- You prefer flat structure

---

## Repository visibility

Bitbucket repositories can be **Private** or **Public**.

### Private repositories

Private repositories are visible only to workspace members and collaborators who have been explicitly granted access. Private is the default for most professional and commercial work.

On the free plan, private repositories count toward the 5-user workspace limit. All workspace members with any level of access consume a seat.

### Public repositories

Public repositories are visible to anyone on the internet. Anyone can clone and view the code without authentication. Anyone with a Bitbucket account can create a fork. However, only authorised users can push to the repository or create pull requests.

Public repositories on Bitbucket do not offer the same discoverability as public repositories on GitHub. Bitbucket has no trending repositories page, no topic-based exploration and no community stars system. A public Bitbucket repository is accessible but not promoted within the Bitbucket community the way a GitHub repository is.

### Changing visibility

You can change a repository's visibility at any time in repository settings. Changing from private to public immediately makes all code, commit history and pull request discussions visible to everyone. Review the content carefully before making a repository public - look for accidentally committed secrets, API keys or sensitive configuration in the commit history.

> [!WARNING]
> Making a repository public does not remove sensitive data from the commit history. If you have ever committed an API key, password or other secret - even if you later deleted the file - the secret is still in the Git history and becomes publicly visible. Use a tool like `git filter-repo` or `BFG Repo Cleaner` to remove sensitive data from history before making a repository public.

---

## Forking

A fork is a copy of a repository in a different workspace. Forks are used for:

- Contributing to a project you do not have write access to (fork, make changes, submit a pull request back to the original)
- Experimenting with a codebase without affecting the original
- Creating a personalised version of an open source project

### Creating a fork

1. Navigate to the repository you want to fork
2. Click the **...** menu (more options) or the Fork button
3. Choose the destination workspace
4. Optionally rename the fork
5. Click **Fork repository**

The fork is a complete copy of the repository, including all branches and tags. Going forward, the fork and the original are independent - changes in one do not automatically appear in the other.

### Fork policy

Repository owners can control forking behaviour:

- **Allow forks** (default) - anyone with read access can fork
- **Allow forks within workspace only** - forks are restricted to the same workspace
- **No forks** - forking is disabled

### Keeping a fork up to date

After forking, the original repository may receive new commits. To get those changes into your fork:

```bash
# Add the original as a second remote (usually called "upstream")
git remote add upstream git@bitbucket.org:original-workspace/original-repo.git

# Fetch the latest from upstream
git fetch upstream

# Merge upstream changes into your local main branch
git checkout main
git merge upstream/main

# Push the updated main to your fork
git push origin main
```

### Pull requests from forks

When you have made changes in your fork and want to contribute them back to the original repository:

1. Push your changes to your fork
2. Go to the original repository (not your fork)
3. Click **Create pull request**
4. In the source section, select your fork and branch
5. The target will default to the original repository's default branch
6. Fill in the PR details and submit

The original repository's maintainers review your pull request and can merge it, request changes or close it.

---

## Large File Storage

Git is designed for text files and source code. Binary files - images, videos, audio files, compiled executables, datasets, design files - cause problems in Git repositories because Git stores complete copies of every version of every file. A 50 MB image that is edited 10 times creates 500 MB of history. Git LFS (Large File Storage) solves this by replacing large files in the repository with small pointer files, storing the actual file content on a separate LFS server.

### How LFS works

When you add a file to LFS tracking:

1. The file is uploaded to Bitbucket's LFS storage server
2. In the repository, a small pointer file replaces the actual content
3. The pointer file contains a hash identifying the LFS object
4. When you clone the repository, Git downloads the pointer files. `git lfs pull` retrieves the actual file content.

The repository history stays small because only tiny pointer files are committed. The LFS server holds the large files.

### Setting up LFS

First, install Git LFS:

🪟 **Windows**:

```bash
# If using Git for Windows, LFS is often included. Check:
git lfs version

# Or install via Chocolatey:
choco install git-lfs

# Or download from git-lfs.com
```

🍎 **Mac**:

```bash
brew install git-lfs
```

🐧 **Linux** (Debian/Ubuntu):

```bash
sudo apt install git-lfs
```

After installing, initialise LFS:

```bash
git lfs install
```

### Tracking file types with LFS

Tell Git LFS which file types to track:

```bash
# Track all PNG files
git lfs track "*.png"

# Track all files in an assets directory
git lfs track "assets/**"

# Track specific large file types
git lfs track "*.psd" "*.ai" "*.sketch" "*.mp4" "*.zip"
```

This creates or updates a `.gitattributes` file in your repository root. Commit this file:

```bash
git add .gitattributes
git commit -m "Configure Git LFS tracking"
```

From now on, any file matching the tracked patterns is stored in LFS when you add and commit it.

### LFS storage limits

LFS storage is separate from repository storage and is plan-dependent:

| Plan     | LFS Storage                                |
| -------- | ------------------------------------------ |
| Free     | 1 GB (shared with total workspace storage) |
| Standard | 5 GB                                       |
| Premium  | 10 GB                                      |

Additional LFS storage can be purchased. If you exceed your LFS quota, pushes that would add LFS objects fail until you either delete old LFS objects or purchase more storage.

### Checking what is in LFS

```bash
# List all tracked LFS files
git lfs ls-files

# See LFS status
git lfs status

# Check LFS environment and configuration
git lfs env
```

---

## Repository transfers and deletion

### Transferring a repository

You can transfer a repository to a different workspace. This is useful when reorganising repositories across workspaces or when a project moves between teams.

**To transfer:**

1. Go to repository **Settings** > **Repository details**
2. Scroll to **Transfer repository**
3. Enter the destination workspace slug
4. Confirm the transfer

**What happens:**

- The repository moves to the new workspace with a new URL
- All existing Git remotes pointing to the old URL break
- Pull requests, commit comments and pipeline history move with the repository
- Watchers and access permissions are reset to the destination workspace's defaults
- Any Jira integration connections may need reconfiguration

> [!IMPORTANT]
> Notify all team members before transferring a repository. They need to update their Git remotes: `git remote set-url origin git@bitbucket.org:new-workspace/repo.git`

### Deleting a repository

Deletion is permanent and cannot be undone through the Bitbucket interface.

**To delete:**

1. Go to repository **Settings** > **Repository details**
2. Scroll to **Delete repository**
3. Type the repository name to confirm
4. Click **Delete**

Before deleting:

- Ensure you have a local clone if you want to keep the code
- Check that no CI/CD pipelines or deployment configurations reference this repository
- Notify any team members who use the repository
- Consider archiving (making read-only) rather than deleting if the code might be needed later

Bitbucket does not have a native archive feature like GitHub's repository archive. To make a repository effectively read-only, you can use branch permissions to block all pushes.

---

## Import a repository from another platform

Bitbucket can import repositories from GitHub, GitLab, or any accessible Git URL.

### Using the web interface

1. In your workspace, click **+** > **Import repository**
2. Choose the source (GitHub, GitLab, Bitbucket Server, or custom URL)
3. Enter the source repository URL
4. Authenticate if the source repository is private (GitHub/GitLab personal access token)
5. Choose the destination workspace and optionally a project
6. Name the new repository
7. Click **Import repository**

The import copies all commits, branches and tags. Pull requests, issues and wikis from the source platform are not imported - only the Git data.

### Using git push from the command line

The most reliable way to import any Git repository:

```bash
# Clone the source repository with full history
git clone --mirror https://github.com/user/repo.git

# Enter the mirrored directory
cd repo.git

# Add Bitbucket as a remote
git remote add bitbucket git@bitbucket.org:your-workspace/your-repo.git

# Push everything (all branches, tags, refs)
git push --mirror bitbucket
```

The `--mirror` flag ensures all branches, tags and refs are pushed, not just the checked-out branch.

---

## Repository variables and deployment environments

### Repository variables

Repository variables are key-value pairs stored securely in Bitbucket and made available to Bitbucket Pipelines during CI/CD runs. They are the right place to store API keys, passwords and configuration values that your build process needs.

**To add a repository variable:**

1. Go to the repository
2. Click **Repository settings** > **Repository variables**
3. Click **Add variable**
4. Enter the name and value
5. Toggle **Secured** to encrypt the value (recommended for sensitive data)
6. Click **Add**

Secured variables are write-only - once saved, the value is not visible in the UI. Unsecured variables are visible to any workspace member with access to the repository settings.

In a pipeline, reference variables like this:

```yaml
pipelines:
  default:
    - step:
        script:
          - echo "Deploying to $DEPLOYMENT_ENV"
          - aws s3 sync ./build s3://$S3_BUCKET_NAME --region $AWS_REGION
```

### Deployment environments

Bitbucket Pipelines supports deployment environments - named stages that your pipeline deploys to. Common environments: `Test`, `Staging`, `Production`.

Environments serve two purposes:

1. They provide a visual deployment history in the Bitbucket interface
2. They can have environment-specific variables that override repository-level variables

**To create a deployment environment:**

1. Go to **Repository settings** > **Deployments**
2. Click **Add environment**
3. Name it (e.g. `Production`)
4. Add environment-specific variables if needed

In your pipeline:

```yaml
pipelines:
  branches:
    main:
      - step:
          name: Deploy to Production
          deployment: production
          script:
            - ./deploy.sh
```

The `deployment: production` key links this step to the Production environment and records the deployment in the deployments history.

> [!NOTE]
> Deployment environments in the Premium plan support **deployment permissions** - you can require a specific user to approve a deployment before the pipeline step runs. This provides a manual gate before production deployments.

---

## Try It Yourself

**Exercise 1 - Create a repository**

1. Sign in to Bitbucket
2. Click **+** in the sidebar > **Repository**
3. Create a repository called `git-unlocked-practice` in your personal workspace
4. Enable the README option
5. Set the access level to Private
6. Click **Create repository**

**Exercise 2 - Clone and make a change**

```bash
# Clone via SSH (if you have set up your SSH key)
git clone git@bitbucket.org:your-username/git-unlocked-practice.git

# Or via HTTPS
git clone https://bitbucket.org/your-username/git-unlocked-practice.git

# Enter the repository
cd git-unlocked-practice

# Create a new file
echo "# My practice repository" >> notes.md

# Stage, commit and push
git add notes.md
git commit -m "Add notes file"
git push
```

**Exercise 3 - Set up LFS**

```bash
# In your repository directory
git lfs install
git lfs track "*.png"
git add .gitattributes
git commit -m "Configure LFS for PNG files"
git push
```

**Exercise 4 - Create a project**

1. In your workspace, click **Projects** in the sidebar
2. Click **Create project**
3. Name it `Personal Projects` with key `PP`
4. Move your practice repository into this project from repository settings

**Exercise 5 - Add a repository variable**

1. In your repository, go to **Repository settings** > **Repository variables**
2. Add a variable called `PRACTICE_ENV` with value `development`
3. Do not mark it as secured (since it is not sensitive)
4. Note how the variable appears in the list

---

## Common Mistakes

**Creating a repository in the wrong workspace**

If you have multiple workspaces (personal and team), it is easy to accidentally create a repository in your personal workspace instead of the team workspace. Check the workspace selector when creating. Repositories can be transferred but it disrupts URLs and remotes.

**Adding a README when importing existing code**

If you create a repository with a README and then try to push an existing local repository, you get a rejection because the remote history diverges from your local history. Either create the repository without any initial files, or fetch and merge the remote README first.

**Not committing .gitattributes after setting up LFS tracking**

Running `git lfs track "*.png"` writes to `.gitattributes`. If you do not commit that file, LFS tracking is only configured locally. Other team members cloning the repository will not have LFS tracking set up. Always `git add .gitattributes && git commit -m "Configure LFS"` after adding tracking rules.

**Storing secrets as unsecured repository variables**

Unsecured repository variables are visible in plaintext in the repository settings to any user with admin access. Use secured variables for API keys, passwords and tokens. Secured variables are never shown after creation.

**Not updating Git remotes after a repository transfer or rename**

After renaming a repository or transferring it to a different workspace, all existing local clones have stale remote URLs. Running `git push` on a stale remote gives an error. Update remotes with `git remote set-url origin new-url`.

**Deleting a repository without a local backup**

Repository deletion is immediate and permanent. If you need the code, clone it first. If the repository might be needed later but you want to stop using it, consider leaving it private and archived via branch permissions rather than deleting it.

---

## Summary

Bitbucket organises repositories in a three-level hierarchy: workspaces contain optional projects which contain repositories. Understanding this structure is essential before creating repositories - put each repository in the right workspace and optionally the right project from the start.

Repositories are created via the web interface or the REST API. Clone using SSH (recommended, no repeated authentication) or HTTPS with API tokens. Repository settings control naming, branching model, merge strategies and visibility.

Projects group repositories within a workspace for organisation and batch permission management. Visibility (private or public) controls who can see the repository. Forking creates independent copies for contribution workflows.

Git LFS handles large binary files by replacing them in the repository with pointer files, with the actual content stored separately. LFS storage limits depend on the Bitbucket plan.

Repository variables and deployment environments are the foundation for secure, environment-aware CI/CD with Bitbucket Pipelines.

---

## Sources

- [Atlassian: Create a repository](https://support.atlassian.com/bitbucket-cloud/docs/create-a-repository/)
- [Atlassian: Git LFS documentation](https://support.atlassian.com/bitbucket-cloud/docs/use-git-lfs-with-bitbucket/)
- [Atlassian: Repository variables](https://support.atlassian.com/bitbucket-cloud/docs/variables-and-secrets/)
- [Atlassian: Deployment environments](https://support.atlassian.com/bitbucket-cloud/docs/set-up-and-monitor-deployments/)
- [Atlassian: Import a repository](https://support.atlassian.com/bitbucket-cloud/docs/import-a-repository-from-github-or-gitlab/)
- [Git LFS: Official documentation](https://git-lfs.com)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# Azure Repos

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Azure Repos is the code hosting service in Azure DevOps. It provides Git repositories with pull requests, code review, branch policies and webhooks - all the features you expect from a modern Git hosting platform. Azure Repos also supports Team Foundation Version Control (TFVC), a legacy centralised version control system carried forward from TFS. This file covers both, though the emphasis is on Git as the recommended choice for all new projects.

---

## Table of Contents

1. [Git vs TFVC: which to use](#git-vs-tfvc-which-to-use)
2. [Navigating Azure Repos](#navigating-azure-repos)
3. [Creating and managing repositories](#creating-and-managing-repositories)
4. [Cloning a repository](#cloning-a-repository)
5. [Branches](#branches)
6. [Pull requests](#pull-requests)
7. [Code review in pull requests](#code-review-in-pull-requests)
8. [Branch policies](#branch-policies)
9. [Repository settings](#repository-settings)
10. [Working with large files (LFS)](#working-with-large-files-lfs)
11. [Importing and migrating repositories](#importing-and-migrating-repositories)
12. [TFVC overview](#tfvc-overview)
13. [Try It Yourself](#try-it-yourself)
14. [Common Mistakes](#common-mistakes)
15. [Summary](#summary)
16. [Sources](#sources)

---

## Git vs TFVC: which to use

Azure Repos supports two version control systems. For any new project, the answer is unambiguous: **use Git**.

### Git

Git is a distributed version control system. Every developer has a full copy of the repository. Branching is cheap, fast and local. The entire developer ecosystem - tooling, tutorials, hosting platforms, IDE integrations - is built around Git. Azure Repos' Git hosting is fully compatible with any standard Git client.

### Team Foundation Version Control (TFVC)

TFVC is a centralised version control system inherited from Team Foundation Server. In TFVC, there is one central server with all the history. Developers check out files to work on them and check them back in. Branching is expensive and slow compared to Git.

TFVC exists in Azure Repos for one reason: organisations that migrated from TFS and have years of TFVC history they cannot easily migrate. If you are starting a new project, there is no reason to use TFVC. If you have an existing TFVC repository, consider migrating to Git (covered at the end of this file).

The rest of this file focuses on Git. TFVC is covered briefly at the end.

---

## Navigating Azure Repos

Azure Repos is accessed by clicking **Repos** in the left sidebar of any Azure DevOps project.

### The Repos sidebar

- **Files** - browse the repository tree, view file contents, edit files in the browser
- **Commits** - full commit history with filtering by author, date and branch
- **Pushes** - recent push events (one push can contain multiple commits)
- **Branches** - all branches with creation dates, last commit author and comparison to the default branch
- **Tags** - all tags in the repository
- **Pull requests** - create, review and manage pull requests

### The repository selector

If a project has multiple repositories, a dropdown at the top of the Repos page allows switching between them. The current repository name is shown in the breadcrumb.

### The default branch

The first repository in a project is created with a default branch named `main` (or `master` for older projects). The default branch is what you see when you open the Repos Files view without specifying a branch.

---

## Creating and managing repositories

### Creating a repository

1. Click **Repos** in the left sidebar
2. Click the repository dropdown at the top
3. Click **New repository**
4. Enter a name
5. Choose to initialise with a README (recommended for new repos), a `.gitignore` (select your language) and/or a licence
6. Click **Create**

### Multiple repositories in a project

One Azure DevOps project can contain multiple repositories. This supports both monorepo setups (one large repository with many components) and multi-repo setups (separate repositories per service or component).

To add another repository, click the repository dropdown > **New repository**.

### Deleting a repository

1. Go to **Project settings** > **Repositories**
2. Find the repository
3. Click the **...** menu > **Delete repository**
4. Type the repository name to confirm

Deletion is immediate and permanent. Ensure you have a local backup if the code is needed.

### Repository settings

Access repository settings from **Project settings** > **Repositories** > select a repository. Settings include:
- Default branch
- Fork policies
- Cross-repository policies
- Security (permissions for the repository)

---

## Cloning a repository

### Via HTTPS

Click the **Clone** button in the top right of the Repos Files page. Copy the HTTPS URL:

```bash
git clone https://dev.azure.com/your-org/your-project/_git/your-repo
```

When prompted:
- Username: your email address (or any string)
- Password: your Personal Access Token

### Via SSH

Click **Clone** > switch to the **SSH** tab. Copy the SSH URL:

```bash
git clone git@ssh.dev.azure.com:v3/your-org/your-project/your-repo
```

SSH requires your public key to be added to Azure DevOps (see [02-creating-an-account.md](02-creating-an-account.md)).

### Setting up the remote in an existing local repo

If you already have a local Git repository and want to push it to a new empty Azure Repos repository:

```bash
cd your-local-repo
git remote add origin https://dev.azure.com/your-org/your-project/_git/your-repo
git push -u origin main
```

---

## Branches

### Viewing branches

Click **Repos** > **Branches** to see all branches. The list shows:
- Branch name
- Last commit author and date
- How many commits ahead/behind the default branch
- Active pull requests for the branch
- Pipeline status for the branch

### Creating a branch

**Via the web interface:**
1. Go to **Repos** > **Branches**
2. Click **New branch**
3. Enter a name and select the source branch (what to branch from)
4. Click **Create branch**

**Via the command line:**
```bash
git checkout -b feature/my-new-feature
git push origin feature/my-new-feature
```

### Naming conventions

Azure DevOps has no enforced branch naming convention, but branch policies (covered below and in [08-branch-policies.md](08-branch-policies.md)) can require specific patterns. Common conventions:

```
feature/short-description
bugfix/issue-number-description
hotfix/critical-fix-description
release/v1.2.0
```

### Deleting branches

After a pull request is merged, the source branch can be deleted automatically (configured in pull request settings) or manually:

```bash
git push origin --delete feature/my-new-feature
```

Or via the web: **Repos** > **Branches** > hover over the branch > click the delete icon.

---

## Pull requests

A pull request (PR) is the code review mechanism in Azure Repos. It is a request to merge code from a source branch into a target branch, accompanied by a review process.

### Creating a pull request

**Method 1 - From the branch:**
1. Push your branch to Azure Repos
2. Go to **Repos** > **Branches**
3. Find your branch and click **Create pull request**

**Method 2 - From the PR list:**
1. Go to **Repos** > **Pull requests**
2. Click **New pull request**
3. Select the source and target branches

**Method 3 - After a push (banner suggestion):**
After pushing a branch, Azure DevOps shows a banner suggesting you create a PR. Click it.

**Method 4 - Via CLI:**
```bash
az repos pr create \
  --title "Add user authentication" \
  --description "Implements JWT-based auth for the API" \
  --source-branch feature/user-auth \
  --target-branch main
```

### Pull request form

**Title**: A concise description of what changes. This becomes the merge commit message.

**Description**: Context for reviewers. Explain why the change is needed, how to test it and any risks. Markdown is supported.

**Reviewers**: Add team members who should review. They receive notifications. Required reviewers are set via branch policies.

**Work items**: Link the PR to Azure Boards work items. This creates traceability from requirement to code. Type `#` followed by an ID to search and link.

**Labels**: Tags for categorising PRs (e.g. "hotfix", "breaking change").

**Draft**: Mark the PR as draft to signal it is not ready for review. Draft PRs cannot be completed.

### Pull request statuses

- **Active** - open and awaiting review
- **Draft** - work in progress, not ready for review
- **Completed** - merged
- **Abandoned** - closed without merging

---

## Code review in pull requests

### The pull request overview

The PR overview shows:
- A summary of what changed (files added, modified, deleted)
- Reviewer status (approvals, rejections, waiting)
- Work item links
- Build status from Pipelines
- Policy compliance status
- The merge button (active when all policies are satisfied)

### The Files tab

The Files tab shows the diff - what changed between the source and target branch. Switch between unified diff (additions and deletions in one view) and side-by-side diff.

Click the comment icon on any line to add an inline comment. Comments on specific lines appear in the diff at the relevant location and in the activity feed.

### Commenting

**Inline comments**: click the `+` icon on any line. Write your comment and click **Comment**. You can link to specific work items, @mention colleagues and format with Markdown.

**Overall comments**: Add a general comment from the **Overview** tab without linking it to a specific line.

**Comment status**: Comments can be marked as **Active** (unresolved), **Pending** (not yet submitted) or **Resolved** (addressed). PR policies can require all comments to be resolved before merging.

### Suggested changes

Azure Repos supports **suggested changes** - reviewers can propose specific code edits directly in the review. The author can apply the suggestion with a single click:

1. In the PR diff, click the comment icon on a line
2. Click the **Suggest changes** icon
3. Edit the code in the suggestion box
4. Click **Comment**

The author sees the suggestion with an **Apply** button that applies the change directly.

### Approving and requesting changes

**Approve**: Signal you have reviewed the code and it is ready to merge.

**Approve with suggestions**: Approve but note some optional improvements. The PR can be merged without implementing the suggestions.

**Wait for author**: Request changes before the PR can proceed. This is not a hard block (unlike GitHub's "Request changes" review state) but signals the author needs to address feedback.

**Reject**: Block the PR from merging until the rejection is overridden or the PR is updated.

### Vote states

Azure Repos uses a voting system:
- **Approved** - +1
- **Approved with suggestions** - +1 (with notes)
- **No vote** - neutral
- **Waiting for author** - -1 (soft block)
- **Rejected** - -10 (hard block when branch policy requires minimum votes)

---

## Branch policies

Branch policies enforce quality gates on specific branches. They are configured in **Project settings** > **Repositories** > select a branch. Full coverage of branch policies is in [08-branch-policies.md](08-branch-policies.md).

Key policies:

**Require a minimum number of reviewers**: PRs must have at least N approvals before merging. Can be configured to require specific reviewers and to reset approvals on new commits.

**Check for linked work items**: PRs must link to at least one Azure Boards work item. Enforces traceability.

**Check for comment resolution**: All PR comments must be resolved before merging.

**Build validation**: A pipeline must pass before the PR can be merged. This is the integration of Azure Repos and Azure Pipelines - the PR build runs automatically and its result is a policy requirement.

**Require merge strategy**: Restrict to specific merge types (merge commit, squash, rebase). Enforces consistent history strategy.

When branch policies are active, the **Complete** (merge) button is disabled until all policy requirements are satisfied. Policy bypass is available only to users with the "Bypass policies when completing pull requests" permission.

---

## Repository settings

### Repository-level settings

Access via **Project settings** > **Repositories** > select a repository:

**Policies**: Repository-level policies apply to all branches (not just the default). These differ from branch-specific policies.

**Security**: Who can read, contribute, manage policies, bypass policies, etc. Permissions can be set for individual users or security groups.

**Cross-repository policies**: Policies that apply across multiple repositories in the project simultaneously.

**Fork policies**: Control whether users can fork the repository and whether forks can submit pull requests back to the upstream.

### Branch-specific settings

Access via **Repos** > **Branches** > find the branch > click the **...** menu > **Branch policies**:

- Add required reviewers
- Configure build validation pipelines
- Set minimum reviewer count
- Configure merge strategy requirements
- Lock the branch (prevent all pushes, including from maintainers)

---

## Working with large files (LFS)

Git LFS (Large File Storage) handles binary files - images, videos, compiled executables, datasets - that do not diff well and would balloon repository size if stored normally.

### Setting up LFS

Install Git LFS:

🪟 **Windows**:
```bash
# Git LFS is included with Git for Windows
git lfs version
```

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

### Tracking file types

```bash
git lfs track "*.psd"
git lfs track "*.zip"
git lfs track "assets/**"
git add .gitattributes
git commit -m "Configure Git LFS"
```

Azure Repos supports Git LFS natively. LFS objects are stored in Azure's LFS storage associated with the repository.

### LFS storage and bandwidth

Azure Repos provides some LFS storage and bandwidth as part of the repository. Large volumes of LFS data may require additional Azure Blob Storage configuration. Check the Azure DevOps billing settings for current LFS storage pricing.

---

## Importing and migrating repositories

### Importing from another Git host

1. Create a new repository in Azure Repos (empty, no README)
2. Go to **Repos** > **Import a repository**
3. Select **Git** as the source type
4. Enter the clone URL of the source repository
5. If private, provide authentication (username/PAT)
6. Click **Import**

Azure DevOps clones the repository including all branches and tags. Pull requests and issues from the source platform are not imported.

### Importing from GitHub with full history

Using the command line for the most reliable import:

```bash
# Clone the source repository with full history
git clone --mirror https://github.com/user/repo.git

# Enter the mirrored directory
cd repo.git

# Add Azure Repos as remote
git remote add azure https://dev.azure.com/your-org/your-project/_git/your-repo

# Push everything
git push --mirror azure
```

### Migrating from TFVC to Git

Microsoft provides the `git-tfs` tool and a built-in migration option for TFVC to Git migration.

**Via the Azure DevOps web interface:**
1. Create a new Git repository in the same project
2. Go to **Repos** > **Import a repository**
3. Select **TFVC** as the source type
4. Enter the TFVC server path (e.g. `$/TeamProject/Main`)
5. Optionally migrate the history (this can be slow for large repositories)
6. Click **Import**

The migration converts TFVC changesets to Git commits. Branch history from TFVC branches maps to Git branches. The TFVC repository continues to exist alongside the new Git repository - teams can switch gradually.

---

## TFVC overview

For teams still using TFVC, here is the essential reference.

### TFVC concepts

**Workspace**: A mapping between server paths and local folders on a developer's machine. Unlike Git where every clone is a full repository, a TFVC workspace maps specific server paths to local directories.

**Check out**: In TFVC, you explicitly check out a file before editing it (in lock-based mode). In newer versions, multiple people can edit without explicit checkout (merge-on-checkin mode).

**Changeset**: The TFVC equivalent of a Git commit. A numbered snapshot of changes checked in at one time.

**Shelvesets**: Temporary storage for in-progress work, equivalent to Git stash. Shelvesets live on the server, not locally.

**Branches**: TFVC branches are expensive (they copy the entire tree server-side) and should be used sparingly compared to Git where branches are just pointers.

### Basic TFVC commands (Visual Studio or tf.exe)

🪟 **Windows** (tf.exe):
```
# Get latest version
tf get /recursive

# Check out a file
tf checkout MyFile.cs

# Check in changes
tf checkin /comment:"Fix null reference"

# View pending changes
tf status
```

TFVC is primarily operated through Visual Studio's Team Explorer or the `tf.exe` command-line tool on Windows. Cross-platform TFVC tooling is limited compared to Git.

---

## Try It Yourself

**Exercise 1 - Create a repository and push code**

1. In your Azure DevOps project, go to **Repos**
2. The default repository already exists - click **Clone** and clone it to your machine
3. Create a file:

```bash
cd your-repo
echo "# My Azure DevOps Practice Repo" > README.md
git add README.md
git commit -m "Initial commit"
git push origin main
```

4. Refresh the Repos Files page in Azure DevOps to see your commit

**Exercise 2 - Create a branch and pull request**

```bash
git checkout -b feature/add-contributing-guide
echo "# Contributing\n\nThank you for contributing!" > CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "Add contributing guide"
git push origin feature/add-contributing-guide
```

1. In Azure DevOps, go to **Repos** > **Branches**
2. Find your branch and click **Create pull request**
3. Fill in a title and description
4. Click **Create**

**Exercise 3 - Review a pull request**

1. Open the pull request you just created
2. Click the **Files** tab to see the diff
3. Hover over a line and click the comment icon to add a comment
4. Click **Overview** to see the PR summary
5. Click **Approve** (you are reviewing your own PR - in real life this would be a colleague)

**Exercise 4 - Explore branch policies**

1. Go to **Project settings** > **Repositories**
2. Select your repository > **Policies**
3. Click **Branch policies** for the `main` branch
4. Enable **Require a minimum number of reviewers**: set to 1
5. Go back to your PR and notice the policy requirement now shown

---

## Common Mistakes

**Not using SSH for Git operations**

HTTPS Git operations in Azure DevOps require a PAT every time (unless a credential manager is configured). SSH is zero-friction after initial setup. Set up SSH keys as a first step.

**Creating a repository with a README and then trying to push an existing repo**

If you initialise a repository with a README, it has a commit history. Pushing an existing local repo with its own history causes a rejection (histories have diverged). Either initialise empty or fetch and merge the remote README first.

**Confusing the project's default repository with the project itself**

When you create a project named "MyApp", Azure DevOps automatically creates a repository also named "MyApp" inside it. This repository is just the first repository in the project, not the project itself. The project can have other repositories with different names.

**Not linking work items to pull requests**

Branch policies can require work item links. Even when not required, linking PRs to work items creates traceability - you can see from a work item what code implemented it and from the code history what requirement a change served.

**Forgetting that TFVC branches are expensive**

TFVC branches server-copy the entire tree. Creating many TFVC branches is expensive in storage and performance. TFVC should have very few, long-lived branches. This is not a problem in Git.

---

## Summary

Azure Repos supports Git (recommended) and TFVC (legacy). All new projects should use Git.

Create repositories from the Repos interface or via the project creation wizard. One project supports multiple repositories. Clone using HTTPS (with a PAT) or SSH (with a public key added to your Azure DevOps security settings).

Pull requests combine the diff, code review comments, approval status, work item links and build status in one interface. Reviewers can approve, approve with suggestions or reject. Suggested changes allow reviewers to propose specific edits that authors can apply with one click.

Branch policies enforce quality gates: minimum reviewers, linked work items, resolved comments and required builds. Policies are configured per branch in Project settings.

TFVC is a legacy centralised version control system available in Azure Repos for backward compatibility. Migrate TFVC repositories to Git using the built-in import tool.

---

## Sources

- [Microsoft Learn: Azure Repos Git documentation](https://learn.microsoft.com/azure/devops/repos/git/)
- [Microsoft Learn: Create a repository](https://learn.microsoft.com/azure/devops/repos/git/create-new-repo)
- [Microsoft Learn: Pull requests](https://learn.microsoft.com/azure/devops/repos/git/pull-requests)
- [Microsoft Learn: Branch policies](https://learn.microsoft.com/azure/devops/repos/git/branch-policies)
- [Microsoft Learn: Git LFS in Azure Repos](https://learn.microsoft.com/azure/devops/repos/git/manage-large-files)
- [Microsoft Learn: Import a repository](https://learn.microsoft.com/azure/devops/repos/git/import-git-repository)
- [Microsoft Learn: Migrate from TFVC to Git](https://learn.microsoft.com/azure/devops/repos/git/migrate-from-tfvc-to-git)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

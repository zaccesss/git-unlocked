# GitLab CLI (glab)

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

`glab` is the official GitLab command-line interface. It lets you manage everything in GitLab - repositories, merge requests, issues, CI/CD pipelines, variables, runners, releases and more - without leaving your terminal. If you are already comfortable with Git on the command line, `glab` extends that workflow to GitLab platform operations: creating MRs, checking pipeline status, viewing live job logs, setting CI/CD variables and triggering deployments all happen through the same terminal you use for `git push`.

This file covers installation on all three operating systems, authentication with both GitLab.com and self-managed instances, every major command group with real examples, the AI integration via `glab duo`, and how `glab` compares to GitHub's `gh` CLI.

---

## Table of Contents

- [What is glab?](#what-is-glab)
- [Installation](#installation)
- [Authentication](#authentication)
- [Configuration](#configuration)
- [Repository commands](#repository-commands)
- [Merge request commands](#merge-request-commands)
- [Issue commands](#issue-commands)
- [CI/CD pipeline commands](#cicd-pipeline-commands)
- [Variable commands](#variable-commands)
- [Release commands](#release-commands)
- [Label commands](#label-commands)
- [Snippet commands](#snippet-commands)
- [Runner commands](#runner-commands)
- [Schedule commands](#schedule-commands)
- [API access](#api-access)
- [GitLab Duo from the CLI](#gitlab-duo-from-the-cli)
- [Using glab with self-managed GitLab](#using-glab-with-self-managed-gitlab)
- [Multiple instances](#multiple-instances)
- [Shell completions](#shell-completions)
- [glab vs gh - comparison](#glab-vs-gh---comparison)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is glab?

`glab` is the official open-source CLI for GitLab, maintained by GitLab itself at `gitlab.com/gitlab-org/cli`. It is MIT-licensed and written in Go. It supports GitLab.com, GitLab Dedicated and all self-managed GitLab instances running version 16.0 or later.

The project was originally created by community contributor Clement Sam in 2020 and later adopted by GitLab as the official CLI. Unlike some CLIs that are an afterthought, `glab` receives active development and stays in sync with GitLab platform changes.

**Key capabilities:**

- Create and manage merge requests from the terminal
- View and interact with CI/CD pipelines and job logs in real time
- Manage issues, labels, milestones and releases
- Set and list CI/CD variables
- Trigger pipelines and schedules
- Call the GitLab REST API directly with automatic authentication
- Authenticate multiple GitLab instances simultaneously
- Ask GitLab Duo AI questions from the terminal
- Open any GitLab resource directly in your browser

**Current version**: check with `glab --version`. Always install the latest - features are added frequently.

---

## Installation

### 🍎 Mac

```bash
# Using Homebrew (recommended)
brew install glab

# Verify
glab --version

# Update later with
brew upgrade glab
```

### 🪟 Windows

```powershell
# Using winget (Windows Package Manager, built into Windows 11 and later Windows 10)
winget install glab

# Using Scoop
scoop install glab

# Using Chocolatey
choco install glab

# Verify
glab --version
```

### 🐧 Linux

```bash
# Using Homebrew on Linux (works on any distro)
brew install glab

# Ubuntu/Debian - add GitLab's package repository
curl -L "https://packages.gitlab.com/install/repositories/gitlab/glab/script.deb.sh" | sudo bash
sudo apt-get install glab

# Fedora/RHEL/AlmaLinux
sudo dnf install glab

# Arch Linux
sudo pacman -S glab
# or via AUR:
yay -S glab-bin

# openSUSE
sudo zypper install glab

# Snap (Ubuntu/Linux with snapd)
sudo snap install glab

# Binary install (works on any Linux with no package manager)
# Download the latest release from:
# https://gitlab.com/gitlab-org/cli/-/releases
# Extract and move to /usr/local/bin/
curl -sL "https://gitlab.com/gitlab-org/cli/-/releases/permalink/latest/downloads/glab_linux_amd64.tar.gz" | tar xz
sudo mv bin/glab /usr/local/bin/
```

### Verifying the installation

```bash
glab --version
# Output: glab version X.Y.Z (YYYY-MM-DD)
```

---

## Authentication

Before using any `glab` commands, you must authenticate. Authentication stores credentials securely in your system keychain or a configuration file.

### Authenticate to GitLab.com

```bash
# Interactive authentication (opens your browser)
glab auth login

# You will be asked:
# What GitLab instance do you want to log into? → GitLab.com
# How would you like to authenticate? → Login with a token (or Web browser for OAuth)
```

**Browser-based OAuth** (easiest): select "Login with a web browser", your default browser opens, you approve access and `glab` stores the token automatically.

**Personal access token**: select "Paste an authentication token". In GitLab, go to Avatar -> Edit profile -> Access tokens, create a token with `api` and `write_repository` scopes, paste the token into the prompt.

### Verify authentication

```bash
glab auth status
# Output:
# gitlab.com
#   - Logged in to gitlab.com as USERNAME (KEYRING)
#   - Git operations for gitlab.com configured to use ssh protocol.
#   - Token: glpat-**********************
#   - Token scopes: api, write_repository
```

### Authenticate via environment variable

For CI/CD or scripting contexts where interactive authentication is not possible:

```bash
export GITLAB_TOKEN="glpat-your-token-here"
export GITLAB_HOST="gitlab.com"  # or your self-managed URL
glab auth status   # will show authenticated without running login
```

---

## Configuration

`glab` stores its configuration in `~/.config/glab-cli/config.yml` (global) and `.git/glab-cli` (per-repository).

### View and set configuration

```bash
# View all configuration
glab config list

# Set a global preference
glab config set --global editor "code --wait"   # VS Code as default editor
glab config set --global browser "firefox"
glab config set --global git_protocol ssh       # prefer SSH over HTTPS for Git operations

# Set per-repository (run inside a Git repo)
glab config set editor vim
```

### Useful configuration options

| Key            | Values              | Description                                 |
| -------------- | ------------------- | ------------------------------------------- |
| `editor`       | Any editor command  | Used when composing MR/issue descriptions   |
| `browser`      | Any browser command | Used when opening GitLab in the browser     |
| `git_protocol` | `ssh`, `https`      | Protocol for Git operations                 |
| `host`         | Domain name         | Default GitLab host for commands            |
| `no_prompt`    | `true`, `false`     | Disable interactive prompts (for scripting) |

---

## Repository commands

`glab repo` manages GitLab repositories (projects).

```bash
# Clone a repository (faster than copy-pasting from the UI)
glab repo clone namespace/project-name
glab repo clone gitlab-org/gitlab   # clone a public project

# Clone with SSH explicitly
glab repo clone --git-protocol ssh namespace/project

# Clone and change directory
glab repo clone namespace/project && cd project

# Create a new repository from the current directory
glab repo create
glab repo create --name "my-new-project" --group "my-group" --visibility private

# View repository information
glab repo view
glab repo view --web    # open in browser instead

# Fork a repository
glab repo fork gitlab-org/gitlab

# Fork into a specific namespace
glab repo fork gitlab-org/gitlab --namespace my-username

# Archive a repository
glab repo archive namespace/project

# Delete a repository (asks for confirmation)
glab repo delete namespace/project

# List repositories accessible to you
glab repo list
glab repo list --group my-group
glab repo list --mine                    # only your own
glab repo list --visibility public       # only public repos
glab repo list --per-page 50             # more results per page
```

---

## Merge Request Commands

`glab mr` is one of the most useful command groups. It covers the full MR lifecycle.

### Creating merge requests

```bash
# Create interactively (prompts for all fields)
glab mr create

# Create from the current branch, filling title/description from last commit
glab mr create --fill

# Create with specific fields
glab mr create \
  --title "feat: add dark mode toggle" \
  --description "Implements dark mode across the settings page.\n\nCloses #42." \
  --target-branch main \
  --assignee zaccess \
  --reviewer alice \
  --label "feature,frontend" \
  --milestone "Sprint 14"

# Create as a draft immediately
glab mr create --draft --fill

# Create and immediately open in browser
glab mr create --fill --web
```

### Viewing and listing merge requests

```bash
# List all open MRs in the current project
glab mr list

# List MRs assigned to you
glab mr list --assignee @me

# List MRs you are reviewing
glab mr list --reviewer @me

# List MRs with a specific label
glab mr list --label "needs-review"

# List closed MRs
glab mr list --state closed

# List all MRs (open and closed)
glab mr list --state all

# View a specific MR in the terminal
glab mr view 42

# Open MR 42 in your browser
glab mr view 42 --web

# View the MR for the current branch
glab mr view
```

### Checking out and working with MRs

```bash
# Check out an MR's branch locally (essential for testing before approving)
glab mr checkout 42

# After reviewing, approve the MR
glab mr approve 42

# Remove your approval
glab mr revoke 42

# Add a comment/note
glab mr note 42 --message "Looks good to me, minor nit on line 47 but not blocking"

# Merge the MR
glab mr merge 42

# Merge with options
glab mr merge 42 --squash --delete-source-branch

# Set to auto-merge (merges when pipeline passes and approvals are met)
glab mr merge 42 --auto-merge

# Rebase the MR's source branch on the target
glab mr rebase 42

# Close without merging
glab mr close 42

# Reopen a closed MR
glab mr reopen 42

# Update MR fields
glab mr update 42 --title "Updated title"
glab mr update 42 --label "bug,critical"
glab mr update 42 --milestone "Sprint 15"
glab mr update 42 --assignee alice
glab mr update 42 --draft   # mark as draft
glab mr update 42 --ready   # mark as ready for review
```

### Practical MR workflow from start to finish

```bash
# Start work on a new feature
git checkout -b feature/user-notifications

# ... make changes, commit ...
git add .
git commit -m "feat: add email notification system"
git push origin feature/user-notifications

# Create the MR from the terminal
glab mr create \
  --fill \
  --target-branch main \
  --label "feature" \
  --reviewer alice

# Check the pipeline
glab ci status

# Once the reviewer approves, merge
glab mr merge --squash --delete-source-branch
```

---

## Issue Commands

`glab issue` manages the full issue lifecycle.

```bash
# List open issues
glab issue list

# List issues assigned to you
glab issue list --assignee @me

# List issues with a specific label
glab issue list --label "bug"

# List issues in a specific milestone
glab issue list --milestone "Sprint 14"

# View a specific issue
glab issue view 42

# Open issue 42 in browser
glab issue view 42 --web

# Create an issue interactively
glab issue create

# Create with fields specified
glab issue create \
  --title "Bug: login fails on Safari 17" \
  --description "Steps to reproduce:\n1. Open Safari 17\n2. Navigate to /login\n3. Enter credentials\nExpected: success. Actual: page hangs." \
  --label "bug,frontend,priority::high" \
  --assignee @me \
  --milestone "Sprint 14"

# Close an issue
glab issue close 42

# Reopen an issue
glab issue reopen 42

# Update issue fields
glab issue update 42 --title "New title"
glab issue update 42 --label "bug,critical"
glab issue update 42 --assignee alice

# Add a comment to an issue
glab issue note 42 --message "Reproduced locally. Looking into it."

# Subscribe to notifications for an issue
glab issue subscribe 42

# Unsubscribe
glab issue unsubscribe 42

# Delete an issue (asks for confirmation)
glab issue delete 42

# Transfer an issue to another project
glab issue transfer 42 --dest-project other-group/other-project
```

---

## CI/CD Pipeline Commands

`glab ci` is arguably the most powerful part of `glab`, particularly the ability to follow live job logs directly in the terminal.

```bash
# View pipeline status for the current branch
glab ci status

# View pipeline status for a specific branch
glab ci status --branch main

# List all pipelines for the project
glab ci list

# Open the pipeline in your browser
glab ci view --web

# Interactive pipeline view (navigable list of stages and jobs)
glab ci view

# Trigger a new pipeline on the current branch
glab ci run

# Trigger a pipeline on a specific branch
glab ci run --branch main

# Trigger with variables
glab ci run --branch main --variables "DEPLOY_ENV=staging" --variables "DEBUG=true"

# Retry a specific pipeline
glab ci retry PIPELINE_ID

# Cancel a running pipeline
glab ci cancel PIPELINE_ID

# Follow job logs in real time (excellent for debugging)
glab ci trace JOB_ID

# Or trace by job name
glab ci trace --job "test-unit"

# List jobs in the most recent pipeline
glab ci list --branch main

# Get artifacts from a job
glab ci artifact JOB_ID --path "coverage/index.html"
```

### Practical debugging with `glab ci trace`

When a pipeline fails, the normal workflow is: navigate to GitLab in the browser, find the pipeline, click the failed job, wait for the logs to load. With `glab ci trace`, you get the same output immediately in your terminal:

```bash
# Push code
git push origin feature/new-api

# Immediately check pipeline status
glab ci status

# If something fails, trace the failing job
glab ci list   # find the job ID or name
glab ci trace --job "test-unit"

# Logs stream in real time until the job completes
```

---

## Variable Commands

`glab variable` manages CI/CD variables at the project level.

```bash
# List all variables in the current project
glab variable list

# List variables with values shown (masked variables show as [masked])
glab variable list --per-page 100

# Set a variable
glab variable set API_TOKEN "your-token-value"

# Set a masked variable (value hidden in job logs)
glab variable set API_TOKEN "your-token-value" --masked

# Set a protected variable (only available on protected branches)
glab variable set PROD_API_TOKEN "prod-value" --masked --protected

# Set a file-type variable (value written to a temp file in jobs)
glab variable set KUBECONFIG --value "$(cat ~/.kube/config)" --type file

# Update an existing variable
glab variable set EXISTING_VAR "new-value"  # same command, updates if exists

# Delete a variable
glab variable delete API_TOKEN

# Export all variables to a .env file (for local development)
glab variable list --output json | jq -r '.[] | .key + "=" + .value' > .env.gitlab
```

---

## Release Commands

`glab release` manages GitLab releases - tagged versions with downloadable assets and release notes.

```bash
# List all releases
glab release list

# View a specific release
glab release view v1.2.0

# Create a release
glab release create v1.2.0 \
  --name "Version 1.2.0" \
  --notes "This release adds dark mode, performance improvements and bug fixes."

# Create a release from a file for release notes
glab release create v1.2.0 \
  --name "Version 1.2.0" \
  --notes-file CHANGELOG.md

# Create a release and attach asset files
glab release create v1.2.0 \
  --name "Version 1.2.0" \
  --notes "Release notes here" \
  'dist/app-linux-amd64' \
  'dist/app-darwin-arm64' \
  'dist/app-windows-amd64.exe'

# Upload an asset to an existing release
glab release upload v1.2.0 dist/app-linux-arm64

# Delete a release
glab release delete v1.2.0
```

---

## Label Commands

```bash
# List all labels in the project
glab label list

# Create a new label
glab label create "priority::high" --color "#ee0701" --description "Must be done this sprint"

# Delete a label
glab label delete "old-label"
```

---

## Snippet Commands

`glab snippet` manages GitLab Snippets (the equivalent of GitHub Gists).

```bash
# List all personal snippets
glab snippet list

# List project snippets
glab snippet list --project namespace/project

# View a snippet
glab snippet view SNIPPET_ID

# Create a snippet from a file
glab snippet create --title "Useful bash script" --filename "setup.sh" < setup.sh

# Create a snippet from stdin
echo 'SELECT * FROM users LIMIT 10;' | glab snippet create --title "Test query" --filename "query.sql"

# Create a public snippet
glab snippet create --title "Public config" --visibility public < config.yaml

# Delete a snippet
glab snippet delete SNIPPET_ID
```

---

## Runner Commands

```bash
# List runners available to the current project
glab runner list

# List all instance runners (requires admin or appropriate access)
glab runner list --type instance_type

# View runner details
glab runner view RUNNER_ID

# Pause a runner
glab runner pause RUNNER_ID

# Resume a paused runner
glab runner start RUNNER_ID

# Delete a runner
glab runner delete RUNNER_ID
```

---

## Schedule Commands

Manage scheduled pipelines from the CLI.

```bash
# List all pipeline schedules
glab schedule list

# Create a new schedule
glab schedule create \
  --cron "0 2 * * *" \
  --description "Nightly build" \
  --branch main \
  --ref main

# Run a schedule immediately (trigger it now without waiting for the cron time)
glab schedule run SCHEDULE_ID

# Delete a schedule
glab schedule delete SCHEDULE_ID
```

---

## API Access

`glab api` is a general-purpose command that makes authenticated HTTP requests to the GitLab REST API. This is invaluable for operations that do not have a dedicated `glab` subcommand yet.

```bash
# GET request - get current user information
glab api /user

# GET with query parameters
glab api /projects --field per_page=50 --field membership=true

# GET a specific project
glab api /projects/:fullpath --field fullpath="gitlab-org/gitlab"

# POST request - create an issue
glab api POST /projects/:id/issues \
  --field title="New issue from CLI" \
  --field description="Created via glab api"

# PUT request - update a project
glab api PUT /projects/:id \
  --field description="Updated description"

# DELETE request
glab api DELETE /projects/:id/issues/42

# Paginate through all results (fetches all pages automatically)
glab api /projects --paginate

# Output as JSON (default) or use jq to process
glab api /user | jq .username

# Template the output
glab api /user --template '{{.name}} ({{.username}})'

# Use :id shorthand (uses the project ID from the current repository)
glab api /projects/:id/variables
```

### Common API use cases

```bash
# List all group members
glab api /groups/my-group/members --paginate

# Get all projects in a group
glab api /groups/my-group/projects --field per_page=100 --paginate | jq '.[].name'

# Get pipeline status
glab api /projects/:id/pipelines --field status=running

# Enable a feature flag
glab api PUT /projects/:id/feature_flags/my-feature --field status=enabled

# Trigger a pipeline with variables via API
glab api POST /projects/:id/pipeline \
  --field ref=main \
  --field "variables[][key]=DEPLOY_ENV" \
  --field "variables[][value]=production"
```

---

## GitLab Duo from the CLI

`glab duo ask` lets you ask GitLab Duo AI questions directly from the terminal.

```bash
# Ask a general programming question
glab duo ask "What is the difference between a merge commit and a squash commit?"

# Ask about a CI/CD concept
glab duo ask "How do I prevent duplicate pipelines in GitLab CI/CD?"

# Ask a shell scripting question
glab duo ask "How do I recursively find all .yml files modified in the last 7 days?"

# Ask about GitLab-specific features
glab duo ask "What is the syntax for a scoped label in GitLab?"
```

> [!NOTE]
> `glab duo ask` requires a GitLab Duo subscription (Duo Pro or above). The response is streamed to your terminal in real time. It does not have access to your local codebase or GitLab project context - for that, use Duo Chat in the web UI or an IDE extension where context can be provided.

---

## Using glab with Self-Managed GitLab

`glab` works identically with self-managed GitLab instances. The only difference is the instance URL.

### Authenticate to a self-managed instance

```bash
# Specify the self-managed URL during login
glab auth login --hostname gitlab.example.com

# Or provide a token directly
glab auth login --hostname gitlab.example.com --stdin <<< "YOUR_TOKEN"
```

### Set the default host

```bash
# Set a self-managed instance as the default
glab config set --global host gitlab.example.com
```

### Per-repository configuration

When you work in a repository cloned from a self-managed instance, `glab` detects the remote URL and uses the correct instance automatically. No additional configuration needed.

```bash
# Clone from self-managed instance
git clone git@gitlab.example.com:namespace/project.git
cd project

# glab commands now automatically use gitlab.example.com
glab mr list   # queries gitlab.example.com, not gitlab.com
glab ci status
```

---

## Multiple Instances

`glab` can be authenticated to multiple GitLab instances simultaneously. This is useful for developers who work with both GitLab.com and a company self-managed instance.

```bash
# Authenticate to GitLab.com
glab auth login

# Authenticate to a self-managed instance (at the same time)
glab auth login --hostname company.gitlab.example.com

# Check all authenticated instances
glab auth status
# Output:
# gitlab.com
#   - Logged in to gitlab.com as alice (KEYRING)
#
# company.gitlab.example.com
#   - Logged in to company.gitlab.example.com as alice.smith (KEYRING)

# Switch the default host
glab config set --global host company.gitlab.example.com

# Or use --hostname flag for a specific command
glab mr list --hostname gitlab.com
```

When working inside a cloned repository, `glab` automatically uses the instance that matches the repository's remote URL, regardless of the configured default host. The `--hostname` flag overrides this.

---

## Shell Completions

Enable tab completion for `glab` commands, subcommands and flags.

### 🍎🐧 Bash

```bash
# Add to ~/.bashrc or ~/.bash_profile
source <(glab completion -s bash)

# Or install permanently
glab completion -s bash > /usr/local/etc/bash_completion.d/glab
```

### 🍎 Zsh

```bash
# Add to ~/.zshrc
source <(glab completion -s zsh)

# Or install permanently (if using oh-my-zsh or similar)
glab completion -s zsh > "${fpath[1]}/_glab"
```

### 🪟 PowerShell

```powershell
# Add to your PowerShell profile ($PROFILE)
glab completion -s powershell | Out-String | Invoke-Expression
```

After adding to your profile and restarting the terminal, press `Tab` after any `glab` command to see available completions.

---

## glab vs gh - Comparison

Both `glab` and GitHub's `gh` are excellent platform CLIs with similar command structures. If you use both platforms, you will find the muscle memory transfers well.

| Feature                 | glab (GitLab)                   | gh (GitHub)                            |
| ----------------------- | ------------------------------- | -------------------------------------- |
| **Merge/pull requests** | `glab mr create/list/merge`     | `gh pr create/list/merge`              |
| **Issues**              | `glab issue create/list/close`  | `gh issue create/list/close`           |
| **Releases**            | `glab release create`           | `gh release create`                    |
| **API access**          | `glab api /endpoint`            | `gh api /endpoint`                     |
| **CI/CD pipelines**     | `glab ci status/view/trace/run` | `gh run list/view` (limited)           |
| **Live job logs**       | `glab ci trace` (real-time)     | `gh run view --log` (after completion) |
| **CI/CD variables**     | `glab variable list/set/delete` | No equivalent                          |
| **Pipeline schedules**  | `glab schedule create/run`      | No equivalent                          |
| **Runners**             | `glab runner list/pause`        | No equivalent                          |
| **AI assistant**        | `glab duo ask`                  | `gh copilot suggest/explain`           |
| **Multiple instances**  | Yes, native support             | Yes, native support                    |
| **Authentication**      | PAT or OAuth                    | PAT or OAuth                           |

**The key advantage of `glab`**: the `glab ci` commands are significantly more capable than GitHub's `gh run` commands. Being able to stream live job logs with `glab ci trace`, trigger pipelines with variables and manage CI/CD variables all from the terminal makes `glab` genuinely useful for DevOps workflows, not just code review workflows.

**The key advantage of `gh`**: GitHub Copilot CLI integration is more deeply developed than `glab duo ask`. `gh copilot suggest` and `gh copilot explain` are richer features than the current `glab duo ask`.

---

## Try It Yourself

**Exercise: Full workflow from branch to merged MR**

```bash
# Step 1: Clone a project you have access to
glab repo clone YOUR_USERNAME/YOUR_PROJECT
cd YOUR_PROJECT

# Step 2: Create a branch and make a change
git checkout -b test/glab-workflow-demo
echo "# glab demo" >> README.md
git add README.md
git commit -m "docs: add glab demo note to README"
git push origin test/glab-workflow-demo

# Step 3: Create an MR from the terminal
glab mr create \
  --title "docs: add glab demo note" \
  --description "Testing the glab CLI workflow. Adds a note to README." \
  --target-branch main \
  --fill

# Step 4: Check the pipeline
glab ci status

# Step 5: View the MR
glab mr list
glab mr view   # view the MR for the current branch

# Step 6: Approve and merge
glab mr approve
glab mr merge --squash --delete-source-branch

# Step 7: Verify the merge
glab mr list --state merged
```

**Exercise: Debug a failing pipeline**

```bash
# Trigger a pipeline
glab ci run --branch main

# Watch it in real time
glab ci view

# If a job fails, trace it
glab ci list  # find the failing job name
glab ci trace --job "test-unit"  # stream the logs live
```

**Exercise: Use the API**

```bash
# Get your user information
glab api /user | jq '{username: .username, name: .name, email: .email}'

# List your projects
glab api /projects --field owned=true --field per_page=10 | jq '.[].name_with_namespace'

# Create an issue via the API
glab api POST /projects/:id/issues \
  --field title="Test issue from API" \
  --field description="Created with glab api"
```

---

## Common Mistakes

**Forgetting to authenticate before using glab.** Every `glab` command requires authentication. If you get "authentication required" errors, run `glab auth login` and check `glab auth status` to confirm it worked.

**Using the wrong host when working with multiple instances.** When authenticated to both GitLab.com and a self-managed instance, `glab` uses the instance matching the current repository's remote URL automatically. If you are not inside a Git repository, it uses the default host. Check with `glab auth status` if commands are hitting the wrong instance.

**Not using `--fill` when creating MRs.** The `--fill` flag auto-populates the MR title and description from your most recent commit message and branch name. Without it you type everything manually. For a branch with a single descriptive commit, `glab mr create --fill` is the fastest way to create an MR.

**Using `--paginate` and not piping to jq.** `glab api /projects --paginate` can return hundreds of projects as a massive JSON array. Always pipe to `jq` to extract the specific fields you need: `glab api /projects --paginate | jq '.[].name'`.

**Not installing shell completions.** `glab` has extensive tab completion for commands, flags, branch names and more. Without it, you have to remember flags manually. Add the completion script to your shell profile on first install.

**Assuming `glab ci trace` shows logs for historical runs.** `glab ci trace` is designed for live streaming of running jobs. For already-completed jobs, the logs are still available but the real-time streaming aspect does not apply. Use `glab ci view` to navigate to the pipeline and view historical logs.

**Confusing `glab mr approve` with merging.** `glab mr approve` marks the MR as approved by you - it is equivalent to clicking the Approve button in the UI. It does not merge. Use `glab mr merge` to actually merge after it is approved.

---

## Summary

- `glab` is GitLab's official MIT-licensed CLI, written in Go, supporting GitLab.com and all self-managed instances
- Install with `brew install glab` (Mac/Linux), `winget install glab` (Windows), or package managers on any Linux distro
- Authenticate with `glab auth login` - supports OAuth browser flow and personal access tokens
- Multiple instances can be authenticated simultaneously; `glab` auto-detects the right one from the repository's remote URL
- **`glab mr`**: create (`--fill` for quick creation), list, view, approve, checkout, merge, rebase, update - full MR lifecycle from the terminal
- **`glab issue`**: create, list, view, close, note, subscribe - full issue management
- **`glab ci`**: status, view (interactive), trace (live logs), run (trigger), retry, cancel - the most powerful CI/CD CLI available
- **`glab variable`**: list, set (with `--masked`, `--protected`), delete - manage CI/CD secrets
- **`glab release`**: create, list, view, upload assets
- **`glab api`**: call any GitLab REST endpoint with automatic auth, pagination support and jq-compatible JSON output
- **`glab duo ask`**: ask GitLab Duo AI questions from the terminal
- Shell completions available for Bash, Zsh, Fish and PowerShell: `glab completion -s bash`
- Key advantage over GitHub's `gh`: `glab ci trace` for live job logs and full CI/CD variable management have no equivalent in `gh`

---

## Sources and Further Reading

- [glab repository](https://gitlab.com/gitlab-org/cli) - source code and issues
- [glab documentation](https://gitlab.com/gitlab-org/cli/-/tree/main/docs/source) - full command reference
- [glab releases](https://gitlab.com/gitlab-org/cli/-/releases) - latest version and changelog
- [GitLab REST API reference](https://docs.gitlab.com/api/rest/) - endpoints available via `glab api`
- [Personal access tokens](https://docs.gitlab.com/user/profile/personal_access_tokens/) - creating tokens for authentication
- [GitLab Duo CLI](https://docs.gitlab.com/user/gitlab_duo/gitlab_duo_chat/) - Duo Chat and CLI integration

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

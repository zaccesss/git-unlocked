# The Azure DevOps CLI

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

The Azure DevOps CLI is an extension to the Azure CLI (`az`) that brings Azure DevOps operations to the command line. It covers all five Azure DevOps services: creating and managing repositories, querying and updating work items, triggering and monitoring pipelines, managing Artifacts feeds and administering organisations and projects. For developers who live in the terminal, the CLI eliminates the need to switch to the browser for common Azure DevOps tasks.

---

## Table of Contents

1. [Installation](#installation)
2. [Authentication](#authentication)
3. [Default organisation and project](#default-organisation-and-project)
4. [Repos commands](#repos-commands)
5. [Pull request commands](#pull-request-commands)
6. [Boards commands](#boards-commands)
7. [Pipelines commands](#pipelines-commands)
8. [Artifacts commands](#artifacts-commands)
9. [User and permission management](#user-and-permission-management)
10. [Extension management](#extension-management)
11. [Using the CLI in scripts](#using-the-cli-in-scripts)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## Installation

The Azure DevOps CLI is not a standalone tool - it is an extension to the Azure CLI. Install the Azure CLI first, then add the DevOps extension.

### Install the Azure CLI

🪟 **Windows** (via winget):
```powershell
winget install Microsoft.AzureCLI
```

🪟 **Windows** (via MSI installer):
Download from [aka.ms/installazurecliwindows](https://aka.ms/installazurecliwindows) and run the installer.

🍎 **Mac** (via Homebrew):
```bash
brew update && brew install azure-cli
```

🐧 **Linux** (Debian/Ubuntu):
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

🐧 **Linux** (RHEL/Fedora/CentOS):
```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo dnf install azure-cli
```

Verify installation:
```bash
az --version
```

### Install the Azure DevOps extension

```bash
az extension add --name azure-devops
```

Verify:
```bash
az devops --version
```

### Updating the extension

```bash
az extension update --name azure-devops
```

---

## Authentication

### Interactive login

```bash
az login
```

This opens a browser window for Microsoft account or Azure AD authentication. After successful login, the CLI stores credentials and reuses them for subsequent commands.

For devices without a browser:
```bash
az login --use-device-code
```

This outputs a code and URL. Open the URL on any device, enter the code and complete authentication.

### Personal Access Token authentication

For automated scripts or CI/CD contexts where interactive login is not possible:

```bash
az devops login --token YOUR_PAT
```

Or set the `AZURE_DEVOPS_EXT_PAT` environment variable:

🪟 **Windows** (PowerShell):
```powershell
$env:AZURE_DEVOPS_EXT_PAT = "your-pat-value"
```

🍎 **Mac / 🐧 Linux**:
```bash
export AZURE_DEVOPS_EXT_PAT="your-pat-value"
```

When `AZURE_DEVOPS_EXT_PAT` is set, all `az devops` commands use the PAT automatically without interactive prompts.

### Checking current login

```bash
az account show
az devops configure --list
```

### Logging out

```bash
az logout
```

---

## Default organisation and project

Most Azure DevOps CLI commands require `--org` (organisation URL) and `--project` (project name). Setting defaults avoids typing them on every command.

### Setting defaults

```bash
az devops configure \
  --defaults organization=https://dev.azure.com/your-org \
             project=your-project
```

### Checking current defaults

```bash
az devops configure --list
```

Output:
```
[defaults]
organization = https://dev.azure.com/your-org
project = your-project
```

### Overriding defaults per command

Even with defaults set, you can override for a specific command:

```bash
az devops project list --org https://dev.azure.com/other-org
```

---

## Repos commands

### List repositories

```bash
az repos list
```

With JSON output (useful for scripting):
```bash
az repos list --output json
```

Filtered with JMESPath:
```bash
az repos list --query "[].{name:name, id:id}" --output table
```

### Get a specific repository

```bash
az repos show --repository my-repo
```

### Create a repository

```bash
az repos create --name new-service --detect false
```

`--detect false` prevents the CLI from trying to detect the repository from the current directory's Git remote.

### Delete a repository

```bash
az repos delete --id REPO-UUID --yes
```

Get the UUID from `az repos list --query "[?name=='repo-name'].id" --output tsv`.

### Import a repository from another Git host

```bash
az repos import create \
  --git-source-url https://github.com/user/repo.git \
  --repository new-repo \
  --requires-authorization false
```

For private sources, add `--requires-authorization true` and `--git-service-endpoint-id YOUR-SERVICE-ENDPOINT-ID`.

---

## Pull request commands

### List pull requests

```bash
# Open PRs in current project
az repos pr list --status active

# PRs targeting a specific branch
az repos pr list --target-branch main --status active

# All PRs (including completed)
az repos pr list --status all
```

### Create a pull request

```bash
az repos pr create \
  --title "Add user authentication" \
  --description "Implements JWT-based auth for the API endpoint. Closes AB#123." \
  --source-branch feature/user-auth \
  --target-branch main \
  --reviewers "alice@example.com bob@example.com" \
  --work-items 123 456 \
  --auto-complete false \
  --squash false \
  --delete-source-branch true
```

### Show pull request details

```bash
az repos pr show --id 42
```

### Update a pull request

```bash
# Change title
az repos pr update --id 42 --title "Updated title"

# Add reviewers
az repos pr reviewer add --id 42 --reviewers "charlie@example.com"

# Add work items
az repos pr work-item add --id 42 --work-items 789
```

### Approve a pull request

```bash
az repos pr set-vote --id 42 --vote approve
```

Vote options: `approve`, `approve-with-suggestions`, `reset`, `wait-for-author`, `reject`.

### Complete (merge) a pull request

```bash
az repos pr complete --id 42
```

With merge options:
```bash
az repos pr complete \
  --id 42 \
  --merge-commit-message "Squash: Add user authentication" \
  --squash true \
  --delete-source-branch true
```

### Abandon a pull request

```bash
az repos pr abandon --id 42
```

### List PR policies

```bash
az repos pr policy list --id 42
```

### Queue a PR policy evaluation

Retry a build validation that failed:
```bash
az repos pr policy queue --id 42 --evaluation-id POLICY-EVALUATION-ID
```

---

## Boards commands

### Work items

```bash
# Show a work item by ID
az boards work-item show --id 123

# Create a work item
az boards work-item create \
  --type "User Story" \
  --title "Implement user profile page" \
  --assigned-to "alice@example.com" \
  --area "MyProject\Backend" \
  --iteration "MyProject\Sprint 5" \
  --description "As a user, I want to view my profile so that I can update my information."

# Update a work item
az boards work-item update \
  --id 123 \
  --state "Active" \
  --assigned-to "bob@example.com"

# Delete a work item
az boards work-item delete --id 123 --yes

# Add a comment to a work item
az boards work-item comment add --id 123 --text "Starting implementation - linked PR #42"
```

### Queries

```bash
# List saved queries
az boards query list

# Run a saved query by ID
az boards query run --id QUERY-ID

# Run an inline WIQL query
az boards query run --wiql "SELECT [System.Id], [System.Title] FROM WorkItems WHERE [System.State] = 'Active' AND [System.AssignedTo] = @me"
```

### Iterations (sprints)

```bash
# List iterations
az boards iteration project list

# Show current team iteration
az boards iteration team list --team "MyTeam"
```

---

## Pipelines commands

### List pipelines

```bash
az pipelines list
```

### Show pipeline details

```bash
az pipelines show --name "MyApp-CI"
# or by ID:
az pipelines show --id 5
```

### Run a pipeline

```bash
# Run on default branch
az pipelines run --name "MyApp-CI"

# Run on a specific branch
az pipelines run --name "MyApp-CI" --branch feature/my-feature

# Run with variables
az pipelines run \
  --name "MyApp-Deploy" \
  --branch main \
  --variables targetEnvironment=staging releaseVersion=1.2.3
```

### Pipeline runs

```bash
# List recent runs
az pipelines runs list --pipeline-name "MyApp-CI" --result succeeded --top 10

# Show a specific run
az pipelines runs show --id RUN-ID

# Show run logs
az pipelines runs artifact download \
  --run-id RUN-ID \
  --artifact-name drop \
  --path ./downloaded
```

### Cancel a running pipeline

```bash
az pipelines runs cancel --id RUN-ID
```

### Variables and variable groups

```bash
# List pipeline variables
az pipelines variable list --pipeline-name "MyApp-CI"

# Create a pipeline variable
az pipelines variable create \
  --pipeline-name "MyApp-CI" \
  --name "MY_VAR" \
  --value "my-value" \
  --secret false

# Update a variable
az pipelines variable update \
  --pipeline-name "MyApp-CI" \
  --name "MY_VAR" \
  --value "new-value"

# Delete a variable
az pipelines variable delete --pipeline-name "MyApp-CI" --name "MY_VAR"

# List variable groups
az pipelines variable-group list

# Create a variable group
az pipelines variable-group create \
  --name "MySecrets" \
  --variables "API_URL=https://api.example.com" "LOG_LEVEL=info" \
  --authorize true

# Add a secret variable to an existing group
az pipelines variable-group variable create \
  --group-id GROUP-ID \
  --name "API_KEY" \
  --value "secret-value" \
  --secret true
```

### Agents and agent pools

```bash
# List agent pools
az pipelines agent pool list

# List agents in a pool
az pipelines agent list --pool-id POOL-ID

# Show agent details
az pipelines agent show --pool-id POOL-ID --agent-id AGENT-ID
```

---

## Artifacts commands

### Feeds

```bash
# List feeds
az artifacts feed list

# Show a feed
az artifacts feed show --feed my-feed

# Create a feed
az artifacts feed create --name new-feed

# Delete a feed
az artifacts feed delete --feed old-feed --yes
```

### Universal Packages

```bash
# Publish a Universal Package
az artifacts universal publish \
  --organization https://dev.azure.com/your-org \
  --project your-project \
  --scope project \
  --feed my-feed \
  --name my-package \
  --version 1.0.0 \
  --description "My package" \
  --path ./package-contents/

# Download a Universal Package
az artifacts universal download \
  --organization https://dev.azure.com/your-org \
  --project your-project \
  --scope project \
  --feed my-feed \
  --name my-package \
  --version 1.0.0 \
  --path ./downloaded/
```

---

## User and permission management

### Organisation user management

```bash
# List users in the organisation
az devops user list

# Add a user
az devops user add \
  --email-id "newdev@example.com" \
  --license-type express \
  --send-email-invite true

# Update user licence
az devops user update \
  --user "newdev@example.com" \
  --license-type stakeholder

# Remove a user from the organisation
az devops user remove --user "leavingdev@example.com" --yes
```

### Teams

```bash
# List teams in a project
az devops team list

# Create a team
az devops team create --name "New Backend Team"

# Add a member to a team
az devops team member add \
  --team "Backend Team" \
  --members "alice@example.com bob@example.com"

# List team members
az devops team member list --team "Backend Team"
```

### Service endpoints (service connections)

```bash
# List service endpoints
az devops service-endpoint list

# Show a specific endpoint
az devops service-endpoint show --id ENDPOINT-ID

# Delete an endpoint
az devops service-endpoint delete --id ENDPOINT-ID --yes
```

---

## Extension management

```bash
# List installed extensions
az devops extension list

# Install an extension
az devops extension install \
  --publisher-id "ms-devlabs" \
  --extension-id "team-retrospectives"

# Disable an extension
az devops extension disable \
  --publisher-id "ms-devlabs" \
  --extension-id "team-retrospectives"

# Enable an extension
az devops extension enable \
  --publisher-id "ms-devlabs" \
  --extension-id "team-retrospectives"

# Uninstall an extension
az devops extension uninstall \
  --publisher-id "ms-devlabs" \
  --extension-id "team-retrospectives" \
  --yes
```

---

## Using the CLI in scripts

The CLI is most powerful in scripts for automation, reporting and bulk operations.

### Output formats

```bash
# Table format (human-readable)
az repos list --output table

# JSON (for further processing)
az repos list --output json

# Tab-separated values (for shell processing)
az repos list --query "[].name" --output tsv

# YAML
az repos list --output yaml
```

### JMESPath queries

Filter and transform output with JMESPath:

```bash
# List only repository names
az repos list --query "[].name" --output tsv

# Get a specific repo's clone URL
az repos show --repository my-repo --query "remoteUrl" --output tsv

# List open PRs with title and author
az repos pr list --status active \
  --query "[].{id:pullRequestId, title:title, author:createdBy.displayName}" \
  --output table

# Count active work items assigned to me
az boards query run \
  --wiql "SELECT [System.Id] FROM WorkItems WHERE [System.AssignedTo] = @me AND [System.State] = 'Active'" \
  --query "length(workItems)"
```

### Automation script examples

**Script: Create a standard set of repositories for a new microservice**

```bash
#!/bin/bash
SERVICE_NAME=$1

az devops configure --defaults organization=https://dev.azure.com/myorg project=MyProject

# Create the main service repository
az repos create --name "${SERVICE_NAME}-api"

# Create the infrastructure repository
az repos create --name "${SERVICE_NAME}-infra"

# Create a work item to track the new service setup
az boards work-item create \
  --type "Feature" \
  --title "Set up ${SERVICE_NAME} microservice" \
  --description "New microservice: ${SERVICE_NAME}"

echo "Created repositories and work item for ${SERVICE_NAME}"
```

**Script: List all failed builds from the last 24 hours**

```bash
#!/bin/bash
az devops configure --defaults organization=https://dev.azure.com/myorg project=MyProject

FAILED_RUNS=$(az pipelines runs list \
  --result failed \
  --query "[].{pipeline:definition.name, run:id, branch:sourceBranch, time:finishTime}" \
  --output table)

echo "Failed builds in the last 24 hours:"
echo "$FAILED_RUNS"
```

**Script: Close all work items in a completed sprint**

```bash
#!/bin/bash
SPRINT_NAME="Sprint 23"

# Get work items in the sprint
ITEMS=$(az boards query run \
  --wiql "SELECT [System.Id] FROM WorkItems WHERE [System.IterationPath] UNDER 'MyProject\\${SPRINT_NAME}' AND [System.State] <> 'Closed'" \
  --query "workItems[].id" \
  --output tsv)

# Close each item
for ID in $ITEMS; do
  az boards work-item update --id "$ID" --state "Closed"
  echo "Closed work item $ID"
done
```

### Using the CLI in Azure Pipelines

The Azure CLI (and DevOps extension) can be used directly in pipelines via the `AzureCLI@2` task. This is useful for pipeline operations that reference themselves (e.g. a pipeline that queries its own run history):

```yaml
- task: AzureCLI@2
  displayName: 'Check if this pipeline passed recently'
  inputs:
    azureSubscription: 'MyAzureConnection'
    scriptType: bash
    scriptLocation: inlineScript
    addSpnToEnvironment: true
    inlineScript: |
      az extension add --name azure-devops
      az devops configure --defaults organization=$(System.CollectionUri) project=$(System.TeamProject)

      RECENT_PASS=$(az pipelines runs list \
        --pipeline-name "$(Build.DefinitionName)" \
        --result succeeded \
        --top 1 \
        --query "length(@)")

      echo "Recent successful runs: $RECENT_PASS"
```

---

## Try It Yourself

**Exercise 1 - Install and authenticate**

```bash
# Install Azure CLI (follow OS-specific steps above)
# Add DevOps extension
az extension add --name azure-devops

# Login
az login

# Set defaults
az devops configure --defaults \
  organization=https://dev.azure.com/YOUR-ORG \
  project=YOUR-PROJECT

# Verify
az devops configure --list
```

**Exercise 2 - Explore repos via CLI**

```bash
# List all repositories
az repos list --output table

# Show your repo's details
az repos show --repository YOUR-REPO-NAME

# Get clone URLs
az repos show --repository YOUR-REPO-NAME --query "{ssh:sshUrl, https:remoteUrl}" --output table
```

**Exercise 3 - Create a work item from the CLI**

```bash
az boards work-item create \
  --type "Task" \
  --title "Test the Azure DevOps CLI" \
  --description "Learning to use the az devops CLI" \
  --assigned-to YOUR-EMAIL

# Note the ID in the output, then show it:
az boards work-item show --id THE-ID
```

**Exercise 4 - Trigger a pipeline and monitor it**

```bash
# List available pipelines
az pipelines list --output table

# Run a pipeline
az pipelines run --name YOUR-PIPELINE-NAME --branch main

# Note the run ID in output, then watch it:
az pipelines runs show --id THE-RUN-ID
```

**Exercise 5 - Write a script to list your active work items**

```bash
az boards query run \
  --wiql "SELECT [System.Id], [System.Title], [System.State] FROM WorkItems WHERE [System.AssignedTo] = @me AND [System.State] = 'Active'" \
  --query "workItems[*]" \
  --output table
```

---

## Common Mistakes

**Forgetting to set defaults**

Every command requires `--org` and `--project` unless defaults are set. Commands without these parameters produce an error. Run `az devops configure --defaults organization=https://... project=...` once per terminal session (or add to your shell profile).

**Using the wrong output format in scripts**

`--output table` is human-readable but not machine-parseable. In scripts, use `--output json` with JMESPath queries or `--output tsv` for simple string values. Parsing table output with regex is fragile and unnecessary.

**Not setting AZURE_DEVOPS_EXT_PAT in CI/CD**

In CI/CD scripts, interactive login is not available. Set `AZURE_DEVOPS_EXT_PAT` as a pipeline variable (marked as secret) and the CLI uses it automatically.

**Assuming the DevOps extension is installed**

The Azure DevOps extension must be installed separately from the Azure CLI. In CI/CD pipelines that use the CLI, add `az extension add --name azure-devops` before any `az devops` commands. The extension may not be installed on all Microsoft-hosted agent images.

**Using `--output table` in JMESPath queries**

JMESPath `--query` transforms the JSON structure before rendering. Using `--output table` after a complex JMESPath query sometimes renders poorly. Use `--output table` for simple queries and `--output json` to inspect results from complex queries.

---

## Summary

The Azure DevOps CLI (`az devops`) is an extension to the Azure CLI providing command-line access to all Azure DevOps services. Install with `az extension add --name azure-devops`. Authenticate with `az login` (interactive) or `AZURE_DEVOPS_EXT_PAT` (automated). Set defaults with `az devops configure --defaults org=... project=...` to avoid repeating them on every command.

Key command groups: `az repos` for repository and pull request management, `az boards` for work items and queries, `az pipelines` for CI/CD operations, `az artifacts` for package feeds and `az devops` for organisation-level management.

Use `--output json` with `--query` JMESPath expressions for scripting. Use `--output table` for interactive use. Use `--output tsv` for simple string extraction in shell scripts.

The CLI shines in automation scripts: bulk work item updates, reporting, creating standard project setups and integrating Azure DevOps operations into shell workflows. In CI/CD pipelines, use the `AzureCLI@2` task with `az extension add --name azure-devops` to enable CLI operations within pipelines.

---

## Sources

- [Microsoft Learn: Azure DevOps CLI reference](https://learn.microsoft.com/cli/azure/devops)
- [Microsoft Learn: Get started with the CLI](https://learn.microsoft.com/azure/devops/cli/get-started)
- [Microsoft Learn: Sign in with the CLI](https://learn.microsoft.com/azure/devops/cli/sign-in-with-pat)
- [Microsoft Learn: `az repos` reference](https://learn.microsoft.com/cli/azure/repos)
- [Microsoft Learn: `az boards` reference](https://learn.microsoft.com/cli/azure/boards)
- [Microsoft Learn: `az pipelines` reference](https://learn.microsoft.com/cli/azure/pipelines)
- [Microsoft Learn: `az artifacts` reference](https://learn.microsoft.com/cli/azure/artifacts)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

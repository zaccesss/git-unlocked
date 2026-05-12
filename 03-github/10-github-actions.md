# GitHub Actions

**Difficulty:** 🔴 Advanced | **Time:** 60 minutes

GitHub Actions is GitHub's built-in automation and CI/CD platform. It lets you run code automatically in response to events in your repository - running tests when code is pushed, deploying when a release is tagged, sending notifications when an issue is opened, or anything else you can describe in a script. It is one of the most powerful features on GitHub and understanding it thoroughly will transform how you build, test and ship software.

---

## Table of Contents

- [What is GitHub Actions?](#what-is-github-actions)
- [Core concepts](#core-concepts)
- [Workflow file structure](#workflow-file-structure)
- [Triggers (on:)](#triggers-on)
- [Jobs](#jobs)
- [Steps](#steps)
- [Runners](#runners)
- [Actions and the marketplace](#actions-and-the-marketplace)
- [Contexts and expressions](#contexts-and-expressions)
- [Environment variables](#environment-variables)
- [Secrets and variables](#secrets-and-variables)
- [The GITHUB_TOKEN](#the-github_token)
- [Artefacts](#artefacts)
- [Caching dependencies](#caching-dependencies)
- [Matrix builds](#matrix-builds)
- [Concurrency and cancellation](#concurrency-and-cancellation)
- [Reusable workflows](#reusable-workflows)
- [Composite actions](#composite-actions)
- [Environments and deployment protection](#environments-and-deployment-protection)
- [Workflow permissions](#workflow-permissions)
- [Debugging workflows](#debugging-workflows)
- [Pricing and usage limits](#pricing-and-usage-limits)
- [Common workflow examples](#common-workflow-examples)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitHub Actions?

GitHub Actions was launched in October 2018 and made generally available in November 2019. It is a platform for automating software workflows directly inside GitHub - no separate CI/CD service required.

**What it can do:**

- Run automated tests on every pull request
- Deploy applications when code is merged to `main`
- Publish packages when a new release is tagged
- Lint and format code on every commit
- Send Slack notifications when a deployment succeeds or fails
- Automatically label pull requests based on changed files
- Generate and publish documentation
- Schedule regular maintenance tasks
- Respond to any event that happens in your GitHub repository

**How it works in one sentence:** You write YAML files that describe what to run, when to run it and where to run it. GitHub executes them for you on cloud machines (or your own machines if you prefer).

---

## Core Concepts

Before writing any YAML, understand these six concepts:

**Workflow** - a YAML file in `.github/workflows/` that defines an automated process. A repository can have multiple workflows. Each workflow is independent.

**Event** - something that triggers a workflow. Examples: a push to a branch, a pull request being opened, a scheduled time, a manual click.

**Job** - a group of steps that run on the same machine. Jobs in a workflow run in parallel by default. Dependencies between jobs can be configured.

**Step** - a single task within a job. Steps run sequentially within a job. A step either runs a shell command or executes an action.

**Action** - a reusable unit of code that performs a specific task. Actions can be written by GitHub, the community or you. They are the building blocks of workflows.

**Runner** - the machine where a job executes. GitHub provides hosted runners (cloud VMs). You can also configure your own self-hosted runners.

```
Workflow
  ├── triggered by Event
  └── contains Jobs
        ├── Job 1 (runs on Runner)
        │     ├── Step 1 (uses Action or runs command)
        │     ├── Step 2
        │     └── Step 3
        └── Job 2 (runs on Runner, parallel to Job 1)
              ├── Step 1
              └── Step 2
```

---

## Workflow File Structure

Workflows are YAML files stored in `.github/workflows/`. The filename is up to you - it has no functional significance. Common names: `ci.yml`, `deploy.yml`, `release.yml`, `test.yml`.

**A complete minimal workflow:**

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Run tests
        run: npm test
```

**Top-level keys:**

`name:` - the workflow's display name in GitHub's Actions tab. Optional but recommended.

`run-name:` - the name of each workflow run. Can use expressions. Example: `run-name: Deploy by @${{ github.actor }}`.

`on:` - the events that trigger the workflow. Required.

`env:` - environment variables available to all jobs in the workflow.

`defaults:` - default settings for all jobs (e.g., default shell).

`concurrency:` - concurrency group settings to prevent duplicate runs.

`permissions:` - GITHUB_TOKEN permission overrides.

`jobs:` - the jobs to run. Required.

---

## Triggers (on:)

The `on:` key defines what triggers the workflow. GitHub supports over 40 event types.

### Push events

```yaml
on:
  push:
    branches: [main, develop] # only these branches
    branches-ignore: [feature/*] # all branches except these
    tags: ["v*"] # only when pushing tags
    paths: ["src/**", "tests/**"] # only when these paths change
    paths-ignore: ["docs/**", "*.md"] # ignore these paths
```

### Pull request events

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened, closed, labeled]
    branches: [main]
```

**`pull_request` vs `pull_request_target`:**

`pull_request` - runs in the context of the PR's source branch. Has no access to repository secrets for PRs from forks. Safe to use for untrusted code.

`pull_request_target` - runs in the context of the base branch. Has access to secrets even from forks. **Use with extreme caution** - only run trusted code from the base repository, never check out and run code from the PR itself without careful validation.

### Schedule

```yaml
on:
  schedule:
    - cron: "0 9 * * 1-5" # every weekday at 9am UTC
    - cron: "0 0 * * 0" # every Sunday at midnight UTC
```

Cron syntax: `minute hour day-of-month month day-of-week`. Minimum interval is every 5 minutes. Scheduled workflows only run on the default branch.

### Manual trigger

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Environment to deploy to"
        required: true
        type: choice
        options: [staging, production]
      version:
        description: "Version to deploy"
        required: false
        type: string
      debug:
        description: "Enable debug mode"
        type: boolean
        default: false
```

`workflow_dispatch` adds a **Run workflow** button to the Actions tab. Input types: `string`, `boolean`, `choice`, `number`, `environment`.

### Other common triggers

```yaml
on:
  release:
    types: [published, created] # when a release is published

  issues:
    types: [opened, labeled] # when an issue is opened

  issue_comment:
    types: [created] # when someone comments

  workflow_run:
    workflows: [CI] # when another workflow completes
    types: [completed]

  repository_dispatch: # custom webhook trigger
    types: [deploy]
```

### Multiple triggers

```yaml
on: [push, pull_request]   # simple list

on:                         # with configuration
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:        # also allow manual trigger
```

---

## Jobs

Jobs are the top-level units of execution in a workflow. Each job runs on its own fresh runner instance.

```yaml
jobs:
  build:
    name: Build the project # display name (optional)
    runs-on: ubuntu-latest # which runner to use
    timeout-minutes: 30 # cancel if takes longer than this
    continue-on-error: false # stop the workflow if this job fails

    steps:
      - run: npm run build

  test:
    name: Run tests
    runs-on: ubuntu-latest
    needs: build # wait for build to succeed first

    steps:
      - run: npm test

  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    needs: [build, test] # wait for BOTH jobs

    if: github.ref == 'refs/heads/main' # only run on main branch

    steps:
      - run: ./deploy.sh
```

**Job dependencies with `needs:`**

By default all jobs run in parallel. Use `needs:` to create sequential dependencies. `needs: [build, test]` means this job only starts after both `build` and `test` succeed.

**Conditional jobs with `if:`**

```yaml
if: github.event_name == 'push'
if: github.ref == 'refs/heads/main'
if: contains(github.event.pull_request.labels.*.name, 'deploy')
if: failure()           # only run if previous jobs failed
if: always()            # always run regardless of failures
```

**Job outputs**

Pass data between jobs:

```yaml
jobs:
  build:
    outputs:
      version: ${{ steps.get-version.outputs.version }}
    steps:
      - id: get-version
        run: echo "version=$(cat VERSION)" >> $GITHUB_OUTPUT

  deploy:
    needs: build
    steps:
      - run: echo "Deploying version ${{ needs.build.outputs.version }}"
```

---

## Steps

Steps are the individual tasks within a job. They run sequentially in order.

```yaml
steps:
  - name: Step name (shown in logs)
    id: my-step # optional ID for referencing outputs
    uses: actions/checkout@v4 # use a pre-built action
    with: # inputs for the action
      ref: main

  - name: Run a command
    run: echo "Hello World"

  - name: Multi-line command
    run: |
      echo "First command"
      npm install
      npm test

  - name: Conditional step
    if: success()
    run: echo "Previous steps succeeded"

  - name: Step that can fail without failing the job
    continue-on-error: true
    run: npm run optional-check

  - name: Set output for later steps
    id: compute
    run: echo "result=42" >> $GITHUB_OUTPUT

  - name: Use previous step's output
    run: echo "The result was ${{ steps.compute.outputs.result }}"
```

**`run:` vs `uses:`**

`run:` executes shell commands. The default shell on Linux/Mac is `bash`. On Windows it is `pwsh` (PowerShell).

`uses:` runs a pre-built action. The format is `owner/repo@ref` - for example `actions/checkout@v4`.

**Setting the shell explicitly:**

```yaml
steps:
  - name: Windows PowerShell
    shell: pwsh
    run: Write-Host "Hello from PowerShell"

  - name: Python script
    shell: python
    run: |
      import os
      print(os.getcwd())
```

---

## Runners

Runners are the machines where jobs execute. GitHub provides hosted runners and you can also use your own.

### GitHub-hosted runners

| Label                           | OS                  | CPU | RAM   |
| ------------------------------- | ------------------- | --- | ----- |
| `ubuntu-latest` (ubuntu-24.04)  | Ubuntu 24.04        | 4   | 16 GB |
| `ubuntu-22.04`                  | Ubuntu 22.04        | 4   | 16 GB |
| `ubuntu-24.04-arm`              | Ubuntu 24.04 ARM    | 4   | 16 GB |
| `windows-latest` (windows-2025) | Windows Server 2025 | 4   | 16 GB |
| `windows-2022`                  | Windows Server 2022 | 4   | 16 GB |
| `macos-latest` (macos-15)       | macOS 15            | 3   | 7 GB  |
| `macos-14`                      | macOS 14            | 3   | 7 GB  |
| `macos-13`                      | macOS 13 (Intel)    | 3   | 14 GB |

> [!NOTE]
> Private repository jobs on standard runners get reduced resources: Linux and Windows get 2 CPU and 8 GB RAM. Public repositories always get the full specification.

**Larger runners (Team and Enterprise plans only):**

Available for Linux and Windows: 8-core to 96-core, up to 384 GB RAM. macOS up to 12-core. ARM64 up to 64-core. GPU runners. Always billed per minute.

**Pre-installed software:**

GitHub-hosted runners come with hundreds of tools pre-installed: Node.js (multiple versions), Python, Java, .NET, Go, Ruby, PHP, Docker, kubectl, Terraform, AWS CLI, Azure CLI and more. Full software lists are in the `actions/runner-images` repository.

### Self-hosted runners

Run jobs on your own machines for: access to private networks, specific hardware requirements, custom software, cost control on high-volume jobs.

**Install a self-hosted runner:**

1. Repository/Organisation → Settings → Actions → Runners → New self-hosted runner
2. Select your OS
3. Follow the download and configuration commands shown
4. Run the runner application

**Use in a workflow:**

```yaml
runs-on: self-hosted
# or with specific labels:
runs-on: [self-hosted, linux, x64, gpu]
```

**Runner Groups (Enterprise):** Organise self-hosted runners into groups with access policies.

**Actions Runner Controller (ARC):** Kubernetes-based auto-scaling for self-hosted runners. Scales from zero to hundreds of runners based on queue depth.

---

## Actions and the Marketplace

Actions are the building blocks of workflows. Instead of writing every task from scratch, you use pre-built actions from the marketplace.

**Finding actions:**

- GitHub Actions Marketplace: [github.com/marketplace?type=actions](https://github.com/marketplace?type=actions) - 20,000+ actions
- Search directly in the workflow editor
- Browse the `actions` organisation on GitHub for official actions

**Using an action:**

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-node@v4
    with:
      node-version: "20"
      cache: "npm"
```

**Action versioning - always pin to a version:**

```yaml
uses: actions/checkout@v4          # semver tag (recommended for official actions)
uses: actions/checkout@v4.1.1      # exact tag
uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683  # full SHA (most secure)
```

> [!TIP]
> For third-party actions from the community, pin to a **full commit SHA** rather than a tag. Tags can be moved to point to different (malicious) commits. A full SHA is immutable.
>
> ```yaml
> uses: some-org/some-action@abc1234def5678... # SHA pin
> ```

**Commonly used official actions:**

| Action                         | Purpose                                       |
| ------------------------------ | --------------------------------------------- |
| `actions/checkout@v4`          | Check out repository code                     |
| `actions/setup-node@v4`        | Install Node.js and configure npm/yarn cache  |
| `actions/setup-python@v5`      | Install Python and configure pip/poetry cache |
| `actions/setup-java@v4`        | Install Java (multiple distributions)         |
| `actions/setup-go@v5`          | Install Go                                    |
| `actions/setup-dotnet@v4`      | Install .NET                                  |
| `actions/upload-artifact@v4`   | Upload files for later jobs or download       |
| `actions/download-artifact@v4` | Download previously uploaded artefacts        |
| `actions/cache@v4`             | Cache dependencies between runs               |
| `actions/github-script@v7`     | Run JavaScript using the GitHub API           |
| `docker/login-action@v3`       | Log in to a container registry                |
| `docker/build-push-action@v6`  | Build and push Docker images                  |

---

## Contexts and Expressions

Contexts provide access to information about the workflow run, repository, event and environment. Access them with `${{ context.property }}`.

**Key contexts:**

**`github` context** - information about the event and repository:

```yaml
${{ github.repository }}     # owner/repo-name
${{ github.ref }}            # refs/heads/main
${{ github.sha }}            # full commit SHA
${{ github.actor }}          # username who triggered the workflow
${{ github.event_name }}     # push, pull_request, etc.
${{ github.run_number }}     # incrementing run counter
${{ github.run_id }}         # unique ID for this run
${{ github.workspace }}      # path to the checkout directory
${{ github.token }}          # equivalent to secrets.GITHUB_TOKEN
```

**`env` context** - environment variables set in the workflow:

```yaml
env:
  NODE_ENV: production
steps:
  - run: echo ${{ env.NODE_ENV }}
```

**`secrets` context** - encrypted secrets:

```yaml
${{ secrets.MY_SECRET }}
${{ secrets.GITHUB_TOKEN }}
```

**`vars` context** - non-sensitive configuration variables:

```yaml
${{ vars.DEPLOY_REGION }}
```

**`steps` context** - outputs from previous steps:

```yaml
${{ steps.my-step-id.outputs.result }}
${{ steps.my-step-id.outcome }} # success, failure, cancelled, skipped
```

**`needs` context** - outputs and results from dependency jobs:

```yaml
${{ needs.build.outputs.version }}
${{ needs.build.result }} # success, failure, cancelled, skipped
```

**`matrix` context** - current matrix values:

```yaml
${{ matrix.os }}
${{ matrix.node-version }}
```

**Expressions:**

```yaml
# Comparison
if: github.ref == 'refs/heads/main'
if: github.event_name != 'pull_request'

# Functions
if: contains(github.ref, 'release')
if: startsWith(github.ref, 'refs/tags/')
if: endsWith(github.actor, '-bot')

# Status functions (in if: conditions)
if: success()    # previous steps/jobs succeeded
if: failure()    # previous steps/jobs failed
if: always()     # always run
if: cancelled()  # workflow was cancelled
```

---

## Environment Variables

**Setting environment variables:**

At the workflow level (available to all jobs):

```yaml
env:
  NODE_ENV: production
  API_URL: https://api.example.com
```

At the job level (available to all steps in the job):

```yaml
jobs:
  build:
    env:
      BUILD_TARGET: release
```

At the step level:

```yaml
steps:
  - name: Run with env
    env:
      DEBUG: true
    run: npm run build
```

**Setting environment variables dynamically:**

Append to `$GITHUB_ENV` to make a variable available to all subsequent steps:

```bash
echo "VERSION=2.1.0" >> $GITHUB_ENV
echo "DEPLOY_TIME=$(date -u +%Y%m%dT%H%M%S)" >> $GITHUB_ENV
```

Then reference in subsequent steps: `${{ env.VERSION }}`

**On Windows:**

```powershell
echo "VERSION=2.1.0" >> $env:GITHUB_ENV
```

---

## Secrets and Variables

### Secrets

Secrets are encrypted values stored in GitHub. They appear as `***` in logs and are never exposed in plain text.

**Create repository secrets:** Settings → Secrets and variables → Actions → New repository secret.

**Create organisation secrets:** Organisation → Settings → Secrets and variables → Actions → New organisation secret. Set which repositories can access the secret.

**Create environment secrets:** Settings → Environments → [environment] → Add secret. Only accessible to workflows deploying to that environment.

**Use in workflows:**

```yaml
steps:
  - name: Deploy
    env:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    run: aws deploy ...
```

**Secret inheritance for forks:** By default, secrets are not available to workflows triggered by pull requests from forks. This prevents malicious PRs from reading secrets. For workflows that need secrets from fork PRs, use `pull_request_target` with extreme caution.

### Configuration variables

For non-sensitive values that vary between environments (region names, feature flags, deployment targets), use **variables** rather than secrets. Variables are visible in logs.

**Create:** Settings → Secrets and variables → Actions → Variables tab.

**Use:**

```yaml
run: echo "Deploying to ${{ vars.DEPLOY_REGION }}"
```

---

## The GITHUB_TOKEN

Every workflow run automatically receives a `GITHUB_TOKEN` - a short-lived token scoped to the repository that triggered the run. It expires when the job finishes.

**Default permissions (as of 2023):** Read-only for most permissions. Write permission only for `contents` when creating releases.

**Common uses:**

```yaml
steps:
  - name: Create a release comment
    uses: actions/github-script@v7
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
      script: |
        github.rest.issues.createComment({
          issue_number: context.issue.number,
          owner: context.repo.owner,
          repo: context.repo.repo,
          body: 'Deployment succeeded!'
        })
```

**Grant additional permissions:**

```yaml
permissions:
  contents: write # needed to create releases
  pull-requests: write # needed to comment on PRs
  issues: write # needed to create/close issues
  packages: write # needed to publish packages
  id-token: write # needed for OIDC authentication

jobs:
  deploy:
    permissions:
      contents: read # override at job level
```

> [!IMPORTANT]
> Keep permissions minimal. Grant only the permissions each job actually needs. Overly permissive tokens are a security risk.

**GITHUB_TOKEN limitations:**

- Cannot trigger other workflow runs (prevents infinite loops). Use a Personal Access Token (PAT) or GitHub App token if you need to trigger workflows from workflows.
- Cannot push to protected branches (unless bypass is configured).

---

## Artefacts

Artefacts are files produced by a workflow job that you want to share with other jobs or download after the run.

**Upload an artefact:**

```yaml
steps:
  - name: Build
    run: npm run build

  - name: Upload build output
    uses: actions/upload-artifact@v4
    with:
      name: build-output
      path: dist/
      retention-days: 7 # default 90, max 400
      if-no-files-found: error # error, warn, or ignore
```

**Download in another job:**

```yaml
jobs:
  build:
    steps:
      - uses: actions/upload-artifact@v4
        with:
          name: build-output
          path: dist/

  deploy:
    needs: build
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: build-output
          path: dist/

      - run: ./deploy.sh dist/
```

**Download via GitHub UI:**

Artefacts appear on the workflow run summary page and can be downloaded as a zip file. They are retained for the configured number of days (default 90).

**Unzipped artefacts (new in 2025):**

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: my-file
    path: output.html
    compression-level: 0 # no compression - file downloads unzipped
```

---

## Caching Dependencies

Caching stores the results of expensive operations (installing dependencies) between runs, dramatically speeding up workflows.

**Basic cache:**

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Cache node modules
    uses: actions/cache@v4
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-node-

  - run: npm ci
```

The `key` is a hash of the lock file. When dependencies change, the hash changes, the cache misses and a fresh install occurs. The new cache is saved for next time.

**Built-in caching in setup actions:**

The official setup actions support caching with a single `cache:` input:

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: "20"
    cache: "npm" # automatically caches npm/yarn/pnpm

- uses: actions/setup-python@v5
  with:
    python-version: "3.12"
    cache: "pip" # automatically caches pip

- uses: actions/setup-java@v4
  with:
    java-version: "21"
    cache: "maven" # automatically caches maven
```

**Cache limits:** 10 GB per repository. Entries unused for 7 days are evicted. Caches are scoped to the repository and branch.

---

## Matrix Builds

Matrix builds run the same job with different configurations simultaneously. Useful for testing across multiple operating systems, language versions or environment variables.

```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [18, 20, 22]
      fail-fast: false # continue other jobs if one fails
      max-parallel: 6 # run at most 6 jobs at once

    runs-on: ${{ matrix.os }}

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

This creates 9 jobs (3 OS × 3 Node versions), all running in parallel.

**Adding specific combinations:**

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [18, 20]
    include:
      - os: ubuntu-latest
        node: 22 # add this extra combination
    exclude:
      - os: windows-latest
        node: 18 # remove this specific combination
```

**Maximum matrix size:** 256 jobs per workflow.

---

## Concurrency and Cancellation

By default, multiple workflow runs can execute simultaneously. Use concurrency groups to control this.

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

This groups runs by workflow name and ref (branch). When a new push triggers a workflow, any previous in-progress run for the same branch is cancelled. Saves CI minutes by not running outdated builds.

**Per-job concurrency:**

```yaml
jobs:
  deploy:
    concurrency:
      group: production-deployment
      cancel-in-progress: false # queue instead of cancel
```

Setting `cancel-in-progress: false` queues the new run instead of cancelling the current one. Good for deployments where you want to ensure the latest code deploys but do not want to interrupt an in-progress deployment abruptly.

---

## Reusable Workflows

Reusable workflows let you define a workflow once and call it from other workflows. Reduces duplication across repositories.

**Create a reusable workflow:**

```yaml
# .github/workflows/deploy-reusable.yml
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
    secrets:
      DEPLOY_KEY:
        required: true
    outputs:
      deployment-url:
        description: "The deployed URL"
        value: ${{ jobs.deploy.outputs.url }}

jobs:
  deploy:
    runs-on: ubuntu-latest
    outputs:
      url: ${{ steps.deploy.outputs.url }}
    steps:
      - id: deploy
        run: echo "url=https://${{ inputs.environment }}.example.com" >> $GITHUB_OUTPUT
```

**Call a reusable workflow:**

```yaml
# .github/workflows/production.yml
jobs:
  deploy-production:
    uses: YOUR_ORG/YOUR_REPO/.github/workflows/deploy-reusable.yml@main
    with:
      environment: production
    secrets:
      DEPLOY_KEY: ${{ secrets.PRODUCTION_DEPLOY_KEY }}
```

**Nesting limit:** Up to 4 levels of reusable workflow nesting.

**Passing all secrets:**

```yaml
secrets: inherit # pass all calling workflow's secrets to the reusable workflow
```

---

## Composite Actions

Composite actions let you bundle multiple steps into a single reusable action. Unlike reusable workflows, they can be used as a step within any job.

**Create in your repository at `.github/actions/my-action/action.yml`:**

```yaml
name: "Setup and Test"
description: "Install dependencies and run tests"
inputs:
  node-version:
    description: "Node.js version"
    required: false
    default: "20"
outputs:
  test-result:
    description: "Test result"
    value: ${{ steps.test.outputs.result }}

runs:
  using: "composite"
  steps:
    - uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}

    - name: Install
      shell: bash
      run: npm ci

    - name: Test
      id: test
      shell: bash
      run: |
        npm test
        echo "result=passed" >> $GITHUB_OUTPUT
```

**Use in a workflow:**

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: ./.github/actions/my-action
    with:
      node-version: "22"
```

---

## Environments and Deployment Protection

Environments represent deployment targets (staging, production). They add protection rules that must be satisfied before a job deploying to that environment can run.

**Create an environment:** Settings → Environments → New environment.

**Protection rules:**

- **Required reviewers:** Up to 6 people or teams must approve before the job runs. A manual approval gate.
- **Wait timer:** Delay the deployment by 0 to 43,200 minutes (30 days) after all other requirements pass.
- **Deployment branches/tags:** Restrict which branches or tags can deploy to this environment.
- **Custom deployment protection rules:** Call an external service (via GitHub App) to make deployment decisions.

**Use in a workflow:**

```yaml
jobs:
  deploy-production:
    environment:
      name: production
      url: https://example.com # shown in PR and Actions UI
    runs-on: ubuntu-latest
    steps:
      - run: ./deploy-to-production.sh
```

When this job runs, GitHub pauses it if reviewers are required and sends them a notification. They review and approve (or reject) in the GitHub UI.

**Environment secrets** scoped to this environment:

```yaml
env:
  PROD_DATABASE_URL: ${{ secrets.PRODUCTION_DB_URL }} # only available for production environment
```

---

## Workflow Permissions

**Repository-level default permissions:** Settings → Actions → General → Workflow permissions.

Options:

- **Read repository contents and packages permissions** (recommended default)
- **Read and write permissions** (overly broad for most use cases)

**Best practice:** Set the repository default to read-only. Grant specific permissions in individual workflows using the `permissions:` key.

```yaml
permissions: {}            # grant no permissions at all (most restrictive)

permissions:
  contents: read
  pull-requests: write
  issues: write

permissions: read-all      # read-only for everything
permissions: write-all     # write for everything (avoid)
```

Available permission scopes: `actions`, `attestations`, `checks`, `contents`, `deployments`, `discussions`, `id-token`, `issues`, `packages`, `pages`, `pull-requests`, `repository-projects`, `security-events`, `statuses`.

---

## Debugging Workflows

**Enable debug logging:**

Add these repository secrets (set to `true`):

- `ACTIONS_RUNNER_DEBUG` - enables runner diagnostic logging
- `ACTIONS_STEP_DEBUG` - enables step debug logging

**Re-run with debug logging:**

On the workflow run page → **Re-run jobs** → **Enable debug logging** checkbox.

**Print debugging information:**

```yaml
steps:
  - name: Debug info
    run: |
      echo "GitHub ref: ${{ github.ref }}"
      echo "Event: ${{ github.event_name }}"
      env | sort

  - name: Dump all contexts
    env:
      GITHUB_CONTEXT: ${{ toJSON(github) }}
    run: echo "$GITHUB_CONTEXT"
```

**Workflow run logs:**

Click any workflow run in the Actions tab to see logs. Each step expands to show full output. Failed steps are highlighted in red.

**tmate action for SSH debugging (advanced):**

The `mxschmitt/action-tmate` action gives you an SSH session into the runner for interactive debugging:

```yaml
- name: Setup tmate session
  uses: mxschmitt/action-tmate@v3
  if: failure() # only open if a previous step failed
```

---

## Pricing and Usage Limits

### Public repositories

**GitHub Actions is completely free and unlimited for public repositories** on standard runners. No minutes limit, no storage limit for logs.

### Private repositories

| Plan       | Free minutes/month | Free storage |
| ---------- | ------------------ | ------------ |
| Free       | 2,000              | 500 MB       |
| Pro        | 3,000              | 1 GB         |
| Team       | 3,000              | 2 GB         |
| Enterprise | 50,000             | 50 GB        |

**Per-minute rates for private repositories (from January 2026):**

| Runner         | Rate per minute |
| -------------- | --------------- |
| Linux 2-core   | $0.006          |
| Windows 2-core | $0.010          |
| macOS 3-core   | $0.062          |

> [!NOTE]
> GitHub reduced Actions pricing by up to 39% on 1 January 2026. macOS runners remain the most expensive due to Apple silicon licensing costs.

**Concurrent job limits:**

| Plan       | Concurrent jobs |
| ---------- | --------------- |
| Free       | 20              |
| Pro        | 40              |
| Team       | 60              |
| Enterprise | 500             |

Self-hosted runners are free to use regardless of plan - you only pay for your own infrastructure.

### Storage limits

Artefacts and caches are stored in GitHub's infrastructure. Beyond the free tier, storage is billed at $0.008/GB/day. Set spending limits in your billing settings.

---

## Common Workflow Examples

### Node.js CI

```yaml
name: Node.js CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [18, 20, 22]

    steps:
      - uses: actions/checkout@v4

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: "npm"

      - run: npm ci
      - run: npm run build --if-present
      - run: npm test
```

### Python CI with coverage

```yaml
name: Python CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
          cache: "pip"

      - run: pip install -r requirements.txt
      - run: pip install pytest pytest-cov

      - name: Run tests with coverage
        run: pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
```

### Deploy to GitHub Pages

```yaml
name: Deploy to Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: "npm"
      - run: npm ci
      - run: npm run build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

### Docker build and push

```yaml
name: Docker

on:
  push:
    branches: [main]
    tags: ["v*"]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - uses: docker/setup-buildx-action@v3

      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: ghcr.io/${{ github.repository }}
          tags: |
            type=ref,event=branch
            type=semver,pattern={{version}}
            type=sha

      - uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### Auto-label pull requests

```yaml
name: Label PRs

on:
  pull_request:
    types: [opened, synchronize]

permissions:
  pull-requests: write

jobs:
  label:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/labeler@v5
        with:
          repo-token: ${{ secrets.GITHUB_TOKEN }}
```

Requires a `.github/labeler.yml` configuration file mapping labels to file path patterns.

---

## Try It Yourself

**Step 1.** Create a repository with a simple Node.js or Python project (or any language).

**Step 2.** Create your first workflow file:

**Windows (PowerShell):**

```powershell
mkdir -p .github/workflows
```

**Mac and Linux:**

```bash
mkdir -p .github/workflows
```

Create `.github/workflows/hello.yml`:

```yaml
name: Hello World

on:
  push:
  workflow_dispatch:

jobs:
  greet:
    runs-on: ubuntu-latest

    steps:
      - name: Say hello
        run: echo "Hello from GitHub Actions!"

      - name: Show repository info
        run: |
          echo "Repository: ${{ github.repository }}"
          echo "Branch: ${{ github.ref_name }}"
          echo "Actor: ${{ github.actor }}"
          echo "Event: ${{ github.event_name }}"

      - name: Show runner info
        run: |
          echo "OS: ${{ runner.os }}"
          uname -a
          node --version
          python3 --version
```

**Step 3.** Commit and push:

```bash
git add .github/workflows/hello.yml
git commit -m "add: hello world GitHub Actions workflow"
git push
```

**Step 4.** Go to your repository → **Actions** tab. Watch the workflow run.

**Step 5.** Click the run to see the logs. Expand each step.

**Step 6.** Add a manual trigger input:

Edit `hello.yml` to add:

```yaml
on:
  push:
  workflow_dispatch:
    inputs:
      name:
        description: "Who to greet"
        default: "World"
        required: true
```

And update the greet step:

```yaml
- name: Say hello
  run: echo "Hello ${{ github.event.inputs.name || 'World' }}!"
```

Push the change. Go to Actions → Hello World → **Run workflow** → enter a name → run.

**Step 7.** Add a CI workflow for your language and commit it. Watch the tests run automatically on your next push.

---

## Common Mistakes

**Not pinning action versions.**

Using `uses: actions/checkout@main` means any change to that action's `main` branch immediately affects your workflows. Pin to a tag (`@v4`) or ideally a full SHA for third-party actions.

**Storing secrets in the workflow file itself.**

Never hardcode API keys, passwords or tokens in workflow YAML. Use `secrets.YOUR_SECRET` and store the value in repository settings.

**Giving workflows write-all permissions by default.**

The `permissions: write-all` shorthand gives every permission to every workflow. Grant only the specific permissions each workflow needs, at the most specific level possible.

**Not using `npm ci` instead of `npm install` in CI.**

`npm install` may update the lock file. `npm ci` installs exactly what is in the lock file and fails if there is a discrepancy. Always use `npm ci` in CI workflows.

**Not caching dependencies.**

Installing all dependencies on every run is slow. A simple cache can reduce a 2-minute install to under 10 seconds. Use the built-in caching in setup actions or `actions/cache`.

**Forgetting that jobs run in parallel by default.**

If Job B depends on files produced by Job A, use `needs: job-a` and pass artefacts. Without this, Job B may start before Job A completes.

**Using `pull_request_target` incorrectly.**

`pull_request_target` runs in the base branch context and has access to secrets. Never check out and run code from the PR itself in a `pull_request_target` workflow - it is a significant security risk.

**Not setting concurrency for deployment workflows.**

Without concurrency settings, pushing twice in quick succession can trigger two parallel deployment runs. Use concurrency groups to prevent concurrent deployments to the same environment.

---

## Summary

- GitHub Actions automates workflows triggered by repository events - push, pull request, schedule, manual and 40+ others
- Workflows are YAML files in `.github/workflows/` containing jobs, which contain steps
- Steps either run shell commands (`run:`) or execute actions (`uses:`)
- GitHub-hosted runners provide Linux, Windows and macOS environments with pre-installed tools
- Use the Actions marketplace (20,000+ actions) for common tasks instead of writing from scratch
- Always pin action versions - use SHA pins for third-party actions
- Secrets are encrypted values referenced as `${{ secrets.NAME }}` - never hardcode credentials
- The `GITHUB_TOKEN` is automatically available with read-only permissions - grant specific write permissions as needed
- Cache dependencies with `actions/cache` or the built-in `cache:` option in setup actions
- Matrix builds test across multiple OS/language version combinations simultaneously
- Reusable workflows share workflow definitions across repositories; composite actions bundle steps
- Environments add approval gates and deployment protection rules for production deployments
- Public repositories get unlimited free Actions minutes; private repositories get 2,000-50,000 depending on plan
- GitHub reduced Actions pricing by up to 39% on 1 January 2026

---

## Sources and Further Reading

- [GitHub Actions documentation](https://docs.github.com/en/actions) - the complete official reference
- [GitHub Actions workflow syntax](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions) - full YAML syntax reference
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions) - browse 20,000+ community actions
- [actions/starter-workflows](https://github.com/actions/starter-workflows) - official starter templates for dozens of languages and frameworks
- [GitHub Actions billing](https://docs.github.com/en/billing/managing-billing-for-github-actions/about-billing-for-github-actions) - pricing and usage limits
- [Security hardening for GitHub Actions](https://docs.github.com/en/actions/security-for-github-actions/security-guides/security-hardening-for-github-actions) - essential security practices
- [actions/runner-images](https://github.com/actions/runner-images) - full list of pre-installed software on hosted runners

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

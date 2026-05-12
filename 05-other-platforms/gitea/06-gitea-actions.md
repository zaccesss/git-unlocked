# Gitea Actions

**Difficulty:** 🟡 Intermediate | **Time:** 40 minutes

Gitea Actions is the built-in CI/CD system for Gitea. It uses workflow syntax that is intentionally compatible with GitHub Actions - the same YAML structure, the same trigger keywords, the same step definitions. If you already know GitHub Actions, you already know most of Gitea Actions. If you do not, this file teaches both simultaneously.

The key architectural difference from GitHub Actions is that Gitea does not provide hosted runners. You supply your own compute by installing and registering **act_runner** - a standalone binary that registers with your Gitea instance and executes workflow jobs on your infrastructure.

---

## Table of Contents

1. [How Gitea Actions works](#how-gitea-actions-works)
2. [Enabling Gitea Actions](#enabling-gitea-actions)
3. [Workflow file location and syntax](#workflow-file-location-and-syntax)
4. [Triggers](#triggers)
5. [Jobs and steps](#jobs-and-steps)
6. [Runners and labels](#runners-and-labels)
7. [Environment variables and secrets](#environment-variables-and-secrets)
8. [Artefacts](#artefacts)
9. [Caching dependencies](#caching-dependencies)
10. [Using actions from the marketplace](#using-actions-from-the-marketplace)
11. [Matrix builds](#matrix-builds)
12. [Managing runners](#managing-runners)
13. [Practical workflow examples](#practical-workflow-examples)
14. [Try It Yourself](#try-it-yourself)
15. [Common Mistakes](#common-mistakes)
16. [Summary](#summary)
17. [Sources](#sources)

---

## How Gitea Actions works

When you push a commit or open a pull request, Gitea evaluates all workflow files in `.gitea/workflows/` (and `.github/workflows/` for compatibility). For each workflow matching the event trigger:

1. Gitea creates a **workflow run** and queues jobs
2. An **act_runner** instance polling your Gitea instance picks up the job
3. The runner executes the job inside a Docker container (or directly on the host if configured)
4. Results, logs and artefacts are reported back to Gitea
5. The workflow run status appears on the commit, on any linked PR and on the Actions tab

The act_runner is based on [nektos/act](https://github.com/nektos/act) - the tool that lets you run GitHub Actions locally. Gitea's fork of act adds the ability to communicate results back to the Gitea server.

---

## Enabling Gitea Actions

Gitea Actions must be enabled at both the instance level and the repository level.

### Instance level (admin)

In `app.ini`:
```ini
[actions]
ENABLED = true
```

Actions have been enabled by default since Gitea v1.21. On older instances, an administrator may need to enable this explicitly.

### Repository level

1. Go to repository **Settings** > **Repository**
2. Under **Features**, enable **Actions**
3. Save

Or via the API:
```bash
curl -X PATCH \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"has_actions": true}' \
  https://gitea.example.com/api/v1/repos/username/repo
```

---

## Workflow file location and syntax

### File location

Gitea Actions reads workflow files from:
- `.gitea/workflows/*.yml` or `.gitea/workflows/*.yaml` (Gitea-specific path)
- `.github/workflows/*.yml` or `.github/workflows/*.yaml` (GitHub-compatible path)

Both paths work. The `.gitea/workflows/` path is preferred for Gitea-specific deployments. The `.github/workflows/` path is useful if you maintain a repository on both GitHub and Gitea and want the same workflows to run on both.

### Minimal workflow

```yaml
name: CI

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: make test
```

### Workflow file structure

```yaml
name: Workflow name (shown in the Actions tab)

on:                    # what triggers this workflow
  push:
    branches: [main]

env:                   # workflow-level environment variables
  NODE_ENV: test

jobs:
  job-name:            # identifier for this job
    name: Human-readable job name
    runs-on: ubuntu-latest
    
    env:               # job-level variables (override workflow-level)
      JOB_SPECIFIC: value
    
    steps:
      - name: Step name
        uses: actions/checkout@v4    # use an action
        
      - name: Run a command
        run: echo "Hello"
        
      - name: Multi-line command
        run: |
          echo "Line 1"
          echo "Line 2"
        env:           # step-level variables (override job-level)
          STEP_VAR: value
```

---

## Triggers

### Push trigger

```yaml
on:
  push:
    branches:
      - main
      - 'release/**'       # matches release/1.0, release/2.0/hotfix, etc.
    branches-ignore:
      - 'feature/**'       # never trigger for feature branches
    tags:
      - 'v*'               # trigger when a version tag is pushed
    paths:
      - 'src/**'           # only trigger when files in src/ change
    paths-ignore:
      - '**.md'            # ignore Markdown file changes
```

### Pull request trigger

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]  # default is all three
    branches:
      - main
```

PR workflows run on the merge commit (the result of merging the PR branch into the base). This tests the actual merged state, not just the PR branch in isolation.

### Schedule trigger

```yaml
on:
  schedule:
    - cron: '0 3 * * 1-5'   # 3 AM UTC, Monday to Friday
```

Cron format: minute, hour, day-of-month, month, day-of-week.

### Manual trigger

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
      debug:
        description: 'Enable debug logging'
        type: boolean
        default: false
```

Manual triggers add a **Run workflow** button to the Actions tab. The defined inputs appear as a form.

### Release trigger

```yaml
on:
  release:
    types: [published, released]
```

Fires when a Gitea release is published. Use for build-and-deploy workflows triggered by releases.

### Multiple triggers

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * 0'   # weekly on Sunday
  workflow_dispatch:
```

A single workflow can respond to multiple events.

---

## Jobs and steps

### Job dependencies

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: make build

  test:
    runs-on: ubuntu-latest
    needs: build           # wait for build to succeed
    steps:
      - uses: actions/checkout@v4
      - run: make test

  deploy:
    runs-on: ubuntu-latest
    needs: [build, test]   # wait for both
    if: github.ref == 'refs/heads/main'
    steps:
      - run: make deploy
```

### Conditional steps

```yaml
steps:
  - name: Deploy to production
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    run: ./deploy.sh production

  - name: Run only on failure
    if: failure()
    run: ./notify-team.sh "Build failed"

  - name: Always run (cleanup)
    if: always()
    run: ./cleanup.sh
```

Condition functions: `success()`, `failure()`, `cancelled()`, `always()`

### Step outputs

Pass data between steps using `GITHUB_OUTPUT`:

```yaml
steps:
  - name: Get version
    id: version
    run: echo "tag=$(git describe --tags --abbrev=0)" >> $GITHUB_OUTPUT

  - name: Use version
    run: echo "Building version ${{ steps.version.outputs.tag }}"
```

### Job outputs

Pass data between jobs:

```yaml
jobs:
  build:
    outputs:
      version: ${{ steps.get-version.outputs.version }}
    steps:
      - id: get-version
        run: echo "version=1.2.3" >> $GITHUB_OUTPUT

  deploy:
    needs: build
    steps:
      - run: echo "Deploying ${{ needs.build.outputs.version }}"
```

---

## Runners and labels

### The `runs-on` key

The `runs-on` value must match a label registered by one of your act_runner instances:

```yaml
jobs:
  job:
    runs-on: ubuntu-latest    # runner must have the "ubuntu-latest" label
```

Common label conventions (you define them when registering the runner):
- `ubuntu-latest` - a Linux runner with Docker
- `ubuntu-22.04` - a specific Ubuntu version
- `windows-latest` - a Windows runner
- `macos-latest` - a macOS runner
- `self-hosted` - any self-hosted runner

If no runner with the required label is available, the job queues indefinitely. Check that at least one registered runner has the label in your workflow's `runs-on`.

### Registering a runner with labels

```bash
act_runner register \
  --no-interactive \
  --instance https://gitea.example.com \
  --token REGISTRATION_TOKEN \
  --name "Docker Runner 1" \
  --labels "ubuntu-latest:docker://ubuntu:22.04,ubuntu-22.04:docker://ubuntu:22.04"
```

The format is `label:executor://image`. The executor can be:
- `docker://image` - run in a Docker container using the specified image
- `host` - run directly on the runner machine (no Docker isolation)

### Runner scope

Runners can be registered at three levels:
- **Instance-wide**: available to all repositories on the instance (admin only)
- **Organisation**: available to all repositories in the organisation
- **Repository**: only available to one specific repository

---

## Environment variables and secrets

### Built-in variables

Gitea provides predefined variables (using GitHub Actions naming for compatibility):

| Variable | Value |
|---|---|
| `github.ref` | Full ref name: `refs/heads/main` |
| `github.ref_name` | Short name: `main` |
| `github.sha` | The triggering commit SHA |
| `github.actor` | Username who triggered the workflow |
| `github.repository` | `owner/repo` |
| `github.event_name` | `push`, `pull_request`, etc. |
| `github.workspace` | Path to the checked-out code |
| `gitea.server_url` | Your Gitea instance URL |

Access them with `${{ github.ref }}` in YAML or as `$GITHUB_REF` in shell.

### Secrets

Secrets are encrypted values stored in Gitea, never shown in logs.

**Setting secrets:**
1. Go to repository **Settings** > **Actions** > **Secrets**
2. Click **Add Secret**
3. Enter name (e.g. `DEPLOY_KEY`) and value
4. Save

Or for organisation-wide secrets: **Organisation** > **Settings** > **Actions** > **Secrets**

**Using secrets in workflows:**
```yaml
steps:
  - name: Deploy
    env:
      API_KEY: ${{ secrets.DEPLOY_API_KEY }}
      DATABASE_URL: ${{ secrets.DATABASE_URL }}
    run: ./deploy.sh
```

Secrets are masked in logs - if a secret value appears in any output, Gitea replaces it with `***`.

> [!WARNING]
> Never print secrets with `echo "${{ secrets.MY_SECRET }}"` - even though they are masked, it is bad practice and may expose them in some edge cases. Pass secrets via environment variables to scripts.

### Variables (non-secret configuration)

For non-sensitive configuration values, use **Variables** (available since Gitea v1.22):
1. Go to **Settings** > **Actions** > **Variables**
2. Add a variable (e.g. `DEPLOY_HOST = staging.example.com`)

Access in workflows:
```yaml
run: ssh ${{ vars.DEPLOY_HOST }} "sudo systemctl restart app"
```

---

## Artefacts

Artefacts store files produced by a workflow run for later download. Common uses: compiled binaries, test reports, coverage data.

### Uploading artefacts

```yaml
- name: Build
  run: make build && tar czf dist.tar.gz dist/

- name: Upload build artefacts
  uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist.tar.gz
    retention-days: 30    # how long to keep the artefact
```

### Downloading artefacts in a later job

```yaml
jobs:
  build:
    steps:
      - uses: actions/upload-artifact@v4
        with:
          name: binary
          path: ./myapp

  test:
    needs: build
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: binary
          path: ./

      - run: chmod +x myapp && ./myapp --version
```

### Downloading artefacts from the UI

After a workflow run, open the run details page in Gitea. Artefacts appear at the bottom and can be downloaded as ZIP files.

---

## Caching dependencies

Caching stores downloaded packages between runs to speed up builds.

```yaml
steps:
  - uses: actions/checkout@v4

  - name: Cache npm packages
    uses: actions/cache@v3
    with:
      path: ~/.npm
      key: ${{ runner.os }}-npm-${{ hashFiles('**/package-lock.json') }}
      restore-keys: |
        ${{ runner.os }}-npm-

  - run: npm ci
```

The cache is identified by a **key**. When the key matches a previous run, the cached directory is restored before the step. When `package-lock.json` changes (a dependency update), the hash changes, the key misses and a fresh install runs.

### Cache storage

Caches are stored on the act_runner machine or in a location configured in act_runner's settings. Unlike GitHub Actions, there is no shared cache storage across multiple runner machines by default - a cache created by Runner A is not visible to Runner B.

---

## Using actions from the marketplace

Gitea Actions can use actions from:

### Actions hosted on GitHub

By default, Gitea fetches actions directly from GitHub when you write `uses: actions/checkout@v4`. This requires the runner to have internet access to `github.com`.

```yaml
steps:
  - uses: actions/checkout@v4          # from github.com/actions/checkout
  - uses: actions/setup-node@v4        # from github.com/actions/setup-node
    with:
      node-version: '20'
  - uses: actions/upload-artifact@v4   # from github.com/actions/upload-artifact
```

### Actions hosted on your Gitea instance

Reference actions in your Gitea repositories:

```yaml
steps:
  - uses: your-org/your-action@v1      # fetched from your Gitea instance
```

### Configuring action sources

In `app.ini`, you can specify where actions are fetched from:

```ini
[actions]
DEFAULT_ACTIONS_URL = https://gitea.com   ; use gitea.com as the default source
; or
DEFAULT_ACTIONS_URL = https://github.com  ; use GitHub (default)
; or
DEFAULT_ACTIONS_URL = self                ; only use actions from this instance
```

Setting `DEFAULT_ACTIONS_URL = self` is useful for air-gapped instances: all actions must be mirrored into your Gitea before use.

### Pinning actions by commit SHA

For security, pin actions to a specific commit SHA rather than a mutable tag:

```yaml
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683  # v4.2.2
```

This prevents supply chain attacks where a tag is moved to a malicious commit.

---

## Matrix builds

Matrix builds run the same job multiple times with different variable values. Common uses: testing against multiple language versions, multiple operating systems or multiple configurations.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [18, 20, 22]
        os: [ubuntu-latest, windows-latest]
      fail-fast: false    # don't cancel other matrix jobs if one fails

    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}

      - run: npm ci && npm test
```

This runs 6 jobs: Node 18/Ubuntu, Node 18/Windows, Node 20/Ubuntu, Node 20/Windows, Node 22/Ubuntu, Node 22/Windows.

### Including and excluding matrix combinations

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest, macos-latest]
    python: ['3.10', '3.11', '3.12']
    exclude:
      - os: macos-latest
        python: '3.10'    # skip this specific combination
    include:
      - os: ubuntu-latest
        python: '3.12'
        experimental: true   # add extra variable for this combination
```

---

## Managing runners

### Viewing runners

**Site-level runners** (admin):
- Go to **Site Administration** > **Runners**

**Repository runners**:
- Go to repository **Settings** > **Actions** > **Runners**

Each runner shows its name, labels, online status, last seen time and the task it is currently executing.

### Runner status

| Status | Meaning |
|---|---|
| **Online** | Connected and ready to accept jobs |
| **Offline** | Not connected (runner stopped or network issue) |
| **Idle** | Connected but not executing a job |
| **Active** | Currently executing a job |

### Blocking and unblocking runners

Administrators can block a runner to prevent it from accepting new jobs (e.g. during maintenance). Blocked runners are visible but do not pick up work.

### Deleting a runner

Deleting a runner in Gitea removes the registration. Running jobs on that runner are cancelled. The act_runner process on the machine continues running until stopped - it will then fail to connect and log authentication errors until it is also stopped.

To cleanly retire a runner:
1. Stop the act_runner daemon on the machine
2. Delete the runner from Gitea

---

## Practical workflow examples

### Node.js CI with caching

```yaml
name: Node.js CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Test
        run: npm test

      - name: Build
        run: npm run build
```

### Python with multiple versions

```yaml
name: Python CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12']

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install dependencies
        run: |
          pip install --upgrade pip
          pip install -r requirements.txt
          pip install pytest pytest-cov

      - name: Run tests with coverage
        run: pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: actions/upload-artifact@v4
        with:
          name: coverage-${{ matrix.python-version }}
          path: coverage.xml
```

### Docker build and push to Gitea registry

```yaml
name: Docker

on:
  push:
    tags: ['v*']

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Log in to Gitea Container Registry
        uses: docker/login-action@v3
        with:
          registry: gitea.example.com
          username: ${{ github.actor }}
          password: ${{ secrets.GITEA_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            gitea.example.com/${{ github.repository }}:latest
            gitea.example.com/${{ github.repository }}:${{ github.ref_name }}
```

### Release workflow with binary artefacts

```yaml
name: Release

on:
  release:
    types: [published]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build binaries
        run: |
          GOOS=linux   GOARCH=amd64 go build -o dist/myapp-linux-amd64   .
          GOOS=windows GOARCH=amd64 go build -o dist/myapp-windows-amd64.exe .
          GOOS=darwin  GOARCH=arm64 go build -o dist/myapp-darwin-arm64  .

      - name: Upload release assets
        uses: actions/upload-artifact@v4
        with:
          name: release-binaries
          path: dist/
```

---

## Try It Yourself

**Exercise 1 - Your first workflow**

In your Gitea repository, create `.gitea/workflows/hello.yml`:

```yaml
name: Hello World

on:
  push:
    branches: [main]

jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Say hello
        run: |
          echo "Hello from Gitea Actions!"
          echo "Repository: ${{ github.repository }}"
          echo "Triggered by: ${{ github.actor }}"
          echo "Branch: ${{ github.ref_name }}"
```

Commit and push. Check the **Actions** tab to see the workflow run.

**Exercise 2 - Add a secret and use it**

1. Go to **Settings** > **Actions** > **Secrets**
2. Add a secret named `GREETING_NAME` with your name as the value
3. Update your workflow:

```yaml
      - name: Greet
        run: echo "Hello, ${{ secrets.GREETING_NAME }}!"
        env:
          NAME: ${{ secrets.GREETING_NAME }}
```

The output will show `Hello, ***!` since secrets are masked.

**Exercise 3 - Matrix build**

```yaml
name: Multi-version test

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node: [18, 20, 22]
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: node --version
```

Observe that 3 jobs run in parallel, each printing a different Node.js version.

---

## Common Mistakes

**No runner registered with the `runs-on` label**

If a job's `runs-on: ubuntu-latest` matches no registered runner, the job queues forever. Check the runner list in Settings > Actions > Runners and verify at least one online runner has the `ubuntu-latest` label.

**Forgetting that Docker is required on the runner**

act_runner executes jobs inside Docker containers by default. The runner machine must have Docker installed and the `docker` daemon running. A common setup failure is installing act_runner on a machine without Docker.

**Using `runs-on: ubuntu-latest` when no runner has that label**

When you register an act_runner, you define its labels. If you registered with `--labels "linux:docker://ubuntu:22.04"` but your workflow says `runs-on: ubuntu-latest`, there is no match. Use consistent labels between runner registration and workflow files.

**Hardcoding secrets in workflow YAML**

Never write `password: myActualPassword` in a workflow file. Use `${{ secrets.MY_SECRET }}`. Workflow files are code - they are version-controlled and visible to anyone with repo access.

**Using mutable action tags**

`uses: actions/checkout@v4` references the `v4` tag which can be moved. For production workflows, pin to a SHA: `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683`. This prevents unexpected changes when action maintainers update the tag.

**Not setting `fail-fast: false` for matrix builds**

By default, if one matrix job fails, all other running jobs are cancelled. This can obscure failures in other combinations. Set `fail-fast: false` when you want to see all results regardless.

---

## Summary

Gitea Actions brings GitHub Actions-compatible CI/CD to self-hosted Gitea. Workflow files in `.gitea/workflows/` use the same YAML syntax as GitHub Actions: `on:` triggers, `jobs:`, `steps:` and `uses:` for reusable actions.

The key difference from GitHub Actions is infrastructure: you supply your own runners by installing **act_runner**, registering it with a token from Gitea and starting it as a daemon. The runner uses Docker to execute jobs in containers (or `host` executor for direct execution). Labels connect workflow `runs-on` values to specific runners.

Secrets are stored in Gitea and injected at runtime - never visible in logs. Variables store non-sensitive configuration. Artefacts persist files from a workflow run. Caching stores downloaded dependencies between runs.

Matrix builds run the same job with different parameter combinations. Actions from GitHub or from your own Gitea instance can be referenced with `uses:`. For air-gapped instances, mirror required actions into Gitea and set `DEFAULT_ACTIONS_URL = self`.

---

## Sources

- [Gitea: Actions documentation](https://docs.gitea.com/usage/actions/overview)
- [Gitea: act_runner documentation](https://docs.gitea.com/usage/actions/act-runner)
- [Gitea: Actions quickstart](https://docs.gitea.com/usage/actions/quickstart)
- [Gitea: Workflow syntax](https://docs.gitea.com/usage/actions/workflow-syntax)
- [nektos/act: GitHub Actions local runner](https://github.com/nektos/act)
- [GitHub: Workflow syntax reference](https://docs.github.com/actions/writing-workflows/workflow-syntax-for-github-actions)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

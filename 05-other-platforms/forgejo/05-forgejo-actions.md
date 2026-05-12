# Forgejo Actions

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Forgejo Actions is the built-in CI/CD system for Forgejo. It uses the same YAML workflow syntax as GitHub Actions and Gitea Actions - if you know one, you know all three. The key infrastructure difference is that Forgejo provides the **Forgejo runner** (its own fork of act_runner) and reads workflow files from `.forgejo/workflows/` (though `.github/workflows/` and `.gitea/workflows/` also work for compatibility).

This file covers enabling Actions, the complete workflow syntax, runner setup and practical examples.

---

## Table of Contents

1. [How Forgejo Actions works](#how-forgejo-actions-works)
2. [Enabling Forgejo Actions](#enabling-forgejo-actions)
3. [Workflow file location](#workflow-file-location)
4. [Triggers](#triggers)
5. [Jobs and steps](#jobs-and-steps)
6. [Runners and labels](#runners-and-labels)
7. [Secrets and variables](#secrets-and-variables)
8. [Artefacts and caching](#artefacts-and-caching)
9. [Matrix builds](#matrix-builds)
10. [Using actions from external sources](#using-actions-from-external-sources)
11. [Practical workflow examples](#practical-workflow-examples)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## How Forgejo Actions works

When you push a commit or open a pull request, Forgejo evaluates all workflow files in the `.forgejo/workflows/` directory (and `.github/workflows/` and `.gitea/workflows/` for compatibility). For each matching trigger:

1. Forgejo creates a workflow run and queues jobs
2. A **Forgejo runner** polling your instance picks up the job
3. The runner executes the job inside a Docker container (or directly on the host)
4. Results, logs and artefacts are reported back to Forgejo
5. Status appears on the commit, PR and Actions tab

The Forgejo runner is maintained by the Forgejo project at `codeberg.org/forgejo/runner`. It is based on nektos/act, the same foundation as Gitea's act_runner, but developed independently and tested specifically against Forgejo.

---

## Enabling Forgejo Actions

### Instance level (admin)

In `app.ini`:
```ini
[actions]
ENABLED = true
```

Actions are enabled by default on fresh Forgejo installations.

### Repository level

1. Repository **Settings** > **Repository**
2. Under **Features**, enable **Actions**
3. Save

### Verify

The **Actions** tab appears in the repository navigation when enabled.

---

## Workflow file location

Forgejo Actions reads workflow files from:

```
.forgejo/workflows/ci.yml       ← preferred for Forgejo deployments
.github/workflows/ci.yml        ← GitHub-compatible path
.gitea/workflows/ci.yml         ← Gitea-compatible path
```

All three locations work. Use `.forgejo/workflows/` when the repository is only on Forgejo. Use `.github/workflows/` when the same repository also runs on GitHub (both platforms run the same workflow file).

### Minimal workflow

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
      - uses: actions/checkout@v4
      - name: Run tests
        run: make test
```

---

## Triggers

### Push trigger

```yaml
on:
  push:
    branches:
      - main
      - 'release/**'
    branches-ignore:
      - 'dependabot/**'
    tags:
      - 'v*'
    paths:
      - 'src/**'
    paths-ignore:
      - '**.md'
      - 'docs/**'
```

### Pull request trigger

```yaml
on:
  pull_request:
    types: [opened, synchronize, reopened]
    branches:
      - main
```

PR workflows run on the merge commit (the result of merging the PR branch into the base), testing the actual merged state.

### Schedule

```yaml
on:
  schedule:
    - cron: '0 2 * * 1-5'    # 2 AM UTC Mon-Fri
```

### Manual trigger with inputs

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Target environment'
        required: true
        type: choice
        options: [staging, production]
      skip-tests:
        description: 'Skip test suite'
        type: boolean
        default: false
```

Adds a **Run workflow** button to the Actions tab with the defined inputs as a form.

### Release trigger

```yaml
on:
  release:
    types: [published]
```

Fires when a Forgejo release is published. Use for build-and-publish workflows.

---

## Jobs and steps

### Job structure

```yaml
jobs:
  build:
    name: Build application
    runs-on: ubuntu-latest
    timeout-minutes: 30

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0    # full history for versioning

      - name: Set up Go
        uses: actions/setup-go@v5
        with:
          go-version: '1.22'

      - name: Build
        run: go build ./...

      - name: Test
        run: go test ./... -v
```

### Job dependencies

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: make build

  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: make test

  deploy:
    needs: [build, test]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: make deploy
```

### Conditional steps

```yaml
steps:
  - name: Deploy
    if: github.ref == 'refs/heads/main' && github.event_name == 'push'
    run: ./deploy.sh

  - name: Notify failure
    if: failure()
    run: ./notify.sh "Build failed on ${{ github.ref }}"

  - name: Cleanup (always)
    if: always()
    run: ./cleanup.sh
```

### Step outputs

```yaml
steps:
  - name: Get version
    id: version
    run: echo "tag=$(git describe --tags --abbrev=0)" >> $GITHUB_OUTPUT

  - name: Use version
    run: echo "Deploying ${{ steps.version.outputs.tag }}"
```

---

## Runners and labels

### The `runs-on` key

The `runs-on` value must match a label registered by one of your Forgejo runner instances:

```yaml
jobs:
  job:
    runs-on: ubuntu-latest
```

If no runner with `ubuntu-latest` is registered and online, the job queues indefinitely.

### Registering the Forgejo runner

Download the Forgejo runner from `codeberg.org/forgejo/runner/releases`:

```bash
RUNNER_VERSION="3.5.1"
wget -O forgejo-runner \
  "https://codeberg.org/forgejo/runner/releases/download/v${RUNNER_VERSION}/forgejo-runner-${RUNNER_VERSION}-linux-amd64"
chmod +x forgejo-runner
```

Get a registration token from:
- Instance-wide: **Site Administration** > **Runners** > **Create new runner**
- Repository: **Settings** > **Actions** > **Runners** > **Create new runner**

Register:

```bash
./forgejo-runner register \
  --no-interactive \
  --instance https://forgejo.example.com \
  --token REGISTRATION_TOKEN \
  --name "Docker Runner" \
  --labels "ubuntu-latest:docker://ubuntu:22.04,ubuntu-22.04:docker://ubuntu:22.04,linux:docker://ubuntu:22.04"
```

Start:

```bash
./forgejo-runner daemon
```

### Label format

```
label-name:executor://image
```

- `ubuntu-latest:docker://ubuntu:22.04` - run in Docker using ubuntu:22.04
- `alpine:docker://alpine:3.19` - run in Docker using Alpine
- `native:host` - run directly on the host machine (no Docker)

### Docker requirement

The Forgejo runner uses Docker by default to provide isolation. The machine running the runner must have Docker installed and the Docker daemon running.

---

## Secrets and variables

### Secrets

Secrets are encrypted and masked in logs. Set them in:
- **Repository** > **Settings** > **Actions** > **Secrets** > **Add Secret**
- **Organisation** > **Settings** > **Actions** > **Secrets** (available to all org repos)

Use in workflows:

```yaml
steps:
  - name: Deploy
    env:
      API_KEY: ${{ secrets.DEPLOY_API_KEY }}
      DB_PASSWORD: ${{ secrets.DATABASE_PASSWORD }}
    run: ./deploy.sh
```

### Variables (non-secret configuration)

For non-sensitive values that do not need encryption:

- **Repository** > **Settings** > **Actions** > **Variables**

Use in workflows:

```yaml
steps:
  - run: ssh ${{ vars.DEPLOY_HOST }} "sudo systemctl restart app"
```

### Built-in variables

| Variable | Value |
|---|---|
| `github.ref` | Full ref: `refs/heads/main` |
| `github.ref_name` | Short name: `main` |
| `github.sha` | Triggering commit SHA |
| `github.actor` | Username who triggered |
| `github.repository` | `owner/repo` |
| `github.event_name` | `push`, `pull_request`, etc. |
| `gitea.server_url` | Your Forgejo instance URL |
| `github.workspace` | Checked-out code path |

Forgejo uses the `github.*` namespace for these variables for GitHub Actions compatibility.

### Forgejo token in workflows

Within a workflow, Forgejo automatically provides a `FORGEJO_TOKEN` (and `GITEA_TOKEN` for compatibility) secret scoped to the current repository:

```yaml
- name: Create release
  run: |
    curl -X POST \
      -H "Authorization: token ${{ secrets.FORGEJO_TOKEN }}" \
      -H "Content-Type: application/json" \
      -d '{"tag_name": "${{ github.ref_name }}", "name": "Release ${{ github.ref_name }}"}' \
      "${{ gitea.server_url }}/api/v1/repos/${{ github.repository }}/releases"
```

---

## Artefacts and caching

### Uploading artefacts

```yaml
- name: Build
  run: make build && tar czf build.tar.gz dist/

- uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: build.tar.gz
    retention-days: 7
```

### Downloading artefacts between jobs

```yaml
jobs:
  build:
    steps:
      - run: make build
      - uses: actions/upload-artifact@v4
        with:
          name: app-binary
          path: ./app

  test:
    needs: build
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: app-binary
          path: ./
      - run: chmod +x app && ./app --version
```

### Caching dependencies

```yaml
- uses: actions/cache@v3
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('requirements.txt') }}
    restore-keys: |
      ${{ runner.os }}-pip-

- run: pip install -r requirements.txt
```

> [!NOTE]
> Caches are stored on the runner machine. A cache created by one runner is not automatically visible to other runner machines. For consistent caching across multiple runners, configure a shared cache backend (e.g. NFS mount or S3-compatible storage) in the runner configuration.

---

## Matrix builds

Run the same job with multiple parameter combinations:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ['3.10', '3.11', '3.12']
        os: [ubuntu-latest, ubuntu-22.04]
      fail-fast: false

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - run: pip install -r requirements.txt && pytest
```

This creates 6 jobs: 3 Python versions × 2 OS images.

### Including specific combinations

```yaml
strategy:
  matrix:
    node: [18, 20, 22]
    os: [ubuntu-latest]
    include:
      - node: 20
        os: ubuntu-latest
        experimental: true    # add an extra variable for this entry
    exclude:
      - node: 18
        os: ubuntu-latest    # skip this combination
```

---

## Using actions from external sources

### Actions from GitHub (default)

By default, Forgejo fetches `uses: actions/checkout@v4` from `github.com/actions/checkout`. This requires the runner machine to have internet access to GitHub.

```yaml
steps:
  - uses: actions/checkout@v4
  - uses: actions/setup-node@v4
    with:
      node-version: '20'
  - uses: docker/build-push-action@v5
```

### Actions from your Forgejo instance

Reference actions hosted in repositories on your Forgejo instance:

```yaml
steps:
  - uses: your-org/your-action@v1    # fetched from your Forgejo instance
```

### Configuring the action source

In `app.ini`, control where actions are fetched from:

```ini
[actions]
DEFAULT_ACTIONS_URL = https://github.com   ; default, fetch from GitHub
; or
DEFAULT_ACTIONS_URL = https://codeberg.org ; use Codeberg (runs Forgejo)
; or
DEFAULT_ACTIONS_URL = self                 ; only use actions from this instance
```

For air-gapped instances, set `DEFAULT_ACTIONS_URL = self`. Mirror all required actions (checkout, setup-node, etc.) into your Forgejo instance before use.

### Pinning by SHA for security

```yaml
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683  # v4.2.2
```

Pinning to a specific commit SHA prevents supply chain attacks where a tag is moved to malicious code.

---

## Practical workflow examples

### Node.js CI

```yaml
name: Node.js CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

### Go with release publishing

```yaml
name: Release

on:
  release:
    types: [published]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-go@v5
        with:
          go-version: '1.22'

      - name: Build for multiple platforms
        run: |
          GOOS=linux   GOARCH=amd64  go build -o dist/app-linux-amd64 .
          GOOS=linux   GOARCH=arm64  go build -o dist/app-linux-arm64 .
          GOOS=darwin  GOARCH=amd64  go build -o dist/app-darwin-amd64 .
          GOOS=darwin  GOARCH=arm64  go build -o dist/app-darwin-arm64 .

      - name: Upload binaries to release
        run: |
          for binary in dist/*; do
            filename=$(basename "$binary")
            curl -X POST \
              -H "Authorization: token ${{ secrets.FORGEJO_TOKEN }}" \
              -H "Content-Type: application/octet-stream" \
              --data-binary "@${binary}" \
              "${{ gitea.server_url }}/api/v1/repos/${{ github.repository }}/releases/assets?name=${filename}&attachment_id=$(curl -s -H 'Authorization: token ${{ secrets.FORGEJO_TOKEN }}' '${{ gitea.server_url }}/api/v1/repos/${{ github.repository }}/releases/latest' | jq .id)"
          done
```

### Docker build and push to Forgejo registry

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

      - uses: docker/login-action@v3
        with:
          registry: forgejo.example.com
          username: ${{ github.actor }}
          password: ${{ secrets.FORGEJO_TOKEN }}

      - uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: |
            forgejo.example.com/${{ github.repository }}:latest
            forgejo.example.com/${{ github.repository }}:${{ github.ref_name }}
```

---

## Try It Yourself

**Exercise 1 - First workflow**

Create `.forgejo/workflows/hello.yml`:

```yaml
name: Hello Forgejo

on: [push]

jobs:
  greet:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Greet
        run: |
          echo "Hello from Forgejo Actions!"
          echo "Repository: ${{ github.repository }}"
          echo "Commit: ${{ github.sha }}"
          echo "Actor: ${{ github.actor }}"
```

Commit, push and watch the Actions tab.

**Exercise 2 - Add a secret**

1. Settings > Actions > Secrets > Add Secret
2. Name: `GREETING`, value: your name
3. Update the workflow:

```yaml
      - name: Greet with secret
        run: echo "Hello, ${{ secrets.GREETING }}!"
        env:
          NAME: ${{ secrets.GREETING }}
```

The log shows `Hello, ***!` - secrets are masked.

**Exercise 3 - Matrix build**

```yaml
name: Multi-version

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

Watch 3 parallel jobs run.

---

## Common Mistakes

**No runner registered with the `runs-on` label**

If a job uses `runs-on: ubuntu-latest` but no runner with that label is online, the job queues forever with "Waiting for runner" status. Check the runner list in Settings > Actions > Runners and confirm at least one runner has the correct label and is online.

**Using Gitea's act_runner instead of the Forgejo runner**

The Forgejo runner (`codeberg.org/forgejo/runner`) is maintained separately. Using Gitea's act_runner may work for basic workflows but is not officially supported against Forgejo and may have compatibility issues. Always use the Forgejo runner for Forgejo instances.

**Runner machine without Docker**

The Forgejo runner requires Docker to run container-based jobs. Install Docker on the runner machine before registering. Without Docker, all container-based jobs fail immediately.

**Hardcoding secrets in workflow YAML**

Never put credentials directly in workflow files. Workflow YAML is version-controlled and visible to anyone with repository access. Use `${{ secrets.MY_SECRET }}` and store the value in Forgejo's secret management.

**Not setting `fail-fast: false` for matrix builds**

By default, if one matrix combination fails, all others are cancelled. Set `fail-fast: false` to see all results, especially useful when testing against multiple platforms to understand which combinations fail.

---

## Summary

Forgejo Actions uses GitHub Actions-compatible YAML syntax in `.forgejo/workflows/` (or `.github/workflows/` for multi-platform repos). Triggers include push, pull_request, schedule, workflow_dispatch and release events.

The Forgejo runner executes jobs on self-hosted infrastructure. Download it from `codeberg.org/forgejo/runner/releases` - not from Gitea's releases. Runners use Docker by default and match jobs to workflow `runs-on` labels. Register with a token from the admin panel or repository settings.

Secrets store encrypted values, masked in logs. Variables store non-sensitive configuration. Built-in variables (`github.ref`, `github.sha`, etc.) provide workflow context. The automatic `FORGEJO_TOKEN` secret gives pipelines API access scoped to the current repository.

Artefacts persist files between jobs and for download after runs. Caching stores dependencies between runs using key-based hashing. Matrix builds run the same job with multiple parameter combinations in parallel.

Actions from GitHub are fetched by default (requires internet). For air-gapped instances, set `DEFAULT_ACTIONS_URL = self` and mirror required actions into Forgejo.

---

## Sources

- [Forgejo: Actions documentation](https://forgejo.org/docs/latest/user/actions/)
- [Forgejo runner: releases](https://codeberg.org/forgejo/runner/releases)
- [Forgejo runner: documentation](https://forgejo.org/docs/latest/admin/actions/)
- [GitHub: Workflow syntax reference](https://docs.github.com/actions/writing-workflows/workflow-syntax-for-github-actions) (compatible syntax)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

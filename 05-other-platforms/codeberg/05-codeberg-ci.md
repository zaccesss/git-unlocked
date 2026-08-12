# Codeberg CI (Woodpecker CI)

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Codeberg does not have native CI/CD built into the platform the way GitHub Actions or GitLab CI does. Instead, Codeberg provides **Woodpecker CI** as an opt-in external service at [ci.codeberg.org](https://ci.codeberg.org). Woodpecker CI is a community-maintained, open source CI system with a workflow syntax similar to but not identical to GitHub Actions. It integrates tightly with Codeberg through OAuth2 and runs pipelines on pushes and pull requests.

This file covers connecting Woodpecker CI to your Codeberg account, writing pipeline YAML, understanding Woodpecker's architecture and practical workflow examples.

---

## Table of Contents

1. [What is Woodpecker CI](#what-is-woodpecker-ci)
2. [Connecting to Codeberg CI](#connecting-to-codeberg-ci)
3. [Enabling a repository](#enabling-a-repository)
4. [Pipeline file syntax](#pipeline-file-syntax)
5. [Steps and plugins](#steps-and-plugins)
6. [Triggers](#triggers)
7. [Environment variables and secrets](#environment-variables-and-secrets)
8. [Matrix builds](#matrix-builds)
9. [Services (Docker sidecar containers)](#services-docker-sidecar-containers)
10. [Practical pipeline examples](#practical-pipeline-examples)
11. [Self-hosted runners](#self-hosted-runners)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## What is Woodpecker CI

Woodpecker CI is a community fork of Drone CI, an open source CI/CD system. It was forked to maintain the open source character that Drone moved away from when it shifted to a proprietary model. Woodpecker is licensed under the Apache 2.0 licence and developed collaboratively.

Codeberg chose Woodpecker CI as its CI service because:
- It is free software (no proprietary components)
- It integrates natively with Forgejo and Gitea
- The governance aligns with Codeberg's values
- It is self-hostable (you can run your own Woodpecker server)

### Woodpecker vs GitHub Actions

| Aspect | Woodpecker CI | GitHub Actions |
|---|---|---|
| Syntax | `.woodpecker.yml` (own format) | `.github/workflows/*.yml` |
| Steps | Sequential only (no `uses:`) | `uses:` for reusable actions |
| Plugins | Docker images with conventions | JavaScript or Docker actions |
| Runners | Community-provided on Codeberg | GitHub provides hosted runners |
| Self-host | Yes, full control | GitHub Enterprise only |
| Marketplace | woodpecker-ci.org/plugins | marketplace.github.com |

### Woodpecker vs Forgejo Actions

Codeberg also supports Forgejo Actions (the GitHub Actions-compatible runner). As of 2026, Forgejo Actions on Codeberg requires self-hosted runners - Codeberg does not provide shared hosted runners for it. Woodpecker CI has shared runners available at ci.codeberg.org.

For most Codeberg users, Woodpecker CI is the practical choice for CI/CD without setting up your own infrastructure.

---

## Connecting to Codeberg CI

### Step 1 - Authenticate with ci.codeberg.org

1. Go to [ci.codeberg.org](https://ci.codeberg.org)
2. Click **Login**
3. You are redirected to Codeberg to authorise the OAuth2 application
4. Click **Authorize Application**
5. You are returned to ci.codeberg.org, signed in

Your Codeberg account is now linked to Woodpecker CI. Woodpecker can see your repositories and post commit status back to Codeberg.

### Step 2 - Grant repository access

Woodpecker needs permission to access your repositories. The OAuth2 authorisation provides this. If you later create new repositories or organisations, you may need to resync Woodpecker's repository list:

1. In Woodpecker CI (ci.codeberg.org), click your avatar
2. Click **Repos** or **Refresh**
3. Your new repositories appear in the list

---

## Enabling a repository

Woodpecker does not automatically run pipelines on all your repositories. You must explicitly enable each one.

1. In Woodpecker CI, click **+ Add repository**
2. Find your repository in the list (search by name)
3. Click **Enable**

Woodpecker adds a webhook to your Codeberg repository. You can verify this in Codeberg repository settings under **Webhooks** - a new webhook pointing to ci.codeberg.org appears.

Once enabled, any push to the repository that contains a `.woodpecker.yml` file triggers a pipeline run.

---

## Pipeline file syntax

Woodpecker pipelines are defined in `.woodpecker.yml` at the root of your repository. The file can also be at `.woodpecker/*.yml` for multiple pipeline files.

### Minimal pipeline

```yaml
steps:
  - name: test
    image: python:3.12
    commands:
      - pip install -r requirements.txt
      - pytest
```

This single-step pipeline runs on every push, using the Python 3.12 Docker image.

### Full pipeline structure

```yaml
# Pipeline-level settings
when:
  branch: main          # only run on main branch (overrideable per step)

variables:
  - &python_image python:3.12

steps:
  - name: install
    image: *python_image
    commands:
      - pip install --upgrade pip
      - pip install -r requirements.txt

  - name: lint
    image: *python_image
    commands:
      - pip install flake8 black
      - flake8 src/
      - black --check src/

  - name: test
    image: *python_image
    commands:
      - pytest --cov=src --cov-report=xml

  - name: build
    image: *python_image
    commands:
      - python -m build
    when:
      branch: main
      event: push

  - name: publish
    image: plugins/pypi
    settings:
      username: __token__
      password:
        from_secret: PYPI_TOKEN
      distributions:
        - bdist_wheel
        - sdist
    when:
      event: tag
```

### Key differences from GitHub Actions

**Steps run sequentially** by default. There is no `needs:` for parallel steps within a pipeline file - use multiple pipeline files for parallelism.

**No `uses:` keyword**. Woodpecker uses Docker images as the execution environment. Reusable functionality comes from Woodpecker plugins (Docker images following a convention).

**`image:`** is required for every step. The step runs inside this Docker container.

**`commands:`** is a list of shell commands to run inside the container.

**`settings:`** is used for plugin configuration (not environment variables).

---

## Steps and plugins

### Steps with shell commands

Each step runs commands inside its Docker container:

```yaml
steps:
  - name: build
    image: node:20-alpine
    commands:
      - npm ci
      - npm run build
      - npm test
```

Multiple commands run sequentially within the same container. If one fails, the step fails and subsequent commands do not run.

### Plugins

Plugins are Docker images that perform specific CI tasks. They follow a convention where configuration is passed via environment variables prefixed with `PLUGIN_`. In Woodpecker YAML, you set them under `settings:`.

**Common Woodpecker plugins:**

**Docker build and push:**
```yaml
- name: docker
  image: woodpeckerci/plugin-docker-buildx
  settings:
    repo: codeberg.org/username/my-app
    registry: codeberg.org
    username:
      from_secret: REGISTRY_USERNAME
    password:
      from_secret: REGISTRY_TOKEN
    tags:
      - latest
      - ${CI_COMMIT_SHA:0:8}
  when:
    event: push
    branch: main
```

**SSH deploy:**
```yaml
- name: deploy
  image: appleboy/drone-ssh
  settings:
    host: your-server.example.com
    username: deploy
    key:
      from_secret: SSH_PRIVATE_KEY
    script:
      - cd /opt/myapp
      - git pull
      - systemctl restart myapp
```

**Notify via Telegram:**
```yaml
- name: notify
  image: appleboy/drone-telegram
  settings:
    token:
      from_secret: TELEGRAM_TOKEN
    to:
      from_secret: TELEGRAM_CHAT_ID
    message: "Build {{build.status}} for {{repo.name}} on branch {{build.branch}}"
  when:
    status: [failure, success]
```

Browse the full plugin directory at [woodpecker-ci.org/plugins](https://woodpecker-ci.org/plugins).

### Volumes and caching

Share data between steps using volumes:

```yaml
steps:
  - name: build
    image: node:20
    volumes:
      - node_cache:/root/.npm
    commands:
      - npm ci
      - npm run build

  - name: test
    image: node:20
    volumes:
      - node_cache:/root/.npm
    commands:
      - npm test

volumes:
  node_cache:
```

The named volume `node_cache` persists between pipeline runs on the same runner host, acting as a dependency cache.

---

## Triggers

### Default trigger

By default, pipelines trigger on every push to any branch and on every pull request.

### Restricting triggers globally

```yaml
when:
  branch: main          # only run on main branch
  event: push           # only on push (not pull_request)
```

### Per-step triggers

Each step can have its own `when` conditions:

```yaml
steps:
  - name: test
    image: golang:1.22
    commands:
      - go test ./...
    # runs on all events (default)

  - name: build-release
    image: golang:1.22
    commands:
      - go build -o dist/app .
    when:
      event: tag          # only when a tag is pushed

  - name: deploy
    image: plugins/ssh
    settings:
      # ...
    when:
      branch: main
      event: push         # only on push to main
```

### Event types

| Event | When it fires |
|---|---|
| `push` | Commit pushed to a branch |
| `pull_request` | PR opened or updated |
| `tag` | Tag pushed |
| `deployment` | Deployment event triggered |
| `cron` | Scheduled run |
| `manual` | Manually triggered from UI |

### Branch filters

```yaml
when:
  branch:
    include: [main, develop, release/*]
    exclude: [feature/*]
```

### PR-only steps

```yaml
- name: pr-check
  image: golangci/golangci-lint
  commands:
    - golangci-lint run
  when:
    event: pull_request
```

---

## Environment variables and secrets

### Built-in variables

Woodpecker provides these automatically:

| Variable | Value |
|---|---|
| `CI_COMMIT_SHA` | The triggering commit SHA |
| `CI_COMMIT_BRANCH` | Branch name |
| `CI_COMMIT_TAG` | Tag name (if triggered by a tag) |
| `CI_COMMIT_MESSAGE` | The commit message |
| `CI_REPO_NAME` | Repository name |
| `CI_REPO_OWNER` | Repository owner |
| `CI_PIPELINE_NUMBER` | Pipeline run number |
| `CI_PIPELINE_STATUS` | `success` or `failure` |

### Custom environment variables

```yaml
steps:
  - name: build
    image: node:20
    environment:
      NODE_ENV: production
      API_URL: https://api.example.com
    commands:
      - npm run build
```

### Secrets

Secrets are stored encrypted in Woodpecker and injected at runtime.

**Adding a secret:**
1. In Woodpecker CI, go to your repository
2. Click **Settings** > **Secrets**
3. Click **Add secret**
4. Enter name (e.g. `DEPLOY_KEY`) and value
5. Save

**Organisation secrets**: available to all repositories in the organisation. Go to the organisation settings in Woodpecker.

**Using secrets in pipelines:**

```yaml
steps:
  - name: deploy
    image: plugins/ssh
    settings:
      key:
        from_secret: DEPLOY_SSH_KEY    # plugin setting from secret
    environment:
      DATABASE_URL:
        from_secret: DATABASE_URL       # environment variable from secret
    commands:
      - ./deploy.sh
```

---

## Matrix builds

Run the same pipeline with different variable combinations:

```yaml
matrix:
  include:
    - GO_VERSION: "1.21"
      OS: linux
    - GO_VERSION: "1.22"
      OS: linux
    - GO_VERSION: "1.22"
      OS: windows

steps:
  - name: test
    image: golang:${GO_VERSION}
    commands:
      - go test ./...
```

Each matrix entry creates a separate pipeline run. Matrix pipelines appear as sibling runs in the Woodpecker UI.

---

## Services (Docker sidecar containers)

Services run alongside your pipeline as Docker containers - useful for databases, message queues or other dependencies.

```yaml
services:
  - name: postgres
    image: postgres:15
    environment:
      POSTGRES_DB: testdb
      POSTGRES_USER: test
      POSTGRES_PASSWORD: testpass

steps:
  - name: test
    image: python:3.12
    environment:
      DATABASE_URL: postgresql://test:testpass@postgres/testdb
    commands:
      - pip install -r requirements.txt
      - pytest tests/integration/
```

Services are accessible by their name as a hostname (e.g. `postgres`). They start before the pipeline steps and stop after.

```yaml
services:
  - name: redis
    image: redis:7-alpine

  - name: rabbitmq
    image: rabbitmq:3-management
    environment:
      RABBITMQ_DEFAULT_USER: guest
      RABBITMQ_DEFAULT_PASS: guest
```

---

## Practical pipeline examples

### Node.js project

```yaml
steps:
  - name: install
    image: node:20-alpine
    commands:
      - npm ci

  - name: lint
    image: node:20-alpine
    commands:
      - npm run lint

  - name: test
    image: node:20-alpine
    commands:
      - npm test -- --coverage

  - name: build
    image: node:20-alpine
    commands:
      - npm run build
    when:
      branch: main
      event: push
```

### Python with PostgreSQL integration tests

```yaml
services:
  - name: postgres
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: testdb
      POSTGRES_USER: ci
      POSTGRES_PASSWORD: cipassword

steps:
  - name: install
    image: python:3.12-slim
    commands:
      - pip install --upgrade pip
      - pip install -r requirements.txt
      - pip install pytest pytest-asyncio

  - name: unit-tests
    image: python:3.12-slim
    commands:
      - pytest tests/unit/ -v

  - name: integration-tests
    image: python:3.12-slim
    environment:
      DATABASE_URL: postgresql+asyncpg://ci:cipassword@postgres/testdb
    commands:
      - pytest tests/integration/ -v
```

### Go with multi-platform Docker build

```yaml
steps:
  - name: test
    image: golang:1.22-alpine
    commands:
      - go test ./...

  - name: build-and-push
    image: woodpeckerci/plugin-docker-buildx
    settings:
      repo: codeberg.org/username/my-app
      registry: codeberg.org
      username:
        from_secret: CODEBERG_USERNAME
      password:
        from_secret: CODEBERG_TOKEN
      platforms:
        - linux/amd64
        - linux/arm64
      tags:
        - latest
        - ${CI_COMMIT_TAG}
    when:
      event: tag
```

### Release pipeline with binary assets

```yaml
steps:
  - name: build-binaries
    image: golang:1.22
    commands:
      - |
        for os in linux darwin windows; do
          for arch in amd64 arm64; do
            GOOS=$os GOARCH=$arch go build \
              -o dist/myapp-$os-$arch \
              .
          done
        done

  - name: publish-release
    image: woodpeckerci/plugin-gitea-release
    settings:
      url: https://codeberg.org
      api_key:
        from_secret: CODEBERG_TOKEN
      title: "Release ${CI_COMMIT_TAG}"
      files:
        - dist/*
    when:
      event: tag
```

---

## Self-hosted runners

If you need more compute, custom environments or want to avoid shared resource limits, run your own Woodpecker agent.

### Installing the Woodpecker agent

```bash
# Download the agent binary
wget https://github.com/woodpecker-ci/woodpecker/releases/latest/download/woodpecker-agent_linux_amd64.tar.gz
tar xzf woodpecker-agent_linux_amd64.tar.gz
```

### Registering with ci.codeberg.org

1. In ci.codeberg.org, go to **Admin** > **Agents** (requires admin access or contact Codeberg admins)
2. For personal use, Codeberg provides agent tokens to trusted users on request

Alternatively, run your own complete Woodpecker server connected to your Codeberg account:

```yaml
# docker-compose.yml for self-hosted Woodpecker
version: "3"

services:
  woodpecker-server:
    image: woodpeckerci/woodpecker-server:latest
    environment:
      WOODPECKER_OPEN: "true"
      WOODPECKER_FORGEJO: "true"
      WOODPECKER_FORGEJO_URL: "https://codeberg.org"
      WOODPECKER_FORGEJO_CLIENT: "YOUR_OAUTH2_CLIENT_ID"
      WOODPECKER_FORGEJO_SECRET: "YOUR_OAUTH2_SECRET"
      WOODPECKER_AGENT_SECRET: "a-long-random-string"
    ports:
      - "8000:8000"

  woodpecker-agent:
    image: woodpeckerci/woodpecker-agent:latest
    environment:
      WOODPECKER_SERVER: "woodpecker-server:9000"
      WOODPECKER_AGENT_SECRET: "a-long-random-string"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    depends_on:
      - woodpecker-server
```

Register the OAuth2 app in Codeberg: Settings > Applications > Manage OAuth2 Applications > Create a new OAuth2 Application with callback URL `https://your-woodpecker.example.com/authorize`.

---

## Try It Yourself

**Exercise 1 - Connect to ci.codeberg.org**

1. Go to [ci.codeberg.org](https://ci.codeberg.org)
2. Log in with your Codeberg account
3. Click **+ Add repository**
4. Enable your `hello-codeberg` repository

**Exercise 2 - Write your first pipeline**

In your `hello-codeberg` repository, create `.woodpecker.yml`:

```yaml
steps:
  - name: hello
    image: alpine
    commands:
      - echo "Hello from Woodpecker CI!"
      - echo "Branch: ${CI_COMMIT_BRANCH}"
      - echo "Commit: ${CI_COMMIT_SHA}"
      - uname -a
```

Push to Codeberg and watch the pipeline run at ci.codeberg.org.

**Exercise 3 - Add a real test step**

For a Python project:

```yaml
steps:
  - name: test
    image: python:3.12-slim
    commands:
      - python -m py_compile *.py
      - echo "Syntax check passed"
```

For a Node.js project:

```yaml
steps:
  - name: test
    image: node:20-alpine
    commands:
      - node --version
      - echo "Node.js is available"
```

**Exercise 4 - Add a secret**

1. In ci.codeberg.org, go to your repository > **Settings** > **Secrets**
2. Add a secret named `GREETING` with value `Codeberg User`
3. Update your pipeline:

```yaml
steps:
  - name: greet
    image: alpine
    environment:
      NAME:
        from_secret: GREETING
    commands:
      - echo "Hello, ${NAME}!"
```

---

## Common Mistakes

**Forgetting to enable the repository in Woodpecker**

Even with a `.woodpecker.yml` file, pipelines do not run until you explicitly enable the repository in the Woodpecker CI interface. Go to ci.codeberg.org > **+ Add repository** > enable it.

**No `.woodpecker.yml` file**

Woodpecker only triggers on repositories that contain `.woodpecker.yml`. A push to a repository without this file produces no pipeline run - no error, just silence. Create the file first.

**Using GitHub Actions `uses:` syntax**

Woodpecker does not support the GitHub Actions `uses: actions/checkout@v4` syntax. Use Docker images directly (`image: alpine`) and Woodpecker plugins from the plugin registry.

**Not adding a `when` filter to slow or resource-intensive steps**

Without `when:` conditions, every push triggers every step, including expensive build and deploy steps. Add `when: { event: push, branch: main }` to deployment steps so they only run on merges to main.

**Storing secrets in the pipeline YAML**

Never put credentials directly in `.woodpecker.yml`. The file is version-controlled and public (for public repos). Always use `from_secret: SECRET_NAME` and store the value in Woodpecker's secret management.

**Expecting GitHub Actions caching to work the same way**

Woodpecker uses Docker volumes for caching, not the `actions/cache` action syntax. Define named volumes in your pipeline and use them across steps to persist dependencies between runs on the same runner.

---

## Summary

Codeberg CI is powered by Woodpecker CI, an open source CI/CD system available at [ci.codeberg.org](https://ci.codeberg.org). Connect by authorising the OAuth2 application, then enable specific repositories.

Pipeline files live at `.woodpecker.yml`. Each step runs inside a Docker image specified with `image:`. Commands run sequentially with `commands:`. Plugins (Docker images following a convention) handle complex tasks like Docker build, SSH deploy and notifications - configured with `settings:` and `from_secret:`.

Triggers control when steps run: `push`, `pull_request`, `tag`, `cron` and `manual`. Per-step `when:` conditions restrict execution to specific events or branches.

Secrets are stored encrypted in Woodpecker and referenced with `from_secret:`. Never put credentials directly in pipeline YAML. Matrix builds run the same pipeline with different variable combinations. Services provide Docker sidecar containers (databases, queues) for integration tests.

For heavy CI usage, set up self-hosted Woodpecker agents to avoid shared resource limits on ci.codeberg.org.

---

## Sources

- [Woodpecker CI: Documentation](https://woodpecker-ci.org/docs/intro)
- [Woodpecker CI: Pipeline syntax](https://woodpecker-ci.org/docs/usage/workflow-syntax)
- [Woodpecker CI: Plugins directory](https://woodpecker-ci.org/plugins)
- [Woodpecker CI: Secrets](https://woodpecker-ci.org/docs/usage/secrets)
- [Codeberg: CI documentation](https://codeberg.org/Codeberg/Community/wiki/CI)
- [Woodpecker CI: Forgejo integration](https://woodpecker-ci.org/docs/administration/vcs/forgejo)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

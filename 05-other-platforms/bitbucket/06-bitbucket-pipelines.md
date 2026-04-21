# Bitbucket Pipelines

**Difficulty:** 🔴 Advanced | **Time:** 60 minutes

Bitbucket Pipelines is the built-in CI/CD system for Bitbucket Cloud. Pipelines run automatically when you push code, open a pull request or trigger them manually. They build, test and deploy your software using Docker containers - you define what happens in a YAML file called `bitbucket-pipelines.yml` committed to the root of your repository.

This file is a complete reference for Bitbucket Pipelines. It covers the full YAML syntax, every major feature (steps, stages, parallel steps, services, caches, artifacts, pipes, deployment environments and self-hosted runners), and real-world patterns for building production-grade pipelines.

---

## Table of Contents

1. [How Pipelines works](#how-pipelines-works)
2. [The bitbucket-pipelines.yml file](#the-bitbucket-pipelinesyml-file)
3. [Pipeline triggers](#pipeline-triggers)
4. [Steps](#steps)
5. [Stages](#stages)
6. [Parallel steps](#parallel-steps)
7. [Variables and secrets](#variables-and-secrets)
8. [Caching dependencies](#caching-dependencies)
9. [Artifacts](#artifacts)
10. [Services (databases and other containers)](#services-databases-and-other-containers)
11. [Pipes: pre-built integrations](#pipes-pre-built-integrations)
12. [Deployment environments](#deployment-environments)
13. [Self-hosted runners](#self-hosted-runners)
14. [Pipeline minutes and limits](#pipeline-minutes-and-limits)
15. [Practical pipeline examples](#practical-pipeline-examples)
16. [Debugging failed pipelines](#debugging-failed-pipelines)
17. [Try It Yourself](#try-it-yourself)
18. [Common Mistakes](#common-mistakes)
19. [Summary](#summary)
20. [Sources](#sources)

---

## How Pipelines works

When you push a commit to Bitbucket, the platform checks whether a `bitbucket-pipelines.yml` file exists in the repository root. If it does, Bitbucket spins up a Docker container based on the image you specified, clones your repository into it, and runs your pipeline steps inside that container.

Each step in a pipeline runs in a fresh container. This isolation means steps cannot accidentally share state or interfere with each other - but it also means you need to explicitly pass data between steps using **artifacts**.

The pipeline lifecycle:

1. Developer pushes a commit or opens a pull request
2. Bitbucket evaluates the `bitbucket-pipelines.yml` conditions to determine which pipeline to run
3. A Docker container is provisioned using the specified image
4. The repository is cloned into the container
5. Pipeline steps execute in sequence (or in parallel if configured)
6. Results (pass/fail, logs, artifacts) are recorded in Bitbucket
7. The pipeline status appears on the commit, pull request and deployment dashboard

Pipelines run on Bitbucket's cloud infrastructure (Linux-based, with no macOS or Windows hosted runners available). For Windows or macOS builds, you need self-hosted runners.

---

## The bitbucket-pipelines.yml file

The pipeline configuration lives in a file called `bitbucket-pipelines.yml` in the root of your repository. This file is committed alongside your code - it is version-controlled just like everything else.

### Minimal valid pipeline

```yaml
pipelines:
  default:
    - step:
        name: Build and test
        script:
          - echo "Hello from Pipelines"
```

This runs on every push to any branch. A single step echoes a message. Real pipelines replace the echo with actual build and test commands.

### Top-level structure

```yaml
image: atlassian/default-image:4  # default Docker image for all steps

definitions:           # reusable components (caches, services, steps)
  caches:
    node: ~/.npm
  services:
    postgres:
      image: postgres:15

options:               # global pipeline options
  max-time: 30         # maximum minutes per step (default: 120)
  size: 2x             # step size (1x default, 2x double resources)

pipelines:
  default:             # runs on any branch not matched by other triggers
    - step: ...

  branches:            # runs on specific branches
    main:
      - step: ...
    'release/*':
      - step: ...

  pull-requests:       # runs on pull request creation and updates
    '**':
      - step: ...

  tags:                # runs when a tag is pushed
    'v*':
      - step: ...

  custom:              # manually triggered pipelines
    deploy-to-production:
      - step: ...
```

---

## Pipeline triggers

Pipelines are triggered by different events depending on which section of the YAML they are defined in.

### default

```yaml
pipelines:
  default:
    - step:
        script:
          - npm test
```

Runs on every push to any branch that does not match a more specific trigger. This is the fallback. If you push to `main` and have a `branches: main:` section, the `branches: main:` section runs instead, not `default`.

### branches

```yaml
pipelines:
  branches:
    main:
      - step:
          script:
            - npm run build
            - npm run deploy
    develop:
      - step:
          script:
            - npm test
    'feature/*':
      - step:
          script:
            - npm test
    'release/**':
      - step:
          script:
            - npm run release
```

Branch patterns support glob matching: `*` matches within a single path segment, `**` matches across multiple segments. Quote patterns containing `*` or `/` to prevent YAML parsing issues.

### pull-requests

```yaml
pipelines:
  pull-requests:
    '**':              # all PRs regardless of target branch
      - step:
          script:
            - npm test
    'main':            # only PRs targeting main
      - step:
          script:
            - npm test
            - npm run integration-tests
```

Pull request pipelines run when a PR is opened, updated with new commits or synchronised. The pipeline result appears as a build status check on the pull request.

### tags

```yaml
pipelines:
  tags:
    'v*':              # any tag starting with v (v1.0, v2.3.1, etc.)
      - step:
          script:
            - npm run build
            - npm publish
    'v*-rc*':          # release candidates: v1.0-rc1
      - step:
          script:
            - npm run build
```

Tag pipelines run when a Git tag is pushed. Useful for release workflows where you tag a commit to trigger publishing to npm, Docker Hub, PyPI or a deployment.

### custom

```yaml
pipelines:
  custom:
    run-database-migrations:
      - step:
          name: Run migrations
          script:
            - python manage.py migrate
    deploy-to-staging:
      - variables:
          - name: DEPLOY_VERSION
      - step:
          name: Deploy
          script:
            - ./deploy.sh $DEPLOY_VERSION
```

Custom pipelines are triggered manually from the Bitbucket UI or via the API. They do not run automatically on any event. Useful for operational tasks (database migrations, one-off scripts, manual deployments). Custom pipelines can accept input variables that are prompted for when manually triggered.

---

## Steps

A step is the basic unit of work in a pipeline. Each step runs in its own Docker container.

### Step anatomy

```yaml
- step:
    name: Run tests          # displayed in the Bitbucket UI
    image: node:20           # Docker image (overrides the top-level image)
    size: 2x                 # 2x doubles CPU and memory
    max-time: 20             # maximum minutes (overrides global max-time)
    caches:
      - node                 # use the node cache defined in definitions
    services:
      - postgres             # start a postgres service container alongside
    artifacts:
      - coverage/**          # files to pass to subsequent steps
    script:
      - npm ci               # commands run in order; step fails if any returns non-zero
      - npm test
    after-script:            # always runs after script, even on failure
      - echo "Step finished with exit code $BITBUCKET_EXIT_CODE"
```

### Script execution

Each line in `script` is a shell command. They run in order. If any command exits with a non-zero code, the step fails immediately and subsequent commands do not run. Use `after-script` for cleanup that should always happen (uploading test reports, notifying Slack).

```yaml
script:
  - echo "Starting build"
  - npm ci                    # fails here? subsequent lines do not run
  - npm run build
  - npm test
after-script:
  - echo "Build complete with exit: $BITBUCKET_EXIT_CODE"
  # BITBUCKET_EXIT_CODE is 0 for success, 1 for failure
```

### Conditional steps

Steps can be skipped based on conditions:

```yaml
- step:
    name: Deploy to production
    trigger: manual           # requires a human to click Run in the UI
    script:
      - ./deploy.sh

- step:
    name: Notify Slack
    condition:
      changesets:
        includePaths:
          - "src/**"          # only run if files in src/ changed
    script:
      - ./notify-slack.sh
```

### Step images

Each step can use a different Docker image:

```yaml
pipelines:
  default:
    - step:
        name: Build frontend
        image: node:20
        script:
          - npm ci
          - npm run build
    - step:
        name: Build backend
        image: python:3.12
        script:
          - pip install -r requirements.txt
          - python -m pytest
    - step:
        name: Build Docker image
        image: docker:dind
        script:
          - docker build -t myapp:latest .
```

Using specific image tags (`:20`, `:3.12`) rather than `:latest` is important for reproducibility. `:latest` changes without warning and can break your pipeline unexpectedly.

---

## Stages

Stages group steps and run them sequentially. Steps within a stage can run in parallel. Stages make large pipelines readable and allow you to fail fast at early stages before running expensive later ones.

```yaml
pipelines:
  default:
    - stage:
        name: Test
        steps:
          - step:
              name: Unit tests
              script:
                - npm test
          - step:
              name: Lint
              script:
                - npm run lint
    - stage:
        name: Build
        steps:
          - step:
              name: Build
              script:
                - npm run build
    - stage:
        name: Deploy
        steps:
          - step:
              name: Deploy to staging
              deployment: staging
              script:
                - ./deploy.sh staging
```

If the Test stage fails, the Build and Deploy stages do not run. This saves pipeline minutes and provides faster feedback.

---

## Parallel steps

Steps within a stage can run in parallel, reducing total pipeline time:

```yaml
pipelines:
  default:
    - stage:
        name: Test
        steps:
          - step:
              name: Unit tests
              script:
                - npm test
          - step:
              name: Integration tests
              script:
                - npm run test:integration
          - step:
              name: Lint and type check
              script:
                - npm run lint
                - npm run typecheck
```

All three steps start simultaneously. The stage completes when all steps finish. If any step fails, the stage fails.

> [!NOTE]
> Parallel steps each consume their own pipeline minutes simultaneously. Three parallel steps each taking 5 minutes consume 15 pipeline minutes, not 5. Balance parallelism against your monthly minutes allocation.

### Parallel with dependencies

You can mix parallel and sequential steps using multiple stages:

```yaml
pipelines:
  default:
    - stage:
        name: Validate          # runs first
        steps:
          - step:
              name: Lint
              script: [ npm run lint ]
          - step:
              name: Type check
              script: [ npm run typecheck ]
    - stage:
        name: Test              # runs after Validate passes
        steps:
          - step:
              name: Unit tests
              script: [ npm test ]
          - step:
              name: E2E tests
              script: [ npm run test:e2e ]
    - step:
        name: Deploy            # runs after Test passes
        script: [ ./deploy.sh ]
```

---

## Variables and secrets

### Built-in variables

Bitbucket provides a set of environment variables automatically available in every pipeline step:

| Variable | Value |
|---|---|
| `BITBUCKET_REPO_SLUG` | Repository name slug |
| `BITBUCKET_REPO_FULL_NAME` | `workspace/repo` |
| `BITBUCKET_BRANCH` | Current branch name |
| `BITBUCKET_TAG` | Current tag (if triggered by tag) |
| `BITBUCKET_COMMIT` | Full commit SHA |
| `BITBUCKET_BUILD_NUMBER` | Pipeline build number (increments per run) |
| `BITBUCKET_WORKSPACE` | Workspace slug |
| `BITBUCKET_PR_ID` | Pull request ID (if PR pipeline) |
| `BITBUCKET_PR_DESTINATION_BRANCH` | Target branch of PR |
| `BITBUCKET_DEPLOYMENT_ENVIRONMENT` | Deployment environment name |
| `BITBUCKET_PIPELINE_UUID` | Unique identifier for this pipeline run |

### Repository variables

Defined in **Repository settings** > **Repository variables**. Available in all pipeline steps automatically.

```yaml
script:
  - docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
  - docker push myapp:$BITBUCKET_COMMIT
```

Secured variables are not echoed in logs and are not accessible by forked repository pipelines.

### Workspace variables

Defined at the workspace level and available to all repositories in the workspace. Useful for shared credentials (cloud provider keys used across multiple repos).

### Inline variables in YAML

Non-sensitive configuration can be set inline:

```yaml
- step:
    name: Build
    script:
      - export NODE_ENV=production
      - export API_URL=https://api.example.com
      - npm run build
```

### Custom pipeline variables

Custom pipelines can prompt for variables when triggered manually:

```yaml
pipelines:
  custom:
    deploy:
      - variables:
          - name: TARGET_ENV
            default: staging
            allowed-values:
              - staging
              - production
          - name: DEPLOY_TAG
      - step:
          script:
            - ./deploy.sh $TARGET_ENV $DEPLOY_TAG
```

> [!WARNING]
> Never hardcode API keys, passwords or tokens directly in `bitbucket-pipelines.yml`. The file is committed to your repository and visible to everyone with repository access. Always use secured repository or workspace variables for sensitive values.

---

## Caching dependencies

Caching stores downloaded dependencies between pipeline runs, dramatically reducing step execution time. Instead of downloading 500 MB of npm packages on every run, you download them once and restore from cache on subsequent runs.

### Defining caches

Caches are defined in the `definitions` section and referenced in steps:

```yaml
definitions:
  caches:
    node: ~/.npm              # npm cache directory
    pip: ~/.cache/pip         # Python pip cache
    gradle: ~/.gradle/caches  # Gradle cache
    maven: ~/.m2/repository   # Maven local repository
    composer: ~/.composer/cache  # PHP Composer cache

pipelines:
  default:
    - step:
        caches:
          - node
        script:
          - npm ci
          - npm test
```

### Built-in cache shortcuts

Bitbucket provides built-in cache shortcuts for common tools. These use the correct cache paths automatically:

```yaml
caches:
  - node      # npm
  - pip       # Python pip
  - gradle    # Gradle
  - maven     # Maven
  - composer  # PHP Composer
  - docker    # Docker image layers
```

### Cache invalidation

Caches are keyed by their path. When the cache is first built, it is stored. On subsequent runs, the cached directory is restored before your script runs. Caches do not expire automatically - they are invalidated when:

- You manually clear the cache in the Bitbucket UI (Repository > Pipelines > Caches)
- The cache exceeds the maximum size (1 GB per cache)
- Seven days pass without the cache being used

To force cache invalidation when dependencies change (e.g. after updating `package.json`), add a custom cache key based on the lock file:

```yaml
definitions:
  caches:
    npm-lock:
      key:
        files:
          - package-lock.json
      path: node_modules
```

This cache is invalidated automatically when `package-lock.json` changes.

---

## Artifacts

Artifacts are files produced by one step and consumed by a subsequent step. Without artifacts, each step starts with a fresh clone of the repository and nothing built by previous steps.

### Defining artifacts

```yaml
pipelines:
  default:
    - step:
        name: Build
        script:
          - npm ci
          - npm run build    # produces dist/ directory
        artifacts:
          - dist/**          # pass dist/ to the next step
    - step:
        name: Deploy
        script:
          - aws s3 sync dist/ s3://my-bucket/
          # dist/ is available here because it was declared as an artifact
```

### Artifact patterns

```yaml
artifacts:
  - dist/**             # everything in dist/
  - coverage/**         # test coverage reports
  - '*.log'             # all log files in root
  - reports/*.xml       # XML files in reports/
  - build/app.jar       # specific file
```

### Artifacts vs caches

| Feature | Artifacts | Caches |
|---|---|---|
| Purpose | Pass build outputs between steps | Speed up dependency downloads |
| Scope | Within a single pipeline run | Across multiple pipeline runs |
| Storage | Temporary (pipeline duration) | Persistent (up to 7 days) |
| Direction | Forward only (step N to step N+1) | Bidirectional (restore and save) |

Use **artifacts** for: compiled code, test reports, Docker layers, built binaries.
Use **caches** for: npm packages, pip packages, Maven dependencies, Gradle caches.

---

## Services (databases and other containers)

Services run alongside your pipeline step in separate Docker containers. They are used for databases, message queues, browsers and other infrastructure your tests depend on.

### Defining a service

```yaml
definitions:
  services:
    postgres:
      image: postgres:15
      environment:
        POSTGRES_DB: testdb
        POSTGRES_USER: testuser
        POSTGRES_PASSWORD: testpassword
    redis:
      image: redis:7
    elasticsearch:
      image: elasticsearch:8.11.0
      environment:
        discovery.type: single-node
        ES_JAVA_OPTS: "-Xms512m -Xmx512m"
      memory: 1024          # MB allocated to this service container

pipelines:
  default:
    - step:
        name: Integration tests
        services:
          - postgres
          - redis
        script:
          - npm run test:integration
```

### Connecting to services

Services are accessible via `localhost` on their default port. The database in the example above is reachable at `localhost:5432` (PostgreSQL default port).

```yaml
script:
  - export DATABASE_URL=postgresql://testuser:testpassword@localhost:5432/testdb
  - npm run test:integration
```

### Docker-in-Docker

To build and push Docker images inside a pipeline step, use Docker-in-Docker (dind):

```yaml
definitions:
  services:
    docker:
      memory: 2048          # Docker service needs extra memory

pipelines:
  default:
    - step:
        name: Build and push Docker image
        services:
          - docker
        script:
          - docker build -t my-app:$BITBUCKET_COMMIT .
          - docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
          - docker push my-app:$BITBUCKET_COMMIT
```

> [!NOTE]
> The `docker` service in Pipelines uses Docker-in-Docker (dind). It enables running Docker commands inside a pipeline step. Each dind session is isolated - images built in one step are not available in another without pushing to a registry.

---

## Pipes: pre-built integrations

Pipes are pre-built pipeline steps packaged as Docker images. Instead of writing complex shell scripts for common tasks (deploying to AWS, sending Slack notifications, running security scans), you use a pipe with a few lines of configuration.

### Using a pipe

```yaml
- step:
    name: Deploy to AWS S3
    script:
      - pipe: atlassian/aws-s3-deploy:1.6.0
        variables:
          AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
          AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
          AWS_DEFAULT_REGION: 'eu-west-1'
          S3_BUCKET: 'my-production-bucket'
          LOCAL_PATH: 'dist'
```

The `pipe:` key replaces `script:`. Variables are passed as key-value pairs specific to that pipe.

### Common official pipes

**Atlassian pipes** (officially maintained):

```yaml
# Deploy to AWS S3
- pipe: atlassian/aws-s3-deploy:1.6.0

# Deploy to AWS Elastic Beanstalk
- pipe: atlassian/aws-elasticbeanstalk-deploy:1.0.0

# Deploy to AWS Lambda
- pipe: atlassian/aws-lambda-deploy:1.9.0

# Publish to AWS ECR (Elastic Container Registry)
- pipe: atlassian/aws-ecr-push-image:2.4.0

# Deploy to Kubernetes
- pipe: atlassian/kubectl-run:2.4.0

# Publish to Microsoft Azure Web App
- pipe: microsoft/azure-web-apps-deploy:1.0.0

# Send a Slack notification
- pipe: atlassian/slack-notify:2.1.0
  variables:
    WEBHOOK_URL: $SLACK_WEBHOOK_URL
    MESSAGE: 'Build $BITBUCKET_BUILD_NUMBER succeeded'

# Snyk security scan
- pipe: snyk/snyk-scan:1.0.0
  variables:
    SNYK_TOKEN: $SNYK_TOKEN
    LANGUAGE: 'node'

# SonarCloud scan
- pipe: sonarsource/sonarcloud-scan:2.0.0
  variables:
    SONAR_TOKEN: $SONAR_TOKEN
```

### Pipe versioning

Always pin pipe versions (`atlassian/aws-s3-deploy:1.6.0` not `atlassian/aws-s3-deploy:latest`). Unpinned pipes may update and break your pipeline without warning.

---

## Deployment environments

Deployment environments in Bitbucket track which version of your code has been deployed to which environment. They provide a visual deployment history and enable environment-specific variables.

### Environment types

Bitbucket supports three built-in environment types:

- **Test** - development and testing environments
- **Staging** - pre-production environments
- **Production** - live production environments

### Configuring environments

Environments are created in **Repository settings** > **Deployments**.

### Using environments in pipelines

```yaml
pipelines:
  branches:
    main:
      - step:
          name: Deploy to staging
          deployment: staging        # links this step to the staging environment
          script:
            - ./deploy.sh staging
      - step:
          name: Deploy to production
          deployment: production     # links this step to the production environment
          trigger: manual            # requires human approval in the UI
          script:
            - ./deploy.sh production
```

### Environment variables

Each environment can have its own variables that override repository-level variables:

- Repository variable: `DATABASE_URL=postgresql://dev-db/app`
- Staging environment variable: `DATABASE_URL=postgresql://staging-db/app`
- Production environment variable: `DATABASE_URL=postgresql://prod-db/app`

When a step runs with `deployment: production`, it uses the production environment's `DATABASE_URL` automatically.

### Deployment permissions (Premium plan)

On the Premium plan, environments can require specific users to approve a deployment before the step runs. This creates a manual gate in your pipeline:

1. The deploy step starts
2. Bitbucket pauses and notifies the designated approvers
3. An approver clicks "Run" in the Bitbucket UI
4. The deployment proceeds

This is suitable for production deployments where a second pair of eyes (or a release manager sign-off) is required before code goes live.

---

## Self-hosted runners

Bitbucket's hosted runners are Linux-only. For Windows builds, macOS builds, builds requiring access to private network resources, or builds needing specialised hardware, you need self-hosted runners.

### Runner types (V5)

The V5 runner architecture (2025-2026) supports two tiers:

**Basic runners** (free, up to 100 per workspace):
- Standard CPU and memory
- Suitable for most builds
- No additional cost

**Premium runners** ($15/slot/month):
- Customisable CPU and memory allocation
- Docker volume mount support
- S3/GCS external cache storage
- OIDC token support for secretless cloud authentication
- Advanced orchestration

> [!IMPORTANT]
> Pre-V5 runners will be disabled on **3 June 2026** for monthly plans and **3 December 2026** for annual plans. If you are running older self-hosted runners, upgrade to V5 before these dates.

### Setting up a runner

1. Go to **Workspace settings** (or **Repository settings** for repo-scoped runners) > **Runners**
2. Click **Add runner**
3. Choose the operating system and architecture
4. Follow the installation instructions

🪟 **Windows installation:**
```powershell
# Download the runner
Invoke-WebRequest -Uri "https://product-downloads.atlassian.com/software/bitbucket/pipelines/runner/latest/windows/runner.exe" -OutFile runner.exe

# Register the runner (use the token shown in Bitbucket UI)
.\runner.exe --setup --accountUuid {workspace-uuid} --repositoryUuid {repo-uuid} --runnerUuid {runner-uuid} --token {token} --name "Windows Runner"

# Start the runner
.\runner.exe
```

🍎 **Mac installation:**
```bash
# Download the runner
curl -Lo runner https://product-downloads.atlassian.com/software/bitbucket/pipelines/runner/latest/macos/runner

chmod +x runner

# Register and start
./runner --setup --accountUuid {workspace-uuid} --token {token} --name "Mac Runner"
./runner
```

🐧 **Linux installation:**
```bash
# Download the runner
curl -Lo runner https://product-downloads.atlassian.com/software/bitbucket/pipelines/runner/latest/linux/runner

chmod +x runner

# Register and start
./runner --setup --accountUuid {workspace-uuid} --token {token} --name "Linux Runner"
./runner
```

### Using a runner in a pipeline

```yaml
- step:
    name: Windows build
    runs-on:
      - self.hosted
      - windows              # label matching the runner's labels
    script:
      - dotnet build
      - dotnet test

- step:
    name: macOS build
    runs-on:
      - self.hosted
      - macos
    script:
      - xcodebuild test
```

### Runner labels

When registering a runner, assign labels that describe its capabilities (OS, architecture, GPU, region). Steps specify `runs-on` with labels - the step is routed to any runner matching all specified labels.

---

## Pipeline minutes and limits

### Monthly minutes

| Plan | Hosted minutes/month | Reset |
|---|---|---|
| Free | 50 | 1st of month |
| Standard | 2,500 | 1st of month |
| Premium | 3,500 | 1st of month |

Additional minutes: $10 per 1,000 minutes. Self-hosted runners do not consume hosted minutes.

### Other limits

| Limit | Value |
|---|---|
| Maximum step runtime | 120 minutes (default), configurable to 120 max |
| Maximum pipeline runtime | No explicit cap (sum of steps) |
| Maximum artifact size | 1 GB per step |
| Maximum cache size | 1 GB per cache key |
| Maximum parallel steps | 10 per stage |
| Maximum steps per pipeline | 100 |
| Clones per minute (rate limit) | 20 |

### Optimising minute usage

Reducing pipeline minutes without sacrificing quality:

- **Use caches aggressively** - restore dependencies from cache rather than downloading on every run
- **Run tests in parallel** - parallel steps reduce wall clock time but use the same minutes
- **Use conditional steps** - skip expensive steps when only documentation changed
- **Optimise Docker images** - use slim base images to reduce pull time
- **Shallow clone** - use `--depth 1` for CI builds that do not need full history

```yaml
# Shallow clone option in pipeline config
clone:
  depth: 1
```

---

## Practical pipeline examples

### Node.js web application

```yaml
image: node:20-alpine

definitions:
  caches:
    node: ~/.npm

pipelines:
  default:
    - step:
        name: Install and test
        caches:
          - node
        script:
          - npm ci
          - npm run lint
          - npm test
        artifacts:
          - coverage/**

  branches:
    main:
      - step:
          name: Install and test
          caches:
            - node
          script:
            - npm ci
            - npm run lint
            - npm test
      - step:
          name: Build
          caches:
            - node
          script:
            - npm ci
            - npm run build
          artifacts:
            - dist/**
      - step:
          name: Deploy to production
          deployment: production
          trigger: manual
          script:
            - pipe: atlassian/aws-s3-deploy:1.6.0
              variables:
                AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
                AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
                AWS_DEFAULT_REGION: 'eu-west-1'
                S3_BUCKET: $PRODUCTION_BUCKET
                LOCAL_PATH: 'dist'
```

### Python Django application

```yaml
image: python:3.12-slim

definitions:
  caches:
    pip: ~/.cache/pip
  services:
    postgres:
      image: postgres:15
      environment:
        POSTGRES_DB: testdb
        POSTGRES_USER: postgres
        POSTGRES_PASSWORD: password

pipelines:
  default:
    - step:
        name: Test
        caches:
          - pip
        services:
          - postgres
        script:
          - pip install -r requirements.txt
          - python manage.py migrate --settings=config.settings.test
          - python manage.py test --settings=config.settings.test

  branches:
    main:
      - step:
          name: Test and lint
          caches:
            - pip
          services:
            - postgres
          script:
            - pip install -r requirements.txt
            - flake8 .
            - python manage.py test --settings=config.settings.test
      - step:
          name: Deploy
          deployment: production
          script:
            - pip install awsebcli
            - eb deploy production-environment
```

### Docker image build and push

```yaml
definitions:
  services:
    docker:
      memory: 2048

pipelines:
  branches:
    main:
      - step:
          name: Build and push
          services:
            - docker
          script:
            - export IMAGE_TAG=$BITBUCKET_COMMIT
            - docker build -t $DOCKER_HUB_USERNAME/myapp:$IMAGE_TAG .
            - docker tag $DOCKER_HUB_USERNAME/myapp:$IMAGE_TAG $DOCKER_HUB_USERNAME/myapp:latest
            - docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD
            - docker push $DOCKER_HUB_USERNAME/myapp:$IMAGE_TAG
            - docker push $DOCKER_HUB_USERNAME/myapp:latest
```

### Monorepo with conditional steps

```yaml
pipelines:
  default:
    - step:
        name: Build frontend
        condition:
          changesets:
            includePaths:
              - "frontend/**"
        image: node:20
        script:
          - cd frontend && npm ci && npm run build
    - step:
        name: Build backend
        condition:
          changesets:
            includePaths:
              - "backend/**"
        image: python:3.12
        script:
          - cd backend && pip install -r requirements.txt && python -m pytest
```

---

## Debugging failed pipelines

### Reading pipeline logs

Every pipeline step writes output to a log visible in the Bitbucket UI. When a step fails:

1. Open the pipeline run from **Repository** > **Pipelines**
2. Click the failed step
3. Scroll to the bottom of the log to find the error
4. Look for the exit code - non-zero means failure

### Common failure causes

**Image pull failure:**
```
Unable to find image 'node:999' locally
```
The Docker image tag does not exist. Check your image name and tag.

**Script command not found:**
```
bash: npm: command not found
```
The tool is not installed in the Docker image. Use a different image that includes the tool, or install it in the script.

**Environment variable not set:**
```
Error: AWS_ACCESS_KEY_ID is not set
```
Check that the variable is defined in repository or workspace settings, and that the variable name matches exactly (case-sensitive).

**Artifact not found in next step:**
```
No such file or directory: dist/index.html
```
The artifact glob pattern in the previous step did not match the files. Check that the build actually produced files at the expected path, and that the artifact pattern is correct.

**Service connection refused:**
```
Error: connect ECONNREFUSED 127.0.0.1:5432
```
The service container is not ready when the script tries to connect. Add a health check wait:
```bash
# Wait for postgres to be ready
until pg_isready -h localhost -p 5432; do sleep 1; done
```

### Re-running a pipeline

Failed pipelines can be re-run from the Bitbucket UI without pushing a new commit:
1. Open the pipeline run
2. Click **Re-run** (or **Re-run failed steps** to skip steps that passed)

Re-running uses the same commit and same YAML configuration. If the failure was caused by a flaky test or a temporary network issue, a re-run may succeed.

### SSH debugging

For complex issues, enable SSH access to a running pipeline step for interactive debugging. This feature is available in the Bitbucket UI during a pipeline run. An SSH key grants you terminal access to the running container, allowing you to inspect the environment, run commands manually and diagnose issues interactively.

---

## Try It Yourself

**Exercise 1 - Create your first pipeline**

1. In your Bitbucket repository, create a file `bitbucket-pipelines.yml` at the root:

```yaml
image: node:20-alpine

pipelines:
  default:
    - step:
        name: Hello Pipelines
        script:
          - echo "Pipeline is working"
          - node --version
          - echo "Commit: $BITBUCKET_COMMIT"
          - echo "Branch: $BITBUCKET_BRANCH"
```

2. Commit and push the file
3. Watch the pipeline run in **Repository** > **Pipelines**

**Exercise 2 - Add caching**

Extend the pipeline to install and cache npm dependencies:

```yaml
image: node:20-alpine

definitions:
  caches:
    node: ~/.npm

pipelines:
  default:
    - step:
        name: Install dependencies
        caches:
          - node
        script:
          - npm init -y
          - npm install lodash
          - echo "Dependencies installed"
```

Run the pipeline twice and compare the step duration - the second run should be faster due to caching.

**Exercise 3 - Add a branch-specific pipeline**

```yaml
pipelines:
  default:
    - step:
        script:
          - echo "Running on branch: $BITBUCKET_BRANCH"

  branches:
    main:
      - step:
          name: Main branch build
          script:
            - echo "This only runs on main"
```

Push to a feature branch, then push to `main`. Observe which pipeline runs in each case.

**Exercise 4 - Add a repository variable**

1. Go to **Repository settings** > **Repository variables**
2. Add: `MY_APP_ENV` = `development`
3. Update your pipeline:

```yaml
pipelines:
  default:
    - step:
        script:
          - echo "Environment is: $MY_APP_ENV"
```

Push and verify the variable appears in the pipeline log.

---

## Common Mistakes

**Using `:latest` image tags**

`node:latest` changes whenever a new Node.js version is released. Your pipeline may work today with Node 20 and break tomorrow when `latest` becomes Node 22 with a breaking API change. Always pin: `node:20-alpine`, `python:3.12-slim`.

**Not declaring artifacts between steps**

Data produced by one step (compiled code, test reports) is not available in the next step unless explicitly declared as an artifact. If your deploy step cannot find the built files, check that the build step declares them as artifacts.

**Storing secrets in the YAML file**

Never put API keys, passwords or tokens directly in `bitbucket-pipelines.yml`. The file is version-controlled and visible to everyone with repository access. Use secured repository or workspace variables.

**Forgetting to wait for service containers**

Database service containers take a second or two to start after the step begins. Connecting immediately often fails with a connection refused error. Add a readiness check loop before running database-dependent tests.

**Running parallel steps without considering minute costs**

Three parallel 10-minute steps consume 30 minutes, not 10. On the free plan (50 minutes/month), running parallel steps aggressively exhausts the allocation quickly.

**Not pinning pipe versions**

`pipe: atlassian/aws-s3-deploy:latest` can update to a version with breaking changes between runs. Pin to a specific version: `pipe: atlassian/aws-s3-deploy:1.6.0`.

**Putting all logic in one giant step**

One step with 50 commands is hard to debug when it fails - you cannot tell which command failed without reading through the full log. Split logic into named steps. When a step fails, the step name tells you immediately where the problem is.

---

## Summary

Bitbucket Pipelines is configured through a `bitbucket-pipelines.yml` file committed to your repository. Pipelines are triggered by branch pushes, pull requests, tags or manual execution. Steps are the basic unit of work, each running in a fresh Docker container. Stages group steps and enable fast-fail behaviour. Steps within a stage can run in parallel to reduce total build time.

Caches speed up pipelines by storing dependency downloads between runs. Artifacts pass build outputs from one step to the next within a pipeline run. Services provide databases, message queues and other infrastructure for integration tests.

Pipes are pre-built integrations for common tasks: deploying to AWS, Azure or GCP, sending Slack notifications, running security scans. Always pin pipe versions.

Deployment environments track which code has been deployed where, enable environment-specific variables and (on Premium) support manual approval gates before production deployments.

Self-hosted runners enable Windows and macOS builds, access to private network resources and specialised hardware. V5 runners are the current architecture; pre-V5 runners are being retired in 2026.

---

## Sources

- [Atlassian: Bitbucket Pipelines documentation](https://support.atlassian.com/bitbucket-cloud/docs/get-started-with-bitbucket-pipelines/)
- [Atlassian: bitbucket-pipelines.yml reference](https://support.atlassian.com/bitbucket-cloud/docs/bitbucket-pipelines-configuration-reference/)
- [Atlassian: Caches in Pipelines](https://support.atlassian.com/bitbucket-cloud/docs/cache-dependencies/)
- [Atlassian: Artifacts in Pipelines](https://support.atlassian.com/bitbucket-cloud/docs/use-artifacts-in-steps/)
- [Atlassian: Pipes documentation](https://support.atlassian.com/bitbucket-cloud/docs/use-pipes-in-pipelines/)
- [Atlassian: Self-hosted runners](https://support.atlassian.com/bitbucket-cloud/docs/runners/)
- [Atlassian: Announcing V5 self-hosted runners](https://www.atlassian.com/blog/bitbucket/announcing-v5-self-hosted-runners)
- [Atlassian: Deployment environments](https://support.atlassian.com/bitbucket-cloud/docs/set-up-and-monitor-deployments/)
- [Atlassian: Built-in pipeline variables](https://support.atlassian.com/bitbucket-cloud/docs/variables-and-secrets/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

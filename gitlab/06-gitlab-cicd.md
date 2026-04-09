# GitLab CI/CD

**Difficulty:** 🔴 Advanced | **Time:** 60 minutes

GitLab CI/CD is the built-in continuous integration and continuous delivery system that runs automated pipelines every time you push code. It is one of GitLab's most powerful features and one of the primary reasons enterprises choose the platform. You define your entire pipeline in a single `.gitlab-ci.yml` file at the root of your repository. When you push, GitLab reads that file and runs whatever you have configured — tests, linting, building Docker images, deploying to staging, scanning for vulnerabilities, sending notifications.

This file covers everything: how pipelines work, every major keyword and what it does, runners and executors, variables, artifacts, caching, dynamic environments, review apps, DAG pipelines, security scanning templates and the common patterns that teams use in production.

---

## Table of Contents

- [How GitLab CI/CD works](#how-gitlab-cicd-works)
- [Your first pipeline](#your-first-pipeline)
- [Stages](#stages)
- [Jobs](#jobs)
- [The script keyword](#the-script-keyword)
- [Images and services](#images-and-services)
- [Rules - controlling when jobs run](#rules---controlling-when-jobs-run)
- [Workflow rules](#workflow-rules)
- [Variables](#variables)
- [Predefined CI/CD variables](#predefined-cicd-variables)
- [Artifacts](#artifacts)
- [Caching](#caching)
- [Needs and DAG pipelines](#needs-and-dag-pipelines)
- [Parallel matrix builds](#parallel-matrix-builds)
- [Extends and hidden jobs](#extends-and-hidden-jobs)
- [Include](#include)
- [Environments and deployments](#environments-and-deployments)
- [Review apps](#review-apps)
- [Merge request pipelines](#merge-request-pipelines)
- [Scheduled pipelines](#scheduled-pipelines)
- [Child and parent pipelines](#child-and-parent-pipelines)
- [Security scanning templates](#security-scanning-templates)
- [Runners](#runners)
- [Installing and registering a runner](#installing-and-registering-a-runner)
- [The pipeline editor](#the-pipeline-editor)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## How GitLab CI/CD Works

The flow is straightforward:

1. You push a commit to a branch in your GitLab repository
2. GitLab detects the push and reads `.gitlab-ci.yml` from the root of your repository
3. GitLab creates a **pipeline** — a collection of jobs organised into stages
4. Jobs are dispatched to **runners** — agents that execute the jobs in isolated environments (usually Docker containers)
5. Each job runs its commands, reports success or failure, and optionally uploads **artifacts** (files produced by the job for later stages to use)
6. The pipeline result (pass/fail) is shown in the MR, on the commit and in the pipeline list

**Key concepts:**

- **Pipeline**: the top-level container. One pipeline per trigger event (push, MR, schedule, API call)
- **Stage**: a phase of the pipeline (e.g. `build`, `test`, `deploy`). Stages run sequentially; all jobs in a stage must pass before the next stage starts
- **Job**: a specific task that runs in one runner. Jobs within the same stage run in parallel by default
- **Runner**: an agent (a server, VM or container) that picks up jobs from the GitLab queue and executes them

---

## Your First Pipeline

Create a file named `.gitlab-ci.yml` at the root of your repository with this content:

```yaml
stages:
  - build
  - test

build-app:
  stage: build
  image: node:20-alpine
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

run-tests:
  stage: test
  image: node:20-alpine
  script:
    - npm ci
    - npm test
```

Commit and push. Go to **Build → Pipelines** to watch it run.

What happens:
1. GitLab assigns `build-app` to an available runner
2. The runner pulls the `node:20-alpine` Docker image and starts a container
3. Inside the container, `npm ci` and `npm run build` run
4. The `dist/` directory is uploaded as an artifact
5. GitLab marks `build-app` as passed and starts the `test` stage
6. `run-tests` runs in a fresh container (no state from `build-app` except artifacts)
7. `npm test` runs and reports results
8. Pipeline passes or fails based on the exit codes of the commands

---

## Stages

Stages define the execution order of the pipeline. All jobs in a stage run in parallel. The next stage starts only when every job in the current stage passes.

```yaml
stages:
  - build
  - test
  - security
  - deploy
```

**Default stages** (used if you do not define `stages`):
`.pre`, `build`, `test`, `deploy`, `.post`

**Special stages:**
- `.pre`: always runs before any other stage, even if you define your own stage list
- `.post`: always runs after all other stages, even if you define your own stage list

```yaml
notify-start:
  stage: .pre
  script: echo "Pipeline started"

cleanup:
  stage: .post
  script: echo "Pipeline finished"
  when: always    # runs even if earlier stages failed
```

---

## Jobs

A job is the fundamental unit of work in a pipeline. Every job needs at minimum a name and a `script`.

```yaml
my-job-name:
  stage: test
  script:
    - echo "This is a job"
```

**Job naming rules:**
- Must be unique within the pipeline
- Cannot use reserved keywords: `image`, `services`, `stages`, `types`, `before_script`, `after_script`, `variables`, `cache`, `include`, `workflow`
- Names starting with `.` are hidden jobs (not run directly — used as templates)

### before_script and after_script

`before_script` runs before the main `script` in every job (or globally before all jobs if defined at the top level). `after_script` always runs after the main `script`, **even if the script failed or the job was cancelled**.

```yaml
default:
  before_script:
    - echo "Global setup — runs before every job's script"

my-job:
  script:
    - npm test
  after_script:
    - echo "Always runs — perfect for cleanup"
    - rm -rf /tmp/test-output
```

`after_script` runs in a separate shell with a 5-minute timeout. Environment variables set in `script` are not available in `after_script`.

### when

Controls under what conditions a job runs:

| Value | Behaviour |
|---|---|
| `on_success` | Run only if all jobs in previous stages passed (default) |
| `on_failure` | Run only if at least one job in a previous stage failed |
| `always` | Run regardless of the result of previous stages |
| `manual` | Only run when manually triggered in the GitLab UI |
| `delayed` | Run after a delay specified by `start_in` |
| `never` | Never run (useful in `rules` to explicitly skip a job) |

```yaml
deploy-production:
  stage: deploy
  script: ./deploy.sh production
  when: manual          # requires a human to click "Run" in the UI
  allow_failure: false  # blocking manual job — pipeline stays "manual" until triggered

notify-failure:
  stage: .post
  script: ./send-alert.sh
  when: on_failure      # only runs if something earlier failed

cleanup:
  stage: .post
  script: ./cleanup.sh
  when: always          # runs regardless
```

### allow_failure

By default, if a job fails (non-zero exit code), the entire pipeline fails and subsequent stages do not run. Set `allow_failure: true` to let the pipeline continue even if this job fails.

```yaml
lint-check:
  script: npx eslint .
  allow_failure: true   # linting failures warn but do not block the pipeline

# Allow failure only for specific exit codes
flaky-test:
  script: ./run-tests.sh
  allow_failure:
    exit_codes: [137]   # permit OOM-killed containers to not fail the pipeline
```

### retry

Automatically retry a failed job up to two additional times:

```yaml
deploy:
  script: ./deploy.sh
  retry:
    max: 2
    when:
      - runner_system_failure  # runner crashed or was unavailable
      - stuck_or_timeout_failure
      - script_failure
```

Retry conditions: `always`, `unknown_failure`, `script_failure`, `api_failure`, `stuck_or_timeout_failure`, `runner_system_failure`, `missing_dependency_failure`, `runner_unsupported`, `stale_schedule`, `job_execution_timeout`, `archived_failure`, `unmet_prerequisites`, `scheduler_failure`, `data_integrity_failure`.

### timeout

Override the default job timeout (set in project settings, default 60 minutes):

```yaml
long-integration-test:
  script: ./integration-tests.sh
  timeout: 3 hours

quick-check:
  script: ./check.sh
  timeout: 5 minutes
```

### tags

Select which runner executes the job based on runner tags:

```yaml
deploy-to-production:
  script: ./deploy.sh
  tags:
    - production     # only runners tagged "production" pick this up
    - docker

gpu-training:
  script: python train.py
  tags:
    - gpu            # only runs on runners with GPU access
```

### resource_group

Ensures only one job with the same resource group key runs at a time across all pipelines. Critical for deployments where concurrent runs could corrupt state:

```yaml
deploy-staging:
  script: ./deploy.sh staging
  resource_group: staging-environment

deploy-production:
  script: ./deploy.sh production
  resource_group: production-environment
```

Even if ten pipelines trigger simultaneously, only one `deploy-staging` job runs at a time. Others queue and wait.

---

## The script keyword

`script` is the list of shell commands that the job runs. Each item in the list is a separate command. If any command exits with a non-zero code, the job fails immediately (unless `allow_failure: true`).

```yaml
build:
  script:
    - echo "Starting build"
    - npm ci
    - npm run build
    - echo "Build complete"
```

**Multi-line commands:**

```yaml
deploy:
  script:
    - |
      if [ "$CI_COMMIT_BRANCH" == "main" ]; then
        echo "Deploying to production"
        ./deploy.sh production
      else
        echo "Deploying to staging"
        ./deploy.sh staging
      fi
```

**Ignoring failures for a single command:**

```yaml
test:
  script:
    - npm test || true    # always continues even if tests fail (use carefully)
    - npm run coverage
```

---

## Images and Services

### image

Specifies the Docker image to use as the job's environment. The runner pulls this image and runs the job inside a container.

```yaml
# Global default image
default:
  image: node:20-alpine

# Override per job
build-java:
  image: maven:3.9-eclipse-temurin-21
  script:
    - mvn clean package

build-go:
  image: golang:1.22-alpine
  script:
    - go build ./...

# With registry authentication
build-private:
  image:
    name: registry.example.com/my-image:latest
    entrypoint: [""]   # override entrypoint to allow script to run
  script:
    - ./build.sh
```

### services

`services` runs additional containers alongside the main job container. They share a network, so the main container can connect to them by the service name.

```yaml
test-with-database:
  image: python:3.12
  services:
    - name: postgres:16
      alias: db              # connect as "db" in the job
    - name: redis:7
      alias: cache
  variables:
    POSTGRES_DB: test_db
    POSTGRES_USER: test_user
    POSTGRES_PASSWORD: secret
    DATABASE_URL: postgresql://test_user:secret@db/test_db
    REDIS_URL: redis://cache:6379
  script:
    - pip install -r requirements.txt
    - python manage.py test

# Docker-in-Docker (building Docker images inside a pipeline)
build-image:
  image: docker:latest
  services:
    - docker:dind
  variables:
    DOCKER_TLS_CERTDIR: "/certs"
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
```

---

## Rules - Controlling When Jobs Run

`rules` is the modern way to control when a job runs. It replaces the deprecated `only` and `except` keywords. `rules` is evaluated top to bottom; the first matching rule wins.

```yaml
deploy:
  script: ./deploy.sh
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
      when: on_success
    - if: $CI_COMMIT_BRANCH == "staging"
      when: manual
    - when: never          # catch-all: skip for all other cases
```

### rules:if

Evaluate a CI/CD variable expression:

```yaml
job:
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"    # only on MR pipelines
    - if: $CI_COMMIT_TAG                                  # only on tag pushes
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH         # only on default branch
    - if: $CI_COMMIT_BRANCH =~ /^release\/.*/             # regex match
```

### rules:changes

Run only when specific files have changed since the last commit:

```yaml
frontend-tests:
  script: npm test
  rules:
    - changes:
        - src/frontend/**/*
        - package.json
        - package-lock.json

backend-tests:
  script: pytest
  rules:
    - changes:
        - src/backend/**/*
        - requirements.txt
```

### rules:exists

Run only if specific files exist in the repository:

```yaml
docker-build:
  script: docker build .
  rules:
    - exists:
        - Dockerfile

terraform-plan:
  script: terraform plan
  rules:
    - exists:
        - "*.tf"
        - "**/*.tf"
```

### Combining conditions

```yaml
deploy-production:
  script: ./deploy.sh
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
      changes:
        - src/**/*
      when: on_success
    - when: never
```

This job only runs when: the branch is `main` AND files in `src/` changed AND previous stages passed.

### rules:variables

Set job-specific variables based on rules:

```yaml
deploy:
  script: ./deploy.sh $ENVIRONMENT
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
      variables:
        ENVIRONMENT: production
    - if: $CI_COMMIT_BRANCH == "staging"
      variables:
        ENVIRONMENT: staging
    - when: never
```

---

## Workflow Rules

`workflow: rules` controls whether a **pipeline** is created at all. Without workflow rules, GitLab creates pipelines for both branch pushes and merge request events, often resulting in duplicate pipelines.

**The standard duplicate-pipeline prevention pattern:**

```yaml
workflow:
  rules:
    # Always run pipelines for merge requests
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    # Run branch pipeline only if no open MR exists for this branch
    - if: $CI_COMMIT_BRANCH && $CI_OPEN_MERGE_REQUESTS
      when: never
    # Run all other branch pushes (branches without open MRs)
    - if: $CI_COMMIT_BRANCH
    # Always run scheduled pipelines
    - if: $CI_PIPELINE_SOURCE == "schedule"
    # Always run manual pipelines triggered via API or web
    - if: $CI_PIPELINE_SOURCE == "web"
    - if: $CI_PIPELINE_SOURCE == "api"
```

This means: if you push to a branch that has an open MR, only the MR pipeline runs. If you push to a branch with no open MR, a branch pipeline runs. No duplicate pipelines.

**Auto-cancelling redundant pipelines:**

```yaml
workflow:
  auto_cancel:
    on_new_commit: interruptible   # cancel old pipelines when new commits arrive
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH
```

Mark individual jobs as `interruptible: true` to allow auto-cancellation of those jobs:

```yaml
test:
  script: npm test
  interruptible: true    # can be cancelled when a newer pipeline starts
```

---

## Variables

Variables are key-value pairs available as environment variables in all pipeline jobs. They can be defined at multiple levels.

### Variable precedence (highest to lowest)

1. Trigger variables (passed in API trigger call)
2. Pipeline-level variables (set via UI when manually running a pipeline)
3. Job-level `variables:` in `.gitlab-ci.yml`
4. Global `variables:` in `.gitlab-ci.yml`
5. Group CI/CD variables (inherited from parent groups)
6. Project CI/CD variables (set in Settings → CI/CD → Variables)
7. Predefined CI/CD variables (set by GitLab automatically)

### Defining variables in .gitlab-ci.yml

```yaml
# Global variables (available in all jobs)
variables:
  NODE_ENV: production
  MAX_RETRIES: "3"
  DEPLOY_REGION: eu-west-1

job:
  # Job-level variables (override globals for this job only)
  variables:
    NODE_ENV: test
  script:
    - echo $NODE_ENV      # prints "test"
    - echo $DEPLOY_REGION # prints "eu-west-1" (inherited from global)
```

### Project and group CI/CD variables

Sensitive values (API keys, passwords, tokens) should never be in `.gitlab-ci.yml`. Store them as project or group CI/CD variables:

**Project**: Settings → CI/CD → Variables → Add variable

**Group**: Group → Settings → CI/CD → Variables → Add variable

Configure each variable with:
- **Type**: Variable (default) or File (writes the value to a temp file; path is the variable value)
- **Protected**: available only in pipelines running on protected branches/tags
- **Masked**: value is never shown in job logs (must be at least 8 characters, cannot contain certain special characters)
- **Expanded**: whether `$OTHER_VAR` references inside the value are expanded

```yaml
# In your .gitlab-ci.yml, just reference the variable — no need to define it
deploy:
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - ./deploy.sh --token $DEPLOY_TOKEN --region $DEPLOY_REGION
```

`$DEPLOY_TOKEN` and `$DEPLOY_REGION` are project CI/CD variables. They are available automatically without any `.gitlab-ci.yml` definition.

---

## Predefined CI/CD Variables

GitLab automatically provides dozens of variables in every pipeline job. These are the most important ones.

### Commit and branch

| Variable | Value |
|---|---|
| `$CI_COMMIT_BRANCH` | Name of the branch being built (empty on tag pipelines) |
| `$CI_COMMIT_TAG` | Name of the tag (only set on tag pipelines) |
| `$CI_COMMIT_SHA` | Full 40-character commit SHA |
| `$CI_COMMIT_SHORT_SHA` | First 8 characters of the commit SHA |
| `$CI_COMMIT_REF_NAME` | Branch or tag name being built |
| `$CI_COMMIT_REF_SLUG` | Branch/tag name with slashes and special chars replaced by hyphens — safe for use in URLs and filenames |
| `$CI_COMMIT_MESSAGE` | Full commit message |
| `$CI_COMMIT_TITLE` | First line of the commit message |
| `$CI_COMMIT_AUTHOR` | Author of the commit (Name <email>) |
| `$CI_DEFAULT_BRANCH` | The project's default branch name (usually `main`) |

### Pipeline

| Variable | Value |
|---|---|
| `$CI_PIPELINE_ID` | Unique pipeline ID (global across GitLab) |
| `$CI_PIPELINE_IID` | Pipeline number within the project (e.g. `42`) |
| `$CI_PIPELINE_SOURCE` | What triggered the pipeline: `push`, `merge_request_event`, `schedule`, `web`, `api`, `trigger`, `pipeline`, `parent_pipeline` |
| `$CI_PIPELINE_URL` | URL to the pipeline in the UI |

### Project

| Variable | Value |
|---|---|
| `$CI_PROJECT_ID` | Numeric project ID |
| `$CI_PROJECT_NAME` | Project name (slug) |
| `$CI_PROJECT_PATH` | Full path including namespace (`group/project`) |
| `$CI_PROJECT_URL` | Full URL to the project |
| `$CI_PROJECT_DIR` | The directory where the repository is cloned |
| `$CI_PROJECT_NAMESPACE` | Namespace (group or username) of the project |

### Job

| Variable | Value |
|---|---|
| `$CI_JOB_ID` | Unique job ID |
| `$CI_JOB_NAME` | Name of the current job |
| `$CI_JOB_STAGE` | Name of the current stage |
| `$CI_JOB_STATUS` | Status at start of `after_script`: `success`, `failed`, `cancelled` |
| `$CI_JOB_TOKEN` | Authentication token for GitLab API (scoped to this project) |
| `$CI_JOB_URL` | URL to this job in the UI |

### Merge request (only in MR pipelines)

| Variable | Value |
|---|---|
| `$CI_MERGE_REQUEST_IID` | MR number within the project (e.g. `42`) |
| `$CI_MERGE_REQUEST_SOURCE_BRANCH_NAME` | Source branch of the MR |
| `$CI_MERGE_REQUEST_TARGET_BRANCH_NAME` | Target branch of the MR |
| `$CI_MERGE_REQUEST_TITLE` | Title of the MR |
| `$CI_OPEN_MERGE_REQUESTS` | Number of open MRs for this branch (useful in workflow rules) |

### Registry

| Variable | Value |
|---|---|
| `$CI_REGISTRY` | Address of the GitLab container registry (`registry.gitlab.com`) |
| `$CI_REGISTRY_IMAGE` | Full image path for this project |
| `$CI_REGISTRY_USER` | Username for registry authentication (always `gitlab-ci-token`) |
| `$CI_REGISTRY_PASSWORD` | Password for registry auth (the `$CI_JOB_TOKEN`) |

### User (who triggered the pipeline)

| Variable | Value |
|---|---|
| `$GITLAB_USER_LOGIN` | GitLab username of whoever triggered the pipeline |
| `$GITLAB_USER_NAME` | Full name |
| `$GITLAB_USER_EMAIL` | Email address |
| `$GITLAB_USER_ID` | Numeric user ID |

---

## Artifacts

Artifacts are files and directories produced by a job that GitLab uploads and stores. They can be downloaded from the UI, passed to later stages and used as pipeline reports.

```yaml
build:
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/               # upload the entire dist directory
      - build/index.html    # or specific files
    exclude:
      - dist/**/*.map       # exclude source maps from the artifact
    expire_in: 1 week       # automatically delete after this time
    when: on_success        # only upload if the job passes (default)
    name: "$CI_JOB_NAME-$CI_COMMIT_REF_SLUG"  # custom archive name
```

### Artifact when

| Value | Behaviour |
|---|---|
| `on_success` | Upload only if the job passes (default) |
| `on_failure` | Upload only if the job fails (useful for test failure logs) |
| `always` | Always upload regardless of outcome |

```yaml
test:
  script: pytest --junit-xml=report.xml
  artifacts:
    when: always    # upload the report even if tests fail
    paths:
      - report.xml
```

### Passing artifacts between jobs

By default, jobs in later stages automatically download artifacts from all jobs in earlier stages. You can control this:

```yaml
deploy:
  script: ./deploy.sh
  dependencies:
    - build      # only download artifacts from "build", not other jobs
    # dependencies: [] would download no artifacts at all
```

### Reports

Artifacts with `reports:` are parsed by GitLab and displayed natively in the UI — in the MR widget, on the pipeline page and in project analytics.

```yaml
test:
  script:
    - pytest --junit-xml=junit.xml --cov=. --cov-report=xml:coverage.xml
  artifacts:
    reports:
      junit: junit.xml          # test results shown in MR pipeline widget
      coverage_report:
        coverage_format: cobertura
        path: coverage.xml      # code coverage shown in MR diff

sast:
  artifacts:
    reports:
      sast: gl-sast-report.json  # security findings shown in MR security panel
```

Other report types: `dast`, `dependency_scanning`, `container_scanning`, `secret_detection`, `performance`, `browser_performance`, `load_performance`, `metrics`, `requirements`, `cyclonedx`.

### Expire_in syntax

```yaml
expire_in: 30 seconds
expire_in: 5 minutes
expire_in: 1 hour
expire_in: 3 days
expire_in: 2 weeks
expire_in: 6 months
expire_in: 1 year
expire_in: never  # keep forever
```

Default: 30 days (configurable per instance). If not set, uses the project or instance default.

---

## Caching

Caching stores files between pipeline runs to speed up jobs that install dependencies. Unlike artifacts (which pass data between jobs in the same pipeline), the cache persists across multiple pipeline executions.

```yaml
default:
  cache:
    key: ${CI_COMMIT_REF_SLUG}   # separate cache per branch
    paths:
      - node_modules/
      - .npm/

test:
  script:
    - npm ci --cache .npm --prefer-offline
    - npm test
```

### Cache keys

The cache key determines which cache is used. Jobs with the same key share a cache. Jobs with different keys have separate caches.

```yaml
# Branch-based cache (separate cache per branch)
cache:
  key: $CI_COMMIT_REF_SLUG

# File-based cache (cache invalidated when lock file changes)
cache:
  key:
    files:
      - package-lock.json   # rebuild cache when dependencies change
    prefix: node

# Per-job cache key
frontend:
  cache:
    key: frontend-$CI_COMMIT_REF_SLUG

backend:
  cache:
    key: backend-$CI_COMMIT_REF_SLUG
```

### Cache policy

```yaml
test:
  cache:
    policy: pull          # download cache but never upload (read-only)

install:
  cache:
    policy: push          # upload cache but never download
    paths:
      - node_modules/

build:
  cache:
    policy: pull-push     # download then upload (default)
```

### Multiple caches per job

```yaml
build:
  cache:
    - key: node-modules-$CI_COMMIT_REF_SLUG
      paths: [node_modules/]
    - key: gradle-cache
      paths: [.gradle/]
```

### Cache vs artifacts

| | Cache | Artifacts |
|---|---|---|
| **Purpose** | Speed up jobs by persisting build dependencies | Pass build outputs between jobs in the same pipeline |
| **Persistence** | Across multiple pipeline runs | Within one pipeline (downloaded by later jobs) |
| **Guaranteed availability** | No — cache may expire or be evicted | Yes — artifacts are guaranteed for later stages |
| **Use for** | `node_modules`, `.m2`, `.gradle`, pip cache | Build outputs, test reports, compiled binaries |

---

## Needs and DAG Pipelines

By default, all jobs in a stage must complete before any job in the next stage starts. The `needs` keyword breaks this restriction — a job with `needs` starts as soon as its dependencies complete, regardless of stage.

This creates a **Directed Acyclic Graph (DAG)** pipeline where jobs run as soon as their specific dependencies are ready, not when their entire stage is done.

```yaml
stages:
  - build
  - test
  - deploy

build-frontend:
  stage: build
  script: npm run build:frontend
  artifacts:
    paths: [dist/frontend/]

build-backend:
  stage: build
  script: go build ./...
  artifacts:
    paths: [bin/]

# These two run in parallel as soon as their respective builds finish
# They do NOT wait for both builds to complete
test-frontend:
  stage: test
  needs: ["build-frontend"]      # starts when build-frontend finishes
  script: npm test

test-backend:
  stage: test
  needs: ["build-backend"]       # starts when build-backend finishes
  script: go test ./...

# Waits for both test jobs
deploy:
  stage: deploy
  needs: ["test-frontend", "test-backend"]
  script: ./deploy.sh
```

Without `needs`: total time = build-frontend + build-backend + max(test-frontend, test-backend) + deploy

With `needs`: total time = max(build-frontend + test-frontend, build-backend + test-backend) + deploy

For most pipelines this cuts total time by 20-40%.

**Start immediately with no dependencies:**

```yaml
lint:
  stage: test
  needs: []     # start immediately, do not wait for any build stage jobs
  script: npx eslint .
```

---

## Parallel Matrix Builds

Run the same job multiple times with different variable combinations:

```yaml
test:
  image: python:$PYTHON_VERSION
  parallel:
    matrix:
      - PYTHON_VERSION: ["3.10", "3.11", "3.12"]
        DJANGO_VERSION: ["4.1", "4.2"]
  script:
    - pip install django==$DJANGO_VERSION
    - pytest

# Generates 6 jobs:
# test: [3.10, 4.1]
# test: [3.10, 4.2]
# test: [3.11, 4.1]
# test: [3.11, 4.2]
# test: [3.12, 4.1]
# test: [3.12, 4.2]
```

Maximum: **200 permutations** per `parallel: matrix` block.

---

## Extends and Hidden Jobs

Hidden jobs (names starting with `.`) are never run directly. They serve as templates for other jobs to extend.

```yaml
# Template — not run directly
.base-test:
  image: node:20-alpine
  before_script:
    - npm ci
  cache:
    key: $CI_COMMIT_REF_SLUG
    paths: [node_modules/]

# Jobs extend the template
unit-tests:
  extends: .base-test
  script:
    - npm run test:unit

integration-tests:
  extends: .base-test
  script:
    - npm run test:integration
  services:
    - postgres:16
```

When a job extends a template, its properties are deep-merged. The job's own properties override the template's. Arrays are replaced (not merged), so if you define `before_script` in the job, it replaces the template's `before_script` entirely.

**Extending multiple templates:**

```yaml
deploy:
  extends:
    - .base-deploy
    - .notify-on-failure
  script: ./deploy.sh
```

---

## Include

Import external YAML files into your pipeline configuration. Enables sharing pipeline configuration across projects.

```yaml
include:
  # Include from the same repository
  - local: '/ci/build.yml'
  - local: '/ci/test.yml'

  # Include from another project (any branch, tag or commit)
  - project: 'my-group/shared-ci-templates'
    ref: main
    file: '/templates/docker-build.yml'

  # Include from an external URL
  - remote: 'https://raw.githubusercontent.com/your-repo/main/.ci-template.yml'

  # Use a GitLab-provided template
  - template: Auto-DevOps.gitlab-ci.yml
  - template: Jobs/SAST.gitlab-ci.yml

  # Use a CI/CD Catalog component
  - component: $CI_SERVER_FQDN/group/project/component@1.0.0
    inputs:
      stage: test
```

Maximum **150 includes** per pipeline. Includes are processed recursively (included files can themselves include other files), counting toward the 150 limit.

---

## Environments and Deployments

Environments track where your application is deployed and the history of who deployed what and when.

```yaml
deploy-staging:
  stage: deploy
  script:
    - ./deploy.sh staging
  environment:
    name: staging
    url: https://staging.example.com

deploy-production:
  stage: deploy
  script:
    - ./deploy.sh production
  environment:
    name: production
    url: https://example.com
  when: manual
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

View all environments: **Operate → Environments**. Each environment shows:
- Current deployment (which commit, which pipeline)
- Deployment history
- A **Visit** button linking to the environment URL
- A **Re-deploy** button to roll back to any previous deployment

### Auto-stop environments

```yaml
review-app:
  script: ./deploy-review.sh $CI_COMMIT_REF_SLUG
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.review.example.com
    on_stop: stop-review-app
    auto_stop_in: 2 days    # automatically stop after 2 days

stop-review-app:
  script: ./cleanup-review.sh $CI_COMMIT_REF_SLUG
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    action: stop
  when: manual
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: manual
```

---

## Review Apps

Review apps create a live, temporary deployment of your code for every merge request. Each MR gets its own unique URL where reviewers can see the changes running before approving.

```yaml
stages:
  - build
  - review
  - test
  - deploy

build:
  stage: build
  script: docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA .
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

deploy-review:
  stage: review
  script:
    - kubectl set image deployment/app app=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA -n review-$CI_MERGE_REQUEST_IID
  environment:
    name: review/$CI_MERGE_REQUEST_IID
    url: https://mr-$CI_MERGE_REQUEST_IID.review.example.com
    on_stop: stop-review
    auto_stop_in: 1 day
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

stop-review:
  stage: review
  script:
    - kubectl delete namespace review-$CI_MERGE_REQUEST_IID
  environment:
    name: review/$CI_MERGE_REQUEST_IID
    action: stop
  when: manual
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: manual
```

Each open MR shows a **View app** button in the MR widget that links directly to the review environment.

---

## Merge Request Pipelines

By default, pipelines triggered by push events run on the branch. Merge request pipelines run specifically when an MR is open and a push is made to its source branch.

Configure jobs to run on MR pipelines:

```yaml
test:
  script: npm test
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

# Or run on both MR pipelines and branch pushes:
test:
  script: npm test
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

MR-specific variables available in MR pipelines: `$CI_MERGE_REQUEST_IID`, `$CI_MERGE_REQUEST_SOURCE_BRANCH_NAME`, `$CI_MERGE_REQUEST_TARGET_BRANCH_NAME`, `$CI_MERGE_REQUEST_TITLE`.

---

## Scheduled Pipelines

Run pipelines on a cron schedule — nightly builds, weekly security scans, daily dependency updates.

**Configure**: Build → Pipeline schedules → New schedule

Fields:
- **Description**: what this schedule does
- **Interval pattern**: cron syntax (`0 2 * * *` = 2am UTC every day)
- **Target branch**: which branch to run on
- **Variables**: schedule-specific variables (e.g. `NIGHTLY_BUILD=true`)
- **Active**: enable/disable the schedule

In your `.gitlab-ci.yml`, check `$CI_PIPELINE_SOURCE == "schedule"` to run certain jobs only on scheduled pipelines:

```yaml
# Run this only on the nightly schedule, not on every push
dependency-update:
  script:
    - npm update
    - git diff --quiet || git commit -am "chore: update dependencies"
  rules:
    - if: $CI_PIPELINE_SOURCE == "schedule"
      variables:
        UPDATE_DEPS: "true"
    - when: never

# Combine with a custom variable from the schedule
full-security-scan:
  script: ./full-security-scan.sh
  rules:
    - if: $CI_PIPELINE_SOURCE == "schedule" && $SECURITY_SCAN == "true"
    - when: never
```

---

## Child and Parent Pipelines

Break large pipelines into smaller, more manageable pipelines.

### Child pipelines (same project)

```yaml
# Parent .gitlab-ci.yml
stages:
  - triggers

trigger-backend:
  stage: triggers
  trigger:
    include: backend/.gitlab-ci.yml
    strategy: depend    # parent waits for child to complete

trigger-frontend:
  stage: triggers
  trigger:
    include: frontend/.gitlab-ci.yml
    strategy: depend
```

### Multi-project pipelines (different projects)

```yaml
deploy-downstream:
  trigger:
    project: my-group/my-deployment-project
    branch: main
    strategy: depend
```

### Dynamic child pipelines

Generate the child pipeline YAML during the pipeline run (useful for monorepos):

```yaml
generate-pipeline:
  stage: .pre
  script:
    - python generate-ci.py > generated-pipeline.yml
  artifacts:
    paths:
      - generated-pipeline.yml

run-generated:
  trigger:
    include:
      - artifact: generated-pipeline.yml
        job: generate-pipeline
    strategy: depend
```

---

## Security Scanning Templates

GitLab provides maintained templates for common security scans. Include them in your pipeline with a single line.

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
  - template: Jobs/Container-Scanning.gitlab-ci.yml
  - template: Security/DAST.gitlab-ci.yml           # Ultimate only
  - template: Security/SAST-IaC.gitlab-ci.yml
```

Each template adds pre-configured jobs to your pipeline. Security findings appear in the MR security widget (Ultimate) or in the pipeline artifacts.

```yaml
# Complete security pipeline example
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml

stages:
  - test
  - security

# Your own jobs alongside the templates
unit-tests:
  stage: test
  script: npm test

# The template jobs run in the "test" stage by default
# Override variables to customise them:
variables:
  SAST_EXCLUDED_PATHS: "spec, test, tests, tmp"
  DS_EXCLUDED_PATHS: "spec, test"
```

---

## Runners

Runners are the agents that execute your CI/CD jobs. Without a runner, jobs queue indefinitely.

### Runner types by scope

**Instance runners** (formerly "shared runners"): available to all projects on the GitLab instance. On GitLab.com, GitLab provides Linux (on Google Cloud), Windows and macOS instance runners. Free tier: 400 compute minutes/month. Require credit card verification for the Free tier.

**Group runners**: registered to a group, available to all projects in that group and all subgroups. Best for teams that want a shared pool of runners across their projects.

**Project runners**: registered to a specific project only.

### Runner executors

The executor determines how the runner executes jobs:

| Executor | How it works | Best for |
|---|---|---|
| **Shell** | Runs directly on the runner host | Simple scripts, when you need the host's tools |
| **Docker** | Runs each job in a fresh Docker container | Most teams — clean, isolated, reproducible |
| **Docker Autoscaler** | Scales Docker hosts on demand in cloud | Large variable workloads |
| **Kubernetes** | Runs each job in a Kubernetes pod | Teams already on Kubernetes |
| **VirtualBox** | Runs jobs in VirtualBox VMs | Testing on different OS variants |
| **SSH** | Executes commands on a remote host via SSH | Deploying to fixed servers |

Docker executor is the standard choice for most teams.

---

## Installing and Registering a Runner

### 🍎 Mac

```bash
# Install via Homebrew
brew install gitlab-runner

# Start as a service
brew services start gitlab-runner

# Register (interactive)
gitlab-runner register
```

### 🪟 Windows

```powershell
# Download the binary
New-Item -ItemType Directory -Force -Path "C:\GitLab-Runner"
Invoke-WebRequest -Uri "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe" -OutFile "C:\GitLab-Runner\gitlab-runner.exe"

# Register (interactive)
C:\GitLab-Runner\gitlab-runner.exe register

# Install as a Windows service
C:\GitLab-Runner\gitlab-runner.exe install
C:\GitLab-Runner\gitlab-runner.exe start
```

### 🐧 Linux

```bash
# Add GitLab's package repository and install
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
sudo apt-get install gitlab-runner

# Or install a single binary
sudo curl -L --output /usr/local/bin/gitlab-runner \
  "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64"
sudo chmod +x /usr/local/bin/gitlab-runner
sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
sudo gitlab-runner start
```

### Registering the runner

Registration links a runner instance to your GitLab project or group. Since GitLab 15.0, registration uses **authentication tokens** (not the old registration tokens).

**Create the token first**:
- For a project runner: Settings → CI/CD → Runners → New project runner
- For a group runner: Group → CI/CD → Runners → New group runner

Configure the runner (tags, whether it runs untagged jobs, max job timeout) then copy the token.

**Register with the token:**

```bash
gitlab-runner register \
  --url "https://gitlab.com/" \
  --token "YOUR_RUNNER_AUTHENTICATION_TOKEN" \
  --executor "docker" \
  --docker-image "alpine:latest" \
  --description "My Docker runner" \
  --tag-list "docker,linux" \
  --run-untagged true
```

Or run interactively: `gitlab-runner register` and answer the prompts.

After registration, the runner appears in Settings → CI/CD → Runners and starts picking up jobs immediately.

---

## The Pipeline Editor

GitLab's built-in pipeline editor helps you write and validate `.gitlab-ci.yml` without pushing to trigger a pipeline.

**Open**: Build → Pipeline editor (or navigate directly to `/-/ci/editor`)

Features:
- **Syntax highlighting** for YAML and GitLab CI/CD keywords
- **Lint validation** — real-time syntax checking as you type. Shows errors and warnings.
- **Visualise tab** — renders a graph of your pipeline stages and jobs so you can see the execution order before running
- **Full configuration tab** — shows the merged pipeline configuration after all `include` directives are resolved
- **Simulate tab** — lets you preview how the pipeline would behave for a specific branch without actually running it

The linter in the pipeline editor catches common mistakes: missing `script`, invalid `when` values, broken `needs` references, YAML indentation errors. Always validate before pushing.

---

## Try It Yourself

**Exercise: Build a complete Node.js pipeline**

Create a new project on GitLab with a simple Node.js app, then add this `.gitlab-ci.yml`:

```yaml
workflow:
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_COMMIT_BRANCH && $CI_OPEN_MERGE_REQUESTS
      when: never
    - if: $CI_COMMIT_BRANCH

stages:
  - install
  - test
  - build

default:
  image: node:20-alpine
  cache:
    key: $CI_COMMIT_REF_SLUG
    paths:
      - node_modules/

install-deps:
  stage: install
  script:
    - npm ci
  artifacts:
    paths:
      - node_modules/
    expire_in: 1 hour

lint:
  stage: test
  needs: ["install-deps"]
  script:
    - npm run lint
  allow_failure: true

unit-tests:
  stage: test
  needs: ["install-deps"]
  script:
    - npm test -- --coverage
  artifacts:
    when: always
    paths:
      - coverage/
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

build:
  stage: build
  needs: ["unit-tests"]
  script:
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 week
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

Push the file, then watch the pipeline run in **Build → Pipelines**. Try clicking into individual jobs to see the logs.

---

## Common Mistakes

**Using `only`/`except` instead of `rules`.** `only` and `except` are deprecated. Use `rules` for all conditional job logic. `rules` is more powerful, more readable and is the only option getting new features.

**Not adding workflow rules and getting duplicate pipelines.** Without `workflow: rules`, every push to a branch with an open MR creates two pipelines — one for the branch push and one for the MR event. Add the standard workflow rules block to prevent this waste.

**Storing secrets in `.gitlab-ci.yml`.** Any value in `.gitlab-ci.yml` is visible to anyone with access to the repository. Never put API keys, tokens, passwords or certificates in the file. Use project or group CI/CD variables with the **Masked** option.

**Not caching `node_modules`.** Every job that runs `npm install` from scratch takes minutes. Caching `node_modules` reduces this to seconds. Pair the cache key with `package-lock.json` so it rebuilds when dependencies change.

**Using artifacts when you should use caching.** Artifacts are for passing build outputs between jobs in the same pipeline. Caching is for persisting data between pipeline runs. Using artifacts for `node_modules` means re-uploading and re-downloading megabytes on every pipeline. Use caching instead.

**Not using `needs` in complex pipelines.** The default stage-sequential behaviour means everything in stage 3 waits for everything in stage 2 to finish. If your test stage has 10 independent jobs, the deploy stage waits for all 10 even if the deploy only depends on one. Use `needs` to express actual dependencies.

**Setting `when: always` on deploy jobs without thinking.** A deploy job that always runs, even when tests fail, can deploy broken code to production. Use `when: on_success` (the default) or `when: manual` for deployments.

**Long pipelines without resource_group on deployments.** Two pipelines running simultaneously can both deploy to the same environment, racing each other and potentially leaving the environment in an unknown state. Always use `resource_group` for deployment jobs.

---

## Summary

- `.gitlab-ci.yml` at the repository root defines your entire pipeline. Push code → GitLab runs the pipeline automatically
- **Stages** define execution order. Jobs within a stage run in parallel. Next stage starts when all jobs in current stage pass
- **Jobs** are the unit of work. Every job needs a name and `script`. Configure with `image`, `services`, `rules`, `when`, `artifacts`, `cache`, `needs`, `tags`, `retry`, `timeout`
- **`rules`** replaces deprecated `only`/`except`. Evaluated top-to-bottom; first match wins. Use `if`, `changes`, `exists`
- **`workflow: rules`** controls whether a pipeline is created at all. Use it to eliminate duplicate pipelines
- **Variables**: defined globally, per-job, in project/group settings, or passed via triggers. Never hardcode secrets in `.gitlab-ci.yml`
- **Predefined variables**: `$CI_COMMIT_BRANCH`, `$CI_COMMIT_SHA`, `$CI_PIPELINE_SOURCE`, `$CI_REGISTRY_IMAGE`, `$CI_JOB_TOKEN` and dozens more
- **Artifacts**: files uploaded after a job; passed to later stages; parsed as reports (JUnit, coverage, SAST findings)
- **Cache**: persists between pipeline runs to speed up dependency installation; use file-based keys tied to lock files
- **`needs`**: start a job as soon as its specific dependencies complete; creates DAG pipelines that run faster than sequential stages
- **`parallel: matrix`**: run the same job with different variable combinations; max 200 permutations
- **`extends`**: inherit configuration from hidden job templates (`.template-name`)
- **`include`**: import external YAML from the same repo, another project, a URL or a GitLab template
- **Environments**: track deployments with who deployed what and when. `resource_group` prevents concurrent deployments
- **Review apps**: live per-MR deployments; each MR gets its own URL with `auto_stop_in` to clean up automatically
- **Merge request pipelines**: `$CI_PIPELINE_SOURCE == "merge_request_event"` — run jobs specifically when an MR is open
- **Runners**: install on any machine; Docker executor recommended; free and unlimited for self-hosted runners

---

## Sources and Further Reading

- [GitLab CI/CD documentation](https://docs.gitlab.com/ci/) - complete reference
- [CI/CD YAML syntax reference](https://docs.gitlab.com/ci/yaml/) - every keyword documented
- [Predefined CI/CD variables](https://docs.gitlab.com/ci/variables/predefined_variables/) - full variable list
- [GitLab Runner installation](https://docs.gitlab.com/runner/install/) - installation guides for all platforms
- [Pipeline architecture](https://docs.gitlab.com/ci/pipelines/pipeline_architectures/) - DAG, parent-child, multi-project
- [Security scanning templates](https://docs.gitlab.com/user/application_security/) - SAST, DAST, secret detection
- [Environments and deployments](https://docs.gitlab.com/ci/environments/) - tracking where code is deployed
- [Review apps](https://docs.gitlab.com/ci/review_apps/) - per-MR dynamic environments
- [Merge trains](https://docs.gitlab.com/ci/pipelines/merge_trains/) - queueing MRs safely

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

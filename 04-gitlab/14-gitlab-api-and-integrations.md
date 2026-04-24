# GitLab API and Integrations

**Difficulty:** 🔴 Advanced | **Time:** 45 minutes

GitLab exposes a comprehensive REST API and a GraphQL API that give you programmatic access to nearly everything on the platform: repositories, merge requests, issues, pipelines, users, groups, registries, security findings and more. Beyond the APIs, GitLab integrates natively with dozens of external tools and includes built-in registries for containers and packages. This file covers everything you need to automate GitLab workflows, connect it to external tools and interact with the API from scripts, CI/CD pipelines and third-party applications.

---

## Table of Contents

- [REST API overview](#rest-api-overview)
- [Making your first API request](#making-your-first-api-request)
- [Authentication methods](#authentication-methods)
- [Pagination](#pagination)
- [Key REST API endpoints](#key-rest-api-endpoints)
- [GraphQL API](#graphql-api)
- [Webhooks](#webhooks)
- [Built-in integrations](#built-in-integrations)
- [Container Registry](#container-registry)
- [Package Registry](#package-registry)
- [GitLab API in CI/CD pipelines](#gitlab-api-in-cicd-pipelines)
- [Rate limits](#rate-limits)
- [Using the API with Python](#using-the-api-with-python)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## REST API Overview

The GitLab REST API follows standard REST conventions. Every resource has a predictable URL structure, uses HTTP verbs correctly and returns JSON. It is the most complete API surface GitLab offers and the right choice for most integrations.

**Base URL:**

- GitLab.com: `https://gitlab.com/api/v4/`
- Self-managed: `https://YOUR_DOMAIN/api/v4/`

**API versioning**: The current version is v4. GitLab does not introduce breaking changes within a major API version. Deprecated endpoints are announced with a deprecation notice and removed only in the next major version. You can rely on v4 endpoints remaining stable.

**Response format**: all responses are JSON. Successful responses return the relevant data object or array. Error responses return a JSON object with an `error` or `message` key.

**HTTP status codes**:

| Code                      | Meaning                                       |
| ------------------------- | --------------------------------------------- |
| 200 OK                    | Request succeeded, data returned              |
| 201 Created               | Resource created successfully                 |
| 204 No Content            | Request succeeded, no data to return (DELETE) |
| 400 Bad Request           | Invalid parameters                            |
| 401 Unauthorized          | Authentication required or token invalid      |
| 403 Forbidden             | Authenticated but insufficient permissions    |
| 404 Not Found             | Resource does not exist or you lack access    |
| 409 Conflict              | Resource already exists or conflict in state  |
| 422 Unprocessable Entity  | Validation errors                             |
| 429 Too Many Requests     | Rate limit exceeded                           |
| 500 Internal Server Error | GitLab error                                  |

---

## Making Your First API Request

### curl examples

```bash
# Get your own user information
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/user"

# List projects you have access to
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects?membership=true&per_page=10"

# Get a specific project by its path
# Note: the slash in the path must be URL-encoded as %2F
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/gitlab-org%2Fgitlab"

# Or use the numeric project ID (more reliable than path encoding)
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/278964"

# Create an issue
curl --request POST \
  --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"title": "New issue from API", "description": "Created via curl"}' \
  "https://gitlab.com/api/v4/projects/YOUR_PROJECT_ID/issues"
```

### Project ID vs path

Every GitLab project has both a numeric ID and a path-based namespace. The numeric ID is stable (never changes even if the project is renamed or transferred). The path changes if the project or group is renamed.

For reliable API integrations, always use the numeric project ID:

```bash
# Find a project's numeric ID
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/namespace%2Fproject" | jq .id
```

Once you have the ID, use it in all subsequent requests: `/projects/12345/issues`, `/projects/12345/merge_requests`, etc.

---

## Authentication Methods

### Personal access tokens (PATs)

The most common authentication method. Pass the token in the `PRIVATE-TOKEN` header.

```bash
curl --header "PRIVATE-TOKEN: glpat-xxxxxxxxxxxxxxxxxxxx" \
  "https://gitlab.com/api/v4/user"
```

Create PATs at: Avatar -> Edit profile -> Access tokens.

### Group and project access tokens

Work identically to PATs but are scoped to a specific group or project and not tied to a personal account. Pass in the same `PRIVATE-TOKEN` header.

### OAuth 2.0

For applications that act on behalf of users. Users authorise your application via a browser flow and your app receives an access token.

```bash
# After completing OAuth flow, use the access token
curl --header "Authorization: Bearer YOUR_OAUTH_TOKEN" \
  "https://gitlab.com/api/v4/user"
```

**Flow types:**

- **Authorization code flow**: standard flow for web applications with a backend
- **PKCE flow**: for mobile apps and SPAs that cannot securely store a client secret
- **Device authorization flow**: for CLI tools and devices without a browser

Register an OAuth application at: Avatar -> Edit profile -> Applications (for personal apps) or Group/Admin settings (for group/instance-wide apps).

### CI/CD job token

Every CI/CD pipeline job has a `$CI_JOB_TOKEN` environment variable automatically injected. This token authenticates as the user who triggered the pipeline and is scoped to the project running the pipeline (plus any other projects that have explicitly allowed access).

```bash
# In a CI/CD job script
curl --header "JOB-TOKEN: $CI_JOB_TOKEN" \
  "${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/releases"
```

The job token is the preferred authentication method for API calls made within CI/CD pipelines - no secrets to manage, automatic scope, expires when the job ends.

### HTTPS Git operations

For Git operations over HTTPS (not API calls), use a PAT or deploy token as the password:

```bash
# Clone using PAT (username can be anything)
git clone https://oauth2:YOUR_PAT@gitlab.com/namespace/project.git

# Or configure Git credential helper
git config --global credential.helper store
# On first operation, enter:
# Username: oauth2
# Password: YOUR_PAT
```

---

## Pagination

List endpoints return paginated results. The default page size is 20 items and the maximum is 100.

### Offset-based pagination (default)

```bash
# Page 1 (default)
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects?per_page=100"

# Page 2
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects?per_page=100&page=2"
```

Response headers include pagination metadata:

```
X-Page: 2
X-Per-Page: 100
X-Total: 847
X-Total-Pages: 9
X-Next-Page: 3
X-Prev-Page: 1
```

### Keyset pagination (recommended for large datasets)

Offset pagination becomes slow and inconsistent on large datasets (if items are added or removed between pages). Keyset pagination uses cursor-based navigation instead:

```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects?pagination=keyset&per_page=100&order_by=id&sort=asc"
```

The response includes a `Link` header with `next` and `first` URLs containing the cursor. Follow the `next` link to get the next page.

### Fetching all pages in a script

```bash
#!/bin/bash
# Fetch all projects using pagination
TOKEN="YOUR_TOKEN"
URL="https://gitlab.com/api/v4/projects?membership=true&per_page=100"

while [ -n "$URL" ]; do
  # Fetch page and capture response headers
  RESPONSE=$(curl -sI -H "PRIVATE-TOKEN: $TOKEN" "$URL" -D /tmp/headers 2>/dev/null)

  # Fetch the actual data
  curl -s -H "PRIVATE-TOKEN: $TOKEN" "$URL" >> all_projects.json

  # Get next page URL from Link header
  URL=$(grep "^link:" /tmp/headers | grep -o '<[^>]*>; rel="next"' | sed 's/<\(.*\)>; rel="next"/\1/')
done
```

---

## Key REST API Endpoints

### Projects

```
GET    /projects                          list accessible projects
GET    /projects/:id                      get a single project
POST   /projects                          create a project
PUT    /projects/:id                      update project settings
DELETE /projects/:id                      delete a project
POST   /projects/:id/fork                 fork a project
GET    /projects/:id/members              list project members
POST   /projects/:id/members              add a member
GET    /projects/:id/branches             list branches
DELETE /projects/:id/repository/branches/:branch   delete a branch
GET    /projects/:id/repository/files/:filepath    get file contents
POST   /projects/:id/repository/commits   create a commit with multiple file changes
GET    /projects/:id/repository/tags      list tags
POST   /projects/:id/repository/tags      create a tag
```

### Merge requests

```
GET    /projects/:id/merge_requests              list merge requests
GET    /projects/:id/merge_requests/:mr_iid      get a single MR
POST   /projects/:id/merge_requests              create an MR
PUT    /projects/:id/merge_requests/:mr_iid      update an MR
PUT    /projects/:id/merge_requests/:mr_iid/merge   merge an MR
GET    /projects/:id/merge_requests/:mr_iid/notes   list MR comments
POST   /projects/:id/merge_requests/:mr_iid/notes   add a comment
POST   /projects/:id/merge_requests/:mr_iid/approve  approve an MR
POST   /projects/:id/merge_requests/:mr_iid/unapprove  remove approval
```

### Issues

```
GET    /projects/:id/issues              list issues
GET    /projects/:id/issues/:issue_iid   get a single issue
POST   /projects/:id/issues              create an issue
PUT    /projects/:id/issues/:issue_iid   update an issue
DELETE /projects/:id/issues/:issue_iid   delete an issue
POST   /projects/:id/issues/:issue_iid/notes    add a comment
PUT    /projects/:id/issues/:issue_iid/notes/:note_id  update a comment
POST   /projects/:id/issues/:issue_iid/move     move to another project
```

### CI/CD and pipelines

```
GET    /projects/:id/pipelines                       list pipelines
POST   /projects/:id/pipeline                        create (trigger) a pipeline
GET    /projects/:id/pipelines/:pipeline_id          get pipeline details
POST   /projects/:id/pipelines/:pipeline_id/cancel   cancel a pipeline
POST   /projects/:id/pipelines/:pipeline_id/retry    retry a pipeline
GET    /projects/:id/pipelines/:pipeline_id/jobs     list jobs in a pipeline
POST   /projects/:id/jobs/:job_id/retry              retry a job
POST   /projects/:id/jobs/:job_id/cancel             cancel a job
GET    /projects/:id/jobs/:job_id/trace              get job log
GET    /projects/:id/jobs/:job_id/artifacts          download artifacts
GET    /projects/:id/variables                       list CI/CD variables
POST   /projects/:id/variables                       create a variable
PUT    /projects/:id/variables/:key                  update a variable
DELETE /projects/:id/variables/:key                  delete a variable
GET    /projects/:id/pipeline_schedules              list schedules
POST   /projects/:id/pipeline_schedules              create a schedule
POST   /projects/:id/pipeline_schedules/:id/play     trigger a schedule now
```

### Users and groups

```
GET    /user                              get current user
GET    /users                             list users (admin only)
GET    /users/:id                         get a user
GET    /groups                            list accessible groups
GET    /groups/:id                        get a group
GET    /groups/:id/members                list group members
POST   /groups/:id/members                add a member
DELETE /groups/:id/members/:user_id       remove a member
GET    /groups/:id/projects               list projects in a group
GET    /groups/:id/variables              list group CI/CD variables
POST   /groups/:id/variables              create a group variable
```

### Releases

```
GET    /projects/:id/releases              list releases
GET    /projects/:id/releases/:tag_name   get a specific release
POST   /projects/:id/releases             create a release
PUT    /projects/:id/releases/:tag_name   update a release
DELETE /projects/:id/releases/:tag_name   delete a release
POST   /projects/:id/releases/:tag_name/assets/links   add a download link
```

---

## GraphQL API

The GraphQL API allows you to request exactly the fields you need in a single query, avoiding over-fetching and multiple round trips.

**Endpoint**: `https://gitlab.com/api/graphql`

**Interactive explorer**: navigate to `/-/graphql-explorer` on any GitLab instance. This gives you a full browser-based IDE with documentation, autocomplete and query execution.

### Making GraphQL requests

```bash
curl --request POST \
  --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"query": "{ currentUser { username name } }"}' \
  "https://gitlab.com/api/graphql"
```

### Example queries

**Get a project with recent merge requests:**

```graphql
query {
  project(fullPath: "my-group/my-project") {
    name
    description
    visibility
    mergeRequests(state: opened, first: 5) {
      nodes {
        iid
        title
        state
        author {
          username
        }
        createdAt
        webUrl
      }
    }
  }
}
```

**Get pipeline status for recent commits:**

```graphql
query {
  project(fullPath: "my-group/my-project") {
    pipelines(first: 5) {
      nodes {
        id
        status
        createdAt
        duration
        commit {
          shortId
          title
          author {
            username
          }
        }
      }
    }
  }
}
```

**Get all open vulnerabilities in a project (Ultimate):**

```graphql
query {
  project(fullPath: "my-group/my-project") {
    vulnerabilities(state: DETECTED, severity: [CRITICAL, HIGH]) {
      nodes {
        title
        severity
        state
        detectedAt
        vulnerabilityPath
        scanner {
          name
        }
      }
    }
  }
}
```

### Mutations (write operations)

```graphql
mutation {
  createIssue(input: { projectPath: "my-group/my-project", title: "Bug: login fails on Safari 17", description: "Steps to reproduce...", labelNames: ["bug", "frontend"] }) {
    issue {
      iid
      title
      webUrl
    }
    errors
  }
}
```

### REST vs GraphQL - when to use each

| Use REST when                                       | Use GraphQL when                                             |
| --------------------------------------------------- | ------------------------------------------------------------ |
| Performing simple CRUD operations                   | Fetching data from multiple related resources in one request |
| Working with binary data (file contents, artifacts) | You need precise control over which fields are returned      |
| Using tools that have built-in REST support         | Building a dashboard or UI that needs flexible data fetching |
| Triggering actions (pipelines, merges)              | Reducing API calls by combining multiple queries             |

Most integrations use REST. GraphQL is most valuable when building applications that need to display data from multiple GitLab resources simultaneously.

---

## Webhooks

Webhooks are HTTP POST requests that GitLab sends to your URL when specific events occur. Use them to trigger external automation, update third-party systems or build real-time integrations.

### Webhook scope

**Project webhooks**: Settings -> Webhooks. Fires for events in this specific project.

**Group webhooks** (Premium on GitLab.com): Group -> Settings -> Webhooks. Fires for events in any project in the group.

**System hooks** (admin only, self-managed): Admin Area -> System hooks. Fires for events across the entire instance.

### Supported events

| Event                      | Trigger                                               |
| -------------------------- | ----------------------------------------------------- |
| Push events                | Commits pushed to any branch                          |
| Tag push events            | A tag is created or deleted                           |
| Comments                   | A comment is added to an issue, MR, commit or snippet |
| Confidential comments      | A comment on a confidential issue                     |
| Issues events              | An issue is created, updated, closed or reopened      |
| Confidential issues events | Changes to confidential issues                        |
| Merge request events       | An MR is created, updated, merged or closed           |
| Job events                 | A CI/CD job changes status                            |
| Pipeline events            | A pipeline changes status                             |
| Wiki page events           | A wiki page is created or updated                     |
| Deployment events          | A deployment starts, succeeds or fails                |
| Releases events            | A release is created or updated                       |
| Member events              | A member is added, updated or removed                 |
| Subgroup events            | A subgroup is created or deleted                      |
| Project events             | A project is created, renamed, transferred or deleted |

### Configuring a webhook

Settings -> Webhooks -> Add new webhook

Fields:

- **URL**: the endpoint GitLab will POST to
- **Secret token**: an optional value GitLab includes in the `X-Gitlab-Token` header, used to verify the request comes from GitLab
- **Trigger events**: tick the events you want to receive
- **SSL verification**: verify the endpoint's SSL certificate (disable only for self-signed certs on internal systems)

### Webhook payload

Each webhook POST includes:

- Headers: `X-Gitlab-Event` (event type), `X-Gitlab-Token` (your secret), `Content-Type: application/json`
- Body: JSON object describing the event

**Example push event payload:**

```json
{
  "object_kind": "push",
  "before": "95790bf891e76fee5726fe34168ef50aa61c5ca",
  "after": "da1560886d4f094c3e6c9ef40349f7d38b5d27d",
  "ref": "refs/heads/main",
  "user_username": "alice",
  "project": {
    "id": 15,
    "name": "my-project",
    "web_url": "https://gitlab.com/my-group/my-project"
  },
  "commits": [
    {
      "id": "da1560886d4f094c3e6c9ef40349f7d38b5d27d",
      "message": "feat: add dark mode",
      "author": {
        "name": "Alice",
        "email": "alice@example.com"
      }
    }
  ]
}
```

### Verifying webhook authenticity

Always verify that incoming webhook requests are genuinely from GitLab:

```python
import hmac
import hashlib
from flask import Flask, request, abort

app = Flask(__name__)
WEBHOOK_SECRET = "your-secret-token"

@app.route("/gitlab-webhook", methods=["POST"])
def webhook():
    # Verify the secret token
    token = request.headers.get("X-Gitlab-Token")
    if token != WEBHOOK_SECRET:
        abort(401)

    event = request.headers.get("X-Gitlab-Event")
    payload = request.json

    if event == "Push Hook":
        branch = payload["ref"].replace("refs/heads/", "")
        user = payload["user_username"]
        commit_count = len(payload["commits"])
        print(f"{user} pushed {commit_count} commits to {branch}")

    elif event == "Merge Request Hook":
        action = payload["object_attributes"]["action"]
        title = payload["object_attributes"]["title"]
        print(f"MR '{title}' was {action}")

    return "", 200
```

### Auto-disabling webhooks

> [!WARNING]
> GitLab automatically **disables a webhook after 4 consecutive delivery failures** (4xx or 5xx responses, or connection timeouts). Once disabled, the webhook stops receiving events until it is manually re-enabled.

Check webhook health: Settings -> Webhooks -> click the webhook name -> **Recent Deliveries** tab. This shows the last 100 delivery attempts with request/response details for debugging.

Re-enable a disabled webhook: Settings -> Webhooks -> click **Enable** next to the disabled webhook.

Add monitoring to your webhook endpoint so you know immediately if it starts returning errors.

---

## Built-in Integrations

GitLab includes native integrations for many external tools, configurable without writing any code.

**Access all integrations**: Settings -> **Integrations**

### Jira

Two-way integration linking GitLab activity to Jira issues.

**How it works**: when you mention a Jira issue key (e.g. `PROJ-123`) in a GitLab commit message, branch name, MR title or MR description, GitLab creates a link in the Jira issue showing the GitLab activity. GitLab can also transition Jira issues (e.g. move to "In Review" when an MR is opened).

**Setup**: Settings -> Integrations -> Jira -> enable -> enter your Jira URL, email and API token.

For Jira Cloud, the **GitLab for Jira Cloud app** in the Atlassian Marketplace provides a development panel inside Jira issues showing linked branches, commits and MRs automatically.

**Transition Jira issues from GitLab**: in a commit message or MR description, use the transition name with the Jira key:

```
feat: implement payment retry logic

Transitions PROJ-123 to In Progress
```

### Slack

**GitLab for Slack app** (recommended over legacy webhook):

Setup: Settings -> Integrations -> GitLab for Slack app -> **Install GitLab for Slack**. Authorise in Slack. Configure which channels receive which event notifications.

**Slash commands** (after installation):

```
/gitlab help                          - show available commands
/gitlab my-group/my-project deploy production  - trigger a deployment
/gitlab my-group/my-project open issue  - open an issue from Slack
```

**Notification configuration**: after installing, go to the integration settings to configure which events send to which channels. You can have pipeline results go to one channel, MR updates to another and deployment events to a third.

Group-level Slack app configuration (GA in GitLab 17.8): configure once at the group level and it applies to all projects in the group.

### Microsoft Teams

Send GitLab event notifications to a Teams channel via incoming webhooks.

Setup: in Teams, create an incoming webhook connector for the channel -> copy the webhook URL -> Settings -> Integrations -> Microsoft Teams notifications -> enter the URL and select events.

Events you can send: push, tag push, issue, confidential issue, merge request, note, confidential note, job, pipeline, wiki page.

### Jenkins

Trigger Jenkins builds from GitLab events.

Setup: Settings -> Integrations -> Jenkins -> enable -> enter Jenkins URL, project name and credentials.

GitLab sends a POST to Jenkins when configured events occur (push, MR events, tag push). Jenkins can report build status back to GitLab via the GitLab API.

### Datadog

Send CI/CD pipeline and job metrics to Datadog for monitoring and alerting.

Setup: Settings -> Integrations -> Datadog -> enable -> enter your Datadog API key and site. GitLab sends pipeline events as Datadog CI Visibility traces.

### Other notable integrations

- **Bugzilla, Redmine, YouTrack**: issue tracker link integrations (reference external issues in commits and MRs)
- **Asana**: create Asana tasks from GitLab issues
- **PagerDuty**: create incidents from GitLab alerts
- **Prometheus**: push pipeline metrics to a Prometheus pushgateway
- **Telegram, Discord**: send notifications via bots
- **Mattermost**: self-hosted Slack alternative with native GitLab integration
- **Harbor**: connect GitLab container scanning with Harbor registry

---

## Container Registry

GitLab includes a built-in Docker/OCI container registry. It is enabled by default on GitLab.com and configurable on self-managed instances.

**Registry URL format**: `registry.gitlab.com/NAMESPACE/PROJECT`

For a subgroup project: `registry.gitlab.com/group/subgroup/project`

### Authenticate to the registry

```bash
# Using a personal access token (needs read_registry or write_registry scope)
docker login registry.gitlab.com -u YOUR_USERNAME -p YOUR_TOKEN

# Using a deploy token
docker login registry.gitlab.com -u DEPLOY_TOKEN_USERNAME -p DEPLOY_TOKEN_VALUE

# In a CI/CD pipeline (automatic - predefined variables)
docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
```

### Build and push in CI/CD

```yaml
build-and-push:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  variables:
    DOCKER_TLS_CERTDIR: "/certs"
    IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
    # Also tag as latest for the default branch
    - |
      if [ "$CI_COMMIT_BRANCH" == "$CI_DEFAULT_BRANCH" ]; then
        docker tag $IMAGE_TAG $CI_REGISTRY_IMAGE:latest
        docker push $CI_REGISTRY_IMAGE:latest
      fi
```

### Managing images

**List images in the UI**: Deploy -> Container registry. Browse by image name and tag, see image sizes and when they were pushed.

**Pull a specific image:**

```bash
docker pull registry.gitlab.com/namespace/project:tag
```

**Delete images**: Navigate to the image in the registry UI and click the delete button. Or use the API:

```bash
# Delete a specific tag
curl --request DELETE \
  --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/registry/repositories/REPOSITORY_ID/tags/TAG_NAME"
```

### Cleanup policies

Configure automatic deletion of old tags: Settings -> Packages and registries -> Container registry -> **Clean up image tags**.

Configure:

- **Run cleanup**: on a schedule (every day, week, month)
- **Keep the most recent**: N tags matching a name pattern
- **Remove tags older than**: N days
- **Remove tags matching**: a regex pattern (e.g. remove all `feature-*` tags older than 7 days)
- **Keep tags matching**: always keep tags matching this pattern (e.g. `main`, `v*`)

### Container registry on self-managed

```ruby
# /etc/gitlab/gitlab.rb

# Registry on the same domain (different port)
registry_external_url 'https://gitlab.example.com:5050'

# Registry on its own subdomain
registry_external_url 'https://registry.example.com'
gitlab_rails['registry_enabled'] = true

# Use object storage for images
registry['storage'] = {
  's3' => {
    'accesskey' => 'YOUR_KEY',
    'secretkey' => 'YOUR_SECRET',
    'bucket' => 'gitlab-registry',
    'region' => 'eu-west-1',
    'secure' => true
  }
}
```

---

## Package Registry

The GitLab Package Registry hosts language-specific packages. It is **free on all plans** (moved from Premium in GitLab 13.3).

**Navigate**: Deploy -> Package registry

**Supported formats**: npm, PyPI, Maven, NuGet, Cargo (Rust), Composer (PHP), Conan (C/C++), Go modules, Terraform modules, Generic packages, Helm charts.

### npm packages

**Publish:**

Configure your project's `package.json`:

```json
{
  "name": "@my-group/my-package",
  "version": "1.0.0",
  "publishConfig": {
    "@my-group:registry": "https://gitlab.com/api/v4/packages/npm/"
  }
}
```

Create `.npmrc` for authentication:

```
//gitlab.com/api/v4/packages/npm/:_authToken=${GITLAB_TOKEN}
@my-group:registry=https://gitlab.com/api/v4/packages/npm/
```

Publish:

```bash
npm publish
```

In CI/CD:

```yaml
publish-npm:
  script:
    - echo "@my-group:registry=https://${CI_SERVER_HOST}/api/v4/packages/npm/" >> .npmrc
    - echo "//${CI_SERVER_HOST}/api/v4/packages/npm/:_authToken=${CI_JOB_TOKEN}" >> .npmrc
    - npm publish
```

**Install:**

```bash
npm install @my-group/my-package
```

### PyPI packages

**Publish with twine:**

```bash
pip install twine

twine upload \
  --repository-url https://gitlab.com/api/v4/projects/PROJECT_ID/packages/pypi \
  -u gitlab-ci-token \
  -p $GITLAB_TOKEN \
  dist/*
```

In CI/CD:

```yaml
publish-pypi:
  script:
    - pip install build twine
    - python -m build
    - |
      TWINE_PASSWORD=${CI_JOB_TOKEN} TWINE_USERNAME=gitlab-ci-token \
      python -m twine upload \
        --repository-url ${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/pypi \
        dist/*
```

**Install:**

```bash
pip install --index-url \
  https://oauth2:YOUR_TOKEN@gitlab.com/api/v4/projects/PROJECT_ID/packages/pypi/simple/ \
  my-package
```

### Maven packages

**Publish via `pom.xml`:**

```xml
<distributionManagement>
  <repository>
    <id>gitlab-maven</id>
    <url>${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/maven</url>
  </repository>
  <snapshotRepository>
    <id>gitlab-maven</id>
    <url>${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/maven</url>
  </snapshotRepository>
</distributionManagement>
```

Add authentication to `~/.m2/settings.xml`:

```xml
<server>
  <id>gitlab-maven</id>
  <configuration>
    <httpHeaders>
      <property>
        <name>Job-Token</name>
        <value>${CI_JOB_TOKEN}</value>
      </property>
    </httpHeaders>
  </configuration>
</server>
```

### Generic packages

For files that do not fit a specific format (compiled binaries, documentation archives, release assets):

```bash
# Upload a generic package
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  --upload-file my-app-linux-amd64 \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/packages/generic/my-app/1.0.0/my-app-linux-amd64"

# Download
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/packages/generic/my-app/1.0.0/my-app-linux-amd64" \
  --output my-app-linux-amd64
```

In CI/CD, use the job token:

```yaml
upload-binary:
  script:
    - |
      curl --header "JOB-TOKEN: ${CI_JOB_TOKEN}" \
        --upload-file dist/my-app-linux-amd64 \
        "${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/packages/generic/my-app/${CI_COMMIT_TAG}/my-app-linux-amd64"
```

---

## GitLab API in CI/CD Pipelines

Using the API within CI/CD pipelines is common for automation. Best practices:

### Use CI_JOB_TOKEN where possible

The job token is automatically available, does not require managing additional secrets and has appropriate scope for most in-project operations:

```yaml
create-release:
  stage: release
  rules:
    - if: $CI_COMMIT_TAG
  script:
    - |
      curl --request POST \
        --header "PRIVATE-TOKEN: $CI_JOB_TOKEN" \
        --header "Content-Type: application/json" \
        --data "{
          \"name\": \"Release $CI_COMMIT_TAG\",
          \"tag_name\": \"$CI_COMMIT_TAG\",
          \"description\": \"Automated release from CI/CD\"
        }" \
        "${CI_API_V4_URL}/projects/${CI_PROJECT_ID}/releases"
```

### Store sensitive tokens as masked CI/CD variables

For operations the job token cannot do (e.g. cross-project API calls), store a PAT or group access token as a protected, masked CI/CD variable:

```yaml
# In project or group CI/CD settings:
# Key: GITLAB_API_TOKEN
# Value: glpat-xxxxx
# Masked: yes, Protected: yes

update-other-project:
  script:
    - |
      curl --request POST \
        --header "PRIVATE-TOKEN: $GITLAB_API_TOKEN" \
        --header "Content-Type: application/json" \
        --data '{"title": "Automated issue from pipeline"}' \
        "${CI_API_V4_URL}/projects/OTHER_PROJECT_ID/issues"
```

### Using the release-cli image for releases

```yaml
create-release:
  image: registry.gitlab.com/gitlab-org/release-cli:latest
  stage: release
  rules:
    - if: $CI_COMMIT_TAG
  release:
    tag_name: $CI_COMMIT_TAG
    name: "Release $CI_COMMIT_TAG"
    description: "$CI_COMMIT_TAG"
    assets:
      links:
        - name: "Linux binary"
          url: "https://gitlab.com/my-group/my-project/-/releases/$CI_COMMIT_TAG/downloads/my-app-linux-amd64"
  script:
    - echo "Creating release $CI_COMMIT_TAG"
```

---

## Rate Limits

GitLab.com applies rate limits to protect the service. Self-managed instances have configurable limits.

### Current GitLab.com limits (as of 2026)

| Request type                 | Limit                          |
| ---------------------------- | ------------------------------ |
| Unauthenticated API requests | 10 requests/second per IP      |
| Authenticated API requests   | 2,000 requests/minute per user |
| Raw file endpoints           | 300 requests/minute per IP     |
| Repository downloads         | 5 downloads/minute per user    |
| Pipeline creation via API    | 25/minute per project          |
| Webhook deliveries           | 500/minute per project         |

When you exceed a rate limit, GitLab returns `429 Too Many Requests` with a `Retry-After` header indicating how many seconds to wait.

### Handling rate limits in code

```python
import requests
import time

def api_request_with_retry(url, headers, max_retries=3):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers)

        if response.status_code == 429:
            retry_after = int(response.headers.get("Retry-After", 60))
            print(f"Rate limited. Waiting {retry_after} seconds before retry {attempt + 1}/{max_retries}")
            time.sleep(retry_after)
            continue

        return response

    raise Exception(f"Max retries exceeded for {url}")

# Usage
headers = {"PRIVATE-TOKEN": "YOUR_TOKEN"}
response = api_request_with_retry(
    "https://gitlab.com/api/v4/projects",
    headers
)
```

---

## Using the API with Python

For complex integrations, use the `python-gitlab` library - the official Python client for the GitLab API.

```bash
pip install python-gitlab
```

```python
import gitlab

# Connect to GitLab.com
gl = gitlab.Gitlab("https://gitlab.com", private_token="YOUR_TOKEN")

# Connect to self-managed
gl = gitlab.Gitlab("https://gitlab.example.com", private_token="YOUR_TOKEN")

# Authenticate (verify credentials)
gl.auth()

# Get current user
user = gl.auth()
print(f"Authenticated as {gl.users.get(gl.user.id).username}")

# Work with projects
project = gl.projects.get("my-group/my-project")
print(f"Project: {project.name} (ID: {project.id})")

# List open merge requests
mrs = project.mergerequests.list(state="opened")
for mr in mrs:
    print(f"  MR !{mr.iid}: {mr.title} by {mr.author['username']}")

# Create an issue
issue = project.issues.create({
    "title": "Bug: payment fails on weekends",
    "description": "Steps to reproduce...",
    "labels": ["bug", "priority::high"]
})
print(f"Created issue #{issue.iid}: {issue.web_url}")

# Get pipeline status
pipelines = project.pipelines.list(ref="main", status="running")
for pipeline in pipelines:
    print(f"Pipeline #{pipeline.id}: {pipeline.status}")

# Trigger a pipeline
pipeline = project.trigger_pipeline("main", "TRIGGER_TOKEN", {
    "DEPLOY_ENV": "staging",
    "DEBUG": "true"
})

# List all groups recursively
def list_all_groups(group_id, indent=0):
    group = gl.groups.get(group_id)
    print(" " * indent + group.full_path)
    for subgroup in group.subgroups.list(all=True):
        list_all_groups(subgroup.id, indent + 2)

# Paginate through all projects in a group
group = gl.groups.get("my-group")
all_projects = []
for project in group.projects.list(all=True, include_subgroups=True):
    all_projects.append(project.name)
print(f"Found {len(all_projects)} projects")
```

---

## Try It Yourself

**Exercise 1: Make your first API call**

1. Create a personal access token with `read_api` scope
2. Run:

```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/user" | python3 -m json.tool
```

You should see your account details formatted as JSON.

**Exercise 2: List and create resources**

```bash
# List your projects
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects?owned=true&per_page=5" \
  | python3 -m json.tool | grep '"name"'

# Get your project's ID
PROJECT_ID=$(curl -s --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/YOUR_NAMESPACE%2FYOUR_PROJECT" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['id'])")

echo "Project ID: $PROJECT_ID"

# Create an issue via API
curl --request POST \
  --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  --header "Content-Type: application/json" \
  --data "{\"title\": \"Test issue from API\", \"description\": \"Created via curl\"}" \
  "https://gitlab.com/api/v4/projects/$PROJECT_ID/issues"
```

**Exercise 3: Set up a webhook**

1. Go to [webhook.site](https://webhook.site) and copy your unique URL
2. In your GitLab project: Settings -> Webhooks -> Add new webhook
3. Enter your webhook.site URL, tick "Push events", save
4. Make a commit and push it
5. Go to webhook.site and see the webhook payload that arrived

**Exercise 4: Explore the GraphQL explorer**

1. Navigate to `https://gitlab.com/-/graphql-explorer`
2. Paste and run this query (change the fullPath to a project you have access to):

```graphql
query {
  project(fullPath: "gitlab-org/gitlab-foss") {
    name
    starCount
    forksCount
    openIssuesCount
    mergeRequests(state: opened, first: 3) {
      nodes {
        title
        author {
          username
        }
      }
    }
  }
}
```

---

## Common Mistakes

**Hardcoding tokens in code or `.gitlab-ci.yml`.** Even in a private repository, tokens in source code are visible to everyone with repository access and are recorded permanently in Git history. Store tokens as masked CI/CD variables. If you accidentally commit a token, revoke it immediately and remove it from history with `git-filter-repo`.

**Forgetting to URL-encode project paths.** A path like `my-group/my-project` must be encoded as `my-group%2Fmy-project` in API URLs. Using the numeric project ID instead avoids encoding entirely and is more reliable.

**Confusing project ID with project IID.** The `id` field is the global database ID (used in API paths). The `iid` is the project-scoped issue or MR number (what you see in the UI as `#42` or `!42`). Using one where the other is expected causes 404 errors or returns the wrong resource.

**Not handling pagination.** List endpoints return only one page by default (20 items). Scripts that only read the first page miss everything beyond the 20th item. Always check for additional pages using the `X-Next-Page` header or implement cursor-based pagination.

**Not checking webhook delivery status.** Teams set up webhooks and assume they work. GitLab silently disables webhooks after 4 consecutive failures. Add monitoring to your webhook endpoint and periodically check the "Recent Deliveries" tab in the webhook settings to catch delivery failures early.

**Using project-scoped tokens for group-level operations.** A project access token can only access its own project. If your automation needs to access multiple projects in a group, use a group access token instead.

**Not setting webhook secret tokens.** Without a secret token, anyone who knows your webhook endpoint URL can send fake GitLab events. Always set a secret token and verify the `X-Gitlab-Token` header in your webhook handler before processing any payload.

---

## Summary

- **REST API**: base URL `/api/v4/`. JSON responses. HTTP verbs map to CRUD operations. Authenticate with `PRIVATE-TOKEN` header.
- **Numeric IDs vs paths**: use numeric project IDs for reliability. Paths must be URL-encoded (replace `/` with `%2F`). IID is the project-scoped number; ID is the global database ID.
- **Authentication**: PAT (most common), group/project access tokens (for automation), OAuth 2.0 (for user-facing apps), CI_JOB_TOKEN (for pipelines), deploy tokens (for Git and registry only).
- **Pagination**: default 20 items per page, max 100. Check `X-Next-Page` header. Use keyset pagination for large datasets.
- **GraphQL API**: endpoint `/api/graphql`. Interactive explorer at `/-/graphql-explorer`. Fetch exactly the fields you need. Use for complex multi-resource queries.
- **Webhooks**: HTTP POST to your URL on events. Set a secret token and verify it. 4 consecutive failures auto-disable the webhook. Check "Recent Deliveries" regularly.
- **Integrations**: Jira (two-way linking), Slack (GitLab app with slash commands), Microsoft Teams, Jenkins, Datadog and dozens more. Configured at Settings -> Integrations.
- **Container Registry**: free, built-in, at `registry.gitlab.com/NAMESPACE/PROJECT`. Use `$CI_REGISTRY_USER` and `$CI_REGISTRY_PASSWORD` in pipelines. Configure cleanup policies to remove old tags automatically.
- **Package Registry**: free (all plans since 13.3). Supports npm, PyPI, Maven, NuGet, Cargo, Composer, Helm, Go modules, Terraform modules and generic files. Use `$CI_JOB_TOKEN` for authentication within pipelines.
- **Rate limits**: 2,000 requests/minute for authenticated API. 429 response includes `Retry-After` header. Implement exponential backoff.
- **python-gitlab**: the official Python library. `pip install python-gitlab`. Handles pagination, authentication and all API resources.

---

## Sources and Further Reading

- [GitLab REST API documentation](https://docs.gitlab.com/api/rest/) - complete endpoint reference
- [GitLab GraphQL API documentation](https://docs.gitlab.com/api/graphql/) - schema, queries and mutations
- [GitLab GraphQL Explorer](https://gitlab.com/-/graphql-explorer) - interactive query builder with autocomplete
- [Webhooks documentation](https://docs.gitlab.com/user/project/integrations/webhooks/) - event payloads and configuration
- [Container Registry documentation](https://docs.gitlab.com/user/packages/container_registry/) - setup and management
- [Package Registry documentation](https://docs.gitlab.com/user/packages/package_registry/) - all supported package formats
- [python-gitlab library](https://python-gitlab.readthedocs.io/) - the official Python client
- [Rate limits on GitLab.com](https://docs.gitlab.com/user/gitlab_com/index.html#gitlabcom-specific-rate-limits) - current rate limit values
- [GitLab for Jira Cloud app](https://marketplace.atlassian.com/apps/1221011) - Atlassian Marketplace
- [CI/CD job token](https://docs.gitlab.com/ci/jobs/ci_job_token/) - scope and cross-project access

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

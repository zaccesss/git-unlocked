# Bitbucket API and Integrations

**Difficulty:** 🔴 Advanced | **Time:** 45 minutes

Every action you take in the Bitbucket web interface can also be performed via the REST API. Creating repositories, managing pull requests, posting build status, triggering pipelines, managing user access - all of it is available programmatically. The Bitbucket REST API v2 is the foundation for custom tooling, automation, third-party integrations and CI/CD systems that interact with Bitbucket programmatically.

This file covers the Bitbucket REST API v2 completely: authentication, common request patterns, webhooks for event-driven integrations, the Atlassian Marketplace for pre-built add-ons, and practical automation examples for real development workflows.

---

## Table of Contents

1. [The Bitbucket REST API overview](#the-bitbucket-rest-api-overview)
2. [Authentication](#authentication)
3. [API structure and conventions](#api-structure-and-conventions)
4. [Working with repositories](#working-with-repositories)
5. [Working with pull requests](#working-with-pull-requests)
6. [Commit statuses (build status)](#commit-statuses-build-status)
7. [Working with branches and tags](#working-with-branches-and-tags)
8. [Pipelines API](#pipelines-api)
9. [User and workspace API](#user-and-workspace-api)
10. [Webhooks](#webhooks)
11. [Rate limiting and pagination](#rate-limiting-and-pagination)
12. [The Atlassian Marketplace](#the-atlassian-marketplace)
13. [Common integrations](#common-integrations)
14. [Building a custom integration](#building-a-custom-integration)
15. [Try It Yourself](#try-it-yourself)
16. [Common Mistakes](#common-mistakes)
17. [Summary](#summary)
18. [Sources](#sources)

---

## The Bitbucket REST API overview

The Bitbucket REST API v2 is the current API version. API v1 was deprecated in 2019 and no longer exists. All integrations must use v2.

**Base URL:**
```
https://api.bitbucket.org/2.0/
```

The API uses standard HTTP methods:
- `GET` - retrieve resources
- `POST` - create resources
- `PUT` - replace a resource
- `PATCH` - update part of a resource
- `DELETE` - remove a resource

Responses are JSON. Successful responses return 2xx status codes. Errors return 4xx (client error) or 5xx (server error) with a JSON error body.

### API documentation

Atlassian publishes interactive API documentation at [developer.atlassian.com/cloud/bitbucket/rest/intro](https://developer.atlassian.com/cloud/bitbucket/rest/intro/). The documentation includes:
- Full endpoint reference with request/response schemas
- Interactive "Try it" panels for testing endpoints
- Code examples in curl, Python and JavaScript
- Authentication guides

---

## Authentication

The Bitbucket API supports three authentication methods. App passwords (the old method) are deprecated and stop working in June 2026.

### API tokens (recommended)

API tokens are the primary authentication method for all new integrations. They work as HTTP Basic Authentication where the token is used as the password.

**Creating an API token:**
1. Go to [bitbucket.org](https://bitbucket.org) > **Personal settings** > **API tokens**
2. Click **Create token**
3. Give it a name, set an expiry, select permissions
4. Copy the token immediately (shown only once)

**Using an API token with curl:**

```bash
# Using -u flag (Basic Auth)
curl -u "your-email@example.com:your-api-token" \
  "https://api.bitbucket.org/2.0/repositories/workspace/repo"

# Using Authorization header
curl -H "Authorization: Basic $(echo -n 'your-email@example.com:your-api-token' | base64)" \
  "https://api.bitbucket.org/2.0/repositories/workspace/repo"
```

**Using an API token in Python:**

```python
import requests
from requests.auth import HTTPBasicAuth

auth = HTTPBasicAuth('your-email@example.com', 'your-api-token')

response = requests.get(
    'https://api.bitbucket.org/2.0/repositories/workspace/repo',
    auth=auth
)
print(response.json())
```

**Using an API token in Node.js:**

```javascript
const axios = require('axios');

const client = axios.create({
  baseURL: 'https://api.bitbucket.org/2.0',
  auth: {
    username: 'your-email@example.com',
    password: 'your-api-token'
  }
});

const response = await client.get('/repositories/workspace/repo');
console.log(response.data);
```

### OAuth 2.0

For applications that act on behalf of users (rather than a single automation account), use OAuth 2.0. OAuth lets users authorise your application to access their Bitbucket account without sharing their credentials.

**OAuth 2.0 flow:**

1. Register your application in Bitbucket workspace settings > OAuth consumers
2. Redirect the user to Bitbucket's authorisation URL with your client ID and requested scopes
3. User approves; Bitbucket redirects back to your app with an authorisation code
4. Exchange the code for an access token and refresh token
5. Use the access token in API requests

```python
# Step 2: Build the authorisation URL
import urllib.parse

params = {
    'client_id': 'your-oauth-client-id',
    'response_type': 'code',
    'scope': 'repository account pullrequest'
}
auth_url = 'https://bitbucket.org/site/oauth2/authorize?' + urllib.parse.urlencode(params)
# Redirect the user to auth_url

# Step 4: Exchange code for token
import requests

token_response = requests.post(
    'https://bitbucket.org/site/oauth2/access_token',
    data={
        'grant_type': 'authorization_code',
        'code': 'code-from-redirect',
    },
    auth=('your-client-id', 'your-client-secret')
)
tokens = token_response.json()
access_token = tokens['access_token']
refresh_token = tokens['refresh_token']

# Step 5: Use the access token
response = requests.get(
    'https://api.bitbucket.org/2.0/user',
    headers={'Authorization': f'Bearer {access_token}'}
)
```

### Workspace access tokens (repository-scoped)

For repository-specific automation, workspace access tokens can be scoped to a specific repository. These are useful for CI/CD systems that should only access one repository. They are created in the repository settings rather than personal settings.

---

## API structure and conventions

### Resource paths

Bitbucket API paths follow a consistent pattern:

```
/2.0/{resource-type}/{identifier}

# Examples:
/2.0/repositories/{workspace}/{repo_slug}
/2.0/repositories/{workspace}/{repo_slug}/pullrequests
/2.0/repositories/{workspace}/{repo_slug}/pullrequests/{pr_id}
/2.0/workspaces/{workspace}/members
/2.0/users/{account_id}
```

`{workspace}` is the workspace slug (e.g. `acmecorp`).
`{repo_slug}` is the repository slug (e.g. `my-api-service`).

### Request and response format

All request bodies must be JSON with the `Content-Type: application/json` header:

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{"scm": "git", "is_private": true}' \
  "https://api.bitbucket.org/2.0/repositories/workspace/new-repo"
```

### Error responses

Error responses include a `type` and `error` object:

```json
{
  "type": "error",
  "error": {
    "message": "Repository acmecorp/nonexistent-repo not found.",
    "detail": "Please ensure you have the correct permissions."
  }
}
```

Common HTTP status codes:
- `200 OK` - successful GET, PUT, PATCH
- `201 Created` - successful POST (resource created)
- `204 No Content` - successful DELETE
- `400 Bad Request` - invalid request body or parameters
- `401 Unauthorized` - missing or invalid credentials
- `403 Forbidden` - valid credentials but insufficient permissions
- `404 Not Found` - resource does not exist or you cannot see it
- `429 Too Many Requests` - rate limit exceeded

---

## Working with repositories

### List all repositories in a workspace

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp"
```

With Python:
```python
import requests

def list_repos(workspace, auth):
    url = f'https://api.bitbucket.org/2.0/repositories/{workspace}'
    repos = []
    while url:
        response = requests.get(url, auth=auth)
        data = response.json()
        repos.extend(data['values'])
        url = data.get('next')  # handle pagination
    return repos
```

### Get a specific repository

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-api-service"
```

### Create a repository

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "scm": "git",
    "is_private": true,
    "name": "new-service",
    "description": "New microservice for the platform",
    "project": {
      "key": "BACKEND"
    }
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/new-service"
```

### Delete a repository

```bash
curl -X DELETE \
  -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/old-service"
```

### Get repository branches

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/refs/branches"
```

### Get commits

```bash
# All commits on main
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/commits/main"

# Commits since a specific date
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/commits/main?q=date>2026-01-01"
```

---

## Working with pull requests

### List pull requests

```bash
# All open pull requests
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests?state=OPEN"

# Merged pull requests
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests?state=MERGED"
```

States: `OPEN`, `MERGED`, `DECLINED`, `SUPERSEDED`

### Create a pull request

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "title": "Add OAuth2 authentication",
    "description": "Implements OAuth2 login flow. Closes PROJ-123.",
    "source": {
      "branch": {
        "name": "feature/PROJ-123-oauth2"
      }
    },
    "destination": {
      "branch": {
        "name": "main"
      }
    },
    "reviewers": [
      {"uuid": "{reviewer-account-uuid}"}
    ],
    "close_source_branch": true
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests"
```

### Get a specific pull request

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests/42"
```

### Merge a pull request

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "type": "pullrequest",
    "message": "Merging feature: OAuth2 authentication",
    "merge_strategy": "squash",
    "close_source_branch": true
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests/42/merge"
```

Merge strategies: `merge_commit`, `squash`, `fast_forward`

### Approve a pull request

```bash
curl -X POST \
  -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests/42/approve"
```

### Add a comment to a pull request

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "content": {
      "raw": "Automated review: All security checks passed."
    }
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pullrequests/42/comments"
```

---

## Commit statuses (build status)

Posting build status to commits is one of the most common API use cases. It allows external CI systems to show their results on pull requests and enforce status checks via branch permissions.

### Posting a build status

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "state": "SUCCESSFUL",
    "key": "jenkins-build",
    "name": "Jenkins Build #142",
    "url": "https://jenkins.acmecorp.com/job/my-repo/142",
    "description": "All 342 tests passed in 4m 23s"
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/commit/abc1234567890/statuses/build"
```

States: `INPROGRESS`, `SUCCESSFUL`, `FAILED`, `STOPPED`

**Posting INPROGRESS when a build starts:**

```bash
# Post in-progress when job starts
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "state": "INPROGRESS",
    "key": "jenkins-build",
    "name": "Jenkins Build #142",
    "url": "https://jenkins.acmecorp.com/job/my-repo/142",
    "description": "Build in progress..."
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/commit/$COMMIT_SHA/statuses/build"

# ... run the build ...

# Post SUCCESSFUL or FAILED when it completes
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "state": "SUCCESSFUL",
    "key": "jenkins-build",
    "name": "Jenkins Build #142",
    "url": "https://jenkins.acmecorp.com/job/my-repo/142",
    "description": "All tests passed"
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/commit/$COMMIT_SHA/statuses/build"
```

The `key` field uniquely identifies the CI system. Using the same key updates the existing status for that key. Multiple CI systems can post different keys to the same commit - all statuses appear on the PR.

### Retrieving build statuses

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/commit/abc1234/statuses"
```

---

## Working with branches and tags

### Create a branch

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "name": "feature/PROJ-456-new-feature",
    "target": {
      "hash": "main"
    }
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/refs/branches"
```

### Delete a branch

```bash
curl -X DELETE \
  -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/refs/branches/feature%2FPROJ-456-new-feature"
```

Note: Branch names containing `/` must be URL-encoded (`feature/branch` → `feature%2Fbranch`).

### Create a tag

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "name": "v2.3.1",
    "target": {
      "hash": "abc1234567890"
    },
    "message": "Release 2.3.1 - security patch"
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/refs/tags"
```

---

## Pipelines API

The Pipelines API allows you to trigger, monitor and manage Bitbucket Pipeline runs programmatically.

### Trigger a pipeline manually

```bash
# Trigger the default pipeline on a branch
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "target": {
      "ref_type": "branch",
      "type": "pipeline_ref_target",
      "ref_name": "main"
    }
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pipelines/"
```

### Trigger a custom pipeline

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "target": {
      "ref_type": "branch",
      "type": "pipeline_ref_target",
      "ref_name": "main",
      "selector": {
        "type": "custom",
        "pattern": "deploy-to-production"
      }
    },
    "variables": [
      {
        "key": "TARGET_ENV",
        "value": "production"
      }
    ]
  }' \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pipelines/"
```

### List recent pipeline runs

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pipelines/?sort=-created_on&pagelen=10"
```

### Get a specific pipeline run

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pipelines/{pipeline_uuid}"
```

### Stop a running pipeline

```bash
curl -X POST \
  -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp/my-repo/pipelines/{pipeline_uuid}/stopPipeline"
```

---

## User and workspace API

### Get the authenticated user

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/user"
```

### Get workspace members

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/workspaces/acmecorp/members"
```

### Get workspace projects

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/workspaces/acmecorp/projects"
```

### Create a project

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "email:token" \
  -d '{
    "name": "New Backend Services",
    "key": "NBS",
    "description": "New backend microservices platform",
    "is_private": true
  }' \
  "https://api.bitbucket.org/2.0/workspaces/acmecorp/projects"
```

---

## Webhooks

Webhooks allow Bitbucket to notify external services when events happen in your repository. Instead of polling the API to check for new pull requests or commits, your service receives an HTTP POST from Bitbucket the moment the event occurs.

### Creating a webhook

1. Go to **Repository settings** > **Webhooks**
2. Click **Add webhook**
3. Enter the webhook URL (the endpoint on your server that will receive the events)
4. Choose the events to subscribe to
5. Optionally add a secret token for payload verification
6. Click **Save**

### Webhook events

| Event | Triggers when |
|---|---|
| `repo:push` | Code is pushed to any branch |
| `repo:fork` | Repository is forked |
| `repo:updated` | Repository settings are changed |
| `repo:commit_status_created` | A build status is posted to a commit |
| `repo:commit_status_updated` | An existing build status is updated |
| `pullrequest:created` | A new pull request is opened |
| `pullrequest:updated` | A PR is updated (new commit, description change) |
| `pullrequest:approved` | A reviewer approves the PR |
| `pullrequest:unapproved` | An approval is revoked |
| `pullrequest:fulfilled` | A PR is merged |
| `pullrequest:rejected` | A PR is declined |
| `pullrequest:comment_created` | A comment is added to a PR |
| `pullrequest:comment_updated` | A PR comment is edited |
| `pullrequest:comment_deleted` | A PR comment is deleted |
| `issue:created` | A new issue is created (if issues enabled) |
| `issue:updated` | An issue is updated |
| `issue:comment_created` | A comment is added to an issue |

### Webhook payload

When an event fires, Bitbucket sends a JSON POST to your URL. For a `pullrequest:created` event:

```json
{
  "actor": {
    "display_name": "Isaac Adjei",
    "uuid": "{account-uuid}"
  },
  "pullrequest": {
    "id": 42,
    "title": "Add OAuth2 authentication",
    "state": "OPEN",
    "source": {
      "branch": {"name": "feature/PROJ-123-oauth2"},
      "repository": {"full_name": "acmecorp/my-repo"}
    },
    "destination": {
      "branch": {"name": "main"}
    },
    "links": {
      "html": {"href": "https://bitbucket.org/acmecorp/my-repo/pull-requests/42"}
    }
  },
  "repository": {
    "full_name": "acmecorp/my-repo",
    "name": "my-repo"
  }
}
```

### Verifying webhook signatures

To ensure webhooks are genuinely from Bitbucket and not from a malicious actor spoofing your endpoint, use a secret token.

**Set a secret when creating the webhook in Bitbucket.** Bitbucket will include an `X-Hub-Signature` header in every request containing an HMAC SHA-256 hash of the payload using your secret.

**Verify the signature in your server:**

```python
import hmac
import hashlib
from flask import Flask, request, abort

app = Flask(__name__)
WEBHOOK_SECRET = 'your-webhook-secret'

@app.route('/webhook', methods=['POST'])
def handle_webhook():
    signature = request.headers.get('X-Hub-Signature', '')
    expected = 'sha256=' + hmac.new(
        WEBHOOK_SECRET.encode(),
        request.data,
        hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(signature, expected):
        abort(403, 'Invalid signature')

    payload = request.json
    event = request.headers.get('X-Event-Key')

    if event == 'pullrequest:fulfilled':
        handle_pr_merged(payload)

    return '', 200

def handle_pr_merged(payload):
    pr = payload['pullrequest']
    print(f"PR merged: {pr['title']} into {pr['destination']['branch']['name']}")
```

### Webhook reliability

Your webhook endpoint must respond within **10 seconds** with a 2xx status. If it does not:
- Bitbucket retries with exponential backoff
- After repeated failures, Bitbucket disables the webhook and notifies repository admins

For time-consuming operations triggered by webhooks (like running a test suite), respond immediately and process asynchronously:

```python
from threading import Thread

@app.route('/webhook', methods=['POST'])
def handle_webhook():
    payload = request.json
    # Respond immediately
    thread = Thread(target=process_event, args=(payload,))
    thread.start()
    return '', 200

def process_event(payload):
    # Do the slow work here, in the background
    run_tests(payload)
```

### Workspace-level webhooks

Webhooks can also be created at the workspace level to receive events from all repositories in the workspace simultaneously. This is useful for audit systems, compliance tools and dashboards that need to track activity across the entire workspace.

Workspace webhooks are configured in **Workspace settings** > **Webhooks**.

---

## Rate limiting and pagination

### Rate limits

Bitbucket Cloud API rate limits vary by authentication method and plan:

| Auth method | Limit |
|---|---|
| Unauthenticated | 60 requests/hour |
| API token | 1,000 requests/hour (per user) |
| OAuth 2.0 | 1,000 requests/hour (per user) |

When rate limited, the API returns `429 Too Many Requests` with a `Retry-After` header indicating how many seconds to wait.

```python
import time
import requests

def api_request_with_retry(url, auth, max_retries=3):
    for attempt in range(max_retries):
        response = requests.get(url, auth=auth)
        if response.status_code == 429:
            retry_after = int(response.headers.get('Retry-After', 60))
            print(f"Rate limited. Waiting {retry_after} seconds...")
            time.sleep(retry_after)
            continue
        return response
    raise Exception("Max retries exceeded")
```

### Pagination

API endpoints that return lists use cursor-based pagination. The response includes `next` and `previous` URLs when there are more results:

```json
{
  "pagelen": 10,
  "values": [...],
  "page": 1,
  "size": 47,
  "next": "https://api.bitbucket.org/2.0/repositories/workspace?page=2"
}
```

To iterate all pages:

```python
def get_all_pages(url, auth):
    results = []
    while url:
        response = requests.get(url, auth=auth)
        data = response.json()
        results.extend(data.get('values', []))
        url = data.get('next')
    return results

# Usage:
all_repos = get_all_pages(
    'https://api.bitbucket.org/2.0/repositories/acmecorp',
    auth=('email@example.com', 'api-token')
)
```

You can control page size with the `pagelen` parameter (max: 100):

```bash
curl -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/acmecorp?pagelen=100"
```

---

## The Atlassian Marketplace

The [Atlassian Marketplace](https://marketplace.atlassian.com) hosts thousands of apps that extend Bitbucket and other Atlassian products. Unlike the GitHub Marketplace which focuses on individual tools, the Atlassian Marketplace benefits from cross-product integration - an app installed for Jira may also extend Bitbucket.

### Finding Bitbucket apps

1. Go to [marketplace.atlassian.com](https://marketplace.atlassian.com)
2. Under **Product**, filter by **Bitbucket**
3. Or filter by **Bitbucket Cloud** vs **Bitbucket Data Center** specifically

### Installing Marketplace apps

For Bitbucket Cloud:
1. Go to **Workspace settings** > **Installed apps**
2. Click **Find new apps**
3. Browse or search the Marketplace
4. Click **Get it free** or **Try it free** on any app
5. Authorise the app's requested permissions

For Bitbucket Data Center:
1. Log in as an administrator
2. Go to the **Administration** panel
3. Click **Find new apps** under Atlassian Marketplace
4. Search and install directly

### Notable Bitbucket Marketplace apps

**Code quality:**

[SonarQube for Bitbucket](https://marketplace.atlassian.com/apps/1211305) - shows SonarQube analysis results directly in pull requests. Inline code quality annotations appear on the diff next to the affected lines.

[Checkmarx for Bitbucket](https://marketplace.atlassian.com/apps/1216861) - static application security testing integrated into the PR review workflow.

**Deployment and DevOps:**

[Deployments for Bitbucket](https://marketplace.atlassian.com/apps/1215097) - enhanced deployment tracking across cloud providers.

[Helm charts for Kubernetes](https://marketplace.atlassian.com/apps/1221360) - Kubernetes deployment automation.

**Notifications and collaboration:**

[Slack for Bitbucket](https://marketplace.atlassian.com/apps/1210819) - receive Slack notifications for PRs, builds and deployments.

[Microsoft Teams for Bitbucket](https://marketplace.atlassian.com/apps/1224975) - Teams notifications and PR interactions.

**Reporting and analytics:**

[Bitbucket Analytics](https://marketplace.atlassian.com/apps/1215390) - PR metrics, cycle time reports, team activity dashboards.

[Git Insights](https://marketplace.atlassian.com/apps/1215765) - developer productivity metrics, commit analysis.

**Access and security:**

[Permissions Auditor](https://marketplace.atlassian.com/apps/1212431) - audit and report on who has access to what across all repositories.

---

## Common integrations

### Jenkins

Jenkins is the most commonly used external CI system with Bitbucket Data Center and also used with Bitbucket Cloud.

**Setup:**
1. Install the [Bitbucket Branch Source Plugin](https://plugins.jenkins.io/cloudbees-bitbucket-branch-source/) in Jenkins
2. Configure a Bitbucket Server or Cloud connection in Jenkins system settings
3. Create a Multibranch Pipeline job pointing to your Bitbucket repository
4. Jenkins discovers branches automatically and builds them

**Build status posting:** The plugin automatically posts build status back to Bitbucket commits.

### Slack notifications via Pipelines

Without a Marketplace app, you can send Slack notifications directly from Bitbucket Pipelines using the Slack webhook pipe:

```yaml
- step:
    name: Notify Slack on failure
    trigger: automatic
    after-script:
      - pipe: atlassian/slack-notify:2.1.0
        variables:
          WEBHOOK_URL: $SLACK_WEBHOOK_URL
          MESSAGE: "Build $BITBUCKET_BUILD_NUMBER failed on $BITBUCKET_BRANCH"
```

### AWS CodeDeploy

Deploy to AWS directly from Bitbucket Pipelines:

```yaml
- step:
    name: Deploy to AWS
    deployment: production
    script:
      - pipe: atlassian/aws-code-deploy:0.6.0
        variables:
          AWS_ACCESS_KEY_ID: $AWS_ACCESS_KEY_ID
          AWS_SECRET_ACCESS_KEY: $AWS_SECRET_ACCESS_KEY
          AWS_DEFAULT_REGION: 'eu-west-1'
          APPLICATION_NAME: 'my-app'
          DEPLOYMENT_GROUP: 'production'
          S3_BUCKET: 'my-deployment-bucket'
          COMMAND: 'upload-and-deploy'
          ZIP_FILE: 'deployment.zip'
```

### Snyk security scanning

Integrate Snyk vulnerability scanning into pull requests:

```yaml
- step:
    name: Snyk security scan
    script:
      - pipe: snyk/snyk-scan:1.0.0
        variables:
          SNYK_TOKEN: $SNYK_TOKEN
          LANGUAGE: 'node'
          SEVERITY_THRESHOLD: 'high'
          FAIL_ON_ISSUES: 'true'
          MONITOR: 'false'
```

### SonarCloud code quality

Run SonarCloud analysis and get results in pull requests:

```yaml
- step:
    name: SonarCloud analysis
    script:
      - pipe: sonarsource/sonarcloud-scan:2.0.0
        variables:
          SONAR_TOKEN: $SONAR_TOKEN
          EXTRA_ARGS: '-Dsonar.projectKey=acmecorp_my-repo'
```

---

## Building a custom integration

### Use case: auto-labelling pull requests

A common custom integration: automatically add labels to pull requests based on the files changed. For example, adding a "database" label when migration files are present.

**Architecture:**
1. Bitbucket sends a webhook on `pullrequest:created`
2. Your server receives the webhook
3. Your server calls the API to get the PR's diff
4. Your server checks for migration file patterns
5. Your server calls the API to add a label (comment or task) to the PR

```python
from flask import Flask, request
import requests

app = Flask(__name__)

WORKSPACE = 'acmecorp'
REPO = 'my-repo'
AUTH = ('ci-bot@acmecorp.com', 'api-token')

@app.route('/webhook', methods=['POST'])
def webhook():
    payload = request.json
    event = request.headers.get('X-Event-Key')

    if event == 'pullrequest:created':
        pr_id = payload['pullrequest']['id']
        label_pr_if_needed(pr_id)

    return '', 200

def label_pr_if_needed(pr_id):
    # Get the PR diff
    diff_url = f'https://api.bitbucket.org/2.0/repositories/{WORKSPACE}/{REPO}/pullrequests/{pr_id}/diffstat'
    response = requests.get(diff_url, auth=AUTH)
    files = response.json().get('values', [])

    changed_paths = [f['new']['path'] for f in files if f.get('new')]

    # Check for database migrations
    migration_files = [p for p in changed_paths if 'migrations' in p]

    if migration_files:
        # Add a comment noting migrations are present
        comment_url = f'https://api.bitbucket.org/2.0/repositories/{WORKSPACE}/{REPO}/pullrequests/{pr_id}/comments'
        requests.post(comment_url, auth=AUTH, json={
            'content': {
                'raw': f'**Database migrations detected** - {len(migration_files)} migration file(s) changed. Please ensure database migrations are reviewed.'
            }
        })

if __name__ == '__main__':
    app.run(port=5000)
```

### Use case: automatically close stale PRs

A script to close pull requests that have had no activity for 30 days:

```python
import requests
from datetime import datetime, timezone, timedelta

WORKSPACE = 'acmecorp'
AUTH = ('ci-bot@acmecorp.com', 'api-token')
STALE_DAYS = 30

def close_stale_prs():
    repos_url = f'https://api.bitbucket.org/2.0/repositories/{WORKSPACE}'
    repos = get_all_pages(repos_url)

    for repo in repos:
        repo_slug = repo['slug']
        prs_url = f'https://api.bitbucket.org/2.0/repositories/{WORKSPACE}/{repo_slug}/pullrequests?state=OPEN'
        prs = get_all_pages(prs_url)

        for pr in prs:
            updated = datetime.fromisoformat(pr['updated_on'].replace('Z', '+00:00'))
            age = datetime.now(timezone.utc) - updated

            if age > timedelta(days=STALE_DAYS):
                decline_url = f'https://api.bitbucket.org/2.0/repositories/{WORKSPACE}/{repo_slug}/pullrequests/{pr["id"]}/decline'
                requests.post(decline_url, auth=AUTH, json={
                    'message': f'Automatically closed after {STALE_DAYS} days of inactivity.'
                })
                print(f"Closed stale PR #{pr['id']} in {repo_slug}: {pr['title']}")

def get_all_pages(url):
    results = []
    while url:
        response = requests.get(url, auth=AUTH)
        data = response.json()
        results.extend(data.get('values', []))
        url = data.get('next')
    return results

close_stale_prs()
```

---

## Try It Yourself

**Exercise 1 - Make your first API call**

```bash
# Get information about yourself
curl -u "your-email@example.com:your-api-token" \
  "https://api.bitbucket.org/2.0/user"
```

**Exercise 2 - List your repositories**

```bash
# Replace 'your-workspace' with your workspace slug
curl -u "your-email@example.com:your-api-token" \
  "https://api.bitbucket.org/2.0/repositories/your-workspace"
```

**Exercise 3 - Create a repository via the API**

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u "your-email@example.com:your-api-token" \
  -d '{
    "scm": "git",
    "is_private": true,
    "name": "api-test-repo",
    "description": "Created via the API"
  }' \
  "https://api.bitbucket.org/2.0/repositories/your-workspace/api-test-repo"
```

**Exercise 4 - Set up a webhook**

1. Start a free webhook testing service like [webhook.site](https://webhook.site) to get a temporary URL
2. Go to your test repository > **Repository settings** > **Webhooks**
3. Add a webhook with the webhook.site URL
4. Subscribe to `pullrequest:created` and `repo:push` events
5. Push a commit and create a PR
6. Watch the events arrive at webhook.site in real time

**Exercise 5 - Post a build status**

```bash
# First, get the latest commit SHA
COMMIT=$(curl -s \
  -u "email:token" \
  "https://api.bitbucket.org/2.0/repositories/your-workspace/api-test-repo/commits/main" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['values'][0]['hash'])")

# Post a fake successful build status
curl -X POST \
  -H "Content-Type: application/json" \
  -u "your-email@example.com:your-api-token" \
  -d '{
    "state": "SUCCESSFUL",
    "key": "test-ci",
    "name": "Test Build",
    "url": "https://example.com",
    "description": "All tests passed"
  }' \
  "https://api.bitbucket.org/2.0/repositories/your-workspace/api-test-repo/commit/$COMMIT/statuses/build"
```

View the commit in Bitbucket to see the build status badge appear.

---

## Common Mistakes

**Using API v1 endpoints**

API v1 was removed in 2019. Any URL starting with `api.bitbucket.org/1.0/` will return 404. All requests must use v2: `api.bitbucket.org/2.0/`.

**Not handling pagination**

API endpoints that return lists return a maximum of 10-100 results per page. If your workspace has 200 repositories and you request the repository list, you will only get the first page. Always follow the `next` link until it is absent to get all results.

**Hardcoding credentials**

Never put API tokens in source code, shell scripts or CI/CD pipeline YAML files committed to the repository. Use environment variables, CI/CD secret variables or a secrets manager (AWS Secrets Manager, HashiCorp Vault).

**Not handling rate limits**

Scripts that make many API calls without rate limit handling will eventually receive 429 responses. Add retry logic with exponential backoff for any script that makes more than a few API calls.

**Forgetting URL encoding for branch names**

Branch names with `/` (like `feature/PROJ-123`) must be URL-encoded in API paths: `feature%2FPROJ-123`. Forgetting this causes 404 responses that are confusing to debug.

**Not responding to webhooks within 10 seconds**

Bitbucket expects a 2xx response from webhook endpoints within 10 seconds. If your handler takes longer, Bitbucket retries and eventually disables the webhook. Always respond immediately and process asynchronously for any non-trivial work.

**Ignoring webhook signature verification**

Without verifying the `X-Hub-Signature` header, any actor on the internet who knows your webhook URL can send fake events to your endpoint. Always verify the HMAC signature.

---

## Summary

The Bitbucket REST API v2 provides programmatic access to every Bitbucket feature. The base URL is `https://api.bitbucket.org/2.0/`. Authenticate with API tokens (Basic Auth with email and token as password), or OAuth 2.0 for user-facing applications. App passwords are retired from June 2026.

The API covers repositories (CRUD operations, branches, tags, commits), pull requests (create, merge, approve, comment), commit statuses (post build results from external CI systems), Pipelines (trigger and monitor runs), and workspace management (members, groups, projects).

Webhooks provide real-time event notifications from Bitbucket to your systems. Subscribe to events (push, PR creation, PR merge, build status changes) and Bitbucket POSTs the event payload to your URL within seconds. Always verify webhook signatures and respond within 10 seconds.

Handle pagination for all list endpoints by following the `next` link. Respect rate limits (1,000 requests/hour for authenticated requests) with retry logic and exponential backoff.

The Atlassian Marketplace provides pre-built integrations for SonarQube, Checkmarx, Slack, Teams, deployment tools and analytics - search by Bitbucket product filter to find compatible apps.

---

## Sources

- [Atlassian: Bitbucket REST API reference](https://developer.atlassian.com/cloud/bitbucket/rest/intro/)
- [Atlassian: API tokens](https://support.atlassian.com/bitbucket-cloud/docs/create-a-repository-access-token/)
- [Atlassian: OAuth 2.0 for Bitbucket](https://developer.atlassian.com/cloud/bitbucket/oauth-2/)
- [Atlassian: Webhooks](https://support.atlassian.com/bitbucket-cloud/docs/manage-webhooks/)
- [Atlassian: Commit status API](https://developer.atlassian.com/cloud/bitbucket/rest/api-group-commit-statuses/)
- [Atlassian: Pipelines API](https://developer.atlassian.com/cloud/bitbucket/rest/api-group-pipelines/)
- [Atlassian Marketplace: Bitbucket apps](https://marketplace.atlassian.com/addons/app-hosting/bitbucket)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

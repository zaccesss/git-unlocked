# Gitea API and Integrations

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Gitea exposes a complete REST API with interactive Swagger documentation. Every operation available through the web interface - creating repositories, managing issues, triggering Actions, publishing packages - is accessible via HTTP. The API is the foundation for CI/CD integrations, migration scripts, custom tooling and third-party application connections.

---

## Table of Contents

1. [The Gitea REST API](#the-gitea-rest-api)
2. [Authentication](#authentication)
3. [API conventions and versioning](#api-conventions-and-versioning)
4. [Repositories and code](#repositories-and-code)
5. [Issues and pull requests](#issues-and-pull-requests)
6. [Users and organisations](#users-and-organisations)
7. [Gitea Actions via API](#gitea-actions-via-api)
8. [Webhooks](#webhooks)
9. [Integrations with external tools](#integrations-with-external-tools)
10. [Rate limits and pagination](#rate-limits-and-pagination)
11. [Using the Swagger UI](#using-the-swagger-ui)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## The Gitea REST API

The Gitea API follows REST conventions and returns JSON. The base URL is:

```
https://gitea.example.com/api/v1/
```

Interactive documentation is built into every Gitea instance at:

```
https://gitea.example.com/api/swagger
```

The Swagger UI lists every endpoint with request/response schemas, lets you authenticate and execute calls directly from the browser.

### Quick health check

```bash
# Returns instance version and configuration (no auth required)
curl https://gitea.example.com/api/v1/version
```

Response:

```json
{
  "version": "1.25.5"
}
```

---

## Authentication

### Access token (recommended)

Include the token in the `Authorization` header:

```bash
curl -H "Authorization: token YOUR_ACCESS_TOKEN" \
  https://gitea.example.com/api/v1/user
```

Or as a query parameter (less secure, avoid for sensitive operations):

```bash
curl "https://gitea.example.com/api/v1/user?token=YOUR_ACCESS_TOKEN"
```

Create tokens at **Settings** > **Applications** > **Generate Token**. Select the scopes the token needs.

### Basic authentication

For scripts where token storage is inconvenient:

```bash
curl -u "username:password" \
  https://gitea.example.com/api/v1/user
```

Use an access token as the password rather than your account password when basic auth is required.

### OAuth2 bearer tokens

For applications authenticating on behalf of users via OAuth2:

```bash
curl -H "Authorization: Bearer OAUTH2_ACCESS_TOKEN" \
  https://gitea.example.com/api/v1/user
```

### In-pipeline authentication

Within a Gitea Actions workflow, use `${{ secrets.GITEA_TOKEN }}` where a token is needed. Gitea automatically provides a `GITEA_TOKEN` secret scoped to the current repository and workflow run.

```yaml
- name: Create release via API
  run: |
    curl -X POST \
      -H "Authorization: token ${{ secrets.GITEA_TOKEN }}" \
      -H "Content-Type: application/json" \
      -d '{"tag_name": "${{ github.ref_name }}", "name": "Release ${{ github.ref_name }}"}' \
      "${GITEA_SERVER_URL}/api/v1/repos/${{ github.repository }}/releases"
```

---

## API conventions and versioning

### HTTP methods

| Method   | Use                          |
| -------- | ---------------------------- |
| `GET`    | Retrieve a resource or list  |
| `POST`   | Create a resource            |
| `PATCH`  | Partially update a resource  |
| `PUT`    | Replace or create a resource |
| `DELETE` | Remove a resource            |

### Response format

Successful responses return JSON. Lists use arrays directly (not wrapped in a `value` key like Azure DevOps):

```json
[
  { "id": 1, "name": "repo-one" },
  { "id": 2, "name": "repo-two" }
]
```

Single resources return the object directly:

```json
{
  "id": 1,
  "login": "username",
  "full_name": "Isaac Adjei",
  "email": "isaac@example.com"
}
```

### HTTP status codes

| Code                       | Meaning                              |
| -------------------------- | ------------------------------------ |
| `200 OK`                   | Success                              |
| `201 Created`              | Resource created                     |
| `204 No Content`           | Success, no body (DELETE)            |
| `400 Bad Request`          | Invalid request body                 |
| `401 Unauthorized`         | Missing or invalid token             |
| `403 Forbidden`            | Valid token, insufficient permission |
| `404 Not Found`            | Resource does not exist              |
| `409 Conflict`             | Resource already exists              |
| `422 Unprocessable Entity` | Validation error                     |

---

## Repositories and code

### List repositories for the authenticated user

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/search?token=TOKEN&limit=50"
```

### Get a specific repository

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name"
```

### Create a repository

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-new-repo",
    "description": "A new repository",
    "private": true,
    "auto_init": true,
    "default_branch": "main",
    "gitignores": "Python",
    "license": "MIT"
  }' \
  "https://gitea.example.com/api/v1/user/repos"
```

### Delete a repository

```bash
curl -X DELETE \
  -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name"
```

### List branches

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/branches"
```

### Get file contents

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/contents/README.md"
```

The response includes `content` (base64-encoded file content) and `sha` (needed for updates).

### Create or update a file

```bash
# Create a new file
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Add configuration file",
    "content": "'"$(echo -n 'key=value' | base64)"'"
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/contents/config.env"

# Update an existing file (requires sha from GET response)
curl -X PUT \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "message": "Update configuration",
    "content": "'"$(echo -n 'key=new_value' | base64)"'",
    "sha": "FILE_SHA_FROM_GET_RESPONSE"
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/contents/config.env"
```

### List commits

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/commits?sha=main&limit=20"
```

### Create a tag

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "tag_name": "v1.2.0",
    "message": "Release 1.2.0",
    "target": "main"
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/tags"
```

---

## Issues and pull requests

### List issues

```bash
# All open issues
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/issues?type=issues&state=open"

# Issues assigned to the authenticated user
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/issues?type=issues&assigned=true"
```

### Create an issue

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Fix null pointer in login handler",
    "body": "## Description\n\nThe handler crashes when user is nil.\n\n## Steps to reproduce\n\n1. Call /api/login with an empty body",
    "labels": [1, 3],
    "assignees": ["devuser"],
    "milestone": 2
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/issues"
```

### Close an issue

```bash
curl -X PATCH \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state": "closed"}' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/issues/42"
```

### Add a comment to an issue

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"body": "Fixed in commit abc1234. Closing this issue."}' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/issues/42/comments"
```

### Create a pull request

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Add user authentication",
    "body": "Implements JWT authentication for all API endpoints.\n\nCloses #23",
    "head": "feature/user-auth",
    "base": "main",
    "assignees": ["devuser"],
    "labels": [2]
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/pulls"
```

### List pull requests

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/pulls?state=open"
```

### Merge a pull request

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "Do": "squash",
    "merge_message_field": "Add user authentication (#24)",
    "delete_branch_after_merge": true
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/pulls/24/merge"
```

Merge strategies: `merge` (merge commit), `rebase` (rebase), `rebase-merge` (rebase with merge commit), `squash` (squash).

---

## Users and organisations

### Get the authenticated user

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/user"
```

### Search users

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/users/search?q=isaac&limit=10"
```

### Get a user's repositories

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/users/username/repos"
```

### List organisations

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/admin/orgs"
```

### Create an organisation

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "my-org",
    "full_name": "My Organisation",
    "description": "Our development organisation",
    "visibility": "private"
  }' \
  "https://gitea.example.com/api/v1/orgs"
```

### Add a member to a team

```bash
# First get the team ID
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/orgs/my-org/teams"

# Add member
curl -X PUT \
  -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/teams/TEAM_ID/members/username"
```

---

## Gitea Actions via API

### List workflow runs

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/actions/tasks"
```

### Get a specific run

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/actions/tasks/RUN_ID"
```

### List secrets for a repository

```bash
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo-name/actions/secrets"
```

### Create or update a secret

```bash
curl -X PUT \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": "secret-value"}' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/actions/secrets/MY_SECRET"
```

### Create a release (commonly triggered from Actions)

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "tag_name": "v1.3.0",
    "name": "v1.3.0 - Performance improvements",
    "body": "### Changes\n\n- Improved query performance by 40%\n- Fixed memory leak in caching layer",
    "draft": false,
    "prerelease": false,
    "target_commitish": "main"
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/releases"
```

---

## Webhooks

Webhooks send HTTP POST requests to your endpoints when events occur in Gitea.

### Creating a webhook via the web UI

1. Go to repository **Settings** > **Webhooks** > **Add Webhook**
2. Choose the type (Gitea native, or Slack, Discord, Dingtalk, etc.)
3. Enter the **Payload URL**: your endpoint that receives events
4. Set **Content Type**: `application/json`
5. Optionally set a **Secret**: used to sign the payload with HMAC-SHA256
6. Choose which events to send:
   - **Just the push event**: recommended for CI triggers
   - **Send me everything**: all events
   - **Let me choose**: select individual events

### Creating a webhook via API

```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "gitea",
    "config": {
      "url": "https://your-endpoint.example.com/webhook",
      "content_type": "json",
      "secret": "your-webhook-secret"
    },
    "events": ["push", "pull_request", "issues"],
    "active": true
  }' \
  "https://gitea.example.com/api/v1/repos/username/repo-name/hooks"
```

### Webhook event types

| Event                   | When it fires                                              |
| ----------------------- | ---------------------------------------------------------- |
| `push`                  | Commit pushed to any branch                                |
| `create`                | Branch or tag created                                      |
| `delete`                | Branch or tag deleted                                      |
| `fork`                  | Repository forked                                          |
| `issues`                | Issue created, edited, closed, reopened                    |
| `issue_comment`         | Comment on issue or PR                                     |
| `pull_request`          | PR opened, closed, reopened, edited, synchronised          |
| `pull_request_approved` | PR review approved                                         |
| `pull_request_rejected` | PR review rejected                                         |
| `release`               | Release published                                          |
| `repository`            | Repository created, deleted, archived, made public/private |
| `wiki`                  | Wiki page created or updated                               |

### Webhook payload structure

A `push` event payload:

```json
{
  "ref": "refs/heads/main",
  "before": "abc123",
  "after": "def456",
  "commits": [
    {
      "id": "def456",
      "message": "Add user authentication\n\nCloses #23",
      "url": "https://gitea.example.com/username/repo/commit/def456",
      "author": {
        "name": "Isaac Adjei",
        "email": "isaac@example.com",
        "username": "zaccessss"
      },
      "added": ["src/auth.go"],
      "removed": [],
      "modified": ["src/main.go"]
    }
  ],
  "repository": {
    "id": 42,
    "name": "my-repo",
    "full_name": "username/my-repo",
    "html_url": "https://gitea.example.com/username/my-repo",
    "private": false
  },
  "pusher": {
    "login": "username",
    "email": "isaac@example.com"
  }
}
```

### Verifying webhook signatures

If you set a webhook secret, Gitea signs every payload with HMAC-SHA256. Verify the signature in your endpoint:

```python
import hmac
import hashlib
from flask import Flask, request, abort

app = Flask(__name__)
WEBHOOK_SECRET = b"your-webhook-secret"

@app.route("/webhook", methods=["POST"])
def webhook():
    signature = request.headers.get("X-Gitea-Signature")
    if not signature:
        abort(400)

    expected = hmac.new(WEBHOOK_SECRET, request.data, hashlib.sha256).hexdigest()
    if not hmac.compare_digest(f"sha256={expected}", signature):
        abort(403)

    event = request.headers.get("X-Gitea-Event")
    payload = request.json

    if event == "push":
        branch = payload["ref"].replace("refs/heads/", "")
        commits = len(payload["commits"])
        print(f"Push to {branch}: {commits} commit(s)")

    return "OK", 200
```

---

## Integrations with external tools

### Drone CI

Drone CI has native Gitea support:

```yaml
# .drone.yml
kind: pipeline
type: docker
name: default

trigger:
  branch:
    - main

steps:
  - name: test
    image: golang:1.21
    commands:
      - go test ./...
```

Configure Drone to use Gitea as the source at `https://drone.example.com` > Settings > Gitea OAuth.

### Jenkins

Use the [Gitea plugin for Jenkins](https://plugins.jenkins.io/gitea/):

1. Install the Gitea plugin in Jenkins
2. Configure a Gitea server connection in Jenkins settings
3. Use a **Gitea Multibranch Pipeline** job type
4. Jenkins automatically discovers branches and pull requests

### ArgoCD

ArgoCD uses Git repositories as its source of truth. Connect it to a Gitea repository:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: gitea-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
type: Opaque
stringData:
  type: git
  url: https://gitea.example.com/username/k8s-manifests.git
  password: YOUR_ACCESS_TOKEN
  username: username
```

### Renovate bot

Renovate automates dependency updates. Configure it for Gitea in `config.js`:

```javascript
module.exports = {
  platform: "gitea",
  endpoint: "https://gitea.example.com",
  token: process.env.RENOVATE_TOKEN,
  repositories: ["username/my-project"],
};
```

Run Renovate as a scheduled Gitea Actions workflow or as a standalone process.

### VS Code Gitea extension

The **Gitea** extension for VS Code provides:

- Repository browsing from VS Code
- Issue and PR viewing
- Clone from Gitea directly

Search "Gitea" in the VS Code marketplace.

### Terraform Gitea provider

Manage Gitea resources via Terraform:

```hcl
terraform {
  required_providers {
    gitea = {
      source  = "Lerentis/gitea"
      version = "~> 0.14"
    }
  }
}

provider "gitea" {
  base_url   = "https://gitea.example.com"
  token      = var.gitea_token
}

resource "gitea_repository" "my_repo" {
  username    = "username"
  name        = "terraform-managed-repo"
  description = "Created by Terraform"
  private     = true
  auto_init   = true
}
```

---

## Rate limits and pagination

### Rate limits

Gitea implements rate limiting to protect the instance from API abuse. The default limits are configurable by the administrator:

```ini
[api]
ENABLE_SWAGGER       = true
MAX_RESPONSE_ITEMS   = 50      ; max items per list response
DEFAULT_PAGING_NUM   = 20      ; default items per page
```

When rate limited, the API returns `429 Too Many Requests` with a `Retry-After` header.

### Pagination

All list endpoints support `page` and `limit` query parameters:

```bash
# Page 1, 20 items (default)
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo/issues?page=1&limit=20"

# Page 2
curl -H "Authorization: token TOKEN" \
  "https://gitea.example.com/api/v1/repos/username/repo/issues?page=2&limit=20"
```

Check the response headers for pagination metadata:

- `X-Total-Count`: total number of items
- `X-Page`: current page number
- `X-Perpage`: items per page
- `X-PageCount`: total number of pages
- `Link`: RFC 5988 link header with `next`, `prev`, `first` and `last` links

### Iterating all pages in a script

🍎 **Mac / 🐧 Linux** (bash):

```bash
#!/bin/bash
TOKEN="YOUR_TOKEN"
BASE="https://gitea.example.com/api/v1"
REPO="username/repo-name"

page=1
while true; do
  response=$(curl -s -H "Authorization: token $TOKEN" \
    "$BASE/repos/$REPO/issues?state=open&type=issues&page=$page&limit=50")

  count=$(echo "$response" | jq 'length')
  echo "$response" | jq -r '.[].title'

  if [ "$count" -lt 50 ]; then
    break
  fi
  ((page++))
done
```

🪟 **Windows** (PowerShell):

```powershell
$token = "YOUR_TOKEN"
$baseUrl = "https://gitea.example.com/api/v1"
$repo = "username/repo-name"
$page = 1

do {
    $response = Invoke-RestMethod \
        -Uri "$baseUrl/repos/$repo/issues?state=open&type=issues&page=$page&limit=50" \
        -Headers @{Authorization = "token $token"}
    $response | ForEach-Object { Write-Host $_.title }
    $page++
} while ($response.Count -eq 50)
```

---

## Using the Swagger UI

Every Gitea instance includes interactive API documentation at `/api/swagger`. This is the most efficient way to explore the API:

1. Navigate to `https://gitea.example.com/api/swagger`
2. Click **Authorize** and enter your access token (just the token, no "token " prefix)
3. Browse endpoints by category (repos, issues, users, orgs, admin, etc.)
4. Click any endpoint to expand it
5. Click **Try it out** to make a live request with your credentials
6. Fill in the parameters and click **Execute**
7. See the actual request URL, response code and response body

The Swagger UI is particularly useful for:

- Discovering less-known endpoints
- Understanding request body schemas
- Testing API calls before scripting them

---

## Try It Yourself

**Exercise 1 - Make your first API call**

```bash
# Replace with your instance URL and token
TOKEN="your-token"
GITEA="https://gitea.example.com"

# Get your user info
curl -H "Authorization: token $TOKEN" \
  "$GITEA/api/v1/user" | python3 -m json.tool

# List your repositories
curl -H "Authorization: token $TOKEN" \
  "$GITEA/api/v1/user/repos" | python3 -m json.tool
```

**Exercise 2 - Create an issue via API**

```bash
curl -X POST \
  -H "Authorization: token $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Test issue created via API", "body": "This was created programmatically!"}' \
  "$GITEA/api/v1/repos/your-username/your-repo/issues" | python3 -m json.tool
```

**Exercise 3 - Set up a webhook**

1. Use [webhook.site](https://webhook.site) to get a temporary webhook URL
2. Create a webhook via the UI pointing to that URL for push events
3. Push a commit to your repository
4. Watch the event arrive at webhook.site and examine the payload structure

**Exercise 4 - Explore the Swagger UI**

1. Go to `https://gitea.example.com/api/swagger`
2. Authorise with your token
3. Find the `GET /repos/{owner}/{repo}/issues` endpoint
4. Click **Try it out**, fill in your username and repo name
5. Execute and see your issues returned

---

## Common Mistakes

**Using the token without the `token ` prefix in the Authorization header**

The header must be `Authorization: token YOUR_TOKEN`, not `Authorization: YOUR_TOKEN`. The `token ` prefix is required. Omitting it results in 401 errors.

**Not URL-encoding repository names with special characters**

Repository names with spaces or special characters must be URL-encoded in API paths. Gitea discourages special characters in repo names but they are technically allowed. Encode with `%20` for spaces, `%2F` for slashes (not needed in the path segments themselves).

**Treating the list endpoints as returning all results**

By default, list endpoints return 20 items. If a repository has 200 issues, the first response only contains the first 20. Always check `X-Total-Count` in the response headers and paginate to retrieve all data.

**Not validating webhook signatures**

Accepting webhook payloads without verifying the HMAC signature means anyone who knows your endpoint URL can send forged events. Always verify the `X-Gitea-Signature` header against the expected signature.

**Using a full-access token in CI/CD pipelines**

CI/CD pipelines typically need only `repository:write` or specific scopes. Using a token with full access in a pipeline expands the blast radius if the pipeline is compromised. Create pipeline-specific tokens with minimum required scopes.

---

## Summary

Gitea's REST API is accessible at `/api/v1/` with Swagger documentation at `/api/swagger`. Authenticate with an access token in the `Authorization: token TOKEN` header.

Core API areas: repositories (create, read, update, delete, browse files, manage branches), issues (create, update, close, comment), pull requests (create, review, merge) and users/organisations (create teams, manage members).

Webhooks deliver event payloads to your endpoints on push, PR and issue events. Always verify the `X-Gitea-Signature` header using your webhook secret. Common webhook payloads include `ref`, `commits`, `repository` and `pusher` objects.

Third-party integrations include Drone CI (native Gitea support), Jenkins (via plugin), ArgoCD (as a Git source), Renovate (dependency updates) and Terraform (via community provider).

Paginate list responses using `page` and `limit` parameters. Check `X-Total-Count` to know the total number of items and loop until all pages are retrieved.

---

## Sources

- [Gitea: API documentation](https://docs.gitea.com/api/1.20/)
- [Gitea: Swagger UI](https://gitea.example.com/api/swagger) (replace with your instance URL)
- [Gitea: Webhooks](https://docs.gitea.com/usage/webhooks)
- [Drone CI: Gitea integration](https://docs.drone.io/server/provider/gitea/)
- [Renovate: Gitea support](https://docs.renovatebot.com/modules/platform/gitea/)
- [Terraform Gitea provider](https://registry.terraform.io/providers/Lerentis/gitea/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

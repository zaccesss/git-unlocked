# Forgejo API and Integrations

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Forgejo's REST API is compatible with Gitea's for all core operations. The base URL, authentication model, endpoint structure and response format are the same. Forgejo adds ActivityPub-specific endpoints for federation. This file covers the API, webhooks, the Swagger documentation and third-party integrations.

---

## Table of Contents

1. [The Forgejo REST API](#the-forgejo-rest-api)
2. [Authentication](#authentication)
3. [Core API endpoints](#core-api-endpoints)
4. [ActivityPub API endpoints](#activitypub-api-endpoints)
5. [Webhooks](#webhooks)
6. [Third-party integrations](#third-party-integrations)
7. [Rate limits and pagination](#rate-limits-and-pagination)
8. [Try It Yourself](#try-it-yourself)
9. [Common Mistakes](#common-mistakes)
10. [Summary](#summary)
11. [Sources](#sources)

---

## The Forgejo REST API

Forgejo's API lives at:
```
https://forgejo.example.com/api/v1/
```

Interactive Swagger documentation is built into every instance:
```
https://forgejo.example.com/api/swagger
```

The API is intentionally compatible with Gitea's for all core operations. Tools built against the Gitea API work against Forgejo without changes for repositories, issues, pull requests, users, organisations and most other resources.

Forgejo adds:
- ActivityPub federation endpoints under `/api/v1/activitypub/`
- NodeInfo at `/.well-known/nodeinfo`
- WebFinger at `/.well-known/webfinger`

### Instance version

```bash
curl https://forgejo.example.com/api/v1/version
# {"version":"9.0.1"}
```

---

## Authentication

### Access token (recommended)

```bash
curl -H "Authorization: token YOUR_ACCESS_TOKEN" \
  https://forgejo.example.com/api/v1/user
```

Create tokens at Settings > Applications > Generate Token. Select minimum required scopes.

### Basic authentication

```bash
curl -u "username:YOUR_ACCESS_TOKEN" \
  https://forgejo.example.com/api/v1/user
```

Use an access token as the password.

### In Forgejo Actions workflows

The automatic `FORGEJO_TOKEN` secret provides scoped API access within workflows:

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

## Core API endpoints

The core API is identical to Gitea's. All examples below work against Forgejo - replace `forgejo.example.com` with your instance URL.

### Repositories

```bash
# List your repositories
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/search?limit=50"

# Create a repository
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "my-project", "private": true, "auto_init": true}' \
  "https://forgejo.example.com/api/v1/user/repos"

# Get repository details
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo-name"

# List branches
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/branches"

# Get file contents
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/contents/README.md"
```

### Issues

```bash
# List open issues
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/issues?state=open&type=issues"

# Create an issue
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Bug report", "body": "Description of the bug", "labels": [1]}' \
  "https://forgejo.example.com/api/v1/repos/username/repo/issues"

# Close an issue
curl -X PATCH \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state": "closed"}' \
  "https://forgejo.example.com/api/v1/repos/username/repo/issues/42"

# Add a comment
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"body": "Fixed in v1.2.0"}' \
  "https://forgejo.example.com/api/v1/repos/username/repo/issues/42/comments"
```

### Pull requests

```bash
# Create a pull request
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Add feature X",
    "body": "Implements feature X\n\nCloses #23",
    "head": "feature/x",
    "base": "main"
  }' \
  "https://forgejo.example.com/api/v1/repos/username/repo/pulls"

# Merge a pull request
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"Do": "squash", "delete_branch_after_merge": true}' \
  "https://forgejo.example.com/api/v1/repos/username/repo/pulls/24/merge"
```

Merge strategies: `merge` (merge commit), `rebase`, `rebase-merge`, `squash`

### Users and organisations

```bash
# Get authenticated user
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/user"

# Create an organisation
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"username": "my-org", "full_name": "My Organisation", "visibility": "private"}' \
  "https://forgejo.example.com/api/v1/orgs"

# List organisation members
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/orgs/my-org/members"
```

### Releases

```bash
# Create a release
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "tag_name": "v1.0.0",
    "name": "v1.0.0 - Initial release",
    "body": "### What is new\n\n- Feature A\n- Bug fix B",
    "draft": false,
    "prerelease": false
  }' \
  "https://forgejo.example.com/api/v1/repos/username/repo/releases"

# List releases
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/releases"
```

### Actions secrets

```bash
# Set a repository secret
curl -X PUT \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"data": "secret-value"}' \
  "https://forgejo.example.com/api/v1/repos/username/repo/actions/secrets/MY_SECRET"

# List secrets (names only, not values)
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/actions/secrets"
```

---

## ActivityPub API endpoints

Forgejo's federation capabilities expose ActivityPub endpoints. These are used by the federation system internally and by external tools that want to interact with Forgejo's ActivityPub identity.

### NodeInfo

Discover whether federation is enabled:

```bash
curl https://forgejo.example.com/.well-known/nodeinfo
```

Response:
```json
{
  "links": [
    {
      "rel": "http://nodeinfo.diaspora.software/ns/schema/2.1",
      "href": "https://forgejo.example.com/api/v1/nodeinfo"
    }
  ]
}
```

Full node info:
```bash
curl https://forgejo.example.com/api/v1/nodeinfo
```

```json
{
  "version": "2.1",
  "software": {
    "name": "forgejo",
    "version": "9.0.1",
    "homepage": "https://forgejo.org",
    "repository": "https://codeberg.org/forgejo/forgejo"
  },
  "protocols": ["activitypub"],
  "openRegistrations": true
}
```

### WebFinger (user discovery)

Discover a federated user identity:

```bash
curl "https://forgejo.example.com/.well-known/webfinger?resource=acct:username@forgejo.example.com"
```

Response:
```json
{
  "subject": "acct:username@forgejo.example.com",
  "links": [
    {
      "rel": "self",
      "type": "application/activity+json",
      "href": "https://forgejo.example.com/api/v1/activitypub/user-id/1"
    }
  ]
}
```

This is how external ActivityPub platforms (Mastodon, other Forgejo instances) discover a user's ActivityPub actor.

### User actor

Get the ActivityPub actor for a user:

```bash
curl -H "Accept: application/activity+json" \
  "https://forgejo.example.com/api/v1/activitypub/user-id/USERNAME"
```

### Repository actor

Get the ActivityPub actor for a repository:

```bash
curl -H "Accept: application/activity+json" \
  "https://forgejo.example.com/api/v1/activitypub/repository-id/REPO_ID"
```

### Following a remote repository

```bash
# Send a Follow activity to a remote repository
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "actor": "https://your-forgejo.example.com/api/v1/activitypub/user-id/YOUR_USER_ID",
    "object": "https://other-forgejo.example.com/api/v1/activitypub/repository-id/REPO_ID"
  }' \
  "https://your-forgejo.example.com/api/v1/activitypub/user-id/YOUR_USER_ID/outbox"
```

---

## Webhooks

Webhooks deliver event payloads to your endpoints when actions occur in Forgejo.

### Creating a webhook

**Via UI:**
1. Repository **Settings** > **Webhooks** > **Add Webhook** > **Gitea** (native format, works for Forgejo)
2. Set the payload URL, content type (`application/json`), and optional secret
3. Choose events to trigger on
4. Click **Add Webhook**

**Via API:**
```bash
curl -X POST \
  -H "Authorization: token TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "type": "forgejo",
    "config": {
      "url": "https://your-endpoint.example.com/webhook",
      "content_type": "json",
      "secret": "your-webhook-secret"
    },
    "events": ["push", "pull_request", "issues", "release"],
    "active": true
  }' \
  "https://forgejo.example.com/api/v1/repos/username/repo/hooks"
```

### Webhook events

| Event | When it fires |
|---|---|
| `push` | Commit pushed to any branch |
| `create` | Branch or tag created |
| `delete` | Branch or tag deleted |
| `fork` | Repository forked |
| `issues` | Issue created, edited, closed, reopened |
| `issue_comment` | Comment on issue or PR |
| `pull_request` | PR opened, closed, synchronised |
| `pull_request_approved` | PR review approved |
| `release` | Release published |
| `repository` | Repository created, deleted, archived |

### Verifying webhook signatures

Forgejo signs payloads with HMAC-SHA256 when a secret is set. Verify in your endpoint:

```python
import hmac
import hashlib
from flask import Flask, request, abort

app = Flask(__name__)
WEBHOOK_SECRET = b"your-webhook-secret"

@app.route("/webhook", methods=["POST"])
def handle_webhook():
    signature = request.headers.get("X-Gitea-Signature")  # Forgejo uses this header
    if not signature:
        abort(400)

    expected = hmac.new(WEBHOOK_SECRET, request.data, hashlib.sha256).hexdigest()
    if not hmac.compare_digest(f"sha256={expected}", signature):
        abort(403)

    event = request.headers.get("X-Gitea-Event")
    payload = request.json

    if event == "push":
        branch = payload["ref"].replace("refs/heads/", "")
        print(f"Push to {branch} by {payload['pusher']['login']}")

    return "OK", 200
```

> [!NOTE]
> Forgejo uses the same webhook header names as Gitea (`X-Gitea-Signature`, `X-Gitea-Event`) for backward compatibility. Do not change existing Gitea webhook handlers when migrating to Forgejo.

---

## Third-party integrations

### Renovate bot

Renovate automates dependency updates. Configure for Forgejo:

```javascript
// renovate.config.js
module.exports = {
  platform: 'gitea',           // works for Forgejo too
  endpoint: 'https://forgejo.example.com',
  token: process.env.RENOVATE_TOKEN,
  repositories: ['username/my-project']
};
```

Run as a scheduled Forgejo Actions workflow:

```yaml
on:
  schedule:
    - cron: '0 3 * * *'

jobs:
  renovate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npx renovate
        env:
          RENOVATE_TOKEN: ${{ secrets.RENOVATE_TOKEN }}
          RENOVATE_PLATFORM: gitea
          RENOVATE_ENDPOINT: https://forgejo.example.com
```

### Woodpecker CI

Woodpecker CI is a community fork of Drone CI with native Forgejo support:

1. Register a Forgejo OAuth2 application (Settings > Applications > Manage OAuth2)
2. Set the callback URL: `https://woodpecker.example.com/authorize`
3. Configure Woodpecker:

```yaml
# docker-compose for Woodpecker
services:
  woodpecker-server:
    image: woodpeckerci/woodpecker-server:latest
    environment:
      WOODPECKER_OPEN: "true"
      WOODPECKER_FORGEJO: "true"
      WOODPECKER_FORGEJO_URL: "https://forgejo.example.com"
      WOODPECKER_FORGEJO_CLIENT: "YOUR_OAUTH2_CLIENT_ID"
      WOODPECKER_FORGEJO_SECRET: "YOUR_OAUTH2_SECRET"
```

### Terraform Forgejo provider

Manage Forgejo resources via Terraform:

```hcl
terraform {
  required_providers {
    forgejo = {
      source  = "svalabs/forgejo"
      version = "~> 0.1"
    }
  }
}

provider "forgejo" {
  base_url = "https://forgejo.example.com"
  token    = var.forgejo_token
}

resource "forgejo_repository" "my_repo" {
  owner       = "username"
  name        = "terraform-managed"
  description = "Created by Terraform"
  private     = true
  auto_init   = true
}
```

### ArgoCD

ArgoCD can use Forgejo repositories as its source of truth:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: forgejo-repo
  namespace: argocd
  labels:
    argocd.argoproj.io/secret-type: repository
type: Opaque
stringData:
  type: git
  url: https://forgejo.example.com/username/k8s-manifests.git
  password: YOUR_ACCESS_TOKEN
  username: username
```

---

## Rate limits and pagination

### Rate limits

Forgejo applies rate limits configurable in `app.ini`:

```ini
[api]
MAX_RESPONSE_ITEMS = 50
DEFAULT_PAGING_NUM = 20
```

On rate limit: HTTP `429 Too Many Requests` with `Retry-After` header.

### Pagination

All list endpoints support `page` and `limit`:

```bash
# Page 1, default limit
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/issues?page=1&limit=50"

# Page 2
curl -H "Authorization: token TOKEN" \
  "https://forgejo.example.com/api/v1/repos/username/repo/issues?page=2&limit=50"
```

Response headers:
- `X-Total-Count`: total number of items
- `X-Page`: current page
- `X-Perpage`: items per page
- `X-PageCount`: total pages

### Iterating all pages

🐧 **Linux / 🍎 Mac** (bash + jq):
```bash
#!/bin/bash
TOKEN="YOUR_TOKEN"
BASE="https://forgejo.example.com/api/v1"
REPO="username/repo"

page=1
while true; do
  response=$(curl -s -H "Authorization: token $TOKEN" \
    "$BASE/repos/$REPO/issues?state=open&page=$page&limit=50")

  count=$(echo "$response" | jq 'length')
  echo "$response" | jq -r '.[].title'

  [ "$count" -lt 50 ] && break
  ((page++))
done
```

---

## Try It Yourself

**Exercise 1 - First API call**

```bash
TOKEN="your-token"
FORGEJO="https://forgejo.example.com"  # or https://codeberg.org

# Get your user info
curl -H "Authorization: token $TOKEN" \
  "$FORGEJO/api/v1/user" | python3 -m json.tool

# List your repos
curl -H "Authorization: token $TOKEN" \
  "$FORGEJO/api/v1/user/repos" | python3 -m json.tool
```

**Exercise 2 - Explore federation endpoints**

```bash
# Check if federation is enabled on Codeberg
curl https://codeberg.org/api/v1/nodeinfo | python3 -m json.tool

# WebFinger lookup for your Codeberg user
curl "https://codeberg.org/.well-known/webfinger?resource=acct:yourusername@codeberg.org" \
  | python3 -m json.tool
```

**Exercise 3 - Set up a webhook**

1. Use [webhook.site](https://webhook.site) for a temporary endpoint
2. Create a webhook pointing to that URL for push events
3. Push a commit and watch the payload arrive
4. Note the `X-Gitea-Event` and `X-Gitea-Signature` headers (Forgejo uses these for compatibility)

**Exercise 4 - Swagger UI**

1. Navigate to `https://forgejo.example.com/api/swagger` (or `https://codeberg.org/api/swagger`)
2. Authorise with your token
3. Find `GET /repos/{owner}/{repo}/issues`
4. Try it with your username and repository

---

## Common Mistakes

**Using `X-Forgejo-Signature` to verify webhooks**

Forgejo uses `X-Gitea-Signature` (not `X-Forgejo-Signature`) for backward compatibility. Existing Gitea webhook verification code works without modification. Do not write new handlers looking for a Forgejo-specific header.

**Not paginating through large result sets**

The default page size is 20 items. A repository with 200 issues returns only the first 20 without pagination. Always check `X-Total-Count` and loop through pages.

**Using full access tokens for automation**

Create scoped tokens for specific automation tasks. A token for a CI pipeline should have `repository:read` and `actions:write` only - not `admin` scope. Minimum scope = minimum risk.

**Treating Forgejo and Gitea APIs as identical**

They are nearly identical for core operations but Forgejo adds ActivityPub endpoints and may add or change endpoints over time as it develops independently. Check the Forgejo Swagger docs for the exact current API surface.

---

## Summary

Forgejo's REST API at `/api/v1/` is compatible with Gitea's for all core operations. Authenticate with `Authorization: token YOUR_TOKEN`. The Swagger UI at `/api/swagger` provides interactive documentation.

Core API areas: repositories, issues, pull requests, users, organisations, releases and Actions secrets. All are identical to Gitea's API - existing Gitea client code works against Forgejo.

Forgejo adds ActivityPub endpoints: NodeInfo at `/.well-known/nodeinfo`, WebFinger at `/.well-known/webfinger` and ActivityPub actors at `/api/v1/activitypub/`. These support federation discovery and interaction.

Webhooks deliver event payloads on push, PR and issue events. Forgejo uses the same webhook headers as Gitea (`X-Gitea-Signature`, `X-Gitea-Event`) for backward compatibility. Verify signatures using HMAC-SHA256.

Third-party integrations: Renovate (using `platform: gitea` - works for Forgejo), Woodpecker CI (native Forgejo support), Terraform (community provider), ArgoCD (as a Git repository source).

Paginate list responses with `page` and `limit` parameters. Check `X-Total-Count` for the total item count.

---

## Sources

- [Forgejo: API documentation](https://forgejo.org/docs/latest/api/)
- [Forgejo: Swagger UI](https://codeberg.org/api/swagger) (Codeberg's public instance)
- [Forgejo: Webhooks](https://forgejo.org/docs/latest/user/webhooks/)
- [Forgejo: Federation API](https://forgejo.org/docs/latest/admin/federation/)
- [Woodpecker CI: Forgejo support](https://woodpecker-ci.org/docs/administration/vcs/forgejo)
- [Renovate: Gitea/Forgejo platform](https://docs.renovatebot.com/modules/platform/gitea/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

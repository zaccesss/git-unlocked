# GitHub Advanced Features

**Difficulty:** 🔴 Advanced | **Time:** 50 minutes

This file covers the powerful GitHub features that sit beyond everyday use: the REST and GraphQL APIs, webhooks, GitHub Apps, OAuth Apps, GitHub Sponsors, Wikis, Gists and advanced search. These are the tools you reach for when you are building on top of GitHub, automating workflows beyond Actions, integrating external services or monetising open source work.

---

## Table of Contents

- [GitHub REST API](#github-rest-api)
- [Authentication for the API](#authentication-for-the-api)
- [Making REST API requests](#making-rest-api-requests)
- [Key REST API endpoints](#key-rest-api-endpoints)
- [Rate limits](#rate-limits)
- [Pagination](#pagination)
- [GitHub GraphQL API](#github-graphql-api)
- [GraphQL vs REST - when to use each](#graphql-vs-rest---when-to-use-each)
- [Making GraphQL queries](#making-graphql-queries)
- [GraphQL mutations](#graphql-mutations)
- [Personal Access Tokens](#personal-access-tokens)
- [Webhooks](#webhooks)
- [Webhook events and payloads](#webhook-events-and-payloads)
- [Verifying webhook signatures](#verifying-webhook-signatures)
- [Testing webhooks locally](#testing-webhooks-locally)
- [GitHub Apps](#github-apps)
- [GitHub Apps vs OAuth Apps vs PATs](#github-apps-vs-oauth-apps-vs-pats)
- [Installing existing GitHub Apps](#installing-existing-github-apps)
- [Creating your own GitHub App](#creating-your-own-github-app)
- [Authenticating as a GitHub App](#authenticating-as-a-github-app)
- [OAuth Apps](#oauth-apps)
- [GitHub Sponsors](#github-sponsors)
- [GitHub Wikis](#github-wikis)
- [GitHub Gists](#github-gists)
- [Advanced Search](#advanced-search)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## GitHub REST API

The GitHub REST API lets you programmatically interact with almost every GitHub feature. You can create repositories, open issues, manage pull requests, trigger workflows, query organisation membership, manage releases and retrieve any data available on GitHub.com - all via HTTP requests.

**Base URL:** `https://api.github.com`

**API version:** GitHub uses a header-based versioning scheme. Include the version header to ensure your requests target a stable API version:

```
X-GitHub-Api-Version: 2022-11-28
```

**Response format:** All responses are JSON. Error responses include a `message` field explaining what went wrong and a `documentation_url` pointing to the relevant docs page.

---

## Authentication for the API

**Unauthenticated requests:**

You can make unauthenticated requests for public data. These are limited to 60 requests per hour per IP address.

```bash
curl https://api.github.com/users/zaccesss
curl https://api.github.com/repos/zaccesss/git-unlocked
```

**Authenticated requests with a Personal Access Token:**

```bash
curl -H "Authorization: Bearer ghp_yourTokenHere" \
     -H "X-GitHub-Api-Version: 2022-11-28" \
     https://api.github.com/user
```

Authenticated requests get 5000 requests per hour. Use a fine-grained PAT with the minimum required scopes.

**Authenticated requests from GitHub Actions:**

In a workflow, the `GITHUB_TOKEN` is available automatically as a secret:

```yaml
- name: Call GitHub API
  run: |
    curl -H "Authorization: Bearer ${{ secrets.GITHUB_TOKEN }}" \
         https://api.github.com/repos/${{ github.repository }}/issues
```

---

## Making REST API requests

**GET - retrieve data:**

```bash
# Get a user's profile
curl -H "Authorization: Bearer TOKEN" \
  https://api.github.com/users/zaccesss

# Get a repository
curl -H "Authorization: Bearer TOKEN" \
  https://api.github.com/repos/zaccesss/git-unlocked

# Get a repository's open issues
curl -H "Authorization: Bearer TOKEN" \
  "https://api.github.com/repos/zaccesss/git-unlocked/issues?state=open&per_page=100"
```

**POST - create resources:**

```bash
# Create an issue
curl -X POST \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  -d '{"title":"Bug: login fails on mobile","body":"Steps to reproduce...","labels":["bug"]}' \
  https://api.github.com/repos/OWNER/REPO/issues
```

**PATCH - update resources:**

```bash
# Update an issue (change state to closed)
curl -X PATCH \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"state":"closed","state_reason":"completed"}' \
  https://api.github.com/repos/OWNER/REPO/issues/42
```

**DELETE - remove resources:**

```bash
# Delete a repository (requires admin permission and repo:delete scope)
curl -X DELETE \
  -H "Authorization: Bearer TOKEN" \
  https://api.github.com/repos/OWNER/REPO
```

**Using the API with Python (requests library):**

```python
import requests

TOKEN = "ghp_yourTokenHere"
HEADERS = {
    "Authorization": f"Bearer {TOKEN}",
    "X-GitHub-Api-Version": "2022-11-28",
    "Accept": "application/vnd.github+json"
}

# List open issues
response = requests.get(
    "https://api.github.com/repos/OWNER/REPO/issues",
    headers=HEADERS,
    params={"state": "open", "per_page": 100}
)
response.raise_for_status()
issues = response.json()
for issue in issues:
    print(f"#{issue['number']}: {issue['title']}")

# Create an issue
new_issue = requests.post(
    "https://api.github.com/repos/OWNER/REPO/issues",
    headers=HEADERS,
    json={"title": "New issue", "body": "Issue body", "labels": ["bug"]}
)
print(f"Created issue #{new_issue.json()['number']}")
```

**Using the API with JavaScript (fetch):**

```javascript
const TOKEN = process.env.GITHUB_TOKEN;
const HEADERS = {
  Authorization: `Bearer ${TOKEN}`,
  "X-GitHub-Api-Version": "2022-11-28",
  Accept: "application/vnd.github+json",
  "Content-Type": "application/json",
};

// List repositories for an organisation
const response = await fetch("https://api.github.com/orgs/ORG_NAME/repos?type=all&per_page=100", { headers: HEADERS });
const repos = await response.json();
repos.forEach((repo) => console.log(repo.full_name));
```

**Using Octokit (official GitHub SDK):**

Octokit is the official GitHub API client, available for JavaScript/TypeScript, Ruby and .NET.

```javascript
import { Octokit } from "@octokit/rest";

const octokit = new Octokit({ auth: process.env.GITHUB_TOKEN });

// Create an issue
const { data: issue } = await octokit.rest.issues.create({
  owner: "zaccesss",
  repo: "git-unlocked",
  title: "New issue via Octokit",
  body: "Issue body",
  labels: ["enhancement"],
});
console.log(`Created issue #${issue.number}`);

// List pull requests
const { data: prs } = await octokit.rest.pulls.list({
  owner: "zaccesss",
  repo: "git-unlocked",
  state: "open",
});
prs.forEach((pr) => console.log(`#${pr.number} ${pr.title}`));
```

---

## Key REST API Endpoints

| Resource      | Method | Endpoint                                                  | Description                 |
| ------------- | ------ | --------------------------------------------------------- | --------------------------- |
| Users         | GET    | `/users/{username}`                                       | Get a user's public profile |
| Users         | GET    | `/user`                                                   | Get the authenticated user  |
| Repositories  | GET    | `/repos/{owner}/{repo}`                                   | Get repository info         |
| Repositories  | POST   | `/user/repos`                                             | Create a repository         |
| Repositories  | GET    | `/repos/{owner}/{repo}/contents/{path}`                   | Get file contents           |
| Issues        | GET    | `/repos/{owner}/{repo}/issues`                            | List issues                 |
| Issues        | POST   | `/repos/{owner}/{repo}/issues`                            | Create an issue             |
| Issues        | PATCH  | `/repos/{owner}/{repo}/issues/{number}`                   | Update an issue             |
| Pull Requests | GET    | `/repos/{owner}/{repo}/pulls`                             | List pull requests          |
| Pull Requests | POST   | `/repos/{owner}/{repo}/pulls`                             | Create a pull request       |
| Pull Requests | PUT    | `/repos/{owner}/{repo}/pulls/{number}/merge`              | Merge a pull request        |
| Reviews       | POST   | `/repos/{owner}/{repo}/pulls/{number}/reviews`            | Create a PR review          |
| Commits       | GET    | `/repos/{owner}/{repo}/commits`                           | List commits                |
| Branches      | GET    | `/repos/{owner}/{repo}/branches`                          | List branches               |
| Branches      | POST   | `/repos/{owner}/{repo}/git/refs`                          | Create a branch             |
| Actions       | GET    | `/repos/{owner}/{repo}/actions/runs`                      | List workflow runs          |
| Actions       | POST   | `/repos/{owner}/{repo}/actions/workflows/{id}/dispatches` | Trigger a workflow          |
| Releases      | GET    | `/repos/{owner}/{repo}/releases`                          | List releases               |
| Releases      | POST   | `/repos/{owner}/{repo}/releases`                          | Create a release            |
| Organisations | GET    | `/orgs/{org}/members`                                     | List org members            |
| Organisations | GET    | `/orgs/{org}/repos`                                       | List org repositories       |
| Search        | GET    | `/search/repositories`                                    | Search repositories         |
| Search        | GET    | `/search/issues`                                          | Search issues and PRs       |
| Search        | GET    | `/search/code`                                            | Search code                 |

---

## Rate Limits

| Request type                  | Limit                                               |
| ----------------------------- | --------------------------------------------------- |
| Unauthenticated               | 60 requests/hour per IP                             |
| Authenticated (PAT or OAuth)  | 5000 requests/hour per token                        |
| GitHub Actions (GITHUB_TOKEN) | 1000 requests/hour per repository                   |
| Search API (authenticated)    | 30 requests/minute                                  |
| GraphQL API                   | 5000 points/hour (points vary per query complexity) |

**Check your current rate limit status:**

```bash
curl -H "Authorization: Bearer TOKEN" https://api.github.com/rate_limit
```

Response includes `limit`, `remaining`, `reset` (Unix timestamp) and `used` for each resource type.

**Handling rate limits in code:**

Check the `X-RateLimit-Remaining` header on every response. When it approaches 0, pause until the `X-RateLimit-Reset` timestamp.

```python
response = requests.get(url, headers=HEADERS)
remaining = int(response.headers.get("X-RateLimit-Remaining", 1))
reset_time = int(response.headers.get("X-RateLimit-Reset", 0))

if remaining < 10:
    wait_seconds = reset_time - time.time() + 1
    if wait_seconds > 0:
        time.sleep(wait_seconds)
```

---

## Pagination

API endpoints that return lists are paginated. By default, most endpoints return 30 items. The maximum is 100 items per page using `?per_page=100`.

**Navigate pages with query parameters:**

```bash
# Page 1 (default)
GET /repos/OWNER/REPO/issues?per_page=100

# Page 2
GET /repos/OWNER/REPO/issues?per_page=100&page=2
```

**Use the Link header for reliable pagination:**

The response `Link` header contains URLs for `next`, `prev`, `first` and `last` pages:

```
Link: <https://api.github.com/repos/OWNER/REPO/issues?page=2>; rel="next",
      <https://api.github.com/repos/OWNER/REPO/issues?page=5>; rel="last"
```

Follow the `next` URL until there is no `next` link, which means you are on the last page.

**Paginating all results with Octokit:**

```javascript
// Get ALL issues, handling pagination automatically
const issues = await octokit.paginate(octokit.rest.issues.listForRepo, {
  owner: "OWNER",
  repo: "REPO",
  state: "all",
  per_page: 100,
});
console.log(`Total issues: ${issues.length}`);
```

**Paginating with gh CLI:**

```bash
gh api repos/OWNER/REPO/issues --paginate --jq '.[].title'
```

The `--paginate` flag automatically follows pagination and concatenates all results.

---

## GitHub GraphQL API

The GraphQL API (`https://api.github.com/graphql`) allows you to request exactly the data you need in a single query. Instead of making three REST calls and discarding unwanted fields, you specify exactly which fields to return at any depth of nesting.

**Endpoint:** Always POST to `https://api.github.com/graphql`

**Authentication:** Same Bearer token as REST, in the `Authorization` header.

---

## GraphQL vs REST - When to Use Each

**Use REST when:**

- You need a simple, single-resource operation (get a user, create an issue)
- You are building a quick script and do not want to learn GraphQL syntax
- You are checking rate limits or account status
- You are working with file contents (REST has better support for base64-encoded file operations)

**Use GraphQL when:**

- You need deeply nested data in one request (e.g. issues → comments → author → following)
- You are building a dashboard or reporting tool that aggregates data from multiple resources
- You are working with GitHub Projects v2 (the Projects API is GraphQL-only)
- You want to minimise API calls and only receive exactly the fields you need
- You are querying large amounts of data where response size matters

---

## Making GraphQL Queries

**Basic query syntax:**

```graphql
query {
  viewer {
    login
    name
    email
  }
}
```

**Send with curl:**

```bash
curl -X POST \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ viewer { login name } }"}' \
  https://api.github.com/graphql
```

**Query with variables (recommended for any non-trivial query):**

```bash
curl -X POST \
  -H "Authorization: Bearer TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "query($owner:String!, $repo:String!) { repository(owner:$owner, name:$repo) { issues(states:OPEN, first:10) { nodes { number title createdAt } } } }",
    "variables": {"owner": "zaccesss", "repo": "git-unlocked"}
  }' \
  https://api.github.com/graphql
```

**Formatted query (same as above, easier to read):**

```graphql
query GetOpenIssues($owner: String!, $repo: String!) {
  repository(owner: $owner, name: $repo) {
    issues(states: OPEN, first: 10, orderBy: { field: CREATED_AT, direction: DESC }) {
      totalCount
      nodes {
        number
        title
        createdAt
        author {
          login
        }
        labels(first: 5) {
          nodes {
            name
            color
          }
        }
      }
      pageInfo {
        hasNextPage
        endCursor
      }
    }
  }
}
```

**GitHub Projects v2 query (GraphQL only):**

```graphql
query {
  user(login: "zaccesss") {
    projectsV2(first: 5) {
      nodes {
        id
        title
        items(first: 20) {
          nodes {
            id
            fieldValues(first: 8) {
              nodes {
                ... on ProjectV2ItemFieldTextValue {
                  text
                  field {
                    ... on ProjectV2FieldCommon {
                      name
                    }
                  }
                }
                ... on ProjectV2ItemFieldSingleSelectValue {
                  name
                  field {
                    ... on ProjectV2FieldCommon {
                      name
                    }
                  }
                }
              }
            }
          }
        }
      }
    }
  }
}
```

**Interactive explorer:**

Test any GraphQL query against your real account at `docs.github.com/en/graphql/overview/explorer`. It has autocomplete, schema browsing and shows live results.

---

## GraphQL Mutations

Mutations create, update or delete data. They use the `mutation` keyword.

**Create an issue via GraphQL:**

```graphql
mutation CreateIssue($repoId: ID!, $title: String!, $body: String) {
  createIssue(input: { repositoryId: $repoId, title: $title, body: $body }) {
    issue {
      number
      url
    }
  }
}
```

**Add an item to a GitHub Project:**

```graphql
mutation AddToProject($projectId: ID!, $contentId: ID!) {
  addProjectV2ItemById(input: { projectId: $projectId, contentId: $contentId }) {
    item {
      id
    }
  }
}
```

**Update a project item field:**

```graphql
mutation UpdateField($projectId: ID!, $itemId: ID!, $fieldId: ID!, $value: ProjectV2FieldValue!) {
  updateProjectV2ItemFieldValue(input: { projectId: $projectId, itemId: $itemId, fieldId: $fieldId, value: $value }) {
    projectV2Item {
      id
    }
  }
}
```

---

## Personal Access Tokens

PATs authenticate API requests on behalf of a user account.

**Two types:**

- **Classic PAT** - coarse-grained scopes (`repo`, `write:org`, `read:user` etc.), applies to all repositories
- **Fine-grained PAT** - granular permissions per repository or organisation, preferred for new integrations

**Creating a fine-grained PAT:**

1. Go to `github.com/settings/tokens`
2. Click **Generate new token → Fine-grained personal access token**
3. Give it a descriptive name (e.g. "CI script - repo issues read")
4. Set an expiration date - never set tokens with no expiry for anything other than local testing
5. Under **Repository access**, choose:
   - **All repositories** - access to all current and future repositories
   - **Only select repositories** - choose specific repositories (more secure, preferred)
6. Under **Permissions**, expand each section and set only what you need:
   - Contents: Read (to read code)
   - Issues: Read and Write (to create and close issues)
   - Pull requests: Read and Write (to create and merge PRs)
   - Actions: Read (to view workflow runs)
   - Metadata: Read (required for all tokens, set automatically)
7. Click **Generate token**
8. Copy the token immediately - it is shown only once

**Storing tokens safely:**

```bash
# Never do this (hardcoded in script)
TOKEN="ghp_abc123"

# Do this instead - environment variable
export GITHUB_TOKEN="ghp_abc123"

# Or load from a .env file (always in .gitignore)
source .env
```

In CI/CD systems, store tokens as encrypted secrets. In GitHub Actions, use repository secrets: `${{ secrets.MY_TOKEN }}`.

**Revoking a token:**

If a token is exposed (committed to a repo, sent in an email, logged), revoke it immediately at `github.com/settings/tokens`. GitHub's push protection may detect it and notify you automatically.

---

## Webhooks

Webhooks allow GitHub to send HTTP POST requests to a URL you control whenever specific events occur. Rather than polling the API, GitHub pushes event data to you in real time.

**Common use cases:**

- Post a Slack or Teams message when a PR is merged or a CI check fails
- Deploy code automatically when a push reaches `main`
- Create a ticket in Jira or Linear when a GitHub issue is opened
- Update a dashboard when a release is published
- Sync data to an external system when repository settings change

**Creating a repository webhook:**

1. Go to your repository → **Settings → Webhooks**
2. Click **Add webhook**
3. **Payload URL** - the HTTPS endpoint on your server that will receive POST requests
4. **Content type** - set to `application/json`
5. **Secret** - enter a random, strong string (e.g. from `openssl rand -hex 32`). GitHub uses this to sign payloads so you can verify they are genuine.
6. **SSL verification** - leave enabled. Your endpoint must have a valid SSL certificate.
7. **Events** - select which events trigger the webhook. Options:
   - **Just the push event** - only push events
   - **Send me everything** - all 50+ event types
   - **Let me select individual events** - choose specifically
8. Click **Add webhook**

**Common events to subscribe to:**

| Event           | When it fires                                              |
| --------------- | ---------------------------------------------------------- |
| `push`          | Any push to any branch                                     |
| `pull_request`  | PR opened, closed, merged, synchronised, reviewed          |
| `issues`        | Issue opened, closed, edited, labelled                     |
| `issue_comment` | Comment added to an issue or PR                            |
| `workflow_run`  | Actions workflow run started, completed, requested         |
| `release`       | Release published, edited, deleted                         |
| `create`        | Branch or tag created                                      |
| `delete`        | Branch or tag deleted                                      |
| `member`        | Collaborator added or removed                              |
| `repository`    | Repository created, deleted, archived, made public/private |

---

## Webhook Events and Payloads

Every webhook POST request includes:

**Headers:**

```
X-GitHub-Event: push
X-GitHub-Delivery: 72d3162e-cc78-11e3-81ab-4c9367dc0958
X-Hub-Signature-256: sha256=d57c68ca6f92289e6987010a9d5c44c62fda1aca4b...
Content-Type: application/json
```

**Example push event payload:**

```json
{
  "ref": "refs/heads/main",
  "before": "abc123",
  "after": "def456",
  "repository": {
    "id": 123456,
    "full_name": "zaccesss/git-unlocked",
    "private": false
  },
  "pusher": {
    "name": "isaacadjei",
    "email": "isaac@example.com"
  },
  "commits": [
    {
      "id": "def456",
      "message": "fix: correct typo in README",
      "timestamp": "2025-01-15T14:30:00Z",
      "author": {
        "name": "Isaac Adjei",
        "email": "isaac@example.com",
        "username": "isaacadjei"
      },
      "added": [],
      "removed": [],
      "modified": ["README.md"]
    }
  ]
}
```

**Example pull_request event payload (partial):**

```json
{
  "action": "closed",
  "pull_request": {
    "number": 42,
    "title": "Add user authentication",
    "state": "closed",
    "merged": true,
    "merged_by": {
      "login": "isaacadjei"
    },
    "base": {
      "ref": "main"
    },
    "head": {
      "ref": "feature/auth"
    }
  },
  "repository": {
    "full_name": "zaccesss/git-unlocked"
  }
}
```

The `action` field distinguishes what happened within an event type. A `pull_request` event can have actions: `opened`, `closed`, `synchronize`, `reopened`, `labeled`, `review_requested` etc.

---

## Verifying Webhook Signatures

Always verify that incoming webhook requests genuinely came from GitHub. Without verification, anyone who discovers your webhook URL can send fake payloads.

**How it works:** GitHub computes `HMAC-SHA256(secret, payload_body)` and sends the result in the `X-Hub-Signature-256` header. You compute the same HMAC and compare.

**Python verification:**

```python
import hmac
import hashlib
from flask import Flask, request, abort

app = Flask(__name__)
WEBHOOK_SECRET = "your-secret-here"

def verify_signature(payload_body: bytes, signature_header: str) -> bool:
    if not signature_header:
        return False
    expected = hmac.new(
        WEBHOOK_SECRET.encode("utf-8"),
        payload_body,
        hashlib.sha256
    ).hexdigest()
    expected_header = f"sha256={expected}"
    # Use compare_digest to prevent timing attacks
    return hmac.compare_digest(expected_header, signature_header)

@app.route("/webhook", methods=["POST"])
def webhook():
    signature = request.headers.get("X-Hub-Signature-256", "")
    if not verify_signature(request.get_data(), signature):
        abort(403, "Invalid signature")

    event = request.headers.get("X-GitHub-Event")
    payload = request.json

    if event == "push" and payload["ref"] == "refs/heads/main":
        # Deploy on push to main
        deploy()

    return "OK", 200
```

**Node.js verification:**

```javascript
const crypto = require("crypto");

function verifySignature(payload, signature, secret) {
  const expected = "sha256=" + crypto.createHmac("sha256", secret).update(payload).digest("hex");
  return crypto.timingSafeEqual(Buffer.from(expected), Buffer.from(signature));
}

app.post("/webhook", express.raw({ type: "application/json" }), (req, res) => {
  const signature = req.headers["x-hub-signature-256"];
  if (!verifySignature(req.body, signature, process.env.WEBHOOK_SECRET)) {
    return res.status(403).send("Forbidden");
  }
  const payload = JSON.parse(req.body);
  // Handle event...
  res.status(200).send("OK");
});
```

> [!IMPORTANT]
> Read the raw request body before any JSON parsing for signature verification. Many frameworks (like Express with `express.json()`) parse the body and discard the raw bytes. Use `express.raw()` or equivalent to preserve the raw body.

---

## Testing Webhooks Locally

During development your local server is not accessible from the internet, so GitHub cannot send webhooks to it directly. Use a relay tool to expose your local port.

**Using smee.io (free, no install):**

```bash
npm install -g smee-client
# Get a channel URL at smee.io first, then:
smee --url https://smee.io/YOUR_CHANNEL --target http://localhost:3000/webhook
```

Point your GitHub webhook at the `smee.io` URL. Smee relays requests to your local port.

**Using ngrok:**

```bash
# Install ngrok at ngrok.com, then:
ngrok http 3000
# ngrok gives you a public HTTPS URL like https://abc123.ngrok.io
# Point your GitHub webhook at https://abc123.ngrok.io/webhook
```

**Replaying deliveries:**

In the webhook settings page (**Settings → Webhooks → your webhook → Recent Deliveries**), you can see all recent delivery attempts and replay any of them. This lets you re-test handling of a specific event without having to trigger it again.

---

## GitHub Apps

GitHub Apps are the recommended way to build production integrations. Unlike OAuth Apps that act as a user, GitHub Apps have their own identity - they act as `app[bot]`.

**Advantages over PATs:**

- Act as the app, not as a user - actions are attributed to the app, not to a person's account
- Fine-grained permissions per repository selected during installation
- Higher rate limits: up to 15,000 requests/hour
- Can be installed on organisations and users can control which repositories it accesses
- Webhooks built in as part of the app definition
- Tokens are short-lived (1 hour) - automatic expiry limits blast radius if compromised

**How GitHub Apps are used in practice:**

Most major GitHub integrations you already use are GitHub Apps: Dependabot, CodeClimate, CircleCI, Travis CI, Vercel, Netlify, Renovate, Linear, Sentry, Datadog, Slack and dozens more. When you install one of these on a repository, you are installing a GitHub App.

---

## GitHub Apps vs OAuth Apps vs PATs

|              | PAT (fine-grained)   | OAuth App                   | GitHub App              |
| ------------ | -------------------- | --------------------------- | ----------------------- |
| Acts as      | A user account       | A user account              | The app itself          |
| Rate limit   | 5000/hour            | 5000/hour                   | 5000-15000/hour         |
| Scope        | Per repository       | All user repos              | Per installation        |
| Token expiry | Set by user          | Never (unless revoked)      | 1 hour (auto-refresh)   |
| Webhook      | No                   | No                          | Yes (built-in)          |
| Best for     | Personal scripts, CI | Web apps needing user login | Production integrations |
| Complexity   | Low                  | Medium                      | High                    |

---

## Installing Existing GitHub Apps

**From GitHub Marketplace:**

1. Go to `github.com/marketplace`
2. Browse or search for apps by category
3. Click any app, read the description, permissions and pricing
4. Click **Install it for free** (or the pricing button)
5. Choose to install on your personal account or an organisation
6. Select which repositories it can access: **All repositories** or **Only select repositories**
7. Review the permissions it requests and click **Install**

**Removing an installed app:**

1. Go to **Settings → Applications → Installed GitHub Apps**
2. Click **Configure** next to the app
3. Click **Uninstall** at the bottom

---

## Creating Your Own GitHub App

**Step 1.** Go to `github.com/settings/apps` and click **New GitHub App**.

**Step 2.** Fill in the basics:

- **GitHub App name** - unique across all of GitHub
- **Homepage URL** - your app's website or repo
- **Callback URL** - where to redirect after OAuth (if your app uses user authentication)
- **Webhook URL** - your endpoint for receiving events
- **Webhook secret** - random string for signature verification

**Step 3.** Set permissions. Be as restrictive as possible:

- Repository permissions: Contents, Issues, Pull requests, Actions, etc.
- Organisation permissions: Members, Teams, etc.
- Account permissions: Email addresses, Profile, etc.

**Step 4.** Subscribe to events. Only subscribe to events you will handle.

**Step 5.** Set **Where can this GitHub App be installed?** to:

- **Only on this account** - for personal tools
- **Any account** - for public apps you want others to install

**Step 6.** Click **Create GitHub App**.

**Step 7.** On the next page, generate and download a private key. This `.pem` file is used to authenticate your app. Store it securely - treat it like a password.

---

## Authenticating as a GitHub App

GitHub Apps use a two-step authentication process:

**Step 1 - Generate a JWT signed with the private key:**

```python
import jwt
import time

APP_ID = "your-app-id"
PRIVATE_KEY = open("private-key.pem").read()

payload = {
    "iat": int(time.time()) - 60,      # issued 60 seconds ago
    "exp": int(time.time()) + (10 * 60),  # expires in 10 minutes
    "iss": APP_ID
}
jwt_token = jwt.encode(payload, PRIVATE_KEY, algorithm="RS256")
```

**Step 2 - Exchange the JWT for an installation access token:**

```python
import requests

headers = {
    "Authorization": f"Bearer {jwt_token}",
    "Accept": "application/vnd.github+json"
}

# Get the installation ID first
installations = requests.get(
    "https://api.github.com/app/installations",
    headers=headers
).json()
installation_id = installations[0]["id"]

# Get an installation token (valid for 1 hour)
token_response = requests.post(
    f"https://api.github.com/app/installations/{installation_id}/access_tokens",
    headers=headers
).json()
access_token = token_response["token"]

# Now use access_token as a Bearer token for API calls
```

For production apps, use the official Octokit `@octokit/auth-app` package which handles this automatically.

---

## OAuth Apps

OAuth Apps let users authorise your application to act on their behalf. When a user connects your app to GitHub, they grant it specific permissions and your app receives an OAuth token to make API calls as that user.

**When to use OAuth Apps:**

- Building a web application where users sign in with their GitHub account
- Your app needs to perform actions as the user (create repos, push code)
- You want users to control exactly what access they grant

**The OAuth flow:**

1. Redirect the user to `https://github.com/login/oauth/authorize?client_id=YOUR_CLIENT_ID&scope=repo,read:user`
2. User reviews the requested scopes and clicks Authorise
3. GitHub redirects back to your `callback_url` with a `code` parameter
4. Your server exchanges the code for an access token:

```bash
POST https://github.com/login/oauth/access_token
{
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "code": "CODE_FROM_CALLBACK"
}
```

5. Use the returned `access_token` to make API calls on behalf of the user

**Register an OAuth App:**

Go to `github.com/settings/developers` → **OAuth Apps** → **New OAuth App**.

---

## GitHub Sponsors

GitHub Sponsors lets open source maintainers receive recurring financial support directly through their GitHub profile.

**For sponsors (paying):**

1. Go to any developer's GitHub profile that has Sponsors enabled
2. Click the **Sponsor** button
3. Choose a sponsorship tier set by the developer (e.g. $1/month, $5/month, $50/month)
4. Enter payment details (credit card or PayPal)
5. The sponsorship is active immediately. You receive the **Public Sponsor** profile badge.

Sponsorships can also be made privately - only you and the maintainer know about it.

**For maintainers (receiving sponsorships):**

**Requirements to apply:**

- An active GitHub account with a history of open source contributions
- Your account must be at least 3 months old (typically)
- You must reside in a country where GitHub Sponsors payouts are supported

**Application process:**

1. Go to `github.com/sponsors` and click **Get sponsored**
2. Fill in your profile: a description of your work and why people should sponsor you
3. GitHub reviews your application. This usually takes a few days.
4. Once approved, set up sponsorship tiers - each tier has a monthly price and optional perks

**Sponsorship tiers - examples:**

| Tier name      | Price     | Perks                                        |
| -------------- | --------- | -------------------------------------------- |
| Coffee         | $1/month  | Thank you message                            |
| Supporter      | $5/month  | Listed in README supporters section          |
| Sustainer      | $15/month | Priority GitHub issue responses              |
| Team supporter | $50/month | Private Discord channel, monthly update call |

**Perks you can offer:**

- Named in README or website
- Access to a private repository (sponsor-only pre-releases)
- Discord role
- Early access to new features
- Monthly newsletter
- Code review credits

**Fees:**

GitHub takes 0% of sponsorship payments (as of March 2026). Stripe payment processing fees apply (typically ~2.9% + $0.30 per transaction). Payouts are made monthly via direct bank transfer (Stripe Connect).

**The Sponsor button on your profile:**

Once approved, a **Sponsor** button appears on your profile and on all your public repositories. You can also add a `FUNDING.yml` file to `.github/` to show the button on specific repositories:

```yaml
# .github/FUNDING.yml
github: isaacadjei
ko_fi: isaacadjei
patreon: isaacadjei
custom: ["https://buymeacoffee.com/isaacadjei"]
```

---

## GitHub Wikis

Every repository can have a Wiki - a set of Markdown pages for project documentation, notes and guides that sit alongside (but separate from) the repository code.

**When to use a Wiki vs docs in the repository:**

- Use a Wiki for documentation that non-developers might edit (project managers, writers, testers) because it has a simpler editing interface
- Use Markdown files in the repository (e.g. `docs/`) when documentation should be versioned alongside code and reviewed in pull requests
- Use GitHub Pages (from `docs/`) for published documentation websites

**Enable the Wiki:**

Go to **Settings → General → Features** and tick **Wikis**.

**Create a Wiki page:**

1. Go to your repository and click the **Wiki** tab
2. Click **Create the first page** (if no pages exist) or **New page**
3. Enter a page title - the title becomes the URL slug (spaces become hyphens)
4. Write content in Markdown - the editor supports standard GitHub Markdown
5. Write an optional edit message (like a commit message for the Wiki)
6. Click **Save page**

**Navigation - the Sidebar:**

Create a page named exactly `_Sidebar` to add a navigation panel on the left side of every Wiki page:

```markdown
## Navigation

- [Home](Home)
- [Getting Started](Getting-Started)
- [API Reference](API-Reference)
- [Contributing](Contributing)
- [FAQ](FAQ)
```

**Footer:**

Create a page named `_Footer` to add a footer to every Wiki page.

**Linking between Wiki pages:**

Use double brackets for internal links: `[[Page Name]]` or `[[Link text|Page Name]]`.

**Cloning and editing the Wiki locally:**

The Wiki is a separate Git repository. Clone it and push changes with standard Git commands:

```bash
git clone https://github.com/USERNAME/REPO.wiki.git
cd REPO.wiki
# Make changes to .md files
git add .
git commit -m "Update API reference"
git push
```

This is useful for bulk edits, restructuring or migrating documentation from another system.

**Wiki access control:**

By default, anyone can edit a public repository's Wiki (even non-contributors). To restrict editing to repository collaborators only, go to **Settings → General** and tick **Restrict editing to collaborators only** under Wikis.

---

## GitHub Gists

Gists are lightweight snippet repositories. Each Gist is a single file (or small collection of files) shared without creating a full repository. They are stored as Git repositories, so they have full version history.

**Create a Gist on the web:**

1. Go to `gist.github.com`
2. Optionally enter a description
3. Enter a filename including the extension (e.g. `parse_date.py`) - the extension determines syntax highlighting
4. Paste or type the content
5. Click **Create public gist** or **Create secret gist**

**Public vs secret Gists:**

- **Public** - indexed by search engines, appears in your public Gists list, discoverable by anyone
- **Secret** - not indexed, not listed publicly, but accessible to anyone who has the URL. Secret Gists are not private - they are just unlisted.

**Adding multiple files to a Gist:**

Click **Add file** below the first file editor. Each file can have a different language and syntax highlighting.

**Embedding a Gist in a web page:**

Each Gist has an embed code button. The embed renders the Gist with syntax highlighting in any web page:

```html
<script src="https://gist.github.com/isaacadjei/GIST_ID.js"></script>
```

**Forking a Gist:**

Any logged-in user can fork a public Gist, creating their own copy they can edit.

**Cloning a Gist as a Git repository:**

```bash
git clone https://gist.github.com/GIST_ID.git
cd GIST_ID
# Edit files, commit, push back to update the Gist
```

**Managing Gists via CLI:**

```bash
gh gist create parse_date.py --public --description "ISO 8601 date parser"
gh gist list
gh gist view GIST_ID
gh gist edit GIST_ID
gh gist clone GIST_ID
gh gist delete GIST_ID
```

---

## Advanced Search

GitHub's search is far more powerful than most users realise. It supports qualifiers that filter by language, dates, stars, file paths, author and more.

**Access the full search:** `github.com/search` or the search bar on any page.

**Code search qualifiers:**

```
language:python requests.get                    # Python files containing requests.get
repo:zaccesss/git-unlocked README             # in a specific repo
path:*.yml workflow_dispatch                    # in YAML files
path:src/ authentication                        # in the src directory
extension:json api_key                         # in JSON files
filename:Dockerfile                            # files named Dockerfile
size:>10000                                    # files larger than 10KB
```

**Issue and PR search qualifiers:**

```
is:issue is:open label:bug                     # open issues labelled bug
is:pr is:merged author:zaccesss              # merged PRs by a specific author
is:issue created:>2025-01-01                  # issues created after a date
is:pr reviewed-by:USERNAME base:main          # PRs reviewed by someone targeting main
is:issue no:assignee label:good-first-issue   # unassigned beginner issues
mentions:zaccesss is:open                    # open issues that mention a user
is:pr is:open draft:false review:required     # open PRs not in draft requiring review
linked:pr is:issue                            # issues linked to a PR
is:issue milestone:"v2.0"                     # issues in a specific milestone
```

**Repository search qualifiers:**

```
stars:>1000 language:python                   # Python repos with 1000+ stars
topic:machine-learning pushed:>2025-01-01     # ML repos active recently
org:microsoft language:typescript             # TypeScript repos in Microsoft org
is:public archived:false stars:10..500        # public, not archived, 10-500 stars
created:2024-01-01..2024-12-31                # repos created in 2024
license:mit                                   # MIT-licensed repos
```

**User and organisation search:**

```
type:user location:London followers:>100      # London users with 100+ followers
type:org members:>50                          # organisations with 50+ members
```

**Common qualifier reference:**

| Qualifier   | Example                    | Meaning                    |
| ----------- | -------------------------- | -------------------------- |
| `is:`       | `is:open`                  | State filter               |
| `in:title`  | `in:title login bug`       | Search in title only       |
| `in:body`   | `in:body deprecated`       | Search in body only        |
| `author:`   | `author:@me`               | Created by user            |
| `assignee:` | `assignee:@me`             | Assigned to user           |
| `label:`    | `label:"good first issue"` | Has this label             |
| `language:` | `language:rust`            | Repository language        |
| `stars:`    | `stars:100..500`           | Star count range           |
| `created:`  | `created:>2024-06-01`      | Created after date         |
| `updated:`  | `updated:<2023-01-01`      | Last updated before date   |
| `pushed:`   | `pushed:>2025-01-01`       | Last push date             |
| `org:`      | `org:microsoft`            | In a specific organisation |
| `repo:`     | `repo:OWNER/NAME`          | In a specific repository   |
| `user:`     | `user:zaccesss`            | Owned by a user            |
| `NOT`       | `python NOT django`        | Exclude a term             |
| `-label:`   | `-label:wontfix`           | Without this label         |

**Saving searches:**

GitHub does not have a built-in saved search feature. Bookmark the search results URL - all filters are encoded in it. Browser bookmarks or a notes app work well.

---

## Try It Yourself

**Exercise 1 - REST API:**

```bash
# Get your own profile (replace with your username)
curl https://api.github.com/users/YOUR_USERNAME | python3 -m json.tool

# Get a repository's open issues (no auth needed for public repos)
curl "https://api.github.com/repos/zaccesss/git-unlocked/issues?state=open" \
  | python3 -m json.tool
```

**Exercise 2 - Advanced search:**

1. Go to `github.com/search`
2. Search for: `is:issue is:open label:"good first issue" language:python stars:>100 no:assignee`
3. These are unassigned beginner-friendly issues in popular Python projects - real contribution opportunities

**Exercise 3 - GraphQL explorer:**

1. Go to `docs.github.com/en/graphql/overview/explorer`
2. Sign in with your GitHub account
3. Run this query:

```graphql
query {
  viewer {
    login
    repositories(first: 5, orderBy: { field: STARGAZERS, direction: DESC }) {
      nodes {
        name
        stargazerCount
        primaryLanguage {
          name
        }
      }
    }
  }
}
```

**Exercise 4 - Create a Gist:**

1. Go to `gist.github.com`
2. Create a public Gist with a useful script or code snippet you have written
3. Copy the embed URL from the Gist page
4. Note the Gist ID in the URL - you will use it with `gh gist view GIST_ID` to access it from the CLI

---

## Common Mistakes

**Committing tokens to Git.** A PAT pushed to a public repository is typically harvested by automated bots within 30 seconds. Always store tokens in environment variables or a secrets manager. GitHub's push protection detects many token formats automatically and will block the push.

**Creating OAuth Apps when GitHub Apps are more appropriate.** For anything running on a server, integrating with an organisation or used by multiple people, a GitHub App is the correct choice.

**Not verifying webhook signatures.** An unverified webhook endpoint can receive and process fake payloads from anyone who knows the URL. Always verify the `X-Hub-Signature-256` header.

**Parsing the request body before signature verification.** Some frameworks parse JSON before your code sees it, losing the raw bytes needed for HMAC verification. Use `express.raw()` in Express or read raw bytes before parsing.

**Setting non-expiring tokens.** Tokens with no expiry are a persistent security risk - if they are ever exposed, they remain valid indefinitely. Set an expiry and rotate tokens regularly.

**Using the REST API for GitHub Projects v2.** Projects v2 is GraphQL-only. If you are automating project management (adding items, updating fields, moving cards), you must use the GraphQL API.

**Using `per_page=30` (the default) for large datasets.** Always set `per_page=100` and paginate when you need complete data. The default of 30 items means your script silently misses most results.

---

## Summary

- The **REST API** (`api.github.com`) covers all GitHub resources; authenticated requests get 5000/hour
- **Fine-grained PATs** are preferred over classic PATs; scope them to specific repositories with minimum permissions and always set an expiry
- The **GraphQL API** is more efficient for complex or nested data; required for GitHub Projects v2
- **Webhooks** push event data to your server in real time; always verify the `X-Hub-Signature-256` signature header
- **GitHub Apps** are the right tool for production integrations - they have their own identity, higher rate limits and short-lived tokens
- **OAuth Apps** let users authorise your web application to act on their behalf
- **GitHub Sponsors** lets maintainers receive recurring funding; GitHub takes 0% of payments
- **Wikis** provide editable Markdown documentation pages per repository; they are a separate Git repository
- **Gists** are lightweight single-file snippets with full Git history; public Gists are indexed, secret Gists are unlisted but not private
- **Advanced search** qualifiers (`is:`, `label:`, `language:`, `stars:`, `org:`, `author:`) find exactly what you need across all of GitHub

---

## Sources and Further Reading

- [GitHub REST API documentation](https://docs.github.com/en/rest) - complete endpoint reference
- [GitHub GraphQL API documentation](https://docs.github.com/en/graphql) - schema, mutations and guides
- [GraphQL Explorer](https://docs.github.com/en/graphql/overview/explorer) - interactive query builder against your account
- [Webhooks documentation](https://docs.github.com/en/webhooks) - all events, payload formats and delivery details
- [GitHub Apps documentation](https://docs.github.com/en/apps/creating-github-apps) - creating and managing apps
- [OAuth Apps documentation](https://docs.github.com/en/apps/oauth-apps) - OAuth flow and token management
- [Octokit - JavaScript](https://github.com/octokit/octokit.js) - official GitHub SDK
- [Octokit - Python](https://github.com/PyGithub/PyGithub) - community Python SDK
- [GitHub Sponsors](https://github.com/sponsors) - apply to receive sponsorships
- [GitHub search syntax](https://docs.github.com/en/search-github/searching-on-github) - complete qualifier reference
- [smee.io](https://smee.io) - free webhook relay for local development

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

# GitHub Advanced Features

**Difficulty:** 🔴 Advanced | **Time:** 45 minutes

This file covers the powerful GitHub features that sit beyond everyday use: the REST and GraphQL APIs, webhooks, GitHub Apps, GitHub Sponsors, Wikis, Gists and advanced search syntax. These are the tools you reach for when building on top of GitHub rather than just using it.

---

## Table of Contents

- [GitHub REST API](#github-rest-api)
- [GitHub GraphQL API](#github-graphql-api)
- [Personal Access Tokens](#personal-access-tokens)
- [Webhooks](#webhooks)
- [GitHub Apps](#github-apps)
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

The GitHub REST API lets you interact with almost every GitHub feature programmatically. You can create repositories, open issues, manage pull requests, trigger workflows and retrieve data - all via HTTP requests.

**Base URL:** `https://api.github.com`

**Making a request (no authentication - public data):**

```bash
curl https://api.github.com/users/zaccessss
```

Returns a JSON object with the user's public profile data.

**Making an authenticated request:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.github.com/user
```

Returns your private account information including email addresses.

**Common endpoints:**

```
GET  /users/{username}                     User profile
GET  /repos/{owner}/{repo}                 Repository info
GET  /repos/{owner}/{repo}/issues          List issues
POST /repos/{owner}/{repo}/issues          Create an issue
GET  /repos/{owner}/{repo}/pulls           List pull requests
POST /repos/{owner}/{repo}/pulls           Create a pull request
GET  /repos/{owner}/{repo}/actions/runs    List workflow runs
POST /repos/{owner}/{repo}/actions/workflows/{id}/dispatches  Trigger a workflow
GET  /orgs/{org}/members                   List organisation members
```

**Example - create an issue with curl:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Bug: login fails on mobile", "body": "Steps to reproduce..."}' \
  https://api.github.com/repos/OWNER/REPO/issues
```

**Example - using the API with Python:**

```python
import requests

token = "YOUR_TOKEN"
headers = {"Authorization": f"Bearer {token}"}

# List open issues
response = requests.get(
    "https://api.github.com/repos/OWNER/REPO/issues",
    headers=headers,
    params={"state": "open"}
)
issues = response.json()
for issue in issues:
    print(issue["number"], issue["title"])
```

**Rate limits:**

- Unauthenticated requests: 60 per hour per IP
- Authenticated requests: 5000 per hour per token
- GitHub Actions GITHUB_TOKEN: 1000 per hour per repository

Check your remaining rate limit:

```bash
curl -I -H "Authorization: Bearer YOUR_TOKEN" https://api.github.com/rate_limit
```

**Pagination:**

Endpoints that return lists are paginated. Use `?page=2&per_page=100` and follow the `Link` header in the response for next/prev/last links.

---

## GitHub GraphQL API

The GraphQL API (`https://api.github.com/graphql`) allows you to request exactly the data you need in a single query, rather than making multiple REST calls and filtering the results.

**Example query - get a repository's open issues:**

```graphql
query {
  repository(owner: "zaccessss", name: "git-unlocked") {
    issues(states: OPEN, first: 10) {
      nodes {
        number
        title
        createdAt
        author {
          login
        }
      }
    }
  }
}
```

**Run with curl:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "{ viewer { login } }"}' \
  https://api.github.com/graphql
```

**Explorer:** Test GraphQL queries interactively at `docs.github.com/en/graphql/overview/explorer`. It has autocomplete and shows your real account data.

GraphQL is useful for:

- Fetching deeply nested data (issue → PR → review → comment) in one request
- Projects API (GitHub Projects v2 is GraphQL-only)
- Reducing API calls in scripts and tools that aggregate data

---

## Personal Access Tokens

Personal Access Tokens (PATs) authenticate API requests and CLI operations on your behalf.

**Types:**

- **Classic PAT** - broad scopes, older format, still widely supported
- **Fine-grained PAT** - granular per-repository permissions, newer and preferred

**Creating a fine-grained PAT:**

1. Go to `github.com/settings/tokens`
2. Click **Generate new token → Fine-grained token**
3. Set a name and expiration date (set an expiry - tokens with no expiry are a security risk)
4. Under **Repository access**, choose which repositories this token can access
5. Under **Permissions**, select only what this token needs:
   - Issues: Read and Write (if you need to create/close issues)
   - Contents: Read (if you need to read code)
   - Pull requests: Read and Write (if you need to open/merge PRs)
6. Click **Generate token**
7. Copy the token immediately - GitHub shows it only once

**Storing tokens securely:**

Never hardcode a token in your code. Store it in:

- An environment variable: `export GITHUB_TOKEN=ghp_xxx` (Mac/Linux) or `$env:GITHUB_TOKEN="ghp_xxx"` (Windows)
- A `.env` file (always add `.env` to `.gitignore`)
- A secrets manager (1Password, AWS Secrets Manager, etc.)
- A GitHub Actions secret (for CI workflows)

---

## Webhooks

Webhooks allow GitHub to send an HTTP POST request to a URL you specify whenever a specific event happens. Instead of polling the API, GitHub pushes data to you.

**Use cases:**

- Post a Slack message when a PR is merged
- Deploy code when a push happens to `main`
- Create a Jira ticket when a GitHub issue is opened
- Update a dashboard when an Actions workflow completes

**Creating a repository webhook:**

1. Go to your repository → **Settings → Webhooks**
2. Click **Add webhook**
3. Enter the **Payload URL** - the endpoint on your server that will receive the POST requests
4. Set **Content type** to `application/json`
5. Enter a **Secret** - a random string you use to verify the request came from GitHub
6. Select which events trigger the webhook (or **Send me everything**)
7. Click **Add webhook**

**Webhook payload example (push event):**

```json
{
  "ref": "refs/heads/main",
  "commits": [
    {
      "id": "abc123",
      "message": "fix: correct login bug",
      "author": { "name": "Isaac Adjei", "email": "isaac@example.com" }
    }
  ],
  "repository": {
    "full_name": "zaccessss/git-unlocked"
  }
}
```

**Verifying webhook signatures:**

GitHub signs every webhook payload with your secret using HMAC-SHA256. Verify it in your server code:

```python
import hmac
import hashlib

def verify_signature(payload_body, secret, signature_header):
    expected = hmac.new(
        secret.encode(),
        payload_body,
        hashlib.sha256
    ).hexdigest()
    return hmac.compare_digest(f"sha256={expected}", signature_header)
```

Always verify signatures. Without verification, anyone who knows your webhook URL can send fake payloads.

**Testing webhooks locally:**

Use `smee.io` (a free webhook relay) or `ngrok` to expose your local server to the internet during development so GitHub can reach it.

```bash
# Using smee
npm install -g smee-client
smee --url YOUR_SMEE_URL --target http://localhost:3000/webhook
```

---

## GitHub Apps

GitHub Apps are the recommended way to build integrations that act on behalf of an organisation or repository rather than as a specific user. Unlike OAuth Apps (which act as a user), GitHub Apps have their own identity.

**GitHub Apps vs Personal Access Tokens:**

| | PAT | GitHub App |
|---|---|---|
| Acts as | A specific user | The app itself |
| Scopes | User-wide | Per-repository |
| Rate limit | 5000/hour | 5000-15000/hour |
| Best for | Personal scripts | Production integrations |

**Installing an existing GitHub App:**

1. Find the app (GitHub Marketplace or a direct link)
2. Click **Install**
3. Choose which repositories it can access
4. Review and approve the permissions

Many common tools are GitHub Apps: Dependabot, CodeClimate, CircleCI, Vercel, Netlify, Linear, Sentry and many more.

**Creating your own GitHub App:**

1. Go to `github.com/settings/apps`
2. Click **New GitHub App**
3. Fill in the name, homepage URL and webhook URL
4. Select permissions (read-only, read-write per resource)
5. Select which events to subscribe to
6. Click **Create GitHub App**
7. Download the private key generated during setup
8. Install the app on your account or organisation

GitHub Apps authenticate using JWT signed with the private key, then exchange it for an installation token per repository. This is more complex than a PAT but gives fine-grained, auditable, app-level access.

---

## GitHub Sponsors

GitHub Sponsors lets open source maintainers receive recurring financial support directly from their GitHub profile.

**For sponsors (paying):**

1. Go to any developer's profile that has Sponsors enabled
2. Click **Sponsor**
3. Choose a tier (monthly amounts set by the developer)
4. Enter payment details
5. The sponsorship is active immediately

Sponsoring also earns you the **Public Sponsor** profile badge.

**For maintainers (receiving):**

1. Apply at `github.com/sponsors` - click **Get sponsored**
2. GitHub reviews your application (requires an active open source presence)
3. Once approved, set up sponsorship tiers (e.g. $1, $5, $10, $50 per month)
4. Connect a Stripe account for payouts
5. A **Sponsor** button appears on your profile and repositories

**Perks for sponsors:**

Maintainers can configure custom rewards per tier: early access to releases, Discord roles, listed in README, private repository access and so on.

**GitHub's fee:**

GitHub takes 0% of sponsorship payments (as of March 2026). Payment processor fees (Stripe) still apply.

---

## GitHub Wikis

Every repository can have a Wiki - a set of Markdown pages for documentation, project notes or anything that does not belong in the repository files themselves.

**Enable the Wiki:**

Go to **Settings → General** and tick **Wikis**.

**Create a Wiki page:**

1. Go to your repository and click the **Wiki** tab
2. Click **Create the first page** or **New page**
3. Write content in Markdown
4. Click **Save page**

**The sidebar and footer:**

Create pages named `_Sidebar` and `_Footer` to add a navigation sidebar and a footer that appears on every Wiki page.

**Cloning the Wiki:**

The Wiki is actually a separate Git repository. You can clone and edit it locally:

```bash
git clone https://github.com/USERNAME/REPO.wiki.git
```

Push changes back with `git push` as normal.

**Limitations:**

Wikis are better for prose documentation than for code documentation. For code-heavy documentation, consider keeping docs inside the repository as Markdown files and using GitHub Pages to publish them.

---

## GitHub Gists

Gists are lightweight snippet repositories. They are single files (or small collections) you want to share without creating a full repository.

**Create a Gist:**

1. Go to `gist.github.com`
2. Enter a description
3. Enter a file name (include the extension so syntax highlighting works - e.g. `script.py`)
4. Paste the content
5. Click **Create public gist** or **Create secret gist**

Secret gists are not indexed by search engines but are accessible to anyone with the URL. They are not truly private.

**What Gists are good for:**

- Sharing a code snippet in a chat or blog post
- Saving a useful script you might need again
- Quick one-off configuration files
- Sharing output of a command for debugging

**Embed a Gist in a web page:**

Each Gist has an embed link:

```html
<script src="https://gist.github.com/USERNAME/GIST_ID.js"></script>
```

**Gist via CLI:**

```bash
gh gist create myfile.py --public --description "Useful Python helper"
gh gist list
gh gist view GIST_ID
```

---

## Advanced Search

GitHub's search is more powerful than most people realise. Use qualifiers to filter precisely.

**Search across GitHub:**

Use the search bar at the top of any GitHub page or go to `github.com/search`.

**Code search:**

```
language:python requests.get
repo:zaccessss/git-unlocked README
path:*.yml workflow_dispatch
extension:json "api_key"
```

**Issue and PR search:**

```
is:issue is:open label:bug
is:pr is:merged author:zaccessss
is:issue created:>2025-01-01
is:pr reviewed-by:username base:main
is:issue no:assignee
mentions:zaccessss
```

**Repository search:**

```
stars:>1000 language:python
topic:machine-learning pushed:>2025-06-01
org:microsoft language:typescript
```

**User search:**

```
type:user location:London followers:>100
```

**Common qualifiers:**

| Qualifier | Example | Meaning |
|---|---|---|
| `is:` | `is:open` | State filter |
| `in:` | `in:title bug` | Search in title/body/comments |
| `author:` | `author:zaccessss` | Created by user |
| `assignee:` | `assignee:@me` | Assigned to user |
| `label:` | `label:good-first-issue` | Has label |
| `language:` | `language:rust` | Repository language |
| `stars:` | `stars:>500` | Star count range |
| `created:` | `created:>2024-01-01` | Creation date range |
| `pushed:` | `pushed:<2023-12-31` | Last push date |
| `org:` | `org:microsoft` | Within organisation |
| `repo:` | `repo:OWNER/NAME` | Specific repository |
| `NOT` | `python NOT django` | Exclude term |

**Save a search:**

There is no built-in saved search on GitHub.com, but you can bookmark the search result URL - all filters are encoded in the URL.

---

## Try It Yourself

**Exercise 1 - REST API:**

```bash
# Get your own user info (no auth required for public data)
curl https://api.github.com/users/YOUR_USERNAME

# Get the git-unlocked repository info
curl https://api.github.com/repos/zaccessss/git-unlocked
```

**Exercise 2 - Advanced search:**

1. Go to `github.com/search`
2. Search for: `is:issue is:open label:good-first-issue language:python stars:>100`
3. Browse the results - these are open beginner-friendly issues in popular Python projects
4. Pick one that interests you

**Exercise 3 - Create a Gist:**

1. Go to `gist.github.com`
2. Create a public Gist with a Python or JavaScript snippet you have written recently
3. Copy the embed code and note the Gist URL

---

## Common Mistakes

**Committing tokens to Git.** A PAT in a public repository is immediately harvested by automated bots. It will be compromised within minutes. Store tokens in environment variables or secrets managers only.

**Creating OAuth Apps when you should use GitHub Apps.** For anything that accesses organisation resources or will be used by others, a GitHub App is the correct approach.

**Not verifying webhook signatures.** Any unverified webhook endpoint can receive and process fake payloads.

**Using the REST API when GraphQL is more efficient.** Fetching a repository's issues, their labels, their assignees and their linked PRs with the REST API takes 4-5 requests. With GraphQL it is one.

**Forgetting to set a token expiry.** Non-expiring tokens are a persistent security risk. Set an expiry and rotate tokens regularly.

---

## Summary

- The **REST API** (`api.github.com`) lets you programmatically manage every GitHub resource; authenticated requests get 5000/hour
- The **GraphQL API** lets you fetch nested data in one query; required for Projects v2
- **Fine-grained PATs** are the preferred authentication method for personal scripts; scope them narrowly and set an expiry
- **Webhooks** push event data to your server in real time - verify signatures with HMAC-SHA256
- **GitHub Apps** are the right tool for production integrations that act on behalf of organisations
- **GitHub Sponsors** lets maintainers receive funding; GitHub takes 0% of payments
- **Wikis** provide separate Markdown documentation pages per repository; they are a separate Git repo under the hood
- **Gists** are lightweight single-file snippets for sharing code without a full repository
- **Advanced search** with qualifiers (`is:`, `label:`, `language:`, `stars:`) finds exactly what you need across all of GitHub

---

## Sources and Further Reading

- [GitHub REST API documentation](https://docs.github.com/en/rest) - complete endpoint reference
- [GitHub GraphQL API documentation](https://docs.github.com/en/graphql) - schema, explorer and guides
- [GitHub GraphQL Explorer](https://docs.github.com/en/graphql/overview/explorer) - interactive query builder
- [Webhooks documentation](https://docs.github.com/en/webhooks) - events, payloads and verification
- [GitHub Apps documentation](https://docs.github.com/en/apps) - creating and managing apps
- [GitHub Sponsors](https://github.com/sponsors) - apply to receive sponsorships
- [GitHub Gists](https://gist.github.com) - create and manage gists
- [GitHub search syntax](https://docs.github.com/en/search-github/searching-on-github) - complete qualifier reference
- [Octokit](https://github.com/octokit) - official GitHub API client libraries for JavaScript, Ruby and .NET

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

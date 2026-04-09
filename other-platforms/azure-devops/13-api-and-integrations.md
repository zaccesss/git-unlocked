# Azure DevOps REST API and Integrations

**Difficulty:** 🔴 Advanced | **Time:** 40 minutes

Every action available in the Azure DevOps web interface is also available via the REST API. Creating work items, triggering pipelines, posting build status, managing repositories, querying test results - all of it is programmable. The Azure DevOps REST API is the foundation for custom tooling, external integrations, automation scripts and the Azure DevOps CLI itself.

This file covers the REST API structure, authentication, common request patterns, service hooks for event-driven integrations, GitHub integration and service connections for external system authentication.

---

## Table of Contents

1. [REST API overview](#rest-api-overview)
2. [Authentication](#authentication)
3. [API structure and conventions](#api-structure-and-conventions)
4. [Working with work items](#working-with-work-items)
5. [Working with repositories and pull requests](#working-with-repositories-and-pull-requests)
6. [Build and pipeline API](#build-and-pipeline-api)
7. [Posting build status to commits](#posting-build-status-to-commits)
8. [Service hooks](#service-hooks)
9. [GitHub integration](#github-integration)
10. [Service connections](#service-connections)
11. [Rate limits and pagination](#rate-limits-and-pagination)
12. [Client libraries](#client-libraries)
13. [Try It Yourself](#try-it-yourself)
14. [Common Mistakes](#common-mistakes)
15. [Summary](#summary)
16. [Sources](#sources)

---

## REST API overview

The Azure DevOps REST API follows consistent conventions across all services. Understanding the pattern once lets you navigate the entire API.

**Base URL patterns:**

For Azure DevOps Services (cloud):
```
https://dev.azure.com/{organisation}/{project}/_apis/{area}/{resource}?api-version={version}
```

For organisation-level (no project scope):
```
https://dev.azure.com/{organisation}/_apis/{area}/{resource}?api-version={version}
```

For Azure DevOps Server (on-premises):
```
https://{server}/{collection}/{project}/_apis/{area}/{resource}?api-version={version}
```

**API versioning:**

Every request must include an `api-version` parameter. Current API version is `7.2` (or `7.2-preview.N` for preview endpoints). Specifying a version ensures your integration does not break when Microsoft releases new API versions.

```bash
# Always include api-version
curl "https://dev.azure.com/org/project/_apis/git/repositories?api-version=7.2"
```

---

## Authentication

### Personal Access Token (PAT)

PATs are the most common authentication method. They are passed as HTTP Basic Authentication with an empty username and the PAT as the password.

```bash
# Using curl with Basic Auth (empty username, PAT as password)
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/your-org/your-project/_apis/git/repositories?api-version=7.2"

# Or using the Authorization header directly
TOKEN=$(echo -n ":YOUR_PAT" | base64)
curl -H "Authorization: Basic $TOKEN" \
  "https://dev.azure.com/your-org/your-project/_apis/git/repositories?api-version=7.2"
```

In Python:
```python
import requests
from requests.auth import HTTPBasicAuth

PAT = 'your-pat'
ORG = 'your-org'
PROJECT = 'your-project'

auth = HTTPBasicAuth('', PAT)  # empty username, PAT as password

response = requests.get(
    f'https://dev.azure.com/{ORG}/{PROJECT}/_apis/git/repositories',
    auth=auth,
    params={'api-version': '7.2'}
)
print(response.json())
```

### Bearer token (OAuth / Azure AD)

For applications authenticating on behalf of users, obtain an Azure AD token and use it as a Bearer token:

```bash
curl -H "Authorization: Bearer {access_token}" \
  "https://dev.azure.com/your-org/_apis/projects?api-version=7.2"
```

Obtain the token via the OAuth 2.0 authorization code flow against Microsoft's identity platform.

---

## API structure and conventions

### HTTP methods

| Method | Use |
|---|---|
| `GET` | Retrieve resources |
| `POST` | Create resources or trigger actions |
| `PUT` | Replace a resource entirely |
| `PATCH` | Update part of a resource |
| `DELETE` | Remove a resource |

### Response format

All responses are JSON. Successful responses return 2xx status codes. List responses include a `value` array and a `count`:

```json
{
  "count": 3,
  "value": [
    { "id": "...", "name": "repo-1" },
    { "id": "...", "name": "repo-2" },
    { "id": "...", "name": "repo-3" }
  ]
}
```

### Error responses

Errors return 4xx or 5xx with a JSON body:

```json
{
  "id": "...",
  "innerException": null,
  "message": "The value 'nonexistent-repo' is not valid for field 'repositoryId'.",
  "typeName": "Microsoft.TeamFoundation.Git.Server.GitRepositoryNotFoundException",
  "typeKey": "GitRepositoryNotFoundException",
  "errorCode": 0,
  "eventId": 3000
}
```

---

## Working with work items

### Get a work item

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/wit/workitems/123?api-version=7.2"
```

### Get multiple work items

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/wit/workitems?ids=1,2,3,4,5&api-version=7.2"
```

### Create a work item

Work item creation uses a JSON Patch document format - an array of `add` operations:

```bash
curl -X POST \
  -H "Content-Type: application/json-patch+json" \
  -u ":YOUR_PAT" \
  -d '[
    { "op": "add", "path": "/fields/System.Title", "value": "New bug: login fails" },
    { "op": "add", "path": "/fields/System.WorkItemType", "value": "Bug" },
    { "op": "add", "path": "/fields/System.State", "value": "New" },
    { "op": "add", "path": "/fields/System.AssignedTo", "value": "alice@example.com" },
    { "op": "add", "path": "/fields/System.Tags", "value": "regression; critical" },
    { "op": "add", "path": "/fields/System.Description", "value": "Login button shows spinner but does not navigate" }
  ]' \
  "https://dev.azure.com/org/project/_apis/wit/workitems/\$Bug?api-version=7.2"
```

The work item type is specified in the URL path (`$Bug`, `$User Story`, `$Task`).

### Update a work item

```bash
curl -X PATCH \
  -H "Content-Type: application/json-patch+json" \
  -u ":YOUR_PAT" \
  -d '[
    { "op": "replace", "path": "/fields/System.State", "value": "Active" },
    { "op": "replace", "path": "/fields/System.AssignedTo", "value": "bob@example.com" }
  ]' \
  "https://dev.azure.com/org/project/_apis/wit/workitems/123?api-version=7.2"
```

### Run a WIQL query

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u ":YOUR_PAT" \
  -d '{
    "query": "SELECT [System.Id], [System.Title], [System.State] FROM WorkItems WHERE [System.TeamProject] = @project AND [System.AssignedTo] = @me AND [System.State] <> \"Closed\" ORDER BY [System.ChangedDate] DESC"
  }' \
  "https://dev.azure.com/org/project/_apis/wit/wiql?api-version=7.2"
```

The response contains work item references (IDs). To get the full items, make a second request with the IDs.

---

## Working with repositories and pull requests

### List repositories

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/git/repositories?api-version=7.2"
```

### Get a repository

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo?api-version=7.2"
```

### Get commits

```bash
# Commits on a branch
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/commits?searchCriteria.itemVersion.version=main&api-version=7.2"
```

### Create a pull request

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u ":YOUR_PAT" \
  -d '{
    "title": "Add user authentication",
    "description": "Implements JWT-based auth for the API. Closes AB#123.",
    "sourceRefName": "refs/heads/feature/user-auth",
    "targetRefName": "refs/heads/main",
    "reviewers": [
      { "id": "REVIEWER-USER-UUID" }
    ],
    "workItemRefs": [
      { "id": "123" }
    ]
  }' \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/pullrequests?api-version=7.2"
```

### List pull requests

```bash
# All open PRs
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/pullrequests?searchCriteria.status=active&api-version=7.2"
```

### Update a pull request (approve)

```bash
# Get the current user's ID first
MY_ID=$(curl -s -u ":YOUR_PAT" "https://dev.azure.com/org/_apis/connectionData?api-version=7.2" | python3 -c "import sys,json; print(json.load(sys.stdin)['authenticatedUser']['id'])")

# Post an approval vote
curl -X PUT \
  -H "Content-Type: application/json" \
  -u ":YOUR_PAT" \
  -d '{ "vote": 10 }' \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/pullrequests/42/reviewers/$MY_ID?api-version=7.2"
```

Vote values: 10 = Approve, 5 = Approve with suggestions, 0 = No vote, -5 = Waiting for author, -10 = Reject.

---

## Build and pipeline API

### List pipeline definitions

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/build/definitions?api-version=7.2"
```

### Trigger a pipeline run

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u ":YOUR_PAT" \
  -d '{
    "definition": { "id": 5 },
    "sourceBranch": "refs/heads/main",
    "parameters": "{\"targetEnvironment\": \"staging\", \"releaseVersion\": \"1.2.3\"}"
  }' \
  "https://dev.azure.com/org/project/_apis/build/builds?api-version=7.2"
```

### Get a pipeline run

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/build/builds/BUILD-ID?api-version=7.2"
```

### List recent runs

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/build/builds?definitions=5&resultFilter=failed&top=10&api-version=7.2"
```

### Get build logs

```bash
# List log entries
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/build/builds/BUILD-ID/logs?api-version=7.2"

# Get specific log content
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/build/builds/BUILD-ID/logs/LOG-ID?api-version=7.2"
```

---

## Posting build status to commits

External CI/CD systems post build status to Azure DevOps commits so the result appears on PRs and in the Repos commit list.

### Post a build status

```bash
curl -X POST \
  -H "Content-Type: application/json" \
  -u ":YOUR_PAT" \
  -d '{
    "state": "succeeded",
    "description": "Build 142 succeeded - all 342 tests passed",
    "context": {
      "name": "jenkins-build",
      "genre": "continuous-integration"
    },
    "targetUrl": "https://jenkins.yourcompany.com/job/my-app/142/"
  }' \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/commits/COMMIT-SHA/statuses?api-version=7.2"
```

**State values**: `pending`, `succeeded`, `failed`, `error`, `notApplicable`, `notSet`.

**Context**: A unique identifier for this CI system. Multiple systems can post different statuses to the same commit. The context `genre` and `name` together identify the status uniquely.

### Using commit status for branch policy

After posting a status, configure a branch policy status check that requires `jenkins-build` in the `continuous-integration` genre to have `succeeded` state. The branch policy blocks PR completion until Jenkins posts a success status.

This is the integration pattern for external CI systems: Jenkins (or CircleCI, Travis, custom build systems) posts status via the API, and Azure Repos' branch policies enforce it.

---

## Service hooks

Service hooks send event notifications from Azure DevOps to external systems. When a PR is created, a build completes, a work item is updated or code is pushed, Azure DevOps sends an HTTP POST to your configured endpoint.

### Creating a service hook subscription

1. Go to **Project settings** > **Service hooks**
2. Click **+** (Create subscription)
3. Choose the consumer (e.g. **Web Hooks** for generic HTTP endpoints, or a specific integration like **Slack**, **Teams**)
4. Select the trigger event
5. Configure filters (optionally limit to specific repositories, branches or work item types)
6. Enter the webhook URL and authentication
7. Test and save

### Service hook events

**Code / Repos events:**
- `git.push` - code is pushed to any branch
- `git.pullrequest.created` - PR opened
- `git.pullrequest.updated` - PR updated (new commits, status changes)
- `git.pullrequest.merged` - PR completed/merged
- `git.pullrequest.commented` - comment added to PR
- `tfvc.checkin` - TFVC changeset checked in

**Build / Pipeline events:**
- `build.complete` - a build finishes (pass or fail)
- `ms.vss-release.release-created-event` - release created
- `ms.vss-release.deployment-approval-pending-event` - deployment awaiting approval
- `ms.vss-release.deployment-completed-event` - deployment completed

**Work item events:**
- `workitem.created` - new work item created
- `workitem.updated` - work item updated
- `workitem.commented` - comment added to work item
- `workitem.deleted` - work item deleted
- `workitem.restored` - deleted work item restored

### Example: Sending a Slack notification when a build fails

1. Create a Slack incoming webhook URL from your Slack workspace
2. In Azure DevOps service hooks, create a subscription:
   - Event: **Build completed**
   - Filter: **Result = Failed**
   - Consumer: **Web Hooks**
   - URL: your Slack webhook URL
   - Headers: none required for Slack
   - Resource details to send: **All**

When a build fails, Azure DevOps POSTs the build details to Slack's webhook URL, and Slack displays the notification in the configured channel.

### Service hook payload example

For a `git.pullrequest.created` event:

```json
{
  "subscriptionId": "...",
  "eventType": "git.pullrequest.created",
  "publisherId": "tfs",
  "resource": {
    "pullRequestId": 42,
    "title": "Add user authentication",
    "status": "active",
    "createdBy": {
      "displayName": "Alice Developer",
      "uniqueName": "alice@example.com"
    },
    "sourceRefName": "refs/heads/feature/user-auth",
    "targetRefName": "refs/heads/main",
    "url": "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/pullRequests/42",
    "_links": {
      "web": {
        "href": "https://dev.azure.com/org/project/_git/my-repo/pullrequest/42"
      }
    }
  }
}
```

### Service hook security

For web hook endpoints, add a secret token. Azure DevOps includes the token in the `X-Azure-DevOps-Signature` header as an HMAC-SHA256 hash of the payload. Verify the signature in your endpoint:

```python
import hmac
import hashlib
from flask import Flask, request, abort

app = Flask(__name__)
SECRET = 'your-webhook-secret'

@app.route('/webhook', methods=['POST'])
def handle_webhook():
    signature = request.headers.get('X-Azure-DevOps-Signature', '')
    expected = 'sha256=' + hmac.new(
        SECRET.encode(),
        request.data,
        hashlib.sha256
    ).hexdigest()

    if not hmac.compare_digest(signature, expected):
        abort(403)

    payload = request.json
    event = payload['eventType']

    if event == 'git.pullrequest.created':
        print(f"New PR: {payload['resource']['title']}")

    return '', 200
```

---

## GitHub integration

Azure DevOps integrates with GitHub repositories, allowing GitHub-hosted code to trigger Azure Pipelines while keeping Azure Boards and Artifacts on Azure DevOps.

### Connecting GitHub to Azure Pipelines

1. In your Azure DevOps project, go to **Pipelines** > **New Pipeline**
2. Select **GitHub** as the code location
3. Authenticate with GitHub (OAuth or GitHub App)
4. Select the GitHub repository
5. Azure DevOps installs the Azure Pipelines GitHub App on the repository
6. Configure the pipeline YAML

Once connected, pushing to the GitHub repository triggers Azure Pipelines. Build status appears on GitHub pull requests as a status check.

### GitHub service connection

The GitHub connection is stored as a service connection in Project settings:

1. Go to **Project settings** > **Service connections**
2. Click **New service connection** > **GitHub**
3. Authenticate via OAuth or Personal Access Token
4. Name the connection

Service connections for GitHub can be scoped:
- **OAuth App**: grants access to repositories you authorise
- **GitHub App**: more secure, installed at the repository or organisation level in GitHub

### GitHub Advanced Security for Azure DevOps (GHAzDO)

Microsoft offers GitHub Advanced Security features (secret scanning, code scanning via CodeQL, dependency vulnerability alerts) for Azure Repos repositories:

1. Go to **Project settings** > **Repositories** > select a repository
2. Click **Advanced Security** > **Enable Advanced Security**

This requires a separate GHAzDO licence per active committer. It provides the same secret scanning and CodeQL SAST that GitHub repos get with GHAS, applied to Azure Repos.

> [!NOTE]
> GHAzDO does **not** include Copilot Autofix (AI-generated fix suggestions). That feature is GitHub-only. GHAzDO gives you the alerts without the AI-powered remediation.

### Azure Boards and GitHub

Linking Azure Boards work items to GitHub commits and pull requests:

1. Go to **Organisation settings** > **GitHub connections**
2. Click **New connection**
3. Connect to a GitHub organisation or specific repositories
4. Once connected, reference work items in GitHub with `AB#ID` in commit messages and PR descriptions

GitHub pull requests show Azure Boards work item links in the PR description, and Azure Boards work items show GitHub commit and PR links in the Development section.

---

## Service connections

Service connections securely store credentials for Azure Pipelines to authenticate with external systems. They prevent secrets from appearing in pipeline YAML.

### Available service connection types

| Type | For |
|---|---|
| Azure Resource Manager | Azure subscriptions, resource groups, resources |
| Docker Registry | Docker Hub, Azure Container Registry, private registries |
| GitHub | GitHub repositories |
| Generic | Any HTTP service with username/password |
| Kubernetes | Kubernetes clusters (AKS or generic) |
| Maven | Maven repositories |
| npm | npm registries |
| NuGet | NuGet feeds |
| SSH | SSH connections to servers |
| Service Fabric | Azure Service Fabric clusters |
| Subversion | SVN repositories |
| Visual Studio App Center | App Center distribution |

### Creating an Azure Resource Manager connection

The most common service connection for teams deploying to Azure:

1. Go to **Project settings** > **Service connections**
2. Click **New service connection** > **Azure Resource Manager**
3. Choose authentication type:
   - **Workload identity federation** (recommended) - secretless, uses OIDC
   - **Service principal (manual)** - client ID and secret
   - **Service principal (automatic)** - Azure DevOps creates the SP in your Azure AD
4. Select subscription and optionally a resource group (to scope access)
5. Name the connection
6. Click **Save**

### Workload identity federation (secretless)

Modern service connections support workload identity federation. Instead of a client secret (which expires and must be rotated), the pipeline authenticates using a short-lived OIDC token issued by Azure DevOps.

Benefits:
- No credential rotation required
- Token is short-lived (minutes, not months)
- Cannot be stolen and reused outside the pipeline context

When creating an Azure Resource Manager service connection, select **Workload identity federation** as the authentication method. Azure DevOps configures the trust relationship in Azure AD automatically.

### Using service connections in pipelines

```yaml
# Azure deployment
- task: AzureWebApp@1
  inputs:
    azureSubscription: 'MyAzureProductionConnection'  # service connection name
    appName: 'my-web-app'
    package: $(Build.ArtifactStagingDirectory)/*.zip

# Docker push
- task: Docker@2
  inputs:
    containerRegistry: 'MyDockerHubConnection'
    repository: 'myorg/myapp'
    command: push
    tags: $(Build.BuildNumber)

# Deploy to Kubernetes
- task: KubernetesManifest@1
  inputs:
    action: deploy
    kubernetesServiceConnection: 'MyAKSClusterConnection'
    namespace: production
    manifests: k8s/deployment.yaml
```

### Service connection security

Control which pipelines can use a service connection:

1. Go to **Project settings** > **Service connections**
2. Select the connection
3. Click **Security**
4. Under **Pipeline permissions**, either:
   - **Allow all pipelines** (default) - any pipeline can use this connection
   - **Restrict** - only specific pipelines can use it

For production deployment connections, restricting to the specific release pipeline is a security best practice. This prevents a developer's branch pipeline from accidentally (or maliciously) deploying to production.

---

## Rate limits and pagination

### Rate limits

Azure DevOps Services has rate limits to prevent abuse:

| Limit | Value |
|---|---|
| REST API requests | 200 requests per 5 seconds per user |
| Maximum response size | 4 MB |
| WIQL query result size | 20,000 work items |

When rate limited, the API returns `429 Too Many Requests` with a `Retry-After` header.

```python
import time
import requests

def api_get(url, auth, **kwargs):
    while True:
        response = requests.get(url, auth=auth, **kwargs)
        if response.status_code == 429:
            retry_after = int(response.headers.get('Retry-After', 5))
            time.sleep(retry_after)
            continue
        return response
```

### Pagination

List endpoints return paginated results. By default, most endpoints return 100 items. Use `$top` and `$skip` (or `continuationToken` for some endpoints) to page through results:

```bash
# Get page 1 (first 100)
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/commits?$top=100&$skip=0&api-version=7.2"

# Get page 2 (next 100)
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/org/project/_apis/git/repositories/my-repo/commits?$top=100&$skip=100&api-version=7.2"
```

Some newer endpoints use continuation tokens (returned in the `x-ms-continuationtoken` response header):

```python
def get_all_results(url, auth):
    all_results = []
    continuation_token = None

    while True:
        params = {'api-version': '7.2'}
        if continuation_token:
            params['continuationToken'] = continuation_token

        response = requests.get(url, auth=auth, params=params)
        data = response.json()
        all_results.extend(data.get('value', []))

        continuation_token = response.headers.get('x-ms-continuationtoken')
        if not continuation_token:
            break

    return all_results
```

---

## Client libraries

Microsoft provides official client libraries for common languages.

### .NET (NuGet)

```bash
dotnet add package Microsoft.TeamFoundationServer.Client
dotnet add package Microsoft.VisualStudio.Services.Client
```

```csharp
using Microsoft.TeamFoundation.WorkItemTracking.WebApi;
using Microsoft.VisualStudio.Services.Common;
using Microsoft.VisualStudio.Services.WebApi;

var orgUrl = new Uri("https://dev.azure.com/your-org");
var credentials = new VssBasicCredential("", "YOUR_PAT");
var connection = new VssConnection(orgUrl, credentials);

var witClient = connection.GetClient<WorkItemTrackingHttpClient>();
var workItem = await witClient.GetWorkItemAsync("your-project", 123);
Console.WriteLine(workItem.Fields["System.Title"]);
```

### Python (pip)

```bash
pip install azure-devops
```

```python
from azure.devops.connection import Connection
from msrest.authentication import BasicAuthentication

PAT = 'your-pat'
ORG_URL = 'https://dev.azure.com/your-org'

credentials = BasicAuthentication('', PAT)
connection = Connection(base_url=ORG_URL, creds=credentials)

wit_client = connection.clients.get_work_item_tracking_client()
work_item = wit_client.get_work_item(123, project='your-project')
print(work_item.fields['System.Title'])

# Get all open work items in a project
from azure.devops.v7_1.work_item_tracking.models import Wiql

wiql = Wiql(query="""
SELECT [System.Id], [System.Title]
FROM WorkItems
WHERE [System.State] = 'Active'
""")
result = wit_client.query_by_wiql(wiql, project='your-project')
ids = [item.id for item in result.work_items]
items = wit_client.get_work_items(ids)
for item in items:
    print(item.fields['System.Title'])
```

### JavaScript/TypeScript (npm)

```bash
npm install azure-devops-node-api
```

```typescript
import * as nodeApi from 'azure-devops-node-api';

const orgUrl = 'https://dev.azure.com/your-org';
const token = 'your-pat';

const authHandler = nodeApi.getPersonalAccessTokenHandler(token);
const connection = new nodeApi.WebApi(orgUrl, authHandler);

const witApi = await connection.getWorkItemTrackingApi();
const workItem = await witApi.getWorkItem(123);
console.log(workItem.fields['System.Title']);
```

---

## Try It Yourself

**Exercise 1 - Make your first API call**

```bash
# List your projects
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/YOUR-ORG/_apis/projects?api-version=7.2"
```

**Exercise 2 - Get your repositories**

```bash
curl -u ":YOUR_PAT" \
  "https://dev.azure.com/YOUR-ORG/YOUR-PROJECT/_apis/git/repositories?api-version=7.2" \
  | python3 -m json.tool
```

**Exercise 3 - Create a work item via the API**

```bash
curl -X POST \
  -H "Content-Type: application/json-patch+json" \
  -u ":YOUR_PAT" \
  -d '[
    { "op": "add", "path": "/fields/System.Title", "value": "API test task" },
    { "op": "add", "path": "/fields/System.Description", "value": "Created via the REST API" }
  ]' \
  "https://dev.azure.com/YOUR-ORG/YOUR-PROJECT/_apis/wit/workitems/\$Task?api-version=7.2"
```

**Exercise 4 - Set up a service hook**

1. Get a test webhook URL from [webhook.site](https://webhook.site)
2. Go to **Project settings** > **Service hooks** > **+**
3. Choose **Web Hooks**
4. Select trigger: **Pull request created**
5. Enter your webhook.site URL
6. Test it with the **Test** button
7. Create a PR in your repository and watch the webhook arrive at webhook.site

**Exercise 5 - Post a build status to a commit**

```bash
# Get the latest commit SHA
COMMIT=$(curl -s -u ":YOUR_PAT" \
  "https://dev.azure.com/YOUR-ORG/YOUR-PROJECT/_apis/git/repositories/YOUR-REPO/commits?api-version=7.2" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['value'][0]['commitId'])")

echo "Latest commit: $COMMIT"

# Post a build status
curl -X POST \
  -H "Content-Type: application/json" \
  -u ":YOUR_PAT" \
  -d '{
    "state": "succeeded",
    "description": "External build passed",
    "context": {"name": "my-external-ci", "genre": "ci"},
    "targetUrl": "https://example.com"
  }' \
  "https://dev.azure.com/YOUR-ORG/YOUR-PROJECT/_apis/git/repositories/YOUR-REPO/commits/${COMMIT}/statuses?api-version=7.2"
```

Check the commit in Azure Repos - a green status badge should appear.

---

## Common Mistakes

**Not including api-version**

Every request must include `?api-version=7.2` (or your chosen version). Omitting it causes inconsistent behaviour as Microsoft's routing may resolve to different API versions. Always pin the version.

**Forgetting that work item creation uses JSON Patch format**

Creating and updating work items uses the JSON Patch document format (an array of operations) with `Content-Type: application/json-patch+json`. Using regular JSON with `Content-Type: application/json` returns a 400 error. This surprises developers familiar with other REST APIs.

**Not handling rate limits**

Scripts that iterate over many resources (all work items, all commits) can hit rate limits. Always implement retry logic with exponential backoff for 429 responses.

**Not paginating large result sets**

Most list endpoints return 100 or 200 items by default. For repositories, pipelines or projects with many items, you must paginate. Scripts that assume all results are in the first response silently miss items.

**Using broad service connection scope**

Creating an Azure Resource Manager service connection scoped to an entire subscription gives all pipelines that use it full access to every Azure resource in that subscription. Scope service connections to specific resource groups or resources wherever possible.

**Exposing webhook endpoints without signature validation**

Without validating the `X-Azure-DevOps-Signature` header, any attacker who discovers your webhook URL can send fake events. Always implement signature validation.

---

## Summary

The Azure DevOps REST API provides programmatic access to every Azure DevOps feature. The base URL pattern is `https://dev.azure.com/{org}/{project}/_apis/{area}/{resource}?api-version=7.2`. Always include the `api-version` parameter.

Authenticate with PATs (HTTP Basic Auth with empty username) or Bearer tokens (Azure AD OAuth). Work item creation and updates use JSON Patch format with `Content-Type: application/json-patch+json`.

Service hooks deliver real-time event notifications to external systems when PRs are created, builds complete, work items change and code is pushed. Verify webhook signatures using `X-Azure-DevOps-Signature`.

GitHub integration connects GitHub repositories to Azure Pipelines and Azure Boards, enabling a hybrid model where GitHub hosts code and Azure DevOps handles project management and deployment. GitHub Advanced Security for Azure DevOps brings CodeQL scanning and secret detection to Azure Repos.

Service connections store credentials for external system authentication in pipelines. Use workload identity federation (OIDC) for Azure Resource Manager connections to eliminate secrets entirely. Restrict service connection access to specific pipelines for sensitive connections.

Official client libraries exist for .NET, Python and JavaScript/TypeScript.

---

## Sources

- [Microsoft Learn: Azure DevOps REST API reference](https://learn.microsoft.com/rest/api/azure/devops)
- [Microsoft Learn: REST API get started](https://learn.microsoft.com/azure/devops/integrate/get-started/rest/basics)
- [Microsoft Learn: Service hooks](https://learn.microsoft.com/azure/devops/service-hooks/overview)
- [Microsoft Learn: GitHub integration](https://learn.microsoft.com/azure/devops/pipelines/repos/github)
- [Microsoft Learn: Service connections](https://learn.microsoft.com/azure/devops/pipelines/library/service-endpoints)
- [Microsoft Learn: Workload identity federation](https://learn.microsoft.com/azure/devops/pipelines/library/connect-to-azure)
- [Microsoft Learn: GitHub Advanced Security for Azure DevOps](https://learn.microsoft.com/azure/devops/repos/security/configure-github-advanced-security-features)
- [PyPI: azure-devops Python client](https://pypi.org/project/azure-devops/)
- [npm: azure-devops-node-api](https://www.npmjs.com/package/azure-devops-node-api)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

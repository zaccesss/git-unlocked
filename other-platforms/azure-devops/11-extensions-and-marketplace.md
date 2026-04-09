# Extensions and the Azure DevOps Marketplace

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

The Azure DevOps Marketplace is an ecosystem of extensions that add capabilities to Azure DevOps beyond what ships in the product. Extensions can add new pipeline tasks, new work item types, new dashboard widgets, new service connections, additional reports and integrations with third-party tools. There are thousands of extensions covering code quality, security scanning, deployment targets, notifications, analytics and more.

Understanding the Marketplace helps you avoid rebuilding what already exists, find the right tool for a specific need and evaluate extensions for security and trustworthiness before installing them in your organisation.

---

## Table of Contents

1. [What extensions are](#what-extensions-are)
2. [The Azure DevOps Marketplace](#the-azure-devops-marketplace)
3. [Finding and evaluating extensions](#finding-and-evaluating-extensions)
4. [Installing extensions](#installing-extensions)
5. [Managing installed extensions](#managing-installed-extensions)
6. [Essential extensions by category](#essential-extensions-by-category)
7. [Extension security considerations](#extension-security-considerations)
8. [Building your own extension](#building-your-own-extension)
9. [Try It Yourself](#try-it-yourself)
10. [Common Mistakes](#common-mistakes)
11. [Summary](#summary)
12. [Sources](#sources)

---

## What extensions are

An Azure DevOps extension is a package that adds one or more of the following to Azure DevOps:

**Pipeline tasks**: A new step type in `azure-pipelines.yml`. Instead of writing a complex `script:` block to deploy to a specific platform, you use a task with simple inputs. Most deployment and integration tasks in pipelines come from extensions.

**Dashboard widgets**: A new widget type for Azure DevOps dashboards. Teams commonly add widgets for work item metrics, pipeline status and external service integrations.

**Work item form extensions**: Additional fields, custom controls or new tabs on work item forms.

**Service connections**: New connection types to external services that can be used in pipelines.

**Hub contributions**: New pages or sections in the Azure DevOps web interface. For example, an extension could add a new "Security Scan Results" page to the project.

**Build result tabs**: Additional tabs on pipeline run results pages, commonly used by test and code quality tools to show their results inline.

Extensions are built using the Azure DevOps Extension SDK and published to the Marketplace. They can be free or paid, open source or proprietary.

---

## The Azure DevOps Marketplace

The Marketplace is at [marketplace.visualstudio.com](https://marketplace.visualstudio.com/azuredevops). It lists extensions for both Azure DevOps and Visual Studio.

### Browsing the Marketplace

Extensions are categorised by:
- **Build and release**: pipeline tasks for building, testing and deploying
- **Code**: code review tools, static analysis, security scanning
- **Plan and track**: work item and project management additions
- **Collaborate**: wikis, communication tools, integrations
- **Test**: test management and execution tools
- **Utility**: various productivity tools

Filter by:
- Free vs paid
- Microsoft-published vs community
- Rating
- Install count

### Extension listing information

Each extension listing shows:
- Name and publisher
- Short description and detailed documentation
- Install count and ratings
- Last updated date
- Pricing (free, paid monthly, one-time)
- Supported Azure DevOps versions
- Reviews from other organisations

---

## Finding and evaluating extensions

### What to look for

**Publisher verification**: Extensions published by Microsoft or verified publishers (indicated by a badge) have been through additional review. Community extensions have no formal vetting.

**Install count**: Extensions with tens of thousands or hundreds of thousands of installs are widely trusted. Newly published extensions with few installs have less community validation.

**Last updated date**: An extension last updated years ago may not work with the current Azure DevOps version. Prefer actively maintained extensions.

**Source availability**: Open source extensions allow you to inspect the code before installing. For security-sensitive operations (deploying to production, accessing secrets), knowing what code runs is important.

**Documentation quality**: Well-documented extensions with clear examples, known issues lists and active GitHub repositories indicate maintained projects.

**Reviews**: Read negative reviews carefully. Common complaints about reliability, breaking updates or security issues are red flags.

### Questions to ask before installing

- What permissions does this extension request?
- Does this extension access code, secrets or credentials?
- Is the publisher a recognisable company or individual?
- Is there an open source alternative I can review?
- What happens if the publisher stops maintaining it?

---

## Installing extensions

### Who can install extensions

On **Azure DevOps Services**: Only **Project Collection Administrators** (organisation administrators) can install extensions for the entire organisation.

On **Azure DevOps Server**: Only server administrators can install extensions.

Project administrators and regular users cannot install extensions - only request them.

### Installing from the Marketplace (organisation admin)

1. Go to [marketplace.visualstudio.com](https://marketplace.visualstudio.com/azuredevops)
2. Find the extension
3. Click **Get it free** (for free extensions) or the pricing option
4. Select the Azure DevOps organisation to install to
5. Review the permissions the extension requests
6. Click **Install**

The extension is immediately available to all projects in the organisation.

### Requesting an extension (non-admin)

If you do not have admin rights but need an extension:

1. Find the extension in the Marketplace
2. Click **Get it free**
3. Select your organisation
4. Since you lack admin rights, click **Request**
5. Add a justification message
6. Click **Request**

An organisation administrator receives an email notification and can approve or deny the request from **Organisation settings** > **Extensions** > **Requested**.

### Installing on Azure DevOps Server (offline)

For air-gapped Server installations without internet access:

1. Download the `.vsix` extension file from the Marketplace (on an internet-connected machine)
2. Transfer the file to the Server machine
3. In the Server admin console: **Extensions** > **Install from VSIX file**
4. Upload the `.vsix` file

---

## Managing installed extensions

### Viewing installed extensions

Go to **Organisation settings** (gear icon, bottom left) > **Extensions** > **Installed**.

You see:
- All installed extensions
- Publisher and version
- When installed
- Number of users with the extension enabled

### Updating extensions

Extensions on Azure DevOps Services auto-update when the publisher releases a new version. If an extension breaks after an update, you can roll back to a previous version from the extension's settings if the publisher supports this.

For Azure DevOps Server, updates must be manually applied - download the updated `.vsix` and reinstall.

### Disabling an extension

You can disable an extension without uninstalling it. Disabled extensions remain installed but do not show their UI elements or run their tasks.

1. Go to **Organisation settings** > **Extensions** > **Installed**
2. Find the extension
3. Click the **...** menu > **Disable**

### Uninstalling an extension

1. Go to **Organisation settings** > **Extensions** > **Installed**
2. Find the extension
3. Click **Uninstall**
4. Confirm

> [!WARNING]
> Uninstalling an extension removes all its UI elements immediately. If any pipelines reference tasks from the extension, those pipelines will fail. Check pipeline usage before uninstalling.

### Extension permissions and scopes

Extensions declare what permissions they need during installation. Common permissions:

| Permission scope | Means |
|---|---|
| Work items (read) | Can read work items |
| Work items (read, write) | Can create and modify work items |
| Code (read) | Can read source code repositories |
| Code (read, write) | Can read and modify source code |
| Build (read) | Can view pipeline runs |
| Build (read, execute) | Can view and trigger pipeline runs |
| Identity (read) | Can see user account information |
| Member Entitlement Management | Can manage user licences |
| Extension data | Can store data associated with the extension |

Review permissions carefully. An extension that reads work items to build a report is low risk. An extension that can read and write code has access to your entire codebase.

---

## Essential extensions by category

### Code quality and security

**[SonarCloud for Azure DevOps](https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarcloud)**
Publisher: SonarSource. Free tier available.
Adds SonarCloud analysis tasks to pipelines and shows code quality results as a pull request comment and a build result tab. One of the most widely used code quality integrations.

**[Checkmarx One](https://marketplace.visualstudio.com/items?itemName=checkmarx.checkmarx)**
Publisher: Checkmarx. Paid.
Static Application Security Testing (SAST) integrated directly into Azure Pipelines. Shows vulnerabilities in pull requests and pipeline results.

**[WhiteSource Bolt](https://marketplace.visualstudio.com/items?itemName=whitesource.whitesource)**
Publisher: Mend (formerly WhiteSource). Free tier.
Open source dependency scanning for security vulnerabilities and licence compliance. Shows vulnerability counts in pipeline results.

**[OWASP ZAP Scanner](https://marketplace.visualstudio.com/items?itemName=CSE-DevOps.zap-scanner)**
Community. Free.
Dynamic Application Security Testing using OWASP ZAP. Useful for web application security scanning in staging environments.

### Deployment and cloud

**[AWS Toolkit for Azure DevOps](https://marketplace.visualstudio.com/items?itemName=AmazonWebServices.aws-vsts-tools)**
Publisher: Amazon Web Services. Free.
Adds AWS-specific pipeline tasks: deploy to Elastic Beanstalk, Lambda, ECS, S3, CodeDeploy. Essential for teams deploying to AWS from Azure Pipelines.

**[Kubernetes extension](https://marketplace.visualstudio.com/items?itemName=ms-kubernetes-tools.vsts-kubernetes-tools)**
Publisher: Microsoft. Free.
Adds Kubernetes deployment tasks and a cluster management hub. Required for teams deploying to Kubernetes clusters that are not AKS.

**[Terraform](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.custom-terraform-tasks)**
Publisher: Microsoft DevLabs. Free.
Adds Terraform tasks: init, plan, apply, destroy. Popular for infrastructure-as-code pipelines.

### Testing

**[Selenium Tests](https://marketplace.visualstudio.com/items?itemName=CloudQATester.selenium-tests)**
Adds Selenium test execution tasks for cross-browser testing in pipelines.

**[Pester Test Runner](https://marketplace.visualstudio.com/items?itemName=Pester.PesterRunner)**
Publisher: Pester community. Free.
Runs PowerShell Pester tests in Azure Pipelines and publishes results.

### Reporting and analytics

**[Analytics - Sprint Burndown](https://marketplace.visualstudio.com/items?itemName=microsoft-elearn.microsoft-vsts-burndown)**
Publisher: Microsoft. Free.
An improved sprint burndown widget with more configuration options than the built-in one.

**[Work Item Visualization](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.WorkItemVisualization)**
Publisher: Microsoft DevLabs. Free.
Visualises work item relationships as a graph diagram. Useful for understanding dependency chains.

### Productivity

**[Retrospectives](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.team-retrospectives)**
Publisher: Microsoft DevLabs. Free.
Adds a Retrospectives hub to Azure DevOps for running sprint retrospectives with voting and action items directly in the tool.

**[Deliverly Plans](https://marketplace.visualstudio.com/items?itemName=ms-devlabs.vss-services-delivery-plans)**
Publisher: Microsoft DevLabs. Free.
Enhanced delivery plans with additional features beyond the built-in Delivery Plans.

**[Code Search](https://marketplace.visualstudio.com/items?itemName=ms.vss-code-search)**
Publisher: Microsoft. Free (requires Elasticsearch on Server).
Full-text code search across all repositories. Essential for large codebases. Included by default on Services; requires configuration on Server.

---

## Extension security considerations

### The extension trust model

When you install an extension, you are trusting it to run code in your Azure DevOps environment. Pipeline tasks run on your build agents - they have access to your source code, your secrets (if the pipeline uses them) and your deployment targets.

An extension with read/write access to code can read every repository in your organisation. An extension with pipeline execution access can read secrets from variable groups. This is not theoretical - extensions with malicious intent could exfiltrate your code or credentials.

### Best practices for extension security

**Prefer Microsoft and verified publishers**: Microsoft-published extensions are the safest choice. Verified publisher badges indicate additional vetting. Community extensions from unknown publishers deserve more scrutiny.

**Review open source extensions before installing**: For extensions with source code available, review what the code actually does before installing. This is especially important for extensions that access code or secrets.

**Use the principle of least privilege**: If you only need a specific pipeline task from an extension, check if the extension requests more permissions than that task needs. Some extensions request broad permissions "just in case."

**Monitor extension updates**: Extensions can change their code in updates. Subscribe to publisher communications or monitor the extension's changelog after installing.

**Limit which organisations use sensitive extensions**: For extensions with broad permissions, install them only in organisations that actually need them. Do not install a code-scanning extension across every project if only one project uses it.

**Audit extension usage periodically**: Review installed extensions quarterly. Remove extensions that are no longer used. Extensions that were installed for a specific project and then forgotten continue to hold their declared permissions.

---

## Building your own extension

If no existing extension does what you need, or if you want to distribute a tool internally across your organisation, you can build your own extension.

### What you need

- Node.js (the extension SDK is JavaScript/TypeScript-based)
- The Azure DevOps Extension SDK (`tfx-cli` command-line tool)
- A `vss-extension.json` manifest file
- Your extension's code (HTML/JavaScript for UI extensions, Node.js for pipeline tasks)

### Basic structure

```
my-extension/
├── vss-extension.json     # extension manifest
├── images/
│   └── logo.png
└── task/
    ├── task.json          # task manifest
    ├── index.js           # task entry point
    └── package.json
```

### The extension manifest (`vss-extension.json`)

```json
{
  "manifestVersion": 1,
  "id": "my-custom-extension",
  "version": "1.0.0",
  "name": "My Custom Extension",
  "description": "Does something useful",
  "publisher": "your-publisher-id",
  "targets": [
    {
      "id": "Microsoft.VisualStudio.Services"
    }
  ],
  "contributions": [
    {
      "id": "my-build-task",
      "type": "ms.vss-distributed-task.task",
      "targets": ["ms.vss-distributed-task.tasks"],
      "properties": {
        "name": "task"
      }
    }
  ],
  "files": [
    {
      "path": "task"
    }
  ]
}
```

### The task manifest (`task/task.json`)

```json
{
  "id": "a1b2c3d4-1234-5678-abcd-ef0123456789",
  "name": "MyTask",
  "friendlyName": "My Custom Task",
  "description": "Does something in a pipeline",
  "version": { "Major": 1, "Minor": 0, "Patch": 0 },
  "author": "Your Name",
  "category": "Utility",
  "inputs": [
    {
      "name": "inputParameter",
      "type": "string",
      "label": "Input value",
      "required": true,
      "helpMarkDown": "The value to process"
    }
  ],
  "execution": {
    "Node20": {
      "target": "index.js"
    }
  }
}
```

### Packaging and publishing

```bash
# Install the tfx CLI
npm install -g tfx-cli

# Package the extension
tfx extension create --manifest-globs vss-extension.json

# This creates a .vsix file
# Install directly on your Server, or publish to the Marketplace
tfx extension publish --manifest-globs vss-extension.json --token YOUR_PAT
```

For internal extensions, you can install the `.vsix` directly without publishing to the public Marketplace. This is the recommended approach for proprietary internal tooling.

---

## Try It Yourself

**Exercise 1 - Browse the Marketplace**

1. Go to [marketplace.visualstudio.com/azuredevops](https://marketplace.visualstudio.com/azuredevops)
2. Browse the Build and Release category
3. Find the AWS Toolkit for Azure DevOps - note the publisher, install count and permissions
4. Find the SonarCloud extension - note it is from a verified publisher
5. Compare a highly-rated Microsoft extension with a low-rated community extension

**Exercise 2 - Install a free extension (admin required)**

If you have organisation admin access:
1. Find the **Retrospectives** extension from Microsoft DevLabs
2. Click **Get it free**
3. Select your organisation
4. Review the permissions it requests
5. Install it
6. Go to a project and find the new **Retrospectives** tab in the navigation

**Exercise 3 - Request an extension (non-admin)**

If you do not have admin access:
1. Find any free extension in the Marketplace
2. Click **Get it free**
3. Select your organisation
4. Since you lack admin rights, the option to Request appears
5. Send a request - observe the flow

**Exercise 4 - Explore a pipeline task from an extension**

After installing the AWS Toolkit (or any extension with pipeline tasks):

1. Create a new pipeline
2. Use the task assistant (the search panel when editing YAML)
3. Search for an AWS task (e.g. "AWS S3 Upload")
4. Click on it to see the available inputs
5. Look at how the YAML task is structured

---

## Common Mistakes

**Installing extensions without reviewing permissions**

The installation dialog shows what permissions an extension requests. Most users click through without reading. An extension that requests Code (read, write) permission can read every repository in your organisation. Always read and understand the permissions before installing.

**Not removing extensions after projects end**

Teams install extensions for specific projects or experiments and forget about them. Unused extensions still hold their declared permissions and could be exploited if the publisher's account is compromised. Audit installed extensions quarterly and remove anything no longer needed.

**Installing multiple extensions that do the same thing**

Installing three separate code quality tools creates noise (developers see three different quality reports on every PR) and confusion about which results to act on. Decide on a single tool for each purpose and stick to it.

**Using untrusted extensions in pipelines that access secrets**

If a pipeline task from an untrusted extension has access to secret variables (API keys, deployment credentials), that extension's code runs with access to those secrets. Reserve pipeline tasks with access to secrets for Microsoft-published or verified-publisher extensions where possible.

**Forgetting that extensions can break pipeline on uninstall**

Uninstalling an extension that provides pipeline tasks causes every pipeline using those tasks to fail immediately. Before uninstalling, audit which pipelines use tasks from that extension and update them first.

---

## Summary

The Azure DevOps Marketplace hosts thousands of extensions that add pipeline tasks, dashboard widgets, service connections and new UI sections to Azure DevOps. Extensions are installed at the organisation level by administrators and become available across all projects.

When evaluating extensions, prefer Microsoft and verified publisher extensions. Review permissions carefully - extensions with code read/write access can access your entire codebase. Check install counts, maintenance status and available source code before installing.

Essential extension categories: code quality (SonarCloud, Checkmarx), deployment (AWS Toolkit, Terraform, Kubernetes), testing (Selenium, Pester) and productivity (Retrospectives, Code Search).

For internal tooling, build custom extensions using the Azure DevOps Extension SDK and distribute them as `.vsix` files without publishing to the public Marketplace.

---

## Sources

- [Azure DevOps Marketplace](https://marketplace.visualstudio.com/azuredevops)
- [Microsoft Learn: Marketplace overview](https://learn.microsoft.com/azure/devops/marketplace/overview)
- [Microsoft Learn: Install extensions](https://learn.microsoft.com/azure/devops/marketplace/install-extension)
- [Microsoft Learn: Develop extensions](https://learn.microsoft.com/azure/devops/extend/get-started/node)
- [Microsoft Learn: Extension security](https://learn.microsoft.com/azure/devops/marketplace/trust)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

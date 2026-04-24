# Creating an Azure DevOps Account

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

Getting started with Azure DevOps requires a Microsoft account, an Azure DevOps organisation and at least one project. The process is straightforward but the terminology differs from other platforms - particularly the relationship between organisations, projects and repositories. This file walks through the complete setup: creating your account, configuring your organisation, creating a project, setting up authentication (PATs and SSH keys) and understanding the free tier limits.

---

## Table of Contents

1. [Microsoft account vs Azure Active Directory](#microsoft-account-vs-azure-active-directory)
2. [Creating your account and organisation](#creating-your-account-and-organisation)
3. [Understanding organisations and projects](#understanding-organisations-and-projects)
4. [Creating your first project](#creating-your-first-project)
5. [Personal Access Tokens](#personal-access-tokens)
6. [SSH keys](#ssh-keys)
7. [Free tier limits](#free-tier-limits)
8. [Adding team members](#adding-team-members)
9. [Connecting to Azure Active Directory](#connecting-to-azure-active-directory)
10. [Account security](#account-security)
11. [Try It Yourself](#try-it-yourself)
12. [Common Mistakes](#common-mistakes)
13. [Summary](#summary)
14. [Sources](#sources)

---

## Microsoft account vs Azure Active Directory

Azure DevOps authentication works through two identity types. Understanding the difference before you set up matters, because switching later is difficult.

### Microsoft account (personal)

A **Microsoft account** (MSA) is a personal identity - the same login you use for Outlook.com, Xbox, OneDrive and consumer Microsoft services. Any email address can be a Microsoft account if you registered it at [account.microsoft.com](https://account.microsoft.com).

Personal Microsoft accounts are suitable for:

- Individual developers
- Small teams without enterprise IT infrastructure
- Open source projects
- Learning and experimentation

### Azure Active Directory (Azure AD / Entra ID)

**Azure Active Directory** (now branded as **Microsoft Entra ID**) is Microsoft's enterprise identity platform. When an organisation uses Microsoft 365, Azure cloud services or Windows Server with Active Directory, they likely have Azure AD managing their identities.

Signing in to Azure DevOps with an Azure AD identity (your work email, e.g. `you@yourcompany.com`) connects Azure DevOps to your organisation's identity management: single sign-on, conditional access policies, multi-factor authentication enforcement and automatic deprovisioning when you leave the organisation.

Azure AD is suitable for:

- Enterprise teams with existing Microsoft 365 or Azure subscriptions
- Teams that need centralised identity management
- Regulated environments requiring SSO and MFA enforcement

> [!IMPORTANT]
> If your organisation uses Azure AD, sign up for Azure DevOps with your **work Azure AD identity**, not a personal Microsoft account. Connecting a personal-account organisation to Azure AD later is possible but complicated. Start right.

---

## Creating your account and organisation

### Step 1 - Navigate to Azure DevOps

Go to [dev.azure.com](https://dev.azure.com) and click **Start free**.

### Step 2 - Sign in

You are redirected to Microsoft's sign-in page. Sign in with:

- A Microsoft account (personal)
- An Azure AD work or school account
- A GitHub account (Microsoft allows GitHub identity for Azure DevOps sign-in)

### Step 3 - Create your organisation

After signing in for the first time, Azure DevOps prompts you to create an organisation.

**Organisation name**: Choose carefully. The name appears in your URL (`dev.azure.com/your-org-name`) and in all repository clone URLs. Changing it later is possible but breaks all existing URLs, remotes and integrations. For a company, use the company name or abbreviation. For a personal account, use your name or a handle.

**Region**: Choose the Azure region where your data will be stored. Options include East US, West Europe, Australia East, South East Asia and others. Choose the region closest to your team. Data residency matters for compliance - if your organisation requires data to stay in the EU, choose West Europe. This cannot be changed after creation without creating a new organisation.

Click **Continue** to create the organisation. Azure DevOps provisions the organisation within a few seconds.

---

## Understanding organisations and projects

Before creating your first project, understand the structure:

```
Organisation (dev.azure.com/your-org)
  ├── Project A
  │     ├── Repos (multiple repositories)
  │     ├── Boards (work items, sprints, kanban)
  │     ├── Pipelines (CI/CD)
  │     ├── Artifacts (package feeds)
  │     └── Test Plans
  └── Project B
        ├── Repos
        ├── Boards
        └── Pipelines
```

**Organisation** is the billing and administrative unit. User licences are assigned at the organisation level. One organisation can have many projects.

**Project** is the workspace unit. One project typically maps to one team, one product area or one major application. A project has its own work items, pipelines and repositories. Members are added at the project level.

**Repository** lives inside a project. One project can have multiple repositories.

### How many projects to create

**Recommended approach for most teams:** One project per team or product area. Put multiple repositories inside. This keeps boards, pipelines and access control unified for the team.

**When to create multiple projects:**

- Different security requirements
- Completely separate teams with no shared backlog
- Different process templates required

---

## Creating your first project

After creating the organisation, Azure DevOps immediately prompts for a project name.

### Project settings

**Project name**: The name of the team or product area. Appears in URLs.

**Visibility**:

- **Private**: only added members can access the project
- **Public**: anyone on the internet can view the project. Suitable for open source.

**Version control**: Choose **Git** (recommended) or **Team Foundation Version Control** (TFVC, legacy).

**Work item process**: The template controlling work item types:

- **Agile**: Epics → Features → User Stories → Tasks/Bugs. Most common.
- **Scrum**: Epics → Features → Product Backlog Items → Tasks/Bugs. Formal Scrum.
- **CMMI**: More formal, for regulated environments.
- **Basic**: Epics → Issues → Tasks. Simplest, closest to GitHub Issues.

> [!NOTE]
> The process template can be changed after project creation but it is a non-trivial operation. If unsure, Agile is the most flexible default.

Click **Create project**.

---

## Personal Access Tokens

**Personal Access Tokens (PATs)** are the primary way to authenticate automated operations to Azure DevOps: HTTPS Git operations, the REST API, the Azure DevOps CLI and third-party integrations.

### Creating a PAT

1. Click your avatar in the top right corner
2. Click **Personal access tokens**
3. Click **+ New Token**
4. Fill in the details:

**Name**: A descriptive label such as `VS Code extension`, `CI/CD pipeline` or `Local dev`.

**Organisation**: Which organisation this token grants access to. Select **All accessible organisations** for cross-organisation use.

**Expiration**: 30, 90, 180 or 365 days, or a custom date. Always set an expiry.

**Scopes**: Always use the minimum required:

| Scope                        | Use case                                |
| ---------------------------- | --------------------------------------- |
| **Code: Read**               | Clone and fetch repositories            |
| **Code: Read & Write**       | Push commits, create branches           |
| **Work Items: Read & Write** | Create and update work items            |
| **Build: Read & Execute**    | View and trigger pipeline runs          |
| **Packaging: Read & Write**  | Download and publish Artifacts packages |

5. Click **Create**
6. **Copy the token immediately** - it is shown only once.

### Using a PAT for HTTPS Git operations

```bash
git clone https://dev.azure.com/your-org/your-project/_git/your-repo
# Username: your email (or any string)
# Password: your-PAT-value
```

**Storing credentials:**

🪟 **Windows** - Git Credential Manager (included with Git for Windows) stores PATs in Windows Credential Manager automatically.

🍎 **Mac** - Git Credential Manager stores PATs in macOS Keychain automatically.

🐧 **Linux**:

```bash
git config --global credential.helper 'cache --timeout=3600'
```

### Rotating PATs

PATs cannot be extended - create a new one, update all systems, then delete the old one. If a PAT is compromised, revoke it immediately from **Personal access tokens** > find the token > **Revoke**.

---

## SSH keys

SSH keys provide passwordless authentication for Git operations, replacing PAT-based HTTPS.

### Generating an SSH key pair

🪟 **Windows** (PowerShell or Git Bash):

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🍎 **Mac**:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

🐧 **Linux**:

```bash
ssh-keygen -t ed25519 -C "your-email@example.com"
```

Accept the default file location (`~/.ssh/id_ed25519`). Add a passphrase to protect the key.

### Adding the public key to Azure DevOps

**Copy the public key:**

🪟 **Windows** (PowerShell):

```powershell
Get-Content ~/.ssh/id_ed25519.pub | clip
```

🍎 **Mac**:

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

🐧 **Linux**:

```bash
cat ~/.ssh/id_ed25519.pub
```

**Add to Azure DevOps:**

1. Click your avatar > **Security**
2. Click **+ New Key**
3. Give the key a name
4. Paste the public key content
5. Click **Add**

### Testing the SSH connection

```bash
ssh -T git@ssh.dev.azure.com
```

Expected response:

```
remote: Shell access is not supported.
```

This is correct - it means the connection works. Shell access is intentionally disabled.

### Cloning with SSH

```bash
git clone git@ssh.dev.azure.com:v3/your-org/your-project/your-repo
```

---

## Free tier limits

| Resource                      | Free allocation            |
| ----------------------------- | -------------------------- |
| Basic users                   | 5 per organisation         |
| Stakeholder users             | Unlimited (free)           |
| Microsoft-hosted parallel job | 1 job, 1,800 minutes/month |
| Self-hosted parallel jobs     | 1 job, unlimited minutes   |
| Artifacts storage             | 2 GiB                      |
| Repositories                  | Unlimited                  |
| Work items                    | Unlimited                  |

**Additional costs:**

- Extra Basic users: $6/user/month
- Extra Microsoft-hosted parallel jobs: $40/month each
- Extra self-hosted parallel jobs: $15/month each
- Basic + Test Plans: $52/user/month
- Artifacts storage beyond 2 GiB: ~$2/GiB/month

Visual Studio subscribers (any level) receive a Basic licence at no extra cost - they do not consume one of the 5 free Basic seats.

---

## Adding team members

### Inviting users

1. Go to **Organisation settings** (gear icon, bottom left)
2. Click **Users**
3. Click **Add users**
4. Enter email addresses (comma-separated for multiple)
5. Select the access level: **Basic** or **Stakeholder**
6. Optionally assign to projects and groups
7. Click **Add**

### Access levels

| Level                  | Cost                       | Capabilities                                                           |
| ---------------------- | -------------------------- | ---------------------------------------------------------------------- |
| **Stakeholder**        | Free                       | View/update work items, view dashboards. No Repos or Pipelines access. |
| **Basic**              | 5 free, then $6/user/month | Full Repos, Pipelines, Boards and Artifacts.                           |
| **Basic + Test Plans** | $52/user/month             | Everything plus Test Plans.                                            |

> [!NOTE]
> Stakeholders cannot clone code, push commits or trigger builds. If a developer is added as Stakeholder, they cannot do their job. Use Basic for anyone working with code.

---

## Connecting to Azure Active Directory

Connecting to Azure AD provides single sign-on, automatic deprovisioning and MFA enforcement.

> [!WARNING]
> This is a significant operation. Existing Microsoft account users must be re-invited as Azure AD users after the connection. All team members lose access temporarily during the migration. Coordinate carefully.

1. Go to **Organisation settings** > **Azure Active Directory**
2. Click **Connect directory**
3. Select your Azure AD tenant
4. Review the user mapping
5. Click **Connect**

After connecting, new users are added using their Azure AD identity (work email).

---

## Account security

### Multi-factor authentication

For personal Microsoft accounts, enable MFA at [account.microsoft.com/security](https://account.microsoft.com/security). For Azure AD accounts, MFA is managed by your IT administrator.

### Reviewing active tokens

Review PATs periodically:

1. Click your avatar > **Personal access tokens**
2. Delete tokens no longer in use
3. Ensure remaining tokens have appropriate expiry and scope

### Reviewing SSH keys

Check your avatar > **Security** > **SSH public keys**. Remove keys from decommissioned or lost devices.

---

## Try It Yourself

**Exercise 1 - Create your organisation and project**

1. Go to [dev.azure.com](https://dev.azure.com)
2. Sign in with a Microsoft account
3. Create an organisation
4. Create a project called `practice` using the Agile process template
5. Explore all five services in the left sidebar

**Exercise 2 - Create a PAT**

1. Click your avatar > **Personal access tokens**
2. Click **+ New Token**
3. Name it `Git operations`, set 30-day expiry, select Code (Read & Write)
4. Click **Create** and copy the token

**Exercise 3 - Clone using HTTPS**

```bash
git clone https://dev.azure.com/YOUR-ORG/practice/_git/practice
# Username: your email
# Password: your PAT
```

**Exercise 4 - Set up SSH and clone**

```bash
# Generate key
ssh-keygen -t ed25519 -C "your-email@example.com"

# Test connection after adding to Azure DevOps
ssh -T git@ssh.dev.azure.com

# Clone via SSH (use URL from Clone > SSH tab in Repos)
git clone git@ssh.dev.azure.com:v3/YOUR-ORG/practice/practice
```

---

## Common Mistakes

**Creating the organisation with a personal account when the team uses Azure AD**

Once created under a personal Microsoft account, connecting to Azure AD requires migrating all users. Start with the right identity type.

**Confusing organisation name and project name in clone URLs**

Clone URLs contain both: `https://dev.azure.com/ORG/PROJECT/_git/REPO`. The organisation is the first segment, the project is the second.

**Not setting PAT expiry**

Azure DevOps Services requires expiry on all PATs. Maximum is 1 year. Use shorter durations for better security.

**Giving PATs full access scope**

For CI/CD, Code: Read & Write and Build: Read & Execute are typically all that is needed. Full access creates unnecessary risk.

**Adding developers as Stakeholders**

Stakeholders cannot access Repos or trigger pipelines. Use Basic for anyone who needs to write code.

---

## Summary

Azure DevOps uses Microsoft identity - personal Microsoft accounts or Azure Active Directory (Entra ID). For enterprise teams, Azure AD is strongly preferred.

Sign up at [dev.azure.com](https://dev.azure.com). Create an organisation (billing unit), then one or more projects (workspace units), each containing repositories, boards and pipelines.

Personal Access Tokens authenticate HTTPS Git operations and the API. Create them with minimum necessary scopes and always set an expiry. SSH keys provide passwordless Git authentication - add the public key under your avatar > Security.

The free tier provides 5 Basic users, 1 Microsoft-hosted parallel job with 1,800 minutes/month and 2 GiB Artifacts storage.

---

## Sources

- [Microsoft Learn: Create an organisation](https://learn.microsoft.com/azure/devops/organizations/accounts/create-organization)
- [Microsoft Learn: Personal access tokens](https://learn.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)
- [Microsoft Learn: SSH keys in Azure DevOps](https://learn.microsoft.com/azure/devops/repos/git/use-ssh-keys-to-authenticate)
- [Microsoft Learn: Azure DevOps pricing](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- [Microsoft Learn: Access levels](https://learn.microsoft.com/azure/devops/organizations/security/access-levels)
- [Microsoft Learn: Connect to Azure AD](https://learn.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

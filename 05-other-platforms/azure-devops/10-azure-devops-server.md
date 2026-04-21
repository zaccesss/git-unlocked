# Azure DevOps Server (On-Premises)

**Difficulty:** 🔴 Advanced | **Time:** 45 minutes

Azure DevOps Server is the self-hosted, on-premises version of Azure DevOps. It is the direct successor to Team Foundation Server (TFS), carrying forward more than 20 years of enterprise development tooling into a modern deployment model. If your organisation cannot use cloud services due to compliance requirements, data sovereignty policies, air-gapped network requirements or existing infrastructure investments, Azure DevOps Server provides the same core capabilities as Azure DevOps Services running on your own hardware.

---

## Table of Contents

1. [Azure DevOps Server vs TFS: the name history](#azure-devops-server-vs-tfs-the-name-history)
2. [When to choose Server over Services](#when-to-choose-server-over-services)
3. [System requirements](#system-requirements)
4. [Installation overview](#installation-overview)
5. [Configuration and initial setup](#configuration-and-initial-setup)
6. [Authentication: Active Directory and Azure AD](#authentication-active-directory-and-azure-ad)
7. [Upgrading from TFS](#upgrading-from-tfs)
8. [Differences from Azure DevOps Services](#differences-from-azure-devops-services)
9. [Licensing](#licensing)
10. [Maintaining a Server instance](#maintaining-a-server-instance)
11. [Build agents on Server](#build-agents-on-server)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## Azure DevOps Server vs TFS: the name history

Understanding the naming helps when reading documentation, searching for help or talking to colleagues from different eras:

| Product name | Years | Notes |
|---|---|---|
| Visual SourceSafe | 1994-2005 | First Microsoft VCS, file-locking model |
| Team Foundation Server (TFS) 2005 | 2005 | First TFS release with TFVC and work items |
| TFS 2008 | 2008 | Added SharePoint integration |
| TFS 2010 | 2010 | Lab Management added |
| TFS 2012 | 2012 | Git support added |
| TFS 2013 | 2013 | Agile planning tools |
| TFS 2015 | 2015 | New build system (replaced XAML builds) |
| TFS 2017 | 2017 | Release Management |
| TFS 2018 | 2018 | Last "TFS" release |
| Azure DevOps Server 2019 | 2019 | First "Azure DevOps Server" release |
| Azure DevOps Server 2020 | 2020 | |
| Azure DevOps Server 2022 | 2022 | |
| Azure DevOps Server (no year) | 2025-present | Modern Lifecycle Policy, continuous support |

The current release is simply called **Azure DevOps Server** with no year designation, released December 2025. This reflects a shift to Microsoft's Modern Lifecycle Policy - rather than annual major releases, the server product now receives ongoing updates.

---

## When to choose Server over Services

Choose Azure DevOps Server over Azure DevOps Services when your organisation has:

**Data sovereignty requirements**: Code and work item data must never leave specific geographic boundaries or your own infrastructure. Regulated industries (certain government, defence, finance and healthcare sectors) may require this.

**Air-gapped networks**: Your development environment has no internet connectivity. Azure DevOps Services is a cloud product; Server runs entirely on-premises without internet access.

**Active Directory integration without Azure AD**: On-premises Active Directory integration is built into Server. Azure DevOps Services requires Azure Active Directory (Entra ID) for similar functionality, which may require an Azure subscription.

**Existing TFS investment**: Organisations with years of TFS history, customisations, process templates and tooling are best served by upgrading to Server rather than migrating to Services.

**Control over upgrade timing**: Services receives updates continuously. Server lets you control exactly when you upgrade, allowing testing before production deployment.

**Custom plugin requirements**: Server supports the full Atlassian/Microsoft plugin SDK for deep customisations. Services has a more restricted extension model.

**Cost at scale**: For very large organisations (1,000+ users), Server licensing can be cheaper than per-user Services billing once infrastructure costs are included.

---

## System requirements

### Application tier (Azure DevOps Server application)

| Component | Minimum | Recommended |
|---|---|---|
| CPU | 4-core | 8-core |
| RAM | 8 GB | 16 GB+ |
| Disk (application) | 10 GB free | SSD, 50 GB+ |
| Disk (repositories) | Depends on code size | SSD, plan for growth |
| OS | Windows Server 2019/2022/2025 or RHEL 8/9, Ubuntu 20.04/22.04/24.04 | Windows Server 2025 or Ubuntu 22.04 |

> [!NOTE]
> Azure DevOps Server now supports Linux (RHEL and Ubuntu) as of 2019. However, Windows Server remains the most tested and most documented deployment platform. If your team has strong Linux infrastructure expertise, Linux is a valid choice. If not, Windows Server is safer.

### Data tier (SQL Server)

Azure DevOps Server stores all data in SQL Server. SQL Server must be installed separately.

| Component | Minimum version |
|---|---|
| SQL Server | 2019 or later |
| SQL Server version | Standard Edition minimum (Enterprise for large deployments) |
| SQL Server features | Database Engine, Full-Text Search |

SQL Server 2025 (when released) is supported. SQL Server 2016 and earlier are not supported.

> [!IMPORTANT]
> **SQL Server Full-Text Search** is required. This is an optional SQL Server feature that is not installed by default in some SQL configurations. Ensure it is installed before running the Azure DevOps Server installer.

### Search tier (Elasticsearch / OpenSearch)

Code search functionality requires a separate Elasticsearch instance. Azure DevOps Server uses Elasticsearch 8.x (earlier versions used OpenSearch on TFS/older ADO Server). This is separate from the application and data tiers.

### Scale configurations

| Configuration | Users | Application nodes | SQL Server |
|---|---|---|---|
| Single-server | Up to 250 | 1 | Local SQL Server Express or Standard |
| Dual-server | Up to 500 | 1 | Separate SQL Server |
| Multi-server | 500+ | Multiple (with load balancer) | SQL Server with High Availability |

For any production deployment with more than 100 users, put SQL Server on a separate server from the Azure DevOps application.

---

## Installation overview

### Prerequisites checklist

Before running the installer:

- [ ] Windows Server 2019/2022/2025 or supported Linux OS installed and patched
- [ ] SQL Server with Full-Text Search installed and accessible
- [ ] Service account created in Active Directory (for Windows) or as local account
- [ ] Inbound firewall port opened (default: 8080 for HTTP, 443 for HTTPS)
- [ ] Static IP or DNS name for the server
- [ ] SSL certificate for HTTPS (strongly recommended for production)

### Downloading the installer

Download Azure DevOps Server from the [Microsoft Visual Studio download page](https://visualstudio.microsoft.com/downloads/#other-family). The file is named `azuredevopsserver.exe` (Windows) or equivalent for Linux.

### Running the installer

🪟 **Windows**:
```
# Run as Administrator
azuredevopsserver.exe
```

Follow the installation wizard:
1. Choose installation type: **New Deployment** (fresh install) or **Upgrade** (from TFS or earlier ADO Server)
2. Select the installation directory
3. The installer checks prerequisites and reports any missing components
4. Accept the licence and click **Install**

🐧 **Linux** (Ubuntu example):
```bash
# Add the Microsoft package repository
wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb

# Install Azure DevOps Server
sudo apt-get update
sudo apt-get install azure-devops-server
```

---

## Configuration and initial setup

After installation, the Azure DevOps Server Configuration Wizard runs. This is separate from the installer and sets up the application.

### Configuration Wizard steps

**Deployment type**: Choose **New Deployment**.

**Database**: Specify the SQL Server instance. The wizard tests the connection. Options:
- `.\` or `localhost` for local SQL Server
- `SERVERNAME` for a named instance
- `SERVERNAME\INSTANCENAME` for a named SQL Server instance

**Account**: The service account that Azure DevOps Server runs as. Options:
- **Network Service** (Windows built-in, suitable for simple deployments)
- **Local service** (more restricted)
- **Domain account** (recommended for AD-integrated deployments - e.g. `DOMAIN\ADOService`)

**Application tier URL**: The URL users will use to access Azure DevOps Server. Examples:
- `http://tfs.yourcompany.com:8080/tfs` (HTTP, non-standard port)
- `https://devops.yourcompany.com` (HTTPS, standard port)

For production, always use HTTPS. The wizard can configure an SSL certificate from:
- Windows Certificate Store (for certificates installed via group policy or IIS)
- A PFX file
- Let's Encrypt (if the server has internet access)

**Search**: Configure Elasticsearch for code search (optional but recommended).

**Reporting**: Configure SQL Server Reporting Services (SSRS) for reports (optional, requires SSRS installation).

After completing the wizard, Azure DevOps Server is accessible at the configured URL.

### First login

Navigate to `http://yourserver:8080/tfs` (or your configured URL). Log in as the Windows/AD account you used during installation. You are automatically an organisation administrator.

### Creating the first project collection

Azure DevOps Server uses **Project Collections** as the organisational unit above projects. A project collection is roughly equivalent to an Azure DevOps Services organisation. Most deployments use a single collection named `DefaultCollection`.

1. After first login, you land on the administration page
2. Under **Collections**, click **New collection**
3. Name it `DefaultCollection` (or a meaningful name)
4. Click **Create collection**

All projects are created within a collection.

---

## Authentication: Active Directory and Azure AD

### Windows Authentication (Active Directory)

The default and most common authentication mode. Users sign in with their Windows/Active Directory credentials. No separate password management required.

When users navigate to the Azure DevOps Server URL in Internet Explorer, Edge or Chrome (with Windows Integrated Authentication configured), they are automatically signed in using their Windows session credentials.

**To configure Windows Authentication:**
This is the default configuration. No additional steps required if the server is joined to the same Active Directory domain as the users.

**For cross-domain or non-domain-joined clients:**
Configure Windows Authentication exceptions or use basic authentication with HTTPS.

### Azure Active Directory (Entra ID) integration

Azure DevOps Server can authenticate against Azure AD, enabling:
- Users to sign in with their Microsoft 365 / Azure AD credentials
- Conditional Access policies
- MFA enforcement from Azure AD

This is configured in the Azure DevOps Server Administration Console:
1. Open the Administration Console
2. Under **Authentication**, configure the Azure AD connection
3. Users can then sign in with their Azure AD identity

### SAML integration

Azure DevOps Server supports SAML 2.0 for integration with any SAML-compliant identity provider (Okta, PingFederate, ADFS). This enables SSO without Azure AD.

Configure SAML in the Administration Console > Authentication > SAML provider configuration.

---

## Upgrading from TFS

Upgrading from TFS to Azure DevOps Server preserves all data - work items, code history, pipelines (with some conversion), build history, test results and permissions.

### Supported upgrade paths

You cannot upgrade directly from very old TFS versions to the latest Azure DevOps Server. Microsoft publishes required upgrade paths - you must pass through certain intermediate versions.

Example paths (check the official upgrade path documentation for current requirements):
- TFS 2015 → TFS 2018 → Azure DevOps Server 2022 → Azure DevOps Server (current)
- TFS 2017 → Azure DevOps Server 2019 → Azure DevOps Server (current)
- Azure DevOps Server 2022 → Azure DevOps Server (current) - direct upgrade

> [!IMPORTANT]
> Always check the [official upgrade path documentation](https://learn.microsoft.com/azure/devops/server/upgrade/get-started) before planning an upgrade. Skipping required intermediate versions causes data corruption.

### Pre-upgrade checklist

- [ ] Full backup of SQL Server databases (TFS configuration DB and all project collection DBs)
- [ ] Test backup restore works (do not skip this)
- [ ] Record current version number (shown in TFS/ADO Server admin console)
- [ ] Check upgrade path - may require intermediate stops
- [ ] Read the release notes for each version in the path
- [ ] Test the upgrade in a non-production environment first
- [ ] Notify all users of the maintenance window
- [ ] Document any custom XAML build definitions (these must be rewritten for the new build system)

### Upgrade procedure

1. Run the Azure DevOps Server installer on the existing TFS/ADO Server machine
2. Choose **Upgrade** (not New Deployment)
3. The wizard detects the existing installation
4. Run the configuration wizard, choosing to upgrade existing databases
5. The upgrade process runs - this can take hours for large deployments

### Post-upgrade tasks

- Verify all project collections are accessible
- Test build pipelines (XAML builds from pre-2015 TFS will not work - they must be rewritten)
- Update agent software on all build agents
- Update client software (Visual Studio, VS Code extensions) to work with the new server version
- Review and update any custom notifications

---

## Differences from Azure DevOps Services

Azure DevOps Server and Services share most features, but there are important differences:

### Features in Services not available in Server

| Feature | Notes |
|---|---|
| **AI features** | Copilot integration, AI-powered PR summaries - Services only |
| **GitHub Codespaces integration** | Services only |
| **Analytics views (full)** | Basic analytics in Server; full Analytics service is Services-focused |
| **Managed DevOps Pools** | Services only |
| **Some Marketplace extensions** | Extensions must be compatible with your Server version |

### Features in Server not available in Services

| Feature | Notes |
|---|---|
| **On-premises AD integration** | Services requires Azure AD |
| **Air-gapped operation** | No internet required for Server |
| **SAML SSO without Azure AD** | Server can use any SAML IDP directly |
| **Full admin control** | You control every setting, upgrade timing and configuration |
| **SQL Server Reporting Services** | Legacy reports via SSRS |
| **SharePoint integration** | Legacy SharePoint portal integration (rarely used) |

### Build agent differences

Azure DevOps Server does not provide Microsoft-hosted agents. All build agents must be self-hosted. You install and manage agent software on your own machines.

This means:
- No built-in macOS build support unless you own and manage a macOS build machine
- All agent maintenance (OS updates, tool updates) is your responsibility
- Agent pools are local to your Server instance

---

## Licensing

### User licences

Azure DevOps Server requires user licences. Licences are bundled with Visual Studio subscriptions and sold separately:

| Licence type | Access level | Typically for |
|---|---|---|
| **Visual Studio Enterprise subscriber** | Basic + Test Plans | Senior developers, architects |
| **Visual Studio Professional subscriber** | Basic | Developers |
| **Visual Studio Test Professional subscriber** | Basic + Test Plans | QA engineers |
| **Basic** | Full Repos, Pipelines, Boards | Developers without VS subscriptions |
| **Stakeholder** | Work items, dashboards (no code) | Free, managers/stakeholders |

Visual Studio subscription licences are transferable - if a developer has a Visual Studio Enterprise subscription for their development tools, that licence also covers Azure DevOps Server access.

### Server licences

Azure DevOps Server itself requires a licence:
- **Azure DevOps Server licence** - the server product licence (separate from user licences)
- Included in several Microsoft enterprise agreements (EA, MPSA)
- Available as a standalone purchase

Contact Microsoft licensing or a Microsoft partner for current pricing.

---

## Maintaining a Server instance

### Backups

**Back up regularly and test restores.** Azure DevOps Server data is irreplaceable - work item history, code, pipeline results and test records accumulated over years. A database corruption or hardware failure without a backup is catastrophic.

**What to back up:**
- SQL Server databases: `Tfs_Configuration`, `Tfs_DefaultCollection` (and any other project collection databases)
- Azure DevOps Server application files (less critical - the installer can recreate these)
- Elasticsearch indices (code search - can be rebuilt, but rebuilding takes time)
- SSL certificates and service account credentials

**Backup schedule:**
- Daily differential backups
- Weekly full backups
- Retain at least 4 weeks of backups

**Test your backups:**
Quarterly, restore from backup to a test environment and verify all data is accessible. An untested backup is not a backup.

Built-in backup: Azure DevOps Server includes a scheduled backup tool in the Administration Console:
1. Open the Administration Console
2. Under **Scheduled backups**, configure the backup schedule and destination
3. Test the restore procedure

### Updates and patches

Azure DevOps Server receives security patches and cumulative updates. Under the Modern Lifecycle Policy, keeping current is expected. Fall significantly behind and you may find support unavailable.

Monitor the [Azure DevOps Server release notes](https://learn.microsoft.com/azure/devops/server/release-notes/azuredevopsserver) for new patches. Apply security patches promptly.

### Monitoring

Monitor these indicators for a healthy Azure DevOps Server instance:
- **SQL Server performance**: query duration, disk I/O, memory pressure
- **Disk space**: repositories grow continuously; monitor and plan expansion
- **Windows Event Log**: application and system errors
- **Azure DevOps Server logs**: located at `%ProgramData%\Microsoft\Azure DevOps\Server Configuration\Logs`
- **IIS (if used)**: web server errors and access logs

### Common maintenance tasks

**Detach/reattach project collections**: For maintenance, migration or splitting, collections can be detached from the server and reattached to another.

**Move to new hardware**: Back up databases, install fresh Server on new hardware, restore databases, update connection strings.

**Change SQL Server**: Use the database detach/backup approach to move to a new SQL Server instance.

---

## Build agents on Server

Since Azure DevOps Server has no hosted agents, you must set up self-hosted agents. The agent is the same software as used with Azure DevOps Services.

### Setting up an agent pool

1. In the Administration Console > **Application Tier** > **Agent Pools**
2. Click **Add Pool**
3. Name the pool

Or from the Azure DevOps Server web UI:
1. **Organisation settings** > **Agent pools** > **Add pool**

### Installing a build agent

🪟 **Windows** (PowerShell):
```powershell
mkdir agent ; cd agent
Invoke-WebRequest -Uri https://vstsagentpackage.azureedge.net/agent/latest/vsts-agent-win-x64-latest.zip -OutFile agent.zip
Expand-Archive agent.zip -DestinationPath .

.\config.cmd --url https://your-server:8080/tfs `
             --auth Negotiate `
             --pool Default `
             --agent "Windows Build Agent 1" `
             --runAsService
```

🍎 **Mac**:
```bash
mkdir myagent && cd myagent
curl -O https://vstsagentpackage.azureedge.net/agent/latest/vsts-agent-osx-x64-latest.tar.gz
tar zxvf vsts-agent-osx-x64-latest.tar.gz

./config.sh --url https://your-server:8080/tfs \
            --auth negotiate \
            --pool Default \
            --agent "Mac Build Agent"

./run.sh
```

🐧 **Linux**:
```bash
mkdir myagent && cd myagent
curl -O https://vstsagentpackage.azureedge.net/agent/latest/vsts-agent-linux-x64-latest.tar.gz
tar zxvf vsts-agent-linux-x64-latest.tar.gz

./config.sh --url https://your-server:8080/tfs \
            --auth negotiate \
            --pool Default \
            --agent "Linux Build Agent"

./run.sh
```

### Agent authentication with Server

When connecting to Server (not Services), agents typically use **Negotiate** (Kerberos/NTLM) authentication with Windows credentials rather than PATs. This works when the agent machine is domain-joined and uses a service account.

---

## Try It Yourself

> [!NOTE]
> Setting up Azure DevOps Server requires a Windows Server or Linux server and SQL Server. This exercise uses a local evaluation installation on Windows.

**Exercise 1 - Download and evaluate**

1. Go to [visualstudio.microsoft.com/downloads](https://visualstudio.microsoft.com/downloads/#other-family)
2. Download Azure DevOps Server (180-day trial available)
3. On a Windows machine with SQL Server installed (SQL Server Express works for evaluation):
   - Run the installer
   - Choose New Deployment
   - Follow the configuration wizard using the local SQL Server instance
4. Access the server at `http://localhost:8080/tfs`

**Exercise 2 - Explore Server-specific admin features**

1. Access the **Administration Console** (from the Windows Start menu or server management tools)
2. Explore sections not available in Services:
   - **Application Tier**: service account, URLs, logging
   - **Team Project Collections**: manage collections
   - **Scheduled Backups**: configure backup schedule
3. Compare to Azure DevOps Services: notice what is managed by Microsoft in Services vs by you in Server

**Exercise 3 - Set up a local build agent**

1. In the Administration Console or web UI, create a pool called "Local"
2. Download and configure a build agent pointing to your local server
3. Create a simple pipeline (`azure-pipelines.yml`):
```yaml
pool:
  name: Local

steps:
  - script: echo "Running on local agent"
  - script: hostname
```
4. Run the pipeline and verify it runs on your local agent

---

## Common Mistakes

**Not testing backups before you need them**

A backup strategy that has never been tested is not a strategy. The first time you restore from backup should not be during a real disaster. Schedule quarterly restore tests.

**Ignoring the upgrade path**

Skipping a required intermediate version in the upgrade path corrupts databases. Always check the official upgrade path documentation before upgrading. Do not assume you can go from TFS 2015 to the latest Server in one step.

**Running Azure DevOps Server and SQL Server on the same machine for production**

For development and evaluation, a single-machine deployment is fine. For production, SQL Server should be on a separate machine. Running both on the same server creates resource contention and makes hardware upgrades more complex.

**Not keeping agents updated**

Build agents must match the server version closely. After upgrading Azure DevOps Server, update all agents. Agents that fall too far behind the server version may fail to connect or run jobs.

**Using XAML builds after upgrading from TFS**

XAML builds from TFS 2013 and earlier do not work in modern Azure DevOps Server. They must be rewritten as YAML pipelines. This is a significant but one-time effort. Do not upgrade Server without a plan to migrate XAML builds.

**Not monitoring disk space for repositories**

Git repositories grow continuously as teams push code, images and other large files. If the disk fills up, Azure DevOps Server stops accepting pushes. Monitor disk space and plan for growth - especially if teams use Git LFS.

---

## Summary

Azure DevOps Server is the on-premises version of Azure DevOps, the successor to Team Foundation Server. The current release is simply "Azure DevOps Server" (no year designation), released December 2025, operating under Microsoft's Modern Lifecycle Policy.

Choose Server over Services for data sovereignty requirements, air-gapped networks, on-premises Active Directory integration without Azure AD, control over upgrade timing or custom plugin requirements.

System requirements include Windows Server 2019+ or supported Linux, SQL Server 2019+ with Full-Text Search, and optionally Elasticsearch for code search. Single-server deployments suit teams up to 250 users; larger teams should separate the application and database tiers.

Authentication on Server integrates directly with Active Directory (the default), Azure AD or any SAML 2.0 identity provider. No Guard add-on required for SSO.

Upgrading from TFS requires following the official upgrade path - skipping intermediate versions causes database corruption. Always back up before upgrading and test backups quarterly.

Key Server differences from Services: no hosted agents (all builds must use self-hosted agents), no AI features, full administrative control, and on-premises AD integration without Azure subscriptions.

---

## Sources

- [Microsoft Learn: Azure DevOps Server installation](https://learn.microsoft.com/azure/devops/server/install/get-started)
- [Microsoft Learn: Azure DevOps Server release notes](https://learn.microsoft.com/azure/devops/server/release-notes/azuredevopsserver)
- [Microsoft Learn: System requirements](https://learn.microsoft.com/azure/devops/server/requirements)
- [Microsoft Learn: Upgrade from TFS](https://learn.microsoft.com/azure/devops/server/upgrade/get-started)
- [Microsoft Learn: Backup and restore](https://learn.microsoft.com/azure/devops/server/admin/backup/back-up-restore)
- [Microsoft Learn: Install a build agent](https://learn.microsoft.com/azure/devops/pipelines/agents/agents)
- [Microsoft: Visual Studio licensing guidance](https://www.microsoft.com/licensing/guidance/Visual-Studio)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

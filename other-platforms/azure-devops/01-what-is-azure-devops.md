# What is Azure DevOps?

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Azure DevOps is Microsoft's integrated platform for software development teams. It bundles five services - project management, Git hosting, CI/CD pipelines, package management and quality assurance - into a single product with a unified identity system, shared permissions and consistent tooling. You can use one service, all five, or any combination in between.

Understanding what Azure DevOps is requires understanding where it came from. The product has one of the longest continuous development histories in the industry, evolving through multiple names and paradigms over more than two decades. That history explains why Azure DevOps works the way it does and why certain design decisions look the way they look in 2026.

---

## Table of Contents

1. [The long history: from Visual SourceSafe to Azure DevOps](#the-long-history-from-visual-sourcesafe-to-azure-devops)
2. [The five services](#the-five-services)
3. [Azure DevOps Services vs Azure DevOps Server](#azure-devops-services-vs-azure-devops-server)
4. [Market position and who uses Azure DevOps](#market-position-and-who-uses-azure-devops)
5. [Microsoft's strategic positioning: Azure DevOps and GitHub](#microsofts-strategic-positioning-azure-devops-and-github)
6. [Azure DevOps in 2026: the current state](#azure-devops-in-2026-the-current-state)
7. [Key concepts before you start](#key-concepts-before-you-start)
8. [Try It Yourself](#try-it-yourself)
9. [Common Mistakes](#common-mistakes)
10. [Summary](#summary)
11. [Sources](#sources)

---

## The long history: from Visual SourceSafe to Azure DevOps

### Visual SourceSafe (1994-2005)

Microsoft's first version control product was **Visual SourceSafe (VSS)**, acquired from One Tree Software in 1994. VSS was a centralised version control system - developers checked out files, edited them, and checked them back in. Only one person could edit a file at a time without conflicts. VSS had a reputation for database corruption and was widely criticised for poor scalability and reliability, but it was deeply integrated into the Visual Studio ecosystem and widely used despite its problems.

### Team Foundation Server (2005-2018)

Microsoft released **Team Foundation Server (TFS)** in 2005 as a comprehensive replacement for Visual SourceSafe. TFS was a significant architectural departure: instead of a file-locking system, it introduced **Team Foundation Version Control (TFVC)**, a centralised version control system that allowed concurrent edits with proper merge support. More importantly, TFS bundled version control with work item tracking, build automation and reporting into a single integrated platform - the first genuinely integrated DevOps platform from Microsoft, years before the term "DevOps" was coined.

TFS was an on-premises product. Organisations installed it on their own Windows servers with SQL Server as the database backend. It became deeply embedded in enterprise Microsoft shops over the following decade.

Key TFS milestones:
- **2010**: TFS Lab Management added for test environments
- **2012**: Git support added alongside TFVC - TFS became the first major platform to support both TFVC and Git
- **2013**: Agile planning tools matured, kanban boards added
- **2015**: Build system completely rewritten (XAML builds replaced by agent-based builds)
- **2017**: Release Management introduced proper deployment pipelines
- **2018**: YAML pipelines introduced (as preview)

### Visual Studio Team Services (2012-2018)

Microsoft launched the cloud-hosted version of TFS in 2012, initially called **Team Foundation Service**, later renamed **Visual Studio Team Services (VSTS)**. VSTS was a hosted SaaS version of TFS, allowing teams to use TFS capabilities without managing their own server infrastructure.

VSTS diverged from on-premises TFS increasingly over time: cloud features arrived in VSTS months before they shipped in TFS, and the velocity of cloud development accelerated. VSTS introduced features like free private Git repositories, a modern CI/CD system, and integration with the growing Azure cloud platform.

### Azure DevOps (2018-present)

In September 2018, Microsoft rebranded VSTS as **Azure DevOps Services** and simultaneously unbundled the platform into five distinct services. This was a significant strategic decision: instead of a monolithic platform where you had to use everything or nothing, teams could now adopt individual services independently.

The rebrand served multiple purposes. It aligned the product with the Azure brand as Microsoft pushed cloud-first messaging. It acknowledged that "Visual Studio" in the name was misleading - the platform supported any language and IDE, not just Visual Studio. And the unbundled service model made it easier to sell individual components to teams who already had solutions for some parts of the DevOps pipeline.

The on-premises version was simultaneously renamed from **TFS** to **Azure DevOps Server**.

---

## The five services

Azure DevOps consists of five services. They share a unified interface, a single sign-on and a common permission model, but each can be used independently.

### Azure Boards

**Azure Boards** is the project management service. It provides work items (the basic unit of tracked work), Kanban boards, sprint planning, backlogs, queries, delivery plans and reporting.

The work item hierarchy in the default Agile process template:
```
Epic
  └── Feature
        └── User Story
              └── Task
              └── Bug
```

Boards supports multiple process templates: **Agile** (Epics, Features, User Stories, Tasks), **Scrum** (Epics, Features, Product Backlog Items, Tasks) and **CMMI** (Epics, Features, Requirements, Tasks). The choice of template affects work item types and workflow states but can be changed later.

Azure Boards is the most direct Azure DevOps competitor to GitHub Projects, Jira and Linear. It is particularly strong for teams doing formal Scrum or SAFe implementations because of its deep sprint and capacity planning features.

### Azure Repos

**Azure Repos** is the code hosting service. It supports two version control systems:

**Git** - the distributed version control system. Azure Repos Git works like any other Git hosting: clone, push, pull, branch, merge. It supports SSH and HTTPS authentication, pull requests with code review, branch policies and webhooks.

**Team Foundation Version Control (TFVC)** - Microsoft's centralised version control system inherited from TFS. TFVC is a legacy system. Most teams should use Git. TFVC is discussed briefly in [03-azure-repos.md](03-azure-repos.md) for teams who may be migrating from it.

Azure Repos Git is a first-class, production-quality Git hosting service. It is competitive with GitHub and GitLab for core hosting features. Where it differs is in ecosystem: fewer third-party integrations, no equivalent of the GitHub Marketplace, and no community discovery mechanism.

### Azure Pipelines

**Azure Pipelines** is the CI/CD service. It is one of the most capable CI/CD systems available, supporting virtually any language, platform and cloud provider. Key capabilities:

- YAML-defined pipelines committed to the repository (pipeline as code)
- Microsoft-hosted agents running Ubuntu, Windows Server and macOS
- Self-hosted agents on any OS including Windows Server, Linux and macOS
- Multi-stage pipelines with environments, approvals and deployment gates
- Parallel jobs for concurrent test execution
- Pipeline templates for reusable pipeline components
- Integration with Azure services (Azure App Service, AKS, Azure Functions)
- Service connections for authenticating to external services

Azure Pipelines has the strongest Windows and .NET build support of any CI/CD system. Microsoft-hosted Windows agents run on Windows Server 2025 with Visual Studio 2022 included. macOS agents run macOS 15. Ubuntu agents run Ubuntu 24.04.

### Azure Artifacts

**Azure Artifacts** is the package management service. It provides feeds (private package registries) for:

- **NuGet** (.NET packages)
- **npm** (JavaScript/Node.js packages)
- **Maven** (Java packages)
- **Python/pip** (Python packages)
- **Cargo** (Rust packages, currently in preview)
- **Universal Packages** (arbitrary file bundles with versioning)

Feeds can be configured with upstream sources - public registries (npmjs.com, nuget.org, PyPI) that the feed proxies and caches. This provides both private package hosting and a curated, cached view of public packages.

### Azure Test Plans

**Azure Test Plans** is the quality assurance service. It provides:

- Manual test plans and test suites
- Test case management with steps and expected results
- Exploratory testing sessions with the Test and Feedback browser extension
- Running automated tests from within Test Plans
- Test coverage reporting linked to pipelines
- Bug filing directly from test execution with automatic evidence attachment

Test Plans is the most enterprise-focused of the five services. It addresses the formal QA workflows of regulated industries - test case libraries, test execution records, pass/fail audit trails. Smaller teams or those doing only automated testing typically do not need Test Plans.

Test Plans requires the **Basic + Test Plans** licence ($52/user/month), which is the most expensive Azure DevOps licence tier.

---

## Azure DevOps Services vs Azure DevOps Server

The same product ships in two deployment models.

### Azure DevOps Services (cloud)

Hosted at [dev.azure.com](https://dev.azure.com). Managed entirely by Microsoft. You log in with a Microsoft account or Azure Active Directory identity and immediately get access to all five services.

**Advantages of Services:**
- No infrastructure to manage
- Continuously updated (new features ship every sprint, approximately every 3 weeks)
- Globally distributed with high availability
- Scales automatically
- Integrated with Azure cloud services

**Limitations of Services:**
- Your code and data are on Microsoft's infrastructure
- Upgrade timing is controlled by Microsoft
- Some highly specialised integrations are only available on Server

### Azure DevOps Server (on-premises)

Installed on your own Windows or Linux servers. You manage the infrastructure, perform upgrades and are responsible for availability.

**Advantages of Server:**
- Complete data sovereignty - your data never leaves your infrastructure
- Air-gapped deployment possible (no internet connectivity required)
- You control upgrade timing
- Deep integration with on-premises Active Directory
- Can integrate with internal network resources without VPN

**Limitations of Server:**
- Infrastructure overhead: Windows Server or Linux, SQL Server, operational management
- Features lag Services - cloud features often arrive months later
- Annual upgrades required to stay current

The latest release is simply called **Azure DevOps Server** (no year designation), released December 2025. It replaced the previous "Azure DevOps Server 2022" naming convention, shifting to a Modern Lifecycle Policy with continuous support.

---

## Market position and who uses Azure DevOps

### Enterprise Microsoft environments

Azure DevOps is most at home in enterprise organisations running Microsoft technology stacks: Azure cloud infrastructure, .NET or C# applications, Windows Server environments, Active Directory for identity management, and Visual Studio for development. In these environments, Azure DevOps integrates naturally with the existing toolchain without additional configuration.

### Regulated industries

The combination of audit trails (work items linked to commits linked to builds linked to deployments), formal test plans and access control inherited from Active Directory makes Azure DevOps attractive for regulated industries: financial services, healthcare, government and defence. The US government has FedRAMP Moderate authorisation for Azure DevOps, making it a viable choice for federal agencies.

### .NET and Windows-first development shops

Azure Pipelines has the best Windows build support of any hosted CI/CD platform. The hosted Windows agents include Visual Studio 2022, the full Windows SDK, .NET SDK and a comprehensive set of Microsoft tools. For teams building Windows desktop applications, UWP apps, WPF applications or enterprise .NET services, Azure Pipelines is the natural choice.

### Teams migrating from TFS

Many large organisations adopted TFS in the 2005-2015 era and built extensive automation, process templates and tooling around it. Azure DevOps is the natural upgrade path. Rather than migrating to a new platform, these teams migrate to the cloud version of the same product they already use.

---

## Microsoft's strategic positioning: Azure DevOps and GitHub

Microsoft owns both Azure DevOps and GitHub (acquired in 2018 for $7.5 billion). This creates an unusual situation where the company competes with itself.

Microsoft's current positioning is clear: **GitHub is the AI-first developer platform and the strategic future**. Azure DevOps remains actively developed and supported but AI features - GitHub Copilot's coding agent, code review AI, pull request summaries - are not being ported to Azure DevOps.

In February 2025, Microsoft published an official migration playbook recommending teams move their repositories from Azure DevOps Repos to GitHub to access these AI capabilities. The recommended architecture is a **hybrid model**: GitHub for code hosting and AI tooling, Azure DevOps for project management (Boards), pipelines (Pipelines) and package management (Artifacts).

Azure DevOps is not being discontinued. It has a 3-week sprint release cadence, regular new features and a published roadmap. But the strategic direction is clear: if your team wants cutting-edge AI developer tooling integrated into your workflow, GitHub is where Microsoft is investing most heavily.

In 2026, Azure DevOps received the **Azure DevOps MCP Server** (Model Context Protocol), enabling AI assistants like GitHub Copilot and Claude to access Azure DevOps data (work items, pipelines, repos) directly. This represents a middle path: rather than building AI into Azure DevOps natively, Microsoft is enabling external AI tools to connect to it.

---

## Azure DevOps in 2026: the current state

**Azure DevOps Services** ships new features approximately every 3 weeks with sprint updates. Recent notable additions include:

- **New Boards Hub** - the fully refreshed Azure Boards interface became the default for all customers globally in June 2025 (Sprint 259), completing a multi-year UI modernisation
- **Managed DevOps Pools** - a new agent pool system offering Gen 2 VMs, scaling from zero, Key Vault integration and (coming mid-2026) Spot VM instances for up to 90% cost savings
- **GitHub organisation connections** - improved integration allowing up to 2,000 GitHub repositories to be managed alongside Azure DevOps (up from 500)
- **Azure DevOps MCP Server** - public preview March 2026, enabling AI assistants to query ADO data

**Microsoft-hosted agent images in 2026:**

| Image alias | Current OS |
|---|---|
| `ubuntu-latest` | Ubuntu 24.04 LTS |
| `windows-latest` | Windows Server 2025 (with VS 2022) |
| `macos-latest` | macOS 15 Sequoia |

Windows Server 2019 agents were retired December 31, 2025. Ubuntu 20.04 agents were retired May 2025. macOS 14 Sonoma deprecation begins July 2026.

**Azure DevOps Server** released its latest version in December 2025, with Patch 1 in March 2026. It supports SQL Server 2025, improved GitHub organisation connections and REST APIs for GitHub repository management.

---

## Key concepts before you start

### Organisation

An **organisation** is the top-level container in Azure DevOps. It has a URL like `dev.azure.com/your-organisation-name`. An organisation contains projects. Billing happens at the organisation level.

### Project

A **project** is the primary workspace. One project contains repositories, boards, pipelines, test plans and an Artifacts feed. This is different from GitHub where a repository is the primary unit - in Azure DevOps, the project wraps the repository.

Projects can be configured with different process templates (Agile, Scrum, CMMI) and can be public or private.

### Repository

A **repository** lives inside a project. One project can have multiple repositories. This is useful for monorepos (all code in one repo) or multi-repo setups (separate repos for frontend, backend, infrastructure). The first repository in a project is created automatically and named after the project.

### Agent

An **agent** is the compute resource that runs pipeline jobs. Microsoft-hosted agents are managed by Microsoft and spun up fresh for each job. Self-hosted agents are servers you manage that register with Azure DevOps and pick up jobs from a queue.

### Personal Access Token (PAT)

A **PAT** is the primary authentication credential for the Azure DevOps REST API, CLI and HTTPS Git operations. PATs are scoped (you choose what permissions they have) and have expiry dates. They are conceptually identical to GitHub and GitLab personal access tokens.

---

## Try It Yourself

**Exercise 1 - Create an Azure DevOps account**

1. Go to [dev.azure.com](https://dev.azure.com)
2. Click **Start free** (or sign in with an existing Microsoft account)
3. Sign in with a Microsoft account, GitHub account or create a new one
4. Create your first organisation (choose a name - this becomes part of your URL)
5. Create your first project (choose a name and process template - Agile is a good default)
6. Explore the five service icons in the left sidebar: Boards, Repos, Pipelines, Test Plans, Artifacts

**Exercise 2 - Explore the interface**

1. Click **Repos** in the left sidebar to see the Git repository
2. Click **Boards** to see the Kanban board
3. Click **Pipelines** to see where you would configure CI/CD
4. Click **Artifacts** to see the package feed
5. Click **Test Plans** to see the testing interface

**Exercise 3 - Compare with GitHub**

Open [github.com](https://github.com) and [dev.azure.com](https://dev.azure.com) side by side. Find the equivalent of:
- A GitHub repository → Azure Repos
- GitHub Actions → Azure Pipelines
- GitHub Projects → Azure Boards
- GitHub Packages → Azure Artifacts
- A GitHub organisation → An Azure DevOps organisation

Notice how Azure DevOps groups everything under a project, while GitHub treats the repository as the primary unit.

---

## Common Mistakes

**Confusing the project with the repository**

In Azure DevOps, you navigate to a project first, then to a repository within that project. New users often look for the repository list directly and cannot find it. Go to your organisation → click a project → click Repos.

**Creating one project per repository**

Some teams coming from GitHub create one Azure DevOps project per code repository (mirroring the GitHub model where the repo is the primary unit). This creates administrative overhead. Instead, create one project per team or product area, and use multiple repositories within that project.

**Using Azure DevOps Repos when GitHub integration would work better**

If your team is moving toward the GitHub + Azure DevOps hybrid model (GitHub for code, ADO for boards and pipelines), do not also create an Azure DevOps Repos repository. It creates confusion about which system holds the canonical code. Pick one Git hosting platform.

**Not understanding the free tier limits**

The free tier includes 5 Basic users, 1 Microsoft-hosted parallel job with 1,800 minutes/month and unlimited self-hosted jobs. Many teams assume they can run multiple parallel jobs for free - they cannot. A second hosted parallel job costs $40/month.

---

## Summary

Azure DevOps evolved over three decades from Visual SourceSafe (1994) through Team Foundation Server (2005-2018) and Visual Studio Team Services (2012-2018) to become Azure DevOps in 2018. This history explains its deep integration with Microsoft tooling and its strong position in enterprise environments.

The platform comprises five services: Boards (project management), Repos (Git hosting), Pipelines (CI/CD), Artifacts (package management) and Test Plans (quality assurance). Each service can be used independently or together.

In 2026, Azure DevOps Services ships updates every 3 weeks. Microsoft-hosted agents run Ubuntu 24.04, Windows Server 2025 and macOS 15. The platform is actively developed but Microsoft's AI investment is primarily in GitHub, not Azure DevOps. The recommended hybrid architecture is GitHub for code and AI tooling, Azure DevOps for boards, pipelines and packages.

Azure DevOps Server (on-premises) is the latest incarnation of what was Team Foundation Server. It runs on Linux or Windows and uses a Modern Lifecycle Policy with continuous support.

---

## Sources

- [Microsoft: Azure DevOps Services overview](https://azure.microsoft.com/products/devops)
- [Microsoft Learn: What is Azure DevOps?](https://learn.microsoft.com/azure/devops/user-guide/what-is-azure-devops)
- [Microsoft: Azure DevOps sprint release notes](https://learn.microsoft.com/azure/devops/release-notes/features-timeline)
- [Microsoft: Azure DevOps Server release notes](https://learn.microsoft.com/azure/devops/server/release-notes/azuredevopsserver)
- [Microsoft: Microsoft-hosted agents](https://learn.microsoft.com/azure/devops/pipelines/agents/hosted)
- [Microsoft: Azure DevOps pricing](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)
- [Microsoft: Sprint 259 update - New Boards Hub](https://learn.microsoft.com/azure/devops/release-notes/2025/sprint-259-update)
- [Baytech Consulting: The future of DevOps - Azure vs GitHub 2026](https://www.baytechconsulting.com/blog/devops-future-azure-vs-github-2026)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

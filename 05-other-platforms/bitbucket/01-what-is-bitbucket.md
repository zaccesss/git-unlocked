# What is Bitbucket?

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Bitbucket is a Git hosting platform owned by Atlassian. It is the platform that most teams choose when they are already inside the Atlassian ecosystem - when they use Jira for project management, Confluence for documentation and Trello for lightweight planning. No other Git platform integrates with Jira as deeply as Bitbucket does. That integration is not an afterthought or an API connection - it is built into the architecture of how Bitbucket works, and it is the main reason teams choose it over GitHub or GitLab.

This file covers where Bitbucket came from, how it has evolved, what it looks like today and what you need to understand before diving into the rest of the folder.

---

## Table of Contents

1. [The origin story](#the-origin-story)
2. [The Atlassian acquisition](#the-atlassian-acquisition)
3. [Mercurial and the move to Git-only](#mercurial-and-the-move-to-git-only)
4. [Bitbucket Server and the Data Center era](#bitbucket-server-and-the-data-center-era)
5. [Bitbucket in 2026: the current state](#bitbucket-in-2026-the-current-state)
6. [The Atlassian ecosystem advantage](#the-atlassian-ecosystem-advantage)
7. [Who uses Bitbucket and why](#who-uses-bitbucket-and-why)
8. [Bitbucket vs the competition: the one-line summary](#bitbucket-vs-the-competition-the-one-line-summary)
9. [Key concepts before you start](#key-concepts-before-you-start)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## The origin story

Bitbucket was created by **Jesper Nøhr**, a Danish developer based in Sydney, Australia. He launched it in 2008 as a side project - a hosting service for Mercurial repositories. At the time, Git was gaining momentum in open source communities but Mercurial was the version control system many professional developers used and there was no strong hosted option for it. GitHub had just launched earlier that year focused exclusively on Git. Jesper saw a gap and filled it.

The original Bitbucket was built on Django (a Python web framework) and ran as a small startup. It offered free hosting for open source Mercurial repositories with private repositories available on paid plans. The feature set was minimal compared to what exists today - basic repository browsing, a simple issue tracker and the ability to push and pull code.

Despite the modest beginnings, Bitbucket attracted a loyal user base among developers who preferred Mercurial's approach to branching and its cleaner history management compared to Git's more chaotic early tooling. This community gave Bitbucket a foundation to grow from.

---

## The Atlassian acquisition

In **September 2010**, Atlassian acquired Bitbucket. At the time, Atlassian was already the dominant player in developer tooling for enterprise teams - Jira was the market-leading issue tracker, Confluence was widely used for internal documentation and the company had built its business on tools that helped software teams coordinate at scale.

The acquisition made strategic sense for Atlassian immediately. They had tools for planning software (Jira), documenting it (Confluence) and communicating about it - but nothing for hosting the actual code. Bitbucket filled that gap. For Bitbucket, the acquisition provided resources, distribution through Atlassian's existing customer base, and integration with the Jira platform that would eventually become its defining feature.

Atlassian did not kill Bitbucket's independent identity. The brand remained, the product continued to develop and the team grew. What changed was the trajectory: instead of building a GitHub competitor from scratch, Bitbucket became part of an integrated developer toolchain.

> [!NOTE]
> Atlassian also acquired **SourceTree** in 2012, a free Git and Mercurial client for Mac and Windows. SourceTree is now one of the most popular GUI clients for Bitbucket and continues to be offered free of charge. It is covered in the `ides/` folder of this course.

---

## Mercurial and the move to Git-only

Bitbucket supported both Git and Mercurial from 2011 onwards. This dual-VCS support was a significant differentiator: GitHub supported only Git, and GitLab (when it launched in 2011) was also Git-only. Bitbucket's Mercurial support made it the natural choice for teams who had built their workflows around Mercurial.

However, Git's dominance in the industry grew rapidly through the 2010s. Mercurial usage declined steadily, particularly as GitHub's popularity drove more and more tooling to be built Git-first. By the late 2010s, Bitbucket's Mercurial repositories represented a tiny and shrinking fraction of active usage but required ongoing maintenance, documentation and testing.

Atlassian announced in **June 2019** that Bitbucket would sunset Mercurial support. The timeline:

- **June 2019** - announcement: no new Mercurial features, existing repos continue to work
- **February 2020** - creation of new Mercurial repositories blocked
- **May 2020** - all Mercurial repositories deleted

This decision was not controversial in the developer community - it was widely seen as the right call, even by Mercurial users. Atlassian provided migration tooling to help teams convert their Mercurial history to Git before the deadline. Bitbucket became a Git-only platform and has remained so since.

> [!TIP]
> If you are working with a legacy codebase that was once hosted on Bitbucket as Mercurial, the history will have been lost after May 2020 unless your team migrated it to a Git repository before the deadline. You can convert Mercurial history to Git locally using the `fast-export` tool, but only if you still have the original Mercurial repository on your local machine.

---

## Bitbucket Server and the Data Center era

When Atlassian first offered a self-hosted version of Bitbucket, it was called **Stash**. Stash allowed enterprises to run Bitbucket on their own servers - useful for organisations with strict data sovereignty requirements, air-gapped networks or compliance environments where cloud services were not permitted.

In **2015**, Atlassian rebranded Stash to **Bitbucket Server**, bringing it in line with the Bitbucket brand family and making the product relationship clearer. Bitbucket Server and Bitbucket Cloud ran in parallel: Cloud was the hosted service, Server was the self-managed equivalent.

As organisations grew larger and their infrastructure requirements became more complex, Atlassian introduced **Bitbucket Data Center** - a version designed for high availability with active-active clustering, horizontal scaling and better performance for large teams. Data Center is not just a renamed Server; it is architecturally different, supporting multiple application nodes, shared file systems and clustered search.

**Bitbucket Server reached end of life on 15 February 2024.** This date is important to understand. Server is gone - Atlassian no longer provides security patches, bug fixes or support for it. Organisations that were running Bitbucket Server had to migrate to either:

1. **Bitbucket Cloud** - migrate to the hosted service
2. **Bitbucket Data Center** - stay self-hosted but move to the DC version

Data Center continues active development. The current long-term support release is **Bitbucket Data Center 10.2** (shipped March 2026, supported until March 2028). Unlike some other Atlassian Data Center products which face a 2029 end-of-life sunset, Bitbucket Data Center is explicitly excluded from that sunset and continues to receive investment.

> [!IMPORTANT]
> Despite Atlassian announcing end-of-life for Jira, Confluence and several other Data Center products in March 2029, **Bitbucket Data Center is not included in this announcement**. It has an active roadmap and Atlassian has committed to its continued development. Do not conflate the Data Center sunset with Bitbucket Data Center - they are separate decisions.

---

## Bitbucket in 2026: the current state

### Bitbucket Cloud

Bitbucket Cloud is the main product for most users. It runs at bitbucket.org and is offered in three pricing tiers:

**Free tier** - $0 per month

- Up to 5 users in a workspace
- 50 Bitbucket Pipelines minutes per month
- 1 GB total storage
- Basic features: repositories, pull requests, issues, wikis (note: wikis are being sunset in Cloud)
- No AI features, no IP allowlisting, no enforced merge checks

**Standard** - $3.65 per user per month

- Unlimited users
- 2,500 Pipelines minutes per month
- 5 GB LFS storage
- AI-powered pull request descriptions (Atlassian Intelligence / Rovo Dev)
- Bitbucket Packages (container registry, npm, Maven support)

**Premium** - $7.25 per user per month

- Unlimited users
- 3,500 Pipelines minutes per month
- 10 GB LFS storage
- IP allowlisting
- Enforced merge checks
- Required builds before merge
- 99.9% uptime SLA
- Full Atlassian Intelligence features

Additional pipeline minutes can be purchased at $10 per 1,000. Self-hosted runner slots cost $15 per month for premium runner capabilities beyond the free tier of 100 basic runners per workspace.

> [!NOTE]
> Atlassian Guard (formerly called Atlassian Access) provides SSO, SCIM provisioning and advanced identity management. It is a separate paid product: Guard Standard costs $4.20 per user per month on top of any Bitbucket plan. If your organisation requires SSO for Bitbucket, budget for Guard separately.

### Bitbucket Data Center

Bitbucket Data Center is the self-hosted enterprise version. It runs on Linux only (Windows support was dropped in version 8.0). Current requirements include Java 11, Git 2.34 or later, PostgreSQL or MySQL as the database and OpenSearch for search indexing (Elasticsearch support was removed).

Data Center licensing is subscription-based with annual fees. Pricing starts around $4,200 per year for up to 50 users. Unlike Cloud, Data Center gives organisations complete control over their data, network topology and upgrade schedule.

A **Bitbucket Hybrid License** is arriving mid-2026. This will allow organisations to use both Data Center and Cloud under a single licence - useful for teams that need some repositories in the cloud while keeping sensitive code on-premises. All existing Data Center licences are expected to transition to hybrid by March 2029.

### Authentication: API tokens replace app passwords

This is the most important change for anyone who automated Bitbucket workflows before 2025. **App passwords are being permanently retired.**

- **September 2025** - new app password creation blocked
- **9 June 2026** - all existing app passwords stop working

The replacement is **API tokens**, which offer expiry controls, scoped permissions and admin-managed lifecycle. API tokens work the same way app passwords did in scripts and Git credential managers - you use them in place of your Atlassian password for HTTPS authentication. The migration is straightforward: create an API token, replace any stored app passwords in your tools and CI/CD systems.

> [!WARNING]
> If you have scripts, CI/CD pipelines or third-party tools that authenticate to Bitbucket using app passwords, they will break on 9 June 2026. Audit your integrations now and migrate to API tokens. The Bitbucket Cloud changelog at developer.atlassian.com tracks the exact deprecation phases.

### Pipelines runners: the V5 overhaul

Bitbucket Pipelines received a significant runner overhaul in 2025-2026. The new V5 self-hosted runners bring customisable CPU and memory, Docker volume mounts, S3 and GCS cache storage and OpenID Connect support for secretless authentication to cloud providers.

The runner pricing model changed to: up to 100 free basic runners per workspace, with premium runners (advanced orchestration, larger specs) costing $15 per slot per month. Pre-V5 runners will stop working on **3 June 2026** for monthly plans and **3 December 2026** for annual plans.

---

## The Atlassian ecosystem advantage

Bitbucket's strongest differentiator is its place inside the Atlassian platform. Understanding this matters even if you are just evaluating Bitbucket - it explains why teams choose it over technically richer options.

### Jira is the core

Atlassian's Jira is the most widely used project management tool in software development. It is used by companies ranging from small startups to Fortune 100 enterprises for tracking bugs, features, sprints and releases. When a development team uses Jira, they organise their work as issues: a task might be JRA-1234, a bug might be PROJ-5678.

Bitbucket connects to Jira at a level that no other Git platform can match, because they are built by the same company and share the same authentication system (Atlassian accounts), the same user directory and the same underlying platform APIs.

The connection works in both directions:

**From Bitbucket into Jira:** When you mention a Jira issue key (like `PROJ-123`) in a commit message, branch name or pull request title, Bitbucket automatically links that code activity to the Jira issue. In Jira, the Development panel on the issue shows the commits, branches, build status and pull request associated with that issue - without any manual configuration.

**From Jira back to Bitbucket:** When you open a Jira issue and click "Create branch," Jira creates a properly named branch in Bitbucket automatically. When you transition an issue from In Progress to In Review, Jira can automatically link to the pull request. Jira Software's boards show CI/CD build status pulled directly from Bitbucket Pipelines.

This bidirectional integration is called the **Jira Development Panel** and it is the closest thing available to a truly unified development workflow. Teams can see at a glance, from within Jira, what code exists for any issue, what state the review is in and whether the builds passed - without switching context to Bitbucket.

**Smart commits** take this further. Certain keywords in commit messages trigger Jira actions automatically:

```
git commit -m "PROJ-123 #comment Fixed the login redirect loop #done"
```

The `#comment` keyword adds a comment to the Jira issue. The `#done` keyword transitions the issue to Done. No human needs to touch Jira - the developer's commit message does it. Smart commit keywords can also transition issues to specific statuses (`#in-review`, `#qa`) and log time (`#time 2h 30m`).

### Confluence and Trello

Bitbucket also connects with Confluence (Atlassian's wiki and documentation platform) and Trello (a lightweight Kanban board tool). These integrations are less deep than the Jira connection but allow teams to link code repositories to documentation pages and cards. For organisations that use all four products, the result is a coherent toolchain where code, documentation, tasks and boards all share context.

### Atlassian Marketplace

The Atlassian Marketplace hosts thousands of apps that extend the Atlassian platform, including Bitbucket. Popular Bitbucket Marketplace additions include code quality tools (SonarQube, Checkmarx), deployment integrations (AWS, Azure, Kubernetes operators) and additional reporting dashboards. Some of these are free; many are paid. The Marketplace is more extensive for Jira and Confluence than for Bitbucket specifically, but the platform integration means Marketplace apps can work across all products simultaneously.

---

## Who uses Bitbucket and why

Understanding the typical Bitbucket user helps you understand the product's design decisions.

### Teams already in the Atlassian ecosystem

This is the primary use case. A company runs Jira for project management, Confluence for documentation and has been using Atlassian products for years. When they need Git hosting, Bitbucket is the natural choice. The Atlassian account system is already in place, the Jira integration works without setup and the IT team already knows how to administer Atlassian products. Switching to GitHub or GitLab would mean managing a second identity system and building custom integrations to get anything close to Bitbucket's Jira connection.

### Enterprises with data sovereignty requirements

Large organisations in regulated industries - finance, healthcare, government, defence - often cannot use public cloud services for source code. Bitbucket Data Center gives them Git hosting with the same feature set as the cloud product but running entirely on their own infrastructure. This audience was previously served by Bitbucket Server; they now use Data Center.

### Small teams on the free plan

Bitbucket's free plan allows up to 5 users with unlimited private repositories. For a small startup team or a developer working alone, this is genuinely competitive. GitHub's free plan has unlimited users but requires all collaborators to have GitHub accounts. Bitbucket's 5-user limit is more restrictive but the overall package - unlimited private repos, some Pipelines minutes, Jira integration - is attractive for small Atlassian-using teams.

### Who Bitbucket is not for

Bitbucket is a weaker choice in a few scenarios:

- **Open source projects** - GitHub has an overwhelmingly dominant position in the open source community. Discoverability, the contributor ecosystem, the pull request culture and the tooling integrations all favour GitHub for public open source work.
- **Teams that do not use Jira** - Without Jira, Bitbucket's main differentiator disappears. GitHub and GitLab both have richer native project management, better CI/CD tooling, larger Marketplace integrations and bigger communities.
- **AI-native development workflows** - GitHub Copilot is far ahead of Bitbucket's Rovo Dev offering in adoption, capability and IDE integration.

---

## Bitbucket vs the competition: the one-line summary

A full three-way comparison is in [03-bitbucket-vs-github-vs-gitlab.md](03-bitbucket-vs-github-vs-gitlab.md). But if you need to orient yourself quickly:

- **Choose Bitbucket** if your team uses Jira and wants the deepest possible integration between code and project management.
- **Choose GitHub** if you work on open source, want the richest AI developer tooling, or need the widest ecosystem of integrations and community.
- **Choose GitLab** if you want a single platform for code, CI/CD, security scanning and DevSecOps without paying extra for each capability, or if you want to self-host an all-in-one platform.

---

## Key concepts before you start

Before working through the rest of this folder, there are a few Bitbucket-specific concepts worth understanding upfront. They are covered in detail in later files but knowing the vocabulary now will make everything else make more sense.

### Workspaces

A **workspace** is the top-level container for repositories in Bitbucket Cloud. Every repository belongs to a workspace. A workspace can belong to an individual developer or to a team. Workspaces are roughly equivalent to organisations in GitHub or groups in GitLab.

When you sign up to Bitbucket, you get a personal workspace with your username. If you create or join a team, that team has its own workspace. Billing happens at the workspace level - when you upgrade to Standard or Premium, you are upgrading a specific workspace.

Workspaces have their own settings, member lists, access controls and permissions. Members can have different roles in different workspaces.

### Projects

Inside a workspace, **projects** are an optional grouping layer above repositories. A project might group all the backend repositories together, or all the mobile app repositories. This is different from GitHub, where there is no built-in grouping layer between the organisation and individual repositories (GitHub Projects are a different concept - they are Kanban-style boards). In GitLab, the equivalent is groups and subgroups.

Projects in Bitbucket are useful for large workspaces where dozens or hundreds of repositories need to be organised. For small teams, they are optional.

### Pull requests

Bitbucket uses the term **pull request** (PR), the same as GitHub. GitLab calls them merge requests (MRs). The concept is identical: a PR is a proposal to merge one branch into another, accompanied by a code review process. Bitbucket's pull request feature supports inline comments, multiple reviewers, approval workflows, merge strategies (merge commit, squash, fast-forward) and integration with build status checks from Pipelines.

### Pipelines

**Bitbucket Pipelines** is Bitbucket's built-in CI/CD system. Pipelines are defined in a file called `bitbucket-pipelines.yml` in the root of your repository. Commits trigger pipeline runs automatically. The pipeline system runs on Docker containers and supports caching, parallel steps, deployment environments and self-hosted runners. Pipelines are deeply integrated with branch permissions - you can require a pipeline to pass before a pull request can be merged.

### API tokens

From June 2026, **API tokens** are the only way to authenticate to Bitbucket programmatically (HTTPS). You create them in your Atlassian account settings, give them a name and select the permissions they need. API tokens work as a password substitute in Git credential managers, scripts and CI/CD systems. They support expiry dates and can be revoked independently of your Atlassian password.

> [!TIP]
> Create API tokens with the minimum permissions needed. A token used only for reading code does not need write access. If a token is compromised, its limited scope limits the damage.

---

## Try It Yourself

You do not need to install anything to complete these exercises - Bitbucket is a web-based service.

**Exercise 1 - Create an Atlassian account**

1. Go to bitbucket.org
2. Click "Get it free"
3. Sign up using your email address or an existing Google or Microsoft account
4. Atlassian will create an account that works across all Atlassian products (Jira, Confluence, Trello and Bitbucket)
5. Verify your email address

**Exercise 2 - Explore the workspace**

1. After signing in, you land in your personal workspace
2. Look at the left sidebar - you will see your avatar, Repos, Projects, and settings icons
3. Click "Repos" to see the repositories in your workspace (it will be empty for a new account)
4. Click your avatar to access workspace settings
5. Notice the workspace URL: `bitbucket.org/{your-username}`

**Exercise 3 - Find the API token settings**

1. Click your avatar in the bottom left corner
2. Click "Personal settings"
3. Navigate to "Personal Bitbucket settings" in the left menu
4. Click "API tokens" (previously called App passwords)
5. Do not create one yet - just note where the settings live for when you need them later

**Exercise 4 - Browse the Atlassian Marketplace**

1. Visit [marketplace.atlassian.com](https://marketplace.atlassian.com)
2. Filter by "Bitbucket" in the product filter
3. Browse the available apps to get a sense of what integrations exist
4. Note that many apps work across multiple Atlassian products simultaneously

---

## Common Mistakes

**Confusing Bitbucket Server with Bitbucket Data Center**

Bitbucket Server reached end of life on 15 February 2024. If you are reading documentation or forum posts that mention Bitbucket Server, check the date - anything written before 2024 may refer to a product that no longer receives updates. When setting up self-hosted Bitbucket today, use Data Center.

**Using app passwords after June 2026**

App passwords are being deprecated. If you have scripts, automation or third-party tools that use app passwords to authenticate to Bitbucket, they will break on 9 June 2026. Migrate to API tokens. The process is simple: create an API token, update your stored credentials. Do not wait until the deadline.

**Expecting a GitHub Pages equivalent**

Bitbucket does not have a built-in static site hosting service equivalent to GitHub Pages or GitLab Pages. If you want to host a site from a Bitbucket repository, you need to deploy it to an external service (Netlify, Cloudflare Pages, AWS Amplify, Vercel). Bitbucket Pipelines can automate that deployment.

**Assuming the Jira integration is automatic without configuration**

The Jira integration is powerful but requires that your Bitbucket workspace is connected to your Jira site. This connection needs to be set up by an admin. Once connected, features like smart commits and the development panel work automatically - but the initial connection step is required.

**Thinking the free plan has unlimited users**

Bitbucket Cloud's free plan supports up to **5 users per workspace**. GitHub's free plan supports unlimited users (with some feature limitations). If your team has more than 5 people, you need to upgrade to Standard or Premium, or look at alternatives.

**Expecting wiki and native Issues to work long-term**

Bitbucket is sunsetting native Issues and Wikis in Cloud. If you use these features, plan to migrate - Issues to Jira, Wikis to Confluence. Atlassian is steering teams toward the full Atlassian product suite rather than maintaining lightweight standalone alternatives inside Bitbucket.

---

## Summary

Bitbucket was founded in 2008 by Jesper Nøhr as a Mercurial hosting service and acquired by Atlassian in 2010. Over the following decade it gained Git support (2011), rebranded its self-hosted product from Stash to Bitbucket Server (2015), dropped Mercurial support entirely (2020), and retired the Server product in favour of Data Center (end of life February 2024).

In 2026, Bitbucket Cloud offers three tiers (Free, Standard at $3.65/user/month, Premium at $7.25/user/month) with an ongoing transition from app passwords to API tokens. Bitbucket Data Center 10.2 is the current LTS self-hosted release, running on Linux with active development continuing.

Bitbucket's strongest selling point is its place inside the Atlassian ecosystem. The Jira integration - smart commits, the development panel, branch-to-issue creation - is the deepest code-to-project-management connection available on any major Git platform. This integration is the primary reason teams choose Bitbucket over GitHub or GitLab.

Key vocabulary to know: workspaces are the top-level container (like GitHub organisations); projects are an optional grouping layer above repositories; pull requests are the code review mechanism; Pipelines is the CI/CD system; and API tokens are the new way to authenticate programmatically.

Bitbucket is not the right choice for open source projects, teams that do not use Jira, or teams that need the latest AI developer tooling. But for Atlassian-native teams, it provides a cohesive development workflow that is difficult to replicate by stitching together separate tools.

---

## Sources

- [Atlassian: Bitbucket pricing](https://www.atlassian.com/software/bitbucket/pricing)
- [Atlassian: Sunsetting Mercurial support in Bitbucket](https://www.atlassian.com/blog/bitbucket/sunsetting-mercurial-support-in-bitbucket)
- [Atlassian: Bitbucket Cloud transitions to API tokens](https://www.atlassian.com/blog/bitbucket/bitbucket-cloud-transitions-to-api-tokens-enhancing-security-with-app-password-deprecation)
- [Atlassian: Bitbucket Data Center 10.2 release notes](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-10-2-release-notes-1738146526.html)
- [Atlassian: Announcing the next chapter for Bitbucket Pipelines runners](https://www.atlassian.com/blog/bitbucket/announcing-the-next-chapter-for-bitbucket-pipelines-runners)
- [Atlassian: The 2025 year in review](https://www.atlassian.com/blog/bitbucket/the-2025-year-in-review-and-whats-coming-soon)
- [Atlassian: Bitbucket end of support announcements](https://confluence.atlassian.com/bitbucketserver/end-of-support-announcements-776640855.html)
- [Atlassian Community: Bitbucket Data Center 2026 LTS release announcement](https://community.atlassian.com/forums/Data-Center-articles/Announcing-New-Bitbucket-Data-Center-Long-Term-Support-LTS/ba-p/3200774)
- [Atlassian: What is Atlassian Guard](https://www.atlassian.com/software/access)
- [Packt: Bitbucket to no longer support Mercurial](https://www.packtpub.com/en-us/learning/how-to-tutorials/bitbucket-to-no-longer-support-mercurial-users-must-migrate-to-git-by-may-2020)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

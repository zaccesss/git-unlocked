# Bitbucket vs GitHub vs GitLab

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Three platforms dominate professional Git hosting. GitHub is the largest and most community-facing. GitLab is the most complete DevSecOps suite. Bitbucket is the deepest Jira integration available anywhere. Each has a genuine reason to exist and a genuine audience it serves best.

This file does not declare a winner. It gives you a thorough, factual comparison across every dimension that matters when choosing a platform - or when explaining to a colleague or manager why a team chose what it chose. By the end you should be able to describe clearly what each platform does well, where each falls short and which to recommend for a given team's situation.

---

## Table of Contents

1. [High-level identity](#high-level-identity)
2. [Pricing comparison](#pricing-comparison)
3. [Free tier comparison](#free-tier-comparison)
4. [Repository hosting and core Git features](#repository-hosting-and-core-git-features)
5. [Code review: pull requests and merge requests](#code-review-pull-requests-and-merge-requests)
6. [CI/CD comparison](#cicd-comparison)
7. [Project management and planning](#project-management-and-planning)
8. [Security features](#security-features)
9. [AI developer tooling](#ai-developer-tooling)
10. [Self-hosting](#self-hosting)
11. [Ecosystem and integrations](#ecosystem-and-integrations)
12. [Community and open source](#community-and-open-source)
13. [Static site hosting](#static-site-hosting)
14. [Package and container registries](#package-and-container-registries)
15. [Mobile apps and access](#mobile-apps-and-access)
16. [Who should choose what](#who-should-choose-what)
17. [Migration between platforms](#migration-between-platforms)
18. [Try It Yourself](#try-it-yourself)
19. [Common Mistakes](#common-mistakes)
20. [Summary](#summary)
21. [Sources](#sources)

---

## High-level identity

Before the detailed comparison, it helps to understand what each platform is fundamentally trying to be.

**GitHub** is the social network of code. It is where open source lives, where developers build reputations, where the largest ecosystem of tooling integrations exists. It is owned by Microsoft (acquired in 2018 for $7.5 billion). GitHub's strategic direction is increasingly AI-first: GitHub Copilot is the most widely adopted AI coding assistant in the industry and GitHub Actions is the dominant CI/CD platform for open source work. GitHub has over 100 million developers and hosts over 420 million repositories.

**GitLab** is a self-contained DevSecOps platform. Its philosophy is that a development organisation should not need to stitch together a dozen separate tools to build and ship software. GitLab provides source code management, CI/CD, container registry, security scanning, package registry, project management, incident management and more - all in one product, with one data model, one API and one interface. GitLab is publicly traded (GTLB on NASDAQ) and used by more than 30 million registered users including over 50% of the Fortune 100.

**Bitbucket** is the Git platform for Atlassian shops. Its identity is inseparable from the Atlassian ecosystem: Jira, Confluence, Trello and the rest of the Atlassian platform. Bitbucket does not try to be a social network for code (it has no equivalent of GitHub's Explore or the Stars ecosystem) and it does not try to be an all-in-one DevSecOps platform (it delegates project management to Jira and documentation to Confluence). It focuses on being the best possible code hosting and CI/CD layer for teams that already use Atlassian tools.

---

## Pricing comparison

### Cloud pricing (per user per month, 2026)

| Tier           | GitHub                | GitLab               | Bitbucket             |
| -------------- | --------------------- | -------------------- | --------------------- |
| **Free**       | $0                    | $0                   | $0                    |
| **Entry paid** | $4 (Pro, individual)  | $29 (Premium)        | $3.65 (Standard)      |
| **Team/mid**   | $4/user (Team)        | $29/user (Premium)   | $3.65/user (Standard) |
| **Enterprise** | $21/user (Enterprise) | ~$99/user (Ultimate) | $7.25/user (Premium)  |

Bitbucket Standard at $3.65/user/month is the cheapest paid tier among the three for teams. However, this comparison is incomplete without factoring in what each paid tier includes.

**GitHub Team ($4/user/month)** includes: 3,000 Actions minutes/month, 2 GB Packages storage, required reviewers, branch protection, CODEOWNERS, draft pull requests, protected tags, code owners, GitHub Discussions and Wikis. No native security scanning, no container registry beyond the basic Packages.

**GitLab Premium ($29/user/month)** includes: merge request approvals, CODEOWNERS enforcement, multiple reviewers, epics, roadmaps, scoped labels, burndown charts, iterations, SAML SSO, merge trains, code quality reports, 50,000 CI/CD minutes/month and much more. GitLab Premium is expensive but it replaces tools that many teams would otherwise buy separately.

**Bitbucket Standard ($3.65/user/month)** includes: 2,500 Pipelines minutes/month, 5 GB LFS storage, AI pull request descriptions and Bitbucket Packages. The low per-user price is attractive but the feature depth at Standard is more limited than GitHub Team or GitLab Premium.

**The real cost comparison**

Headline per-user pricing is misleading without considering add-ons:

- GitHub adds GitHub Advanced Security (GHAS) at $49/user/month for secret scanning, code scanning and Dependabot on private repos. Enterprise customers get 50 included.
- GitLab Ultimate bundles all security scanning - no add-on required.
- Bitbucket requires Atlassian Guard for SSO at $4.20/user/month (Guard Standard) on top of any Bitbucket plan. GitHub Enterprise and GitLab Premium/Ultimate include SSO.
- Bitbucket requires Jira for robust project management. If a team uses GitHub or GitLab, native project management tools are included at no extra cost.

A team with 20 developers doing serious work might pay:

- **GitHub Enterprise + GHAS**: ~$70/user/month = $1,400/month
- **GitLab Ultimate**: ~$99/user/month = $1,980/month
- **Bitbucket Premium + Guard Standard**: ~$11.45/user/month = $229/month (but without integrated security scanning, which would require a third-party tool or Jira-adjacent product)

---

## Free tier comparison

| Feature                | GitHub Free                   | GitLab Free                   | Bitbucket Free             |
| ---------------------- | ----------------------------- | ----------------------------- | -------------------------- |
| **Private repos**      | Unlimited                     | Unlimited                     | Unlimited                  |
| **Users**              | Unlimited                     | Unlimited                     | Up to 5 per workspace      |
| **CI/CD minutes**      | 2,000/month                   | 400/month                     | 50/month                   |
| **Storage**            | 500 MB Actions / 1 GB LFS     | 5 GB                          | 1 GB total                 |
| **Container registry** | Packages (500 MB)             | Container registry (included) | Packages (limited)         |
| **Pages**              | Yes (public only without Pro) | Yes                           | No                         |
| **SSO**                | No                            | No                            | No (requires Guard add-on) |
| **Security scanning**  | Basic Dependabot alerts       | Limited SAST                  | No                         |
| **Issue tracker**      | Yes                           | Yes                           | Yes (being sunset)         |
| **Wiki**               | Yes                           | Yes                           | Yes (being sunset)         |
| **Project management** | GitHub Projects (Kanban)      | Issue boards                  | Requires Jira              |
| **Mobile app**         | Yes                           | Yes                           | Limited                    |

GitHub's free tier has the best CI/CD allocation (2,000 minutes), no user limit and includes GitHub Pages. GitLab's free tier has the best storage and a usable container registry. Bitbucket's free tier has the tightest restrictions (5 users, 50 minutes) but is genuinely useful for a small Atlassian-native team.

---

## Repository hosting and core Git features

All three platforms provide solid, production-quality Git hosting. The core Git features - cloning, pushing, branching, tagging, releases, LFS, repository mirroring and webhooks - work well on all three. The differences are in organisation structure and discovery.

### Organisation structure

**GitHub**: Organisations contain repositories. No native grouping layer below organisations (GitHub Projects are Kanban boards, not a structural layer). Simple and flat.

**GitLab**: Groups contain subgroups and projects (repositories). Subgroups allow nested organisational hierarchies - a company group can have a backend subgroup and a frontend subgroup, each with their own repositories, permission sets, CI/CD variables and runners. More powerful for large organisations.

**Bitbucket**: Workspaces contain projects which contain repositories. The Project layer is optional but useful for grouping related repositories within a large workspace. Similar to GitLab's structure but one level less deep.

### Repository discovery

**GitHub**: Repositories can be starred, forked and explored. The GitHub Explore page, trending repositories and topic tags make it easy to discover interesting public projects. GitHub has by far the best discovery experience for open source work.

**GitLab**: GitLab.com has a public projects section but discovery is weaker than GitHub. The platform is not designed primarily for community exploration.

**Bitbucket**: Bitbucket has virtually no public discovery mechanism. It is designed as a private team tool, not a community platform. You cannot easily browse popular Bitbucket repositories or find interesting public projects.

### Forking

All three platforms support forking. The workflows differ slightly:

- **GitHub**: Fork to your personal account or an organisation, submit a pull request back to the upstream.
- **GitLab**: Fork to your personal namespace or a group. Merge requests back to upstream. GitLab shows fork relationships in the UI.
- **Bitbucket**: Fork to your workspace. Pull requests back to the original. Bitbucket tracks fork relationships and shows them on the repository page.

---

## Code review: pull requests and merge requests

All three platforms have mature code review tools. The terminology differs: GitHub and Bitbucket call them pull requests; GitLab calls them merge requests. The concept is identical.

### Comparison of code review features

| Feature                   | GitHub                | GitLab                      | Bitbucket                          |
| ------------------------- | --------------------- | --------------------------- | ---------------------------------- |
| Inline comments           | Yes                   | Yes                         | Yes                                |
| Suggested changes         | Yes                   | Yes                         | No                                 |
| Required reviewers        | Team plan+            | Premium+                    | Free (default reviewers)           |
| Draft PRs/MRs             | Yes                   | Yes                         | Yes                                |
| Review threads/resolution | Yes                   | Yes                         | Yes                                |
| CODEOWNERS                | Yes (enforced: Team+) | Yes (enforced: Premium+)    | Default reviewers (similar)        |
| Merge methods             | Merge, squash, rebase | Merge, squash, fast-forward | Merge commit, squash, fast-forward |
| Merge queues              | Enterprise            | Premium+                    | No                                 |
| PR templates              | Yes                   | Yes                         | Yes                                |
| Auto-assign reviewers     | Yes (with CODEOWNERS) | Yes                         | Yes (default reviewers)            |
| Review expiry             | No                    | No                          | Yes (re-approval on new commits)   |

**Suggested changes** (GitHub and GitLab) let reviewers propose specific code edits directly in the review interface - the author can apply them with a single click. Bitbucket does not have this feature; reviewers must describe changes in comments and authors must implement them manually. This is a meaningful gap for teams doing detailed code review.

**Default reviewers** in Bitbucket automatically add specified users as reviewers on any PR targeting a specific branch. This is similar to CODEOWNERS but configured per workspace or repository rather than per file path. It is simpler but less granular than CODEOWNERS.

**Merge queues** (GitHub Enterprise, GitLab Premium) manage concurrent PRs targeting the same branch by queuing them and testing them in combination, preventing integration failures when multiple PRs are merged in quick succession. This is a sophisticated feature for large teams with high merge frequency. Bitbucket does not yet have an equivalent.

---

## CI/CD comparison

### Architecture comparison

**GitHub Actions**: Event-driven workflows defined in YAML files in `.github/workflows/`. Jobs run on GitHub-hosted runners (Ubuntu, Windows, macOS) or self-hosted runners. The Marketplace has over 20,000 reusable actions. GitHub Actions is the dominant CI/CD system in open source.

**GitLab CI/CD**: Pipelines defined in `.gitlab-ci.yml`. Stages, jobs and dependencies are defined declaratively. GitLab Runner (the agent) can run on any OS and any infrastructure. Built-in deployment environments, review apps and advanced pipeline features (DAG, parent-child pipelines, merge trains). Arguably the most powerful CI/CD system of the three for complex enterprise workflows.

**Bitbucket Pipelines**: Pipelines defined in `bitbucket-pipelines.yml`. Steps run in Docker containers. Simpler than the other two - suitable for most use cases but lacks some advanced orchestration features. Pipes (pre-built integrations) speed up common deployment tasks.

### Hosted runner comparison (2026)

| Spec        | GitHub                      | GitLab                | Bitbucket            |
| ----------- | --------------------------- | --------------------- | -------------------- |
| **Linux**   | Ubuntu 24.04 (2-core, 7 GB) | Ubuntu (2-core)       | Ubuntu (unspecified) |
| **Windows** | Windows Server 2025         | Windows (limited)     | No                   |
| **macOS**   | macOS 15 (3-core, 14 GB)    | macOS (limited, paid) | No                   |
| **ARM**     | Yes (GitHub-hosted)         | Limited               | No                   |
| **GPU**     | Yes (larger runners)        | Limited               | No                   |

GitHub has by far the strongest hosted runner offering. Windows and macOS runners on GitHub are first-class. GitLab macOS runners require GitLab SaaS Premium. Bitbucket Pipelines only offers Linux-based hosted runners - for Windows or macOS builds, you must use self-hosted runners.

### CI/CD minutes comparison

| Plan           | GitHub                    | GitLab                  | Bitbucket              |
| -------------- | ------------------------- | ----------------------- | ---------------------- |
| **Free**       | 2,000/month               | 400/month               | 50/month               |
| **Entry paid** | 3,000/month (Team)        | 10,000/month (Premium)  | 2,500/month (Standard) |
| **Top paid**   | 50,000/month (Enterprise) | 50,000/month (Ultimate) | 3,500/month (Premium)  |

Bitbucket's free allocation (50 minutes) is significantly lower than GitHub (2,000) and GitLab (400). At the paid tiers, Bitbucket Standard and GitLab Premium offer comparable allocations for the user count, but GitLab's $29/user price gets you 10,000 minutes vs Bitbucket Standard's 2,500 at $3.65/user.

### Reusable components

**GitHub**: The Actions Marketplace hosts 20,000+ reusable actions. Community-contributed, covering deployment to every major cloud provider, code quality tools, testing frameworks and more.

**GitLab**: CI/CD components (newer), CI/CD templates (older, built-in). Smaller community ecosystem than GitHub but first-party templates are high quality.

**Bitbucket**: Pipes are pre-built integration steps (AWS, Azure, GCP, Snyk, Slack and dozens more). Fewer third-party contributions than GitHub but the official pipes are well-maintained.

---

## Project management and planning

| Feature            | GitHub                  | GitLab                  | Bitbucket                      |
| ------------------ | ----------------------- | ----------------------- | ------------------------------ |
| Issues             | Yes, native             | Yes, native             | Being sunset (migrate to Jira) |
| Kanban boards      | GitHub Projects         | Issue boards            | Requires Jira                  |
| Milestones         | Yes                     | Yes                     | Yes (limited)                  |
| Epics              | No (use Projects)       | Yes (Premium+)          | Requires Jira                  |
| Roadmaps           | GitHub Projects         | Yes (Premium+)          | Requires Jira                  |
| Sprints/iterations | No                      | Yes (Premium+)          | Requires Jira                  |
| Time tracking      | No                      | Yes                     | Requires Jira                  |
| Custom fields      | GitHub Projects         | Yes                     | Requires Jira                  |
| Jira integration   | Available (via API/app) | Available (via API/app) | Native, deepest available      |

This is the starkest difference in the comparison. **GitHub and GitLab have native project management tools included in their platforms.** Bitbucket is sunsetting its native Issues and Wikis and explicitly pointing teams toward Jira and Confluence.

For teams that already use Jira, Bitbucket's position is defensible: why maintain a mediocre issue tracker when you can integrate deeply with the world's most widely used issue tracking system? For teams that do not use Jira, Bitbucket's project management story is weak.

GitLab's project management is strongest in its paid tiers (epics, roadmaps, iterations, burndown charts) but even at the free tier it is more capable than Bitbucket's offering.

---

## Security features

| Feature             | GitHub                                  | GitLab                          | Bitbucket                 |
| ------------------- | --------------------------------------- | ------------------------------- | ------------------------- |
| Secret scanning     | Yes (free for public, GHAS for private) | Free tier (limited)             | Yes (Standard+)           |
| Dependency scanning | Dependabot (free)                       | Yes (Ultimate for full)         | Limited (via pipes)       |
| SAST                | GHAS ($49/user add-on)                  | Free (limited), Ultimate (full) | No native                 |
| DAST                | GHAS                                    | Ultimate                        | No                        |
| Container scanning  | GHAS                                    | Ultimate                        | No                        |
| IP allowlisting     | Enterprise                              | Premium                         | Premium                   |
| 2FA enforcement     | Organisation level                      | Group level                     | Workspace level (Premium) |
| Audit log           | Enterprise                              | Premium                         | Premium                   |
| SSO (SAML)          | Team+                                   | Premium+                        | Guard add-on              |

GitLab has the most comprehensive native security scanning, particularly at Ultimate tier where SAST, DAST, dependency scanning, secret detection, fuzz testing and container scanning are all included. GitHub's security features are strong but require the GHAS add-on for private repositories. Bitbucket has the weakest native security scanning and relies on third-party pipes and Marketplace apps.

For security-conscious teams at GitLab Ultimate, the included security tools can replace separate products like Snyk, Checkmarx or Aqua Security. This is a meaningful cost justification for the higher Ultimate price.

---

## AI developer tooling

| Feature            | GitHub                                     | GitLab                          | Bitbucket                    |
| ------------------ | ------------------------------------------ | ------------------------------- | ---------------------------- |
| AI code completion | Copilot (paid add-on)                      | Duo Code Suggestions (Premium+) | Rovo Dev (Standard+)         |
| AI chat assistant  | Copilot Chat                               | Duo Chat                        | Rovo Chat                    |
| AI PR/MR summaries | Copilot                                    | Duo                             | Rovo Dev                     |
| AI code review     | Copilot (Enterprise)                       | Duo (Ultimate)                  | Limited                      |
| AI security fixes  | Copilot Autofix (Enterprise)               | Duo Vulnerability Explanation   | No                           |
| IDE extensions     | VS Code, JetBrains, Neovim, Vim, many more | VS Code, JetBrains              | VS Code, JetBrains (limited) |

**GitHub Copilot** is the market leader in AI coding assistance by adoption, capability and ecosystem. It integrates with virtually every major IDE and editor. GitHub Copilot Individual costs $10/month or $100/year. Enterprise tier includes Copilot Autofix (automatic security fix suggestions) and fine-tuned models. Copilot has the deepest context about your codebase when used within GitHub itself.

**GitLab Duo** (Code Suggestions and Duo Chat) is included in GitLab Premium and Ultimate. It is a genuine competitor with solid code completion and a capable chat interface. Duo Chat in the web UI can explain code, suggest fixes and summarise merge requests. Duo is less mature than Copilot but is included in the plan price rather than being an additional charge.

**Atlassian Rovo Dev** (formerly Atlassian Intelligence) is Bitbucket's AI offering. It can generate pull request descriptions and summary comments. It is less capable than Copilot and Duo for code completion. IDE integration is limited compared to GitHub and GitLab. AI features in Bitbucket are improving but Atlassian has been transparent that AI is not currently a primary focus for Bitbucket - it is more central to Jira and Confluence within the Atlassian suite.

---

## Self-hosting

| Platform      | Self-hosted product          | Price model               | OS support | Min requirements      |
| ------------- | ---------------------------- | ------------------------- | ---------- | --------------------- |
| **GitHub**    | GitHub Enterprise Server     | Per-user licence          | Linux      | 8-core, 32 GB RAM     |
| **GitLab**    | GitLab CE (free) / EE (paid) | Free (CE) / per-user (EE) | Linux      | 4-core, 4 GB RAM (CE) |
| **Bitbucket** | Bitbucket Data Center        | Annual subscription       | Linux only | 4-core, 8 GB RAM      |

**GitLab** has the strongest self-hosting story. GitLab Community Edition (CE) is free, open source (MIT licence) and provides a substantial feature set. GitLab Enterprise Edition (EE) adds premium features with a per-user licence. The same codebase runs on-premises and in the cloud, making GitLab the natural choice for teams that want full control with no vendor lock-in.

**GitHub Enterprise Server** is mature and battle-tested but expensive. It requires a per-user licence with no free tier and has higher minimum hardware requirements. GHES is typically used by large enterprises with strict data residency requirements.

**Bitbucket Data Center** provides a solid self-hosted option with active development (the current LTS is 10.2, released March 2026). It runs on Linux only and uses annual subscription pricing. It is more expensive per user than GitLab EE for equivalent features but makes sense for organisations already on Atlassian's Data Center platform (Jira DC, Confluence DC).

---

## Ecosystem and integrations

| Dimension                   | GitHub                            | GitLab                            | Bitbucket                                                      |
| --------------------------- | --------------------------------- | --------------------------------- | -------------------------------------------------------------- |
| Marketplace apps            | 20,000+ GitHub Apps and Actions   | Limited (integrations via API)    | Atlassian Marketplace (thousands, shared with Jira/Confluence) |
| Jira integration            | Third-party / GitHub for Jira app | Third-party / GitLab for Jira app | Native, deepest available                                      |
| Slack integration           | Native                            | Native                            | Via Atlassian                                                  |
| Cloud provider integrations | AWS, Azure, GCP (first-class)     | AWS, Azure, GCP                   | AWS, Azure, GCP (via Pipelines pipes)                          |
| IDE integrations            | Very broad                        | Broad                             | Limited                                                        |

GitHub's ecosystem advantage is strongest for general developer tooling: IDE plugins, testing frameworks, deployment tools, monitoring systems and more are built for GitHub first. The GitHub API and webhook system are the most widely targeted by third-party developers.

Bitbucket's ecosystem advantage is specifically within Atlassian. The Atlassian Marketplace has thousands of apps that work across Jira, Confluence and Bitbucket simultaneously. If a team uses all three products, Marketplace apps can serve all of them through one purchase and one management interface.

---

## Community and open source

GitHub is the undisputed home of open source software. Virtually every major open source project - Linux kernel, VS Code, React, TypeScript, Node.js, Python, Rust - uses GitHub as its primary hosting and collaboration platform. If you contribute to open source, your GitHub profile and contribution history are visible to potential employers and collaborators.

GitLab hosts some significant open source projects (GitLab itself is open source and hosted on GitLab.com) but has a smaller community footprint than GitHub.

Bitbucket has minimal open source community presence. It has no equivalent of GitHub's Stars, Topics or Explore features. Developers building reputations through open source contribution should maintain a GitHub presence regardless of which platform their employer uses.

---

## Static site hosting

| Platform      | Service      | Free | Custom domain | HTTPS               |
| ------------- | ------------ | ---- | ------------- | ------------------- |
| **GitHub**    | GitHub Pages | Yes  | Yes           | Yes (Let's Encrypt) |
| **GitLab**    | GitLab Pages | Yes  | Yes           | Yes (Let's Encrypt) |
| **Bitbucket** | None         | N/A  | N/A           | N/A                 |

Bitbucket has no static site hosting. For teams that want to host documentation, project websites or demos directly from a repository, GitHub Pages and GitLab Pages are available for free. Bitbucket users deploy to external services (Netlify, Cloudflare Pages, Vercel, AWS Amplify) using Bitbucket Pipelines.

---

## Package and container registries

| Platform      | Registry                                     | Package types                                                                                  |
| ------------- | -------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| **GitHub**    | GitHub Packages                              | npm, Maven, Gradle, NuGet, RubyGems, Docker/OCI                                                |
| **GitLab**    | GitLab Package Registry + Container Registry | npm, Maven, Gradle, NuGet, PyPI, Composer, Conan, Helm, Terraform modules, Docker/OCI, generic |
| **Bitbucket** | Bitbucket Packages (Standard+)               | Docker/OCI (Container), npm, Maven (in progress)                                               |

GitLab has the most complete package registry, supporting more package types than either competitor. GitHub Packages is mature and well-integrated with GitHub Actions. Bitbucket Packages launched in 2025 with container support and is expanding - it is the newest and least complete of the three.

---

## Mobile apps and access

All three platforms have mobile apps but with different levels of capability:

**GitHub**: Native iOS and Android apps with meaningful functionality - reviewing pull requests, browsing code, managing issues, merging PRs, viewing notifications. The GitHub mobile app is one of the most capable developer mobile experiences available.

**GitLab**: iOS and Android apps exist but are more limited than GitHub's. The mobile experience is primarily for viewing and notifications rather than full workflow management.

**Bitbucket**: A mobile app exists but is relatively limited. Bitbucket's primary interface is the web browser. The Jira mobile app is far more capable for mobile project management work.

---

## Who should choose what

### Choose Bitbucket if:

- Your team already uses Jira and the deep Jira integration is a priority
- You use multiple Atlassian products (Confluence, Trello) and want a unified ecosystem
- You are running Atlassian Data Center and want self-hosted Git hosting from the same vendor
- Your organisation has an existing Atlassian license agreement and can extend it to Bitbucket

### Choose GitHub if:

- You work on open source projects or want visibility in the developer community
- You want the most mature AI coding assistant (GitHub Copilot)
- You need the broadest ecosystem of integrations and community tooling
- Your team does not use Jira and does not need deep project management integration
- You want the best CI/CD free tier (2,000 minutes)

### Choose GitLab if:

- You want a single platform for source code, CI/CD, security scanning and DevSecOps without buying additional products
- You need strong self-hosting with a free open source option (GitLab CE)
- Your team values the "one tool" philosophy over best-of-breed integrations
- Security scanning (SAST, DAST, dependency scanning) is a core requirement and you do not want to pay for it separately
- You need advanced CI/CD features like parent-child pipelines, DAG or merge trains

### Mixed use cases

In practice, many organisations use more than one platform. A company might use Bitbucket for internal proprietary code (with Jira integration) while maintaining a GitHub presence for open source contributions and community engagement. There is no rule that says a developer or organisation must use only one platform.

---

## Migration between platforms

Switching platforms is possible but not trivial. The Git repository itself is easy to migrate - it is just a Git remote and `git push` to a new remote is all that is needed to move the code. The difficult parts are:

- **Pull requests and issues** - not transferable automatically. Tools like GitHub's repository import feature, GitLab's importers and third-party migration tools (Babelfish, Monkey-Patch) can help.
- **CI/CD pipelines** - syntax differs between GitHub Actions, GitLab CI/CD and Bitbucket Pipelines. Pipelines must be rewritten, not just copied.
- **Webhooks and integrations** - all webhook URLs and API integrations must be updated to point to the new platform.
- **Team permissions and access control** - must be recreated on the new platform.
- **Git LFS** - LFS objects must be migrated separately. The Git repository history references LFS pointers but the actual objects live in LFS storage.

All three platforms provide import tools for repositories from their competitors:

- GitHub: Import from Bitbucket, GitLab or any Git URL
- GitLab: Import from GitHub, Bitbucket (Cloud and Server) and others
- Bitbucket: Import from GitHub, GitLab or any Git URL

---

## Try It Yourself

**Exercise 1 - Compare free plans hands-on**

Create a free account on all three platforms (if you do not already have them):

- github.com
- gitlab.com
- bitbucket.org

Create a simple test repository on each. Push the same commit to all three. Compare:

- How does the UI feel on each?
- Where is the CI/CD setup?
- What does the repository page look like?

**Exercise 2 - Compare CI/CD syntax**

Take a simple pipeline that runs a test (or just echoes a message) and write it in all three syntaxes:

GitHub Actions (`.github/workflows/test.yml`):

```yaml
name: Test
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Tests passing"
```

GitLab CI/CD (`.gitlab-ci.yml`):

```yaml
test:
  image: ubuntu:latest
  script:
    - echo "Tests passing"
```

Bitbucket Pipelines (`bitbucket-pipelines.yml`):

```yaml
pipelines:
  default:
    - step:
        name: Test
        image: ubuntu:latest
        script:
          - echo "Tests passing"
```

Notice the structural similarities and differences between the three.

**Exercise 3 - Explore the Jira integration (if you have a Jira account)**

If you have access to a Jira project:

1. Connect it to a Bitbucket workspace in workspace settings
2. Create a branch in Bitbucket named after a Jira issue key (e.g. `PROJ-1-my-feature`)
3. Check the Jira issue's Development panel to see it reflected there

---

## Common Mistakes

**Assuming the cheapest per-user price is the cheapest total cost**

Bitbucket Standard at $3.65/user is the lowest headline price. But a team also needs Jira ($7.75/user/month for Jira Standard) and potentially Atlassian Guard ($4.20/user/month) to get equivalent functionality to what GitHub Team ($4/user) includes. Always calculate total toolchain cost, not individual product cost.

**Choosing GitHub for a team that does not do open source**

GitHub's advantages (community, discovery, open source ecosystem) are irrelevant for teams building private commercial software. GitLab or Bitbucket may be better choices depending on the team's existing toolchain and requirements.

**Expecting to migrate pipelines automatically**

GitHub Actions syntax, GitLab CI/CD syntax and Bitbucket Pipelines syntax are all different. There is no automatic converter. When migrating platforms, allocate time to rewrite CI/CD pipelines - it is not a copy-paste job.

**Treating GitLab's all-in-one pitch as inherently superior**

GitLab Ultimate is expensive. For many teams, the all-in-one approach means paying for features they do not need. A team that is happy with GitHub Actions for CI/CD and does not need integrated security scanning is not getting value from GitLab Ultimate's pricing. Match the tool to the actual requirements.

**Using Bitbucket for open source and expecting community engagement**

Bitbucket has no meaningful open source discovery or community features. Public repositories on Bitbucket are publicly accessible but do not appear in trending lists, cannot be meaningfully explored and will attract far fewer external contributors than the same project hosted on GitHub.

---

## Summary

GitHub, GitLab and Bitbucket each serve a distinct primary audience. GitHub is the social network and open source hub of the software development world, with the strongest AI tooling and the broadest ecosystem. GitLab is the all-in-one DevSecOps platform that bundles the most features into a single product, particularly strong for security scanning and self-hosting. Bitbucket is the Git platform for Atlassian shops, with the deepest Jira integration available anywhere and a coherent toolchain story for teams already using Jira and Confluence.

Pricing comparisons must account for the full toolchain: Bitbucket's low per-user price does not include project management (Jira), SSO (Guard) or security scanning. GitHub's mid-range price includes more out of the box. GitLab Ultimate is expensive but replaces multiple separate products.

All three platforms support the same core Git workflows. The differences are in the surrounding ecosystem: code review sophistication, CI/CD capability, project management, security tooling, AI features and community. Choose based on where your team currently lives and what additional value the platform's ecosystem provides on top of Git hosting itself.

---

## Sources

- [GitHub: Pricing](https://github.com/pricing)
- [GitLab: Pricing](https://about.gitlab.com/pricing/)
- [Atlassian: Bitbucket pricing](https://www.atlassian.com/software/bitbucket/pricing)
- [Atlassian: Atlassian Guard pricing](https://www.atlassian.com/software/access)
- [GitHub: GitHub Actions runner images](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners)
- [GitLab: Feature comparison](https://about.gitlab.com/features/)
- [Atlassian: Bitbucket vs GitHub vs GitLab](https://www.atlassian.com/git/tutorials/bitbucket-vs-github-vs-gitlab)
- [Microsoft Learn: Azure Pipelines hosted agents](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/hosted)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

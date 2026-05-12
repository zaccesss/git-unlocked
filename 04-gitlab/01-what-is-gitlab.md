# What is GitLab?

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

GitLab is a complete DevSecOps platform delivered as a single application. Where GitHub connects best-of-breed third-party tools through a marketplace, GitLab bundles everything into one product with a unified data model: code hosting, CI/CD pipelines, security scanning, project management, container registries, package hosting, AI assistance and more. It is used by over 40 million registered users, more than 50% of the Fortune 100 and some of the most security-conscious organisations on the planet, including banks, defence contractors and government agencies.

This file explains where GitLab came from, how it is structured, what makes it different from GitHub, who uses it and where it stands in 2026.

---

## Table of Contents

- [Git vs GitLab - the essential distinction](#git-vs-gitlab---the-essential-distinction)
- [The founding story](#the-founding-story)
- [How GitLab grew](#how-gitlab-grew)
- [The Microsoft moment and what it meant for GitLab](#the-microsoft-moment-and-what-it-meant-for-gitlab)
- [The IPO and life as a public company](#the-ipo-and-life-as-a-public-company)
- [GitLab in 2026](#gitlab-in-2026)
- [The single application philosophy](#the-single-application-philosophy)
- [What GitLab adds on top of Git](#what-gitlab-adds-on-top-of-git)
- [GitLab editions and plans](#gitlab-editions-and-plans)
- [GitLab for education and open source](#gitlab-for-education-and-open-source)
- [Who uses GitLab and why](#who-uses-gitlab-and-why)
- [GitLab as a company](#gitlab-as-a-company)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Git vs GitLab - the essential distinction

Before anything else, one thing must be crystal clear: **Git and GitLab are not the same thing, and one does not require the other.**

**Git** is version control software. You install it on your computer and it tracks changes to files locally. Linus Torvalds wrote it in 2005 for the Linux kernel project. It is free, open source, has no web interface and works entirely on your local machine without an internet connection. When you run `git commit` or `git branch`, you are using Git - it has nothing to do with GitLab, GitHub or any other platform.

**GitLab** is a hosting platform and development toolchain built on top of Git. It gives Git repositories a home on remote servers, adds a web interface for browsing and reviewing code and layers a complete software development toolchain on top: merge requests, CI/CD pipelines, security scanning, project boards, package registries and more.

The relationship is this: Git is the engine that moves code around. GitLab is the platform that hosts it, collaborates around it, tests it, secures it and deploys it.

> [!NOTE]
> GitHub and Bitbucket do the same core job as GitLab: they host Git repositories and add collaboration tools. The fundamental Git workflow is identical across all three platforms. The platform-specific features differ significantly - and those differences matter a great deal in professional environments. GitHub is covered in the `github/` folder of this course.

You can use Git without GitLab entirely - many teams host their own Git servers using bare repositories over SSH with no web platform at all. You can also use GitLab and never touch the terminal directly, using only the web interface. In practice, professional developers do both: they use Git locally and push to GitLab for collaboration, CI/CD and code review.

---

## The Founding Story

GitLab did not begin as a company. It began as a side project built out of frustration.

In **2011**, **Dmytro (Dmitriy) Zaporozhets**, a Ukrainian software developer, was working at a consulting firm in Kharkiv, Ukraine. The firm had around 200 employees who needed to share and collaborate on code. GitHub existed but was still in its early years and not well-suited to private, self-hosted deployments. Other alternatives were expensive, slow or built around older version control systems.

So Zaporozhets built his own tool over a weekend. He wrote it in **Ruby on Rails**, put it on a public repository and released it as open source under the MIT licence on **22 September 2011**. The first commit message was simply: "Initial commit."

The project was called GitLab. It was a web application that gave teams a self-hosted platform for Git repositories with a basic web interface. Nothing more than that, initially.

In **2012**, **Sytse "Sid" Sijbrandij**, a Dutch entrepreneur, stumbled across GitLab on Hacker News. He was immediately taken by the concept: an open-source, self-hosted alternative to GitHub that any organisation could run on their own servers. He began contributing code. The two connected directly, and Sijbrandij later described what happened next with characteristic directness: he essentially told Zaporozhets he was going to start a company around GitLab and asked him to join.

They incorporated **GitLab B.V.** in the Netherlands in **2013**, with Zaporozhets as CTO and Sijbrandij as CEO. Both worked remotely from day one - Zaporozhets from Ukraine, Sijbrandij from the Netherlands. What would become a defining feature of the company was not a deliberate philosophy at that point. It was simply geography.

**GitLab Inc.** was formally incorporated in Delaware, USA in **September 2014**, the move being partly for access to US venture capital. The company was accepted into **Y Combinator's Winter 2015 batch** - one of the few European startups in the cohort, and a significant validation that accelerated both fundraising and credibility in Silicon Valley.

---

## How GitLab Grew

### Remote from the start, transparent by design

GitLab became famous in the technology industry not only for its product but for how the company operates. It is one of the world's largest **all-remote** companies. No office exists anywhere. Every employee works from wherever they choose. This was not a response to the COVID-19 pandemic - it is how the company was built from its first days, driven by the simple fact that its founders were in different countries.

GitLab responded to this distributed reality by building one of the most comprehensive public company handbooks in existence. Almost everything about how GitLab operates - every policy, every process, every compensation philosophy, every hiring decision framework - is documented publicly at `handbook.gitlab.com`. The handbook runs to millions of words and is updated constantly. Other companies now study it as a model for remote operations and radical transparency.

### Funding timeline

| Round            | Date     | Amount     | Notable investors             | Valuation         |
| ---------------- | -------- | ---------- | ----------------------------- | ----------------- |
| Seed             | Jul 2015 | $1.5M      | Khosla Ventures, 500 Startups | -                 |
| Series A         | Sep 2015 | $4M        | Khosla Ventures               | -                 |
| Series B         | Sep 2016 | $20M       | August Capital                | ~$100M            |
| Series C         | Oct 2017 | $20M       | GV (Google Ventures)          | ~$400M            |
| Series D         | Sep 2018 | $100M      | ICONIQ Capital                | **$1B (unicorn)** |
| Series E         | Jan 2019 | $268M      | Goldman Sachs, ICONIQ         | $2.75B            |
| **Total raised** |          | **~$435M** | 51 investors                  | -                 |

The Series D in September 2018 made GitLab a unicorn - valued at over one billion dollars. The timing of this round was not coincidental. Three months earlier, something had happened that transformed GitLab's trajectory.

---

## The Microsoft Moment and What It Meant for GitLab

**4 June 2018** was a turning point for GitLab.

On that day, **Microsoft announced it would acquire GitHub for $7.5 billion**. The developer community's reaction was fast and loud. Many developers feared Microsoft's ownership would compromise GitHub's culture, change its pricing or eventually restrict the openness that had made it the home of open source software. Others were philosophically opposed to so much of the world's code - including the Linux kernel, Python, Ruby and thousands of other critical projects - being concentrated under a single corporation with a complicated history in open source.

The response to GitLab was immediate and historically significant:

- Over **100,000 repositories** were imported to GitLab in the days following the announcement - a number that took GitLab's servers hours to absorb
- More than **2,000 tweets** were posted with the hashtag `#movingtogitlab`
- GitLab saw **a 7× increase in orders** for its paid plans
- GitLab's CEO live-tweeted that the site was struggling under the load and asked people to slow down the imports

GitLab responded with two smart moves. First, they were transparent about the chaos - Sijbrandij tweeted real-time updates about server strain rather than pretending everything was fine. Second, and more strategically, **on the same day** as the Microsoft/GitHub announcement, GitLab announced that its **Ultimate and Gold tiers would be free forever for qualifying educational institutions and open source projects**. The timing was deliberate. The message was clear: if you are uncomfortable with GitHub being owned by Microsoft, GitLab is here, and it is generous to the communities that matter.

This single week accelerated GitLab's enterprise sales, brand recognition and developer community by years. It established GitLab permanently as the primary alternative for organisations that could not or would not use GitHub.

---

## The IPO and Life as a Public Company

On **17 September 2021**, GitLab filed its S-1 registration statement with the US Securities and Exchange Commission. The filing revealed, for the first time publicly, the full scale of the company: revenue, customer counts, burn rate and the financial details of a company that had been largely private.

GitLab began trading on the **NASDAQ Global Select Market** on **14 October 2021** under the ticker symbol **GTLB**. The IPO was priced at **$77 per share** and raised approximately **$650 million**. On the first day of trading, shares closed at **$103.89** - a 35% premium to the IPO price - giving GitLab a market capitalisation of approximately **$14.9 billion** at the close of its first day.

Two aspects of the IPO were unusual even by technology standards. First, GitLab was a fully remote company with no physical headquarters going public - rare for a company of this scale. Second, the timing: GitLab went public the same week as Coinbase and within days of other major tech IPOs, in the peak of the 2021 technology bull market.

### Leadership changes (2024-2026)

In **December 2024**, Sid Sijbrandij stepped down as CEO. He had been diagnosed with cancer and chose to step back from day-to-day operations to focus on treatment. He remains Executive Chair of the Board. Sijbrandij is widely credited not only with growing GitLab into a public company but with popularising the all-remote work model in the technology industry.

**Bill Staples** was appointed CEO in December 2024. Staples was previously CEO of New Relic, where he ran a similarly infrastructure-focused developer tools business.

In **January 2026**, **Siva Padisetty** was appointed Chief Technology Officer, replacing the previous CTO and bringing deep experience in enterprise software infrastructure.

---

## GitLab in 2026

As of April 2026, GitLab is one of the most significant infrastructure companies in enterprise software. Its market position has strengthened consistently as the DevSecOps category has matured.

**Platform statistics:**

| Metric                        | Figure                                        |
| ----------------------------- | --------------------------------------------- |
| Registered users              | 40M+ (some sources cite 50M+ as of late 2025) |
| Fortune 100 customers         | More than 50%                                 |
| Paying customers ($5K+ ARR)   | 10,475 (Q3 FY2026)                            |
| Paying customers ($100K+ ARR) | 1,405 (Q3 FY2026, +23% year-over-year)        |
| FY2025 revenue                | $759.2M (+31% year-over-year)                 |
| Q3 FY2026 revenue             | $244.4M (+25% year-over-year)                 |
| Employees                     | ~2,000 across 60+ countries                   |
| Open source contributors      | 3,300+                                        |
| Current version               | GitLab 18.10 (19 March 2026)                  |
| NASDAQ ticker                 | GTLB                                          |

**Awards and recognition (2025):**

- Gartner Magic Quadrant: **Leader in DevOps Platforms** (third consecutive year)
- Gartner Magic Quadrant: **Leader in AI Code Assistants** (second consecutive year)

**Notable enterprise customers**: Goldman Sachs, NVIDIA, Siemens, T-Mobile, Barclays, Ticketmaster, Jaguar Land Rover, NASDAQ, Dish Network, Comcast, Apple, Dell, IBM, the US Department of Homeland Security and the French Ministry of Education.

**Current version**: GitLab **18.10** was released on **19 March 2026**. Key features in this release include SAST false positive detection via the Duo Agent Platform, credits for free tier users, passwordless sign-in with passkeys (GA), a new work items list with saved views and multiple security improvements.

GitLab releases on the **third Thursday of every month**. Only the latest release is actively maintained. The previous two minor releases receive security backports.

---

## The Single Application Philosophy

GitLab's defining claim - and the source of both its strengths and its criticisms - is that it is **a single application for the entire DevSecOps lifecycle**.

To understand why this matters, it helps to understand what the alternative looks like. A typical software team using the GitHub marketplace approach might use:

- GitHub for code hosting and pull requests
- GitHub Actions or CircleCI for CI/CD
- Snyk or Dependabot for dependency vulnerability scanning
- SonarQube for code quality
- Jira or Linear for project management and issue tracking
- Artifactory or Docker Hub for container and package hosting
- DataDog for monitoring and alerting
- PagerDuty for incident management
- Confluence for documentation

Each of these is potentially best-of-breed in its category. But each has its own login system, its own API, its own data model and its own billing. Integrating them requires custom webhooks, API connections and maintenance work. When something goes wrong across multiple tools, tracing the problem requires jumping between platforms.

GitLab's answer is to collapse all of these into one product. The software development lifecycle has many stages, and GitLab maps them as:

**Plan → Create → Verify → Package → Secure → Release → Configure → Monitor → Govern**

In the GitLab model, every stage of this lifecycle produces data that is linked to every other stage. A security vulnerability found by a SAST scan in a CI/CD pipeline is directly linked to the merge request that introduced the code, which is linked to the issue that described the feature, which is linked to the milestone that planned the sprint, which is linked to the epic that defined the initiative. Nothing is siloed. A single audit log covers the entire process.

**The practical consequences of this philosophy:**

For teams that commit to the GitLab model, the maintenance burden is dramatically lower. One platform means one login, one SSO configuration, one API to learn, one support contract, one place to look for audit logs. Compliance frameworks and security policies apply consistently across everything because everything is in one place.

For teams that want flexibility, the single-application model is a constraint. If you want the absolute best code quality scanner, the absolute best project management tool and the absolute best incident management system, GitLab may not be any of those individually. You are trading best-of-breed flexibility for integration quality and operational simplicity.

This trade-off is why GitLab is particularly strong in enterprise environments, regulated industries and government. When data residency matters, when audit trails are legally required, when the compliance team needs to sign off on every tool - a single application that can be deployed on-premises and configured centrally is enormously valuable.

---

## What GitLab Adds on Top of Git

At its core, GitLab hosts Git repositories. Everything beyond that is what makes it a platform rather than just a server. Here is what GitLab adds:

### Code management

The web-based repository browser gives you syntax-highlighted file views, blame views showing who wrote each line and when and a commit history browser. You can browse the code at any commit, tag or branch without cloning locally.

**Merge requests** (GitLab's name for pull requests) are the primary code review mechanism. They show diffs, support inline comments, track suggestions that can be applied with one click, require approval workflows and integrate directly with CI/CD pipeline results. A merge request that fails its pipeline cannot be merged until the failure is resolved.

**Protected branches** prevent direct pushes to important branches like `main` or `production`. Rules can require pipeline success, specific approvers or both before any merge is allowed.

**The Web IDE** is a browser-based editor that runs a VS Code-like experience directly in your browser. You can make multi-file changes, preview Markdown and commit without installing anything locally.

**Snippets** are versioned, shareable code fragments - GitLab's equivalent of GitHub Gists. They support multiple files and can be public, internal (self-managed only) or private.

### CI/CD (Continuous Integration / Continuous Delivery)

Every GitLab project can have a `.gitlab-ci.yml` file at its root that defines an automated pipeline. When you push code, GitLab reads this file and runs your pipeline: build the code, run the tests, check code quality, scan for security vulnerabilities, build a Docker image, push it to the registry, deploy to a staging environment and notify the team.

GitLab provides **shared runners** on GitLab.com that execute these pipelines without any infrastructure setup. You can also install your own runners on any machine - a laptop, a cloud VM, a Kubernetes cluster - and connect them to your projects for unlimited free execution.

**Review apps** create a live, temporary deployment of your code for every merge request. Each MR gets its own unique URL where anyone can preview the changes before approving the merge.

**Auto DevOps** is a zero-configuration mode where GitLab automatically detects your project's language and applies a standard build, test and deploy pipeline without any `.gitlab-ci.yml` file required. Useful for teams getting started with CI/CD.

### Security

GitLab's security scanning is one of its strongest differentiators, particularly at the Ultimate tier. Rather than connecting to a third-party scanner, security scans run as jobs in your existing CI/CD pipeline:

**SAST** (Static Application Security Testing) analyses your source code for common vulnerabilities: SQL injection, cross-site scripting, insecure cryptography, hardcoded credentials and more. Basic SAST is available on the Free tier. Advanced SAST with cross-file and cross-function analysis requires Ultimate.

**Secret Detection** scans your code and commit history for accidentally committed credentials - API keys, tokens, passwords, private keys. Available on the Free tier. Push protection (blocking the push before it reaches the server) is also available.

**Dependency Scanning** checks your third-party dependencies against the GitLab Advisory Database (GLAD) for known vulnerabilities. Basic scanning is free.

**Container Scanning** scans Docker images in the container registry for OS and package vulnerabilities.

**DAST** (Dynamic Application Security Testing) runs automated penetration tests against a running instance of your application. Ultimate only.

**Fuzz Testing** throws unexpected, malformed input at your application to find crashes and unexpected behaviour. Ultimate only.

### Project management

Issues are the primary unit of work tracking. Each issue can have assignees, labels, a due date, a milestone, child tasks (sub-issues), time estimates and logged time. Issues automatically link to merge requests that reference them.

**Labels** are colour-coded tags for categorising issues. **Scoped labels** (Premium) use a `key::value` format where applying one value automatically removes others in the same scope - for example, setting `workflow::in-review` removes `workflow::in-progress` automatically.

**Boards** give you a Kanban view where columns are defined by labels, milestones or assignees. You move cards between columns to update the issue state.

**Epics** (Premium) group related issues across multiple milestones into a single higher-level initiative. **Roadmaps** (Premium) show epics on a timeline, giving management and stakeholders a project-level view of progress.

**Service Desk** turns your project's issue tracker into a customer support system. Customers email a unique address, and their emails create confidential issues. Your team responds through comments, and the customer receives the response by email - all without the customer needing a GitLab account.

### Registries

**Container Registry**: a Docker/OCI-compatible registry built into every project. Push and pull container images using the `CI_REGISTRY_IMAGE` variable in CI/CD without setting up or paying for a separate registry service.

**Package Registry**: hosts npm packages, Python wheels, Maven artifacts, NuGet packages, Cargo crates, Composer packages, Helm charts and more. Available on the Free tier.

### AI (GitLab Duo)

GitLab Duo is the umbrella name for GitLab's AI capabilities, covered fully in [10-gitlab-duo.md](10-gitlab-duo.md). In brief:

**Code Suggestions** provides inline code completion and generation in VS Code, JetBrains IDEs and the GitLab Web IDE. It uses models from Anthropic (Claude), Google Vertex AI and Fireworks AI.

**Duo Chat** is a conversational AI assistant available in the IDE and on GitLab.com. You can ask it to explain code, review a diff, write tests or help debug a pipeline failure.

**Duo Agent Platform** (GA January 2026) extends AI from answering questions to taking actions: automatically converting an issue into a merge request, analysing CI/CD pipeline failures and generating fixes, reviewing security vulnerabilities and suggesting patches and migrating CI/CD configurations.

---

## GitLab Editions and Plans

GitLab is available in two editions and three pricing tiers. Understanding both dimensions matters.

### Editions

**Community Edition (CE)** is fully open source under the MIT licence. Every feature in CE is available to all users with no restrictions, no expiry and no licence required. CE is self-hosted only - it cannot be used on GitLab.com. The source code is at `gitlab.com/gitlab-org/gitlab-foss`.

**Enterprise Edition (EE)** uses a proprietary licence. It contains all CE features plus Premium and Ultimate features. The key point: when EE is installed without a valid paid licence, it behaves identically to CE. No paid features are active. You get a CE-equivalent experience on EE infrastructure. When you purchase a Premium or Ultimate subscription and upload your licence, the paid features activate.

**Recommendation**: always install EE, even if you plan to stay on the free tier indefinitely. If you ever decide to upgrade, EE lets you activate paid features without reinstalling the entire platform. Starting with CE and later upgrading to EE requires a more involved migration.

### GitLab.com pricing tiers (as of April 2026)

| Tier         | Monthly cost (annual billing)   | Key limits                                                    | Key features                                                                                                                                     |
| ------------ | ------------------------------- | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Free**     | $0                              | 5 users/private group, 400 compute mins/month, 10 GiB/project | Unlimited projects, basic SAST, secret detection, container/package registries, Service Desk, GitLab Pages                                       |
| **Premium**  | $29/user/month                  | 10,000 compute mins/month, 500 GiB/project                    | Merge request approvals, CODEOWNERS enforcement, epics, roadmaps, iterations, scoped labels, merge trains, SAML SSO, priority support            |
| **Ultimate** | ~$99/user/month (contact sales) | 50,000 compute mins/month, 500 GiB/project                    | Advanced SAST, DAST, fuzz testing, license compliance, vulnerability management, security policies, compliance framework, value stream analytics |

> [!IMPORTANT]
> All paid plans are **annual billing only** - there is no monthly option. A team of five on Premium commits $1,740 upfront per year. Ultimate pricing moved to "contact sales" in 2024; it was previously listed at $99/user/month.

> [!NOTE]
> GitLab raised Premium from $19 to $29/user/month on 3 April 2023, a 53% increase that generated significant community criticism. Existing customers received a transition price of $24/user/month until April 2024.

**A common frustration with the Free tier**: merge request approvals and CODEOWNERS enforcement are behind the Premium paywall. On GitHub, basic branch protection rules that require reviews are free. On GitLab, they are not. This is the most frequently cited complaint about GitLab's pricing.

**AI add-ons (on top of base plan)**:

| Add-on         | Price          | Requires                    | Includes                                                                                              |
| -------------- | -------------- | --------------------------- | ----------------------------------------------------------------------------------------------------- |
| Duo Core       | Included       | Premium or Ultimate (18.0+) | Basic Code Suggestions, Duo Chat                                                                      |
| Duo Pro        | $19/user/month | Premium or Ultimate         | Advanced Code Suggestions, test generation, MR summaries, Agent Platform                              |
| Duo Enterprise | $39/user/month | Ultimate                    | Everything in Pro, plus root cause analysis, vulnerability explanation and resolution, AI code review |

---

## GitLab for Education and Open Source

GitLab runs three community programmes that make the Ultimate tier available for free to qualifying organisations.

### GitLab for Education

Qualifying organisations receive GitLab Ultimate (SaaS or self-managed EE) and 50,000 compute minutes per month at no cost.

**Eligibility**: non-profit educational or research institutions. Use must be for instructional or research purposes. Commercial use within the same organisation does not qualify.

**How to apply**: `about.gitlab.com/solutions/education/`. Approval typically takes a few weeks and requires institutional email verification.

No support is included with the education programme. Support can be purchased separately at a discounted rate ($4.95/user/month as of 2025).

### GitLab for Open Source

**Eligibility**: open source projects with an OSI-approved licence, non-commercial use, no paid contributors developing as part of their employment.

**What you get**: GitLab Ultimate SaaS, 50,000 compute minutes per month.

Many well-known open source projects run on GitLab using this programme, including projects in the Linux, GNOME, KDE and Freedesktop ecosystems.

### GitLab for Startups

**Eligibility**: early-stage companies that have raised less than $5M in funding and are less than five years old (exact criteria change - check the application page).

**What you get**: free GitLab Ultimate for 12 months.

---

## Who Uses GitLab and Why

GitLab's user base divides into distinct segments with different reasons for choosing the platform.

**Enterprise and regulated industries** are GitLab's strongest segment and its most important revenue source. Banks, insurance companies, healthcare providers, government agencies and defence contractors choose GitLab primarily because of two capabilities that GitHub cannot match at the same price:

First, **self-hosting**. GitLab Community Edition is free and can be installed entirely on-premises, in a private cloud or in an air-gapped environment with no internet connectivity. Regulated industries often cannot allow source code or CI/CD infrastructure to be hosted on a third-party cloud. GitHub's self-hosted option (GitHub Enterprise Server) costs $21/user/month. GitLab CE is free.

Second, **built-in security scanning**. SAST, DAST, dependency scanning and vulnerability management in a single pipeline without connecting to external scanners or purchasing additional licences. For banks and government agencies with mandatory security testing requirements, this reduces both cost and compliance risk.

Specific named customers: Goldman Sachs, Barclays, T-Mobile, NVIDIA, Siemens, the US Department of Homeland Security, the French Ministry of Education, Jaguar Land Rover, NASDAQ and Ticketmaster. Many more are confidential.

**DevOps teams** that want a single vendor for code, pipelines and deployment. Instead of managing the integration between GitHub + CircleCI + Artifactory + Snyk, everything is in one place. This reduces engineering time spent on toolchain maintenance and gives a consistent view of the entire delivery process.

**Universities and research institutions** use GitLab through the education programme. Gitlab is widely used in computer science and data science curricula. The CERN physics laboratory runs one of the world's largest self-managed GitLab installations for its research computing infrastructure.

**Open source projects** in ecosystems that prefer non-Microsoft infrastructure. The GNOME desktop environment, KDE, FreeDesktop and many freedesktop.org projects moved to or remained on GitLab partly as a response to Microsoft's GitHub acquisition.

**Individual developers** use the free tier for personal projects. The five-user limit on private groups is a real constraint for small teams, but unlimited public projects and the free CI/CD pipeline (400 minutes/month) make it a capable platform for individuals.

---

## GitLab as a Company

A few things make GitLab genuinely unusual as a technology company.

**All-remote**: GitLab has no office anywhere in the world and no plans to build one. Every employee works from wherever they choose. This is not a pandemic adaptation - it is how the company was designed from 2013 onward. With around 2,000 employees across 60+ countries, it is one of the largest all-remote companies ever built. The logistics of coordinating a distributed workforce of this scale have forced GitLab to develop and document management practices, communication norms and operational processes that it then makes publicly available. Other companies study the GitLab Handbook as a model.

**Radical transparency**: almost everything about how GitLab operates is public. The employee handbook at `handbook.gitlab.com` covers compensation philosophy, performance review processes, hiring criteria, engineering standards, product decision frameworks and much more. GitLab publishes its OKRs (Objectives and Key Results) publicly. It publishes meeting recordings. It is one of the most transparent large companies in existence.

**Open core model**: the Community Edition is genuinely open source under the MIT licence. Anyone can read it, modify it and run it without restriction. Premium and Ultimate features are closed-source. This open core model distinguishes GitLab from companies that describe themselves as open source but restrict important features behind source-available or proprietary licences.

**Geography**: incorporated in Delaware, USA. Significant operations in the Netherlands (where Sijbrandij is based). Ukrainian roots (where Zaporozhets built the original platform). The company has always emphasised that talent exists everywhere and that remote work enables access to it.

---

## Try It Yourself

**Create a free GitLab.com account:**

1. Go to `https://gitlab.com/users/sign_up`
2. Fill in your first name, last name, a username (2-255 characters, letters, digits, `_`, `-` and `.` only) and your email address
3. Create a strong password and click **Register now**
4. Check your email for a confirmation link and click it
5. Complete the brief onboarding survey (you can skip most of it)
6. Your profile is now live at `https://gitlab.com/YOUR_USERNAME`

**Explore a real, large GitLab project:**

Go to `https://gitlab.com/gitlab-org/gitlab`. This is the GitLab application itself, developed publicly on GitLab. It is one of the largest active open source codebases in the world. Notice:

- **Code → Repository**: the full source code, browsable by branch, tag or commit
- **Build → Pipelines**: dozens of pipelines running at any given moment, testing and building every change
- **Plan → Work items** (filtered to Issues): thousands of open issues from contributors worldwide
- **Code → Merge requests**: the active code review queue - see what the GitLab team is working on right now

This single project demonstrates almost every feature GitLab offers at enterprise scale.

**Compare GitLab and GitHub side by side:**

Open `https://gitlab.com/gitlab-org/gitlab` and `https://github.com/torvalds/linux` in two browser tabs. Find the equivalents: code browser, issue tracker, pipeline/Actions, merge request/pull request. Notice what each platform emphasises in its UI and navigation.

---

## Common Mistakes

**Confusing GitLab the platform with GitLab the company and GitLab the software.** GitLab Inc. is the company. `gitlab.com` is the SaaS cloud service. GitLab CE and GitLab EE are the software products you can self-host. All three use the name "GitLab" and all three are related but distinct. When someone says "we use GitLab", they could mean any combination of these.

**Assuming GitLab.com and self-managed GitLab are identical.** Several features behave differently or do not exist on one or the other. Internal visibility (projects visible to all authenticated users) works on self-managed but is disabled for new projects on GitLab.com. The Admin Area exists only on self-managed. SAML SSO is configured at the group level on GitLab.com but at the instance level on self-managed. Where differences exist, this course notes them.

**Assuming GitLab is simply a GitHub clone.** GitLab predates many of GitHub's enterprise features and has architectural differences that are not superficial. Merge trains, built-in DAST, fuzz testing, the single-application data model, the value stream analytics and the free self-hosted CE edition have no direct GitHub equivalent.

**Confusing "project" and "repository".** On GitLab, a project is more than a Git repository. It includes the repository plus issues, merge requests, CI/CD configuration, wiki pages, snippets, packages, the container registry and project-level settings. The word "repository" appears in GitLab's UI but "project" is the correct term. This matters when working with the API or reading documentation.

**Expecting 2,000 free CI minutes like GitHub.** GitLab Free gives 400 compute minutes per month for pipelines running on GitLab.com's shared runners. GitHub Free gives 2,000 minutes. If you install and register your own GitLab Runner on any machine, you get unlimited pipeline execution at no cost. Self-hosted runners are free and eliminate the compute minutes constraint entirely.

**Not checking whether a feature requires Premium or Ultimate.** GitLab's free tier is genuinely capable, but some features that developers consider basic - merge request approvals, CODEOWNERS enforcement, epics, iterations - require Premium. Always check the tier requirement before building a workflow that depends on a feature you have not confirmed is available on your plan.

---

## Summary

- GitLab was created by **Dmytro Zaporozhets** in Ukraine in **2011** as an open-source side project, and co-founded as a company with **Sid Sijbrandij** in **2013**
- It is a **single application for the entire DevSecOps lifecycle**: code hosting, CI/CD, security scanning, project management, container and package registries and AI in one product
- **40M+ registered users**, more than 50% of the Fortune 100, trading on NASDAQ as **GTLB**
- Current version: **GitLab 18.10** (March 2026). CEO: **Bill Staples**. CTO: **Siva Padisetty**
- Available as **GitLab.com** (SaaS) or self-hosted **Community Edition** (free, MIT licence) or **Enterprise Edition** (proprietary but CE-equivalent without a paid licence)
- Free tier: unlimited projects, 5 users per private group, 400 compute minutes/month, 10 GiB/project, basic security scanning
- Premium: $29/user/month; Ultimate: contact sales (~$99) - both annual billing only
- **GitLab for Education** and **GitLab for Open Source** give qualifying organisations free Ultimate with 50,000 compute minutes
- GitLab's growth was dramatically accelerated by **Microsoft's acquisition of GitHub in June 2018**, which sent 100,000+ repositories and a 7× spike in orders to GitLab
- The key philosophical difference: GitLab is **one integrated application** vs GitHub's **marketplace ecosystem** of best-of-breed tools. Neither approach is universally superior - the right choice depends on your team's size, compliance requirements and preference for flexibility vs simplicity

---

## Sources and Further Reading

- [GitLab company history](https://about.gitlab.com/company/) - official GitLab history and about page
- [GitLab Inc. Wikipedia](https://en.wikipedia.org/wiki/GitLab_Inc.) - detailed history, funding timeline and key milestones
- [GitLab 18.10 release post](https://about.gitlab.com/releases/2026/03/19/gitlab-18-10-released/) - latest release notes
- [GitLab pricing](https://about.gitlab.com/pricing/) - current plan details and the feature comparison matrix
- [GitLab for Education](https://about.gitlab.com/solutions/education/) - applying for free Ultimate
- [GitLab for Open Source](https://about.gitlab.com/solutions/open-source/) - applying for free Ultimate as an OSS project
- [GitLab handbook](https://handbook.gitlab.com) - how GitLab operates as an all-remote company
- [GitLab IPO coverage - CNBC](https://www.cnbc.com/2021/10/14/gitlab-jumps-in-nasdaq-debut-after-pricing-ipo-above-expected-range.html) - first day of trading
- [GitLab Global DevSecOps Report 2026](https://about.gitlab.com/developer-survey/) - annual survey of DevSecOps trends
- [GitLab Community Edition source](https://gitlab.com/gitlab-org/gitlab-foss) - the open source code

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

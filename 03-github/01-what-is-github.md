# What is GitHub?

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

GitHub is where the world builds software. It is the largest code hosting platform on the planet, home to over 180 million developers and more than 1 billion repositories. Whether you are pushing your first project or contributing to software that runs inside spacecraft, GitHub is almost certainly involved. This file explains what GitHub actually is, where it came from, how it grew and why it matters to every developer working today.

---

## Table of Contents

- [Git vs GitHub](#git-vs-github)
- [The founding story](#the-founding-story)
- [How GitHub launched](#how-github-launched)
- [Key milestones](#key-milestones)
- [The Microsoft acquisition](#the-microsoft-acquisition)
- [GitHub in 2025 and 2026](#github-in-2025-and-2026)
- [GitHub Octoverse 2025](#github-octoverse-2025)
- [What GitHub adds on top of Git](#what-github-adds-on-top-of-git)
- [GitHub vs the alternatives](#github-vs-the-alternatives)
- [Who uses GitHub?](#who-uses-github)
- [The Octocat](#the-octocat)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Git vs GitHub

Before anything else: **Git and GitHub are not the same thing.**

**Git** is the version control system - the tool you install on your computer to track changes in files. It was created by Linus Torvalds in 2005. It is free, open source and works entirely on your local machine. Git has no servers, no accounts and no web interface.

**GitHub** is a hosting platform built on top of Git. It stores your Git repositories on remote servers, adds a web interface for browsing and managing code and layers on collaboration tools - pull requests, issues, Actions, Discussions, Projects and more. GitHub is a company (now owned by Microsoft) and a product.

You can use Git without GitHub. You can use GitHub without understanding Git deeply. But the two work best together - Git manages your code locally, GitHub hosts it remotely and connects you with collaborators.

> [!NOTE]
> GitLab and Bitbucket are alternative platforms that do the same job as GitHub. They host Git repositories and add collaboration tools. The core Git workflow is identical across all three. The platform-specific features differ significantly. GitLab is covered in the `gitlab/` folder of this course.

---

## The Founding Story

GitHub was not built to become the world's dominant code hosting platform. It was built to scratch an itch.

In 2007, **Tom Preston-Werner** was a developer who wanted a better way to share code and collaborate on open source projects. Git was gaining traction in the Linux kernel community but had no convenient hosting. The only real options were self-hosted servers or platforms like SourceForge and Google Code, which were slow, complicated and not built around Git.

Preston-Werner pitched the idea at a bar to **Chris Wanstrath**, a developer he had just met. The two clicked immediately. They brought in **P.J. Hyett** and **Scott Chacon** and the four of them began building.

**The first commit to the GitHub codebase was made on 19 October 2007.**

The platform was built using Ruby on Rails and deployed on Erlang-based infrastructure. Development moved fast. The four founders were working other jobs simultaneously and building GitHub on the side.

GitHub was incorporated as **Logical Awesome LLC** on **8 February 2008**.

---

## How GitHub Launched

GitHub launched publicly on **10 April 2008** after a short private beta. The pitch was simple: host your Git repositories online, collaborate with others through a clean web interface and make open source contribution accessible to everyone.

The timing was perfect. Git was rapidly displacing Subversion in the open source community. GitHub gave Git a home that matched its philosophy - distributed, open and fast.

**The early growth was remarkable:**

- By **July 2009** - 100,000 users
- By **2010** - 1 million repositories
- By **2011** - 2 million repositories, GitHub overtook SourceForge and Google Code in traffic
- By **January 2013** - 3 million users and 5 million repositories
- By the **end of 2013** - 10 million repositories

For the first four years, GitHub was **entirely bootstrapped** - funded only by the revenue it generated, with no outside investment. This was unusual for a Silicon Valley startup and gave the founders complete control over the product's direction.

**The first outside investment:**

- **July 2012 (Series A):** Andreessen Horowitz invested **$100 million**. GitHub was valued at approximately **$750 million**.
- **July 2015 (Series B):** Raised **$250 million** led by Sequoia Capital, with Andreessen Horowitz, Thrive Capital and IVP. Valued at approximately **$2 billion**.

---

## Key Milestones

| Date | Milestone |
|---|---|
| 19 Oct 2007 | First commit to the GitHub codebase |
| 8 Feb 2008 | Incorporated as Logical Awesome LLC |
| **10 Apr 2008** | **Public launch** |
| Jul 2009 | 100,000 users |
| 2010 | 1 million repositories |
| 2011 | Overtook SourceForge and Google Code |
| Jul 2012 | $100M Series A - valued at $750M |
| Jan 2013 | 3 million users, 5 million repositories |
| End 2013 | 10 million repositories |
| 2014 | Preston-Werner resigns; Wanstrath becomes CEO |
| Jul 2015 | $250M Series B - valued at $2B |
| Feb 2018 | Largest DDoS attack in history at the time (~1.35 Tbps) |
| **Jun 2018** | **Microsoft announces $7.5 billion acquisition** |
| **Oct 2018** | **Acquisition closes; Nat Friedman becomes CEO** |
| Sept 2019 | Acquired Semmle (became CodeQL) |
| Mar 2020 | Acquired npm (JavaScript package registry) |
| Jul 2020 | Arctic Code Vault - code archived in Svalbard |
| Oct 2018 | GitHub Actions launched (beta) |
| Nov 2019 | GitHub Actions generally available |
| Aug 2021 | GitHub Codespaces generally available |
| Jun 2022 | GitHub Copilot generally available |
| Nov 2021 | Thomas Dohmke becomes CEO |
| **Jan 2023** | **100 million developers on GitHub** |
| **Jun 2025** | **Repositories surpass 1 billion (including forks)** |
| Aug 2025 | Thomas Dohmke steps down as CEO |
| End 2025 | GitHub absorbed into Microsoft's CoreAI division |

---

## The Microsoft Acquisition

On **4 June 2018**, Microsoft announced it would acquire GitHub for **$7.5 billion** in an all-stock deal. The acquisition closed on **26 October 2018**.

The announcement was met with significant community anxiety. GitHub had built its reputation as an independent, developer-first platform. Many feared Microsoft's ownership would change the product's direction, introduce paywalls or compromise its openness.

Those fears proved largely unfounded. Nat Friedman, the former CEO of Xamarin (a company Microsoft had previously acquired), became GitHub's CEO and maintained the platform's independence and open source commitments. Microsoft itself was already one of GitHub's largest users - hosting .NET Core, Visual Studio Code, PowerShell and hundreds of other projects on the platform.

**What Microsoft ownership brought:**

- Deeper integration with Azure and other Microsoft developer tools
- Significant investment in new features - Actions, Codespaces, Copilot
- GitHub Copilot (the most significant AI coding tool of the decade) was only possible with Microsoft's resources and partnership with OpenAI
- Free private repositories for all users (previously a paid feature)

**Current leadership (March 2026):**

Thomas Dohmke stepped down as CEO in August 2025. GitHub was subsequently absorbed into Microsoft's **CoreAI - Platform and Tools** division led by **Jay Parikh**. GitHub no longer has an independent CEO. **Kyle Daigle** serves as COO and **Mario Rodriguez** as Chief Product Officer. This structural change reflects Microsoft's strategy of tightly integrating GitHub into its AI platform around Copilot and agentic development.

---

## GitHub in 2025 and 2026

GitHub's growth has accelerated dramatically, driven largely by the explosion of AI-assisted development.

| Metric | Figure (2025/2026) |
|---|---|
| Total developers | **180+ million** |
| New developers in 2025 | 36 million - roughly **1 per second** |
| Total repositories | **630 million** (excluding forks) / **1 billion+** (including forks) |
| New repositories in 2025 | 121 million - over **230 per minute** |
| GitHub Copilot users | **20+ million** |
| Total commits in 2025 | ~986 million (+25% year on year) |
| Merged pull requests in 2025 | 518.7 million (+29% year on year) |
| AI-related repositories | 4.3+ million (nearly doubled since 2023) |
| Fortune 100 companies using GitHub | 90%+ |
| Daily GitHub Actions jobs | 40+ million (on weekdays) |

**Regional growth:** India added 5.2 million developers in 2025 - 14% of all new accounts worldwide - and overtook the United States in total open source contributor count for the first time. Brazil, Indonesia, Japan and Germany also showed strong growth.

---

## GitHub Octoverse 2025

The **GitHub Octoverse** is GitHub's annual report on developer trends and platform statistics. The 2025 edition was published on **28 October 2025** and identified three major shifts in software development:

### 1. TypeScript became the most-used language on GitHub

TypeScript overtook Python and JavaScript as the number one language on GitHub by monthly contributor count in August 2025, reaching 2,636,006 monthly contributors - a 66.63% increase year on year. The driver is AI tooling: AI coding assistants work significantly better with typed languages, and 94% of LLM-generated compilation errors are type-check failures. Developers are choosing TypeScript specifically because it improves AI-assisted development.

### 2. Generative AI became standard in software development

Over 1.1 million public repositories now use an LLM SDK - a 178% increase year on year. **80% of new GitHub developers use Copilot in their first week.** Over 1 million pull requests were authored by Copilot's coding agent between May and September 2025. Model Context Protocol (MCP) gained 37,000 GitHub stars in 8 months.

### 3. AI is reshaping technology choices

Nearly 80% of new repositories used just 6 languages: Python, JavaScript, TypeScript, Java, C++ and C#. Jupyter Notebooks reached 2.4 million repositories (+75% year on year). Dockerfiles reached 1.9 million repositories (+120% year on year). Security metrics improved - the average time to fix a critical vulnerability shrank from 37 days to 26 days.

---

## What GitHub Adds on Top of Git

Git is a command-line tool. GitHub wraps it in a platform with features that make team collaboration practical at scale.

**Hosting and access:**
- Remote repository storage accessible from anywhere
- HTTPS and SSH authentication
- Repository visibility controls (public, private, internal)
- Organisations and teams with granular permissions

**Collaboration:**
- **Pull requests** - propose changes, request reviews, discuss code line by line
- **Issues** - track bugs, features and tasks with labels, assignees and milestones
- **Discussions** - forum-style conversations separate from the issue tracker
- **Code review** - inline comments, suggested changes, approval workflows

**Automation:**
- **GitHub Actions** - CI/CD pipelines triggered by any Git event
- **GitHub Copilot** - AI pair programming integrated into the development workflow
- **Dependabot** - automated dependency updates and security patches

**Discovery and community:**
- Stars and watches to bookmark repositories
- Topics and search for discoverability
- Contributor graphs, traffic insights and repository analytics
- GitHub Explore for discovering trending projects

**Security:**
- Secret scanning - detects credentials accidentally committed
- Code scanning with CodeQL - finds security vulnerabilities in code
- Push protection - blocks commits containing secrets before they land
- Private vulnerability reporting - structured process for responsible disclosure

**Deployment:**
- **GitHub Pages** - free static site hosting from any repository
- **GitHub Releases** - versioned software packages with binary attachments and release notes
- **GitHub Packages** - package registry for npm, Docker, Maven, NuGet and more

---

## GitHub vs the Alternatives

| Feature | GitHub | GitLab | Bitbucket |
|---|---|---|---|
| Free private repos | Yes | Yes | Yes (limited) |
| CI/CD | GitHub Actions | GitLab CI/CD | Bitbucket Pipelines |
| AI assistant | Copilot | GitLab Duo | No native AI |
| Container registry | Yes (ghcr.io) | Yes | Yes |
| Pages | Yes | Yes | No |
| Self-hosted option | GitHub Enterprise Server | Yes (free CE) | Yes |
| Marketplace | 20,000+ actions | Limited | Limited |
| Community size | Largest | Large | Smaller |

**GitHub** dominates open source and is the default choice for most public projects. Its network effects are enormous - most developers already have accounts, most open source projects are hosted there and most tooling integrates with it first.

**GitLab** is strong in enterprise environments and offers a more complete DevOps platform out of the box. Its free self-hosted Community Edition is popular with organisations that need full control over their infrastructure.

**Bitbucket** is popular in teams that use other Atlassian products (Jira, Confluence). It has a smaller community and fewer features than GitHub or GitLab.

---

## Who Uses GitHub?

GitHub is used across every sector of the technology industry and beyond.

**Open source projects:** The Linux kernel, Python, React, Vue, VS Code, TensorFlow, Kubernetes, Node.js, Rust, Go - effectively every major open source project in the world is on GitHub.

**Companies:** 90% of Fortune 100 companies use GitHub. Microsoft, Google, Meta, Amazon, Apple, Netflix and thousands of others host code on GitHub.

**Governments:** The UK Government Digital Service, the US government's code.gov, NASA and many other government organisations publish code on GitHub.

**Students and individuals:** GitHub is the standard portfolio platform for developers. A well-maintained GitHub profile is the first thing many employers look at when evaluating candidates.

**Research:** Academia uses GitHub extensively for sharing datasets, experiment code and reproducible research.

---

## The Octocat

GitHub's mascot is the **Octocat** - a creature with a cat's body and an octopus's tentacles. The original image was created by graphic designer **Simon Oxley** as clip art for the iStock photo library, where it was listed as "Octopuss." GitHub purchased exclusive rights to the image.

The Octocat has become one of the most recognisable mascots in software development. GitHub sells Octocat merchandise, has created hundreds of custom Octocat variations and the mascot appears throughout the GitHub interface.

You can create your own custom Octocat at [myoctocat.com](https://myoctocat.com).

---

## Try It Yourself

If you do not yet have a GitHub account, this is the right moment to create one. Account setup is covered in detail in the next file - [02-creating-an-account.md](02-creating-an-account.md).

**Explore GitHub without an account:**

**Step 1.** Go to [github.com](https://github.com) and look at the homepage.

**Step 2.** Visit [github.com/explore](https://github.com/explore) to see trending repositories.

**Step 3.** Search for a project you use - for example, search `visual studio code` or `react` or `python`.

**Step 4.** Click on a repository and explore:
- The file browser showing the code
- The commit history
- The Issues tab
- The Pull requests tab
- The Actions tab (if the project uses CI/CD)

**Step 5.** Visit [github.com/torvalds/linux](https://github.com/torvalds/linux) - the Linux kernel repository. This is one of the largest and most active repositories in the world. Look at the commit history and the contributor count.

**Step 6.** Visit [github.com/github](https://github.com/github) - GitHub's own organisation page. Note that GitHub itself hosts its open source projects on GitHub.

---

## Common Mistakes

**Confusing Git and GitHub.**

Git is the version control system. GitHub is the hosting platform. You can use Git without GitHub and GitHub without Git expertise. They are separate things that work well together.

**Thinking GitHub is the only option.**

GitHub is the largest platform but not the only one. GitLab and Bitbucket are legitimate alternatives. Many enterprise teams use GitLab's self-hosted Community Edition. The choice of platform does not affect how Git itself works.

**Assuming private means secure.**

Private repositories are not visible to the public, but they are not encrypted and GitHub staff can access them under certain circumstances. Never store passwords, API keys, tokens or personal data in any repository - public or private.

**Thinking you need to understand everything before starting.**

GitHub has dozens of features. You do not need all of them immediately. Start with repositories, commits and push/pull. Add pull requests and issues when you start collaborating. Add Actions when you want automation. Build up gradually.

---

## Summary

- GitHub is the world's largest code hosting platform with 180+ million developers and 1 billion+ repositories
- Git and GitHub are not the same - Git is the version control system, GitHub is the hosting platform built on top of it
- GitHub was founded by Tom Preston-Werner, Chris Wanstrath, P.J. Hyett and Scott Chacon and launched publicly on 10 April 2008
- GitHub was bootstrapped for four years before its first outside investment of $100 million in 2012
- Microsoft acquired GitHub for $7.5 billion in October 2018; GitHub continued operating independently under its own CEO until late 2025 when it was absorbed into Microsoft's CoreAI division
- GitHub had 180+ million developers and 1 billion+ repositories by 2025, growing at roughly 1 new developer per second
- Octoverse 2025 identified TypeScript as the top language, AI tooling as the dominant development trend and India overtaking the US in open source contributions
- GitHub adds pull requests, issues, Actions, Copilot, Pages, security scanning and dozens of other features on top of Git
- 90% of Fortune 100 companies use GitHub; effectively every major open source project is hosted there

---

## Sources and Further Reading

- [GitHub's official history](https://github.com/about) - about page with company information
- [GitHub Octoverse 2025](https://github.blog/news-insights/octoverse/octoverse-a-new-developer-joins-github-every-second-as-ai-leads-typescript-to-1/) - the full 2025 annual developer report
- [Wikipedia - GitHub](https://en.wikipedia.org/wiki/GitHub) - comprehensive article with full timeline and references
- [The GitHub Blog](https://github.blog) - official announcements, feature launches and engineering posts
- [GitHub Octoverse archive](https://octoverse.github.com) - all past Octoverse reports
- [Microsoft acquires GitHub](https://blogs.microsoft.com/blog/2018/06/04/microsoft-github-empowering-developers/) - the original Microsoft announcement
- [GitHub statistics 2026](https://coinlaw.io/github-statistics/) - current platform statistics

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

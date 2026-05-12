# What is Codeberg?

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Codeberg is a public Git hosting platform run as a non-profit community service. It provides free code hosting, pull requests, issues, project management, static site hosting and CI/CD for open source projects. Unlike GitHub (owned by Microsoft) or GitLab.com (commercial SaaS), Codeberg is operated by Codeberg e.V. - a registered German non-profit association whose mission is to promote free and open source software.

The platform runs Forgejo, the community-governed fork of Gitea. If you have used GitHub, Codeberg will feel familiar within minutes.

---

## Table of Contents

1. [Codeberg e.V.: the organisation behind the platform](#codeberg-ev-the-organisation-behind-the-platform)
2. [History: from Gitea hosting to Forgejo stewardship](#history-from-gitea-hosting-to-forgejo-stewardship)
3. [How Codeberg differs from GitHub and GitLab.com](#how-codeberg-differs-from-github-and-gitlabcom)
4. [What Codeberg provides](#what-codeberg-provides)
5. [Who uses Codeberg and why](#who-uses-codeberg-and-why)
6. [Codeberg's community and values](#codebergs-community-and-values)
7. [Limitations and trade-offs](#limitations-and-trade-offs)
8. [Try It Yourself](#try-it-yourself)
9. [Common Mistakes](#common-mistakes)
10. [Summary](#summary)
11. [Sources](#sources)

---

## Codeberg e.V.: the organisation behind the platform

**Codeberg e.V.** is a German registered association (eingetragener Verein, "e.V."). An e.V. in Germany has a formal legal structure: it must have a written constitution (Satzung), hold regular general assemblies, elect a board, file annual accounts with the registry and is prohibited from distributing profits to members. It is a genuine non-profit with legal accountability.

The association's stated mission is to promote free and open source software, free knowledge and a free society. Codeberg's hosting service is one realisation of this mission: providing infrastructure for open source developers without commercial interests or investor pressure shaping product decisions.

Codeberg e.V. is funded by voluntary donations from users and supporters. There are no venture capital investors, no advertising revenue and no data monetisation. The sustainability model depends on community support.

### Who controls Codeberg?

Codeberg e.V.'s members elect the board. The board operates the organisation and makes strategic decisions. Members can vote on major decisions at general assemblies. This is meaningfully different from a company owned by shareholders or a project controlled by a single for-profit entity.

Codeberg also stewards the Forgejo project - the open source software that powers the Codeberg hosting service. This creates alignment: Codeberg has a strong incentive to keep Forgejo healthy because it runs on it, and Forgejo benefits from Codeberg's infrastructure and community.

---

## History: from Gitea hosting to Forgejo stewardship

Codeberg began as a community effort to provide Gitea hosting for open source projects. The founding team believed that the open source community should have its own collaborative infrastructure, not be dependent on commercial platforms with misaligned interests.

The platform launched publicly around 2019 and grew steadily. By 2022, it was the largest single Gitea instance outside of the software development shops that self-hosted Gitea internally.

In October 2022, the Gitea governance controversy erupted (covered in detail in the `gitea/` and `forgejo/` folders). Two Gitea maintainers incorporated a for-profit company and transferred the project's domains and trademarks without community notice. Codeberg, which ran Gitea for all of its users, was directly affected by this governance failure.

In December 2022, Codeberg e.V. announced and hosted the **Forgejo** project - the community fork of Gitea. Codeberg became Forgejo's primary infrastructure provider and institutional steward. In early 2023, Codeberg migrated its own platform from Gitea to Forgejo.

Today, Codeberg serves two roles simultaneously:

1. A public hosted Git service (codeberg.org) for open source projects
2. The institutional home of the Forgejo project

---

## How Codeberg differs from GitHub and GitLab.com

### Governance and ownership

**GitHub** is owned by Microsoft (acquired 2018 for $7.5 billion). Product decisions are made by a corporation with commercial obligations to its parent company and enterprise customers.

**GitLab.com** is the cloud service of GitLab Inc., a publicly traded company (GTLB on NASDAQ). Enterprise revenue drives product priorities.

**Codeberg** is run by a German non-profit. No investors. No advertising. No commercial enterprise product shaping the roadmap.

### Data and privacy

Codeberg's privacy policy commits to not selling user data, not using tracking pixels and not running advertising. The platform is hosted in Germany under EU/GDPR jurisdiction. For developers concerned about data sovereignty, this is meaningful.

GitHub's data practices are governed by Microsoft's privacy policy and the EU-US Data Privacy Framework. GitLab.com processes data through a US company.

### Sustainability model

GitHub and GitLab.com are sustained by commercial revenue: enterprise plans, Copilot subscriptions, marketplace fees and GitHub Advanced Security licences. Their free tiers are customer acquisition channels.

Codeberg is sustained by donations. The free tier is the product, not a funnel. There is no premium tier to upsell to.

### AI features

GitHub has invested heavily in AI (Copilot, code review AI, PR summaries). GitLab has Duo. Codeberg has no AI features. The Forgejo project has no current plans for AI integration. If AI tooling in your Git platform matters, Codeberg is not the right choice.

### Scale and ecosystem

GitHub hosts hundreds of millions of repositories. The GitHub ecosystem - Actions marketplace, integrations, social discovery, stars as social proof - is enormous. Codeberg is much smaller. Most major open source projects live on GitHub. Discoverability and community features on Codeberg are more limited.

### CI/CD

GitHub has Actions (native, tightly integrated). GitLab.com has native CI/CD. Codeberg provides **Woodpecker CI** as an optional external service - it works well but requires a separate account and configuration step. Codeberg does not have native CI/CD built into the platform the way GitHub and GitLab do.

---

## What Codeberg provides

### Core features (free for everyone)

**Git hosting**: unlimited public repositories. Private repositories are available. No repository size limit stated explicitly, though very large repositories are subject to fair use.

**Pull requests and code review**: identical to Forgejo's PR workflow - inline comments, code suggestions, review approvals, protected branches.

**Issues and project management**: issues, labels, milestones, Kanban boards, issue templates.

**Codeberg Pages**: free static site hosting from a `pages` branch or a dedicated `pages` repository. Custom domains supported. HTTPS provided automatically via Let's Encrypt.

**Package registry**: inherited from Forgejo - npm, PyPI, NuGet, Maven, Docker/OCI, Helm, Debian, Alpine and more.

**Releases**: versioned releases with binary assets attached to Git tags.

**Wikis**: per-repository documentation.

**Organisation accounts**: organisations with teams and permissions.

**SSH and HTTPS Git access**: standard Git protocol support.

**REST API**: Forgejo-compatible REST API with Swagger documentation at [codeberg.org/api/swagger](https://codeberg.org/api/swagger).

**Woodpecker CI**: available via a separate service at [ci.codeberg.org](https://ci.codeberg.org). Requires opting in per repository. GitHub Actions-compatible workflow syntax.

### What Codeberg does not provide

- **AI features**: no code completion, no PR summaries, no AI review
- **Native CI/CD**: Woodpecker CI is external, not built-in like GitHub Actions
- **GitHub Actions compatibility**: Woodpecker CI uses similar but not identical YAML
- **Sponsor/monetisation features**: no built-in way to accept donations for your project
- **Mobile app**: no dedicated Codeberg mobile app (GitHub has iOS and Android apps)

---

## Who uses Codeberg and why

### Free software advocates

Developers and projects committed to the philosophy that software infrastructure should be community-owned and free. KDE (the desktop environment), GNOME (another major Linux desktop), and many other established free software projects host mirrors or primary repositories on Codeberg.

### Privacy-conscious developers

Developers who do not want their code, collaboration history and contribution patterns stored on a US corporate platform subject to US surveillance law (FISA 702, national security letters).

### European public sector and academic projects

German public administration projects increasingly use Codeberg as a default choice given GDPR compliance, EU jurisdiction and non-commercial governance. Academic projects funded by institutions with open source mandates often land on Codeberg.

### GitHub migration refugees

Developers who left GitHub after the Microsoft acquisition (2018), after GitHub Copilot trained on their code without explicit consent (2022 controversy), or after GitHub's increasing focus on enterprise and AI rather than community.

### NLnet-funded projects

NLnet Foundation frequently funds projects with requirements to use community-governed infrastructure. Many NLnet grantees host on Codeberg as a result.

---

## Codeberg's community and values

Codeberg has a Code of Conduct and a community that actively discusses the platform's direction. The Codeberg forum and issue tracker are used for feature requests and governance discussions.

The community tends toward:

- Strong preference for free software licences (GPL, AGPL, MIT)
- Scepticism of proprietary tools and lock-in
- Valuing privacy and data sovereignty
- Supporting smaller projects and individual developers as equals to large organisations

This culture shapes the platform in subtle ways. Codeberg does not optimise for viral growth metrics or engagement. It optimises for being a reliable, trustworthy home for open source code.

---

## Limitations and trade-offs

### Smaller ecosystem

GitHub's advantage of network effects is real. Pull requests from strangers, issue reports from users you have never met, contributors finding your project through explore or trending pages - all of this is much more likely on GitHub. If community growth and visibility matter for your project, GitHub remains the dominant choice.

### CI/CD friction

Setting up Woodpecker CI requires a separate authentication step and slightly more configuration than GitHub Actions. For teams used to GitHub Actions working immediately, this is a small but real friction point.

### Resource limits

Codeberg is funded by donations from a relatively small community. Compute resources for CI/CD are limited. Codeberg asks teams with high CI usage to run their own self-hosted runners to avoid consuming shared resources.

### No private repository for organisations on free tier

Organisation accounts on Codeberg do not offer private repositories on the free tier. Private repositories are available for personal accounts and for organisations that support Codeberg financially. Check the current policy at [codeberg.org/Codeberg/Community](https://codeberg.org/Codeberg/Community) as this evolves.

### No SLA or guaranteed uptime

Codeberg is operated by volunteers and a small team. There is no enterprise SLA. Downtime, while infrequent, does happen without guaranteed response times. For mission-critical infrastructure, a commercial platform with SLA guarantees may be more appropriate.

---

## Try It Yourself

**Exercise 1 - Create a Codeberg account**

1. Go to [codeberg.org](https://codeberg.org)
2. Click **Register**
3. Fill in username, email and password
4. Verify your email address

**Exercise 2 - Explore the interface**

1. Once registered, browse the **Explore** page to see recent public activity
2. Search for a project you know (many GNOME, KDE and other free software projects have mirrors or primary repos here)
3. Look at your profile page - note the federated identity (`@username@codeberg.org`) shown on profiles

**Exercise 3 - Compare with GitHub**

Open [codeberg.org](https://codeberg.org) and [github.com](https://github.com) side by side. Compare:

- The explore/discovery pages
- A repository's issue list
- The pull request interface
- What is absent on Codeberg (AI features, Copilot, sponsor buttons)

**Exercise 4 - Check the governance documents**

- [Codeberg e.V. about page](https://codeberg.org/Codeberg/Community/wiki/About-Codeberg)
- [Codeberg terms of service](https://codeberg.org/Codeberg/org/src/branch/main/TOS.md)
- [Codeberg privacy policy](https://codeberg.org/Codeberg/org/src/branch/main/PrivacyPolicy.md)

---

## Common Mistakes

**Expecting GitHub parity**

Codeberg is not a GitHub clone with different ownership. It is a smaller platform with a different mission. The ecosystem is smaller, AI features do not exist, CI requires an extra step and discoverability is limited. Do not choose Codeberg expecting a GitHub experience - choose it because the mission and governance align with your values or requirements.

**Hosting commercial or proprietary code**

Codeberg's terms of service and community ethos focus on open source. Hosting proprietary or closed-source code is technically possible but misaligned with the platform's purpose. For private, proprietary code, use a commercial platform.

**Expecting SLA-grade reliability**

Codeberg is maintained by volunteers and a small team funded by donations. It is reliable but not at enterprise SLA standards. Do not build production pipelines that depend on Codeberg uptime guarantees.

**Not supporting Codeberg financially**

Codeberg depends on donations. If you use it, consider supporting it at [codeberg.org/Codeberg/Community/wiki/Donate](https://codeberg.org/Codeberg/Community/wiki/Donate). The platform's long-term sustainability depends on the community it serves.

---

## Summary

Codeberg is a free, public Git hosting service run by Codeberg e.V., a German non-profit. It powers open source projects with Git hosting, pull requests, issues, project boards, static site hosting (Codeberg Pages), a package registry and CI/CD via Woodpecker CI.

The platform runs Forgejo, the community fork of Gitea. Codeberg e.V. is also the institutional steward of the Forgejo project.

Codeberg differs from GitHub and GitLab.com in governance (non-profit vs corporate), data practices (EU jurisdiction, no tracking or advertising), sustainability model (donations vs enterprise revenue) and AI features (none on Codeberg).

The trade-offs: smaller ecosystem and community, CI friction compared to GitHub Actions, no mobile app, resource limits on CI and no enterprise SLA.

The right audience for Codeberg: free software advocates, privacy-conscious developers, European public sector projects, developers who have migrated from GitHub and want community-governed infrastructure.

---

## Sources

- [Codeberg: About Codeberg e.V.](https://codeberg.org/Codeberg/Community/wiki/About-Codeberg)
- [Codeberg: Terms of service](https://codeberg.org/Codeberg/org/src/branch/main/TOS.md)
- [Codeberg: Privacy policy](https://codeberg.org/Codeberg/org/src/branch/main/PrivacyPolicy.md)
- [Codeberg: Community discussions](https://codeberg.org/Codeberg/Community)
- [Forgejo: About Forgejo](https://forgejo.org/about/)
- [Woodpecker CI: Documentation](https://woodpecker-ci.org/docs/intro)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

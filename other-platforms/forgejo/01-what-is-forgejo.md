# What is Forgejo?

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Forgejo is a self-hosted Git platform steered by Codeberg e.V., a German non-profit organisation. It forked from Gitea in December 2022 following a governance dispute over who controlled the Gitea project's assets, and became a hard fork in February 2024 when it began diverging intentionally from Gitea's codebase. Forgejo's defining characteristics are its non-profit governance, fully free software model (no proprietary enterprise tier), SAML authentication in the free edition and active development of ActivityPub federation between instances.

---

## Table of Contents

1. [Why Forgejo exists: the governance dispute](#why-forgejo-exists-the-governance-dispute)
2. [Codeberg e.V. and non-profit governance](#codeberg-ev-and-non-profit-governance)
3. [Soft fork to hard fork: the development timeline](#soft-fork-to-hard-fork-the-development-timeline)
4. [The licence change: MIT to GPLv3+](#the-licence-change-mit-to-gplv3)
5. [Forgejo in 2026: the current state](#forgejo-in-2026-the-current-state)
6. [What Forgejo provides](#what-forgejo-provides)
7. [ActivityPub federation: forge federation](#activitypub-federation-forge-federation)
8. [Who uses Forgejo and why](#who-uses-forgejo-and-why)
9. [Key concepts before you start](#key-concepts-before-you-start)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## Why Forgejo exists: the governance dispute

In October 2022, two Gitea maintainers incorporated a company called Gitea Limited in the UK without informing the broader contributor community. The project's domain names (`gitea.com`, `gitea.io`) and trademarks were transferred to this new entity. When the community discovered the transfer, 48 contributors signed an open letter demanding the assets be returned to a community-controlled non-profit foundation.

Gitea Limited (later restructured as CommitGo Inc.) declined. The contributors concluded that the only way to have a genuinely community-owned Git platform was to fork the codebase and steward it under a non-profit.

In **December 2022**, Forgejo was announced. The name is Esperanto for "forge" - a deliberate choice of a language designed to be neutral and belong to everyone. Within weeks, Codeberg (the largest public Forgejo/Gitea-based hosting platform, a German non-profit) announced it would migrate its own infrastructure from Gitea to Forgejo.

The full governance context and comparison is in [../gitea/10-gitea-vs-forgejo.md](../gitea/10-gitea-vs-forgejo.md).

---

## Codeberg e.V. and non-profit governance

Codeberg e.V. is a registered German association (eingetragener Verein - "e.V.") incorporated to serve the public interest by promoting free and open source software. An e.V. in Germany has a formal democratic membership structure, must file annual accounts and cannot distribute profits to members. It is a genuine non-profit, not a foundation with corporate sponsors controlling the board.

Forgejo is hosted at [codeberg.org/forgejo/forgejo](https://codeberg.org/forgejo/forgejo) - on Codeberg's own Forgejo instance. Development is carried out in public. The Forgejo governance model involves elected maintainers and a transparent decision-making process, documented publicly in the project's governance repository.

**Why governance matters for software choices:**
- Forgejo cannot be acquired by a private company that then adds proprietary features
- There is no pressure to monetise through an enterprise tier
- Feature decisions are made on technical and community merit, not commercial strategy
- The project cannot be killed by a single company's decision

---

## Soft fork to hard fork: the development timeline

### Phase 1: Soft fork (December 2022 - October 2023)

During this period, Forgejo tracked Gitea's releases closely. The Forgejo team back-ported Gitea's patches and applied them to Forgejo, but intentionally avoided diverging. The goal was maximum compatibility - if Gitea fixed a bug, Forgejo got the same fix. The two platforms were functionally identical.

### Phase 2: Intentional divergence begins (November 2023)

Forgejo v1.21.0-0 was the last release closely tracking Gitea. From this point, Forgejo began adding features that were not in Gitea and declining to adopt some Gitea design decisions. The development teams were now making independent choices.

Key early divergences:
- Forgejo added SAML authentication to the free edition
- Forgejo began building ActivityPub federation infrastructure
- Forgejo adopted the GPLv3+ licence for new code

### Phase 3: Hard fork (February 2024)

With the release of Forgejo v7.0 (based on Gitea's 1.21 lineage but substantially diverged), Forgejo officially declared itself a hard fork. The database schemas diverged in ways that prevented direct upgrade from Gitea to Forgejo without migration tooling. The two platforms were now distinct software.

The Forgejo team published a detailed post titled "Forking Forward" explaining the decision and what it meant for users.

### Phase 4: Independent development (2024-present)

Forgejo continues as a fully independent platform. Version numbering jumped to reflect the independence: v7.0, v8.0, v9.0 - using semantic versioning without the legacy `1.x` prefix that Gitea maintains. In early 2026, Forgejo is at v9.0.x.

---

## The licence change: MIT to GPLv3+

When Forgejo was a soft fork, it used the same MIT licence as Gitea. In 2023, Forgejo began licensing new code under **GPLv3+** (GNU General Public Licence version 3 or later). The change was phased - existing MIT-licensed code remains MIT, but new contributions to the Forgejo-specific codebase are GPLv3+.

### What GPLv3+ means for Forgejo users

**For self-hosters**: No practical impact. You can run Forgejo on your own infrastructure, modify it for your needs and keep modifications private. GPLv3 copyleft obligations only apply when you distribute the software.

**For SaaS providers**: If you run Forgejo as a service for others (i.e. you provide access to a Forgejo instance for external users), you must make the source code of your modified version available to those users. This is the "network use" provision of the AGPL model; GPLv3 itself has a more limited version of this.

**For vendors who want to build proprietary products**: You cannot take Forgejo, add proprietary features and sell them without releasing the source. This is intentional - it prevents the CommitGo/Gitea Enterprise scenario from happening to Forgejo.

**For most organisations**: Choose Forgejo, self-host it, modify it if needed, keep those modifications to yourself (you are not distributing to third parties). No licence obligations triggered.

### Why the community chose GPLv3+ over AGPL

The Forgejo community deliberately chose GPLv3+ rather than AGPL (which has stronger network copyleft). The reasoning: AGPL would create friction for organisations that want to customise Forgejo for internal use. GPLv3+ is permissive enough for self-hosting while preventing proprietary forks for commercial distribution.

---

## Forgejo in 2026: the current state

### Version and releases

Forgejo's current stable version is **v9.0.x** (April 2026). Check [codeberg.org/forgejo/forgejo/releases](https://codeberg.org/forgejo/forgejo/releases) for the exact current patch version. Forgejo follows a release cadence roughly matching major features, with patch releases for security fixes.

### What makes Forgejo v9.0 notable

- **SAML included**: SAML 2.0 SSO authentication is part of the core free edition
- **Federation development ongoing**: ActivityPub federation for following repositories across instances is in active development (not yet production-ready for all use cases)
- **Forgejo runner**: the maintained fork of act_runner for Forgejo Actions
- **GPLv3+ codebase stabilising**: the licence transition is well-established

### Codeberg.org

Codeberg (codeberg.org) is the largest public Forgejo instance. It provides free hosting for open source projects and is run by Codeberg e.V. Codeberg is both Forgejo's primary user and its primary infrastructure provider. If you want to try Forgejo without self-hosting, create a free account on Codeberg.

### Windows support

Forgejo dropped official Windows support in 2024. The project does not provide Windows binaries and does not test on Windows. If you need to run a Gitea-compatible platform on Windows, use Gitea. Forgejo is Linux and macOS only.

---

## What Forgejo provides

Forgejo provides all the features of the Gitea community edition (from which it forked) plus additional features in the free edition:

### Core Git hosting (same as Gitea)

- Unlimited repositories (public, private, internal)
- SSH and HTTPS clone
- Web-based code browsing and editing
- Blame view, commit history, diff viewer
- Repository mirroring and import from GitHub, GitLab, Bitbucket
- Git LFS support
- Repository forks, stars and topics

### Code review

- Pull requests with diff view
- Inline comments and code suggestions
- Review approval system (approve, request changes)
- Draft pull requests
- Protected branches with required approvals and status checks
- Merge commit, squash and rebase strategies
- Auto-merge

### Issues and project management

- Issues with labels, milestones, assignees
- Issue templates
- Issue dependencies
- Kanban project boards
- Reactions and cross-repository references

### CI/CD: Forgejo Actions

- GitHub Actions-compatible workflow syntax
- Self-hosted Forgejo runner (maintained fork of act_runner)
- Workflow files in `.forgejo/workflows/` or `.github/workflows/`
- Matrix builds, caching, artefacts

### Package registry

All package formats from Gitea: npm, PyPI, NuGet, Maven, Cargo, Docker/OCI, Helm, Debian, Alpine, RPM, RubyGems, Go, Generic and more.

### Forgejo-exclusive free features

**SAML 2.0 authentication**: Connect to Okta, OneLogin, Azure AD, Keycloak and any SAML identity provider. This is included free. In Gitea, SAML requires the paid Enterprise licence.

**ActivityPub federation (experimental)**: Follow repositories across Forgejo instances, receive cross-instance notifications. The federation stack is under active development.

---

## ActivityPub federation: forge federation

ActivityPub is the protocol behind the Fediverse - the decentralised social network that includes Mastodon, PeerTube and others. Forgejo is implementing ActivityPub support to enable "forge federation": Git hosting instances communicating with each other.

### What federation enables (in development)

- **Follow a repository**: A developer on instance A can follow a repository on instance B and receive notifications when new releases, issues or commits appear
- **Fork across instances**: Fork a repository from one Forgejo instance to another, creating a genuine distributed development workflow
- **Comment across instances**: Eventually, participate in issues and pull requests on remote instances from your home instance

### Current status (2026)

Federation is in active development but not yet stable enough for all production use cases. Basic following and notification delivery works between Forgejo instances. Cross-instance forking and commenting are works in progress.

Gitea received an NLnet grant to implement federation but active development moved primarily to Forgejo after the split. Gitea has minimal federation capability; Forgejo is where the serious work is happening.

### The Forge Federation working group

The [Forge Federation](https://forgefed.org) working group is developing the ForgeFed protocol specification - a set of ActivityPub extensions specific to forge (Git hosting) use cases. Forgejo is the primary implementation. The goal is a future where a developer can have a single identity on their preferred Forgejo instance and participate in the global open source community without needing accounts on every platform.

---

## Who uses Forgejo and why

### Codeberg users

Codeberg is the most accessible way to use Forgejo. Thousands of open source projects are hosted there, including many that migrated from GitHub for philosophical reasons or from Gitea after the governance dispute.

### Organisations with SAML requirements

Any organisation that requires SAML SSO (common in enterprises using Okta, Azure AD or similar identity providers) but cannot or will not pay for Gitea Enterprise. Forgejo provides SAML free. This is the single most practical driver for choosing Forgejo over Gitea in enterprise contexts.

### Free software advocates

Developers and organisations who require that all their infrastructure is free software under copyleft licences. GitLab CE is free but complex; Gitea is MIT but has a proprietary enterprise tier; Forgejo is GPLv3+ with no proprietary fork.

### Federation-interested deployments

Research institutions, academic networks and organisations building decentralised infrastructure who want to participate in forge federation as it matures.

---

## Key concepts before you start

### Forgejo runner vs act_runner

Forgejo maintains its own fork of act_runner called the **Forgejo runner**. The two are similar but Forgejo's runner is specifically tested against Forgejo releases and may receive Forgejo-specific improvements. When installing, download the Forgejo runner from Forgejo's releases, not the generic act_runner from the Gitea project.

### app.ini

Like Gitea, Forgejo uses an `app.ini` configuration file. The structure and most keys are identical to Gitea. SAML configuration adds new sections not present in Gitea's app.ini.

### Workflow file location

Forgejo Actions reads workflow files from:
- `.forgejo/workflows/*.yml` (Forgejo-specific, preferred)
- `.github/workflows/*.yml` (GitHub-compatible)
- `.gitea/workflows/*.yml` (Gitea-compatible)

Use `.forgejo/workflows/` for Forgejo-specific deployments.

### Federation identity

When federation is enabled, each user has a federated identity: `@username@forgejo.example.com`. This is your identity across the Fediverse for forge-related activities.

---

## Try It Yourself

**Exercise 1 - Explore Codeberg**

1. Visit [codeberg.org](https://codeberg.org)
2. Create a free account (Codeberg runs Forgejo)
3. Explore the interface - compare it to GitHub and Gitea
4. Notice the federation indicator on user profiles (ActivityPub link)
5. Browse the [codeberg.org/forgejo](https://codeberg.org/forgejo) organisation to see Forgejo's own development

**Exercise 2 - Compare with Gitea**

Open [gitea.com](https://gitea.com) and [codeberg.org](https://codeberg.org) side by side. Look for:
- Interface similarities (shared origin)
- The SAML option in Codeberg's sign-in (if enabled for the instance)
- Any federation-related UI elements on Codeberg
- Version numbers (Forgejo shows its own version)

**Exercise 3 - Read the governance documents**

- [Forgejo governance](https://codeberg.org/forgejo/governance) - how decisions are made
- [Codeberg about page](https://codeberg.org/Codeberg/Community/wiki/About-Codeberg) - the non-profit structure
- [Forge Federation spec](https://forgefed.org) - the federation protocol

---

## Common Mistakes

**Assuming Forgejo runs on Windows**

Forgejo dropped Windows support in 2024. There are no official Windows binaries. If your infrastructure includes Windows servers, you need Gitea or a different platform. Do not attempt to run Forgejo on Windows in production.

**Comparing version numbers to Gitea**

Forgejo v9.0 does not mean it has fewer features than Gitea v1.25. They use completely different versioning schemes. The numbers are not comparable. Forgejo's higher numbers reflect its independent release cadence since the hard fork.

**Expecting production-ready federation today**

ActivityPub federation is in active development but not fully stable for all use cases in 2026. Basic following works. Cross-instance forking and commenting are works in progress. Do not choose Forgejo primarily for federation if you need it for production workloads right now - evaluate the current state at the time of your decision.

**Treating GPLv3+ as more restrictive than it is for self-hosters**

The GPLv3+ licence creates obligations when you distribute the software. Self-hosting Forgejo for your organisation (which uses it internally) does not trigger copyleft obligations. The licence is only relevant if you plan to redistribute modified Forgejo or offer it as a service to external users.

---

## Summary

Forgejo forked from Gitea in December 2022 after two Gitea maintainers incorporated a for-profit company and transferred the project's assets without community consent. Codeberg e.V., a German non-profit, stewards Forgejo under a community governance model with no corporate controller.

Forgejo became a hard fork in February 2024 when its codebase diverged beyond backward compatibility with Gitea. Version numbers are independent (v9.0.x in 2026, not comparable to Gitea's v1.25.x).

New Forgejo code is licenced GPLv3+. Existing code from the Gitea lineage remains MIT. For self-hosters, the practical impact is minimal - GPLv3 obligations only apply to software distribution, not internal use.

Forgejo's key differentiators over Gitea community edition: SAML authentication is free (Gitea Enterprise charges for this), ActivityPub federation is actively developed (Gitea's effort stalled) and governance is non-profit with no commercial interests to manage.

Forgejo does not support Windows. It runs on Linux and macOS.

---

## Sources

- [Forgejo: About page](https://forgejo.org/about/)
- [Forgejo: Forking Forward (hard fork announcement)](https://forgejo.org/2024-02-forking-forward/)
- [Forgejo: First monthly update (December 2022)](https://forgejo.org/2022-12-26-monthly-update/)
- [Codeberg: About Codeberg e.V.](https://codeberg.org/Codeberg/Community/wiki/About-Codeberg)
- [Forgejo: Licence change rationale](https://codeberg.org/forgejo/forgejo/issues/1561)
- [Forge Federation: ForgeFed specification](https://forgefed.org)
- [LWN: Forgejo makes a full break from Gitea](https://lwn.net/Articles/963095/)
- [Open letter to Gitea owners](https://gitea-open-letter.coding.social/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

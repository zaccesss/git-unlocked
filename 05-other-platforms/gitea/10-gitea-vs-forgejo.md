# Gitea vs Forgejo

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Gitea and Forgejo share a common codebase origin but are now distinct products with different governance, licences, feature priorities and long-term trajectories. Choosing between them is a real decision with lasting consequences - particularly around federation, Windows support, SAML authentication and the open-core vs fully-free software philosophy.

This file explains the history of the split, maps what has diverged, and gives a clear framework for choosing between them in 2026.

---

## Table of Contents

1. [The split: a brief timeline](#the-split-a-brief-timeline)
2. [Governance and ownership](#governance-and-ownership)
3. [Licence differences](#licence-differences)
4. [Feature divergence in 2026](#feature-divergence-in-2026)
5. [Performance and resource usage](#performance-and-resource-usage)
6. [Community and ecosystem](#community-and-ecosystem)
7. [Versions and compatibility](#versions-and-compatibility)
8. [Migrating between them](#migrating-between-them)
9. [Which to choose](#which-to-choose)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## The split: a brief timeline

| Date | Event |
|---|---|
| Nov 2016 | Gitea forked from Gogs |
| Oct 2022 | Two Gitea maintainers incorporate Gitea Limited (UK) without community notice; domains transferred to the company |
| Nov 2022 | Open letter signed by 48 contributors demands return of assets to community control |
| Dec 2022 | Forgejo announced as a community fork; hosted on Codeberg |
| Feb 2023 | Codeberg migrates its own instance from Gitea to Forgejo |
| Nov 2023 | Forgejo becomes a **soft fork** - intentionally diverges from Gitea for the first time |
| Feb 2024 | Forgejo becomes a **hard fork** - drops Git compatibility with Gitea releases, begins independently developing the federation (ActivityPub) stack |
| Mar 2024 | CommitGo Inc. (US entity) launches **Gitea Enterprise** with proprietary SAML, audit logs and IP allowlist |
| Apr 2024 | Forgejo v8.0 released as the first truly independent major release |
| 2025 | Forgejo reaches v14.x; Gitea at v1.25.x; version numbers no longer meaningful to compare |

---

## Governance and ownership

### Gitea

**Entity**: CommitGo, Inc. (Delaware, US) owns the domains, trademarks and the commercial entity. The open source project operates under a Technical Oversight Committee (TOC) with six members: three appointed by CommitGo and three elected by the community. TOC members are compensated at $500/month from OpenCollective funds.

**Model**: Open-core. The community edition (MIT) is the foundation. Gitea Enterprise adds proprietary features (SAML, audit logs, IP allowlist, dependency scanning) sold commercially by CommitGo.

**Decision-making**: CommitGo holds tie-breaking authority on TOC deadlocks and controls the commercial roadmap. The community elects three seats but cannot override CommitGo's three on contested issues.

### Forgejo

**Entity**: Codeberg e.V. is a registered German non-profit (eingetragener Verein) that stewards the Forgejo project. Codeberg operates its own Forgejo instance (codeberg.org) and provides infrastructure for the project.

**Model**: Fully free software. No proprietary add-ons. No commercial tier. Revenue comes from donations to Codeberg e.V.

**Decision-making**: Community-driven. The Forgejo governance structure involves maintainers elected by contributors. No single company controls the project.

---

## Licence differences

| Aspect | Gitea | Forgejo |
|---|---|---|
| Community edition licence | MIT | MIT (through v8.x), then GPLv3+ from v9.0 |
| Enterprise tier | Proprietary (CommitGo) | Does not exist |
| Can you fork and commercialise? | Yes (MIT) | MIT code: yes. GPLv3+ code: modifications must remain GPLv3+ |

### What the GPLv3+ licence change means for Forgejo

Starting with Forgejo v9.0, new Forgejo code is licenced under GPLv3+. This means:
- You can self-host Forgejo for free forever
- You can modify and redistribute it, but modifications must also be GPLv3+
- You cannot take Forgejo, add proprietary features and sell them without releasing the source (the Gitea/CommitGo model)
- Running Forgejo as a service for others (SaaS) has specific GPLv3 obligations around source availability

For most self-hosters, the GPLv3+ licence change has no practical effect. It matters for vendors who want to build proprietary products on top of the codebase.

---

## Feature divergence in 2026

These are the meaningful functional differences between Gitea and Forgejo in 2026.

### Features in Forgejo not in Gitea (community edition)

**ActivityPub federation (F3 / ForgeFed)**
Forgejo's flagship differentiator. Federation allows Forgejo instances to interact across servers: follow repositories on other Forgejo instances, receive issue notifications across federated instances, and eventually fork across instances. Gitea received NLnet grant funding for federation work but active development stalled and moved primarily to Forgejo.

**SAML authentication**
Forgejo includes SAML 2.0 SSO in the core, free tier. Gitea's SAML support is Gitea Enterprise (paid). This is one of the most significant practical differences for enterprise deployments that require SSO with Okta, OneLogin, Azure AD or similar SAML identity providers.

**Forgejo-specific runner improvements**
The Forgejo runner (a maintained fork of act_runner) has received additional stability and compatibility improvements specific to Forgejo deployments.

**Stricter open source governance**
Forgejo's code contributions undergo governance review to ensure no proprietary encumbrances. For organisations with strict open source procurement policies, this matters.

### Features in Gitea not in Forgejo

**Windows support**
Gitea continues to release official Windows binaries and supports Windows Server as a deployment platform. Forgejo dropped official Windows support in 2024. If your infrastructure includes Windows servers, Gitea is your only option between the two.

**macOS binary releases**
Gitea provides official macOS binaries. Forgejo provides macOS binaries inconsistently - primarily for development rather than production use.

**Gitea Enterprise features** (paid)
SAML authentication, audit logs, IP allowlist, branch protection inheritance and dependency scanning are available in Gitea Enterprise. None of these are in Forgejo Enterprise (which does not exist - all Forgejo features are in the community edition, though SAML is the key one).

**Longer release history and documentation**
Gitea has been public longer than Forgejo's independent releases. Some third-party tooling and documentation still references Gitea specifically.

### Features identical or near-identical in both

- Core Git hosting (clone, push, pull, browse)
- Pull requests and code review
- Issues, labels, milestones and project boards
- Gitea/Forgejo Actions (GitHub Actions-compatible CI/CD)
- Package registry (all 23+ formats)
- Web interface (still visually very similar)
- REST API (mostly compatible between recent versions)
- LDAP and OAuth2 authentication
- Webhooks and service integrations
- Git LFS

---

## Performance and resource usage

Both platforms have the same Go-based architecture and similar resource requirements at equivalent loads. Performance differences between a current Gitea and current Forgejo instance of the same version lineage are negligible.

Where you might notice differences:
- **Frontend**: Both completed the HTMX/Tailwind frontend rewrite, but at different paces. Individual releases may have temporary regressions.
- **Actions**: Forgejo's runner may have specific optimisations for its own instance. In practice, the difference is not observable for most workloads.
- **Federation overhead**: Forgejo's ActivityPub federation adds processing for instances that have it enabled. This is optional and off by default.

For resource planning, treat them identically: same hardware recommendations, same database requirements, same backup strategies.

---

## Community and ecosystem

### Gitea community

- GitHub repository: [github.com/go-gitea/gitea](https://github.com/go-gitea/gitea)
- Official instance: gitea.com (CommitGo-hosted)
- Forum: [forum.gitea.com](https://forum.gitea.com)
- Discord: Gitea Discord server
- Larger historical user base (Gitea predates Forgejo by 6 years)
- More third-party integrations and documentation specifically for Gitea

### Forgejo community

- Forgejo repository: [codeberg.org/forgejo/forgejo](https://codeberg.org/forgejo/forgejo)
- Official instance: codeberg.org (Codeberg e.V.-hosted, runs Forgejo)
- Discussion: Forgejo's own forum and issue tracker
- Smaller but rapidly growing; strong alignment with free software community
- Fediverse presence (mastodon.social/@forgejo)

### Third-party tool support

Most tools that support Gitea also support Forgejo since the API is mostly compatible. Gitea-specific documentation may reference features not present in Forgejo (SAML, Enterprise features) and vice versa (federation). Always check which platform documentation is written for.

---

## Versions and compatibility

### Why you cannot compare version numbers

Gitea is at v1.25.x. Forgejo is at v14.x (as of early 2026). The numbers are not comparable - Forgejo jumped its version numbering when it became a hard fork to clearly signal independence from Gitea's releases.

### API compatibility

The REST APIs were nearly identical through 2023. Since becoming a hard fork, Forgejo has added Forgejo-specific API endpoints (for federation, for Forgejo-specific features) while maintaining the core Gitea-compatible API. Tools built against the standard Gitea API continue to work on Forgejo for common operations.

### Migration compatibility

Gitea and Forgejo can migrate repositories between them using standard Git operations (push/pull). Issue and PR data requires the migration tool. There is no direct database migration between platforms - use the API or the web migration tools.

---

## Migrating between them

### From Gitea to Forgejo

Since both platforms share similar APIs and data structures, migration is feasible:

1. **Repository data**: clone all repositories from Gitea and push to Forgejo. Automate with the API:

```bash
# List all repos from Gitea
curl -H "Authorization: token GITEA_TOKEN" \
  https://gitea.example.com/api/v1/repos/search?limit=50 \
  | jq -r '.data[].clone_url'

# For each repo, mirror to Forgejo using the web migration tool
# Forgejo: + > New Migration > Gitea
```

2. **Issues and pull requests**: use Forgejo's built-in Gitea migration tool:
   - In Forgejo: **+** > **New Migration** > **Gitea**
   - Enter Gitea instance URL and access token
   - Select which data to migrate (repos, issues, PRs, labels, milestones, wiki)

3. **Users**: must be recreated manually or via the API. User history is preserved in migrated content but user accounts are separate.

4. **Organisation structure**: recreate organisations in Forgejo, then migrate repositories into them.

### From Forgejo to Gitea

The reverse process using Gitea's migration tool:
- In Gitea: **+** > **New Migration** > **Gitea** (Gitea can migrate from Forgejo using its Gitea-compatible migration path)
- Or use the generic Git migration for just the repository data

### Considerations before migrating

- **Open issues and PRs**: all open items migrate as data. Assigned users must exist in the target instance.
- **CI/CD workflows**: Gitea Actions and Forgejo Actions use the same workflow syntax. Workflows transfer directly.
- **Webhooks**: must be reconfigured for the new domain.
- **SSH keys**: users must re-add their SSH keys to the new instance.
- **Access tokens**: all tokens are invalidated. Users must create new ones.
- **DNS and clone URLs**: all `git remote` references in local repositories must be updated.

---

## Which to choose

There is no universally correct answer. The right choice depends on your specific situation.

### Choose Gitea if:

- **You run Windows servers**: Forgejo dropped Windows support in 2024. Gitea is the only option for Windows-based deployments.
- **You need SAML without paying**: Wait - actually SAML in Gitea requires the Enterprise (paid) tier. If you need free SAML, choose Forgejo.
- **You want maximum third-party documentation**: Gitea has a longer history and more community resources.
- **You are migrating from an existing Gitea instance**: Staying on Gitea avoids a platform migration.
- **You need Gitea Enterprise features**: Audit logs, IP allowlist, dependency scanning, SAML (paid) are Gitea Enterprise only.
- **Your team is already familiar with Gitea**: Switching has a real migration cost.

### Choose Forgejo if:

- **You need SAML authentication without paying**: Forgejo includes SAML in the free community edition. This is the single most impactful feature difference for enterprise deployments.
- **You value fully free software governance**: No proprietary tier, non-profit stewardship, GPLv3+ (new code).
- **You are interested in federation**: Forgejo is the only platform actively developing ActivityPub forge federation.
- **You are deploying on Linux**: Forgejo's primary and best-supported platform.
- **You have a strict open source procurement policy**: Forgejo's governance and licence may be easier to justify to legal/compliance teams.
- **You use Codeberg.org**: Codeberg runs Forgejo. If your team is already there, stay there.

### If you are starting fresh

For a new self-hosted deployment in 2026 with no existing investment:
- **Linux-only, open source philosophy, no Windows**: Forgejo is the stronger choice. SAML is free, federation is developing and governance is cleaner.
- **Mixed OS including Windows, or uncertain about Windows needs**: Gitea. You cannot run Forgejo on Windows in production.
- **Enterprise with budget for SAML/audit features**: Gitea Enterprise if you need the enterprise features; Forgejo free if SAML is all you need.

---

## Try It Yourself

**Exercise 1 - Compare the interfaces**

Open both platforms side by side:
- Gitea: [gitea.com](https://gitea.com)
- Forgejo: [codeberg.org](https://codeberg.org)

Compare:
- The repository creation flow
- The issues interface
- The profile page
- The organisation structure

Note the similarities and the small but growing differences.

**Exercise 2 - Check the API compatibility**

```bash
# Both should return similar user info
curl https://gitea.com/api/v1/users/search?q=gitea | python3 -m json.tool | head -30
curl https://codeberg.org/api/v1/users/search?q=forgejo | python3 -m json.tool | head -30
```

Note the common fields (login, full_name, avatar_url, html_url) - the API is compatible for core operations.

**Exercise 3 - Review the governance documents**

- Gitea TOC: [gitea.com/gitea/governance](https://gitea.com/gitea/governance)
- Forgejo governance: [codeberg.org/forgejo/governance](https://codeberg.org/forgejo/governance)

Compare how decisions are made, who has authority and how conflicts are resolved.

---

## Common Mistakes

**Assuming they are interchangeable**

Gitea and Forgejo look nearly identical and share common origins, but they are distinct software. SAML works differently (free in Forgejo, paid in Gitea Enterprise), Windows support differs and the federation feature set exists only in Forgejo. Treat them as separate products that happen to look similar.

**Choosing Forgejo for a Windows deployment**

Forgejo does not support Windows in production. If your servers run Windows Server, Gitea is your only option between the two. Check your infrastructure requirements before choosing.

**Choosing Gitea expecting free SAML**

Free SAML authentication is available only in Forgejo. Gitea's community edition supports LDAP and OAuth2 but not SAML. SAML requires Gitea Enterprise (paid commercial licence). Do not choose Gitea expecting to use Okta/OneLogin/Azure AD SAML without paying.

**Comparing version numbers**

Gitea v1.25 and Forgejo v14 are not meaningfully comparable. Forgejo's high version numbers reflect the fork's independent release cadence, not feature superiority or inferiority. Judge them on features, not version numbers.

**Not accounting for migration effort**

Switching between Gitea and Forgejo is a real project. Repository data migrates cleanly, but users must update SSH keys and access tokens, CI/CD webhook URLs change, all git remotes need updating and some tool integrations need reconfiguring. Budget time for this if you are switching an established team.

---

## Summary

Gitea and Forgejo share origins as a Gogs fork but diverged after the 2022 governance controversy. Gitea is now controlled by CommitGo (for-profit) with an open-core model. Forgejo is stewarded by Codeberg e.V. (non-profit) with a fully free software model.

The most practically significant differences in 2026: Forgejo includes SAML authentication free; Gitea requires the paid Enterprise tier. Gitea supports Windows Server; Forgejo does not. Forgejo is the only platform actively developing ActivityPub federation. Forgejo's new code is GPLv3+; Gitea is MIT.

Core features - Git hosting, pull requests, issues, Gitea/Forgejo Actions, the package registry and the REST API - are nearly identical and largely interoperable. The differences are at the edges: authentication methods, OS support, long-term governance and federation.

Choose Gitea for Windows deployments, existing Gitea investments or commercial Enterprise features. Choose Forgejo for free SAML, federation interest, open source governance philosophy or Linux-only deployments.

---

## Sources

- [Forgejo: About Forgejo](https://forgejo.org/about/)
- [Forgejo: Becoming a hard fork](https://forgejo.org/2024-02-forking-forward/)
- [Gitea: Blog - Governance update](https://blog.gitea.com/gitea-governance-update/)
- [Open letter to Gitea owners](https://gitea-open-letter.coding.social/)
- [LWN: Forgejo makes a full break from Gitea](https://lwn.net/Articles/963095/)
- [Codeberg: About Codeberg e.V.](https://codeberg.org/Codeberg/Community/wiki/About-Codeberg)
- [Gitea: CommitGo announcement](https://blog.gitea.com/hello-commitgo/)
- [Forgejo: Licence change rationale](https://codeberg.org/forgejo/forgejo/issues/1561)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

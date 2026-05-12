# Bitbucket Cloud vs Data Center

**Difficulty:** 🔴 Advanced | **Time:** 40 minutes

Bitbucket comes in two fundamentally different deployment models. **Bitbucket Cloud** is the hosted service run by Atlassian at [bitbucket.org](https://bitbucket.org) - you pay a monthly subscription and Atlassian manages all the infrastructure, upgrades, backups and availability. **Bitbucket Data Center** is the self-hosted version - you download and install it on your own servers, manage your own infrastructure, and are responsible for uptime, security patching and upgrades.

Choosing between them is not just a technical decision. It involves cost modelling, compliance requirements, operational capacity, infrastructure expertise and strategic alignment with your organisation's approach to software procurement. This file gives you everything you need to make or explain that decision.

> [!NOTE]
> **Bitbucket Server is no longer an option.** It reached end of life on 15 February 2024. All self-hosted deployments use Bitbucket Data Center. If your organisation is still running Bitbucket Server, it is running unsupported software with no security patches. Migration to Data Center or Cloud is urgent.

---

## Table of Contents

1. [The fundamental choice](#the-fundamental-choice)
2. [Feature comparison: Cloud vs Data Center](#feature-comparison-cloud-vs-data-center)
3. [Pricing models](#pricing-models)
4. [Bitbucket Cloud in depth](#bitbucket-cloud-in-depth)
5. [Bitbucket Data Center in depth](#bitbucket-data-center-in-depth)
6. [Infrastructure and system requirements](#infrastructure-and-system-requirements)
7. [Installation and setup](#installation-and-setup)
8. [High availability and clustering](#high-availability-and-clustering)
9. [Performance and scaling](#performance-and-scaling)
10. [Migrating from Cloud to Data Center](#migrating-from-cloud-to-data-center)
11. [Migrating from Data Center to Cloud](#migrating-from-data-center-to-cloud)
12. [Migrating from Bitbucket Server to Data Center](#migrating-from-bitbucket-server-to-data-center)
13. [The Bitbucket Hybrid Licence](#the-bitbucket-hybrid-licence)
14. [When to choose Cloud](#when-to-choose-cloud)
15. [When to choose Data Center](#when-to-choose-data-center)
16. [Try It Yourself](#try-it-yourself)
17. [Common Mistakes](#common-mistakes)
18. [Summary](#summary)
19. [Sources](#sources)

---

## The fundamental choice

The decision between Cloud and Data Center comes down to a few key questions:

**Who manages the infrastructure?**

- Cloud: Atlassian manages servers, networking, storage, backups, OS updates, Java updates, database maintenance and availability. Your team touches none of this.
- Data Center: Your team manages everything. You need Linux administration expertise, database management skills, storage management and on-call capacity for incidents.

**Where does your code live?**

- Cloud: In Atlassian's datacentres (AWS-hosted, in multiple regions). Atlassian has access to the infrastructure your code runs on.
- Data Center: On your own servers, in your own datacentre or private cloud. Only you have access to the hardware.

**What are your compliance requirements?**

- Cloud: Atlassian holds various compliance certifications (SOC 2 Type II, ISO 27001, FedRAMP Moderate for some products). Check the current certification list at the Atlassian Trust Center.
- Data Center: You are responsible for compliance. You can achieve any compliance posture because you control the entire stack - but you must do the work yourself.

**What is your budget model?**

- Cloud: Operational expenditure (OpEx). Monthly or annual subscription. Predictable, scales with users.
- Data Center: Upfront licence cost (CapEx/OpEx depending on your accounting) plus ongoing infrastructure costs. Can be cheaper at scale but requires capital.

---

## Feature comparison: Cloud vs Data Center

| Feature                         | Cloud                            | Data Center                    |
| ------------------------------- | -------------------------------- | ------------------------------ |
| **Git hosting**                 | Yes                              | Yes                            |
| **Pull requests**               | Yes                              | Yes                            |
| **Bitbucket Pipelines**         | Yes                              | No (use external CI)           |
| **Branch permissions**          | Yes                              | Yes                            |
| **Default reviewers**           | Yes                              | Yes                            |
| **Jira integration**            | Native (same Atlassian org)      | Via application links          |
| **Atlassian Intelligence / AI** | Yes (Standard+)                  | No                             |
| **Bitbucket Packages**          | Yes (Standard+)                  | No                             |
| **IP allowlisting**             | Yes (Premium)                    | Yes (network level)            |
| **Secret scanning**             | Yes (Standard+)                  | No                             |
| **SSH keys**                    | Yes                              | Yes                            |
| **API tokens**                  | Yes                              | Yes (local accounts)           |
| **SAML SSO**                    | Via Atlassian Guard              | Built-in                       |
| **LDAP authentication**         | Via Atlassian Guard              | Yes, built-in                  |
| **Audit log**                   | Yes (Premium)                    | Yes, built-in                  |
| **High availability**           | Built-in (Atlassian manages)     | Yes (Active-Active clustering) |
| **Windows OS support**          | N/A                              | No (Linux only since v8.0)     |
| **Upgrade control**             | Atlassian upgrades automatically | You control upgrade timing     |
| **Custom plugins**              | Limited (Marketplace apps)       | Full plugin API                |
| **Data residency control**      | Regional (EU, US, etc.)          | Complete (your hardware)       |
| **Mirror repositories**         | Yes                              | Yes (Smart Mirroring)          |
| **Webhook limits**              | Platform limits                  | Configurable                   |
| **Storage limits**              | Plan-based                       | Limited by your hardware       |

### Feature gaps that matter most

**Bitbucket Pipelines is Cloud-only.** Data Center has no built-in CI/CD. Data Center users must integrate with external CI systems: Jenkins, GitLab CI/CD (via mirrors), GitHub Actions (via mirrors), TeamCity, Bamboo Data Center (Atlassian's own CI product), or hosted services like CircleCI and Travis CI.

**AI features are Cloud-only.** Atlassian Intelligence (Rovo Dev), AI pull request descriptions and AI-powered search are available only on Cloud. Data Center does not receive these features.

**Secret scanning is Cloud-only.** Data Center has no native secret scanning. Third-party tools or pipeline-based scanning must be used.

---

## Pricing models

### Cloud pricing

Monthly subscription per user per workspace:

| Plan     | Price/user/month | Pipeline minutes | Users     |
| -------- | ---------------- | ---------------- | --------- |
| Free     | $0               | 50               | Max 5     |
| Standard | $3.65            | 2,500            | Unlimited |
| Premium  | $7.25            | 3,500            | Unlimited |

For a 100-person engineering team:

- Standard: 100 × $3.65 = $365/month = $4,380/year
- Premium: 100 × $7.25 = $725/month = $8,700/year

Plus Atlassian Guard if SSO is required: 100 × $4.20 = $420/month = $5,040/year

Total Premium + Guard for 100 users: ~$13,740/year

### Data Center pricing

Data Center uses annual subscription pricing based on user tier. The licence includes all features regardless of user count within the tier. Current approximate pricing (check [atlassian.com](https://www.atlassian.com/software/bitbucket/pricing) for current figures):

| User tier       | Annual price (approx.) |
| --------------- | ---------------------- |
| Up to 25 users  | ~$2,300/year           |
| Up to 50 users  | ~$4,200/year           |
| Up to 100 users | ~$7,500/year           |
| Up to 250 users | ~$15,000/year          |
| Up to 500 users | ~$25,000/year          |
| 500+ users      | Contact Atlassian      |

Data Center licence costs must be added to infrastructure costs:

- Servers (2+ application nodes for HA): $500-2,000/month depending on spec
- Database server: $200-500/month
- Shared storage (NFS or equivalent): $100-300/month
- Load balancer: $50-150/month
- Operations engineering time: significant (estimate 0.25-0.5 FTE for a team of 100)

For a 100-person team, Data Center total cost of ownership including infrastructure is often comparable to or higher than Cloud - the savings only materialise at larger scales (500+ users).

---

## Bitbucket Cloud in depth

### Regional data residency

Atlassian offers data residency for Cloud products, allowing you to choose which AWS region your Bitbucket data is stored in. Available regions include:

- United States (us-east-1)
- European Union (eu-central-1, Frankfurt)
- Australia (ap-southeast-2, Sydney)

Data residency affects where data is stored at rest. It does not guarantee that data is never processed in other regions (e.g. for globally distributed infrastructure components). For strict EU data sovereignty, evaluate the Atlassian Data Residency documentation carefully.

### Uptime and SLA

- Free and Standard: No formal SLA. Atlassian publces status at [status.atlassian.com](https://status.atlassian.com)
- Premium: 99.9% uptime SLA with credits if violated

Atlassian's actual uptime for Bitbucket Cloud is typically above 99.9% but the SLA is only formally guaranteed on Premium.

### Automatic upgrades

Atlassian upgrades Cloud continuously. New features, security patches and performance improvements are deployed without customer action. You cannot stay on an older version. If a breaking change is introduced, Atlassian provides migration guidance.

### Bitbucket Pipelines

Bitbucket Pipelines is exclusive to Cloud. It runs on Atlassian's infrastructure (Linux-based Docker containers). For Windows and macOS builds, self-hosted runners are required. Minutes are consumed from the plan allocation; self-hosted runners do not consume minutes.

---

## Bitbucket Data Center in depth

### Current version

The current Long Term Support release is **Bitbucket Data Center 10.2** (released March 2026, supported until March 2028). LTS releases receive bugfix and security patches for approximately 2 years.

Non-LTS versions are released more frequently with shorter support windows. For production environments, run an LTS release.

### No Bitbucket Pipelines

Data Center has no built-in CI/CD. You integrate with external systems:

**Jenkins** - the most common choice for Data Center users. Jenkins has a mature Bitbucket plugin that triggers builds on push events via webhooks and posts build status back to commits.

**Bamboo Data Center** - Atlassian's own CI/CD product, designed to integrate with Bitbucket Data Center. Deep integration similar to Bitbucket Pipelines on Cloud, but requires a separate Bamboo licence and infrastructure.

**GitLab CI/CD via mirrors** - complex but possible: mirror your Bitbucket repositories to GitLab and use GitLab CI/CD for the pipeline, with results posted back to Bitbucket.

**Self-hosted GitHub Actions runners** - Bitbucket can mirror to GitHub, triggering GitHub Actions workflows on push. Build status is posted back to Bitbucket.

### Authentication on Data Center

Data Center has its own local user account system but is typically integrated with:

**LDAP/Active Directory** - the most common enterprise authentication method. Bitbucket DC connects directly to your LDAP server and authenticates users against the directory without any additional Atlassian products.

**SAML 2.0 SSO** - built into Data Center (no Guard required). Configure your SAML identity provider (Okta, Azure AD, ADFS, PingFederate) directly in the Bitbucket DC admin panel.

**Crowd** - Atlassian's identity management product for self-hosted Atlassian tools. Crowd provides centralised SSO across Jira, Confluence and Bitbucket Data Center.

### Smart Mirroring

Data Center supports Smart Mirrors - read-only replica nodes distributed geographically. Developers cloning from a mirror get the repository data from the closest node rather than the central primary. This dramatically improves clone and fetch performance for globally distributed teams.

Mirrors are configured from the Bitbucket Data Center admin panel and connect to the primary instance. Push operations always go to the primary; pull operations can use the nearest mirror.

---

## Infrastructure and system requirements

### Bitbucket Data Center system requirements

**Minimum (small team, non-HA):**

- 4-core CPU
- 8 GB RAM
- 10 GB free disk (excluding repository data)
- Linux (RHEL/CentOS 7+, Ubuntu 18.04+, Debian 9+)
- Java 11 (LTS)
- Git 2.34.0 or later

**Recommended (production, 100 users):**

- 8-core CPU per application node
- 16 GB RAM per application node
- SSD storage
- PostgreSQL 14+ or MySQL 8.0+
- OpenSearch 2.x (for search indexing)
- Shared filesystem (NFS, GlusterFS or equivalent) for application nodes

**High availability minimum:**

- 2+ application nodes (active-active)
- External load balancer (HAProxy, Nginx, AWS ALB)
- Shared filesystem accessible by all nodes
- Clustered or replicated database

### Supported databases

| Database   | Supported versions |
| ---------- | ------------------ |
| PostgreSQL | 14, 15, 16         |
| MySQL      | 8.0, 8.4           |

Microsoft SQL Server and Oracle Database are not supported. If your organisation uses these databases, you need a separate PostgreSQL or MySQL instance for Bitbucket.

### Search (OpenSearch)

Bitbucket Data Center requires OpenSearch for code search and repository search functionality. Elasticsearch support was removed in version 9.0. You must run OpenSearch 2.x alongside Bitbucket.

OpenSearch can run on the same server as Bitbucket for small deployments but should be on separate hardware for production. OpenSearch requires:

- 4+ GB RAM dedicated to the JVM heap
- SSD storage recommended for index files

---

## Installation and setup

### Installing Bitbucket Data Center

**Step 1 - Download the installer**

Download from the [Atlassian downloads page](https://www.atlassian.com/software/bitbucket/download-archives):

```bash
# On the Bitbucket server
wget https://product-downloads.atlassian.com/software/bitbucket/downloads/atlassian-bitbucket-10.2.0-x64.bin
chmod +x atlassian-bitbucket-10.2.0-x64.bin
```

**Step 2 - Set up the database**

🐧 Linux (PostgreSQL example):

```bash
# Install PostgreSQL
sudo apt install postgresql postgresql-contrib

# Create the database and user
sudo -u postgres psql
CREATE USER bitbucket WITH PASSWORD 'securepassword';
CREATE DATABASE bitbucket OWNER bitbucket;
\q
```

**Step 3 - Run the installer**

```bash
sudo ./atlassian-bitbucket-10.2.0-x64.bin
```

The installer prompts for:

- Installation directory (default: `/opt/atlassian/bitbucket`)
- Home directory for data (default: `/var/atlassian/application-data/bitbucket`)
- Port (default: 7990)
- Whether to install as a service (yes, for production)

**Step 4 - Set up OpenSearch**

```bash
# Download and install OpenSearch
wget https://artifacts.opensearch.org/releases/bundle/opensearch/2.x/opensearch-2.x.0-linux-x64.tar.gz
tar xzf opensearch-2.x.0-linux-x64.tar.gz
cd opensearch-2.x.0/
./bin/opensearch
```

**Step 5 - Initial setup wizard**

Navigate to `http://your-server:7990` in a browser. The setup wizard guides you through:

1. Entering your Data Center licence key
2. Connecting to the database
3. Connecting to OpenSearch
4. Creating the first administrator account
5. Configuring the base URL

**Step 6 - Reverse proxy (production)**

For production, put Bitbucket behind Nginx or Apache to handle HTTPS termination:

```nginx
# /etc/nginx/sites-available/bitbucket
server {
    listen 443 ssl;
    server_name bitbucket.example.com;

    ssl_certificate /etc/ssl/certs/bitbucket.example.com.crt;
    ssl_certificate_key /etc/ssl/private/bitbucket.example.com.key;

    location / {
        proxy_pass http://localhost:7990;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
    }
}
```

---

## High availability and clustering

### Active-Active clustering

Bitbucket Data Center supports active-active clustering: multiple application nodes running simultaneously, all serving traffic. If one node goes down, the others continue serving requests with no downtime.

**Requirements for clustering:**

- All nodes share a common home directory (NFS or equivalent shared filesystem)
- A load balancer distributes traffic across nodes
- A shared database all nodes connect to
- A shared cache (Bitbucket uses Hazelcast for inter-node communication)
- Each node must be able to reach all other nodes on the Hazelcast port (typically 5701)

**Adding a second node:**

1. Ensure shared filesystem is mounted on both nodes
2. Install Bitbucket on the second node pointing to the same home directory and database
3. Configure the load balancer to include the second node
4. The nodes discover each other automatically via Hazelcast

### Zero-downtime upgrades

With active-active clustering, upgrades can be performed with zero downtime using a rolling restart:

1. Remove node 1 from the load balancer rotation
2. Upgrade Bitbucket on node 1
3. Restart node 1 - node 2 continues serving all traffic
4. Once node 1 is running the new version, add it back to rotation
5. Remove node 2 from rotation
6. Upgrade Bitbucket on node 2
7. Restart node 2 and add back to rotation

---

## Performance and scaling

### Cloud performance

Atlassian manages Cloud performance. For the vast majority of teams, Cloud performance is sufficient. Very large repositories (with large binary files, very long commit histories) may experience slower operations, but this is an edge case.

For performance issues on Cloud, options include:

- Enabling Git LFS for large files
- Shallow clones in CI/CD (`git clone --depth 1`)
- Git maintenance operations (`git gc`, `git pack-refs`)

### Data Center performance

Data Center performance depends on your infrastructure configuration.

**Slow clone/fetch speeds** are usually caused by:

- Insufficient RAM for the JVM (increase the heap: `-Xmx4g` for 4 GB)
- Spinning disk storage (use SSDs for the home directory and database)
- Insufficient CPU for concurrent operations
- Not using Smart Mirrors for geographically distributed teams

**Database performance** - ensure the database has proper indexes and is not resource-starved. PostgreSQL with `pg_stat_statements` enabled helps identify slow queries.

**OpenSearch performance** - give OpenSearch at least 50% of available RAM for the JVM heap, up to 32 GB maximum. SSDs are essential for search performance.

---

## Migrating from Cloud to Data Center

Teams move from Cloud to Data Center when:

- Their organisation's security policy prohibits cloud-hosted code
- Compliance requirements mandate on-premises data control
- They need LDAP/Active Directory integration without the Guard add-on cost
- They need custom plugin capabilities not available in Cloud

### Migration process

1. **Install Data Center** - set up the Data Center infrastructure first
2. **Export from Cloud** - use Bitbucket's migration tool or Git mirror approach
3. **Mirror repositories** - set up mirrors of all Cloud repositories on the Data Center instance
4. **Migrate users** - users need local accounts or LDAP integration on Data Center; Atlassian Cloud accounts do not transfer directly
5. **Migrate settings** - branch permissions, default reviewers, webhooks and integrations must be recreated
6. **Update CI/CD** - pipelines no longer exist on Data Center; external CI must be set up
7. **DNS cutover** - update the remote URLs in all local clones and CI/CD configurations

### What does not migrate automatically

- Pull request history (comments, approvals, status)
- Pipeline run history
- Issue tracking content (if using native Bitbucket issues, which are being sunset anyway)
- Workspace-level settings and billing
- User Atlassian account integration (Data Center uses local or LDAP accounts)

---

## Migrating from Data Center to Cloud

Teams move from Data Center to Cloud when:

- They want to reduce operational burden
- They want access to Bitbucket Pipelines, AI features and Packages
- Their compliance requirements have changed and cloud is now permitted
- Infrastructure maintenance costs exceed Cloud subscription costs

### Migration process

Atlassian provides a **Bitbucket Cloud Migration Assistant** app for Data Center. This app:

1. Analyses your Data Center instance for migration readiness
2. Exports repositories with their Git history
3. Migrates users to Atlassian Cloud accounts (or maps existing accounts)
4. Transfers repository settings (branch permissions, webhooks, access keys)
5. Optionally migrates pull request history

The migration can be run incrementally - repositories can be migrated in batches while Data Center remains the source of truth. Once all repositories are on Cloud, DNS and remote URLs are updated and the Data Center instance is decommissioned.

### Post-migration setup

After migrating to Cloud:

- Set up Bitbucket Pipelines (replace Jenkins or Bamboo)
- Connect to Jira Cloud via the native Atlassian integration
- Set up Atlassian Guard if SSO is required
- Review and update branch permissions (some settings behave differently)

---

## Migrating from Bitbucket Server to Data Center

If your organisation is still running Bitbucket Server (which reached end of life February 2024), this is urgent. You are running unpatched software.

### Server to Data Center migration

The migration path is straightforward because Server and Data Center use the same codebase:

1. **Back up the Server instance** (home directory + database)
2. **Install Data Center** on new hardware with the same version number as your Server instance
3. **Restore the backup** to the new Data Center installation
4. **Upgrade to the current LTS** (10.2 as of 2026) following the upgrade path in Atlassian documentation
5. **Obtain a Data Center licence** (different from Server licence)
6. **Test thoroughly** before decommissioning the Server instance

The migration preserves all repository data, pull request history, user accounts and settings because the underlying data format is identical.

### Server to Cloud migration

Use the same Cloud Migration Assistant approach described above. This is a larger change because you are moving from a self-hosted system to a cloud service - plan accordingly.

---

## The Bitbucket Hybrid Licence

Arriving mid-2026, the **Bitbucket Hybrid Licence** allows organisations to operate both Bitbucket Cloud and Bitbucket Data Center under a single licence agreement.

### What hybrid enables

- Certain repositories can be hosted in the cloud (for teams that benefit from Pipelines, AI features, Packages)
- Other repositories can stay on Data Center (for sensitive code with strict data sovereignty requirements)
- A single commercial agreement covers both deployments
- Billing is simplified: one invoice, one renewal date

### Who hybrid is for

Hybrid is designed for large organisations transitioning from Data Center to Cloud who cannot move everything at once, or organisations with mixed requirements - some code can be in the cloud, some cannot.

All existing Data Center licences are expected to transition to Hybrid licences by March 2029.

---

## When to choose Cloud

Choose Bitbucket Cloud if:

- Your team is small to medium (under 500 users) and you want simplicity
- You want Bitbucket Pipelines without managing separate CI/CD infrastructure
- You want AI features (Rovo Dev, intelligent PR descriptions)
- Your compliance posture is compatible with cloud hosting (most organisations)
- You already use Jira Cloud and want native integration
- You want automatic security patches without managing infrastructure
- Your team does not have Linux server administration expertise
- You are starting a new team or company

---

## When to choose Data Center

Choose Bitbucket Data Center if:

- Your organisation's security policy prohibits cloud-hosted source code
- You need air-gapped network deployment (no internet connectivity)
- You are in a regulated industry with strict data sovereignty requirements (certain government, defence, finance sectors)
- You need LDAP/AD integration without paying for Atlassian Guard
- You need custom plugins for workflow automation or toolchain integration
- You already run Jira Data Center and Confluence Data Center and want a consistent on-premises Atlassian stack
- You have large-scale infrastructure (1,000+ users) where the economics favour self-hosting
- You need Smart Mirroring for geographically distributed teams with performance-sensitive workflows

---

## Try It Yourself

**Exercise 1 - Compare the web interfaces**

1. Open [bitbucket.org](https://bitbucket.org) (Cloud)
2. If you have access to a Data Center instance, open it alongside
3. Compare the repository creation, branch permissions and admin settings
4. Note which features are present in each and which are missing

**Exercise 2 - Explore the Migration Assistant (Data Center users)**

If you manage a Data Center instance:

1. Install the Bitbucket Cloud Migration Assistant app from the Atlassian Marketplace
2. Run the pre-migration analysis (no data is moved at this stage)
3. Review the migration readiness report to understand what would and would not transfer

**Exercise 3 - Calculate total cost of ownership**

For your actual team size, calculate:

- Cloud Premium cost: users × $7.25/month × 12
- Cloud Guard Standard cost (if SSO needed): users × $4.20/month × 12
- Data Center licence cost (from current Atlassian pricing)
- Estimated infrastructure cost for Data Center (2 app nodes + DB + storage)
- Estimated operations time cost for Data Center (0.25 FTE at your engineering salary)

Which is cheaper? Which has lower operational risk?

---

## Common Mistakes

**Staying on Bitbucket Server**

Bitbucket Server reached end of life in February 2024. There are no more security patches. If you are still running it, you are operating with unpatched vulnerabilities. Migrate to Data Center or Cloud urgently.

**Underestimating Data Center operational cost**

The licence fee is visible; the operational cost is not. Data Center requires server maintenance, database administration, backup management, upgrade testing, monitoring and on-call coverage. These costs are real even if they are absorbed into existing infrastructure team budgets rather than appearing as separate line items.

**Assuming Data Center is always cheaper than Cloud**

For teams under 200-300 users, Cloud is often cheaper once infrastructure and operations time are included. Run the total cost of ownership calculation with honest numbers before assuming self-hosting saves money.

**Not planning for CI/CD when migrating to Data Center**

Teams migrating from Cloud lose Bitbucket Pipelines. Many organisations underestimate the work involved in replacing Pipelines with Jenkins or Bamboo. Plan the CI/CD migration as a major project in its own right, not an afterthought.

**Migrating without backing up first**

Before any migration - Cloud to DC, DC to Cloud, Server to DC - take a complete backup. Full home directory backup and database dump. Test that you can restore from the backup before starting the migration. A failed migration with no backup is a catastrophic data loss event.

**Not testing the upgrade path for Data Center**

Data Center upgrades must follow Atlassian's supported upgrade paths. You cannot jump from version 7.0 to 10.2 directly; you must pass through specific intermediate versions. Plan upgrades carefully and test in a staging environment first.

---

## Summary

Bitbucket Cloud and Bitbucket Data Center serve the same fundamental purpose but in different operational models. Cloud is managed by Atlassian - you pay a subscription and use the service. Data Center is managed by you - you install it, maintain it, upgrade it and are responsible for its availability.

Bitbucket Server reached end of life in February 2024. All self-hosted deployments must run Data Center.

The current Data Center LTS is 10.2 (March 2026, supported until March 2028). It runs on Linux only, requires OpenSearch for search and supports active-active clustering for high availability.

Cloud offers features Data Center does not: Bitbucket Pipelines, AI features (Rovo Dev), Bitbucket Packages and secret scanning. Data Center offers features Cloud does not: built-in LDAP/SAML without Guard, complete data sovereignty, custom plugins and Smart Mirroring.

Choose Cloud for simplicity, Pipelines access and lower operational burden. Choose Data Center for data sovereignty, air-gapped deployments, compliance environments that prohibit cloud hosting and very large teams where self-hosting economics favour on-premises.

The Bitbucket Hybrid Licence (arriving mid-2026) will allow organisations to operate both Cloud and Data Center under a single agreement, supporting gradual migrations and mixed-posture environments.

---

## Sources

- [Atlassian: Bitbucket pricing and plans](https://www.atlassian.com/software/bitbucket/pricing)
- [Atlassian: Bitbucket Data Center 10.2 release notes](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-10-2-release-notes-1738146526.html)
- [Atlassian: Bitbucket Data Center system requirements](https://confluence.atlassian.com/bitbucketserver/supported-platforms-776640981.html)
- [Atlassian: Bitbucket Data Center installation guide](https://confluence.atlassian.com/bitbucketserver/bitbucket-data-center-documentation-home-776640954.html)
- [Atlassian: Bitbucket Cloud Migration Assistant](https://confluence.atlassian.com/bitbucketserver/bitbucket-cloud-migration-assistant-1262242535.html)
- [Atlassian: End of support for Bitbucket Server](https://confluence.atlassian.com/bitbucketserver/end-of-support-announcements-776640855.html)
- [Atlassian: Bitbucket Hybrid Licence announcement](https://community.atlassian.com/forums/Data-Center-articles/Announcing-New-Bitbucket-Data-Center-Long-Term-Support-LTS/ba-p/3200774)
- [Atlassian: Data residency for Bitbucket Cloud](https://www.atlassian.com/software/bitbucket/data-residency)
- [Atlassian Trust Center](https://www.atlassian.com/trust)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

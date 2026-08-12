# Self-Hosting GitLab

**Difficulty:** 🔴 Advanced | **Time:** 50 minutes

Self-hosting GitLab means running the entire platform on infrastructure you control: your own servers, your own cloud account, your own data centre. This is one of GitLab's most significant advantages over GitHub - GitLab's Community Edition is free, open source under the MIT licence and has no user limits, no storage caps and no feature paywalls beyond the paid Enterprise Edition tiers. GitHub's equivalent self-hosted option costs $21 per user per month.

Organisations choose to self-host for several reasons: data sovereignty requirements that prohibit sending source code to external clouds, air-gapped environments with no internet access, regulated industries like finance, healthcare and defence that must keep code on-premises and large teams where the cost savings over GitLab.com are substantial.

This file covers everything you need to plan, install, configure, maintain and upgrade a self-managed GitLab instance: editions and licences, hardware requirements, all installation methods, initial configuration, backup and restore, upgrades, runner installation and the key differences between self-managed GitLab and GitLab.com.

---

## Table of Contents

- [Community Edition vs Enterprise Edition](#community-edition-vs-enterprise-edition)
- [Why self-host?](#why-self-host)
- [System requirements](#system-requirements)
- [Reference architectures](#reference-architectures)
- [Installation methods](#installation-methods)
- [Linux package installation (Omnibus)](#linux-package-installation-omnibus)
- [Docker installation](#docker-installation)
- [Kubernetes Helm chart installation](#kubernetes-helm-chart-installation)
- [Initial configuration](#initial-configuration)
- [Configuring email](#configuring-email)
- [Configuring HTTPS with Let's Encrypt](#configuring-https-with-lets-encrypt)
- [Configuring LDAP and SAML](#configuring-ldap-and-saml)
- [Backup and restore](#backup-and-restore)
- [Upgrades](#upgrades)
- [Installing GitLab Runner on self-managed](#installing-gitlab-runner-on-self-managed)
- [GitLab Pages on self-managed](#gitlab-pages-on-self-managed)
- [Container Registry on self-managed](#container-registry-on-self-managed)
- [Monitoring your instance](#monitoring-your-instance)
- [Self-managed vs GitLab.com differences](#self-managed-vs-gitlabcom-differences)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Community Edition vs Enterprise Edition

GitLab is distributed in two editions. Understanding the difference is important before you install.

### Community Edition (CE)

- Licence: **MIT** - fully open source, free forever
- Source code: publicly available at `gitlab.com/gitlab-org/gitlab-foss`
- Features: everything that GitLab considers "core" - Git hosting, merge requests, CI/CD, issues, boards, wikis, container registry, package registry, GitLab Pages, basic security scanning
- User limits: none
- Storage limits: none (limited only by your hardware)
- Cost: $0

### Enterprise Edition (EE)

- Licence: proprietary (source-available but not open source)
- Source code: publicly viewable at `gitlab.com/gitlab-org/gitlab` but not freely reusable
- Features: all CE features plus Premium and Ultimate features when a paid licence is activated
- Without a licence: **EE behaves identically to CE** - no paid features are active, no expiry, no restrictions
- With a Premium licence: merge request approvals, epics, roadmaps, SAML SSO, iterations and more activate
- With an Ultimate licence: Advanced SAST, DAST, fuzz testing, vulnerability management, security policies and more activate

### Which should you install?

**Always install EE, even if you plan to stay free.**

The reason is straightforward: if you start with CE and later purchase a Premium or Ultimate licence, you need to migrate from CE to EE. This involves re-installing GitLab and migrating your data - a significant operational burden. Starting with EE and running it licence-free gives you CE-equivalent functionality today with the ability to activate paid features tomorrow without reinstalling.

EE and CE are functionally identical without a licence. The only difference is the package name (`gitlab-ee` vs `gitlab-ce`) and the binary.

---

## Why Self-Host?

Teams self-host GitLab for distinct reasons. Understanding your reason shapes your architecture decisions.

### Data sovereignty and compliance

Many regulated industries prohibit storing source code on third-party cloud infrastructure. Banks operating under DORA (Digital Operational Resilience Act), healthcare organisations under HIPAA, defence contractors under ITAR and government agencies under FedRAMP often cannot use GitLab.com. Self-managed GitLab keeps all data within your own infrastructure under your own control.

### Air-gapped environments

Some environments have no internet connectivity at all - classified government networks, nuclear facility control systems, certain financial trading systems. GitLab can be installed and operated with zero internet access. All dependencies (packages, Docker images, runner binaries) can be mirrored internally.

### Cost at scale

For large organisations, the per-seat cost of GitLab.com adds up quickly. A 500-person engineering team on GitLab Premium (GitLab.com) costs $174,000 per year. Self-managed GitLab CE is $0. Even with infrastructure costs (servers, storage, operational overhead), self-hosting is often significantly cheaper at scale.

### Control and customisation

Self-managed GitLab gives you control over: upgrade timing (you decide when to update), resource allocation (how much CPU and RAM pipelines get), network configuration, storage backends, integration with internal systems that are not reachable from GitLab.com and instance-level settings that do not exist on GitLab.com (such as configuring default project visibility or disabling specific features instance-wide).

---

## System Requirements

GitLab's resource requirements depend heavily on the number of users and the volume of CI/CD activity.

### Minimum requirements for production use

**For up to 20 users / low CI/CD usage:**

| Resource | Minimum | Recommended |
|---|---|---|
| CPU | 4 vCPU | 4 vCPU |
| RAM | 4 GB | 8 GB |
| Storage | 10 GB (OS + GitLab) + repo storage | 50 GB+ |
| PostgreSQL | 12.x or later | Latest supported |
| Redis | 6.x or 7.x | Latest supported |

> [!IMPORTANT]
> 4 GB RAM is genuinely the minimum and GitLab will run slowly at this level. 8 GB is the practical minimum for a responsive instance. For any team larger than a handful of people, start at 8 GB.

**For up to 1,000 users / moderate CI/CD usage (GitLab's official 1K reference architecture):**

| Resource | Requirement |
|---|---|
| CPU | 8 vCPU |
| RAM | 16 GB |
| Storage | 100 GB SSD (OS + GitLab) + repository storage |
| PostgreSQL | Separate server recommended |
| Redis | Bundled or separate |

### Storage planning

Storage requirements depend on:
- Repository sizes (source code, commit history)
- LFS (Large File Storage) usage
- CI/CD artifact retention
- Container registry image sizes
- Package registry usage

A rough starting point: allocate at least 2x your current total repository size, plus 50 GB for OS and GitLab itself, plus additional space for artifacts and registry. Add storage liberally - running out of disk space on a production GitLab instance causes cascading failures.

**Storage types:**
- Operating system and GitLab application: SSD strongly recommended
- Repository data (`/var/opt/gitlab/git-data/`): SSD preferred, high IOPS important for performance
- Artifacts and uploads: can use slower HDD if SSD is expensive at scale
- Container registry: typically the largest and fastest-growing storage consumer

### Supported operating systems

| OS | Supported versions |
|---|---|
| Ubuntu | 20.04 LTS, 22.04 LTS, 24.04 LTS |
| Debian | 11, 12 |
| Red Hat Enterprise Linux (RHEL) | 8, 9 |
| CentOS | 8 (Stream) |
| AlmaLinux | 8, 9 |
| Rocky Linux | 8, 9 |
| Amazon Linux | 2, 2023 |
| SUSE Linux Enterprise Server | 15.5, 15.6 |
| openSUSE Leap | 15.5, 15.6 |

Architectures: `amd64` (x86-64) and `arm64` (AArch64).

> [!NOTE]
> macOS and Windows are not supported as GitLab server platforms. GitLab itself runs only on Linux. Client machines (developers) can use any OS.

---

## Reference Architectures

GitLab publishes tested reference architectures for different scales. These are the production-validated configurations GitLab supports.

| Architecture | Users | Requests/sec | Components |
|---|---|---|---|
| 1K | Up to 1,000 | 20 RPS | Single server (all components on one machine) |
| 2K | Up to 2,000 | 40 RPS | Single server or split with external Postgres/Redis |
| 3K | Up to 3,000 | 60 RPS | Multiple servers with load balancer |
| 5K | Up to 5,000 | 100 RPS | Distributed components |
| 10K | Up to 10,000 | 200 RPS | Fully distributed with HA |
| 25K | Up to 25,000 | 500 RPS | Large distributed with HA |
| 50K | Up to 50,000 | 1,000 RPS | Largest distributed with HA |

**High availability** starts at the 3K reference architecture. Below 3K, GitLab runs on a single server with no redundancy - a server failure takes the instance down until it is restarted or the server is replaced.

For most self-hosting organisations (teams under 500 people), the 1K or 2K architecture on a single server is the right starting point. Add HA later if the instance becomes critical infrastructure that cannot tolerate any downtime.

### Cloud Native Hybrid

For teams already running Kubernetes, GitLab offers a Cloud Native Hybrid architecture: stateless components (web, API, Sidekiq workers) run in Kubernetes pods, while stateful components (Gitaly for Git storage, PostgreSQL, Redis) run on VMs. This gives Kubernetes scalability for the stateless parts without the complexity of stateful workloads in Kubernetes.

---

## Installation Methods

GitLab supports four primary installation methods. Choose based on your infrastructure preferences.

| Method | Best for | Complexity |
|---|---|---|
| Linux package (Omnibus) | Most teams, traditional server infrastructure | Low |
| Docker | Teams comfortable with containers, single server | Low-Medium |
| Kubernetes Helm chart | Cloud-native teams, large scale | High |
| Cloud Native Hybrid | Large teams already on Kubernetes | High |

---

## Linux Package Installation (Omnibus)

The Linux package (historically called Omnibus) is the most common and most supported installation method. It bundles GitLab's application code, all its dependencies (PostgreSQL, Redis, NGINX, Puma, Sidekiq, Gitaly) and configuration tooling into a single package. One command installs everything.

### 🐧 Ubuntu / Debian

```bash
# Step 1: Install dependencies
sudo apt-get update
sudo apt-get install -y curl openssh-server ca-certificates tzdata perl

# Step 2: (Optional) Install Postfix for local mail delivery
# Or configure an external SMTP server later
sudo apt-get install -y postfix

# Step 3: Add GitLab package repository
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.deb.sh | sudo bash

# Step 4: Install GitLab EE, setting the external URL
# Replace https://gitlab.example.com with your actual domain
sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ee

# If you do not have DNS set up yet, use an IP address for initial setup:
# sudo EXTERNAL_URL="http://192.168.1.100" apt-get install gitlab-ee
```

### 🐧 RHEL / AlmaLinux / Rocky Linux

```bash
# Step 1: Install dependencies
sudo dnf install -y curl openssh-server perl postfix

# Step 2: Add GitLab package repository
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash

# Step 3: Install
sudo EXTERNAL_URL="https://gitlab.example.com" dnf install -y gitlab-ee
```

### What happens during installation

The installation:
1. Downloads the GitLab EE package (~1 GB)
2. Installs bundled PostgreSQL, Redis, NGINX and all other components
3. Runs `gitlab-ctl reconfigure` to configure all services based on `/etc/gitlab/gitlab.rb`
4. Starts all GitLab services
5. Creates the initial root password (saved to `/etc/gitlab/initial_root_password` for 24 hours)

### Accessing GitLab after installation

Navigate to `https://gitlab.example.com` (or your IP address if using HTTP). Sign in with:
- Username: `root`
- Password: contents of `/etc/gitlab/initial_root_password`

Change the root password immediately on first login.

### Key management commands

```bash
# Check status of all services
sudo gitlab-ctl status

# Restart all services
sudo gitlab-ctl restart

# Restart a specific service
sudo gitlab-ctl restart puma
sudo gitlab-ctl restart sidekiq

# View logs
sudo gitlab-ctl tail
sudo gitlab-ctl tail nginx
sudo gitlab-ctl tail postgresql

# Apply configuration changes
sudo gitlab-ctl reconfigure

# Stop all services
sudo gitlab-ctl stop

# Start all services
sudo gitlab-ctl start
```

---

## Docker Installation

Running GitLab in Docker is useful for local testing, small teams or infrastructure managed through Docker Compose.

### Docker Compose setup (recommended)

Create a `docker-compose.yml`:

```yaml
version: '3.8'

services:
  gitlab:
    image: gitlab/gitlab-ee:latest
    container_name: gitlab
    restart: always
    hostname: gitlab.example.com
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'https://gitlab.example.com'
        gitlab_rails['gitlab_shell_ssh_port'] = 2222
        # Add other configuration here - see Initial Configuration section
    ports:
      - '80:80'
      - '443:443'
      - '2222:22'    # SSH on a non-standard port to avoid conflict with host SSH
    volumes:
      - './gitlab/config:/etc/gitlab'
      - './gitlab/logs:/var/log/gitlab'
      - './gitlab/data:/var/opt/gitlab'
    shm_size: '256m'
```

Start GitLab:

```bash
docker compose up -d

# Watch the startup logs
docker compose logs -f gitlab

# GitLab takes 2-5 minutes to start fully
# Check when it is ready:
docker exec -it gitlab gitlab-ctl status
```

### Getting the initial root password from Docker

```bash
docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

### Updating GitLab in Docker

```bash
# Pull the latest image
docker compose pull

# Recreate the container with the new image
docker compose up -d

# GitLab runs a reconfigure and any database migrations on startup
```

> [!IMPORTANT]
> Always back up before updating, even in Docker. The volumes contain your data - they persist across container restarts and recreations. But database migrations cannot always be undone.

### Pinning to a specific version

For production, always pin to a specific version rather than using `latest`:

```yaml
image: gitlab/gitlab-ee:17.10.2-ee.0
```

Find available versions at `hub.docker.com/r/gitlab/gitlab-ee/tags`.

---

## Kubernetes Helm Chart Installation

For large-scale production deployments on Kubernetes. This is the most complex installation method but enables horizontal scaling, rolling updates and Kubernetes-native operations.

### Prerequisites

- Kubernetes 1.27 or later
- Helm 3.3 or later
- An ingress controller (NGINX ingress recommended)
- A PostgreSQL instance (external, not in Kubernetes - GitLab strongly recommends this)
- A Redis instance (external recommended for production)
- Persistent storage provisioner
- A domain with DNS wildcard (`*.gitlab.example.com`)

### Basic installation

```bash
# Add the GitLab Helm repository
helm repo add gitlab https://charts.gitlab.io/
helm repo update

# Install GitLab with minimum configuration
helm install gitlab gitlab/gitlab \
  --set global.hosts.domain=example.com \
  --set global.ingress.configureCertmanager=false \
  --set certmanager-issuer.email=admin@example.com \
  --set global.edition=ee \
  --timeout 600s \
  --namespace gitlab \
  --create-namespace
```

For production, use a `values.yaml` file to configure all options rather than long `--set` chains.

### Getting the initial root password from Kubernetes

```bash
kubectl get secret gitlab-gitlab-initial-root-password \
  -n gitlab \
  -o jsonpath='{.data.password}' | base64 -d
```

### Recommended architecture: Cloud Native Hybrid

Rather than running everything in Kubernetes, run only stateless components there and keep stateful components on VMs:

- **In Kubernetes**: Webservice (Puma), Sidekiq, GitLab Shell, Registry
- **On VMs**: Gitaly (Git repository storage), PostgreSQL, Redis, object storage

This avoids the significant complexity of running stateful workloads (especially Gitaly with its high IOPS requirements) in Kubernetes.

---

## Initial Configuration

All GitLab configuration lives in `/etc/gitlab/gitlab.rb`. Edit this file and run `sudo gitlab-ctl reconfigure` to apply changes.

### Key configuration options

```ruby
# /etc/gitlab/gitlab.rb

# External URL - MUST match what users type in their browser
external_url 'https://gitlab.example.com'

# Time zone
gitlab_rails['time_zone'] = 'Europe/London'

# Default theme (1=Indigo, 2=Dark, 3=Light, etc.)
gitlab_rails['gitlab_default_theme'] = 2

# Disable public sign-ups (recommended for private instances)
gitlab_rails['gitlab_signup_enabled'] = false

# Require admin approval for new sign-ups
gitlab_rails['gitlab_signup_enabled'] = false

# Maximum file size for attachments (MB)
gitlab_rails['max_attachment_size'] = 100

# Default project visibility (public, internal, private)
gitlab_rails['gitlab_default_projects_features_visibility_level'] = 'private'

# CI/CD job artifacts maximum size (MB)
gitlab_rails['artifacts_enabled'] = true
gitlab_rails['artifacts_object_store_enabled'] = false

# Maximum number of CI/CD jobs per runner
gitlab_rails['max_artifact_size_in_mb'] = 100
```

---

## Configuring Email

GitLab sends emails for: account confirmation, password reset, notifications, pipeline alerts and more. Email configuration is essential for a functional instance.

### SMTP configuration (recommended for production)

```ruby
# /etc/gitlab/gitlab.rb

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.gmail.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "your-account@gmail.com"
gitlab_rails['smtp_password'] = "your-app-password"
gitlab_rails['smtp_domain'] = "gmail.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = false

gitlab_rails['gitlab_email_from'] = 'gitlab@example.com'
gitlab_rails['gitlab_email_reply_to'] = 'noreply@example.com'
```

Common SMTP providers and their settings:

**SendGrid:**
```ruby
gitlab_rails['smtp_address'] = "smtp.sendgrid.net"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "apikey"
gitlab_rails['smtp_password'] = "YOUR_SENDGRID_API_KEY"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
```

**Amazon SES:**
```ruby
gitlab_rails['smtp_address'] = "email-smtp.eu-west-1.amazonaws.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "YOUR_SES_ACCESS_KEY"
gitlab_rails['smtp_password'] = "YOUR_SES_SECRET_KEY"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
```

### Testing email configuration

```bash
# After reconfiguring, test by sending a test email
sudo gitlab-rails console
# In the console:
Notify.test_email('recipient@example.com', 'Test subject', 'Test body').deliver_now
```

---

## Configuring HTTPS with Let's Encrypt

GitLab's Omnibus package includes built-in Let's Encrypt support for automatic HTTPS certificate provisioning and renewal.

```ruby
# /etc/gitlab/gitlab.rb

external_url 'https://gitlab.example.com'

# Enable Let's Encrypt
letsencrypt['enable'] = true
letsencrypt['contact_emails'] = ['admin@example.com']

# Auto-renew certificates (recommended)
letsencrypt['auto_renew'] = true
letsencrypt['auto_renew_hour'] = "12"
letsencrypt['auto_renew_minute'] = "30"
letsencrypt['auto_renew_day_of_month'] = "*/7"  # every 7 days
```

**Requirements for Let's Encrypt:**
- Port 80 must be accessible from the internet (Let's Encrypt uses HTTP-01 challenge)
- DNS must point to your server's IP address
- The domain must be a real public domain (not `localhost` or a private IP)

### Manual SSL certificate

If you have your own certificate (corporate CA, wildcard certificate):

```ruby
external_url 'https://gitlab.example.com'

# Disable Let's Encrypt
letsencrypt['enable'] = false

# GitLab looks for certificates in /etc/gitlab/ssl/
# Files must be named: gitlab.example.com.crt and gitlab.example.com.key
nginx['ssl_certificate'] = "/etc/gitlab/ssl/gitlab.example.com.crt"
nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/gitlab.example.com.key"
```

Copy your certificate files to `/etc/gitlab/ssl/` and run `sudo gitlab-ctl reconfigure`.

---

## Configuring LDAP and SAML

### LDAP (Active Directory, OpenLDAP)

```ruby
# /etc/gitlab/gitlab.rb

gitlab_rails['ldap_enabled'] = true
gitlab_rails['prevent_ldap_sign_in'] = false

gitlab_rails['ldap_servers'] = {
  'main' => {
    'label' => 'Corporate LDAP',
    'host' => 'ldap.example.com',
    'port' => 389,
    'uid' => 'sAMAccountName',      # or 'uid' for OpenLDAP
    'bind_dn' => 'CN=GitLab,OU=ServiceAccounts,DC=example,DC=com',
    'password' => 'service-account-password',
    'encryption' => 'start_tls',    # or 'simple_tls' for LDAPS
    'verify_certificates' => true,
    'active_directory' => true,     # false for OpenLDAP
    'allow_username_or_email_login' => false,
    'lowercase_usernames' => false,
    'base' => 'OU=Users,DC=example,DC=com',
    'user_filter' => '(memberOf=CN=GitLabUsers,OU=Groups,DC=example,DC=com)',
    'attributes' => {
      'username' => ['uid', 'userid', 'sAMAccountName'],
      'email' => ['mail', 'email', 'userPrincipalName'],
      'name' => 'cn',
      'first_name' => 'givenName',
      'last_name' => 'sn'
    }
  }
}
```

### SAML (Okta, Azure AD, Google Workspace)

SAML on self-managed is configured at the instance level (unlike GitLab.com where it is per-group):

```ruby
# /etc/gitlab/gitlab.rb

gitlab_rails['omniauth_enabled'] = true
gitlab_rails['omniauth_allow_single_sign_on'] = ['saml']
gitlab_rails['omniauth_block_auto_created_users'] = false

gitlab_rails['omniauth_providers'] = [
  {
    name: 'saml',
    label: 'Company SSO',
    args: {
      assertion_consumer_service_url: 'https://gitlab.example.com/users/auth/saml/callback',
      idp_cert_fingerprint: 'YOUR_IDP_CERTIFICATE_FINGERPRINT',
      idp_sso_target_url: 'https://your-idp.example.com/sso/saml',
      issuer: 'https://gitlab.example.com',
      name_identifier_format: 'urn:oasis:names:tc:SAML:2.0:nameid-format:persistent',
      attribute_statements: {
        email: ['email', 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress'],
        first_name: ['first_name'],
        last_name: ['last_name'],
        name: ['name']
      }
    }
  }
]
```

---

## Backup and Restore

Backup is not optional for a production GitLab instance. Data loss from an unbackedup instance is permanent and catastrophic.

### What the backup includes

GitLab's built-in backup tool creates a compressed archive containing:
- Database (all projects, issues, MRs, users, groups, CI/CD configurations)
- Repository data (all Git repositories, wikis)
- Uploads (issue attachments, avatars, personal files)
- LFS objects
- CI/CD job artifacts
- Pages content
- Registry data (if enabled)
- Snippets

### What the backup does NOT include

These must be backed up separately:
- `/etc/gitlab/gitlab.rb` (your configuration file)
- `/etc/gitlab/gitlab-secrets.json` (encryption keys - **CRITICAL**)
- SSL certificates (`/etc/gitlab/ssl/`)
- Any custom hooks

> [!CAUTION]
> The `gitlab-secrets.json` file contains the encryption keys for two-factor authentication tokens, CI/CD variables and other sensitive data. If you restore from a backup without this file, all 2FA tokens are invalidated, CI/CD variables are unreadable and users must re-enrol in 2FA. Back this file up separately and securely.

### Creating a backup

```bash
# Create a backup
sudo gitlab-backup create

# Create a backup and skip specific components
sudo gitlab-backup create SKIP=artifacts,registry

# Create a backup with a custom strategy (useful for large instances)
sudo gitlab-backup create STRATEGY=copy

# The backup is saved to /var/opt/gitlab/backups/
# Filename format: EPOCH_YYYY_MM_DD_VERSION_gitlab_backup.tar
# Example: 1744200000_2026_04_09_18.10.0_gitlab_backup.tar
```

### Automated backup with cron

```bash
# Edit root's crontab
sudo crontab -e

# Add: run backup at 2:00 AM every day
0 2 * * * /opt/gitlab/bin/gitlab-backup create CRON=1 2>&1 | /usr/bin/logger -t gitlab-backup
```

The `CRON=1` flag suppresses non-error output, preventing empty emails from cron.

### Uploading backups to object storage

Configure GitLab to automatically upload backups to S3, Google Cloud Storage or Azure Blob:

```ruby
# /etc/gitlab/gitlab.rb

gitlab_rails['backup_upload_connection'] = {
  'provider' => 'AWS',
  'region' => 'eu-west-1',
  'aws_access_key_id' => 'YOUR_ACCESS_KEY',
  'aws_secret_access_key' => 'YOUR_SECRET_KEY'
}
gitlab_rails['backup_upload_remote_directory'] = 'my-gitlab-backups-bucket'
gitlab_rails['backup_keep_time'] = 604800  # keep 7 days of backups (in seconds)
```

### Restoring from backup

> [!WARNING]
> Restoration overwrites all existing data. Always restore to the same GitLab version that created the backup. Cross-version restoration is not supported.

```bash
# Step 1: Stop services that write to the database
sudo gitlab-ctl stop puma
sudo gitlab-ctl stop sidekiq

# Verify they are stopped
sudo gitlab-ctl status

# Step 2: Restore the backup
# Replace BACKUP_TIMESTAMP with the timestamp from your backup filename
sudo gitlab-backup restore BACKUP=BACKUP_TIMESTAMP

# Step 3: Restart GitLab
sudo gitlab-ctl start

# Step 4: Run reconfigure to ensure everything is consistent
sudo gitlab-ctl reconfigure

# Step 5: Check GitLab is working
sudo gitlab-rake gitlab:check SANITIZE=true
```

---

## Upgrades

GitLab releases a new version on the third Thursday of every month. Staying current is important for security patches.

### Upgrade paths

GitLab has strict upgrade path requirements. You cannot skip major versions and sometimes cannot skip certain minor versions. Always check the official upgrade path tool before upgrading.

**General rules:**
- You must upgrade through each major version sequentially (16.x -> 17.0 -> 17.x, not 16.x -> 17.x directly if there are mandatory stops)
- Some minor versions are designated "required stops" - you must upgrade to them before continuing
- Check `docs.gitlab.com/update/upgrade_paths/` for the current required upgrade path

### Upgrading the Linux package

```bash
# Check current version
sudo gitlab-rake gitlab:env:info | grep "GitLab version"

# Or check the package version
gitlab-ctl --version

# Update package lists
sudo apt-get update   # Ubuntu/Debian
# or
sudo dnf check-update  # RHEL/AlmaLinux

# Upgrade to the latest version
sudo apt-get install gitlab-ee   # Ubuntu/Debian
# or
sudo dnf update gitlab-ee        # RHEL/AlmaLinux

# GitLab automatically runs reconfigure and database migrations during upgrade
```

### Upgrading to a specific version

```bash
# Ubuntu/Debian - install specific version
sudo apt-get install gitlab-ee=17.10.2-ee.0

# RHEL/AlmaLinux
sudo dnf install gitlab-ee-17.10.2
```

### Before upgrading

1. **Take a backup** - always, no exceptions
2. **Check the upgrade path** - verify no required stops between current and target
3. **Read the release notes** - check for any breaking changes or manual steps
4. **Check background migrations** - GitLab runs database migrations in the background after upgrades. Do not upgrade again until previous background migrations complete:

```bash
# Check for pending background migrations
sudo gitlab-rails runner -e production 'puts Gitlab::BackgroundMigration.remaining'
# or in GitLab 16.x+:
sudo gitlab-rails runner "puts BatchedMigration.where(status: [:active, :paused]).count"
```

### Zero-downtime upgrades

For HA setups, GitLab supports zero-downtime upgrades. Upgrade one component at a time:
1. Upgrade all non-database nodes (web, Sidekiq) to the new version
2. Run database migrations
3. Upgrade remaining nodes

This is only possible on HA architectures. Single-server instances have brief downtime during upgrades (typically 2-5 minutes for migrations).

---

## Installing GitLab Runner on Self-Managed

Your self-managed GitLab instance can use:
- GitLab.com's shared runners (if your instance has outbound internet access - unusual for air-gapped setups)
- Your own runners registered to your instance

Installing runners on your self-managed instance follows the same process as registering runners to GitLab.com, with your instance URL replacing `https://gitlab.com`.

### Install the runner

```bash
# Linux (Ubuntu/Debian)
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
sudo apt-get install gitlab-runner

# macOS
brew install gitlab-runner
sudo brew services start gitlab-runner

# Windows (PowerShell)
New-Item -ItemType Directory -Force -Path "C:\GitLab-Runner"
Invoke-WebRequest -Uri "https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe" -OutFile "C:\GitLab-Runner\gitlab-runner.exe"
```

### Register the runner to your instance

1. In your self-managed GitLab instance: Admin Area -> CI/CD -> Runners -> New instance runner (for instance-wide runners) or Settings -> CI/CD -> Runners -> New project runner (for project-specific)
2. Copy the authentication token

```bash
gitlab-runner register \
  --url "https://gitlab.example.com/" \
  --token "YOUR_RUNNER_AUTHENTICATION_TOKEN" \
  --executor "docker" \
  --docker-image "alpine:latest" \
  --description "Production Docker runner" \
  --tag-list "docker,linux" \
  --run-untagged true
```

### Instance runners vs group runners vs project runners

On self-managed GitLab, administrators can register **instance runners** (available to all projects on the instance). This is different from GitLab.com where the instance runners are managed by GitLab itself.

```ruby
# /etc/gitlab/gitlab.rb
# Control whether instance runners are available by default
gitlab_rails['shared_runners_enabled'] = true
```

---

## GitLab Pages on Self-Managed

GitLab Pages must be explicitly enabled and configured on self-managed instances.

```ruby
# /etc/gitlab/gitlab.rb

pages_external_url "https://pages.example.com"
gitlab_pages['enable'] = true
gitlab_pages['access_control'] = true   # enable Pages access control feature

# Wildcard DNS must resolve *.pages.example.com to your GitLab server
# Each project's Pages site gets: projectname.pages.example.com
```

Run `sudo gitlab-ctl reconfigure` after making changes.

**DNS requirement**: a wildcard DNS record pointing `*.pages.example.com` to your GitLab server IP. Without this, each project's Pages URL will not resolve.

---

## Container Registry on Self-Managed

The container registry is bundled with GitLab EE and enabled by default when GitLab is configured with HTTPS.

```ruby
# /etc/gitlab/gitlab.rb

# Registry on the same domain with a different port
registry_external_url 'https://gitlab.example.com:5050'

# Or registry on its own subdomain (requires separate DNS record)
registry_external_url 'https://registry.example.com'
gitlab_rails['registry_enabled'] = true
```

For production, configure the registry to store images in object storage (S3, GCS, Azure) rather than local disk:

```ruby
registry['storage'] = {
  's3' => {
    'accesskey' => 'YOUR_ACCESS_KEY',
    'secretkey' => 'YOUR_SECRET_KEY',
    'bucket' => 'gitlab-registry',
    'region' => 'eu-west-1'
  }
}
```

---

## Monitoring Your Instance

### Built-in Prometheus

GitLab includes a bundled Prometheus instance that scrapes metrics from all GitLab components. Access it at `http://gitlab.example.com:9090` (not exposed externally by default).

```ruby
# /etc/gitlab/gitlab.rb

# Enable Prometheus monitoring
prometheus['enable'] = true
prometheus_monitoring['enable'] = true

# Expose metrics endpoint for external Prometheus scraping
gitlab_rails['monitoring_whitelist'] = ['127.0.0.1/8', 'YOUR_PROMETHEUS_SERVER_IP/32']
```

### Grafana dashboards

GitLab includes bundled Grafana with pre-configured dashboards for GitLab metrics. Access at `https://gitlab.example.com/-/grafana`.

```ruby
grafana['enable'] = true
grafana['gitlab_secret'] = 'YOUR_SECRET_HERE'
```

### Health checks

GitLab exposes health check endpoints:

```bash
# Overall health
curl https://gitlab.example.com/-/health

# Liveness check (is the process running?)
curl https://gitlab.example.com/-/liveness

# Readiness check (is GitLab ready to serve requests?)
curl https://gitlab.example.com/-/readiness
```

These are used by load balancers and monitoring systems to verify the instance is healthy.

---

## Self-Managed vs GitLab.com Differences

Several features work differently or do not exist on self-managed compared to GitLab.com.

| Feature | GitLab.com | Self-Managed |
|---|---|---|
| **Internal visibility** | Disabled for new projects (July 2019) | Available - visible to all authenticated instance users |
| **SAML SSO** | Configured per group | Configured at instance level in `gitlab.rb` |
| **LDAP authentication** | Not available | Fully supported |
| **Admin Area** | Does not exist | Full admin area for instance management |
| **Instance runners** | Provided by GitLab | You install and manage your own |
| **Storage limits** | 10 GiB per project (Free) | No limits - constrained only by disk space |
| **Compute minutes** | 400/month (Free), 10K (Premium) | No limits - you manage your own runners |
| **Group access tokens** | Premium+ | Available on any plan |
| **Pages access control** | Requires admin enable | Must be configured in `gitlab.rb` |
| **Identity verification** | Required (risk-based) | Not applicable |
| **Free tier 5-user limit** | Applies to private groups | Does not apply |
| **Top-level group limit** | 3 (post Jan 2026) | No limit |
| **GitLab.com runner macOS** | Available (charges apply) | Must self-provision |
| **Custom instance settings** | Not configurable | Full control via `gitlab.rb` |
| **Upgrade timing** | Managed by GitLab | You control |

---

## Try It Yourself

**Exercise: Run GitLab locally with Docker**

If you want to explore a self-managed GitLab instance without committing to server infrastructure, Docker on your local machine is the fastest path.

```bash
# Pull and run GitLab (takes 5-10 minutes to start fully)
docker run --detach \
  --hostname localhost \
  --publish 443:443 \
  --publish 80:80 \
  --publish 2222:22 \
  --name gitlab \
  --restart always \
  --volume $HOME/gitlab/config:/etc/gitlab \
  --volume $HOME/gitlab/logs:/var/log/gitlab \
  --volume $HOME/gitlab/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:latest

# Wait for GitLab to start
docker exec -it gitlab gitlab-ctl status

# Get the root password
docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

Navigate to `http://localhost` and sign in with username `root` and the password from the command above.

You now have a fully functional GitLab instance running locally. Create projects, set up CI/CD, explore the admin area - everything works identically to a production instance.

> [!NOTE]
> This local instance will be slow compared to a properly provisioned server - your laptop does not have the dedicated resources a real server does. It is for exploration only, not production use.

---

## Common Mistakes

**Not backing up `gitlab-secrets.json` separately.** The backup created by `gitlab-backup create` does not include `gitlab-secrets.json`. This file contains the encryption keys for 2FA tokens, CI/CD variables and other sensitive data. If you restore from backup without this file, all 2FA tokens are invalidated and all CI/CD variables become unreadable. Back it up separately, encrypted, to a different location than your regular backups.

**Installing CE when EE is the right choice.** There is no reason to install CE. EE without a licence is identical to CE. Installing CE means a more complex migration if you ever purchase a licence. Always install EE.

**Not following the upgrade path.** GitLab has specific required upgrade paths. Attempting to upgrade from 15.x directly to 17.x without going through required intermediate versions corrupts the database. Always check `docs.gitlab.com/update/upgrade_paths/` before upgrading.

**Underprovisioning storage.** Running out of disk space on a production GitLab instance causes pipeline failures, commit failures and potential data corruption. Start with more storage than you think you need. Monitor disk usage and add capacity before reaching 80% full.

**Not configuring email.** A GitLab instance without working email cannot send password reset links, confirmation emails or notifications. Many organisations install GitLab, set up user accounts and then discover months later that password resets do not work. Configure SMTP during initial setup.

**Exposing the admin area to the internet.** The admin area (`/admin`) should not be reachable from the public internet. Use NGINX restrictions or firewall rules to limit admin area access to trusted IP ranges or a VPN.

**Not monitoring the instance.** GitLab includes Prometheus and Grafana - enable them from day one. Without monitoring, you will not notice when disk space is running out, when Sidekiq queues are backing up or when database connections are exhausted - until something breaks.

---

## Summary

- **Always install Enterprise Edition (EE)**, even if you plan to stay on the free tier. EE without a licence is identical to CE and EE makes future licence activation seamless
- **Community Edition** is MIT-licensed, free, with no user limits, no storage limits and no feature expiry - an excellent choice for teams that must self-host
- **Minimum production requirements**: 4 vCPU / 8 GB RAM (8 vCPU / 16 GB for 1,000 users). SSD storage strongly recommended
- **Linux package (Omnibus)** is the recommended installation method for most teams: one command installs everything, all components bundled, managed with `gitlab-ctl`
- **Docker** works well for small teams and local exploration. Use Docker Compose and pin to a specific version, not `latest`
- **Kubernetes Helm chart** for cloud-native large-scale deployments. Consider Cloud Native Hybrid (K8s for stateless, VMs for stateful) over fully cloud-native
- **Configuration** lives in `/etc/gitlab/gitlab.rb`. Apply changes with `sudo gitlab-ctl reconfigure`
- **Backup**: run `sudo gitlab-backup create` daily via cron. Back up `gitlab-secrets.json` and `gitlab.rb` separately. Upload backups to object storage
- **Restore**: requires the same GitLab version as the backup. Stop `puma` and `sidekiq` first. Restore the secrets file before restoring the backup
- **Upgrades**: check the required upgrade path before upgrading. Never skip required stops. Back up before every upgrade
- **Runners**: install separately, register to your instance URL (not `gitlab.com`). Instance-level runners are available to all projects
- **Key self-managed advantages over GitLab.com**: no user limits, no storage limits, no compute minute limits (with own runners), Internal visibility level, instance-level LDAP/SAML, full Admin Area, complete control over upgrades and configuration

---

## Sources and Further Reading

- [GitLab installation documentation](https://docs.gitlab.com/install/) - official installation guide for all methods
- [Reference architectures](https://docs.gitlab.com/administration/reference_architectures/) - tested architectures for different scale
- [GitLab upgrade paths](https://docs.gitlab.com/update/upgrade_paths/) - required upgrade sequence
- [Backup and restore documentation](https://docs.gitlab.com/administration/backup_restore/) - complete backup guide
- [GitLab.rb configuration options](https://docs.gitlab.com/omnibus/settings/) - all configuration options
- [LDAP configuration](https://docs.gitlab.com/administration/auth/ldap/) - Active Directory and OpenLDAP setup
- [SAML configuration](https://docs.gitlab.com/integration/saml/) - SAML SSO for self-managed
- [GitLab Runner installation](https://docs.gitlab.com/runner/install/) - installing runners on all platforms
- [Monitoring GitLab](https://docs.gitlab.com/administration/monitoring/) - Prometheus and Grafana configuration
- [GitLab Docker images](https://hub.docker.com/r/gitlab/gitlab-ee) - available Docker image versions

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

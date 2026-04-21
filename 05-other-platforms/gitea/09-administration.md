# Gitea Administration

**Difficulty:** 🔴 Advanced | **Time:** 45 minutes

Administering a Gitea instance means managing users, organisations, repositories and authentication across the entire platform. This file covers the admin panel, user lifecycle management, external authentication (LDAP, OAuth2), instance configuration, email and the management commands available from the command line.

---

## Table of Contents

1. [The admin panel](#the-admin-panel)
2. [User management](#user-management)
3. [Organisation management](#organisation-management)
4. [Repository management (admin)](#repository-management-admin)
5. [Authentication: local, LDAP, OAuth2](#authentication-local-ldap-oauth2)
6. [Email configuration](#email-configuration)
7. [Instance settings via app.ini](#instance-settings-via-appini)
8. [Queue and background tasks](#queue-and-background-tasks)
9. [Maintenance and Gitea CLI commands](#maintenance-and-gitea-cli-commands)
10. [Backup and restore](#backup-and-restore)
11. [Monitoring](#monitoring)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## The admin panel

The admin panel is accessible to users with the Administrator flag on their account. Navigate to it via your avatar > **Site Administration**, or directly at `https://gitea.example.com/-/admin`.

### Admin panel sections

| Section | What it contains |
|---|---|
| **Dashboard** | Instance overview: user count, repo count, recent operations |
| **Users** | Full user list, create/edit/delete users, impersonate users |
| **Organisations** | All organisations on the instance |
| **Repositories** | All repositories, git hooks management, force push controls |
| **Runners** | All registered Gitea Actions runners across the instance |
| **Packages** | All packages across all users and organisations |
| **Emails** | Recent email send log |
| **Configuration** | Live view of current app.ini settings |
| **Git Repositories** | Repository resync and garbage collection tools |
| **Authentication Sources** | LDAP, OAuth2 and other external auth providers |
| **Notifications** | Admin notifications settings |
| **Monitoring** | Queue status, goroutine dump, background task status |

---

## User management

### Creating a user (admin-created)

1. Go to **Site Administration** > **Users** > **Create User**
2. Fill in:
   - **Authentication source**: Local (password-based) or an external provider
   - **Username** and **Email**
   - **Password** (for local accounts)
   - **Administrator**: tick to grant admin access
   - **Allow create organisations**: whether this user can create organisations
   - **Must change password on next login**: forces a password reset at first sign-in
3. Click **Create User**

An invitation email is sent if email is configured.

### Editing a user

1. Go to **Site Administration** > **Users**
2. Find the user and click **Edit**
3. Adjust:
   - Account status (active / suspended)
   - Admin flag
   - Email and username
   - Reset password
   - Two-factor authentication status
   - Maximum repository count

### Suspending a user

Suspension prevents sign-in and API access without deleting the account:
1. Edit the user
2. Uncheck **Is Active**
3. Save

Suspended users cannot log in. Their repositories remain accessible. Re-enable by ticking **Is Active** again.

### Deleting a user

1. Edit the user
2. Scroll to the bottom
3. Click **Delete User**

> [!WARNING]
> Deleting a user is permanent. Their repositories must be transferred or deleted first. Gitea will refuse to delete a user who still owns repositories.

### Impersonating a user

Administrators can temporarily impersonate a user to troubleshoot their experience:
1. Go to **Site Administration** > **Users**
2. Click **Impersonate** next to the user
3. You are now browsing as that user
4. Click **Leave Impersonation** in the top bar to return to your admin account

Impersonation is logged in the admin audit trail.

### Global 2FA enforcement

Since Gitea v1.24, administrators can require all users to set up 2FA:

```ini
[service]
REQUIRE_SIGNIN_2FA = true
```

Users without 2FA are redirected to the setup page on next login and cannot access anything until they complete it.

---

## Organisation management

### Viewing all organisations

**Site Administration** > **Organisations** shows every organisation with member count, repo count and owner information.

### Creating an organisation (admin)

Administrators can create organisations for any user:
1. Go to **Site Administration** > **Organisations** > **Create Organisation**
2. Enter the name and select the owner
3. Click **Create Organisation**

### Managing an organisation

Clicking an organisation in the admin list opens a management view:
- View and add members
- View and transfer repositories
- Delete the organisation

### Transferring organisations

If an organisation owner leaves, transfer ownership:
1. Go to the organisation **Settings** > **Danger Zone**
2. **Transfer Organisation** to a new owner

Or as an admin:
1. Edit the organisation in the admin panel
2. Change the owner field

---

## Repository management (admin)

### Viewing all repositories

**Site Administration** > **Repositories** lists every repository on the instance. Filter by user, search by name and access repository settings.

### Forcing a git fsck

To check repository integrity:
1. Go to **Site Administration** > **Git Repositories**
2. Click **Git Fsck** for a specific repository or run it globally
3. Gitea reports any object corruption

### Repository archives and quotas

Set per-user repository limits in **Site Administration** > **Users** > Edit User > **Maximum Repository Creation Limit** (-1 for unlimited).

Instance-wide defaults:
```ini
[repository]
MAX_CREATION_LIMIT = -1   ; -1 = unlimited, 0 = cannot create, N = limit
```

---

## Authentication: local, LDAP, OAuth2

### Local authentication

The default. Users register with a username, email and password. Passwords are hashed with bcrypt.

Relevant settings:
```ini
[service]
DISABLE_REGISTRATION = false
REQUIRE_SIGNIN_VIEW  = false
MIN_PASSWORD_LENGTH  = 8
PASSWORD_COMPLEXITY  = lower,upper,digit,spec   ; require these character types
```

### LDAP / Active Directory

Connect Gitea to an LDAP server for corporate SSO.

1. Go to **Site Administration** > **Authentication Sources** > **Add Authentication Source**
2. Choose **LDAP (via BindDN)** or **LDAP (simple auth)**
3. Configure:

| Field | Description | Example |
|---|---|---|
| **Name** | Label for this auth source | `Corporate AD` |
| **Host** | LDAP server address | `ldap.example.com` |
| **Port** | LDAP port | `389` (LDAP) or `636` (LDAPS) |
| **Security protocol** | None, LDAPS, or StartTLS | `STARTTLS` |
| **Bind DN** | The account used to search | `cn=gitea-bind,ou=service,dc=example,dc=com` |
| **Bind password** | Password for the bind account | (your bind account password) |
| **User search base** | Where to look for users | `ou=users,dc=example,dc=com` |
| **User filter** | LDAP filter for valid users | `(&(objectClass=person)(memberOf=cn=developers,ou=groups,dc=example,dc=com))` |
| **Username attribute** | Maps to Gitea username | `sAMAccountName` (AD) or `uid` (OpenLDAP) |
| **Firstname attribute** | Maps to first name | `givenName` |
| **Surname attribute** | Maps to last name | `sn` |
| **Email attribute** | Maps to email | `mail` |
| **Admin filter** | Who gets admin | `(memberOf=cn=gitea-admins,ou=groups,dc=example,dc=com)` |

4. Click **Test and add authentication source**

When a user signs in with their LDAP credentials, Gitea creates a local account automatically and marks it as externally authenticated. Password changes must happen in LDAP, not in Gitea.

### Active Directory specifics

For Active Directory, the filter for active users is:
```
(&(objectClass=person)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))
```

The `userAccountControl` check excludes disabled accounts.

For group-based access (only members of a specific AD group can access Gitea):
```
(&(objectClass=person)(memberOf=CN=GiteaUsers,OU=Groups,DC=example,DC=com))
```

### OAuth2 providers

OAuth2 enables sign-in with external accounts: GitHub, GitLab, Google, Microsoft and others.

1. Go to **Site Administration** > **Authentication Sources** > **Add Authentication Source**
2. Choose **OAuth2**
3. Select the provider:

**GitHub:**
- Register a GitHub OAuth App at [github.com/settings/developers](https://github.com/settings/developers)
- Callback URL: `https://gitea.example.com/user/oauth2/github/callback`
- Enter the Client ID and Client Secret

**GitLab:**
- Register at GitLab > User Settings > Applications
- Callback URL: `https://gitea.example.com/user/oauth2/gitlab/callback`

**Microsoft / Azure AD:**
- Register an app in Azure AD
- Callback URL: `https://gitea.example.com/user/oauth2/microsoftonline/callback`
- Tenant ID, Client ID and Client Secret required

**Custom OpenID Connect:**
For any OIDC provider:
1. Choose **OpenID Connect**
2. Enter the provider's discovery URL (e.g. `https://sso.example.com/.well-known/openid-configuration`)
3. Enter Client ID and Client Secret
4. Map user attribute claims

### Automatic account creation

When a user signs in with OAuth2 for the first time, Gitea creates a new account. Configure whether auto-creation is allowed:

```ini
[oauth2]
ENABLE_AUTO_REGISTRATION = true
```

Set to `false` to require administrators to pre-create accounts before users can sign in with OAuth2.

---

## Email configuration

Email enables registration confirmations, password resets, issue notifications and PR review requests. Without email, these notifications are unavailable.

### SMTP configuration

```ini
[mailer]
ENABLED           = true
PROTOCOL          = smtp+starttls    ; smtp, smtp+starttls, smtp+tls, sendmail, dummy
SMTP_ADDR         = smtp.example.com
SMTP_PORT         = 587
USER              = gitea@example.com
PASSWD            = your_smtp_password
FROM              = "Gitea" <gitea@example.com>
```

**Protocol options:**
- `smtp` - plain SMTP (port 25, insecure)
- `smtp+starttls` - SMTP with STARTTLS (port 587, recommended)
- `smtp+tls` - SMTPS (port 465)
- `sendmail` - use system sendmail binary
- `dummy` - log emails but do not send (useful for development)

### Common providers

**Gmail:**
```ini
SMTP_ADDR = smtp.gmail.com
SMTP_PORT = 587
USER      = you@gmail.com
PASSWD    = your-app-password   ; App password, not account password
PROTOCOL  = smtp+starttls
```

**SendGrid:**
```ini
SMTP_ADDR = smtp.sendgrid.net
SMTP_PORT = 587
USER      = apikey
PASSWD    = SG.your_sendgrid_api_key
PROTOCOL  = smtp+starttls
```

**Amazon SES:**
```ini
SMTP_ADDR = email-smtp.us-east-1.amazonaws.com
SMTP_PORT = 587
USER      = your-ses-access-key
PASSWD    = your-ses-secret
PROTOCOL  = smtp+starttls
```

### Testing email configuration

After configuring, send a test email from the admin panel:
1. Go to **Site Administration** > **Dashboard**
2. Under **Email**, click **Send Test Email**
3. Enter a recipient address and click send

Or use the Gitea CLI:
```bash
gitea admin send-mail \
  --to recipient@example.com \
  --subject "Gitea test email" \
  --body "This is a test from your Gitea instance"
```

---

## Instance settings via app.ini

Key settings for a production Gitea instance:

### Registration and access control

```ini
[service]
DISABLE_REGISTRATION              = false   ; set true after initial setup if you want closed signup
REQUIRE_EMAIL_CONFIRMATION        = true    ; require email verification
REGISTER_EMAIL_CONFIRM            = true    ; send confirmation email
REGISTER_MANUAL_CONFIRM           = false   ; require admin approval for new accounts
ALLOW_ONLY_INTERNAL_REGISTRATION  = false   ; only allow registration if not using OAuth
ALLOW_ONLY_EXTERNAL_REGISTRATION  = false   ; only allow OAuth registration
```

### Repository defaults

```ini
[repository]
DEFAULT_PRIVATE         = false   ; new repos are private by default
ENABLE_PUSH_CREATE_ORG  = false   ; allow push-creating org repos
ENABLE_PUSH_CREATE_USER = false   ; allow push-creating user repos
DEFAULT_BRANCH          = main    ; default branch name for new repos
```

### Hooks and integrations

```ini
[webhook]
ALLOWED_HOST_LIST = *      ; restrict webhook destinations (use * for all, or list IPs)
SKIP_TLS_VERIFY   = false  ; set true if webhook target has self-signed cert
```

### Security

```ini
[security]
INSTALL_LOCK     = true             ; prevents re-running the install wizard
MIN_PASSWORD_LENGTH = 12
PASSWORD_COMPLEXITY = lower,upper,digit,spec
DISABLE_GIT_HOOKS = false           ; set true to prevent server-side git hooks
```

---

## Queue and background tasks

Gitea uses an internal queue system for background tasks: email sending, index updates, webhook delivery, repository maintenance and more.

### Viewing queue status

**Site Administration** > **Monitoring** shows:
- Queue lengths and processing rates
- Active background goroutines
- Memory usage
- Database connection pool status

### Resetting a stuck queue

If a queue is stuck (length growing, nothing being processed):
1. Check the Gitea logs for errors
2. Restart the Gitea service: `sudo systemctl restart gitea`
3. If the queue is Redis-backed, check Redis connectivity

### Queue configuration

```ini
[queue]
TYPE             = level        ; level (default), redis, memory
CONN_STR         = redis://localhost:6379/0   ; for Redis
MAX_WORKERS      = 10
QUEUE_LENGTH     = 20
BATCH_LENGTH     = 20
```

For high-volume instances, Redis-backed queues are more reliable than the default in-process queue.

---

## Maintenance and Gitea CLI commands

The `gitea` binary includes administrative commands for maintenance tasks.

### Admin user management

```bash
# Create an admin user
gitea admin user create \
  --username admin \
  --email admin@example.com \
  --password 'SecurePassword123!' \
  --admin

# Change a user's password
gitea admin user change-password \
  --username johndoe \
  --password 'NewPassword456!'

# Generate access token for a user
gitea admin user generate-access-token \
  --username johndoe \
  --token-name "admin-token" \
  --raw
```

### Repository maintenance

```bash
# Run git fsck on all repos
gitea admin git fsck

# Rebuild git hooks for all repos
gitea admin git repos --update-git-hooks

# Resync all repository hooks
gitea admin git push-mirrors --sync
```

### Database maintenance

```bash
# Run database migrations manually
gitea migrate

# Check migration status
gitea doctor check
```

### Doctor command

The `gitea doctor` command checks for common problems:

```bash
# Run all checks
gitea doctor check --all

# Run specific checks
gitea doctor check --run check-db-consistency,check-repos
```

Common checks:
- `check-db-consistency`: verify database integrity
- `check-repos`: verify all repositories have corresponding database entries
- `check-hooks`: verify git hooks are in place for all repos

Fix detected problems:
```bash
gitea doctor check --run check-repos --fix
```

---

## Backup and restore

### What to back up

A complete Gitea backup requires:
1. **Database**: all configuration, user data, issues, PRs - the source of truth for everything except the actual Git data
2. **Repository data**: the bare Git repositories at `$GITEA_WORK_DIR/data/gitea/repositories` (or your configured `[repository] ROOT`)
3. **LFS storage**: LFS objects at `$GITEA_WORK_DIR/data/lfs`
4. **Attachments**: uploaded files at `$GITEA_WORK_DIR/data/attachments`
5. **Avatar storage**: custom avatars at `$GITEA_WORK_DIR/data/avatars`
6. **app.ini**: the configuration file

### Backup command

Gitea has a built-in backup command that bundles everything into a zip archive:

```bash
gitea dump \
  --file /backup/gitea-backup-$(date +%Y%m%d-%H%M%S).zip \
  --skip-log
```

The dump includes the database, repository data, LFS files and configuration. For large instances, the dump may take significant time and disk space.

### Database-only backup (for large instances)

```bash
# PostgreSQL
pg_dump gitea > /backup/gitea-db-$(date +%Y%m%d).sql

# MySQL
mysqldump gitea > /backup/gitea-db-$(date +%Y%m%d).sql

# SQLite
cp /var/lib/gitea/data/gitea.db /backup/gitea-db-$(date +%Y%m%d).db
```

Combine with a file system snapshot or rsync of the repository data directory.

### Restore

```bash
# Restore from a dump zip
gitea restore --from /backup/gitea-backup-20260101-120000.zip

# Or restore components manually:

# 1. Stop Gitea
sudo systemctl stop gitea

# 2. Restore database
psql gitea < /backup/gitea-db-20260101.sql

# 3. Restore repository data
rsync -av /backup/repositories/ /var/lib/gitea/data/gitea/repositories/

# 4. Restore LFS
rsync -av /backup/lfs/ /var/lib/gitea/data/lfs/

# 5. Start Gitea
sudo systemctl start gitea
```

### Automated backup with cron

🐧 **Linux** (`crontab -e`):
```bash
# Backup Gitea every day at 2 AM
0 2 * * * /usr/local/bin/gitea dump --file /backup/gitea-$(date +\%Y\%m\%d).zip --skip-log
# Keep only last 14 days of backups
0 3 * * * find /backup -name "gitea-*.zip" -mtime +14 -delete
```

---

## Monitoring

### Log files

Gitea logs to files at `$GITEA_WORK_DIR/log/` by default. Key log files:

- `gitea.log`: main application log
- `xorm.log`: database query log
- `router.log`: HTTP request log

Configure log levels in `app.ini`:
```ini
[log]
MODE      = file,console   ; log to file and stdout
LEVEL     = Info           ; Trace, Debug, Info, Warn, Error, Critical, Fatal
ROOT_PATH = /var/lib/gitea/log
```

For debugging, temporarily set `LEVEL = Debug`. Do not leave debug logging enabled in production - it generates very large log files.

### Prometheus metrics

Gitea exposes Prometheus metrics at `/metrics`:

```ini
[metrics]
ENABLED        = true
TOKEN          = your-metrics-token   ; optional, protects the endpoint
```

Scrape configuration for Prometheus:
```yaml
scrape_configs:
  - job_name: gitea
    bearer_token: your-metrics-token
    static_configs:
      - targets: ['gitea.example.com']
    scheme: https
    metrics_path: /metrics
```

Key metrics:
- `gitea_repositories_total` - total repository count
- `gitea_users_total` - total user count
- `gitea_issue_by_type` - issues by type
- `go_*` - Go runtime metrics (memory, goroutines, GC)

### Health check endpoint

```bash
curl https://gitea.example.com/-/health
```

Returns `OK` when Gitea is healthy. Use this for load balancer health checks.

---

## Try It Yourself

**Exercise 1 - Explore the admin panel**

If you have a local Gitea instance:
1. Log in as the admin account created during installation
2. Go to avatar > **Site Administration**
3. Explore each section: Users, Repositories, Authentication Sources, Configuration
4. Check the **Monitoring** section to see queue status

**Exercise 2 - Create a test user**

1. Go to **Site Administration** > **Users** > **Create User**
2. Create a user with username `testuser`, a test email and a password
3. Enable **Must change password on next login**
4. Log out and log in as `testuser`
5. Notice the password change prompt
6. Return to admin and delete the test user

**Exercise 3 - Run the doctor command**

```bash
# Check for common issues
gitea doctor check --all

# Review the output for any warnings or errors
```

**Exercise 4 - Test the backup command**

```bash
# Run a backup (adjust paths for your setup)
gitea dump --file /tmp/gitea-test-backup.zip --skip-log

# Check the contents
unzip -l /tmp/gitea-test-backup.zip | head -30
```

---

## Common Mistakes

**Running Gitea as root**

Gitea should run as a dedicated non-root user (typically `git`). Running as root creates unnecessary security risk - a compromise of the Gitea process would immediately have root access. Create a dedicated service account.

**Forgetting to set INSTALL_LOCK = true**

If `INSTALL_LOCK` is not set in app.ini, the installation wizard remains accessible. Anyone who finds your Gitea instance before you set this can run the wizard and potentially overwrite your configuration. Always set `INSTALL_LOCK = true` after initial setup.

**Not configuring email for a multi-user instance**

Without email, users cannot receive password reset links, issue notifications or pull request review requests. New users may not be able to verify their email address. Email is effectively required for any instance with real users.

**Using default SQLite for more than personal use**

SQLite is single-writer. Under concurrent load from multiple users, database lock errors occur. Migrate to PostgreSQL for any team deployment. Gitea's `gitea convert` command helps migrate from SQLite.

**Not setting up regular backups**

Gitea stores everything that matters in the database and the repository file system. Hardware failures, accidental deletions and corruption happen. A weekly cron job that runs `gitea dump` and stores the result off-site is the minimum viable backup strategy.

**Setting WEBHOOK_ALLOWED_HOST_LIST incorrectly for production**

The default `*` allows webhooks to any host, including internal network addresses (SSRF risk). For production instances accessible from the internet, restrict the allowed webhook destinations to known external services.

---

## Summary

Gitea administration covers the full lifecycle of a running instance. The admin panel provides access to user management, organisation management, repository oversight, authentication sources, monitoring and runner management.

User management includes creating, suspending and deleting users, impersonation for troubleshooting and global 2FA enforcement. LDAP integration connects Gitea to corporate Active Directory with group-based access control. OAuth2 enables sign-in with external providers including GitHub, GitLab, Google and any OIDC provider.

Email configuration via SMTP enables notifications and account management. Production instances should use a dedicated SMTP provider (SendGrid, SES, Postmark) rather than Gmail.

The `gitea` CLI provides maintenance commands for user administration, repository integrity checks, database migrations and the `doctor` command for automated issue detection and repair.

Regular backups with `gitea dump` capture the database, repositories, LFS data and configuration. Schedule automated backups with cron and verify restores periodically.

---

## Sources

- [Gitea: Administration documentation](https://docs.gitea.com/administration)
- [Gitea: LDAP authentication](https://docs.gitea.com/administration/authentication#ldap-via-binddn)
- [Gitea: OAuth2 authentication](https://docs.gitea.com/administration/oauth2-provider)
- [Gitea: Email configuration](https://docs.gitea.com/administration/email-setup)
- [Gitea: Backup and restore](https://docs.gitea.com/administration/backup-and-restore)
- [Gitea: Configuration cheat sheet](https://docs.gitea.com/administration/config-cheat-sheet)
- [Gitea: CLI reference](https://docs.gitea.com/administration/command-line)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

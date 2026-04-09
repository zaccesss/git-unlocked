# Forgejo Administration

**Difficulty:** 🔴 Advanced | **Time:** 40 minutes

Forgejo administration is nearly identical to Gitea administration, with one major addition: SAML 2.0 authentication is built into the free edition. This file covers the admin panel, user lifecycle management, LDAP, SAML (Forgejo's free SSO feature), OAuth2, email and the maintenance CLI commands. Administrators familiar with Gitea will find almost everything the same - read the SAML section for what is new.

---

## Table of Contents

1. [The admin panel](#the-admin-panel)
2. [User management](#user-management)
3. [Authentication: local, LDAP, SAML, OAuth2](#authentication-local-ldap-saml-oauth2)
4. [Email configuration](#email-configuration)
5. [Federation administration](#federation-administration)
6. [Instance settings via app.ini](#instance-settings-via-appini)
7. [Maintenance CLI commands](#maintenance-cli-commands)
8. [Backup and restore](#backup-and-restore)
9. [Monitoring](#monitoring)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## The admin panel

Access: avatar > **Site Administration** or directly at `https://forgejo.example.com/-/admin`.

### Admin panel sections

| Section | What it contains |
|---|---|
| **Dashboard** | Instance overview: user count, repo count, recent operations |
| **Users** | Full user list; create, edit, delete, impersonate |
| **Organisations** | All organisations on the instance |
| **Repositories** | All repositories with maintenance tools |
| **Runners** | All registered Forgejo Actions runners |
| **Packages** | All packages across all namespaces |
| **Emails** | Recent email send log |
| **Configuration** | Live view of current `app.ini` values |
| **Authentication Sources** | LDAP, SAML, OAuth2 configuration |
| **Monitoring** | Queue status, background task status |

---

## User management

### Creating a user (admin-created)

1. **Site Administration** > **Users** > **Create User**
2. Set username, email, password
3. Optionally: grant admin privileges, set registration restrictions, require password change on first login
4. Click **Create User**

### Suspending a user

Suspension prevents sign-in without deleting the account:
1. **Users** > find user > **Edit**
2. Uncheck **Is Active**
3. Save

Re-enable by ticking **Is Active** again.

### Deleting a user

A user with owned repositories cannot be deleted until repositories are transferred or deleted. Edit the user, scroll to bottom, click **Delete User**. Deletion is permanent.

### Impersonating a user

Admins can browse as another user for troubleshooting:
1. **Users** > find user > **Impersonate**
2. Click **Leave Impersonation** in the top bar when done

Impersonation is logged in the audit trail.

### Global 2FA enforcement

Require all users to have 2FA before they can use the instance:

```ini
[service]
REQUIRE_SIGNIN_2FA = true
```

Users without 2FA are redirected to the setup page on next login.

---

## Authentication: local, LDAP, SAML, OAuth2

### Local authentication

The default. Users create accounts with username, email and password. Bcrypt hashing.

Configure minimum password requirements:
```ini
[service]
MIN_PASSWORD_LENGTH = 12
PASSWORD_COMPLEXITY = lower,upper,digit,spec
```

### LDAP / Active Directory

Connect Forgejo to an LDAP server for corporate directory authentication.

1. **Site Administration** > **Authentication Sources** > **Add Authentication Source** > **LDAP (via BindDN)**
2. Configure:

| Field | Value | Example |
|---|---|---|
| Name | Label | `Corporate AD` |
| Host | LDAP server | `ldap.example.com` |
| Port | LDAP port | `389` (LDAP) or `636` (LDAPS) |
| Security | Encryption | `STARTTLS` |
| Bind DN | Service account | `cn=forgejo,ou=service,dc=example,dc=com` |
| Bind password | Service account password | |
| User search base | Where to look | `ou=users,dc=example,dc=com` |
| User filter | LDAP filter | `(&(objectClass=person)(sAMAccountName=%s))` |
| Username attribute | Maps to username | `sAMAccountName` |
| Email attribute | Maps to email | `mail` |
| Admin filter | Who gets admin | `(memberOf=cn=forgejo-admins,ou=groups,dc=example,dc=com)` |

3. Click **Test and add authentication source**

For Active Directory, filter for active accounts only:
```
(&(objectClass=person)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))
```

### SAML 2.0 (Forgejo exclusive - free edition)

SAML is Forgejo's most significant authentication feature compared to Gitea community edition. It allows sign-in via any SAML 2.0 identity provider: Okta, Azure AD, Google Workspace, Keycloak, OneLogin, PingFederate and others.

#### Generating the SAML certificate

```bash
openssl req -x509 -newkey rsa:4096 \
  -keyout /etc/forgejo/saml-key.pem \
  -out /etc/forgejo/saml-cert.pem \
  -days 3650 -nodes \
  -subj "/CN=forgejo.example.com"

# Restrict permissions
chmod 600 /etc/forgejo/saml-key.pem
chown git:git /etc/forgejo/saml-key.pem /etc/forgejo/saml-cert.pem
```

#### Configuring SAML in app.ini

```ini
[saml]
ENABLED            = true
SECURITY_KEY_FILE  = /etc/forgejo/saml-key.pem
SECURITY_CERT_FILE = /etc/forgejo/saml-cert.pem

; Option A: fetch metadata from IdP URL (preferred)
IDP_METADATA_URL   = https://your-idp.example.com/saml/metadata

; Option B: use a downloaded metadata XML file
; IDP_METADATA_FILE = /etc/forgejo/idp-metadata.xml

; Attribute mapping (adjust to match your IdP's attribute names)
NAME_ID_FORMAT    = urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress
ATTRIBUTE_NAME    = uid
ATTRIBUTE_EMAIL   = email
ATTRIBUTE_FIRSTNAME = firstName
ATTRIBUTE_LASTNAME  = lastName

; Optional: auto-create accounts on first SAML login
ALLOW_SIGNUP      = true
```

#### Forgejo service provider URLs

Provide these to your identity provider when configuring the SAML app:

| URL | Value |
|---|---|
| **Service Provider Entity ID / Audience URI** | `https://forgejo.example.com/user/saml/metadata` |
| **Assertion Consumer Service URL (ACS)** | `https://forgejo.example.com/user/saml/acs` |
| **SP metadata URL** | `https://forgejo.example.com/user/saml/metadata` |

#### IdP-specific configuration

**Okta:**
1. Admin Console > Applications > Create App Integration > SAML 2.0
2. Single sign on URL: `https://forgejo.example.com/user/saml/acs`
3. Audience URI: `https://forgejo.example.com/user/saml/metadata`
4. Attribute statements: map `email` → `user.email`, `firstName` → `user.firstName`, `lastName` → `user.lastName`
5. Download the IdP metadata XML or copy the metadata URL
6. Set `IDP_METADATA_URL` to the Okta metadata URL

**Azure AD / Entra ID:**
1. Azure Portal > Enterprise Applications > New application > Create your own
2. Set up Single Sign-On > SAML
3. Identifier (Entity ID): `https://forgejo.example.com/user/saml/metadata`
4. Reply URL (ACS URL): `https://forgejo.example.com/user/saml/acs`
5. Attributes: map `email`, `givenname`, `surname` claims
6. Copy the App Federation Metadata URL for `IDP_METADATA_URL`

**Keycloak:**
1. Realm > Clients > Create client > SAML
2. Client ID: `https://forgejo.example.com/user/saml/metadata`
3. Valid Redirect URIs: `https://forgejo.example.com/user/saml/acs`
4. Set the realm's SAML metadata URL for `IDP_METADATA_URL`

**Google Workspace:**
1. Admin Console > Apps > Web and mobile apps > Add app > Add custom SAML app
2. ACS URL: `https://forgejo.example.com/user/saml/acs`
3. Entity ID: `https://forgejo.example.com/user/saml/metadata`
4. Download the IdP metadata XML and use `IDP_METADATA_FILE`

#### Testing SAML safely

> [!WARNING]
> A misconfigured SAML setup can lock all users out if local login is also disabled. Always test in a separate browser session while remaining logged in as admin in another, so you can fix errors without being locked out.

1. Restart Forgejo after `app.ini` changes
2. Open an incognito window and navigate to the Forgejo sign-in page
3. The SAML button appears - click it
4. Complete the IdP flow
5. You should be redirected back to Forgejo and signed in

If the SAML flow fails:
- Check Forgejo logs at `$FORGEJO_WORK_DIR/log/forgejo.log`
- Verify the ACS URL and Entity ID match exactly between `app.ini` and the IdP configuration
- Check that the IdP is sending the correct attribute names

#### SAML and local accounts

By default, SAML users get a new Forgejo account on first login, separate from any existing local account with the same email. To link SAML to an existing local account:
1. Sign in with the local account
2. Avatar > **Settings** > **Security** > **Linked Accounts**
3. Link the SAML provider

### OAuth2 providers

OAuth2 enables sign-in with GitHub, GitLab, Google, Microsoft and custom OIDC providers.

1. **Site Administration** > **Authentication Sources** > **Add Authentication Source** > **OAuth2**
2. Select the provider
3. Register an OAuth2 app on the provider (callback URL: `https://forgejo.example.com/user/oauth2/PROVIDER_NAME/callback`)
4. Enter the Client ID and Client Secret
5. Save

---

## Email configuration

Email is required for registration confirmations, password resets, notifications and PR reviews.

```ini
[mailer]
ENABLED        = true
PROTOCOL       = smtp+starttls
SMTP_ADDR      = smtp.example.com
SMTP_PORT      = 587
USER           = forgejo@example.com
PASSWD         = smtp_password
FROM           = "Forgejo" <forgejo@example.com>
```

Protocol options: `smtp`, `smtp+starttls` (recommended), `smtp+tls`, `sendmail`, `dummy` (log only, no send).

### Test email

After configuring, send a test from the admin dashboard:
- **Site Administration** > **Dashboard** > **Email** > **Send Test Email**

Or via CLI:
```bash
forgejo admin send-mail \
  --to admin@example.com \
  --subject "Forgejo test" \
  --body "Testing email configuration"
```

---

## Federation administration

When federation is enabled, additional administration options appear.

### Enabling federation

```ini
[federation]
ENABLED = true
```

Restart Forgejo.

### Viewing federated follows

From the admin dashboard, view which remote instances have followers on your instance and which remote repositories your users follow.

### Managing federation at scale

For instances with many federated followers, federation activity adds background processing load. Monitor the queue status in **Site Administration** > **Monitoring** after enabling federation.

### Blocking federated instances

If a remote Forgejo instance is sending unwanted federation traffic, block it:

```ini
[federation]
ENABLED = true
BLOCKED_INSTANCES = bad-actor.example.com,spam-forge.example.org
```

---

## Instance settings via app.ini

### Registration and access

```ini
[service]
DISABLE_REGISTRATION              = false   ; set true for closed-signup instances
REQUIRE_EMAIL_CONFIRMATION        = true    ; email verification required
REGISTER_MANUAL_CONFIRM           = false   ; admin approval for new accounts
ALLOW_ONLY_EXTERNAL_REGISTRATION  = false   ; force OAuth2/SAML only, no local
```

### Repository defaults

```ini
[repository]
DEFAULT_PRIVATE         = false   ; new repos are private by default
DEFAULT_BRANCH          = main    ; default branch name for new repos
ENABLE_PUSH_CREATE_USER = false   ; disallow push-creating user repos
```

### Security

```ini
[security]
INSTALL_LOCK        = true              ; prevents re-running setup wizard
MIN_PASSWORD_LENGTH = 12
PASSWORD_COMPLEXITY = lower,upper,digit,spec
```

---

## Maintenance CLI commands

### User administration

```bash
# Create an admin user
forgejo admin user create \
  --username admin \
  --email admin@example.com \
  --password 'SecurePassword!' \
  --admin

# Change a user's password
forgejo admin user change-password \
  --username johndoe \
  --password 'NewPassword!'

# Generate an access token
forgejo admin user generate-access-token \
  --username johndoe \
  --token-name "admin-token" \
  --raw
```

### Repository maintenance

```bash
# Run git fsck on all repos
forgejo admin git fsck

# Rebuild git hooks for all repos
forgejo admin git repos --update-git-hooks
```

### Doctor check

```bash
# Run all health checks
forgejo doctor check --all

# Fix detected issues
forgejo doctor check --run check-repos --fix

# Run database consistency check
forgejo doctor check --run check-db-consistency
```

Common checks: `check-db-consistency`, `check-repos`, `check-hooks`

### Database migration

```bash
# Run pending migrations manually
forgejo migrate

# Check migration status
forgejo doctor check
```

---

## Backup and restore

### What to back up

| Component | Location | Importance |
|---|---|---|
| Database | PostgreSQL / MySQL / SQLite | Critical - all metadata |
| Repositories | `$FORGEJO_WORK_DIR/data/forgejo/repositories/` | Critical - Git data |
| LFS objects | `$FORGEJO_WORK_DIR/data/lfs/` | Important |
| Attachments | `$FORGEJO_WORK_DIR/data/attachments/` | Important |
| Configuration | `/etc/forgejo/app.ini` | Important |
| SAML certificates | `/etc/forgejo/saml-*.pem` | Important |

### Built-in backup command

```bash
forgejo dump \
  --file /backup/forgejo-$(date +%Y%m%d-%H%M%S).zip \
  --skip-log
```

The dump includes the database, repositories, LFS files and configuration. Large instances may take significant time.

### Database-only backup

```bash
# PostgreSQL
pg_dump forgejo > /backup/forgejo-db-$(date +%Y%m%d).sql

# SQLite
cp $FORGEJO_WORK_DIR/data/forgejo.db /backup/forgejo-db-$(date +%Y%m%d).db
```

Combine with an `rsync` of the work directory for a complete backup.

### Restore procedure

```bash
# Stop Forgejo
sudo systemctl stop forgejo

# Restore database
psql forgejo < /backup/forgejo-db-20260101.sql

# Restore work directory
rsync -av /backup/forgejo-data/ /var/lib/forgejo/

# Restore SAML certificates if needed
cp /backup/saml-*.pem /etc/forgejo/

# Start Forgejo
sudo systemctl start forgejo
```

### Automated backup cron

```bash
# Daily backup at 2 AM, keep 14 days
0 2 * * * /usr/local/bin/forgejo dump --file /backup/forgejo-$(date +\%Y\%m\%d).zip --skip-log
0 3 * * * find /backup -name "forgejo-*.zip" -mtime +14 -delete
```

---

## Monitoring

### Log files

```
$FORGEJO_WORK_DIR/log/forgejo.log    # main application log
$FORGEJO_WORK_DIR/log/xorm.log       # database queries
$FORGEJO_WORK_DIR/log/router.log     # HTTP requests
```

Log level configuration:
```ini
[log]
MODE      = file,console
LEVEL     = Info           ; Trace, Debug, Info, Warn, Error
ROOT_PATH = /var/lib/forgejo/log
```

### Prometheus metrics

```ini
[metrics]
ENABLED = true
TOKEN   = your-metrics-token
```

Access at: `https://forgejo.example.com/metrics`

Key metrics: `forgejo_repositories_total`, `forgejo_users_total`, `forgejo_issues_total`, `go_*` runtime metrics.

### Health check

```bash
curl https://forgejo.example.com/-/health
# Returns: OK
```

Use for load balancer health checks.

---

## Try It Yourself

**Exercise 1 - Explore the admin panel**

On your Forgejo instance:
1. Avatar > **Site Administration**
2. Explore each section
3. In **Authentication Sources**, note the available options including SAML (not present in Gitea CE)

**Exercise 2 - Create a test user**

1. **Users** > **Create User**
2. Username: `testuser`, email and password
3. Enable **Must change password on next login**
4. Test login as `testuser`
5. Delete the user (transfer/delete any repositories first)

**Exercise 3 - Doctor check**

```bash
forgejo doctor check --all
```

Review output for any warnings or errors.

**Exercise 4 - Test backup**

```bash
forgejo dump --file /tmp/forgejo-test.zip --skip-log
unzip -l /tmp/forgejo-test.zip | head -20
```

---

## Common Mistakes

**Running Forgejo as root**

Run as a dedicated non-root user (typically `git`). Root execution creates unnecessary security risk. Configure a systemd service with `User=git`.

**Not setting INSTALL_LOCK = true**

Without this, the installation wizard remains accessible. Set `INSTALL_LOCK = true` immediately after completing setup.

**Configuring SAML without testing in an incognito window**

Misconfigured SAML with no way back in (if local login is also disabled) causes a lockout. Always test in a separate browser session while remaining logged in as admin. Never disable local login until SAML is confirmed working.

**Not backing up SAML certificates with the rest of the system**

The SAML private key (`saml-key.pem`) is required for SAML authentication to work after a restore. Include it in your backup strategy. Store it securely - it should have 600 permissions and be owned by the Forgejo service user.

**Skipping release notes before upgrading**

Forgejo major version upgrades may have breaking database changes. Read the release notes for every major version in the upgrade path before proceeding.

---

## Summary

Forgejo administration follows the same model as Gitea: admin panel at `/-/admin`, user creation and management, organisation oversight, repository maintenance and CLI tools.

The key addition is **SAML 2.0 authentication**, included free. Configure it in `app.ini` with the IdP metadata URL and attribute mappings. Generate a self-signed certificate for the service provider. Provide the ACS URL and Entity ID to your identity provider. Test in an incognito session before going live.

LDAP and OAuth2 work identically to Gitea. Global 2FA enforcement is available via `REQUIRE_SIGNIN_2FA = true`. Federation administration (enabling, blocking instances) is unique to Forgejo.

Back up the database, work directory and SAML certificates. The `forgejo dump` command creates a complete archive. Schedule automated backups with cron and test restores periodically.

---

## Sources

- [Forgejo: Administration documentation](https://forgejo.org/docs/latest/admin/)
- [Forgejo: SAML documentation](https://forgejo.org/docs/latest/admin/saml/)
- [Forgejo: LDAP authentication](https://forgejo.org/docs/latest/admin/authentication/#ldap)
- [Forgejo: Configuration reference](https://forgejo.org/docs/latest/admin/config-cheat-sheet/)
- [Forgejo: Backup and restore](https://forgejo.org/docs/latest/admin/backup-and-restore/)
- [Forgejo: Federation](https://forgejo.org/docs/latest/admin/federation/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

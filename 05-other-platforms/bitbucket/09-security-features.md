# Security Features in Bitbucket

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Security in Bitbucket operates at multiple levels: who can access your code, where they can access it from, what they must authenticate with, what happens when secrets are accidentally committed and what conditions must be met before code reaches production. This file covers every security feature available in Bitbucket Cloud, from the basics (2FA enforcement, SSH key management) through to advanced controls (IP allowlisting, Atlassian Guard SSO, secret scanning, merge checks as security gates).

Understanding which features are available on which plan is important: some security controls are free, some require Standard and the most powerful enforcement mechanisms require Premium or the Atlassian Guard add-on.

---

## Table of Contents

1. [Security overview: what Bitbucket provides](#security-overview-what-bitbucket-provides)
2. [Authentication security](#authentication-security)
3. [Two-factor authentication enforcement](#two-factor-authentication-enforcement)
4. [SSH key management](#ssh-key-management)
5. [API token controls](#api-token-controls)
6. [IP allowlisting](#ip-allowlisting)
7. [Secret scanning](#secret-scanning)
8. [Merge checks as security gates](#merge-checks-as-security-gates)
9. [Atlassian Guard: enterprise identity security](#atlassian-guard-enterprise-identity-security)
10. [Repository access control](#repository-access-control)
11. [Audit logs](#audit-logs)
12. [Pipeline security](#pipeline-security)
13. [Security for self-hosted (Data Center)](#security-for-self-hosted-data-center)
14. [Security checklist](#security-checklist)
15. [Try It Yourself](#try-it-yourself)
16. [Common Mistakes](#common-mistakes)
17. [Summary](#summary)
18. [Sources](#sources)

---

## Security overview: what Bitbucket provides

Bitbucket's security model covers four domains:

**Identity and access** - who can sign in to Bitbucket, with what credentials, from where and what they are allowed to do once authenticated.

**Code protection** - preventing unauthorised changes to important branches, requiring review before merging, blocking force pushes that could erase history.

**Secret protection** - detecting API keys, passwords and tokens that were accidentally committed to repositories.

**Pipeline security** - securing the CI/CD system against secrets exposure, credential theft and supply chain attacks.

The right combination of these controls depends on your team size, industry requirements and risk tolerance. A solo developer needs different security than a 500-person engineering organisation in a regulated industry.

---

## Authentication security

### Atlassian account security

Bitbucket authentication runs through Atlassian's identity platform. The strength of your Bitbucket security depends on the strength of your Atlassian account security. Key practices:

**Strong passwords.** Atlassian enforces a minimum password length but does not enforce complexity requirements beyond that. Use a password manager and generate a long, random password.

**Two-factor authentication.** Enable 2FA using an authenticator app. An attacker with your password cannot sign in without also having your second factor.

**No shared accounts.** Every team member should have their own Atlassian account. Shared accounts make audit trails useless (you cannot tell who did what) and make access revocation impossible (revoking one person's access revokes everyone sharing the account).

**Review active sessions.** Check [id.atlassian.com](https://id.atlassian.com) > Security > Active sessions periodically. If you see sessions from unexpected devices or locations, revoke them and change your password.

### App passwords are gone

**App passwords are permanently retired from June 2026.** New app passwords were blocked in September 2025. Anyone still using app passwords after June 2026 will lose access to HTTPS-based Git operations and the API. The replacement is API tokens (see the API token controls section below).

---

## Two-factor authentication enforcement

Individual 2FA setup is covered in [02-creating-an-account.md](02-creating-an-account.md). This section covers how workspace administrators enforce 2FA for all members.

### Enforcing 2FA at the workspace level

Workspace administrators can require all workspace members to have 2FA enabled:

1. Go to **Workspace settings**
2. Click **Security** under the workspace management section
3. Enable **Require two-step verification for all members**

When this is enabled:

- Members who do not have 2FA enabled are immediately shown a prompt to set it up
- Members who do not complete 2FA setup lose access to the workspace until they do
- New members must enable 2FA before they can access workspace resources

> [!IMPORTANT]
> Enabling 2FA enforcement can immediately lock out team members who have not yet set it up. Warn your team before enabling this setting and give them time to configure 2FA. Consider setting a grace period if your organisation has a large team.

### 2FA and CI/CD

Requiring 2FA for human accounts does not affect Bitbucket Pipelines or automated processes that use API tokens for authentication. API tokens do not require 2FA - they are machine credentials, not human login credentials. Service accounts and CI/CD integrations using API tokens continue to work when 2FA enforcement is enabled.

### Atlassian Guard for enterprise 2FA

For enterprise-grade 2FA management, Atlassian Guard provides:

- Authentication policies that enforce specific 2FA methods (hardware keys only, authenticator apps only)
- Visibility into which team members have 2FA enabled across your entire Atlassian organisation
- The ability to require re-authentication after a set session duration
- Conditional access policies (require 2FA only when accessing from outside the corporate network)

---

## SSH key management

SSH keys authenticate Git operations over the SSH protocol without requiring a username and password. They are more secure than password-based HTTPS because private keys never leave your machine.

### Key types and recommendations

| Algorithm | Recommendation                       | Key size         |
| --------- | ------------------------------------ | ---------------- |
| `ed25519` | Preferred - modern, fast, small keys | Fixed (256-bit)  |
| `rsa`     | Acceptable if ed25519 not supported  | 4096-bit minimum |
| `dsa`     | Insecure - do not use                | -                |
| `ecdsa`   | Acceptable but ed25519 preferred     | 256-bit+         |

Always use `ed25519` unless you have a specific reason not to.

### Per-repository SSH keys (access keys)

Beyond personal SSH keys tied to user accounts, Bitbucket supports **access keys** - SSH keys tied to a specific repository rather than a user account. Access keys are used for:

- CI/CD systems that need to clone a specific repository
- Deploy scripts that need read-only access to one repository
- Automated tools that should only access specific repositories, not all repositories the user can access

**Adding a repository access key:**

1. Go to **Repository settings** > **Access keys**
2. Click **Add key**
3. Give it a label
4. Paste the public key
5. Choose Read or Read/Write access
6. Click **Add key**

Access keys with Read access can clone and fetch. Read/Write can also push. For deployment and CI/CD use cases, Read access is usually sufficient.

### Key rotation policy

SSH keys do not expire automatically. As a security practice:

- Rotate SSH keys annually or when a team member leaves
- Remove access keys from repositories when the integration they were created for is decommissioned
- Review the list of SSH keys on user accounts and repository access keys periodically

---

## API token controls

API tokens replace app passwords as the mechanism for HTTPS authentication and REST API access. Full setup is in [02-creating-an-account.md](02-creating-an-account.md). This section covers the security aspects of token management.

### Token scope (least privilege)

Every API token has a set of permission scopes. Create tokens with the minimum permissions required:

- A token for cloning repositories needs only `Repositories: Read`
- A token for pushing code needs `Repositories: Write`
- A token for pipeline automation might need `Repositories: Write` and `Pipelines: Write`
- A token should never have `Account: Admin` unless it explicitly needs to manage workspace settings

The principle of least privilege limits the blast radius if a token is compromised. A read-only token that leaks cannot be used to push malicious code or delete branches.

### Token expiry

Set expiry dates on all tokens. Short-lived tokens are safer than long-lived tokens:

- Project-specific tokens: expire when the project ends
- CI/CD tokens: rotate on a schedule (every 90 days is a reasonable default)
- Developer personal tokens: 1 year maximum, with a calendar reminder to rotate

### Token revocation

Revoke tokens immediately when:

- A developer leaves the team
- A token value is accidentally exposed (committed to code, shown in a screenshot, logged to an insecure location)
- A system using a token is decommissioned

Revocation is instant. Once revoked, the token cannot be used for any authentication.

### Detecting token exposure

If a token is committed to a repository by mistake:

1. Revoke the token immediately in Bitbucket account settings
2. Create a new token to replace it
3. Remove the token from the repository's Git history (see the secret scanning section below)

A token that was pushed to a public repository should be considered permanently compromised, even if you delete it from the current state of the repository. The history is public and can be crawled. Revoke and replace it.

---

## IP allowlisting

**Plan required: Premium**

IP allowlisting restricts access to Bitbucket resources to connections originating from specific IP addresses or CIDR ranges. Only requests from the allowlisted addresses can authenticate and access the workspace.

### Use cases

- Enforce that Bitbucket can only be accessed from the corporate office network or VPN
- Prevent access from personal devices or home networks in high-security environments
- Satisfy compliance requirements that mandate IP-based access controls (financial services, healthcare)

### Configuring IP allowlisting

1. Go to **Workspace settings** > **Security** > **IP allowlisting**
2. Enable IP allowlisting
3. Add the IP addresses or CIDR ranges that should have access:
   - Single IP: `203.0.113.45`
   - CIDR range: `203.0.113.0/24` (covers 203.0.113.0 to 203.0.113.255)
   - IPv6: `2001:db8::/32`

### What IP allowlisting affects

When enabled, IP allowlisting applies to:

- Web access to bitbucket.org (browser)
- Git operations over HTTPS
- Git operations over SSH
- REST API access

### Important warnings before enabling

> [!WARNING]
> If you enable IP allowlisting and your current IP address is not in the allowlist, you will immediately lose access to the workspace. Always add your current IP address before enabling. Test from a second device if possible.

> [!WARNING]
> CI/CD pipelines running on Bitbucket's hosted runners use dynamic IP addresses that change. If your pipeline uses HTTPS to access external Bitbucket resources or the Bitbucket API, those IPs must be allowlisted. Atlassian publishes a list of Bitbucket's egress IPs. Alternatively, use self-hosted runners with known, static IP addresses for IP-allowlisted environments.

### Getting Bitbucket's egress IPs

Atlassian publishes the current list of IP ranges used by Bitbucket Cloud's infrastructure at:
[ip-ranges.atlassian.com](https://ip-ranges.atlassian.com)

If you use IP allowlisting with external services that need to accept connections from Bitbucket Pipelines, use this list.

---

## Secret scanning

Bitbucket's secret scanning feature detects API keys, passwords, tokens and other sensitive values that are accidentally committed to a repository. When a commit containing a recognised secret pattern is pushed, Bitbucket flags it.

### What secret scanning detects

Bitbucket scans for patterns matching known credential formats including:

- AWS access keys and secret keys
- Google Cloud service account keys
- Stripe API keys
- GitHub and GitLab tokens
- Atlassian API tokens
- Private SSH keys
- JWT tokens
- Generic high-entropy strings that match credential patterns

### Plan availability

Secret scanning is available on **Standard and Premium plans**. It is not available on the free tier.

### What happens when a secret is detected

When a commit containing a detected secret is pushed:

1. Bitbucket sends an email notification to the repository administrators
2. The secret finding appears in the repository's security tab
3. The code push is **not blocked** - the commit is accepted and the code is pushed successfully

> [!NOTE]
> Unlike some platforms (GitHub Advanced Security can block pushes), Bitbucket's secret scanning is currently **detect and notify** rather than **detect and block**. The goal is to alert you quickly so you can act, not to block developer workflows. Future versions may add push protection.

### Responding to a secret detection

When you receive a secret scanning alert:

**Step 1 - Revoke the credential immediately.** Even before cleaning up the repository, revoke the API key, rotate the password or invalidate the token. A credential in the repository history is compromised regardless of whether you delete it from the code.

**Step 2 - Remove the secret from the repository history.** Use `git filter-repo` (the modern replacement for `git filter-branch`):

```bash
# Install git-filter-repo
pip install git-filter-repo

# Remove a specific file from all history
git filter-repo --path secrets.txt --invert-paths

# Replace a specific string in all commits
git filter-repo --replace-text <(echo 'ACTUAL_SECRET_VALUE==>REMOVED')

# Force push the rewritten history (coordinate with your team first)
git push origin --force --all
git push origin --force --tags
```

**Step 3 - Force push the cleaned history.** After rewriting history, force push all branches and tags. All team members must delete their local clones and re-clone - stale local copies still contain the secret in their history.

**Step 4 - Rotate all related credentials.** If the leaked credential had access to other systems, audit those systems for unauthorised access and rotate all related credentials.

> [!CAUTION]
> If the repository is or was public at any time while the secret was in the history, assume the secret has been harvested by automated scanners. Credential harvesting bots scan public repositories continuously. Revocation is non-negotiable.

### Preventing secrets in repositories

Prevention is better than detection:

**Use a `.gitignore` for secret-containing files:**

```gitignore
.env
.env.local
*.pem
*_rsa
config/secrets.yml
credentials.json
```

**Use pre-commit hooks to scan before pushing:**

```bash
# Install detect-secrets
pip install detect-secrets

# Scan the staged changes
detect-secrets scan --baseline .secrets.baseline
```

**Use environment variables instead of files:**
Store secrets in Bitbucket's secured repository variables, not in files committed to the repository. The pipeline reads secrets from environment variables at runtime.

---

## Merge checks as security gates

Branch permission merge checks serve a dual purpose: they enforce code quality and they enforce security practices. Key merge checks from a security perspective:

**No failed builds** - prevents merging code that fails security scanning pipelines. If your pipeline includes SAST (Static Application Security Testing) via a security pipe, failing the security scan fails the build, which blocks the merge.

**Minimum approvals** - ensures that security-sensitive code changes have been reviewed by a human. Automated scanning catches known patterns; human review catches logic flaws.

**Author cannot approve** - prevents developers from bypassing review on their own code changes.

### Adding security scanning to your pipeline

```yaml
pipelines:
  pull-requests:
    "**":
      - step:
          name: Security scan
          script:
            - pipe: snyk/snyk-scan:1.0.0
              variables:
                SNYK_TOKEN: $SNYK_TOKEN
                LANGUAGE: "node"
                SEVERITY_THRESHOLD: "high"
                FAIL_ON_ISSUES: "true"
```

With `FAIL_ON_ISSUES: 'true'`, Snyk fails the pipeline step if high-severity vulnerabilities are found. Combined with the "no failed builds" merge check, this blocks merging vulnerable code.

---

## Atlassian Guard: enterprise identity security

Atlassian Guard (formerly Atlassian Access) is a separately priced add-on that provides enterprise identity and security management across all Atlassian Cloud products including Bitbucket.

### Guard Standard ($4.20/user/month)

**Single sign-on (SSO) via SAML 2.0** - integrate with your corporate identity provider (Okta, Azure Active Directory, Google Workspace, Ping Identity). Team members authenticate through your IDP rather than maintaining Atlassian passwords.

**SCIM provisioning** - automatically create and deactivate Atlassian accounts based on your IDP's user directory. When an employee joins, their Atlassian account is created automatically. When they leave, it is deactivated immediately - no manual account cleanup.

**Authentication policies** - configure which sign-in methods are permitted. Restrict to SSO only (disable direct Atlassian password login), require hardware security keys, set session duration limits.

**API token controls** - see which users have created API tokens, when they were last used and revoke them centrally if needed.

**Two-step verification controls** - enforce 2FA across the organisation from a central location, with visibility into compliance status.

### Guard Premium (~$8.18/user/month)

**Data classification** - classify data sensitivity across Atlassian products and enforce access controls based on classification.

**Anomaly detection** - identify unusual login patterns (unexpected geolocation, time of day, device fingerprint) and flag or block suspicious sessions.

**SIEM integration** - forward security events to your Security Information and Event Management system (Splunk, Datadog, etc.) for centralised security monitoring.

**Content scanning** - scan Confluence pages and Bitbucket repositories for sensitive data patterns (PII, payment card data, etc.).

### When Guard is required

Guard is required if your organisation needs:

- SSO for Bitbucket (any tier of Guard)
- Automated user provisioning/deprovisioning from your HR system
- Centralised management of authentication policies
- Compliance evidence showing centralised access management

For teams without these requirements, Bitbucket's built-in security controls (2FA, SSH keys, API tokens, branch permissions) are sufficient.

---

## Repository access control

### Workspace roles

| Role       | Capabilities                                                 |
| ---------- | ------------------------------------------------------------ |
| **Owner**  | Full admin access: billing, workspace deletion, all settings |
| **Admin**  | Manage members, create repos, change all settings            |
| **Member** | Access repositories based on individual repo permissions     |

### Repository roles

| Role      | Read | Clone | Push | Create PR | Merge PR | Admin |
| --------- | ---- | ----- | ---- | --------- | -------- | ----- |
| **Read**  | Yes  | Yes   | No   | No        | No       | No    |
| **Write** | Yes  | Yes   | Yes  | Yes       | No       | No    |
| **Admin** | Yes  | Yes   | Yes  | Yes       | Yes      | Yes   |

### Group-based access

For large teams, use groups to manage access:

1. Create groups in workspace settings (e.g. "Backend Team", "Security Team", "QA")
2. Add users to groups
3. Grant groups access to repositories instead of granting access to individuals

This scales: when someone joins the backend team, add them to the Backend Team group and they get all the repository permissions the group has. When they leave, remove them from the group.

---

## Audit logs

**Plan required: Premium**

Audit logs record security-relevant events across the workspace:

- User sign-ins and sign-out events
- Repository creation, deletion and transfers
- Permission changes (who granted what to whom)
- Branch permission modifications
- SSH key additions and removals
- API token creation and revocation
- Pull request merges
- Pipeline runs and their results

### Accessing audit logs

1. Go to **Workspace settings**
2. Click **Audit log** under Security

Logs can be filtered by event type, user and date range. Logs can also be exported for external analysis or SIEM ingestion.

### Compliance use cases

Audit logs satisfy compliance requirements that mandate evidence of access controls and change management:

- SOC 2 Type II: evidence of who had access and when
- ISO 27001: change management records
- PCI-DSS: audit trail of changes to code handling payment data
- HIPAA: access records for code handling health information

---

## Pipeline security

Bitbucket Pipelines has specific security considerations beyond general Bitbucket account security.

### Securing pipeline variables

Use **secured variables** (not unsecured) for any sensitive values:

- API keys for cloud providers
- Database connection strings
- Deployment credentials
- Notification webhook URLs

Secured variables are:

- Not shown in plaintext in the Bitbucket UI after creation
- Masked in pipeline logs (the value is replaced with `****`)
- Not available to pipelines triggered from forked repositories (prevents secret exfiltration via PRs from forks)

### Fork security

> [!IMPORTANT]
> Secured repository variables are **not accessible** in pipelines triggered by pull requests from forked repositories. This is a deliberate security control. If a malicious actor forks your repository and opens a PR, the pipeline triggered by that PR cannot access your secrets. The pipeline runs but without access to sensitive variables.

This means your pull request pipeline must be designed to work without sensitive variables (it should only run tests, not deploy to production). Deployment pipelines targeting production branches are only triggered by pushes to those branches - not by PRs from forks.

### OIDC for secretless cloud authentication

Modern cloud providers support OpenID Connect (OIDC) for CI/CD authentication - instead of storing a long-lived cloud credential in a Bitbucket variable, the pipeline authenticates using a short-lived token issued by Bitbucket.

Bitbucket Pipelines supports OIDC on V5 premium runners. With OIDC:

- No long-lived AWS, GCP or Azure credentials stored in Bitbucket
- The cloud provider trusts Bitbucket's identity tokens
- Credentials cannot be extracted even if a pipeline is compromised

```yaml
- step:
    name: Deploy to AWS (OIDC)
    oidc: true
    script:
      - export AWS_WEB_IDENTITY_TOKEN_FILE=$(cat $BITBUCKET_STEP_OIDC_TOKEN)
      - aws sts assume-role-with-web-identity \
        --role-arn arn:aws:iam::123456789:role/BitbucketDeployRole \
        --role-session-name bitbucket-pipeline \
        --web-identity-token file://$BITBUCKET_STEP_OIDC_TOKEN
```

---

## Security for self-hosted (Data Center)

Bitbucket Data Center users have additional security responsibilities since they manage the infrastructure:

**Network security** - Bitbucket Data Center should be placed behind a reverse proxy (Nginx, Apache) and load balancer. Direct internet access to the application server is not recommended. Use HTTPS with valid certificates.

**Operating system hardening** - keep the underlying Linux OS patched and updated. Bitbucket runs on Java; keep the JDK updated.

**Database security** - the database (PostgreSQL or MySQL) should not be accessible from the internet. Use internal network access only with strong credentials.

**File system permissions** - the Bitbucket home directory contains repository data. Restrict access to the Bitbucket service account only.

**Backup encryption** - backup the Bitbucket home directory and database regularly. Encrypt backups at rest.

**LDAP/SAML integration** - configure Bitbucket Data Center to authenticate against your corporate LDAP/Active Directory or SAML IDP rather than using Bitbucket-internal user accounts.

---

## Security checklist

Work through this checklist for any production Bitbucket workspace:

### Account security

- [ ] All team members have 2FA enabled
- [ ] 2FA enforcement is enabled at the workspace level
- [ ] No shared accounts
- [ ] Password manager in use for all Atlassian accounts
- [ ] Active sessions reviewed periodically

### Authentication

- [ ] API tokens in use (no app passwords)
- [ ] All tokens have expiry dates set
- [ ] Tokens created with minimum necessary permissions
- [ ] SSH keys use ed25519 algorithm
- [ ] Unused SSH keys and repository access keys removed

### Code protection

- [ ] `main` and release branches have write restrictions (no direct pushes)
- [ ] Force push protection enabled on all permanent branches
- [ ] Branch delete restriction on permanent branches
- [ ] Minimum approval merge checks configured
- [ ] Required builds merge check enabled
- [ ] Author cannot approve their own PRs

### Secret management

- [ ] No secrets in committed files
- [ ] `.gitignore` covers secret-containing file patterns
- [ ] Secret scanning enabled (Standard/Premium)
- [ ] All pipeline secrets stored as secured repository variables
- [ ] CI/CD pipelines use OIDC where supported

### Access control

- [ ] Repository access follows least privilege
- [ ] Groups used for access management (not individual permissions)
- [ ] Leaver process includes Bitbucket account revocation

### Enterprise (if applicable)

- [ ] Atlassian Guard connected for SSO
- [ ] SCIM provisioning configured
- [ ] IP allowlisting configured (Premium)
- [ ] Audit logs monitored
- [ ] SIEM integration configured (Guard Premium)

---

## Try It Yourself

**Exercise 1 - Check your 2FA status**

1. Go to [id.atlassian.com](https://id.atlassian.com)
2. Navigate to Security
3. Verify 2FA is enabled with an authenticator app
4. Download recovery codes if you have not already

**Exercise 2 - Audit your SSH keys**

1. Go to **Personal settings** > **SSH keys**
2. Review each key - do you recognise all of them?
3. Remove any keys from devices you no longer use
4. Check the key types - if any are RSA, consider replacing with ed25519

**Exercise 3 - Create a minimal-permission API token**

1. Go to **Personal settings** > **API tokens**
2. Create a token named "Read-only test" with `Repositories: Read` permission only
3. Set it to expire in 7 days
4. Test that you can clone with it but cannot push

**Exercise 4 - Simulate a secret in a commit (test environment only)**

In a private test repository:

1. Create a file containing a fake credential: `echo "API_KEY=FAKE_TEST_KEY_12345" > test-secret.txt`
2. Commit and push it
3. Check whether Bitbucket flags it (requires Standard/Premium for secret scanning)
4. Immediately delete the file and commit again
5. Practice removing it from history with git filter-repo

---

## Common Mistakes

**Not revoking API tokens when team members leave**

When a developer leaves, their Atlassian account should be deactivated (or their workspace membership removed). But any API tokens they created for CI/CD systems, scripts or integrations continue to work until explicitly revoked. Audit token ownership during offboarding.

**Using the same API token for multiple purposes**

Creating one "master" token with all permissions for all uses is convenient but dangerous. If that token leaks, everything is exposed. Create separate tokens for separate purposes with appropriate scopes. When one token needs to be rotated (system decommissioned, suspected exposure), you only need to rotate that token.

**Trusting advisory merge checks on Free/Standard**

Merge checks on Free and Standard plans are advisory. A determined developer can merge a PR despite failing checks. For enforced security gates, the Premium plan is required.

**Enabling IP allowlisting without preparing the team**

IP allowlisting takes effect immediately. If team members are working remotely or from personal devices not on the allowlist, they are locked out the moment you enable it. Communicate the change in advance, get everyone's IPs added to the allowlist, and consider a grace period.

**Not scanning for secrets before making a repository public**

Before making any repository public, use `git log --all -- '*.env'`, `git log -S 'API_KEY'` and a dedicated secret scanning tool like truffleHog or detect-secrets to scan the entire commit history for credentials. A secret in any commit - even one deleted in a later commit - is exposed when the repository is public.

---

## Summary

Bitbucket security operates across four domains: identity and access, code protection, secret protection and pipeline security.

Identity security starts with strong Atlassian accounts: 2FA (enforced at workspace level), SSH keys using ed25519, and API tokens with appropriate scopes and expiry dates. App passwords are fully retired from June 2026.

Code protection uses branch permissions to prevent unauthorised pushes, force pushes and deletions on important branches. Merge checks (enforced on Premium) block merges that do not meet quality and security requirements.

Secret scanning (Standard/Premium) detects accidentally committed credentials and notifies administrators. Prevention is more effective than detection: `.gitignore` patterns, secured pipeline variables and pre-commit hooks stop secrets from reaching the repository.

IP allowlisting (Premium) restricts access to known networks. Atlassian Guard adds enterprise identity management: SSO, SCIM provisioning, authentication policies and centralised API token management. Audit logs (Premium) provide the compliance evidence that regulated industries require.

---

## Sources

- [Atlassian: Bitbucket Cloud security](https://www.atlassian.com/trust/security/security-practices)
- [Atlassian: IP allowlisting](https://support.atlassian.com/bitbucket-cloud/docs/control-access-to-your-private-content/)
- [Atlassian: Secret scanning](https://support.atlassian.com/bitbucket-cloud/docs/detect-secrets-in-your-repositories/)
- [Atlassian: Branch permissions](https://support.atlassian.com/bitbucket-cloud/docs/use-branch-permissions/)
- [Atlassian: Repository access keys](https://support.atlassian.com/bitbucket-cloud/docs/set-up-access-keys/)
- [Atlassian: Atlassian Guard](https://www.atlassian.com/software/access)
- [Atlassian: Pipelines OIDC](https://support.atlassian.com/bitbucket-cloud/docs/deploy-on-aws-using-bitbucket-pipelines-oidc/)
- [Atlassian: Audit log](https://support.atlassian.com/bitbucket-cloud/docs/view-workspace-audit-log/)
- [Atlassian IP ranges](https://ip-ranges.atlassian.com)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

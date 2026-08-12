# What Should and Should Not Be on GitHub

**Difficulty:** 🟢 Beginner | **Time:** 25 minutes

Putting the wrong things on GitHub can compromise your security, expose someone's personal data, violate a licence, get your account suspended or cause real financial damage. This file explains exactly what belongs on GitHub, what must never go there, how to protect yourself before you push and precisely what to do when something sensitive has already been committed and pushed.

---

## Table of Contents

- [What GitHub is for](#what-github-is-for)
- [What should never go on GitHub](#what-should-never-go-on-github)
- [Secrets and credentials - the most dangerous mistake](#secrets-and-credentials---the-most-dangerous-mistake)
- [Why private repositories are not safe enough for secrets](#why-private-repositories-are-not-safe-enough-for-secrets)
- [Personal and private data](#personal-and-private-data)
- [Legal issues - licences and copyright](#legal-issues---licences-and-copyright)
- [Binary files and large assets](#binary-files-and-large-assets)
- [Platform-specific junk files](#platform-specific-junk-files)
- [What to do if you have already pushed something sensitive](#what-to-do-if-you-have-already-pushed-something-sensitive)
- [Removing a file from Git history with git filter-repo](#removing-a-file-from-git-history-with-git-filter-repo)
- [Removing a file with BFG Repo Cleaner](#removing-a-file-with-bfg-repo-cleaner)
- [Replacing a string across all history](#replacing-a-string-across-all-history)
- [After cleaning history - essential follow-up steps](#after-cleaning-history---essential-follow-up-steps)
- [Protecting yourself going forward](#protecting-yourself-going-forward)
- [Setting up gitleaks as a pre-commit hook](#setting-up-gitleaks-as-a-pre-commit-hook)
- [Using detect-secrets](#using-detect-secrets)
- [GitHub push protection](#github-push-protection)
- [The .env pattern - correct way to handle credentials](#the-env-pattern---correct-way-to-handle-credentials)
- [Secrets in CI/CD](#secrets-in-cicd)
- [GitHub Terms of Service - key restrictions](#github-terms-of-service---key-restrictions)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What GitHub Is For

GitHub is designed for:

- Source code in any programming language
- Plain text files: Markdown, YAML, JSON, TOML, XML, CSV, HTML, CSS
- Documentation and wikis
- Configuration files that do not contain secrets
- Scripts and automation
- Small static assets for documentation (images under 1 MB, SVG icons)
- Infrastructure-as-code (Terraform, Kubernetes manifests, Ansible playbooks) - without embedded credentials
- CI/CD workflow definitions
- Issue tracking, project planning and collaboration

GitHub is **not** designed for:

- Credentials, secrets or tokens of any kind
- Personal data about identifiable individuals
- Large binary files (use Git LFS or dedicated asset storage)
- Compiled executables as the primary distribution method (use GitHub Releases for that)
- Database backups
- Log files
- Generated build output (use artefact storage in CI, not Git)

---

## What Should Never Go on GitHub

### Secrets and credentials - the most dangerous mistake

**Never commit to any GitHub repository - public or private:**

- API keys in any format (`sk-ant-...`, `AIza...`, `AKIA...`, `sk_live_...`, `pk_live_...`)
- Database connection strings containing a password: `postgresql://user:PASSWORD@host/db`
- Service account JSON files (Google Cloud, Firebase, AWS)
- `.env` files containing real values
- Any file named `secrets.json`, `credentials.json`, `config.json` or similar containing live values
- Private SSH keys (`-----BEGIN OPENSSH PRIVATE KEY-----` or `-----BEGIN RSA PRIVATE KEY-----`)
- Private GPG keys
- OAuth client secrets
- JWT signing secrets (`SECRET_KEY`, `JWT_SECRET`)
- Stripe, PayPal or other payment processing API keys
- Twilio, SendGrid, Mailgun, Postmark API keys (these can send emails and run up bills)
- AWS Access Key ID and Secret Access Key (`AKIAIOSFODNN7EXAMPLE` format)
- GitHub Personal Access Tokens (ironic but common)
- Slack bot tokens, Discord bot tokens, Telegram bot tokens
- `*.pem` files, `*.p12` files, `*.pfx` files (certificate files with private keys)
- Any hardcoded password, even a "test" one

---

## Secrets and Credentials - The Most Dangerous Mistake

Leaked credentials cause real harm:

**Immediate bot harvesting:** Automated scanners monitor every public GitHub push in real time. A token pushed to a public repository is typically found and used within 30 seconds to a few minutes. These bots are run by attackers looking for AWS keys (to mine cryptocurrency), Stripe keys (to steal money), email service keys (to send spam) and database credentials (to exfiltrate data).

**Financial damage:** A leaked AWS key was famously used to run up $50,000 in EC2 charges overnight. Attackers spin up GPU instances to mine cryptocurrency - billed to the account owner.

**Data breach:** A leaked database connection string lets anyone connect to your database and download, modify or delete everything in it.

**Account compromise:** A leaked GitHub PAT lets an attacker access all repositories the token has permission for, modify code, add backdoors or delete repositories.

**The damage does not reverse when you delete the file.** Removing the secret from your repository does not undo the harm if it was already seen and used. Revoke the credential first. Always.

---

## Why Private Repositories Are Not Safe Enough for Secrets

Many developers believe private repositories are safe places for secrets. They are not safe enough.

**Private repositories can be accessed by:**

- GitHub staff (for legal, safety or abuse investigations)
- All collaborators with any level of access - they can clone the repository and store the history permanently
- Anyone who ever had access, even if you removed them - they may have already cloned it
- Future repository owners if you transfer the repository
- GitHub's internal systems for security scanning and compliance

**Private repositories can become public:**

- A repository made public (accidentally or intentionally) immediately exposes its entire history
- A repository transferred to a new owner may change visibility settings
- A public fork of a later-made-public repository retains the history

**The correct approach:** Treat every repository - public and private - as if it might one day be public. Secrets do not belong in Git history regardless of visibility.

---

## Personal and Private Data

**Never push:**

- Full names combined with email addresses, phone numbers or physical addresses
- Medical records, health information or conditions
- Financial records, bank account details, credit card numbers
- Passport numbers, national insurance numbers, social security numbers, driving licence numbers
- Biometric data
- Private messages, emails or communications
- Student records or grades
- IP addresses combined with other identifying information
- Location history
- Any data about individuals who have not consented to its publication

Storing personal data on GitHub (especially in public repositories) may violate:

- **UK GDPR / Data Protection Act 2018** - UK and post-Brexit
- **EU GDPR** - European Union
- **CCPA** - California Consumer Privacy Act
- Other national data protection laws

Violations can result in significant fines and legal liability.

---

## Legal Issues - Licences and Copyright

**Code you do not have the right to distribute:**

- Proprietary source code from a previous or current employer (unless you have explicit written permission to make it public)
- Client code if your contract does not grant you redistribution rights
- Code licensed under terms that prohibit redistribution or require different licensing
- Third-party libraries not under an open source licence

**Employment contracts and IP assignment:**

Many employment contracts include an Intellectual Property (IP) assignment clause that gives the employer ownership of code you write - even personal projects, even on your own hardware, even on your own time - if it is related to the employer's business. Read your contract before open-sourcing anything you wrote while employed.

**Licence compatibility:**

If you include GPL-licensed code in your project, your project must also be GPL-licensed (or compatible). Mixing MIT and GPL code can be done but check the specific versions. Do not include third-party code without verifying its licence.

**What to do if unsure:** Do not publish until you have confirmed you have the rights. Ask a lawyer if significant code or significant risk is involved.

---

## Binary Files and Large Assets

Git stores the complete content of every version of every tracked file. Binary files cause specific problems:

**History bloat:** A 20 MB video file changed 5 times stores 100 MB in Git history. This makes every `git clone` slow - even if the video was deleted later, the history still contains all 5 versions.

**Meaningless diffs:** Git cannot show what changed inside a compiled binary, image or video. `git diff` is useless for binary files. Every change is stored as a complete new copy.

**Clone performance:** Large binary histories make repositories slow to clone, pull and push. New contributors on slow connections suffer most.

**Solutions:**

| Asset type | Correct approach |
|---|---|
| Large images, video, audio | Git LFS |
| Design files (`.fig`, `.psd`, `.ai`, `.sketch`) | Git LFS |
| Compiled binaries (`.exe`, `.dll`, `.so`, `.dylib`) | Git LFS or GitHub Releases |
| Datasets (CSV > 10 MB, JSON > 10 MB) | Git LFS, DVC or cloud storage |
| Fonts | Git LFS if large; small fonts (< 500 KB) can go in the repo |
| PDF documentation | Git LFS |
| Test fixture files (small PNGs for tests) | Fine in the repo if small (< 1 MB each) |
| node_modules, .venv, vendor | Never - regenerate from lockfile |
| Build output (dist/, build/, target/) | Never - add to .gitignore |

**Git LFS in brief:**

```bash
# Install Git LFS (once per machine)
git lfs install

# Tell LFS to track a file type
git lfs track "*.psd"
git lfs track "*.zip"

# The .gitattributes file is updated - commit it
git add .gitattributes
git commit -m "add: track PSD and ZIP files with Git LFS"

# From now on, matching files are stored in LFS automatically
git add design.psd
git commit -m "add: initial design file"
git push
```

See `git/22-large-repos.md` for the full LFS guide.

---

## Platform-Specific Junk Files

These files are generated by your operating system or editor and are meaningless to other developers. Add them to your global `~/.gitignore` so they are never committed from any project.

**🪟 Windows:**

```
Thumbs.db
Thumbs.db:encryptable
ehthumbs.db
Desktop.ini
$RECYCLE.BIN/
*.lnk
```

**🍎 Mac:**

```
.DS_Store
.AppleDouble
.LSOverride
._*
.Spotlight-V100
.Trashes
Icon
```

**🐧 Linux:**

```
*~
.directory
.Trash-*
.nfs*
```

**Editors and IDEs:**

```
.idea/                    # JetBrains (all IDEs)
*.iml                     # JetBrains module files
.vscode/settings.json     # VS Code personal settings (not launch.json which can be shared)
*.suo                     # Visual Studio
*.user                    # Visual Studio
*.swp                     # Vim swap files
*.swo                     # Vim swap files
*~                        # Emacs backup files
.history/                 # Local history extension
```

**Build outputs:**

```
node_modules/
.npm/
__pycache__/
*.pyc
*.pyo
*.pyd
.Python
dist/
build/
out/
target/                   # Rust, Java Maven
bin/
obj/
*.egg-info/
.eggs/
.gradle/
```

**Environment and secrets:**

```
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
*.env
.envrc
```

**Setting up a global gitignore:**

🍎🐧:

```bash
git config --global core.excludesfile ~/.gitignore_global
```

🪟:

```powershell
git config --global core.excludesfile "$env:USERPROFILE\.gitignore_global"
```

Then add your OS and editor patterns to that file. Use `gitignore.io` or `github.com/github/gitignore` to generate a comprehensive file for your tools.

---

## What to Do If You Have Already Pushed Something Sensitive

If you have pushed a secret, API key, credential or sensitive file to GitHub, follow this exact sequence:

### Step 1 - Revoke the secret immediately

Before touching Git history, revoke or rotate the exposed credential in whatever service issued it:

- **AWS key:** Go to IAM → Security credentials → Deactivate the key, then create a new one
- **GitHub PAT:** Go to `github.com/settings/tokens` → Delete the token
- **Stripe key:** Go to Stripe Dashboard → Developers → API keys → Roll the key
- **Database password:** Change the database user's password in your database admin panel
- **Google Cloud service account:** Go to IAM → Service Accounts → Manage keys → Delete the key

Assume the secret was already seen and used. Revoking it limits ongoing damage.

### Step 2 - Make the repository private temporarily (if public)

While you clean history, make the repository private to prevent further exposure. Go to **Settings → Danger Zone → Change repository visibility → Make private**.

### Step 3 - Notify affected parties if necessary

If the secret gave access to user data or a production system and there is evidence or reason to believe it was accessed, follow your incident response process. In some jurisdictions, data breaches must be reported to regulators.

---

## Removing a File from Git History with git filter-repo

`git filter-repo` is the modern, recommended tool for rewriting Git history. It replaced `git filter-branch` which is slow and error-prone.

**Install git filter-repo:**

🍎🐧:

```bash
pip install git-filter-repo --break-system-packages
# Or with Homebrew on Mac:
brew install git-filter-repo
```

🪟:

```powershell
pip install git-filter-repo
```

**Remove a specific file from all history:**

```bash
# Work on a fresh clone to avoid affecting your working copy
git clone --mirror https://github.com/OWNER/REPO.git repo-mirror
cd repo-mirror

# Remove the file from all commits
git filter-repo --path path/to/secret-file.env --invert-paths

# Force push the rewritten history to all branches and tags
git push --force --all
git push --force --tags
```

**Remove multiple files:**

```bash
git filter-repo \
  --path .env \
  --path secrets.json \
  --path config/production.yml \
  --invert-paths
```

**Remove a whole directory:**

```bash
git filter-repo --path secrets/ --invert-paths
```

After running, check that the file is gone from history:

```bash
git log --all --full-history -- path/to/secret-file.env
# Should return nothing if the file was successfully removed
```

---

## Removing a File with BFG Repo Cleaner

BFG is faster than `git filter-repo` for simple cases on very large repositories with long histories. It requires Java.

**Download BFG:**

Get the JAR from `rtyley.github.io/bfg-repo-cleaner/`.

**Remove a specific file:**

```bash
# Clone a mirror
git clone --mirror https://github.com/OWNER/REPO.git repo-mirror

# Run BFG
java -jar bfg.jar --delete-files secret-file.env repo-mirror

# Clean up refs and garbage collect
cd repo-mirror
git reflog expire --expire=now --all
git gc --prune=now --aggressive

# Force push
git push --force --all
git push --force --tags
```

**Remove files matching a pattern:**

```bash
java -jar bfg.jar --delete-files "*.env" repo-mirror
java -jar bfg.jar --delete-files "{secret.json,credentials.json}" repo-mirror
```

**Remove a directory:**

```bash
java -jar bfg.jar --delete-folders secrets repo-mirror
```

---

## Replacing a String Across All History

If a secret is embedded inside a file (e.g. a config file that you want to keep but the secret value is hardcoded in it), you can replace the secret string with a placeholder throughout all history.

**With git filter-repo:**

Create a `replacements.txt` file:

```
# Format: LITERAL_STRING==>REPLACEMENT
ghp_actualSecretToken1234==>REMOVED_SECRET
postgresql://user:actualPassword@host/db==>REMOVED_DATABASE_URL
```

Then run:

```bash
git filter-repo --replace-text replacements.txt
```

This finds every occurrence of the secret string in every file in every commit and replaces it with the placeholder.

---

## After Cleaning History - Essential Follow-Up Steps

After rewriting history, several things need attention:

**1. Request a GitHub cache purge:**

GitHub caches repository content. Even after force-pushing cleaned history, some old content may still be accessible through GitHub's cached pages and APIs for a period of time.

Contact GitHub Support at `support.github.com` and request a cache purge for your repository. Explain that you have removed sensitive data from history and need the cached versions cleared. GitHub's support team handles these requests.

**2. All collaborators must re-clone:**

Rewriting history changes commit hashes. Anyone who cloned the repository before the rewrite has the old history locally. They cannot simply `git pull` - this will fail with a non-fast-forward error.

Notify all collaborators:

```bash
# They must delete their local copy and re-clone
git clone https://github.com/OWNER/REPO.git
```

Or they can reset their local copy:

```bash
git fetch origin
git checkout main
git reset --hard origin/main
```

**3. Check forks:**

If the repository is public and was forked before the history rewrite, those forks still contain the sensitive data in their history. GitHub does not automatically update forks when you rewrite history. Contact GitHub Support to request removal of cached data from forks as well.

**4. Check GitHub's cache:**

Old commit URLs (`github.com/OWNER/REPO/blob/OLD_HASH/file.ext`) may still be accessible via GitHub's web interface until the cache clears. This is why the Support request is important.

**5. Check any CI/CD logs:**

If the secret appeared in a workflow run log (e.g. accidentally echoed), those logs remain accessible. Go to the Actions tab and delete the affected workflow runs.

**6. Check any issues or PR comments:**

If you or anyone else pasted the secret into an issue or PR comment, edit the comment to remove it.

---

## Protecting Yourself Going Forward

The best approach is preventing secrets from reaching Git at all.

**Use a .gitignore from day one:**

Create a `.gitignore` before your first commit. Add `.env`, build directories and platform files. Once a file is committed, adding it to `.gitignore` stops future tracking but does not remove it from history.

Template: `gitignore.io` - type your OS, language and editor and get a complete `.gitignore`.

**Never hardcode secrets in source code:**

```python
# WRONG - hardcoded secret
api_key = "sk-ant-api03-actualKeyHere"

# CORRECT - loaded from environment variable
import os
api_key = os.environ.get("ANTHROPIC_API_KEY")
if not api_key:
    raise ValueError("ANTHROPIC_API_KEY environment variable is not set")
```

**Use the .env pattern:**

Create two files:

`.env` (in `.gitignore` - contains real values, never committed):
```
DATABASE_URL=postgresql://user:realpassword@localhost/mydb
STRIPE_SECRET_KEY=sk_live_realStripeKey
GITHUB_TOKEN=ghp_realToken
```

`.env.example` (committed to Git - shows structure, contains fake values):
```
# Copy this to .env and fill in real values
DATABASE_URL=postgresql://user:password@localhost/mydb
STRIPE_SECRET_KEY=sk_live_yourStripeKeyHere
GITHUB_TOKEN=ghp_yourGithubTokenHere
```

Load `.env` in your application using `python-dotenv` (Python), `dotenv` (Node.js), `godotenv` (Go) or similar.

---

## Setting Up Gitleaks as a Pre-Commit Hook

Gitleaks scans your staged changes for patterns that look like secrets before each commit. It blocks the commit if anything suspicious is found.

**Install gitleaks:**

🪟 **Windows:**

```powershell
winget install gitleaks
```

🍎 **Mac:**

```bash
brew install gitleaks
```

🐧 **Linux:**

```bash
# Download the latest release binary
LATEST=$(curl -s https://api.github.com/repos/gitleaks/gitleaks/releases/latest | grep tag_name | cut -d '"' -f 4)
curl -LO "https://github.com/gitleaks/gitleaks/releases/download/${LATEST}/gitleaks_${LATEST#v}_linux_x64.tar.gz"
tar -xzf gitleaks_*.tar.gz
sudo mv gitleaks /usr/local/bin/
```

**Scan a repository manually:**

```bash
gitleaks detect --source .
```

**Set up as a Git pre-commit hook:**

Create `.git/hooks/pre-commit`:

```bash
#!/bin/sh
gitleaks protect --staged --redact --no-banner
if [ $? -ne 0 ]; then
  echo ""
  echo "gitleaks detected a potential secret in your staged files."
  echo "Review the output above and remove the secret before committing."
  echo "If this is a false positive, run: git commit --no-verify"
  exit 1
fi
```

Make it executable:

```bash
chmod +x .git/hooks/pre-commit
```

**Using pre-commit framework (recommended for teams):**

```bash
pip install pre-commit --break-system-packages

# Create .pre-commit-config.yaml in your repository root:
```

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.0
    hooks:
      - id: gitleaks
```

```bash
pre-commit install
```

Now every `git commit` automatically runs gitleaks. The hook runs for every developer who runs `pre-commit install` in the repository.

---

## Using detect-secrets

`detect-secrets` by Yelp is an alternative secret scanner that uses a baseline approach - it creates a list of known false positives so they do not trigger alerts on every commit.

**Install:**

```bash
pip install detect-secrets --break-system-packages
```

**Create a baseline (scan existing codebase and mark known non-secrets):**

```bash
detect-secrets scan > .secrets.baseline
```

Review `.secrets.baseline` and mark any false positives as `"is_secret": false`.

**Add to pre-commit config:**

```yaml
repos:
  - repo: https://github.com/Yelp/detect-secrets
    rev: v1.4.0
    hooks:
      - id: detect-secrets
        args: ['--baseline', '.secrets.baseline']
```

---

## GitHub Push Protection

GitHub has built-in secret scanning that can automatically block pushes containing known secret formats.

**How it works:**

GitHub maintains patterns for hundreds of known secret formats - AWS access keys, GitHub tokens, Stripe keys, Twilio credentials, Google API keys and many more. When you push code, GitHub scans the diff for these patterns. If a match is found, the push is blocked and GitHub shows you exactly what was detected.

**Enable push protection:**

For public repositories, push protection is enabled by default.

For private repositories:

1. Go to your repository → **Settings → Code security**
2. Under **Secret scanning**, enable **Secret scanning**
3. Under **Push protection**, enable **Push protection**

For organisations, enable push protection across all repositories:

1. Go to Organisation **Settings → Code security and analysis**
2. Enable **Secret scanning** and **Push protection** for all repositories

**What happens when push protection blocks a push:**

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote:
remote: - Push cannot contain secrets
remote: Push blocked by GitHub secret scanning
remote:
remote: GitHub Actions Token
remote: Detected in commit: abc1234
remote: Location: src/config.py, line 42
```

You can review the detection and, if it is a false positive, bypass the block with a justification. GitHub logs all bypasses for audit purposes.

---

## The .env Pattern - Correct Way to Handle Credentials

The `.env` file pattern is the industry standard for managing secrets in development.

**How it works:**

Your application loads configuration from environment variables at startup. During development, a `.env` file in the project root provides those variables. In production, the real environment variables are set by the server, CI/CD system or secrets manager - never from a `.env` file.

**Python setup with python-dotenv:**

```bash
pip install python-dotenv
```

```python
from dotenv import load_dotenv
import os

load_dotenv()  # reads .env file into environment variables

DATABASE_URL = os.environ["DATABASE_URL"]  # raises KeyError if not set
API_KEY = os.getenv("API_KEY", "")         # returns "" if not set
```

**Node.js setup with dotenv:**

```bash
npm install dotenv
```

```javascript
require("dotenv").config();
// or in ES modules:
import "dotenv/config";

const dbUrl = process.env.DATABASE_URL;
const apiKey = process.env.API_KEY;
```

**Always add to .gitignore before creating the file:**

```bash
echo ".env" >> .gitignore
echo ".env.local" >> .gitignore
git add .gitignore
git commit -m "add: exclude .env files from tracking"
# Now safely create your .env
```

---

## Secrets in CI/CD

In GitHub Actions, store secrets in the repository or organisation settings and reference them in workflows:

**Store a secret:**

Go to **Repository Settings → Secrets and variables → Actions → New repository secret**.

Name: `DATABASE_URL`
Value: `postgresql://user:realpassword@host/db`

**Use in a workflow:**

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Deploy
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          STRIPE_KEY: ${{ secrets.STRIPE_KEY }}
        run: ./deploy.sh
```

Secrets are masked in logs - if a secret value appears in a log line, GitHub replaces it with `***`.

**Never echo secrets:**

```yaml
# WRONG - prints the secret in logs
- run: echo ${{ secrets.DATABASE_URL }}

# Also wrong - still logged even though indirect
- run: echo "$DATABASE_URL"
  env:
    DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

---

## GitHub Terms of Service - Key Restrictions

GitHub's ToS prohibits hosting:

- **Malware and exploits** - viruses, ransomware, spyware, rootkits, code designed to attack production systems without authorisation. Security research tools are acceptable when clearly documented as defensive.
- **Child sexual abuse material** (CSAM) - zero tolerance, immediate removal and referral to authorities
- **Content facilitating real-world violence** - targeted harassment, threats or material designed to help someone commit violence against a specific person
- **Spam** - repositories created primarily to boost SEO or distribute unsolicited commercial content
- **Cryptomining scripts** - code designed to be injected into websites to use visitor CPUs for mining without consent
- **Private information about individuals** - doxing, sharing personal information without consent

**GitHub actively enforces these rules** through automated detection, user reports and content moderation. Violations result in content removal, account suspension or both.

For the complete ToS: `docs.github.com/en/site-policy/github-terms/github-terms-of-service`

---

## Try It Yourself

**Set up a secure repository from scratch:**

**Step 1.** Create a new repository on GitHub (do not initialise it).

**Step 2.** Clone it and set up a proper `.gitignore` before anything else:

🪟 **Windows:**

```powershell
git clone https://github.com/YOUR_USERNAME/YOUR_REPO
cd YOUR_REPO
# Download a Python + Windows gitignore
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/github/gitignore/main/Python.gitignore" -OutFile .gitignore
Add-Content .gitignore "`n# Environment files`n.env`n.env.*`n!.env.example"
```

🍎 **Mac / 🐧 Linux:**

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO
cd YOUR_REPO
curl -o .gitignore https://raw.githubusercontent.com/github/gitignore/main/Python.gitignore
echo -e "\n# Environment files\n.env\n.env.*\n!.env.example" >> .gitignore
```

**Step 3.** Create a `.env.example` with placeholder values:

```bash
# .env.example
DATABASE_URL=postgresql://user:password@localhost/mydb
API_KEY=your-api-key-here
SECRET_KEY=your-secret-key-here
```

**Step 4.** Commit both files:

```bash
git add .gitignore .env.example
git commit -m "add: gitignore and env example"
git push
```

**Step 5.** Install gitleaks and test it:

```bash
# Create a test file with a fake "secret" to verify gitleaks works
echo 'STRIPE_KEY = "sk_live_testOnlyFakeKey1234567890"' > test_secret.py
gitleaks detect --source . --verbose
# gitleaks should flag this file
rm test_secret.py
```

---

## Common Mistakes

**"It is a private repository so it is safe."** Private repositories are safer than public ones but they are not a safe place for secrets. Rotate credentials that were committed to private repositories too.

**Removing the file in a new commit.** Creating a commit that deletes `secrets.json` removes the file from the working tree but it remains permanently in Git history. Anyone with access can still find it with `git log --all -- secrets.json` and `git show COMMIT_HASH:secrets.json`.

**Assuming GitHub secret scanning catches everything.** GitHub push protection covers many common formats but not all custom or uncommon credential formats. Do not rely solely on GitHub's scanning.

**Not revoking before cleaning history.** History rewriting takes time. During that time, the secret is still valid and exploitable. Revoke first, always.

**Forgetting to notify collaborators after history rewrite.** After force-pushing rewritten history, everyone's local clone is on a diverged branch. They need to re-clone or reset. If you do not tell them, they may push the old history back when they next push.

**Committing `node_modules/`, `__pycache__/` or `.venv/`.** These directories are often hundreds of megabytes, can contain executable code and should never be committed. They are fully reproducible from `package.json`/`package-lock.json` or `requirements.txt`/`Pipfile.lock`. If they are already committed, use `git rm -r --cached node_modules/` to untrack them.

**Not adding `.gitignore` before creating `.env`.** If you create `.env` before adding it to `.gitignore` and then run `git add .`, you may accidentally stage it. Always set up `.gitignore` first.

**Using `git commit --no-verify` to bypass pre-commit hooks.** Pre-commit hooks exist to protect you. Bypassing them because they are "annoying" defeats their purpose. Fix the false positive properly instead.

---

## Summary

- **Never commit:** API keys, passwords, tokens, private SSH keys, `.env` files, PII, proprietary code you do not own
- **Private repositories are not safe for secrets** - they can become public, be accessed by collaborators or be breached
- **Binary files and large assets** belong in Git LFS or attached to GitHub Releases, not in the Git object database
- **If you push a secret:** revoke it immediately (before anything else), then clean history with `git filter-repo` or BFG, then request a GitHub cache purge, then notify all collaborators to re-clone
- **Use `.gitignore` from day one** - set it up before your first commit, include `.env` and build outputs
- **Use the `.env` pattern** - real values in `.env` (never committed), structure in `.env.example` (always committed)
- **Install gitleaks** as a pre-commit hook to catch secrets before they are committed
- **Enable GitHub push protection** in repository and organisation settings
- Secrets in CI/CD go in GitHub Actions secrets, accessed as `${{ secrets.NAME }}` in workflows - never echoed in logs

---

## Sources and Further Reading

- [GitHub secret scanning documentation](https://docs.github.com/en/code-security/secret-scanning) - automatic detection and push protection
- [git-filter-repo](https://github.com/newren/git-filter-repo) - recommended tool for rewriting Git history
- [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) - fast history cleaning for large repositories
- [Gitleaks](https://github.com/gitleaks/gitleaks) - secret scanning for repositories and CI pipelines
- [detect-secrets by Yelp](https://github.com/Yelp/detect-secrets) - baseline-based secret detection
- [pre-commit framework](https://pre-commit.com) - multi-language pre-commit hook manager
- [gitignore.io](https://gitignore.io) - generate `.gitignore` for your OS, language and editor
- [github/gitignore](https://github.com/github/gitignore) - GitHub's official gitignore templates
- [GitHub Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service) - what is and is not allowed
- [Removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository) - GitHub's official guide

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

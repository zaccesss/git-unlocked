# What Should and Should Not Be on GitHub

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Putting the wrong things on GitHub can compromise your security, breach someone's privacy, violate licences or get your account suspended. This file explains what belongs on GitHub, what must never go there, and exactly how to fix it when something sensitive has already been pushed.

---

## Table of Contents

- [What GitHub is for](#what-github-is-for)
- [What should never go on GitHub](#what-should-never-go-on-github)
- [Secrets and credentials](#secrets-and-credentials)
- [Personal and private data](#personal-and-private-data)
- [Legal issues - licences and copyright](#legal-issues---licences-and-copyright)
- [Binary files and large assets](#binary-files-and-large-assets)
- [Platform-specific junk files](#platform-specific-junk-files)
- [What to do if you have already pushed something sensitive](#what-to-do-if-you-have-already-pushed-something-sensitive)
- [Protecting yourself going forward](#protecting-yourself-going-forward)
- [GitHub Terms of Service - key restrictions](#github-terms-of-service---key-restrictions)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What GitHub Is For

GitHub is designed for:

- Source code in any programming language
- Plain text files: Markdown, YAML, JSON, TOML, XML, CSV and similar formats
- Documentation
- Configuration files that do not contain secrets
- Scripts and automation
- Static assets at reasonable sizes (small images for documentation, SVG icons)
- Issue tracking, project planning and collaboration

GitHub is **not** designed for:

- Large binary files (use Git LFS or a dedicated asset store)
- Sensitive credentials or personal data
- Compiled binaries as the primary distribution method (use GitHub Releases for that)
- Storing backups of unrelated data

---

## What Should Never Go on GitHub

### Secrets and credentials

**Never push to any GitHub repository - public or private:**

- API keys (`sk-ant-...`, `AIza...`, `AKIA...` etc.)
- Database passwords and connection strings containing credentials
- Private SSH keys (files starting with `-----BEGIN ... PRIVATE KEY-----`)
- OAuth tokens and Personal Access Tokens
- Service account credentials (AWS, GCP, Azure)
- `.env` files containing real values
- `secrets.json` or `config.json` files with live credentials
- JWT signing secrets
- Stripe, PayPal or other payment API keys
- Twilio, SendGrid, Mailgun API keys
- Any file named `credentials`, `secrets` or `token` that contains real values

> [!CAUTION]
> Bots scan GitHub continuously for API keys. A secret pushed to a public repository is typically harvested within 30 seconds. Even private repositories can be compromised if the repository is ever made public or a collaborator account is breached. Treat all GitHub repositories as potentially public.

### Why private repositories are not safe enough

Private repositories protect secrets from casual browsing but not from:

- GitHub staff (who can access repositories for legal or safety reasons)
- Collaborators who can clone and store the repository
- Future repository visibility changes (if you accidentally make it public)
- GitHub security scanning which notifies GitHub of certain secret formats
- Account compromise - if your GitHub account is hacked, private repos are accessible

---

## Personal and Private Data

**Never push:**

- Full names combined with email addresses, phone numbers or physical addresses (PII)
- Medical records or health data
- Financial records
- Passport numbers, national insurance numbers, social security numbers or other government identifiers
- Biometric data
- Private communications (messages, emails)
- Data about identifiable individuals without their consent

Storing personal data on GitHub (especially in public repositories) may violate GDPR in the UK and EU, CCPA in California and other data protection laws.

---

## Legal Issues - Licences and Copyright

**Do not push code you do not have the right to distribute:**

- Proprietary source code from a previous employer or client (unless you have explicit written permission)
- Code licensed under terms that prohibit redistribution
- Third-party libraries not under an open source licence

**Do check your employment contract.** Many employment contracts include an IP assignment clause that gives your employer ownership of any code you write, including on personal time, using your own equipment. If your contract contains such a clause, personal projects may legally belong to your employer. Consult a lawyer if you are unsure.

**Do not mix licences incompatibly.** If you include GPL-licensed code in your project, your project may need to be GPL-licensed too. Check the licence of every dependency you vendor into your repository.

---

## Binary Files and Large Assets

Git is designed for text files. Binary files cause several problems:

- Every version of a binary file is stored in full - a 10 MB image changed 10 times stores 100 MB in Git history
- Binary diffs are meaningless - Git cannot show what changed inside a PNG, PSD or compiled binary
- Repositories with large binaries become slow to clone

**What to do instead:**

- **For images in documentation:** keep only small PNG/SVG files under 1 MB
- **For fonts, compiled assets and design files:** use Git LFS (Large File Storage)
- **For release binaries:** upload them to GitHub Releases as attached assets, not committed to the repository
- **For datasets:** use a dedicated data platform (Hugging Face, Kaggle, DVC) or cloud storage

**Git LFS:**

Git LFS stores large files outside the Git object database but tracks them with pointer files. See `git/22-large-repos.md` for full LFS setup instructions.

---

## Platform-Specific Junk Files

Some files belong on your machine and should never be committed to a shared repository. Add these to your `.gitignore` (or a global `~/.gitignore`).

**🪟 Windows:**

```
Thumbs.db
Desktop.ini
*.lnk
```

**🍎 Mac:**

```
.DS_Store
.AppleDouble
.LSOverride
```

**🐧 Linux:**

```
*~
.directory
```

**Editors and IDEs:**

```
.idea/           # JetBrains
.vscode/         # VS Code (commit .vscode/settings.json but not extensions.json if it contains personal recommendations)
*.suo
*.user
*.swp            # Vim swap files
```

**Build outputs:**

```
node_modules/
__pycache__/
*.pyc
dist/
build/
target/          # Rust, Java
bin/
obj/
```

**Environment and secrets:**

```
.env
.env.local
.env.production
*.pem
*.key
```

Use `gitignore.io` or `github.com/github/gitignore` to generate a complete `.gitignore` for your language and toolchain.

---

## What to Do If You Have Already Pushed Something Sensitive

If you have pushed a secret, API key or sensitive file to GitHub, treat it as compromised immediately regardless of whether you can remove it from Git history.

**Step 1 - Revoke the secret first.**

Before doing anything else:

- Rotate or revoke the API key, password or token in the service that issued it
- Generate a new one
- Update your application to use the new value

Even if you remove the file from Git history perfectly, someone may have already seen or copied it. Revoking it immediately limits the damage.

**Step 2 - Remove it from Git history.**

**Option A - If the push was very recent (same day) and the repository is not widely cloned:**

Use `git filter-repo` (the modern replacement for `filter-branch`):

```bash
pip install git-filter-repo --break-system-packages  # install once
git filter-repo --path path/to/secret-file --invert-paths
git push --force --all
git push --force --tags
```

This rewrites all commits that touched the file and force-pushes the rewritten history. All collaborators must re-clone the repository after this.

**Option B - Using BFG Repo Cleaner (faster for large histories):**

```bash
# Download BFG from https://rtyley.github.io/bfg-repo-cleaner/
java -jar bfg.jar --delete-files secret-file.env
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force --all
```

**Step 3 - Request GitHub cache purge.**

GitHub caches repository data. After rewriting history, contact GitHub Support at `support.github.com` to request a cache purge for your repository. Until the cache clears, some old content may still be findable.

**Step 4 - Audit access logs.**

Check the service dashboard for the compromised credential. Look for any API calls, logins or actions you did not make. Report suspicious activity to the service's security team.

> [!IMPORTANT]
> Removing a secret from Git history does not mean it was never seen. It only means it is no longer in the repository. Always revoke first, then clean history. In that order.

---

## Protecting Yourself Going Forward

**Use a `.gitignore` from day one.**

The moment you create a repository, add a `.gitignore` that excludes `.env` files, build outputs and platform junk.

**Use environment variables for all secrets.**

Store credentials in environment variables, not in files. Load them with `dotenv` (Node.js), `python-dotenv` (Python) or the equivalent for your language.

**Use a secrets manager for production.**

For deployed applications: AWS Secrets Manager, GCP Secret Manager, Azure Key Vault, HashiCorp Vault or similar. Never use environment variables on servers that are visible to all team members.

**Install a pre-commit hook with secret scanning.**

Tools like `gitleaks` or `detect-secrets` scan your staged changes before you commit and block commits that contain patterns that look like secrets.

```bash
# Install gitleaks
brew install gitleaks          # Mac
winget install gitleaks        # Windows
sudo apt install gitleaks      # Linux

# Run on your repository
gitleaks detect --source .

# Set up as a pre-commit hook
gitleaks protect --staged      # scan staged changes before commit
```

**Enable GitHub push protection.**

GitHub's built-in secret scanning can block pushes that contain known secret formats (AWS keys, GitHub tokens, Stripe keys and more). Enable it at repository level:

Go to **Settings → Code security → Push protection** and turn it on.

**Use a `.env.example` file.**

Commit a `.env.example` file that shows the structure of your environment variables with fake values. This documents what variables are needed without exposing any real values.

```bash
# .env.example
DATABASE_URL=postgresql://user:password@localhost/mydb
API_KEY=your-api-key-here
SECRET_KEY=your-secret-key-here
```

---

## GitHub Terms of Service - Key Restrictions

A GitHub repository cannot be used to host or distribute:

- Malware, ransomware, viruses or exploits targeting production systems
- Content that sexualises minors
- Content that facilitates real-world violence against specific individuals
- Spam or content created to artificially boost search rankings
- Cryptocurrency mining scripts (cryptojacking)

Full Terms of Service: `docs.github.com/en/site-policy/github-terms/github-terms-of-service`

Violations can result in repository removal, account suspension or both. GitHub actively enforces these rules.

---

## Try It Yourself

**Set up a safe repository from scratch:**

**Step 1.** Create a new repository on GitHub.

**Step 2.** Clone it locally.

**Step 3.** Before writing any code, create a `.gitignore`:

```bash
curl -o .gitignore https://raw.githubusercontent.com/github/gitignore/main/Node.gitignore
# Or for Python:
curl -o .gitignore https://raw.githubusercontent.com/github/gitignore/main/Python.gitignore
```

**Step 4.** Add `.env` to your `.gitignore` if it is not already there.

**Step 5.** Create a `.env.example` with fake placeholder values.

**Step 6.** Commit the `.gitignore` and `.env.example`:

```bash
git add .gitignore .env.example
git commit -m "add: gitignore and env example"
git push
```

**Step 7.** Install gitleaks and run a scan:

```bash
gitleaks detect --source .
```

---

## Common Mistakes

**"It is a private repository so it is safe."** Private repositories are safer but not completely safe. Revoke credentials that were pushed to private repositories too.

**Removing the file in a new commit.** Deleting a file in a new commit removes it from the working tree but it remains in Git history. Anyone with access can still find it with `git log` and `git show`.

**Assuming GitHub secret scanning catches everything.** GitHub push protection covers many common secret formats but not all. Do not rely solely on GitHub's scanning.

**Committing `node_modules/` or `venv/`.** These directories are often hundreds of megabytes, slow to push and pull and should never be committed. They are reproducible from `package.json` or `requirements.txt`.

**Not adding a `.gitignore` until after the first commit.** A `.gitignore` only prevents future tracking. Files already committed remain tracked. Use `git rm --cached filename` to untrack a file that is already in the repository.

---

## Summary

- **Never push:** API keys, passwords, tokens, private SSH keys, `.env` files, PII or proprietary code you do not own
- **Private repositories are not a safe place for secrets** - they can be made public, accessed by collaborators or breached
- **Binary files and large assets** should use Git LFS or be attached to GitHub Releases
- **If you push a secret:** revoke it immediately, then clean history with `git filter-repo` or BFG, then request a GitHub cache purge
- **Use `.gitignore` from day one** - add `.env` and build outputs before your first commit
- **Use environment variables and secrets managers** for credentials - never hardcode them
- **Install gitleaks** or `detect-secrets` as a pre-commit hook to catch secrets before they are committed
- **Enable GitHub push protection** to block pushes containing known secret formats

---

## Sources and Further Reading

- [GitHub secret scanning documentation](https://docs.github.com/en/code-security/secret-scanning) - automatic detection of secrets
- [git-filter-repo](https://github.com/newren/git-filter-repo) - the recommended tool for rewriting Git history
- [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) - fast history cleaning
- [Gitleaks](https://github.com/zricethezav/gitleaks) - secret scanning for repositories and CI
- [GitHub gitignore templates](https://github.com/github/gitignore) - community-maintained templates for every language
- [gitignore.io](https://gitignore.io) - generate a custom .gitignore by language and IDE
- [GitHub Terms of Service](https://docs.github.com/en/site-policy/github-terms/github-terms-of-service) - what is and is not allowed

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

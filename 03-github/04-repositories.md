# Repositories

**Difficulty:** 🟢 Beginner to 🟡 Intermediate | **Time:** 35 minutes

A repository is the fundamental unit of GitHub. Everything you do on GitHub happens inside a repository - your code lives there, your issues are tracked there, your pull requests are reviewed there and your Actions run there. Understanding repositories thoroughly - how to create them, configure them, protect them and manage them - is essential for using GitHub effectively.

---

## Table of Contents

- [What is a repository?](#what-is-a-repository)
- [Creating a repository](#creating-a-repository)
- [Repository visibility](#repository-visibility)
- [Repository settings overview](#repository-settings-overview)
- [The repository homepage](#the-repository-homepage)
- [Starring and watching](#starring-and-watching)
- [Repository topics](#repository-topics)
- [Template repositories](#template-repositories)
- [Pinning repositories to your profile](#pinning-repositories-to-your-profile)
- [Repository insights](#repository-insights)
- [Branch protection rules](#branch-protection-rules)
- [Rulesets](#rulesets)
- [CODEOWNERS](#codeowners)
- [Archiving a repository](#archiving-a-repository)
- [Deleting a repository](#deleting-a-repository)
- [Transferring a repository](#transferring-a-repository)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Repository?

A repository (repo) is a directory that Git tracks. On GitHub, a repository is that same directory stored on GitHub's servers, wrapped in a web interface with collaboration tools layered on top.

Every repository contains:
- Your project files and folders
- The complete Git history of every change ever made
- Branches, tags and releases
- Issues, pull requests and discussions (if enabled)
- A wiki (if enabled)
- Settings and configuration

A repository belongs to either a **personal account** or an **organisation**. Personal repositories live at `github.com/YOUR_USERNAME/REPO_NAME`. Organisation repositories live at `github.com/ORG_NAME/REPO_NAME`.

---

## Creating a Repository

**From the GitHub web interface:**

**Step 1.** Click the **+** icon in the top-right corner of any GitHub page and select **New repository**. Or go directly to [github.com/new](https://github.com/new).

**Step 2.** Fill in the repository details:

**Owner:** Choose between your personal account and any organisations you belong to.

**Repository name:** Must be unique within the owner's account. Can contain letters, numbers, hyphens and underscores. Maximum 100 characters. Choose a descriptive, lowercase, hyphenated name. Examples: `my-portfolio`, `weather-app`, `data-analysis-tools`.

**Description:** Optional but recommended. One or two sentences describing what the repository does. This appears in search results and on your profile.

**Visibility:** Public or Private (covered in the next section).

**Initialise this repository with:**

- **Add a README file:** Creates a `README.md` with the repository name as a heading. Recommended - without this, your repository starts empty and cloning it locally requires extra steps.
- **Add .gitignore:** Choose a template for your language or framework. GitHub provides templates for Python, Node, Java, Go, Ruby, C++, Unity, macOS and many more. Sourced from the `github/gitignore` repository.
- **Choose a licence:** Select from MIT, Apache 2.0, GPL v3, GPL v2, LGPL v3, AGPL v3, BSD 2-Clause, BSD 3-Clause, Mozilla Public License 2.0, Creative Commons Zero (CC0), Boost, Eclipse, Unlicense and more. For open source projects, MIT or Apache 2.0 are the most common choices.

**Step 3.** Click **Create repository**.

**From the GitHub CLI:**

```bash
gh repo create YOUR_REPO_NAME --public --clone
gh repo create YOUR_REPO_NAME --private --clone
gh repo create YOUR_ORG/YOUR_REPO_NAME --public
```

The `--clone` flag clones the new repository to your local machine immediately after creating it.

**From the command line (push an existing local repo):**

```bash
# Create the repo on GitHub first (without initialising)
gh repo create YOUR_REPO_NAME --public --source=. --push
```

Or manually:

```bash
git init
git add .
git commit -m "add: initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPO.git
git push -u origin main
```

---

## Repository Visibility

Every GitHub repository is either **public**, **private** or **internal** (organisations only).

### Public

- Visible to everyone on the internet without signing in
- Anyone can clone and download the code
- Anyone can open issues and pull requests (unless you restrict this)
- Only collaborators with write access can push changes
- GitHub's free security features (secret scanning, code scanning, push protection) are available automatically
- Required for GitHub Pages on the Free plan

**Use public repositories for:** Open source projects, portfolios, course projects, anything you want the world to see and contribute to.

### Private

- Visible only to you and collaborators you explicitly invite
- Does not appear in search results or your public profile's repository list
- Organisation owners always have access to private repos in their organisation
- Full access to all GitHub features (some require Pro or higher plan)
- Forks of a private repository are also private by default
- **Deleting a private repository also deletes all its forks**

**Use private repositories for:** Work projects, client code, personal projects you are not ready to share, repositories containing sensitive configuration.

> [!WARNING]
> Private does not mean secret. GitHub staff can access private repositories in certain circumstances (legal requirements, support requests). Never store passwords, API keys, certificates or personal data in any repository - public or private.

### Internal (Enterprise only)

Available on GitHub Enterprise Cloud and Server. Internal repositories are visible to all members of the enterprise but not to the public. Members can fork internal repositories to other internal or private locations within the enterprise. This supports "innersource" practices - applying open source collaboration patterns inside an organisation.

### Changing visibility

You can change a repository's visibility in **Settings → General → Danger Zone**.

**Public → Private consequences:**
- All existing stars and watchers are removed
- GitHub Pages is unpublished
- Public forks are detached and become independent public repositories
- Repository disappears from search results

**Private → Public consequences:**
- All code becomes visible to everyone immediately
- Commit history, issues and Actions logs become public
- Anyone can fork the repository
- If secrets were ever committed to the history, they are now exposed - even after deletion

> [!CAUTION]
> Before making a private repository public, check the **entire commit history** for secrets, API keys or personal information. Even deleted files remain in the Git history. Use `git log` and `git show` to inspect past commits, or run a secret scanning tool.

---

## Repository Settings Overview

Access repository settings from the **Settings** tab on any repository you own or have admin access to.

**General tab:**
- Repository name (rename - GitHub creates a redirect from the old URL)
- Default branch (change from `main` to any other branch)
- Template repository checkbox (makes this a template others can use)
- Features toggles: Wikis, Issues, Sponsorships, Discussions, Projects
- Pull request merge options: merge commits, squash merging, rebase merging
- Auto-merge: allow users to enable auto-merge on pull requests
- Automatically delete head branches after merge
- **Danger Zone:** change visibility, transfer ownership, archive, delete

**Collaborators and teams (or Access):**
- Add individual collaborators by username or email
- Permission levels: Read, Triage, Write, Maintain, Admin
- For organisation repositories: add teams with permissions
- Manage access for GitHub Apps

**Branches:**
- Default branch configuration
- Branch protection rules (see [Branch protection rules](#branch-protection-rules))

**Tags:**
- Tag protection rules (now superseded by Rulesets)

**Rules → Rulesets:**
- Modern replacement for branch protection rules
- Can target branches, tags and pushes

**Actions:**
- Enable or disable GitHub Actions for this repository
- Allow all actions, local actions only or specific external actions
- GITHUB_TOKEN permissions: read-only by default or read-write
- Artefact and log retention settings
- Runner groups and self-hosted runner management

**Webhooks:**
- Configure HTTP callbacks for repository events
- Useful for integrating with external services

**Environments:**
- Create named environments (staging, production etc.)
- Add protection rules, required reviewers and deployment branch restrictions

**Codespaces:**
- Dev container configuration
- Default machine type
- Prebuild configuration

**Pages:**
- Enable GitHub Pages, select source branch and directory
- Configure custom domain

**Security:**
- Enable Dependabot alerts, security updates and version updates
- Enable secret scanning and push protection
- Enable code scanning
- Private vulnerability reporting

**Deploy keys:**
- SSH keys scoped to this single repository for deployment pipelines

**Secrets and variables:**
- Repository-level secrets and variables for GitHub Actions

**Autolinks:**
- Map text patterns (like `JIRA-123`) to external URLs automatically

---

## The Repository Homepage

When you visit a repository on GitHub, the default view shows:

**The file browser:** Displays the repository contents at the current branch's HEAD. Click any file to view its contents with syntax highlighting. Click any folder to navigate into it.

**The README:** If a `README.md` (or `README.rst`, `README.txt`, `README.adoc`) exists in the root, it is rendered below the file browser. The README is the first thing visitors see - treat it as your project's front page.

**Repository metadata (right sidebar):**
- About section (description, website, topics)
- Releases
- Packages
- Contributors
- Languages bar
- Activity (commits, branches, tags)
- Stars and watchers count

**Branch selector:** Switch between branches and tags.

**Latest commit:** Shows the most recent commit message, hash and time for each file and folder.

**Clone button:** The green "Code" button provides HTTPS, SSH and GitHub CLI clone commands, plus the option to open in GitHub Desktop or Codespaces.

---

## Starring and Watching

### Stars

Starring a repository bookmarks it to your profile and signals interest to the maintainer. Stars influence GitHub's search ranking and trending lists. You can view all your starred repositories at [github.com/stars](https://github.com/stars) and organise them into named lists.

Stars do not create notifications. They are bookmarks, not subscriptions.

**Via GitHub CLI:**

```bash
gh repo star OWNER/REPO
gh repo unstar OWNER/REPO
```

### Watching

Watching subscribes you to notifications for activity in a repository. Options:

**Participating and @mentions** (recommended default): You only receive notifications for conversations you are directly involved in or where you are @mentioned.

**All Activity**: Every issue, pull request, commit comment, review, release and discussion generates a notification. Only suitable for repositories with very low activity.

**Ignore**: Suppresses all notifications even if you are @mentioned. Use for repositories that have auto-subscribed you.

**Custom**: Choose specific events to notify you - issues, pull requests, releases, discussions, security alerts.

You can watch up to **10,000 repositories**. GitHub automatically subscribes you to watch repositories you create, fork or are added as a collaborator to (at the "Participating" level).

---

## Repository Topics

Topics are short, lowercase, hyphenated tags that categorise your repository. They appear on the repository homepage and make the repository discoverable via GitHub's topic search.

**Add topics:** Click the gear icon next to "About" on the repository homepage → Topics field.

**Rules:** Lowercase only, hyphens for spaces, no special characters, maximum 20 topics per repository.

**Effective topics:** Use specific, well-known tags. `python`, `machine-learning`, `api`, `cli`, `tutorial`, `open-source`, `hacktoberfest` (for October events). GitHub may auto-suggest topics based on your repository's content.

Browse repositories by topic at `github.com/topics/TOPIC_NAME`. For example, `github.com/topics/python` or `github.com/topics/machine-learning`.

---

## Template Repositories

A template repository lets others create new repositories that start with your repository's files, structure and optionally branches - but with a clean, single-commit history and no link back to the original.

**Mark a repository as a template:**

Settings → General → Check "Template repository".

A **Use this template** button appears on the repository homepage. Clicking it opens a form to create a new repository with the template's contents.

**Template vs fork:**

| Feature | Template | Fork |
|---|---|---|
| History | Single fresh commit | Full original history |
| Link to original | None | Linked - can PR back |
| Files included | Root files and selected branches | All files and history |
| Git LFS files | Not supported | Supported |
| Use case | Starting a new independent project | Contributing to the original |

**Using a template via CLI:**

```bash
gh repo create MY_NEW_REPO --template OWNER/TEMPLATE_REPO
```

---

## Pinning Repositories to Your Profile

You can pin up to **6 repositories** to the top of your GitHub profile page. Pinned repositories appear prominently and are the first things visitors see when browsing your profile. Pin your best work, your most active projects or repositories that best represent your skills.

**To pin repositories:**

1. Go to your profile page at `github.com/YOUR_USERNAME`
2. Click **Customize your pins**
3. Select up to 6 repositories (your own repos or repos you have contributed to)
4. Click **Save pins**

Pinned repositories display their name, description, primary language and star/fork counts. You can drag them to reorder them.

---

## Repository Insights

Repository Insights provide analytics and statistics about your repository's activity. Access via the **Insights** tab on any repository.

### Traffic

Shows views, unique visitors, clones and popular content from the **past 14 days**. Requires write access to view.

- **Views:** How many times repository pages were loaded, with unique visitor count
- **Clones:** How many times the repository was cloned (via HTTPS, SSH or GitHub Desktop)
- **Referring sites:** Where visitors came from (Google, npm, Reddit etc.)
- **Popular content:** Which files and pages got the most views

### Contributors

Timeline of contributions from the top 100 contributors. Shows commits, additions and deletions per week. Helps understand who is most active and when contribution peaks occur.

### Commits

Commit frequency graph showing how activity fluctuates over time. Code frequency shows additions and deletions per week. Useful for identifying periods of heavy activity and quiet periods.

### Dependency graph

Lists all dependencies your repository relies on, with version numbers, licences and known vulnerabilities. Requires the repository to have a recognised dependency manifest (`package.json`, `requirements.txt`, `Gemfile`, `go.mod`, `pom.xml`, `Cargo.toml` etc.).

The dependency graph also shows **dependents** - repositories that depend on your repository. Important for maintainers of libraries and frameworks.

### Network graph

Visualises the fork network - all forks of the repository and their branches. Useful for seeing how a project has diverged across forks.

### Forks

Lists all repositories that have forked yours, with filtering by activity level (active, inactive, starred, archived). Useful for discovering who is using and building on your project.

---

## Branch Protection Rules

Branch protection rules prevent direct pushes to important branches and enforce quality checks before code can be merged. They are configured per-branch pattern in **Settings → Branches → Add branch ruleset**.

**Available protections:**

**Require a pull request before merging:** No one can push directly to this branch. All changes must go through a pull request. Sub-options:
- Required approvals (1-6 reviewers must approve before merge)
- Dismiss stale pull request approvals when new commits are pushed
- Require review from code owners
- Require approval of the most recent reviewable push
- Require conversation resolution before merging

**Require status checks to pass before merging:** Specific CI checks must succeed. Add check names from your Actions workflows or other status providers.

- Require branches to be up to date before merging

**Require signed commits:** All commits to this branch must have verified GPG or SSH signatures.

**Require linear history:** No merge commits allowed - only squash merges or rebase merges. Keeps history clean and linear.

**Require merge queue:** Changes must join a queue and are merged in order after all checks pass. Prevents race conditions in high-throughput repositories.

**Require deployments to succeed before merging:** A deployment to a specific environment must succeed before the pull request can be merged.

**Lock branch:** Make the branch read-only. No one can push even with admin access (unless you bypass it explicitly).

**Do not allow bypassing the above settings:** When checked, even repository administrators must follow the rules.

**Restrict who can push to matching branches:** Whitelist specific users, teams or apps allowed to push.

**Allow force pushes:** Normally blocked on protected branches. Enabling this defeats most of the protection.

**Allow deletions:** Normally blocked on protected branches. Enabling allows branch deletion.

---

## Rulesets

Rulesets are the modern successor to branch protection rules, introduced in 2023. They offer significant advantages over branch protection rules and GitHub is moving toward rulesets as the standard approach.

**Key advantages of rulesets over branch protection rules:**

- **Multiple rulesets can apply simultaneously.** The most restrictive setting wins when rulesets conflict.
- **Organisation-level rulesets** apply across multiple repositories without configuring each one.
- **Target branches AND tags** - branch protection only covers branches.
- **Apply before branches or tags exist** - proactive protection.
- **Push rulesets** restrict pushes across the entire fork network.
- **Evaluate mode** for dry-run testing before enforcing.
- **Additional rules** not available in branch protection: maximum file size, restricted file paths, file extensions, branch naming conventions and required metadata in commit messages.
- **Bypass lists** with granular control over who can bypass which rules.

**Creating a ruleset:**

Settings → Rules → Rulesets → New ruleset.

Choose the target: **branch**, **tag** or **push**.

Configure:
- **Enforcement status:** Active, Evaluate (dry run) or Disabled
- **Bypass list:** Specific roles, teams or apps that can bypass the ruleset
- **Target branches/tags:** Name patterns (e.g., `main`, `release/*`, `v*`)
- **Rules:** Select from the available rule types

> [!TIP]
> If you have existing branch protection rules, you do not need to migrate immediately - both systems work simultaneously. Start using rulesets for new configurations and migrate existing rules over time.

---

## CODEOWNERS

A `CODEOWNERS` file defines who is responsible for code in specific parts of your repository. When a pull request modifies files matching a CODEOWNERS pattern, the designated owners are **automatically added as required reviewers**.

**Location:** Place in `.github/CODEOWNERS`, the repository root or `docs/`. GitHub uses the first file found in that priority order.

**Syntax:**

```
# Each line: pattern followed by owners (GitHub usernames or teams)

# Global owners - own everything by default
* @YOUR_USERNAME @another-user

# Specific directory
/docs/ @YOUR_USERNAME

# Specific file type
*.js @YOUR_USERNAME

# Specific subdirectory
/backend/ @YOUR_ORG/backend-team

# Last matching pattern takes precedence
# So more specific patterns should come after general ones
```

**Requirements for CODEOWNERS to require reviews:**

1. The repository must have branch protection rules or rulesets that require code owner review
2. The code owner must have read access to the repository at minimum
3. For organisation-owned repositories, code owners must be organisation members

**Team syntax:** `@YOUR_ORG/team-name` adds all members of that team as code owners.

CODEOWNERS works in both public and private repositories. For private repositories, code owners must be collaborators. For public repositories, they must have explicit access.

---

## Archiving a Repository

Archiving makes a repository permanently read-only for all users, including administrators. No new commits, pushes, issues, pull requests, comments, labels, milestones or wiki pages can be created or modified.

**When to archive:**

- A project is completed and no longer actively developed but should remain accessible for reference
- A deprecated library that you want to keep visible but clearly mark as inactive
- A competition or hackathon project that is done

**How to archive:**

Settings → General → Danger Zone → Archive this repository → Confirm.

Archived repositories display a grey banner: "This repository has been archived by the owner."

**Archiving is reversible.** You can unarchive at any time via the same Danger Zone section.

---

## Deleting a Repository

Deleting permanently removes the repository and all its data from GitHub. This cannot be undone after 90 days.

> [!CAUTION]
> GitHub retains deleted repositories for **90 days** and they can be restored within that window via GitHub Support. After 90 days, deletion is permanent and unrecoverable.

**Consequences of deletion:**

- All code, history, branches, tags, issues, pull requests, wiki and releases are deleted
- Public forks are **detached** and become independent repositories (they are not deleted)
- Private forks **are deleted** along with the parent
- GitHub Pages is unpublished
- Actions workflows stop running
- All webhooks are removed
- The repository name becomes available for reuse

**How to delete:**

Settings → General → Danger Zone → Delete this repository → Type the repository name to confirm → Click the confirmation button.

**You must be the repository owner or an organisation admin to delete a repository.**

---

## Transferring a Repository

You can transfer a repository to another user account or organisation. Reasons to transfer include: moving a personal project to an organisation, transferring ownership when leaving a team or donating a project to another maintainer.

**What transfers:**

- All code, history, branches and tags
- Issues and pull requests
- Releases and tags
- GitHub Pages configuration
- Stars count

**What does not transfer:**

- Collaborator access (must be re-invited by new owner)
- Webhooks (URLs may no longer work)
- GitHub Apps access

**How to transfer:**

Settings → General → Danger Zone → Transfer ownership → Enter new owner's username or organisation name → Confirm.

GitHub creates a redirect from the old URL to the new one. This redirect lasts indefinitely but can be overridden if the old name is reused.

---

## Try It Yourself

**Step 1.** Create a new public repository:

1. Go to [github.com/new](https://github.com/new)
2. Name it `hello-github` or any name you prefer
3. Set it to **Public**
4. Check **Add a README file**
5. Select a `.gitignore` template for your preferred language
6. Choose the **MIT licence**
7. Click **Create repository**

**Step 2.** Explore the repository homepage:

- Look at the file browser
- Read the auto-generated README
- Check the right sidebar - About, Releases, Packages, Contributors, Languages

**Step 3.** Add a description and topics:

- Click the gear icon next to **About** on the right sidebar
- Add a short description
- Add 3 topics relevant to your project (e.g., `tutorial`, `beginner`, your language)
- Click **Save changes**

**Step 4.** Explore Settings:

- Click **Settings** at the top of the repository
- Browse through General, Branches, Secrets and variables, Pages
- Notice what each section controls

**Step 5.** Clone the repository to your local machine:

**Windows (PowerShell):**

```powershell
gh repo clone YOUR_USERNAME/hello-github
```

Or using Git directly:

```powershell
git clone https://github.com/YOUR_USERNAME/hello-github.git
```

**Mac and Linux:**

```bash
gh repo clone YOUR_USERNAME/hello-github
```

Or:

```bash
git clone https://github.com/YOUR_USERNAME/hello-github.git
```

**Step 6.** Make a change locally and push it:

```bash
cd hello-github
echo "My first GitHub repository" >> README.md
git add README.md
git commit -m "update: add description to README"
git push
```

**Step 7.** Refresh the repository on GitHub. Your change should appear.

**Step 8.** Pin the repository to your profile:

1. Go to `github.com/YOUR_USERNAME`
2. Click **Customize your pins**
3. Select `hello-github`
4. Click **Save pins**

---

## Common Mistakes

**Not adding a README when creating the repository.**

An empty repository is awkward to clone and has nothing to show visitors. Always initialise with a README. If you forgot, GitHub prompts you to create one on the repository homepage when it is empty.

**Using a vague or unprofessional repository name.**

Repository names appear in URLs and on your profile. `project1`, `test`, `untitled` and `asdfgh` look unprofessional. Use descriptive lowercase names: `weather-dashboard`, `portfolio-site`, `python-data-tools`.

**Making private repositories public without checking the history.**

If you committed an API key, password or personal data to a private repository, making it public exposes that data to the world - even if you deleted the file in a later commit. The Git history retains deleted files. Audit your history before changing visibility.

**Confusing stars and watches.**

Stars are bookmarks. Watches are notification subscriptions. Starring a repository does not notify you when it is updated. If you want updates, watch it.

**Deleting a repository when archiving would do.**

If you want to stop actively working on a project but keep it visible and accessible, archive it rather than delete it. Deletion is permanent after 90 days.

**Not using branch protection on main.**

Many beginners work directly on `main` and push commits without any review. This is fine for solo projects but causes problems in teams. Add branch protection early - require at least one review before merging even on personal projects you share with others.

**Ignoring CODEOWNERS.**

In a team setting, CODEOWNERS ensures the right people review changes to sensitive parts of the codebase. Without it, a junior developer could unknowingly merge a change to the payment system without any specialist review.

---

## Summary

- A repository is your project's home on GitHub - code, history, issues, pull requests and settings all live there
- Create repositories at github.com/new with a README, `.gitignore` and licence
- **Public** repositories are visible to everyone; **private** repositories are visible only to collaborators
- Never make a private repository public without checking the history for secrets
- Repository Settings covers features, collaborators, branch rules, Actions, webhooks, environments and security
- Star repositories to bookmark them; watch to subscribe to notifications
- Add topics to make your repository discoverable via GitHub search
- Branch protection rules and Rulesets enforce code quality gates on important branches
- CODEOWNERS automatically requests reviews from designated owners when matching files change
- Archive repositories that are complete but should remain visible; delete only when you want permanent removal
- GitHub retains deleted repositories for 90 days - contact Support to restore within that window

---

## Sources and Further Reading

- [About repositories - GitHub Docs](https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories) - official reference for all repository concepts
- [Branch protection rules - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches) - full guide to all protection options
- [About rulesets - GitHub Docs](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets) - rulesets reference and comparison with branch protection
- [About code owners - GitHub Docs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) - CODEOWNERS syntax and requirements
- [Repository settings reference - GitHub Docs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features) - complete settings reference
- [github/gitignore](https://github.com/github/gitignore) - collection of .gitignore templates for every language and framework

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# Changelog

All notable changes to git-unlocked are documented here.

This project follows [Semantic Versioning](https://semver.org). The format is based on [Keep a Changelog](https://keepachangelog.com).

---

## Version format

```
MAJOR.MINOR.PATCH

MAJOR - significant restructure or complete section overhaul
MINOR - new files, new sections or meaningful content additions
PATCH - typo fixes, small corrections, link updates
```

| Tag       | Meaning                                 |
| --------- | --------------------------------------- |
| `Added`   | New files or sections                   |
| `Updated` | Existing content improved or expanded   |
| `Fixed`   | Typos, errors or broken links corrected |
| `Removed` | Content removed                         |
| `Renamed` | Files or folders renamed                |

---

## [Unreleased]

In progress or planned - not yet in a release.

- All `terminal/` folder content
- All `real-world/` folder content
- All `reference/` folder content
- All `resources/` folder content
- `first-contribution/` sandbox
- `assets/` banner and diagrams
- `introduction/02-how-to-use-this-course.md` - full rewrite once all folders complete
- `introduction/04-version-control-concepts.md` - new file explaining what version control is before Git specifics
- Codespaces dev container setup
- Root-level setup documentation

---

## [1.0.0] - 2026-04-20

### Added

- `ides-and-editors/00-ides-overview.md` - the three generations of Git tooling (classic, AI-assisted, agentic), the VS Code fork family table, full file index table with platform coverage, how-to-choose guide, integrated terminal shortcut reference across all editors, exercises
- `ides-and-editors/01-vs-code.md` - complete VS Code Git coverage: Source Control panel in full detail (staging at file/hunk/line level, 3-way merge editor, Source Control Graph, Timeline view, inline blame, gutter indicators), GitLens (free Community vs Pro features, Commit Graph, Worktrees, Cloud Patches, Launchpad, Home view, pricing), GitHub Pull Requests extension (PR creation, in-editor review, in-editor commenting, issue hover cards), GitLab Workflow extension (MR creation, pipeline status, Duo Chat, Code Suggestions), GitHub Copilot (model picker including Claude Sonnet 4.6 and GPT-5, Agent mode, Coding Agent, commit message generation, merge conflict AI resolution, pricing tiers), GitLab Duo in VS Code (Code Suggestions, limitations vs Copilot), all keyboard shortcuts
- `ides-and-editors/02-jetbrains.md` - complete JetBrains Git coverage across IntelliJ IDEA, PyCharm, WebStorm, Rider, GoLand, CLion: Git menu and VCS Operations popup, Commit tool window with pre-commit checks and AI commit messages, changelists explained in full (JetBrains-exclusive concept, how they map to git staging at commit time), Git Staging mode toggle, shelving vs stashing (when to use each, how they differ), Log tab with full filtering and right-click actions, interactive rebase editor (drag-to-reorder, squash/fixup/reword/drop), three-pane merge tool with Magic Resolve and AI-assisted resolution, Annotate with Git Blame, partial commits, GitHub integration (bundled plugin), GitLab integration (official plugin), JetBrains AI Assistant (credits model, pricing), Android Studio (inherits full JetBrains Git + Gemini AI), 2025.3 unified IntelliJ Community licence change, all keyboard shortcuts
- `ides-and-editors/03-neovim.md` - complete Neovim Git stack: lazy.nvim (bootstrap snippet, lockfile, all commands), vim-fugitive in full (all :Git/:G commands, status buffer keybindings, Gdiffsplit 3-way merge, GBrowse), gitsigns.nvim (all signs, hunk operations, virtual-text blame, full keymap setup code), neogit (Magit-style porcelain, popup-driven interface), diffview.nvim (:DiffviewOpen, :DiffviewFileHistory, 4-pane merge layout), lazygit.nvim (floating terminal), recommended complete git.lua plugin config, daily workflow walkthrough, config file paths on all platforms, classic Vim differences (vim-gitgutter, vim-plug)
- `ides-and-editors/04-github-desktop.md` - full GitHub Desktop v3.5.x coverage: install on Windows/Mac/Linux (shiftkey fork), checkbox-based staging model, line-level staging, image diffs (2-up/swipe/onion-skin/difference), Copilot commit message generation (v3.5.0, GA June 2025), multi-domain auth, filterable changes, cherry-pick via drag-and-drop, one-slot stash with limitation noted, merge conflict resolution with external editor integration, Git hooks support (fixed in v3.5.5 Feb 2026), pull request creation and checkout, forking workflow, all keyboard shortcuts
- `ides-and-editors/05-cursor.md` - complete Cursor coverage: Anysphere history (founding, funding to $29.3B Series D Nov 2025), VS Code fork architecture, all inherited Git features, AI commit message generation, Cursor Blame (unique feature - human/Tab/Agent attribution with conversation links, added v2.4), Composer vs Agent mode, autonomous Git workflows in Agent mode (branch creation, staging, committing, rebasing, PR description generation), Background Agents (May 2025) and Cloud Agents with Computer Use (Feb 2026, 35% of Cursor's own PRs), Cursor 3 Agents Window (April 2026), .cursor/rules/*.mdc format with full Git conventions example, credit-based pricing table (Hobby/Pro $20/Pro+ $60/Ultra $200/Business $40)
- `ides-and-editors/06-windsurf.md` - complete Windsurf coverage: full ownership history (Codeium → OpenAI deal collapse → Google $2.4B talent deal July 2025 → Cognition AI ~$250M acquisition), Cascade Code vs Chat modes, Flow awareness in depth (tracks edits/terminal/clipboard/navigation automatically), Planning mode and Checkpoints, AI-driven Git workflows, Wave 13 (Dec 2025) Git worktrees and Arena Mode parallel agents, Windsurf 2.0 (April 2026) Agent Command Center + Spaces + Devin integration, Memories system, .windsurfrules with full Git conventions example, Cascade dedicated terminal, pricing ($15/month Pro)
- `ides-and-editors/07-antigravity.md` - complete Google Antigravity coverage: origin story (ex-Windsurf team Google hired for $2.4B, shipped in 4 months alongside Gemini 3, Nov 2025), dual-view Editor/Manager architecture, Artifacts system (task.md/implementation_plan.md/git_operations.md/screenshots/walkthrough.md) as audit trail for all agent Git actions, Google Docs-style commenting on Artifacts, multi-model support (Gemini 3.1 Pro, Claude Opus 4.6, Claude Sonnet 4.6, GPT-OSS-120B), parallel agents with Manager view, free public preview, honest coverage of stability issues (context errors, quota tightening, March 2026 lockout incident, security vulnerabilities), macOS sandbox (Feb 2026)
- `ides-and-editors/08-kiro.md` - complete AWS Kiro coverage: history (preview July 2025, GA Nov 17 2025 at re:Invent, Delta Air Lines 94% satisfaction), Code-OSS vs VS Code distinction (uses OpenVSX not VS Code Marketplace), spec-driven development explained in full (requirements.md/design.md/tasks.md with real YAML/Markdown examples), specs committed to Git branches (reviewable in PRs), the Hooks system (YAML format, Agent Prompt vs Shell Command actions), complete Git event hooks table (branch_created/branch_switched/pre_commit/commit_created/pull_request_opened/review_requested/merge_completed/rebase_completed + external events), GitHub Autonomous Agent (kiro label / /kiro comment / /kiro fix, sandbox network policies, co-authored commits, what it does not do), steering files with full tech.md example enforcing Git conventions, Kiro Powers contextual MCP loading, Kiro CLI for CI/CD, OpenVSX extension availability notes, pricing (Free 50 credits/Pro $20/Pro+ $40/Power $200)
- `ides-and-editors/09-zed.md` - complete Zed coverage: GPUI architecture (not Electron, Metal/Vulkan/DirectX11), Windows support added 2026, created by ex-Atom team (Nathan Sobo, Max Brunsfeld - Tree-sitter creator), native Git launched March 2025 (v0.177), Git panel with badges and file icons (v0.229), Diff View unique architecture (deleted text navigable, interleaved staging), hunk staging with Cmd+Y, split diffs default from v0.224, AI commit message generation (configurable model), inline blame, conflict resolution with AI option, multiplayer Co-Authored-By auto-trailers, Zeta2 edit prediction model (April 7 2026), GitHub Copilot from Feb 2026, ~700 WASM extensions, BYOK pricing model
- `ides-and-editors/10-helix.md` - complete Helix coverage: history (Blaž Hrastnik, modal + Kakoune-inspired, Rust, no plugin system by design), stable 25.07.1, Steel/Scheme plugin system still unmerged April 2026, built-in Git (gutter indicators + branch name only), the deliberate minimalism philosophy explained, lazygit install on all platforms, tmux popup integration with exact TOML keybinding config, WezTerm pane integration with shell script, :sh for one-off commands, core.editor = hx for interactive rebase, ]g/[g hunk navigation, known issues with inline :sh lazygit approach, gitui/tig/git-cola as alternatives
- `ides-and-editors/11-sublime-text.md` - complete Sublime Text + GitSavvy coverage: history (Jon Skinner, Sublime HQ), Package Control install, zero default keybindings explained, full status dashboard key reference (s/u/d/l/=/c/C/f/p/P/g/b/t/ss/sa/sp/sd), hunk/line staging with selection, commit view as full Sublime buffer with diff below, Repo History ASCII graph with [r] interactive rebase menu (reword/squash/fixup/drop/autosquash, [N]/[P] reflog walk), File History and Line History (the "wtf?" command), blame view, branching Command Palette commands, stashing two-key combos, GitHub github: commands (open on remote, create PR, checkout PR), Terminus for integrated terminal, Sublime Merge companion app (LFS lock/unlock April 2026), recommended keybindings JSON, pricing ($99 one-time)
- `ides-and-editors/12-other-editors.md` - coverage of 8 additional editors: Emacs + Magit (region-aware staging, transient menus, s/u/k/Tab/cc/ca/cw/cf/cs/ri/zz keybindings, Forge for GitHub/GitLab PR management), Visual Studio 2026 (Git Changes + Git Repository windows, interactive rebase UI, PR review preview, Azure DevOps integration, Copilot Self-Review, pricing), Xcode 26 (Source Control Navigator, Integrate menu full command list, blame popover, PAT authentication, AI via Claude and ChatGPT, MCP in Xcode 26.3), Android Studio (full JetBrains Git + Gemini AI), Eclipse + EGit (Git Staging drag-and-drop, History/Reflog/Synchronize/Compare views, interactive rebase, GPG commits, honest decline assessment), Nova (macOS-only, Show Last Change for Line, $99), Vim classic (vim-fugitive identical, vim-gitgutter, native packages install), nano and Notepad++ (minimal coverage)
- `ides-and-editors/13-all-ides-features-reference.md` - complete reference file with 7 comparison tables: platform support (20 tools), Git feature comparison (20 tools × 15 features split across two tables), AI Git features comparison (8 tools × 10 AI features), generation and philosophy summary (20 tools), pricing at a glance (20 tools), extension ecosystems (registries, counts, key extensions), integrated terminal and GIT_EDITOR support (18 tools), quick decision guide (12 use-case scenarios → recommended tool), keyboard shortcuts for 4 core Git operations across all editors

### Renamed

- `ides/` folder renamed to `ides-and-editors/` to more accurately reflect the full scope of tools covered

### Updated

- `README.md` - IDEs and Editors section updated with correct folder name `ides-and-editors/`, full 14-file table, "In progress" note removed (section now complete), Learning Path updated
- `ROADMAP.md` - version bumped to v1.0.0, `ides-and-editors/` marked as Complete with full 14-file checklist, `terminal/` marked as Next, current status table updated

---

## [0.9.0] - 2026-04-09

### Added

- `other-platforms/codeberg/00-codeberg-overview.md` - folder navigation, Forgejo relationship, OS coverage note, prerequisites
- `other-platforms/codeberg/01-what-is-codeberg.md` - Codeberg e.V. (German non-profit e.V. structure, democratic membership, no investor pressure), history (Gitea hosting → Forgejo stewardship after 2022 governance dispute), how it differs from GitHub/GitLab.com (governance/data/sustainability model/AI features/scale), what Codeberg provides (free repos, Pages, Woodpecker CI, package registry, federation), who uses it (free software advocates, privacy-conscious devs, European public sector, NLnet-funded projects), limitations (smaller ecosystem, CI friction, no SLA, no private org repos on free tier)
- `other-platforms/codeberg/02-creating-an-account.md` - registration at codeberg.org (hCaptcha, email verification), username considerations (federated identity implications), profile settings, 2FA setup (Aegis recommended, recovery codes), SSH key generation on all three OS, access token creation with scopes, federated identity (`@username@codeberg.org`), ActivityPub discoverability, Codeberg-specific limits, supporting Codeberg financially
- `other-platforms/codeberg/03-repositories-and-code-review.md` - creating repositories, HTTPS and SSH clone, importing from GitHub (full migration with issues/PRs/labels/milestones), git push --mirror for code-only migrations, pull mirrors, push mirrors to GitHub, pull request workflow, code review (inline comments, suggestions, review submission), branch protection, PR templates, topics and discoverability, wikis, Codeberg-specific limits (storage fair use, private org repos)
- `other-platforms/codeberg/04-issues-and-projects.md` - issue management for public open source projects (triage patterns, duplicate tracking, upstream labels, stale management), labels setup (type/status/difficulty sets, `good first issue` for onboarding), milestones for release planning, issue templates (bug report and feature request with front matter), security vulnerability template, Kanban project boards, contribution workflows (fork-and-PR model, CONTRIBUTING.md importance, good first issues), cross-repository references, federation and issues
- `other-platforms/codeberg/05-codeberg-ci.md` - Woodpecker CI overview (what it is, community Drone fork, comparison with GitHub Actions and Forgejo Actions), connecting to ci.codeberg.org (OAuth2), enabling repositories, `.woodpecker.yml` syntax (steps, image, commands, settings), plugins (Docker build/push, SSH deploy, Telegram), volumes and caching, triggers (push/pull_request/tag/cron/manual, per-step when conditions, branch filters), environment variables and secrets (from_secret, built-in CI variables), matrix builds, services (Docker sidecars for databases), practical pipeline examples (Node.js, Python with PostgreSQL, Go multi-platform Docker, release with binaries), self-hosted Woodpecker agents (Docker Compose setup, OAuth2 app registration)
- `other-platforms/codeberg/06-pages.md` - how Codeberg Pages works (pages branch, automatic serving), basic setup (orphan branch, static files), URL structure (username.codeberg.page for pages repo, /repo-name sub-path), deploy from Woodpecker CI, custom domains (.domains file, CNAME DNS setup, Let's Encrypt auto-HTTPS), static site generators (Hugo, MkDocs, Eleventy with full CI pipelines), SPA support (404.html trick for client-side routing), limitations (public repos only, exact `pages` branch name required, no server-side code, fair use storage)
- `other-platforms/codeberg/07-packages-and-releases.md` - package registry (23+ Forgejo formats, URL pattern), container images (login/push/pull, Kubernetes imagePullSecret), npm packages (.npmrc configuration, scoped packages), PyPI packages (twine upload, pip.conf), generic packages (curl PUT/GET), publishing from Woodpecker CI (Docker, Python, npm pipelines), releases (web UI creation, release description best practices, API creation), publishing release assets from CI (Go multi-platform pipeline, upload script), Codeberg-specific storage considerations and fair use etiquette
- `other-platforms/codeberg/08-organisations-and-teams.md` - creating organisations (permanent URL slug, federated identity), organisation settings, members and roles (Owner vs Member), teams (creating, permission levels: None/Read/Write/Admin, all repos vs specific repos), common team structures for open source, service account teams for CI, organisation repositories, transferring repositories to orgs, forks and contributor workflows, organisation-level features (shared labels, webhooks, packages, Woodpecker CI org secrets), Codeberg considerations (private repos require financial support, bot accounts allowed, organisation name and federated identity)
- `other-platforms/codeberg/09-all-codeberg-features-reference.md` - complete feature reference tables (account, repos, code review, issues, Woodpecker CI, Codeberg Pages, packages/releases, organisations, authentication, federation, API), Codeberg vs GitHub vs GitLab.com quick comparison, quick navigation

### Updated

- `CHANGELOG.md` - added v0.9.0 entry
- `ROADMAP.md` - marked other-platforms/codeberg/ as complete, updated current status to v0.9.0

---

## [0.8.0] - 2026-04-09

### Added

- `other-platforms/forgejo/00-forgejo-overview.md` - folder navigation, relationship with Gitea and Codeberg, OS coverage note (Linux/Mac only)
- `other-platforms/forgejo/01-what-is-forgejo.md` - the 2022 Gitea governance dispute (48-contributor open letter, domain transfer without notice), Codeberg e.V. as steward, soft fork (December 2022) to hard fork (February 2024) timeline, GPLv3+ licence change and what it means for self-hosters vs distributors, Forgejo in 2026 (v9.0.x), ActivityPub federation (ForgeFed protocol, ForgeFed working group), who uses Forgejo (SAML requirement, free software advocates, federation interest), key concepts (Forgejo runner, app.ini, .forgejo/workflows)
- `other-platforms/forgejo/02-installation.md` - binary install on Linux and macOS (download from codeberg.org/forgejo/forgejo/releases), Docker and Docker Compose (codeberg.org/forgejo/forgejo image, FORGEJO__ env prefix), system requirements, installation wizard, app.ini configuration, PostgreSQL database setup, Nginx and Caddy reverse proxy, systemd service, Forgejo runner setup (download from codeberg.org/forgejo/runner, register with token, daemon start), upgrading
- `other-platforms/forgejo/03-creating-an-account.md` - registration, 2FA, SSH keys, access tokens, SAML sign-in (Forgejo exclusive free feature: Okta/Azure AD/Keycloak/Google Workspace configuration, SP metadata URLs, ACS URL, IdP-specific setup, safe testing procedure), OAuth2 providers, account security checklist
- `other-platforms/forgejo/04-repositories-and-code-review.md` - creating repos, clone via HTTPS/SSH, mirroring and migration, pull requests, code review (inline comments, suggestions, review submission), merge strategies, protected branches, releases, Git LFS, visibility levels (public/private/internal), forking
- `other-platforms/forgejo/05-forgejo-actions.md` - enabling Actions (app.ini + repository level), .forgejo/workflows/ file location (also accepts .github/ and .gitea/), triggers, jobs and steps, runners and labels, the Forgejo runner vs act_runner distinction, secrets and variables (FORGEJO_TOKEN automatic secret), artefacts, caching, matrix builds, actions from external sources (DEFAULT_ACTIONS_URL options for air-gapped), practical examples (Node.js, Python, Docker, release binary pipeline)
- `other-platforms/forgejo/06-issues-packages-and-federation.md` - issues (identical to Gitea), labels and milestones, issue templates (.forgejo/ISSUE_TEMPLATE/), Kanban project boards, package registry (all 23+ formats, identical to Gitea with forgejo.example.com URLs), ActivityPub federation (what it enables: federated identity, following repos across instances, cross-instance notifications; current status 2026; enabling with [federation] ENABLED=true; blocking instances; ForgeFed long-term vision)
- `other-platforms/forgejo/07-administration.md` - admin panel, user management (create/suspend/delete/impersonate), global 2FA enforcement, LDAP configuration (Active Directory specifics, user filter), SAML 2.0 (generating certificates, app.ini configuration, SP URLs for Okta/Azure AD/Keycloak/Google Workspace, safe testing), OAuth2 providers, email configuration, federation administration, instance settings, maintenance CLI commands, backup and restore (database + work directory + SAML certs), Prometheus metrics
- `other-platforms/forgejo/08-api-and-integrations.md` - REST API (compatible with Gitea for core operations), authentication, core endpoints (repos/issues/PRs/users/orgs/actions secrets/releases), ActivityPub API endpoints (NodeInfo, WebFinger, user actor, repository actor, follow API), webhooks (X-Gitea-Signature header for backward compatibility), Renovate bot (platform: gitea works), Woodpecker CI (native Forgejo support, Docker Compose), Terraform provider, ArgoCD, rate limits, pagination
- `other-platforms/forgejo/09-all-forgejo-features-reference.md` - complete feature reference tables covering all Forgejo features, Forgejo vs Gitea comparison, ActivityPub federation status, SAML free tier comparison

### Updated

- `CHANGELOG.md` - added v0.8.0 entry
- `ROADMAP.md` - marked other-platforms/forgejo/ as complete, updated current status to v0.8.0

---

## [0.7.0] - 2026-04-09

### Added

- `other-platforms/gitea/00-gitea-overview.md` through `other-platforms/gitea/12-all-gitea-features-reference.md` - complete Gitea coverage (13 files): history and 2022 governance dispute, installation on all three OS (binary, Docker, packages), account setup and 2FA, repositories and mirroring, pull requests and code review, Gitea Actions (act_runner, full YAML guide), issues and projects, package registry (23 formats), administration (LDAP, OAuth2, maintenance), Gitea vs Forgejo comparison, REST API and integrations, complete features reference

### Updated

- `CHANGELOG.md` - added v0.7.0 entry
- `ROADMAP.md` - marked other-platforms/gitea/ as complete, updated current status to v0.7.0

---

## [0.6.0] - 2026-04-09

### Added

- `other-platforms/azure-devops/00-azure-devops-overview.md` through `other-platforms/azure-devops/14-all-azure-devops-features-reference.md` - complete Azure DevOps coverage (15 files): history (VSS/TFS/VSTS/ADO), all five services (Boards, Repos, Pipelines, Artifacts, Test Plans), branch policies and security, ADO Server on-premises, Marketplace extensions, CLI, API and service hooks, complete features reference

### Updated

- `CHANGELOG.md` - added v0.6.0 entry
- `ROADMAP.md` - marked other-platforms/azure-devops/ as complete, updated current status to v0.6.0

---

## [0.5.0] - 2026-04-09

### Added

- `other-platforms/bitbucket/00-bitbucket-overview.md` through `other-platforms/bitbucket/13-all-bitbucket-features-reference.md` - complete Bitbucket coverage (14 files): Atlassian account setup, app passwords, Pipelines (full YAML guide), Jira smart commits, branch permissions, Cloud vs Data Center, workspaces and permissions, REST API, complete features reference

### Updated

- `CHANGELOG.md` - added v0.5.0 entry
- `ROADMAP.md` - marked other-platforms/bitbucket/ as complete, updated current status to v0.5.0

---

## [0.4.0] - 2026-04-09

### Added

- `gitlab/00-gitlab-overview.md` through `gitlab/15-all-gitlab-features-reference.md` - complete GitLab coverage (16 files): account setup, GitLab vs GitHub comparison, projects and namespaces, merge requests, CI/CD (.gitlab-ci.yml), GitLab Pages, issues and planning, security (SAST/DAST/secret detection/fuzzing), GitLab Duo AI, self-hosting, CLI (glab), groups and permissions, API and integrations, complete features reference

### Updated

- `CHANGELOG.md` - added v0.4.0 entry for gitlab/ folder
- `ROADMAP.md` - marked gitlab/ as complete, updated current status to v0.4.0

---

## [0.3.0] - 2026-04-09

### Added

- `github/18-badges-and-achievements.md` through `github/27-all-github-features-reference.md` - completion of the GitHub section (10 files): badges/achievements, Copilot, Education/Student Pack, CLI, mobile apps, collaborators/teams/orgs, advanced API and GraphQL, what not to put on GitHub, platform comparison, complete features reference

### Updated

- `github/00-github-overview.md` - updated table to include files 18-27, added new learning path sections

---

## [0.2.0] - 2026-03-22

### Added

- `CODE_OF_CONDUCT.md` - Contributor Covenant 3.0
- `SECURITY.md` - security reporting policy
- `SUPPORT.md` - how to get help
- `.editorconfig` - consistent editor settings across all IDEs
- `.markdownlint.json` - markdown linting rules
- `.github/workflows/markdownlint.yml` - automated markdown linting on every PR
- `.github/workflows/linkcheck.yml` - weekly automated link checking
- `.github/ISSUE_TEMPLATE/bug_report.md` - structured bug reports
- `.github/ISSUE_TEMPLATE/content_suggestion.md` - structured content suggestions
- `.github/ISSUE_TEMPLATE/config.yml` - issue template config with Discussions link
- `.github/PULL_REQUEST_TEMPLATE.md` - PR checklist
- Contributors section in README using contrib.rocks
- Open in GitHub Codespaces badge in README

### Updated

- All files updated to new formatting standard - GitHub alert syntax replaces emoji callouts, minimal emoji throughout, proper bold and italic usage
- `introduction/03-setting-up.md` - GitHub and GitLab account creation added as Steps 2 and 3, full step reorder, new common mistakes
- `CONTRIBUTING.md` - new emoji policy, callout syntax guide, branding rules
- `README.md` - Contributor Covenant badge, Codespaces button, contrib.rocks grid, cleaner structure

---

## [0.1.0] - 2026-03-22

### Added

- Initial repository at [github.com/zaccessss/git-unlocked](https://github.com/zaccessss/git-unlocked)
- `README.md`, `LICENSE`, `CONTRIBUTING.md`, `CHANGELOG.md`, `ROADMAP.md`, `FAQ.md`, `HALL_OF_FAME.md`
- All folder structure with `.gitkeep` files
- `introduction/01-welcome.md`
- `introduction/02-how-to-use-this-course.md`
- `introduction/03-setting-up.md`

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

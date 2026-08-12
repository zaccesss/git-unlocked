# AWS Kiro

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

AWS Kiro is an agentic IDE built by Amazon on a Code-OSS fork - the open-source base of VS Code - with a fundamentally different philosophy from Cursor or Windsurf. Where those editors treat AI as a coding accelerator, Kiro introduces **spec-driven development**: before writing a single line of code, the agent produces structured requirement documents, design documents and implementation plans that you review and approve. Git is integrated throughout this lifecycle, from hooks that fire on branch creation and PR events to an autonomous agent that can be assigned GitHub issues directly via issue labels. Kiro launched in preview in July 2025, reached general availability at AWS re:Invent on November 17, 2025 and is particularly well-suited to teams building on AWS who want a structured, documented approach to AI-assisted development.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Kiro](#2-installing-kiro)
3. [Git basics - inherited from Code-OSS](#3-git-basics---inherited-from-code-oss)
4. [Spec-driven development](#4-spec-driven-development)
5. [Specs and Git](#5-specs-and-git)
6. [The Hooks system](#6-the-hooks-system)
7. [Git event hooks in depth](#7-git-event-hooks-in-depth)
8. [GitHub integration and autonomous agent](#8-github-integration-and-autonomous-agent)
9. [Steering files](#9-steering-files)
10. [Kiro Powers and MCP](#10-kiro-powers-and-mcp)
11. [The integrated terminal for Git](#11-the-integrated-terminal-for-git)
12. [Extensions in Kiro](#12-extensions-in-kiro)
13. [Keyboard shortcuts](#13-keyboard-shortcuts)
14. [Pricing](#14-pricing)
15. [Try It Yourself](#15-try-it-yourself)
16. [Common Mistakes](#16-common-mistakes)
17. [Summary](#17-summary)
18. [Sources](#18-sources)

---

## 1. History and background

Kiro was built by an Amazon team focused on bringing agentic AI to the software development lifecycle in a way that produces production-grade output rather than prototype-quality vibe coding. The core insight behind Kiro is that most AI-assisted coding tools fail at complexity - they produce working code for simple tasks but struggle with large systems where requirements, architecture and testing must be thought through before implementation begins.

Kiro launched in **preview on July 14, 2025** with access by invitation. It reached **general availability on November 17, 2025**, the day before AWS re:Invent 2025, where it received prominent coverage in Werner Vogels' keynote. At re:Invent, Amazon announced that Kiro had become its internal standard IDE for new development projects, with Delta Air Lines as a key enterprise early adopter reporting 94% developer satisfaction scores.

A **Kiro CLI** launched at re:Invent 2025, allowing agent execution in any terminal over SSH - useful for running Kiro agents in CI/CD pipelines or on remote development servers.

Kiro is built on **Code-OSS** (the MIT-licensed open-source base of VS Code) rather than VS Code itself. This means it uses the **OpenVSX** extension registry rather than the VS Code Marketplace. Most popular extensions are available on both, but some proprietary VS Code extensions are only on the VS Code Marketplace and will not install in Kiro.

---

## 2. Installing Kiro

🪟 **Windows** / 🍎 **macOS** / 🐧 **Linux**

Download from [kiro.dev](https://kiro.dev). Kiro provides installers for Windows, macOS (Apple Silicon and Intel) and Linux.

**Authentication**: Kiro supports four sign-in methods - no AWS account is required:

- GitHub account
- Google account
- AWS Builder ID (free, no credit card)
- AWS IAM Identity Center (for enterprise SSO)

On first launch, Kiro imports VS Code settings if you choose. It uses OpenVSX for extensions rather than the VS Code Marketplace.

The free tier provides 50 credits per month with 500 bonus credits in the first 14 days. No credit card is required for the free tier.

---

## 3. Git basics - inherited from Code-OSS

Kiro uses Code-OSS as its base, which shares the same Git extension source as VS Code. The Source Control panel, diff editor, 3-way merge editor, inline blame, gutter indicators and branch management all work identically to VS Code.

The **Generate commit message** sparkle button in the commit box uses Claude Sonnet 4.6 via AWS Bedrock by default and can be steered by your steering files.

Keyboard shortcuts for Git operations are the same as VS Code: `Ctrl+Shift+G` / `Cmd+Shift+G` opens the Source Control panel, `Ctrl+Enter` / `Cmd+Enter` commits and the Status Bar branch name opens the branch picker.

---

## 4. Spec-driven development

Spec-driven development is Kiro's defining feature. Instead of asking the AI to implement feature X, you ask it to first produce a spec - a structured set of documents capturing requirements, design decisions and an implementation plan. You review and approve the spec before any code is written.

A spec lives in `.kiro/specs/<feature-name>/` and contains three files:

**`requirements.md`** - user stories in EARS format (Easy Approach to Requirements Syntax):

```markdown
# Feature: CSV Export for Orders

## Requirements

### 1. Export orders as CSV

**As a** report administrator
**I want to** export the orders table to CSV
**So that** I can analyse data in spreadsheet tools

#### Acceptance criteria

- WHEN the user clicks "Export CSV" on the orders page
- THEN the system generates a CSV file with all visible columns
- AND the file downloads with the filename format orders-YYYY-MM-DD.csv
- AND the CSV includes a header row with column names
```

**`design.md`** - architecture decisions with Mermaid diagrams:

```markdown
# Design: CSV Export

## Architecture

The export is implemented as a streaming endpoint to handle large datasets
without memory issues.

## Decision log

- Chose streaming over buffering because order tables can exceed 100k rows
- CSV format chosen over XLSX for universal compatibility
```

**`tasks.md`** - an ordered, dependency-aware implementation checklist:

```markdown
# Tasks: CSV Export

- [ ] 1. Add streaming CSV endpoint to OrderController
- [ ] 2. Implement OrderCsvService with row streaming
- [ ] 3. Add "Export CSV" button to orders UI
- [ ] 4. Write integration tests
```

---

## 5. Specs and Git

Spec files are committed to Git as part of the feature branch. The spec is documentation that lives alongside the code, versioned with it and reviewable in pull requests.

A typical Kiro feature branch looks like:

```
.kiro/specs/csv-export/
    requirements.md    (committed)
    design.md          (committed)
    tasks.md           (committed, checkboxes updated as tasks complete)
src/...                (implementation files)
```

When Kiro completes a task in `tasks.md`, it checks off the checkbox and commits the update alongside the implementation. The Git history tells you not just what code changed but which requirement it satisfied and when.

Reviewers can see the full spec in the PR diff, providing context for code changes that is normally absent from PRs. Comments can be left on spec documents, which Kiro incorporates into subsequent agent runs.

For bug fixes, Kiro uses a simpler `bugfix.md` format capturing reproduction steps, root cause analysis, fix plan and verification criteria.

---

## 6. The Hooks system

Hooks are Kiro's automation layer - scripts or agent prompts that fire automatically in response to events in your development workflow. They live in `.kiro/hooks/*.kiro.hook` and are committed to Git so the whole team inherits them.

A hook file is YAML with two parts: a trigger and an action. Actions are either:

- **Agent Prompt** - Kiro runs an agent task, consuming credits
- **Shell Command** - a shell script runs for free; exit 0 adds stdout to agent context, non-zero sends stderr as an error

**Example: run tests on file save**

```yaml
name: Run tests on save
trigger:
  type: file_save
  glob: "src/**/*.py"
action:
  type: shell_command
  command: python -m pytest tests/ --tb=short
```

**Example: validate conventional commit format**

```yaml
name: Check commit message format
trigger:
  type: git_event
  event: pre_commit
action:
  type: shell_command
  command: |
    MSG=$(cat "$1" 2>/dev/null || git log --format=%s -1 HEAD)
    if ! echo "$MSG" | grep -qE "^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .+"; then
      echo "ERROR: Commit message must follow conventional commits format"
      echo "Example: feat(auth): add OAuth login"
      exit 1
    fi
```

---

## 7. Git event hooks in depth

Kiro hooks support a comprehensive set of Git-related events across three categories.

**Repository events**:

| Event                           | When it fires                               |
| ------------------------------- | ------------------------------------------- |
| `branch_created`                | A new local branch is created               |
| `branch_switched`               | The active branch changes                   |
| `pre_commit`                    | Immediately before a commit is created      |
| `commit_created`                | After a commit is created                   |
| `pull_request_opened`           | A PR is opened on the connected GitHub repo |
| `pull_request_review_requested` | A review is requested on a PR               |
| `merge_completed`               | A branch merge completes                    |
| `rebase_completed`              | A rebase completes                          |

**Example: auto-run tests and create a baseline report when a feature branch is created**

```yaml
name: Feature branch setup
trigger:
  type: git_event
  event: branch_created
  branch_pattern: "feature/*"
action:
  type: shell_command
  command: |
    echo "Branch: $(git branch --show-current)"
    python -m pytest tests/ --json-report \
      --json-report-file=.kiro/test-baseline.json
    echo "Baseline test report saved"
```

**Example: generate a PR summary when a PR is opened**

```yaml
name: PR summary generator
trigger:
  type: git_event
  event: pull_request_opened
action:
  type: agent_prompt
  prompt: |
    A pull request has been opened. Read the diff between the base branch
    and this branch. Write a summary covering:
    1. What changed and why
    2. Which specs in .kiro/specs/ this PR addresses
    3. Testing approach and coverage
    4. Any concerns reviewers should focus on
    Post this as a PR comment via the GitHub API.
```

**External events** (Teams/Enterprise plans): AWS EventBridge messages, S3 upload events and scheduled cron triggers are also supported, allowing Kiro hooks to be triggered by external infrastructure events.

---

## 8. GitHub integration and autonomous agent

The **Kiro Autonomous Agent** (launched at re:Invent 2025 as a preview feature) integrates directly with GitHub to accept work assignments from issue labels and review comments.

**Assigning an issue to Kiro**:

1. Install the Kiro GitHub App on your organisation (one-time setup)
2. Add the label `kiro` to any GitHub issue or comment `/kiro` on any issue
3. For PR review feedback, comment `/kiro fix` to ask Kiro to implement the review comment

Kiro creates a spec from the issue description, requests your approval via a comment on the issue, then works in an isolated sandbox: it creates a feature branch, implements the spec, runs tests and opens a pull request.

**Sandbox network policies**:

- `integration_only` - only connected integrations (GitHub, AWS)
- `common_dependencies` - package registries (npm, PyPI, Maven) included
- `open_internet` - full internet access
- `custom_allowlist` - specified domain list

**Commit attribution**: Kiro agent commits include both the task creator and Kiro as co-authors:

```
feat(orders): add CSV export streaming endpoint

Implements requirements 1 and 2 from .kiro/specs/csv-export/requirements.md

Co-authored-by: Isaac Adjei <isaac@example.com>
Co-authored-by: Kiro <kiro@amazon.com>
```

**What Kiro's agent does not do**: it does not auto-merge pull requests, does not act on review comments from reviewers other than the task creator and does not push directly to protected branches.

---

## 9. Steering files

Steering files in `.kiro/steering/` provide persistent project context to every Kiro agent session. They are committed to Git so all team members get consistent agent behaviour. Kiro creates four default files on project initialisation:

**`product.md`** - what the product is and who it is for

**`structure.md`** - how the codebase is organised

**`tech.md`** - technology stack and conventions

**Example `tech.md` with Git conventions**:

```markdown
# Technology stack

Backend: Python 3.12, FastAPI, PostgreSQL 16
Frontend: React 19, TypeScript 5.4, Tailwind CSS

## Git conventions

- Conventional commits: feat, fix, docs, chore, test, refactor
- Branch naming: feature/<ticket>-<description>, fix/<ticket>-<description>
- Subject line max 72 characters, imperative mood
- Always reference the ticket number in the commit body: Refs: #<n>
- Never commit debug logging or commented-out code

## Code conventions

- TypeScript strict mode, no any types
- All API endpoints must have OpenAPI docstrings
- Services must have 100% unit test coverage
```

You can add custom steering files for specific areas. Kiro loads all files in `.kiro/steering/` for every agent session, so the Git conventions you write here are automatically applied to every commit message, branch name and PR description the agent generates.

---

## 10. Kiro Powers and MCP

**Kiro Powers** address a common problem with MCP: too many connected tools leads to bloated context windows where the agent processes irrelevant tool descriptions before getting to the task.

Each Kiro Power is a bundle of related tools that loads only when relevant keywords appear in your task description:

- "Stripe" or "payment" → loads the Stripe Power
- "database" or "Postgres" → loads the Neon or Supabase Power
- "deploy" or "Lambda" → loads the AWS Power

Launch partners include Datadog, Dynatrace, Figma, Neon, Netlify, Postman, Stripe, Supabase and AWS services.

**MCP for Git-adjacent workflows**: configure MCP servers in `.kiro/settings/mcp.json`:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

With the GitHub MCP server, agents can read issue descriptions, post PR comments, fetch check run results and interact with the GitHub API without leaving Kiro.

---

## 11. The integrated terminal for Git

Kiro's integrated terminal is identical to VS Code's: `` Ctrl+` `` / `` Cmd+` `` to open. `GIT_EDITOR` is set to open files in Kiro, so `git commit` and `git rebase -i` route through the IDE.

**Kiro CLI** (launched at re:Invent 2025):

```bash
# Install
npm install -g @aws/kiro-cli

# Run an agent task from the terminal
kiro task "Review my staged changes and generate a commit message following conventional commits"

# Execute a spec
kiro spec .kiro/specs/csv-export/
```

The CLI is particularly useful for running Kiro agent tasks in GitHub Actions workflows:

```yaml
- name: Kiro review
  run: kiro task "Review the diff of this PR and post a summary comment"
  env:
    KIRO_API_KEY: ${{ secrets.KIRO_API_KEY }}
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## 12. Extensions in Kiro

Kiro uses the **OpenVSX** registry at [open-vsx.org](https://open-vsx.org). Most popular extensions are available there.

**Git extensions available on OpenVSX**:

- GitLens - available and fully compatible
- Git Graph - available
- GitLab Workflow - available
- Git History - available

**Not available on OpenVSX**: the GitHub Pull Requests extension is a Microsoft/GitHub-published extension that may not be on OpenVSX. Use the Kiro GitHub App integration and the web interface for PR management or the GitHub CLI in the integrated terminal (`gh pr create`, `gh pr review`).

---

## 13. Keyboard shortcuts

| Action                    | Windows/Linux                | Mac                     |
| ------------------------- | ---------------------------- | ----------------------- |
| Open Source Control panel | `Ctrl+Shift+G`               | `Cmd+Shift+G`           |
| Open terminal             | `` Ctrl+` ``                 | `` Cmd+` ``             |
| Commit                    | `Ctrl+Enter` (in commit box) | `Cmd+Enter`             |
| Open Command Palette      | `Ctrl+Shift+P`               | `Cmd+Shift+P`           |
| Open Kiro chat            | `Ctrl+Shift+K`               | `Cmd+Shift+K`           |
| New spec                  | Kiro sidebar > New Spec      | Kiro sidebar > New Spec |
| New hook                  | Kiro sidebar > New Hook      | Kiro sidebar > New Hook |

---

## 14. Pricing

| Plan  | Price      | Credits/month                | Notes                        |
| ----- | ---------- | ---------------------------- | ---------------------------- |
| Free  | $0         | 50 + 500 bonus first 14 days | GitHub/Google/AWS Builder ID |
| Pro   | $20/month  | Higher limits                | Most teams                   |
| Pro+  | $40/month  | Higher limits                | Power users                  |
| Power | $200/month | Maximum                      | Heavy agentic use            |

Overage billing at $0.04 per credit is disabled by default - you must opt in. Shell Command hooks are free and do not consume credits. Agent Prompt hooks and agent tasks consume credits.

GovCloud availability adds approximately 20% to pricing with no Free tier.

---

## 15. Try It Yourself

**Exercise 1 - create your first spec**

Open a repository in Kiro. From the Kiro sidebar, click **New Spec**. Describe a small feature: "Add a health check endpoint at /health that returns status 200 with the JSON body {"status": "ok"}." Let Kiro generate the three spec files. Review `requirements.md`, `design.md` and `tasks.md`. Approve the spec and let Kiro implement it. Check the resulting commits with `git log`.

**Exercise 2 - write a pre-commit hook**

Create `.kiro/hooks/conventional-commits.kiro.hook` with the YAML from section 6 that validates conventional commit format. Attempt a commit with a badly formatted message ("fixed stuff") and verify the hook rejects it. Fix the message and verify the hook passes. Commit the hook file to the repository.

**Exercise 3 - add steering files**

Create `.kiro/steering/tech.md` with your project's technology stack and commit conventions. Make a code change and ask Kiro to generate a commit message. Verify it follows the conventions you defined. Ask a colleague to pull the repository and make a change - their Kiro should follow the same conventions.

**Exercise 4 - assign a GitHub issue**

If you have the Kiro GitHub App installed on a repository, create a simple issue (for example, "Add a CONTRIBUTING.md with setup instructions"). Add the `kiro` label. Watch Kiro create a spec as an issue comment. Approve the spec by commenting. Watch the resulting PR and review the spec files inside it.

**Exercise 5 - branch creation hook**

Create a hook that fires on `branch_created` for `feature/*` branches. Have it run a shell command that echoes the branch name and runs a quick lint check. Create a new feature branch and watch the hook fire in the Kiro panel.

---

## 16. Common Mistakes

Using Kiro for trivial one-line fixes where spec-driven development adds unnecessary overhead. Reserve specs for features that genuinely benefit from documented requirements and design decisions. For a typo fix or a config tweak, write the commit manually and move on.

Not committing steering files and hook files to the repository. These are the team's shared AI configuration. If they only exist on one developer's machine, nobody else benefits. Document them in your README or CONTRIBUTING guide.

Treating Kiro-generated pull requests as finished work. They are starting points for code review. The agent follows the spec it was given - if the spec was incomplete or ambiguous, the code will reflect that ambiguity. Review Kiro PRs with the same care you would give to any PR.

Confusing OpenVSX with the VS Code Marketplace. If an extension is unavailable in Kiro, it is probably not on OpenVSX. Check open-vsx.org before concluding it does not exist. For some missing extensions, the GitHub CLI (`gh`) in the integrated terminal is a practical alternative for PR workflows.

Running Agent Prompt hooks on high-frequency events without monitoring credit consumption. A hook that fires on every file save and runs an agent prompt will rapidly exhaust your credit allocation. Use Shell Command hooks (free) for file-save events and reserve Agent Prompt hooks for lower-frequency Git events.

---

## 17. Summary

AWS Kiro is a Code-OSS fork with spec-driven development at its core: before implementing any feature, the agent produces `requirements.md`, `design.md` and `tasks.md` committed to Git as part of the feature branch, making requirements reviewable in pull requests alongside code. The Hooks system fires Agent Prompt or Shell Command actions on a comprehensive range of Git events - branch creation, pre-commit, PR opened, review requested, merge completed - enabling automation throughout the development lifecycle. The GitHub-integrated Autonomous Agent accepts work assignments via the `kiro` label or `/kiro` comment on issues, builds a spec, implements it in an isolated sandbox and opens a pull request with co-authored commits. Steering files in `.kiro/steering/` enforce project-wide Git conventions for every agent session. Kiro Powers load MCP tool bundles contextually to avoid context bloat. Built on Code-OSS rather than VS Code, Kiro uses OpenVSX for extensions. No AWS account is required; the free tier provides 50 credits per month. Kiro is the strongest choice for AWS-centric teams who want structured, documented, Git-integrated AI-assisted development.

---

## 18. Sources

- [Kiro documentation](https://kiro.dev/docs)
- [Introducing Kiro - Kiro blog](https://kiro.dev/blog/introducing-kiro/)
- [AWS re:Invent 2025 Kiro coverage](https://caylent.com/blog/aws-reinvent-2025-every-ai-announcement-including-amazon-nova-2-and-kiro)
- [Amazon Kiro complete developer guide](https://www.digitalapplied.com/blog/amazon-kiro-aws-agentic-ide-complete-guide)
- [AWS Kiro IDE challenger review](https://dev.to/onepoint/aws-kiro-another-ai-powered-ide-challenger-or-a-game-changer-1bj8)
- [Kiro at AWS re:Invent 2025 session](https://dev.to/kazuya_dev/aws-reinvent-2025-kiro-your-agentic-ide-for-spec-driven-development-dvt209-12gd)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

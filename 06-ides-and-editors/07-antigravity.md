# Google Antigravity

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Google Antigravity is an agent-first IDE built by Google using the ex-Windsurf/Codeium team hired in the $2.4 billion talent deal of July 2025. It launched on November 18, 2025 alongside Gemini 3, is built on a VS Code fork, and is free in public preview. Its distinguishing concept is the **Manager view** - a mission control panel for orchestrating multiple parallel AI agents, each producing verifiable Artifacts that you can audit before accepting. For Git, this means agents that commit code, open pull requests and create branches, with every action traceable through Artifacts. As of April 2026, Antigravity is a genuinely capable but still-preview tool with real stability limitations worth understanding before adopting for production work.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Antigravity](#2-installing-antigravity)
3. [Git basics - inherited from VS Code](#3-git-basics---inherited-from-vs-code)
4. [The dual-view architecture](#4-the-dual-view-architecture)
5. [The Artifacts system and Git](#5-the-artifacts-system-and-git)
6. [AI-driven Git workflows in Antigravity](#6-ai-driven-git-workflows-in-antigravity)
7. [Models available in Antigravity](#7-models-available-in-antigravity)
8. [The integrated terminal for Git](#8-the-integrated-terminal-for-git)
9. [Extensions in Antigravity](#9-extensions-in-antigravity)
10. [Security considerations](#10-security-considerations)
11. [Current stability and known issues](#11-current-stability-and-known-issues)
12. [Pricing](#12-pricing)
13. [Keyboard shortcuts](#13-keyboard-shortcuts)
14. [Try It Yourself](#14-try-it-yourself)
15. [Common Mistakes](#15-common-mistakes)
16. [Summary](#16-summary)
17. [Sources](#17-sources)

---

## 1. History and background

The story of Antigravity is inseparable from Windsurf's. In July 2025, when OpenAI's acquisition of Windsurf collapsed over IP concerns, Google moved quickly: it paid $2.4 billion in a **talent deal** - technically not an acquisition of the company itself, but a licensing agreement plus compensation packages - that brought Windsurf's CEO Varun Mohan, co-founder Douglas Chen, and approximately 40 senior engineers into Google. The roughly 200 remaining Windsurf employees were left behind (they became part of the Cognition acquisition that created the current Windsurf, covered in [06-windsurf.md](06-windsurf.md)).

Inside Google, the ex-Windsurf team was given resources to build an IDE on top of their existing deep familiarity with VS Code forks and agentic coding systems. They shipped Antigravity in approximately four months, launching on **November 18, 2025** alongside Gemini 3 at a Google event. The name reflects Google's ambition to lift AI-assisted development beyond the gravitational pull of traditional coding approaches.

Antigravity is available free in public preview at [antigravity.google](https://antigravity.google) (or through Google's developer portal). There is no paid tier yet; Google has indicated premium tiers will be introduced in 2026 but has not announced pricing.

---

## 2. Installing Antigravity

🪟 **Windows** / 🍎 **macOS** / 🐧 **Linux**

Download from the Antigravity website. Antigravity supports:

- macOS 12 (Monterey) or later, on both Apple Silicon and Intel
- Windows 10 64-bit or later
- Linux 64-bit with glibc 2.28+ and glibcxx 3.4.25+

A Google account is required to sign in and access Gemini models. The free tier includes access to Gemini 3.1 Pro within daily usage limits.

On first launch, Antigravity offers to import VS Code settings, extensions and keybindings. The migration is one-click.

---

## 3. Git basics - inherited from VS Code

Antigravity is a VS Code fork and inherits the complete VS Code Git integration:

- **Source Control panel** (`Ctrl+Shift+G` / `Cmd+Shift+G`)
- **Line and hunk-level staging**
- **3-way merge editor**
- **Source Control Graph**
- **Inline blame**
- **Gutter indicators**
- **Branch management** via the Status Bar
- **Push, pull, fetch** via the overflow menu

Everything in [01-vs-code.md](01-vs-code.md) applies to Antigravity. The **Generate commit message** button in the Source Control panel uses Gemini 3.1 Pro by default.

---

## 4. The dual-view architecture

Antigravity's defining UX is the toggle between two primary interfaces:

**Editor view** (press `Cmd+E` on Mac or `Ctrl+E` on Windows/Linux to return to it): the standard IDE interface. A full VS Code-compatible editor with source control panel, file explorer, terminal, debugger and an AI agent sidebar similar to Cursor's Composer. This is where you write code, review diffs, and do hands-on work.

**Manager view** (press `Cmd+E` / `Ctrl+E` again to toggle, or click the grid icon in the Activity Bar): mission control for orchestrating multiple AI agents. The Manager view shows up to 5 parallel agents running simultaneously. Each agent has its own panel showing:

- The current task description
- A live log of actions (files read, commands run, Git operations performed)
- The Artifact progress (which deliverables have been created)
- Controls: Pause, Stop, Provide Feedback

The Manager view is Antigravity's clearest differentiator from Cursor and Windsurf. Rather than one Composer or Cascade panel, you get a dashboard for parallel AI work.

---

## 5. The Artifacts system and Git

Every agent action in Antigravity produces **Artifacts** - verifiable deliverables that document what the agent did and why. For Git-related work, common Artifacts include:

- **task.md** - a breakdown of the task into steps with acceptance criteria
- **implementation_plan.md** - the approach the agent chose, with alternatives it considered
- **git_operations.md** - a log of every Git command the agent ran: branches created, commits made, files staged, pushes executed
- **screenshots/** - browser recordings or editor screenshots showing the agent verifying its work
- **walkthrough.md** - a narrative explanation of the completed work

Artifacts are stored in `~/.gemini/antigravity/brain/<GUID>/` on your local machine and linked from the agent panel. Reusable patterns are saved to `.antigravity/knowledge/` in the project directory (committed to Git if you choose).

For Git workflows, the Artifact model is valuable because you can read `git_operations.md` to see exactly which commits the agent created, what messages it used and whether it pushed. This is more auditable than Cursor or Windsurf, where you need to run `git log` yourself to understand what the agent did.

**Commenting on Artifacts**: click any section of an Artifact to leave a comment. Antigravity incorporates your comment into the agent's context for the next step, similar to leaving review comments on a Google Doc. For example, if the agent's `git_operations.md` shows it committed to the wrong branch, you can comment "this should have been on feature/login, not on main" and Antigravity will correct the branch.

---

## 6. AI-driven Git workflows in Antigravity

**Starting a task**: type your task in the agent sidebar in the Editor view or in the Manager view. Antigravity generates a task plan (Artifact) and asks you to approve before executing.

**Git workflow examples**:

**Feature branch and implementation**:

> "Create a branch called feature/export-csv, implement CSV export for the orders table in src/reports/orders.py, update the API endpoint in src/api/orders.py to accept an export=csv query parameter, write tests and commit everything with appropriate conventional commit messages."

Antigravity creates the Artifact plan first. After you approve, it executes: branch creation, file edits, test runs, commit and logs everything in `git_operations.md`.

**Reviewing before merge**:

> "Compare the current branch with main. List every file changed, summarise the intent of each change and flag any potential issues I should know about before merging."

The agent runs `git diff main...HEAD`, reads the output and produces a review Artifact that you can read before deciding to merge.

**Multi-agent parallel work**: from the Manager view, spawn two agents simultaneously:

- Agent 1: "Implement the dark mode UI on feature/dark-mode"
- Agent 2: "Write the tests for the existing login flow on fix/login-tests"

Both agents work on separate branches (Antigravity uses Git worktrees for isolation) and report to the Manager view. You can monitor both, provide feedback to either, and review their Artifacts when done.

---

## 7. Models available in Antigravity

Antigravity supports multiple AI models. You switch models from the model selector dropdown in the agent sidebar.

| Model             | Notes                                                                  |
| ----------------- | ---------------------------------------------------------------------- |
| Gemini 3.1 Pro    | Default; 1M token input, 65K output; Low/Medium/High thinking variants |
| Gemini 3 Pro      | Launch-era model; 76.2% SWE-Bench Verified                             |
| Gemini 3 Flash    | Faster, cheaper; suitable for routine tasks                            |
| Claude Opus 4.6   | Anthropic's most capable model available in Antigravity                |
| Claude Sonnet 4.6 | Faster Claude option; Thinking variant available                       |
| GPT-OSS-120B      | Open-source OpenAI model; available in Antigravity                     |

**Auto-failover**: if your primary model hits a rate limit, Antigravity automatically switches to an available model rather than failing the task. This is useful during peak usage.

**Switching models mid-task**: you can switch the model for a running agent from the Manager view without stopping it. The agent picks up with the new model from the current step.

For Git-heavy tasks (understanding large diffs, planning complex refactors), Gemini 3.1 Pro or Claude Opus 4.6 produce the most reliable results. For routine commit message generation or simple branch creation, Gemini 3 Flash is sufficient and faster.

---

## 8. The integrated terminal for Git

The integrated terminal in Antigravity is identical to VS Code's: `` Ctrl+` `` / `` Cmd+` `` to open. `GIT_EDITOR` is set to open files in Antigravity, so `git commit` and `git rebase -i` route through the IDE.

The terminal in the Editor view is your interactive terminal for manual Git commands. It is separate from the agent's execution environment.

**Agent terminal**: when an agent is running in the Manager view, it uses a separate, isolated execution environment. Agent-run Git commands are shown in the Artifact log (`git_operations.md`) but do not appear in your interactive terminal history.

> [!TIP]
> Running `git log --oneline -20` in your interactive terminal before starting an agent task is useful context-setting. Antigravity can see your terminal history through its editor integration, so the log output is available to the agent without you needing to paste it into the task description.

---

## 9. Extensions in Antigravity

Antigravity supports VS Code extensions. Import from VS Code during initial setup, or install from the VS Code Marketplace within the IDE.

**Git extensions that work in Antigravity**:

- **GitLens** - inline blame and history views complement the Artifact audit trail
- **GitHub Pull Requests** - create and review PRs without leaving the IDE
- **GitLab Workflow** - pipeline status and MR workflows for GitLab users

Note that some Copilot-dependent extension features may not work since Antigravity uses Gemini rather than Copilot by default. Copilot is not bundled with Antigravity.

---

## 10. Security considerations

Antigravity processes code through Google's cloud infrastructure. This matters for:

**Data residency**: all code sent to Gemini models goes to Google's servers. If your organisation has data residency requirements or handles code that cannot leave your jurisdiction, Antigravity may not be appropriate. Unlike JetBrains AI Ultimate or some Cursor configurations, there is no self-hosted option for Antigravity as of April 2026.

**Agent permissions**: Antigravity agents can read any file in your repository, run terminal commands and push to remote repositories. Review the agent's planned Artifact before approving execution for sensitive tasks.

**macOS sandbox**: a macOS sandbox was added in February 2026, limiting what agents can access outside the project directory. Windows and Linux rely on Secure Mode (a configuration flag), which is less comprehensive.

**Credentials in code**: Antigravity's agents are trained to avoid committing credentials and will flag suspected hardcoded secrets. However, this is not a substitute for pre-commit hooks that scan for secrets (tools like `gitleaks` or `trufflehog`). Set up your own hooks - Antigravity respects `.git/hooks/` scripts.

---

## 11. Current stability and known issues

Antigravity is in public preview as of April 2026. It is a capable tool but has real stability limitations that are important to understand before using it for production-critical work.

**Known issues as of April 2026**:

- **Context memory errors**: agents occasionally lose track of changes they made earlier in a long session, leading to repeated work or inconsistent file states
- **Premature agent termination**: agents on complex multi-hour tasks sometimes stop mid-execution without completing the Artifact
- **Quota unpredictability**: the free tier uses a "work done" metric rather than simple request counts. Complex reasoning tasks consume quota faster. Some users report hitting daily limits within 2-3 hours of intensive use
- **Rate limit incidents**: in March 2026, multiple Pro-tier subscribers reported 7-day account lockouts instead of the advertised 5-hour refresh period, prompting community backlash
- **UI freeze**: occasional freezes in the Manager view when monitoring multiple agents simultaneously
- **Legacy codebase struggles**: agents perform well on standard library patterns but can misinterpret unusual frameworks, unusual naming conventions or undocumented legacy code

**The practical advice**: use Antigravity for new features, greenfield work and tasks with well-defined requirements. For critical production paths - authentication, payment processing, data migration - either use a more mature tool or supervise Antigravity's work very closely and review every Artifact and every commit before accepting.

---

## 12. Pricing

Antigravity is **free in public preview** as of April 2026. Google has indicated it will introduce paid tiers in 2026. Based on the existing Google AI product structure, expected tiers are:

| Tier            | Estimated price | Notes                                                      |
| --------------- | --------------- | ---------------------------------------------------------- |
| Free            | $0              | Limited daily rate; Gemini 3 Flash + 3.1 Pro within limits |
| Google AI Pro   | $20/month       | Higher limits, Claude access                               |
| Google AI Ultra | $249.99/month   | Maximum limits, all models, priority                       |

These are projections based on Google's AI product pricing patterns. Check [antigravity.google](https://antigravity.google) for current pricing.

> [!NOTE]
> Antigravity's free tier is genuinely generous compared to Cursor's Hobby or Windsurf's Free tier in terms of model access - you get Gemini 3.1 Pro and Claude Opus 4.6 without paying. However, the rate limits tighten during peak hours and the "work done" quota system is opaque. If you need predictable access for daily professional use, wait for the paid tier or use Cursor or Windsurf in the meantime.

---

## 13. Keyboard shortcuts

Antigravity inherits VS Code shortcuts and adds:

| Action                     | Windows/Linux                | Mac           |
| -------------------------- | ---------------------------- | ------------- |
| Toggle Editor/Manager view | `Ctrl+E`                     | `Cmd+E`       |
| New agent task             | `Ctrl+N` (in Manager view)   | `Cmd+N`       |
| Open Source Control panel  | `Ctrl+Shift+G`               | `Cmd+Shift+G` |
| Open terminal              | `` Ctrl+` ``                 | `` Cmd+` ``   |
| Commit                     | `Ctrl+Enter` (in commit box) | `Cmd+Enter`   |
| Open Command Palette       | `Ctrl+Shift+P`               | `Cmd+Shift+P` |
| Stop agent                 | `Esc` (in agent panel)       | `Esc`         |

---

## 14. Try It Yourself

**Exercise 1 - generate a commit message with Gemini**

Make a meaningful change to a file in a repository. Stage the changes in the Source Control panel. Click the sparkle icon in the commit message box. Compare the Gemini-generated message with what Copilot or Cursor would have generated for the same diff. Note any differences in style or specificity.

**Exercise 2 - use the Manager view for a simple task**

Switch to the Manager view. Create a new agent task: "Look at the most recent 5 commits in this repository and write a brief changelog entry for each one." Watch the agent log in the Manager view as it runs `git log`. Read the resulting Artifact. This is a low-risk task to get familiar with the Manager view without risking any code changes.

**Exercise 3 - audit an agent's Git operations**

Give an agent a task that involves creating a branch and making a commit: "Create a branch called test/antigravity-demo, add a file called ANTIGRAVITY_DEMO.md explaining what this branch was for, and commit it." After the agent completes, find the `git_operations.md` Artifact and read the log of Git commands it ran. Verify with `git log` in the terminal that the commits match the Artifact.

**Exercise 4 - parallel agents on two branches**

Open the Manager view and create two agent tasks simultaneously:

- Task 1: "Create branch feature/readme-update, improve the README with better installation instructions, and commit."
- Task 2: "Create branch feature/add-gitignore, add a comprehensive .gitignore for a Python project, and commit."

Watch both agents work in parallel in the Manager view. Review both Artifacts when done. Check `git branch` to confirm both branches were created.

---

## 15. Common Mistakes

Treating Antigravity as production-ready because it is a Google product is a mistake. It is in public preview, has documented stability issues, and Google itself describes it as experimental. Use it for exploration, prototyping and non-critical tasks rather than production deployments until the stable release.

Not reading the Artifact before accepting an agent's work means losing the main advantage Antigravity offers over Cursor and Windsurf. The `git_operations.md` Artifact is your audit trail. Read it. Verify that the branches, commits and pushes it lists are what you asked for.

Confusing the free tier's quota with a fixed request count leads to unexpected limit hits. Antigravity's quota is usage-based on "work done" - a complex 10-file refactor consumes more quota than a simple commit message generation. Plan your usage accordingly and expect limits to hit faster on intensive tasks.

Not setting up Git hooks for secrets detection because the agent checks for credentials. Agent-side checking is a secondary layer, not a primary defence. Set up `gitleaks`, `trufflehog` or `git-secrets` as pre-commit hooks so that even if an agent somehow commits a credential, the hook catches it before the push.

Expecting Antigravity to handle large legacy codebases without guidance leads to poor results. If your repository is large, undocumented or uses unusual patterns, write a `.antigravity/knowledge/` file that explains the architecture, naming conventions and patterns. This context helps the agent make better decisions about which files to modify and how.

---

## 16. Summary

Google Antigravity is an agent-first VS Code fork built by the ex-Windsurf team Google hired in July 2025, launched in November 2025 alongside Gemini 3. It inherits the full VS Code Git experience and adds a dual-view architecture: the Editor view for hands-on work and the Manager view for orchestrating up to 5 parallel AI agents simultaneously. The Artifacts system produces verifiable deliverables - task plans, implementation notes, Git operation logs and browser recordings - that make agent work auditable in ways Cursor and Windsurf do not match. Agents create branches, stage changes, commit code and open pull requests, logging every action in `git_operations.md`. Multiple models are available including Gemini 3.1 Pro, Claude Opus 4.6, Claude Sonnet 4.6 and GPT-OSS-120B. The free-in-preview tier is genuinely generous with model access. As of April 2026, Antigravity has real stability limitations - context memory errors, quota unpredictability and premature agent termination - that make it better suited for exploration and non-critical tasks than for production-critical daily workflows. For production use, Cursor or Windsurf are more mature choices.

---

## 17. Sources

- [Google Antigravity - Wikipedia](https://en.wikipedia.org/wiki/Google_Antigravity)
- [Google Antigravity setup guide - Petronella Technology](https://petronellatech.com/blog/google-antigravity-ide-setup-guide-2026)
- [Google Antigravity vs Cursor review - Y Build](https://ybuild.ai/en/blog/google-antigravity-vs-cursor-vs-claude-code-free-ai-ide-2026)
- [Google Antigravity agentic IDE - VentureBeat](https://venturebeat.com/ai/google-antigravity-introduces-agent-first-architecture-for-asynchronous)
- [Google hires Windsurf CEO Varun Mohan - CNBC](https://www.cnbc.com/2025/07/11/google-windsurf-ceo-varun-mohan-latest-ai-talent-deal-.html)
- [Windsurf VCs and founders payment details - Yahoo Finance](https://finance.yahoo.com/news/more-details-emerge-windsurf-vcs-160000238.html)
- [Google Antigravity agentic development - Baytech Consulting](https://www.baytechconsulting.com/blog/google-antigravity-ai-ide-2026)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

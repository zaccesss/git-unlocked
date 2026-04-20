# Windsurf

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Windsurf is an AI-first code editor built on a VS Code fork, distinguished by its Cascade agent system - an agentic AI that maintains real-time awareness of your file edits, terminal commands and clipboard contents to stay in context automatically without requiring you to repeat yourself. Its ownership history in 2025 was one of the most turbulent in developer tools, passing through an attempted OpenAI acquisition, a Google talent deal and finally a Cognition AI acquisition. The product has remained under active development throughout. Windsurf 2.0, released in April 2026, brought the Agent Command Center and Devin integration. Like Cursor, Windsurf inherits the full VS Code Git experience and layers autonomous agent capabilities on top.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Windsurf](#2-installing-windsurf)
3. [Git basics - inherited from VS Code](#3-git-basics---inherited-from-vs-code)
4. [Cascade - the Windsurf agent](#4-cascade---the-windsurf-agent)
5. [Flow awareness and Git context](#5-flow-awareness-and-git-context)
6. [AI-driven Git workflows with Cascade](#6-ai-driven-git-workflows-with-cascade)
7. [Git worktrees and parallel agents](#7-git-worktrees-and-parallel-agents)
8. [The Windsurf 2.0 Agent Command Center](#8-the-windsurf-20-agent-command-center)
9. [Memories and rules](#9-memories-and-rules)
10. [The Cascade dedicated terminal](#10-the-cascade-dedicated-terminal)
11. [The integrated terminal for Git](#11-the-integrated-terminal-for-git)
12. [Extensions in Windsurf](#12-extensions-in-windsurf)
13. [Keyboard shortcuts](#13-keyboard-shortcuts)
14. [Pricing](#14-pricing)
15. [Try It Yourself](#15-try-it-yourself)
16. [Common Mistakes](#16-common-mistakes)
17. [Summary](#17-summary)
18. [Sources](#18-sources)

---

## 1. History and background

Windsurf was created by Codeium, an AI coding company founded in 2022 that initially offered a free alternative to GitHub Copilot as a VS Code extension. In November 2024, Codeium pivoted: it rebranded to Windsurf and released the Windsurf Editor, an AI-native IDE built on a VS Code fork with Cascade as its centrepiece agent. By April 2025 the company had rebranded entirely from Codeium to Windsurf.

The acquisition saga began in mid-2025. OpenAI announced an approximately $3 billion acquisition bid, which collapsed over IP-sharing concerns with Microsoft (OpenAI's primary investor). **On July 11, 2025, Google paid $2.4 billion in a talent deal**: CEO Varun Mohan, co-founder Douglas Chen, and approximately 40 senior researchers joined Google, where they built Google Antigravity (see [07-antigravity.md](07-antigravity.md)). This left approximately 200 remaining Windsurf employees without the founding team. **Cognition AI**, the company behind the Devin autonomous coding agent, acquired what remained - the product, brand, IP and remaining team - for approximately $250 million. Jeff Wang became interim CEO.

Despite the turbulence, Windsurf continued shipping: Wave 13 (December 2025) added Git worktrees and parallel agent sessions; Windsurf 2.0 (April 2026) added the Agent Command Center, Spaces, and Devin integration. As of April 2026, Windsurf Pro has over 350 enterprise customers and more than 800,000 active monthly users.

---

## 2. Installing Windsurf

🪟 **Windows** / 🍎 **macOS** / 🐧 **Linux**

Download from [windsurf.com](https://windsurf.com). Windsurf provides installers for Windows (x64, ARM64), macOS (Apple Silicon, Intel) and Linux (AppImage, `.deb`).

On first launch, Windsurf offers to import your VS Code or Cursor settings, extensions and keybindings. This is a one-click migration. After signing in with a Codeium/Windsurf account, you gain access to Cascade.

The free tier is functional and includes Cascade with limited daily usage. No credit card is required to start.

---

## 3. Git basics - inherited from VS Code

Windsurf is a VS Code fork, so it includes the complete VS Code Git integration:

- **Source Control panel** (`Ctrl+Shift+G` / `Cmd+Shift+G`) with staging, unstaging, discard and the commit box
- **Line and hunk-level staging** via right-click in the diff editor
- **3-way merge editor** for conflict resolution
- **Source Control Graph** for commit history
- **Inline blame** with `git.blame.editorDecoration.enabled`
- **Gutter indicators** for added, modified and deleted lines
- **Branch management** via the Status Bar branch picker
- **Push, pull, fetch** via the Source Control panel overflow menu

Everything in [01-vs-code.md](01-vs-code.md) applies to Windsurf. The VS Code shortcuts work without modification.

---

## 4. Cascade - the Windsurf agent

Cascade is Windsurf's built-in agentic AI. It is not a sidebar chatbot bolted onto the editor - it is the primary AI interface, designed to maintain context across long work sessions without you needing to re-explain what you are doing.

**Opening Cascade**: click the Cascade icon in the Activity Bar, or press `Ctrl+L` / `Cmd+L`. The Cascade panel appears on the right side of the editor.

**Two modes**:

- **Code mode** - Cascade can read, create and edit files, run terminal commands, and take actions. This is the default mode and the one used for Git workflows.
- **Chat mode** - Cascade answers questions and explains code but does not make changes. Use this for understanding unfamiliar code before deciding what to do.

**Planning mode**: for complex tasks, Cascade creates a Todo list at the start of the conversation outlining the steps it plans to take. You can edit the plan by telling Cascade to modify specific steps. This is particularly useful for multi-step Git workflows where you want to review the plan before execution starts.

**Checkpoints**: Cascade creates a checkpoint of your working tree before making significant changes. If you dislike the result, click **Restore Checkpoint** to revert to the state before the agent started. This is a safety net that makes experimenting with agent-driven changes less risky.

**Auto-fix**: Cascade automatically fixes linting errors it introduces during a task, at no credit cost. This means commits produced by Cascade are less likely to fail your pre-commit lint hooks.

---

## 5. Flow awareness and Git context

Windsurf's most distinctive feature is **Flow awareness** - a shared context layer that tracks everything happening in the editor in real time:

- Files you open and edit
- Terminal commands you run (including Git commands)
- Clipboard contents
- Your navigation history (which files and sections you have visited)
- Conversation history with Cascade

This means Cascade can pick up on what you are doing without you explaining it. If you run `git diff main` in the terminal and then ask Cascade "what should I clean up before merging?", Cascade already has the diff in its context from watching the terminal. If you made several commits and then ask "summarise what I have done today", Cascade reads the git log from its terminal observation.

**Git-specific context that Flow awareness captures**:

- The current branch name (from the Status Bar)
- Recent `git` commands you ran in the terminal
- The diff of staged and unstaged changes (from watching the Source Control panel)
- Commit messages you recently wrote
- Merge conflict markers if you are in the middle of a merge

You do not need to paste diffs or explain your repository structure - Cascade updates its context from what you are actively doing.

---

## 6. AI-driven Git workflows with Cascade

Cascade in Code mode can execute complete Git workflows. Examples:

**Creating a feature branch and implementing a task**:

> "Create a branch called feature/dark-mode, implement a dark mode toggle in src/components/ThemeToggle.tsx, add the necessary CSS in src/styles/themes.css, run the tests to make sure nothing breaks, then commit with a conventional commit message."

Cascade creates the branch, implements the changes, runs `npm test` or your configured test command, fixes failures if it can, and commits.

**Reviewing staged changes before committing**:

> "Look at what I have staged and tell me if it is production-ready. Check for: console.log statements, TODO comments, hardcoded credentials, and any obvious bugs."

Cascade runs `git diff --staged` and reports its findings.

**Rebasing interactively**:

> "I have made 4 commits that should really be 2 clean commits. Squash them logically - keep the feature work in one commit and the tests in another."

Cascade looks at the recent commits, runs an interactive rebase, squashes appropriately and writes clean commit messages.

**Generating a pull request description**:

> "Compare this branch with main and write a pull request description covering what changed, why it changed and what a reviewer should focus on."

Cascade runs `git log main..HEAD` and `git diff main...HEAD`, reads the output and produces the description.

---

## 7. Git worktrees and parallel agents

**Wave 13 (December 24, 2025)** added first-class Git worktree support to Windsurf. Git worktrees allow multiple working trees from the same repository to be checked out simultaneously in different directories - each on a different branch - without interfering with each other.

In Windsurf, each worktree gets its own Cascade session. This enables **true parallel agent development**: you can have one Cascade session working on a feature branch, another on a bug fix branch, and a third on documentation, all at the same time from the same repository, without agents conflicting or stepping on each other's changes.

**Creating a worktree in Windsurf**:

From the Command Palette: `Git: Add Worktree`. Choose a branch and a local path. Windsurf opens the new worktree in a separate window with its own Cascade panel.

Alternatively, Cascade itself can create worktrees when asked:

> "Create a worktree for the branch fix/auth-bug at ~/projects/myapp-auth-fix and open it."

**The Arena Mode** (Wave 13 name for the multi-pane parallel Cascade UI): open multiple Cascade sessions side-by-side in panes or tabs within the same Windsurf window. Each pane shows a different Cascade session and its associated worktree. Monitor progress of multiple agents simultaneously.

---

## 8. The Windsurf 2.0 Agent Command Center

**Windsurf 2.0 (April 15, 2026)** introduced the Agent Command Center - a Kanban-style panel showing all Cascade sessions (local and cloud Devin sessions) grouped by status: Active, Blocked, Review Needed, Complete.

The Command Center allows you to:

- See all running agents at a glance without switching windows
- Monitor which agents are waiting for your input vs working autonomously
- Jump to any agent's session to review its progress
- Assign new tasks to idle agents
- Review and merge agent results directly from the panel

**Spaces** (also Windsurf 2.0) group related work together: a Space bundles Cascade sessions, PRs, files, notes and project context. When you return to a Space after switching tasks, all the context from previous sessions is preserved. Each Space corresponds to a logical unit of work (a feature, a sprint task, a bug investigation).

**Devin integration** (Windsurf 2.0): Devin is Cognition's cloud-based autonomous software engineer. From the Windsurf Agent Command Center, you can delegate tasks directly to Devin cloud agents. Devin operates in an isolated cloud VM, opens PRs, and reports back to you through the Command Center. You plan and design locally in Windsurf, then delegate execution to Devin when the task is well-specified.

---

## 9. Memories and rules

**Memories** persist context across Cascade sessions. When Cascade discovers something important about your project - your preferred commit message format, the testing framework you use, which directories contain which types of code - it can save this as a memory. Future sessions start with this context already loaded.

Memories are scoped per workspace. You can view and manage memories in the Cascade panel settings. Delete outdated memories when your project structure changes.

**Rules** are more explicit than memories. They are project-level instructions committed to your repository in `.windsurfrules` (at the repository root) or workspace-level instructions in Windsurf's settings. Rules tell Cascade how to behave consistently for everyone on the team.

**Example `.windsurfrules` for Git conventions**:

```
## Git conventions

Branch naming:
- Features: feature/<ticket>-<description>
- Fixes: fix/<ticket>-<description>
- Always use kebab-case

Commit messages:
- Follow conventional commits: feat, fix, docs, chore, test, refactor
- Subject line max 72 characters, imperative mood
- Include the ticket number in the body: Refs: #<ticket>

Before committing:
- Always run the test suite
- Check for console.log statements
- Ensure no hardcoded credentials or API keys

Pull requests:
- Title must match the feature/fix branch name pattern
- Always include a description with "What changed" and "Why"
```

---

## 10. The Cascade dedicated terminal

In Wave 13, Windsurf added the **Cascade Dedicated Terminal** - a separate zsh shell specifically configured for Cascade's command execution. Previously, Cascade ran commands in your interactive terminal, which could mix your session history with agent output and cause environment inconsistencies with tools like nvm or pyenv.

The dedicated terminal:

- Uses a fresh zsh environment that reads `.zshrc` for environment variables
- Is interactive - shell scripts that prompt for input work correctly
- Is isolated from your interactive terminal so agent commands do not pollute your history
- Shows all agent commands in a dedicated pane labelled "Cascade Terminal"

You can watch the agent's commands in real time and interrupt at any point by clicking **Stop** in the Cascade panel. The agent terminal persists between messages in the same session so Cascade can run multiple commands and remember the state.

---

## 11. The integrated terminal for Git

The interactive integrated terminal (`` Ctrl+` `` / `` Cmd+` ``) is separate from the Cascade dedicated terminal. Use it for your own Git commands while Cascade works in its dedicated terminal.

`GIT_EDITOR` is set to `windsurf --wait` so `git commit` and `git rebase -i` open files in Windsurf. Running `git rebase -i HEAD~3` in the Windsurf terminal opens the rebase todo file in a new editor tab with syntax highlighting.

The interactive terminal is also useful for running Git commands that you want Cascade to observe through Flow awareness. If you run `git log --oneline -20` before asking Cascade a question about your history, Cascade has already seen the output.

---

## 12. Extensions in Windsurf

Windsurf uses the VS Code Marketplace and is compatible with most VS Code extensions. Extensions migrate automatically from VS Code during the initial setup.

**Recommended Git extensions for Windsurf**:

- **GitLens** - full compatibility; inline blame complements Cascade's context awareness
- **GitHub Pull Requests** - create and review PRs without leaving Windsurf
- **GitLab Workflow** - pipeline status and MR workflows for GitLab users

Note that some Copilot-specific features in extensions may not work in Windsurf since Windsurf uses its own AI backend rather than GitHub Copilot. The Cascade agent handles the AI tasks that Copilot handles in VS Code.

---

## 13. Keyboard shortcuts

Windsurf inherits all VS Code shortcuts and adds:

| Action | Windows/Linux | Mac |
|---|---|---|
| Open Cascade | `Ctrl+L` | `Cmd+L` |
| New Cascade session | `Ctrl+Shift+L` | `Cmd+Shift+L` |
| Open Source Control panel | `Ctrl+Shift+G` | `Cmd+Shift+G` |
| Open terminal | `` Ctrl+` `` | `` Cmd+` `` |
| Commit | `Ctrl+Enter` (in commit box) | `Cmd+Enter` |
| Open Command Palette | `Ctrl+Shift+P` | `Cmd+Shift+P` |
| Accept Cascade suggestion | `Tab` | `Tab` |
| Stop Cascade | `Esc` (while agent is running) | `Esc` |
| Open Agent Command Center | `Ctrl+Shift+A` | `Cmd+Shift+A` |

---

## 14. Pricing

| Plan | Price | What is included |
|---|---|---|
| Free | $0 | 25 Cascade prompt credits/day, Supercomplete |
| Pro | $15/month | Full Cascade access, premium models, higher limits |
| Teams | $30/user/month | All Pro features, team management |
| Enterprise | Custom | SSO, HIPAA, FedRAMP, ITAR, custom contracts |

Windsurf's Pro tier is priced lower than Cursor's Pro tier ($15 vs $20). The free tier is more generous on daily limits than Cursor's Hobby tier, making Windsurf a popular choice for developers evaluating AI IDEs before committing to a subscription.

Model access is managed by Windsurf's infrastructure. Unlike Cursor, Windsurf does not support bring-your-own-key (BYOK) for custom API keys as of April 2026, though this is on their roadmap. The default models in Pro are SWE-1 (Windsurf's proprietary coding model), GPT-5.2 and Gemini 3 Flash.

---

## 15. Try It Yourself

**Exercise 1 - let Flow awareness capture your context**

Open a repository in Windsurf. Run `git log --oneline -10` in the terminal. Run `git diff HEAD~3` in the terminal. Then open Cascade and ask: "Based on what I have been looking at, what is the main area of work in the recent commits and are there any patterns worth noting?" See how Cascade incorporates the terminal output into its answer without you needing to paste it.

**Exercise 2 - use Cascade to prepare commits**

Make several changes to a file that cover two different concerns - for example, a bug fix and a style improvement mixed together. Open Cascade in Code mode and say: "Look at my uncommitted changes and organise them into two separate, logical commits. Create the commits in the right order with appropriate conventional commit messages." Review the commits with `git log` to verify.

**Exercise 3 - create a worktree for parallel work**

If you have two tasks to work on simultaneously, ask Cascade: "Create a Git worktree for a new branch called feature/task-two at a sibling directory called myproject-task-two." Open the worktree in a new Windsurf window. Now you have two Windsurf windows, each working on a different branch from the same repository, with independent Cascade sessions.

**Exercise 4 - add a `.windsurfrules` file**

Create a `.windsurfrules` file in your repository root with your team's commit message conventions (an example is shown in section 9). Commit it. Make a change, stage it, and ask Cascade to write a commit message. Verify that the generated message follows your rules. Ask a teammate to pull the repository and make a change - their Cascade should also follow the rules automatically.

**Exercise 5 - explore the Agent Command Center**

Start two Cascade sessions on different tasks. Open the Agent Command Center (`Ctrl+Shift+A` / `Cmd+Shift+A`) and see both sessions listed. Let both agents work for a moment, then pause one by clicking Stop. Switch to the other and continue it. See how the Command Center gives you oversight of multiple concurrent AI workflows.

---

## 16. Common Mistakes

Confusing the Cascade terminal with the interactive terminal is a common source of confusion. The Cascade dedicated terminal is for agent commands - you should not type in it. Your interactive terminal (`` Ctrl+` ``) is where you run your own Git commands. If you close the Cascade terminal pane by mistake, Cascade can no longer run commands until you restart the session.

Not setting up `.windsurfrules` in team projects means each developer's Cascade behaves differently. The rules file makes Cascade consistent across the team. Commit it to the repository so everyone gets the same AI behaviour automatically.

Ignoring Checkpoints when experimenting with agent-driven changes means you have no easy rollback path if the agent's output is not what you wanted. Check the Cascade panel for a Restore Checkpoint option before accepting large agent changes. If the agent has committed code you do not want, use `git reset --soft HEAD~1` to undo the last commit while keeping the changes for review.

Treating Windsurf Pro as identical to Cursor Pro in terms of capabilities is incorrect. Cursor Pro allows bring-your-own-key for any AI provider; Windsurf Pro does not. Cursor has more model options; Windsurf has fewer. Windsurf's Flow awareness is genuinely different from Cursor's Composer - it is less manually directed and more ambient, which suits some workflows better and others worse.

Assuming the Devin integration in Windsurf 2.0 means Devin is free to use. Devin is a separate Cognition product with its own pricing, distinct from the Windsurf subscription. Delegating to Devin from the Agent Command Center incurs Devin usage charges.

---

## 17. Summary

Windsurf is a VS Code fork built around Cascade, an agentic AI system that maintains real-time awareness of your edits, terminal commands and clipboard through Flow awareness. It inherits the complete VS Code Git experience and adds AI-driven Git workflows: branch creation, staging, committing, rebasing, conflict resolution and PR description generation, all from natural language instructions. Wave 13 (December 2025) added Git worktrees for parallel agent workflows in the Arena Mode multi-pane UI. Windsurf 2.0 (April 2026) added the Agent Command Center for oversight of multiple concurrent agent sessions, Spaces for project organisation, and Devin integration for delegating tasks to cloud-based autonomous agents. Memories and `.windsurfrules` allow team-level Git conventions to be enforced consistently across all Cascade sessions. Windsurf's Pro tier at $15/month is cheaper than Cursor's $20/month and the free tier is more generous on daily limits, making it a strong choice for developers evaluating AI-first editors.

---

## 18. Sources

- [Windsurf documentation](https://docs.windsurf.com)
- [Windsurf changelog](https://windsurf.com/changelog)
- [Cascade documentation](https://docs.windsurf.com/windsurf/cascade/cascade)
- [Windsurf Wave 13 announcement](https://x.com/windsurf/status/2003925433352364068)
- [Windsurf 2.0 Agent Command Center](https://docs.windsurf.com/windsurf/agent-command-center)
- [Windsurf 2.0 release - AlternativeTo](https://alternativeto.net/news/2026/4/windsurf-2-0-has-launched-with-a-new-agent-command-center-spaces-and-devin-integration/)
- [Google hires Windsurf CEO - CNBC](https://www.cnbc.com/2025/07/11/google-windsurf-ceo-varun-mohan-latest-ai-talent-deal-.html)
- [Windsurf/Codeium acquisition details - Yahoo Finance](https://finance.yahoo.com/news/more-details-emerge-windsurf-vcs-160000238.html)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# Cursor

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Cursor is an AI-first code editor built by Anysphere on a fork of Visual Studio Code. It inherits the entire VS Code experience - extensions, keybindings, settings, the Source Control panel and the integrated terminal - and adds a layer of AI capabilities that go significantly further than GitHub Copilot. For Git, this means not just AI commit message generation but agents that can create branches, stage changes, commit code, open pull requests and iterate on the output autonomously. As of April 2026, Cursor is the most widely adopted AI-first editor among professional developers, with over $1 billion in annual recurring revenue and a $29.3 billion valuation after its Series D.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Cursor](#2-installing-cursor)
3. [Git basics - inherited from VS Code](#3-git-basics---inherited-from-vs-code)
4. [AI commit message generation](#4-ai-commit-message-generation)
5. [Cursor Blame](#5-cursor-blame)
6. [Composer and Agent mode](#6-composer-and-agent-mode)
7. [AI-driven Git workflows in Agent mode](#7-ai-driven-git-workflows-in-agent-mode)
8. [Background Agents and Cloud Agents](#8-background-agents-and-cloud-agents)
9. [Cursor Rules and Git conventions](#9-cursor-rules-and-git-conventions)
10. [The integrated terminal for Git](#10-the-integrated-terminal-for-git)
11. [Extensions in Cursor](#11-extensions-in-cursor)
12. [Keyboard shortcuts](#12-keyboard-shortcuts)
13. [Pricing](#13-pricing)
14. [Try It Yourself](#14-try-it-yourself)
15. [Common Mistakes](#15-common-mistakes)
16. [Summary](#16-summary)
17. [Sources](#17-sources)

---

## 1. History and background

Cursor was created by Anysphere, founded in 2022 by MIT graduates Michael Truell, Sualeh Asif, Arvid Lunnemark and Aman Sanger. The initial version launched in 2023 as a VS Code fork with more deeply integrated AI completions than what Copilot offered as a plugin. Traction grew rapidly: a $60 million Series A in August 2024, followed by a $900 million Series B, then a **$2.3 billion Series D in November 2025 at a post-money valuation of $29.3 billion**, with Accel and Coatue co-leading and NVIDIA and Google joining as investors.

By early 2026 Cursor's reported annual recurring revenue had crossed $1 billion. The editor has approximately 500,000 paying subscribers. Co-founder Arvid Lunnemark left in October 2025 to found an AI safety organisation; the remaining three founders continue to run the company.

The core architectural decision was to fork VS Code rather than build a plugin. This gave Cursor access to VS Code's rendering pipeline and allowed AI to be integrated at a deeper level than an extension API permits - for example, rendering AI suggestions directly in the editor buffer with custom decorations, and building the Composer agent panel as a first-class UI element rather than a webview.

---

## 2. Installing Cursor

🪟 **Windows** / 🍎 **macOS** / 🐧 **Linux**

Download from [cursor.com](https://cursor.com). Cursor provides installers for Windows (x64, ARM64), macOS (Apple Silicon, Intel) and Linux (AppImage, `.deb`). There is no Homebrew cask or winget package; download from the website directly.

On first launch, Cursor offers to import your VS Code settings, extensions and keybindings. This is a one-click migration. After importing, your VS Code environment is reproduced in Cursor and you can continue from where you left off.

Cursor requires signing in to an Anysphere account to use AI features. The Hobby (free) tier is available without a credit card.

---

## 3. Git basics - inherited from VS Code

Because Cursor is a VS Code fork, it has the full VS Code Git integration:

- **Source Control panel** (`Ctrl+Shift+G` / `Cmd+Shift+G`) with staging, unstaging, discard and the commit box
- **Line and hunk-level staging** via right-click in the diff editor
- **3-way merge editor** for conflict resolution with Accept Incoming, Accept Current and Accept Both options
- **Source Control Graph** for commit history visualisation
- **Inline blame** with `git.blame.editorDecoration.enabled`
- **Gutter indicators** for added, modified and deleted lines
- **Timeline view** for per-file commit history
- **Branch management** via the Status Bar branch picker
- **Push, pull, fetch** via the Source Control panel overflow menu or the Status Bar sync button

Everything covered in [01-vs-code.md](01-vs-code.md) applies directly to Cursor. The VS Code shortcuts, the GitLens extension, the GitHub Pull Requests extension and the GitLab Workflow extension all work identically in Cursor.

---

## 4. AI commit message generation

Cursor generates commit messages from your staged diff using a sparkle icon in the commit message box of the Source Control panel, exactly as Copilot does in VS Code. The difference is that Cursor uses its own models (or your configured model) rather than requiring a Copilot subscription.

The generated messages respect conventional commit format and can be steered by a **Cursor Rule** (see section 9) that specifies your team's commit style. For example, a rule can enforce a specific prefix list (`feat:`, `fix:`, `docs:`, `chore:`), a maximum subject line length, or a requirement to reference issue numbers.

To regenerate: click the sparkle icon again. Each click produces a fresh message from the same diff.

---

## 5. Cursor Blame

Cursor Blame is a feature unique to Cursor, added in Cursor 2.4. It extends `git blame` with AI attribution - every line in a file is tagged not just with the commit that last modified it but with **how it was written**: human-authored, Tab-completion suggestion, or Agent-generated code.

To open Cursor Blame, right-click any line and select **Cursor Blame**, or run `Cursor: Show Blame` from the Command Palette. The gutter fills with a colour-coded annotation:

- **Human** - written directly by a developer
- **Tab** - accepted from a Cursor Tab (inline completion) suggestion
- **Agent** - produced by Cursor's Composer agent in a conversation

Clicking any annotation opens the associated context: for human edits, it shows the commit; for Tab completions, it shows the completion suggestion that was accepted; for Agent lines, it opens the Composer conversation that produced the change.

This is useful for code review and archaeology - when you find a line that needs explaining, Cursor Blame shows you not just when it was written but whether it came from a human decision or an AI suggestion, and which conversation led to it.

---

## 6. Composer and Agent mode

**Composer** is Cursor's primary AI interface, accessed with `Ctrl+I` / `Cmd+I` (inline, scoped to a single file) or from the Composer panel on the right side of the screen for multi-file tasks.

Composer has two modes:

**Normal mode** (default): you describe a task and Cursor proposes edits across one or more files. You review the proposed changes in the diff view and accept or reject each one. Cursor does not run commands or modify files without your review.

**Agent mode**: switch to Agent mode by clicking the **Agent** toggle in the Composer panel or pressing `Ctrl+Shift+I` / `Cmd+Shift+I`. In Agent mode, Cursor uses tools to act on your behalf:

- **Read files** - read any file in the repository to understand context
- **Edit files** - make changes across multiple files simultaneously
- **Run terminal commands** - execute shell commands including `git` commands
- **Browse the web** - search documentation when needed
- **Iterate** - run tests, read their output, fix failures and repeat

Agent mode is where Git integration becomes genuinely autonomous.

---

## 7. AI-driven Git workflows in Agent mode

In Agent mode, you can ask Cursor to perform complete Git workflows in natural language:

**Creating a branch and committing**:

> "Create a branch called feature/user-authentication, implement JWT token validation in src/auth/middleware.py, write unit tests in tests/test_auth.py, commit both files with an appropriate conventional commit message and push the branch."

Cursor runs `git checkout -b feature/user-authentication`, edits the files, runs your test suite, fixes any failures it can, commits with a generated message, and pushes. It reports what it did at each step.

**Reviewing your changes before committing**:

> "Look at all the changes in my working tree and tell me if they are ready to commit. Flag any issues: missing tests, debugging code left in, TODO comments, console.log statements."

Cursor reads the diff from `git diff` and reports its findings before you commit.

**Preparing a clean commit**:

> "I have several unrelated changes staged together. Look at the diff and split them into logical commits. Create a separate commit for each distinct change with an appropriate message."

Cursor reads the staged changes, unstages everything, then stages and commits in logical groups.

**Summarising a pull request**:

> "Look at the diff between this branch and main and write a pull request description explaining the changes, why they were made, and what reviewers should focus on."

Cursor runs `git diff main...HEAD`, reads the output and generates a PR description you can copy into GitHub.

> [!NOTE]
> Agent mode runs real Git commands. Commits it creates are real commits on your branch. Pushes it makes are real pushes to your remote. Always review what the agent has done before considering the task complete - use `git log` and `git diff` in the terminal to verify.

---

## 8. Background Agents and Cloud Agents

**Background Agents** (launched May 2025) run in the background on your local machine while you work on other things. You assign a task in the Agents panel and the agent works on it asynchronously, including making Git commits. When it is done, you review the result.

**Cloud Agents with Computer Use** (launched February 24, 2026) run in isolated cloud VMs with a full desktop environment - a browser, a file system and the ability to run tests. Cloud Agents can:

- Clone a repository into a cloud VM
- Implement changes across multiple files
- Run a full test suite
- Fix test failures
- Commit the result with a descriptive message
- Open a pull request on GitHub
- Respond to PR review comments and push fixes

Anysphere reports that approximately 35% of Cursor's own merged pull requests are now opened by Cloud Agents.

**Cursor 3 (April 2026)** introduced the **Agents Window** - a dedicated panel for orchestrating multiple agents across local, worktree, SSH and cloud targets. Each agent gets its own branch via Git worktrees, allowing true parallel development.

To use Cloud Agents, open the Agents panel and click **+ New Cloud Agent**. Assign a GitHub issue, a task description or a PR review comment. The agent spins up a VM, does the work and reports back with a link to the resulting PR.

---

## 9. Cursor Rules and Git conventions

Cursor Rules (stored in `.cursor/rules/*.mdc` files, committed to your repository) tell the AI how to behave consistently across all conversations in the project. They are particularly useful for enforcing Git conventions across a team.

**Example commit message rule** (`.cursor/rules/git-conventions.mdc`):

```markdown
---
description: Git commit message conventions for this project
alwaysApply: true
---

# Commit message conventions

All commit messages MUST follow conventional commits format:

- feat: new feature
- fix: bug fix
- docs: documentation only
- style: formatting, no logic change
- refactor: code restructuring
- test: adding or fixing tests
- chore: build process, tooling

Subject line rules:

- Maximum 72 characters
- Imperative present tense: "add feature" not "added feature"
- No period at the end

Body rules:

- Reference issue numbers: "Closes #123"
- Explain WHY not WHAT (the diff shows what)
- Wrap at 72 characters

Branch naming:

- Feature branches: feature/<short-description>
- Bug fix branches: fix/<issue-number>-<short-description>
- Release branches: release/v<version>
```

With this rule, every AI commit message Cursor generates in this repository follows your conventions automatically. The rule is committed to the repository so every team member's Cursor instance applies it.

**Example branch naming rule** (`.cursor/rules/branching.mdc`):

```markdown
---
description: Branch naming for this project
globs: ["**/*"]
---

When creating branches, always use kebab-case.
Feature branches: feature/<ticket-number>-<description>
Example: feature/AUTH-42-add-oauth-login

When committing, always add the ticket number to the commit message.
Example: "feat(auth): add OAuth login [AUTH-42]"
```

> [!NOTE]
> `.cursor/rules/*.mdc` files replaced the older root-level `.cursorrules` file. The `.mdc` format supports YAML frontmatter for specifying when the rule applies (`alwaysApply`, `globs`, `fileMatch`). Both formats still work as of April 2026, but `.mdc` is the current standard.

---

## 10. The integrated terminal for Git

Cursor's integrated terminal is identical to VS Code's: `` Ctrl+` `` / `` Cmd+` `` to open, multiple tabs supported, `GIT_EDITOR` is set to `cursor --wait` so `git commit` and `git rebase -i` open files in Cursor.

**Copilot in the terminal** (if you have Copilot): `Ctrl+I` / `Cmd+I` in the terminal opens an inline AI prompt where you can type a description like "show me commits that touched src/auth.py in the last month" and get the corresponding command.

**Agent in the terminal**: in Agent mode, when Cursor runs terminal commands as part of a task, they appear in a dedicated terminal tab labelled "Agent Terminal". You can watch the agent's commands in real time and interrupt if needed by clicking **Stop**.

The agent terminal uses a separate shell session from your interactive terminal so your working directory and history are not mixed with the agent's commands.

---

## 11. Extensions in Cursor

Cursor is compatible with most VS Code extensions. It uses the VS Code Marketplace by default (unlike Kiro, which uses OpenVSX). Extensions you installed in VS Code are imported automatically during the setup migration.

**Git-related extensions that work in Cursor**:

- **GitLens** - full compatibility including Pro features if you have a GitLens Pro subscription
- **GitHub Pull Requests** - create and review PRs from inside Cursor
- **GitLab Workflow** - pipeline status and MR creation for GitLab users
- **Git Graph** - a commit graph visualiser that complements the built-in Source Control Graph
- **Git History** - per-file and per-commit history viewer

> [!TIP]
> GitLens and Cursor Blame serve overlapping but distinct purposes. GitLens blame shows commit metadata (author, date, message). Cursor Blame additionally shows the attribution type (human, Tab, Agent) and links to the original AI conversation. Both are useful and can be used together.

---

## 12. Keyboard shortcuts

Cursor inherits all VS Code shortcuts and adds:

| Action                     | Windows/Linux                | Mac                        |
| -------------------------- | ---------------------------- | -------------------------- |
| Open Composer (inline)     | `Ctrl+K`                     | `Cmd+K`                    |
| Open Composer (panel)      | `Ctrl+I`                     | `Cmd+I`                    |
| Open Composer (Agent mode) | `Ctrl+Shift+I`               | `Cmd+Shift+I`              |
| Open Chat                  | `Ctrl+L`                     | `Cmd+L`                    |
| Accept AI suggestion (Tab) | `Tab`                        | `Tab`                      |
| Reject AI suggestion       | `Esc`                        | `Esc`                      |
| Open Source Control panel  | `Ctrl+Shift+G`               | `Cmd+Shift+G`              |
| Open terminal              | `` Ctrl+` ``                 | `` Cmd+` ``                |
| Commit                     | `Ctrl+Enter` (in commit box) | `Cmd+Enter`                |
| Open Agents panel          | `Ctrl+Shift+J`               | `Cmd+Shift+J`              |
| Show Cursor Blame          | right-click > Cursor Blame   | right-click > Cursor Blame |

---

## 13. Pricing

| Plan     | Price          | What is included                                       |
| -------- | -------------- | ------------------------------------------------------ |
| Hobby    | $0             | 2,000 Tab completions/month, 50 slow premium requests  |
| Pro      | $20/month      | $20 usage credits, fast requests, most models          |
| Pro+     | $60/month      | $60 usage credits, all models, priority                |
| Ultra    | $200/month     | $200 usage credits, maximum limits                     |
| Business | $40/user/month | All Pro features, SSO/SAML, admin controls, audit logs |

Cursor moved from a fixed-request model to usage-based credits in June 2025. Each AI operation costs a variable number of credits depending on the model used and the size of the context. Simpler models (Claude Haiku 4.5, GPT-4o-mini) cost fewer credits than frontier models (Claude Opus 4.6, GPT-5). Cloud Agent tasks on frontier models can consume credits quickly.

Background Agent usage and Cloud Agent tasks draw from the same credit pool. Monitor your usage in `Cursor > Account > Usage`.

---

## 14. Try It Yourself

**Exercise 1 - AI commit message with a custom rule**

Create a `.cursor/rules/git-conventions.mdc` file in your repository with a simple rule: commit messages must use conventional commit format and be under 72 characters. Make a meaningful change to a file, stage it, and click the sparkle icon in the commit message box. Verify that the generated message follows your rule.

**Exercise 2 - use Agent mode for a Git task**

Open Composer in Agent mode. Type: "Look at all my uncommitted changes and write a summary of what I have done that I can use as a commit message. Then stage everything and commit with that message." Watch Cursor run `git diff`, generate the message and execute the commit. Review the result with `git log` in the terminal.

**Exercise 3 - explore Cursor Blame**

Open a file with a reasonable commit history in a repository where you have used Cursor for some time. Right-click a line and choose **Cursor Blame**. See which lines are attributed to human edits, Tab completions and Agent runs. Click an Agent annotation and see if it links to the original conversation.

**Exercise 4 - Branch with an Agent**

Open Composer in Agent mode and type: "Create a new branch called feature/test-agent-workflow, add a new file called AGENT_TEST.md with a brief description of what this branch is for, commit it with a conventional commit message and show me the git log for this branch." Watch the agent execute the full workflow. Verify the result in the Source Control panel and `git log`.

**Exercise 5 - PR description from a diff**

Push a feature branch that has several commits. Open Composer in Agent mode and type: "Compare this branch with main and write a pull request description I can use on GitHub. Include a summary, a list of changes and any testing notes." Review the output and use it when opening the PR.

---

## 15. Common Mistakes

Treating Agent mode Git operations as safe to ignore is a significant risk. When an agent commits and pushes, those actions are real and permanent on your remote. Always review `git log` after an agent task and verify that the commits contain what you expected before treating the task as done.

Using `.cursorrules` at the root instead of `.cursor/rules/*.mdc` is not wrong (it still works) but it is the older format. New projects should use the `.mdc` format in the `.cursor/rules/` directory. Teams should commit these rule files to the repository so all members get consistent AI behaviour.

Over-relying on Cursor Blame for code attribution without understanding that AI attribution is not perfectly accurate. Cursor Blame tracks Tab completions and Agent runs within a specific Cursor session and repository. If you switch machines, use a different editor, or the session data is lost, the attribution may be incomplete or missing.

Running out of credits mid-task without monitoring usage. Cursor's credit-based billing means a complex multi-file Agent task on a frontier model can consume a significant portion of a Pro plan's monthly allocation. Check your usage regularly and consider using a less expensive model (Claude Haiku, GPT-4o-mini) for routine tasks.

Forgetting that Cursor is a VS Code fork means forgetting that all VS Code Git documentation applies. Most Cursor Git questions are answered by VS Code documentation, not Cursor-specific documentation.

---

## 16. Summary

Cursor is a VS Code fork that inherits the complete VS Code Git experience - Source Control panel, line-level staging, 3-way merge editor, Source Control Graph, inline blame, Timeline view - and adds AI capabilities that go significantly beyond Copilot. AI commit message generation is built in. Cursor Blame extends `git blame` with AI attribution showing which lines came from human edits, Tab completions, or Agent conversations. Agent mode gives the AI access to the terminal and file system, enabling complete autonomous Git workflows: branch creation, staging, committing, pushing and pull request generation, all from natural language instructions. Background and Cloud Agents extend this to asynchronous and cloud-based execution. Cursor Rules committed to the repository enforce team Git conventions consistently across all AI interactions. The VS Code extension ecosystem works without modification. As of April 2026, Cursor is the most mature and widely adopted AI-first editor for production Git workflows.

---

## 17. Sources

- [Cursor documentation](https://docs.cursor.com)
- [Cursor changelog](https://cursor.com/changelog)
- [Cursor Git documentation](https://cursor.com/help/integrations/git)
- [Cursor Rules documentation](https://docs.cursor.com/context/rules)
- [Anysphere Series D announcement](https://www.goodwinlaw.com/en/news-and-events/news/2025/11/announcements-technology-goodwin-advises-cursor-on-2-billion-series-b-financing)
- [Cursor Background Agents](https://madewithlove.com/blog/using-cursor-background-agents/)
- [Cursor Cloud Agents announcement](https://devops.com/cursor-cloud-agents-get-their-own-computers-and-35-of-internal-prs-to-prove-it/)
- [Cursor 2.4: Cursor Blame release](https://forum.cursor.com/t/cursor-2-4-cursor-blame/149405)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

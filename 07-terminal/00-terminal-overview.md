# Terminal Overview

**Difficulty:** 🟢 Beginner | **Time:** 5 minutes

This folder covers everything you need to work with Git confidently from the command line. It starts from the absolute basics - what a terminal is and how it differs from a shell - and builds up through shell setup on every platform, safe working habits, deep Git configuration and the modern ecosystem of terminal tools that make Git workflows faster and clearer.

You do not need to be a terminal expert to start here. Every file covers Windows, Mac and Linux side by side, and every concept is explained from scratch.

---

## Table of Contents

1. [What this section covers](#1-what-this-section-covers)
2. [File index](#2-file-index)
3. [How to use this section](#3-how-to-use-this-section)
4. [Prerequisites](#4-prerequisites)

---

## 1. What this section covers

Most Git tutorials assume you already know your way around a terminal. This section does not. It explains what a terminal is, how it differs from a shell and a command prompt, why Windows has three different ways to run commands, what those `$` and `>` symbols actually mean, and how Git fits into all of it.

From there it moves into practical territory: configuring Git properly, writing aliases that save you time every day, and the tools that make terminal Git genuinely pleasant to use - better diffs with delta, a full TUI with lazygit, fuzzy finding with fzf and more.

The section closes with a full workflow walkthrough that pulls everything together.

---

## 2. File index

| File                                                                     | Topic                                                                  | Level |
| ------------------------------------------------------------------------ | ---------------------------------------------------------------------- | ----- |
| [01-what-is-a-terminal.md](01-what-is-a-terminal.md)                     | Terminal vs shell vs CLI vs command prompt - what each term means      | 🟢    |
| [02-shells-and-platforms.md](02-shells-and-platforms.md)                 | bash, zsh, fish, PowerShell, CMD, WSL2 - setup on every platform       | 🟢    |
| [03-terminal-and-git.md](03-terminal-and-git.md)                         | How Git runs in the terminal, how IDEs connect to the same binary      | 🟢    |
| [04-terminal-safety.md](04-terminal-safety.md)                           | Dangerous commands, destructive Git operations, API keys in history    | 🟡    |
| [05-git-config-in-depth.md](05-git-config-in-depth.md)                   | Global/local/system config levels, .gitconfig anatomy, useful settings | 🟡    |
| [06-git-aliases.md](06-git-aliases.md)                                   | Simple aliases, shell function aliases, sharing across machines        | 🟡    |
| [07-delta.md](07-delta.md)                                               | Syntax-highlighted diffs and better git output with delta              | 🟢    |
| [08-lazygit.md](08-lazygit.md)                                           | Full terminal UI for Git - all panels, keybindings and config          | 🟡    |
| [09-fzf-and-git.md](09-fzf-and-git.md)                                   | Fuzzy finding for branches, commits, files and stashes                 | 🟡    |
| [10-bat-and-tig.md](10-bat-and-tig.md)                                   | bat for syntax-highlighted output, tig for log browsing                | 🟢    |
| [11-other-tools.md](11-other-tools.md)                                   | gitui, starship prompt, zoxide, forgit and diff-so-fancy               | 🟡    |
| [12-terminal-git-workflow.md](12-terminal-git-workflow.md)               | A complete real-world workflow combining all tools                     | 🟡    |
| [13-all-terminal-tools-reference.md](13-all-terminal-tools-reference.md) | Complete comparison tables and quick reference                         | 🟢    |

---

## 3. How to use this section

If you are new to the terminal, read files 01 through 03 before anything else. They build the mental model you need for everything that follows.

If you already know your way around a terminal and just want to configure Git properly, jump to [05-git-config-in-depth.md](05-git-config-in-depth.md).

If you want better tooling immediately, start with [07-delta.md](07-delta.md) (five minutes to a noticeably better `git diff`) and [08-lazygit.md](08-lazygit.md) (the single biggest quality-of-life upgrade for terminal Git).

The tools in files 07 through 11 are independent of each other - install any combination you find useful.

---

## 4. Prerequisites

- Git installed ([git/02-installing-git.md](../02-git/02-installing-git.md))
- A terminal open and ready (any terminal on any platform is fine)
- No prior terminal experience required for files 01-03

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

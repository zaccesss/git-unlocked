# Zed

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Zed is a high-performance code editor built entirely in Rust by the creators of the Atom editor and Tree-sitter. It uses a custom GPU-accelerated rendering framework called GPUI instead of Electron, which gives it startup times and input latency that graphical editors built on web technology cannot match. Zed added native Git integration in March 2025, making it the most recent mainstream editor to build Git into its core. It now covers staging, committing, diffs, conflict resolution, inline blame and AI commit messages. Windows support was added in 2026, making Zed available on all three major platforms. It is a strong choice for developers who want a modern, fast editor with growing Git capabilities and do not want Electron overhead.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Zed](#2-installing-zed)
3. [Native Git integration](#3-native-git-integration)
4. [The Git panel](#4-the-git-panel)
5. [Staging and the Diff View](#5-staging-and-the-diff-view)
6. [Committing](#6-committing)
7. [AI commit message generation](#7-ai-commit-message-generation)
8. [Inline blame](#8-inline-blame)
9. [Conflict resolution](#9-conflict-resolution)
10. [Branching and remotes](#10-branching-and-remotes)
11. [Multiplayer and collaboration](#11-multiplayer-and-collaboration)
12. [AI integration](#12-ai-integration)
13. [The integrated terminal for Git](#13-the-integrated-terminal-for-git)
14. [Extensions](#14-extensions)
15. [Keyboard shortcuts](#15-keyboard-shortcuts)
16. [Pricing](#16-pricing)
17. [Try It Yourself](#17-try-it-yourself)
18. [Common Mistakes](#18-common-mistakes)
19. [Summary](#19-summary)
20. [Sources](#20-sources)

---

## 1. History and background

Zed was founded in 2021 by Nathan Sobo, Max Brunsfeld and Antonio Scandurra - the same team that built the Atom editor at GitHub (Atom was sunset in December 2022). Rather than building on Electron as Atom had, the Zed team wrote a custom GPU-accelerated UI framework called GPUI in Rust. GPUI renders Zed like a game engine: it maintains a scene graph, diffs it between frames and sends draw calls to the GPU directly - Metal on macOS, Vulkan on Linux, DirectX 11 on Windows. This eliminates the DOM layout, CSS cascade and JavaScript event loop overhead that Electron editors carry.

Max Brunsfeld is the creator of Tree-sitter, the incremental parsing framework that gives Neovim and Helix their precise syntax highlighting. Tree-sitter is built into Zed for all language support.

Zed was macOS-only until mid-2024 when it added Linux support. **Windows support launched in 2026** with full feature parity, making Zed available on all three platforms. Zed is backed by Sequoia Capital with a $32 million investment round in 2025.

The native Git integration launched on **March 13, 2025** (v0.177) as a major milestone that many users had been waiting for before fully switching from VS Code. It has grown steadily since. The current stable release is **v0.232** (April 2026).

---

## 2. Installing Zed

🍎 **macOS**

```bash
brew install --cask zed
```

Or download from [zed.dev](https://zed.dev). Zed supports Apple Silicon and Intel; the download page detects your architecture automatically.

🐧 **Linux**

```bash
curl -f https://zed.dev/install.sh | sh
```

Or download the tarball from the website. The install script places the `zed` binary at `~/.local/bin/zed`.

🪟 **Windows**

Download from [zed.dev](https://zed.dev). Windows support (x86_64 and aarch64) was added in 2026. WSL integration is included.

Zed requires Git to be installed separately. Open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) and run `zed: install cli` to add the `zed` command to your PATH.

---

## 3. Native Git integration

Zed's Git integration is built directly into the editor core in Rust using the `gitoxide` library (a pure-Rust Git implementation). It does not shell out to the `git` binary for diff computation and sign operations, which contributes to its speed advantage over editors that invoke the `git` CLI for every diff update.

Key Git features as of April 2026:

- Git panel with file-level staging and status
- Diff View for hunk-level and line-level staging
- Commit with message, amend and AI generation
- Inline blame annotations
- Gutter diff indicators (added/modified/deleted)
- Conflict resolution with accept-ours/theirs/both
- Push and pull via the Command Palette
- Branch switching via the Status Bar
- `Co-Authored-By` trailers for multiplayer sessions

Features not yet in Zed's Git UI as of April 2026 (use the terminal):

- Interactive rebase UI
- Stash management UI
- Submodule management
- Full commit log / graph view
- Cherry-pick UI

---

## 4. The Git panel

The Git panel is the primary staging interface. Open it with `Ctrl+Shift+G` / `Cmd+Shift+G` or by clicking the Git icon in the left sidebar.

The panel shows all modified files grouped into:

- **Staged** - files added to the index
- **Unstaged** - modified tracked files not yet staged
- **Untracked** - new files Git has not seen

Each file shows a coloured icon: green for added, yellow/orange for modified, red for deleted. A **badge** on the Git panel icon in the sidebar shows the count of uncommitted changes (added in v0.229, March 2026).

**File-level operations**: right-click any file in the Git panel to:

- Stage / Unstage
- Revert to HEAD (discard changes)
- Open the file
- Open the diff

Click any file to open its diff in the Diff View.

---

## 5. Staging and the Diff View

The **Diff View** is Zed's approach to reviewing and staging changes. It differs from VS Code's diff editor in a key architectural decision: staged and unstaged hunks are shown interleaved in a single buffer rather than split across two separate panes.

Deleted lines are first-class members of the text coordinate system in Zed's Diff View. This means:

- You can select text inside deleted (red) regions
- You can search inside deleted regions with `Ctrl+F` / `Cmd+F`
- `SelectAllMatches` works across both added and deleted content

This makes it possible to review complex diffs where understanding removed code is as important as understanding added code.

**Staging operations in the Diff View**:

| Action | Shortcut |
|---|---|
| Stage hunk under cursor | `Cmd+Y` (Mac) / `Ctrl+Y` (Win/Linux) |
| Unstage hunk under cursor | `Shift+Cmd+Y` / `Shift+Ctrl+Y` |
| Stage all changes | `Cmd+Shift+A` in Git panel |
| Unstage all | `Cmd+Shift+U` in Git panel |
| Next hunk | `]h` |
| Previous hunk | `[h` |

**Hunk-level staging**: position your cursor within a hunk and press `Cmd+Y` to stage only that hunk. Position in a different hunk and stage it separately. This gives you fine-grained control over what goes into each commit.

**Split diffs** became the default in v0.224 (February 2026), showing before and after side-by-side for each changed file rather than the interleaved single-buffer view. Toggle between the two modes from the Diff View toolbar.

---

## 6. Committing

The commit message box is at the bottom of the Git panel. Type the subject on the first line. Zed shows a character counter and wraps at 72 characters following Git convention. Press `Cmd+Enter` / `Ctrl+Enter` to commit staged changes.

**Amending**: click the **Amend** checkbox in the Git panel to amend the previous commit. The previous commit message is loaded into the box.

**Committing all tracked files without staging**: run `git: Commit` from the Command Palette with no files staged - Zed offers to commit all changes to tracked files directly, equivalent to `git commit -a`. This is the "quick save to a branch" workflow for developers who prefer to stage selectively only before significant commits.

---

## 7. AI commit message generation

Zed can generate commit messages using your configured AI model. Click the sparkle icon in the commit message box in the Git panel, or run `git: generate commit message` from the Command Palette.

The generated message is based on the staged diff. You can configure the model used via `agent.commit_message_model` in your Zed settings:

```json
{
  "agent": {
    "commit_message_model": {
      "provider": "anthropic",
      "model": "claude-sonnet-4-6"
    }
  }
}
```

Any model configured in Zed's AI settings (Anthropic, OpenAI, Gemini, local Ollama) can generate commit messages.

---

## 8. Inline blame

Enable inline blame with `editor: toggle git blame` from the Command Palette, or add to your settings:

```json
{
  "git": {
    "inline_blame": {
      "enabled": true,
      "delay_ms": 600
    }
  }
}
```

Blame annotations appear at the end of the current line in faded text showing the author's name, the relative time and the first line of the commit message. Hovering over the annotation shows the full commit hash, the author email and the complete commit message.

Click any blame annotation to open the full commit details in a panel, including a list of all files changed in that commit and their diffs.

**Gutter indicators** appear in the column to the left of line numbers:

- Green bar: line added since last commit
- Yellow bar: line modified since last commit
- Red triangle: line deleted at this position

Click any gutter indicator to open the hunk diff for that change inline.

---

## 9. Conflict resolution

When a merge, rebase or pull produces conflicts, Zed opens the conflicting files with standard conflict markers highlighted in colour. Each conflict region has three sections:

- **Current** (green) - your changes on the current branch
- **Separator** - the `=======` divider
- **Incoming** (blue) - changes from the branch being merged

Above each conflict, Zed renders clickable action labels:

- **Accept Current** - keep your changes, discard incoming
- **Accept Incoming** - keep incoming changes, discard yours
- **Accept Both** (current first) - keep both in sequence
- **Accept Both** (incoming first) - keep both in reverse sequence

For conflicts requiring manual editing, click either Accept option and then edit the result directly in the buffer. Delete the parts you do not want, keep what you do and save.

**AI conflict resolution**: with an AI model configured, run `assistant: resolve conflict` from the Command Palette. Zed sends the conflict to your configured model and proposes a resolution in the buffer. Review and accept or modify the proposal.

After resolving all conflicts in a file, stage it from the Git panel. Once all conflicting files are staged, commit the merge.

---

## 10. Branching and remotes

**Current branch**: shown in the Status Bar at the bottom left. Click it to open the branch switcher.

**Switching branches**: click the branch name in the Status Bar and select from the list, or type to filter. Zed shows local branches with their ahead/behind counts relative to the remote.

**Creating a branch**: in the branch switcher, type a new branch name and press Enter. Zed creates and checks out the branch.

**Push and pull**: run `git: push` or `git: pull` from the Command Palette. Zed shows a notification when the operation completes.

**Fetch**: run `git: fetch` from the Command Palette to update remote-tracking branches.

For more complex remote operations (multiple remotes, force push with lease, upstream configuration), use the integrated terminal.

---

## 11. Multiplayer and collaboration

Multiplayer is one of Zed's most unique features and has a direct impact on Git workflows. Multiple developers can edit the same file simultaneously in real time using a CRDT (Conflict-free Replicated Data Type) approach - similar to how Google Docs works but for code.

When collaborating in a multiplayer session, Zed automatically adds `Co-Authored-By` trailers to commits for every participant in the session:

```
feat(ui): implement dark mode toggle

Co-Authored-By: Alice <alice@example.com>
Co-Authored-By: Bob <bob@example.com>
```

This correctly attributes the commit to everyone who worked on it, following the GitHub convention for co-authorship attribution in pull requests.

**Voice and video**: Zed includes built-in voice calls and screen sharing for multiplayer sessions, without requiring a separate tool like Discord or Zoom.

---

## 12. AI integration

Zed integrates with multiple AI providers for code assistance and commit message generation:

- **Anthropic** (Claude Sonnet 4.6, Opus 4.6 and others)
- **OpenAI** (GPT-5 and variants)
- **Google** (Gemini models)
- **xAI** (Grok)
- **Local models** via Ollama
- **AWS Bedrock** for enterprise
- **OpenRouter** for model aggregation
- **GitHub Copilot** (available to Copilot subscribers from February 2026)

Configure your provider in Zed's settings:

```json
{
  "language_models": {
    "anthropic": {
      "api_key": "sk-ant-..."
    }
  }
}
```

**Agent mode**: Zed's AI assistant can be put into agent mode where it reads files, runs commands and makes edits. This is less developed than Cursor's or Windsurf's agent implementations as of April 2026, but the foundation is in place.

**Zeta2** (Zed's proprietary edit prediction model, launched April 7, 2026): an on-device model trained specifically for code editing rather than generation. It predicts multi-cursor edits - if you rename a variable in one place, Zeta2 predicts you want to rename it consistently elsewhere and offers to do so. This is distinct from general-purpose AI completions.

---

## 13. The integrated terminal for Git

Open the integrated terminal with `` Ctrl+` `` / `` Cmd+` ``. Zed's terminal is native (not Electron-based), which makes it fast and low-latency.

`GIT_EDITOR` is set to `zed --wait` so `git commit` and `git rebase -i` open files in Zed. Running `git rebase -i HEAD~3` in the Zed terminal opens the rebase todo file in the editor. Save and close to continue the rebase.

Zed's terminal fully supports WSL on Windows - you can run a Linux shell inside Zed on Windows with all Git tools available.

**Useful Git operations to do from the terminal** (not yet in the Zed Git UI):

```bash
# Interactive rebase (no Zed UI yet)
git rebase -i HEAD~5

# Stash management
git stash push -m "WIP: dark mode"
git stash list
git stash pop

# Cherry-pick
git cherry-pick abc1234

# View commit graph
git log --oneline --graph --all --decorate
```

---

## 14. Extensions

Zed has approximately 700 extensions as of early 2026, compared to VS Code's 50,000+. Most common languages and workflows are covered, but niche tools may not have extensions yet. Check [zed.dev/extensions](https://zed.dev/extensions).

Extensions are written in WebAssembly (WASM) and run sandboxed - they cannot directly access the file system or network. This differs from VS Code where extensions run as Node.js processes with broader permissions.

**Git-related extensions for Zed**:

- No direct equivalent of GitLens exists yet in Zed - the built-in blame, gutter indicators and Diff View cover most of what beginners use GitLens for
- The GitHub Copilot integration is now built in (for subscribers)
- Some community extensions add additional Git workflow helpers - check the extension marketplace in Zed

For advanced Git history browsing and PR review, use the GitHub CLI in the terminal or open GitHub in your browser.

---

## 15. Keyboard shortcuts

| Action | Windows/Linux | Mac |
|---|---|---|
| Open Git panel | `Ctrl+Shift+G` | `Cmd+Shift+G` |
| Open terminal | `` Ctrl+` `` | `` Cmd+` `` |
| Commit | `Ctrl+Enter` (in commit box) | `Cmd+Enter` |
| Stage hunk | `Ctrl+Y` | `Cmd+Y` |
| Unstage hunk | `Ctrl+Shift+Y` | `Cmd+Shift+Y` |
| Toggle inline blame | Command Palette: `editor: toggle git blame` | same |
| Next hunk | `]h` | `]h` |
| Previous hunk | `[h` | `[h` |
| Open Command Palette | `Ctrl+Shift+P` | `Cmd+Shift+P` |
| Push | Command Palette: `git: push` | same |
| Pull | Command Palette: `git: pull` | same |

---

## 16. Pricing

Zed has a straightforward pricing model:

| Plan | Price | What is included |
|---|---|---|
| Free | $0 | Full editor, all Git features, BYOK AI, 2,000 Zeta edit predictions/month |
| Pro | $5/month credit | Full editor + $5 AI credit at 10% markup over API costs |
| Student | $10/month credit | Verified via GitHub Student Pack |
| Enterprise | Custom | Custom contracts, SSO, priority support |

Zed's AI usage is billed at API list price plus 10% - you pay for what you use rather than a flat monthly fee. For developers who use AI sparingly, the Free tier with bring-your-own-key is effectively unlimited at no cost. GitHub Copilot subscribers can use their existing Copilot subscription in Zed without additional charges.

---

## 17. Try It Yourself

**Exercise 1 - stage individual hunks**

Open a file and make changes in two separate places for two different purposes. Open the Git panel and click the file to open the Diff View. Position your cursor in the first hunk and press `Cmd+Y` / `Ctrl+Y` to stage only that hunk. Verify in the Git panel that the file now shows partial staging (some changes staged, some not). Stage the second hunk separately and commit them in two separate commits.

**Exercise 2 - enable and explore inline blame**

Open a file with a meaningful commit history. Run `editor: toggle git blame` from the Command Palette. Read through the file and observe the blame annotations. Hover over several annotations to see the full commit details. Click one annotation to open the commit details panel. Find a line that looks like it might be a bug and use blame to determine which commit introduced it.

**Exercise 3 - resolve a conflict in Zed**

Create two branches from the same commit and edit the same line differently on each. Merge one into the other. Open the conflicting file in Zed. Find the conflict markers - they should be highlighted in green and blue. Click **Accept Current** on one conflict and **Accept Incoming** on another to practice both options. Edit the result of a third conflict manually if you have one. Stage all resolved files and commit the merge.

**Exercise 4 - generate an AI commit message**

Configure an AI provider in Zed settings (Anthropic, OpenAI or your Copilot account). Make a meaningful change to several files. Stage them all. Click the sparkle icon in the commit box. Read the generated message. Edit it if needed and commit. Try changing the `agent.commit_message_model` setting to a different model and compare the messages generated for the same diff.

**Exercise 5 - multiplayer collaboration**

If you have a colleague using Zed, start a multiplayer session (`collab: open channel` or share a link via `collab: share project`). Both edit the same file simultaneously. Have one person stage and commit the changes. Check the resulting commit message - it should include `Co-Authored-By` trailers for both participants.

---

## 18. Common Mistakes

Expecting Zed's Git UI to cover all operations leads to frustration. Zed deliberately focuses on the most common operations - staging, committing, basic branching and conflict resolution. Interactive rebase, stash management, cherry-pick and commit graph browsing require the terminal. Keep a terminal tab open for these operations.

Overlooking the terminal integration for complex operations. Zed's terminal with `GIT_EDITOR=zed` set means you can run `git rebase -i` and get the todo file in Zed's editor. This is a better experience than most editors' rebase UIs because you get Zed's speed and keybindings for editing the file.

Not configuring an AI provider before expecting AI commit messages. Zed's AI features require an API key or Copilot subscription. The free tier with no key configured has no AI features. Add your key in settings or enable Copilot to unlock commit message generation.

Assuming the extension ecosystem will have VS Code equivalents for everything. Zed has 700 extensions compared to VS Code's 50,000. GitLens, in particular, has no Zed equivalent. The built-in Git features cover the core workflow, but advanced Git history browsing and PR management require the terminal or the browser.

Forgetting that Windows support is new in 2026 and may have rough edges. Zed's Windows release is relatively recent. If you encounter issues specific to Windows (WSL integration quirks, path handling), check the Zed GitHub issues tracker and the community Discord before assuming a fundamental limitation.

---

## 19. Summary

Zed is a GPU-accelerated native editor built in Rust by the creators of Atom and Tree-sitter. It runs on Windows, macOS and Linux. Native Git integration launched in March 2025 and covers file-level and hunk-level staging in a novel Diff View where deleted content is navigable like normal text, AI commit message generation using any configured AI provider, inline blame with commit details, gutter indicators, conflict resolution with accept-ours/theirs/both and AI-assisted resolution, push/pull/fetch via the Command Palette, and branch switching via the Status Bar. Zed's multiplayer real-time collaboration automatically adds `Co-Authored-By` trailers for all session participants. Complex operations - interactive rebase, stash management, cherry-pick, full commit graph - require the integrated terminal with `GIT_EDITOR=zed --wait` set. The extension ecosystem has approximately 700 WASM extensions, far fewer than VS Code's 50,000, so advanced Git UI tools like GitLens have no direct equivalent. The free tier includes the full editor and bring-your-own-key AI at no cost. Zed is the best choice for developers who want the fastest graphical editor experience, are comfortable using the terminal for advanced Git operations and do not need the VS Code extension breadth.

---

## 20. Sources

- [Zed documentation](https://zed.dev/docs)
- [Zed Git documentation](https://zed.dev/docs/git)
- [Native Git support in Zed - Zed blog](https://zed.dev/blog/git)
- [Zed native Git integration - Phoronix](https://www.phoronix.com/news/Zed-Editor-Native-Git)
- [Zed stable releases](https://zed.dev/releases/stable)
- [Zed editor 2026 guide - Petronella Technology](https://petronellatech.com/blog/zed-editor-setup-guide-2026)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

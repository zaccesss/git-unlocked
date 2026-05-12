# Neovim

**Difficulty:** 🔴 Advanced | **Time:** 40 minutes

Neovim is a terminal-based editor that extends Vim with a Lua scripting API, built-in LSP client, tree-sitter integration and an active plugin ecosystem. It has no built-in Git UI, but its plugin ecosystem provides the most keyboard-efficient Git workflow available in any editor. The standard stack in 2026 combines four plugins: vim-fugitive for full Git command access, gitsigns.nvim for inline diff indicators and hunk operations, neogit for a Magit-style porcelain, and diffview.nvim for dedicated diff and merge views. This file covers how to install and configure that stack and how to use it for the full daily Git workflow. It also covers Vim (classic), which shares vim-fugitive but not the Neovim-specific plugins.

---

## Table of Contents

1. [History and background](#1-history-and-background)
2. [Installing Neovim](#2-installing-neovim)
3. [Configuration and init.lua](#3-configuration-and-initlua)
4. [lazy.nvim - the plugin manager](#4-lazynvim---the-plugin-manager)
5. [vim-fugitive](#5-vim-fugitive)
6. [gitsigns.nvim](#6-gitsignsnvim)
7. [neogit](#7-neogit)
8. [diffview.nvim](#8-diffviewnvim)
9. [lazygit.nvim](#9-lazygitsnvim)
10. [A recommended Git configuration](#10-a-recommended-git-configuration)
11. [Daily Git workflow in Neovim](#11-daily-git-workflow-in-neovim)
12. [The integrated terminal for Git](#12-the-integrated-terminal-for-git)
13. [Classic Vim and vim-fugitive](#13-classic-vim-and-vim-fugitive)
14. [Try It Yourself](#14-try-it-yourself)
15. [Common Mistakes](#15-common-mistakes)
16. [Summary](#16-summary)
17. [Sources](#17-sources)

---

## 1. History and background

Vim was created by Bram Moolenaar in 1991 as an improvement on the Unix `vi` editor, adding programmability, syntax highlighting and an extension system via VimScript. Neovim was forked from Vim in 2014 by Thiago de Arruda with the goal of refactoring Vim's codebase for modern contributors and adding a first-class asynchronous plugin API. Where Vim used blocking VimScript for extensions, Neovim introduced a msgpack-based RPC API that let plugins be written in any language. Neovim 0.5 (2021) added a built-in LSP client and tree-sitter integration, which transformed its plugin ecosystem: plugins could now parse code semantically rather than with regular expressions.

Bram Moolenaar, Vim's creator and long-time maintainer, died in August 2023. Vim continues as a community-maintained project at version 9.1, but its development pace has slowed considerably. The Neovim project is the primary focus of most new Vim ecosystem development in 2026. The current stable Neovim release is **0.12**, which introduced a built-in `vim.pack` package manager as an alternative to third-party managers like lazy.nvim.

Neovim runs on Windows, macOS and Linux. It is free and open source under the Apache 2.0 licence.

---

## 2. Installing Neovim

🪟 **Windows**

```bash
winget install Neovim.Neovim
```

Or download the installer from [github.com/neovim/neovim/releases](https://github.com/neovim/neovim/releases). The `NVIM_WIN64_INSTALL.msi` installer is the easiest option. After installing, `nvim` should be on your PATH.

🍎 **macOS**

```bash
brew install neovim
```

Or download the `.tar.gz` for your architecture from the releases page and add the `bin/` directory to your PATH.

🐧 **Linux**

The package manager version is often outdated. Install from the releases page or use a package manager that tracks current releases:

```bash
# Ubuntu/Debian - via PPA for current stable
sudo add-apt-repository ppa:neovim-ppa/stable
sudo apt update
sudo apt install neovim

# Fedora - current version in official repos
sudo dnf install neovim

# Arch Linux - always current
sudo pacman -S neovim

# Or build from source / download the appimage
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.appimage
chmod u+x nvim-linux-x86_64.appimage
./nvim-linux-x86_64.appimage
```

Verify the installation with `nvim --version`. You need **0.9.0 or later** for the plugins in this file; 0.12 is recommended.

---

## 3. Configuration and init.lua

Neovim's configuration file is `init.lua`, written in Lua. Its location depends on your operating system:

| Platform | Config directory | Config file |
|---|---|---|
| 🐧 Linux | `~/.config/nvim/` | `~/.config/nvim/init.lua` |
| 🍎 macOS | `~/.config/nvim/` | `~/.config/nvim/init.lua` |
| 🪟 Windows | `%LOCALAPPDATA%\nvim\` | `%LOCALAPPDATA%\nvim\init.lua` |

Create the directory and file if they do not exist:

```bash
# Linux/macOS
mkdir -p ~/.config/nvim
touch ~/.config/nvim/init.lua

# Windows (PowerShell)
New-Item -ItemType Directory -Force -Path "$env:LOCALAPPDATA\nvim"
New-Item -ItemType File -Force -Path "$env:LOCALAPPDATA\nvim\init.lua"
```

A typical `init.lua` for a project with separate plugin files:

```lua
-- Load the plugin manager bootstrap
require("config.lazy")

-- Optional: load other config modules
require("config.options")
require("config.keymaps")
```

Plugin specs live in `lua/plugins/*.lua`. This separation keeps `init.lua` short and makes it easy to enable or disable individual plugins.

---

## 4. lazy.nvim - the plugin manager

**lazy.nvim** (by Folke Vanhoucke) is the dominant plugin manager for Neovim in 2026. Its key features are:

- **Lockfile** (`lazy-lock.json`) that pins every plugin to a specific commit, so your config reproduces exactly across machines
- **Lazy loading** via `event`, `cmd`, `ft` and `keys` triggers, so plugins only load when needed
- **Graphical UI** (`:Lazy`) for installing, updating, profiling and reverting plugins
- **Health checks** (`:checkhealth lazy`) for diagnosing setup issues

**Bootstrapping lazy.nvim** in `init.lua`:

```lua
-- Bootstrap lazy.nvim
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not (vim.uv or vim.loop).fs_stat(lazypath) then
  local lazyrepo = "https://github.com/folke/lazy.nvim.git"
  vim.fn.system({
    "git", "clone", "--filter=blob:none", "--branch=stable",
    lazyrepo, lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

-- Load plugins from lua/plugins/*.lua
require("lazy").setup({ import = "plugins" })
```

On first launch, lazy.nvim clones itself and then installs all plugins from your spec files. Subsequent launches load plugins lazily.

**Key lazy.nvim commands**:

| Command | Action |
|---|---|
| `:Lazy` | Open the UI |
| `:Lazy install` | Install missing plugins |
| `:Lazy update` | Update all plugins |
| `:Lazy sync` | Install + update + clean |
| `:Lazy restore` | Revert all plugins to lockfile state |
| `:Lazy clean` | Remove unused plugins |
| `:Lazy profile` | Show load time per plugin |

---

## 5. vim-fugitive

vim-fugitive by Tim Pope is the foundation of the Neovim Git stack. It wraps the `git` CLI so comprehensively that Tim Pope calls it "a Git wrapper so awesome it should be illegal". It works in both Vim and Neovim.

**Install with lazy.nvim** - create `~/.config/nvim/lua/plugins/git.lua`:

```lua
return {
  "tpope/vim-fugitive",
}
```

**The `:Git` command** (also shortened to `:G`) is fugitive's core. It runs any Git command and handles the output intelligently:

```vim
:Git status       " Opens the fugitive status buffer
:Git add .        " Stages all changes (no output to handle)
:Git commit       " Opens commit message in the editor
:Git push         " Pushes (output shown, press ENTER when done)
:Git pull         " Pulls
:Git fetch        " Fetches all remotes
:Git log          " Opens log in a temporary buffer
:Git diff         " Opens diff in the editor
:Git blame        " Opens blame alongside the file
:Git rebase -i HEAD~3  " Opens interactive rebase in the editor
:Git stash        " Stashes changes
:Git stash pop    " Pops the last stash
```

**The status buffer** is the hub. Run `:Git` with no arguments to open it. Inside the status buffer:

| Key | Action |
|---|---|
| `s` | Stage file or hunk under cursor |
| `u` | Unstage file or hunk under cursor |
| `X` | Discard change under cursor |
| `=` | Toggle inline diff for file under cursor |
| `cc` | Create a commit |
| `ca` | Amend the last commit |
| `cw` | Reword the last commit message |
| `cf` | Create a fixup commit for the commit under cursor |
| `cs` | Create a squash commit for the commit under cursor |
| `ri` | Interactive rebase from commit under cursor |
| `czz` | Push current stash |
| `czp` | Pop most recent stash |
| `[c` / `]c` | Navigate between hunks |
| `g?` | Show full keybinding help |

**Diff commands**:

```vim
:Gdiffsplit       " Side-by-side diff of current file vs index
:Gdiffsplit HEAD  " Side-by-side diff vs last commit
:Gdiffsplit main  " Side-by-side diff vs main branch
:Gdiffsplit!      " Three-way diff for merge conflicts
```

In `:Gdiffsplit!` (3-way merge), use `do` (diff obtain) to pull a hunk from the other side and `dp` (diff put) to push a hunk to the other side.

**Blame**:

```vim
:Git blame        " Opens blame in a split alongside the file
```

Inside the blame buffer, press `o` to open the commit for the line under the cursor in a new split.

**GBrowse**: opens the current file, selection or commit on its remote host (GitHub, GitLab etc.) in the browser. Requires companion plugins for non-GitHub hosts:

```lua
-- For GitHub (usually automatic via vim-fugitive)
-- For GitLab
"shumphrey/fugitive-gitlab.vim",
-- For Bitbucket
"tommcdo/vim-fubitive",
```

---

## 6. gitsigns.nvim

gitsigns.nvim by Lewis Russell adds live diff indicators to the signcolumn (the thin column between line numbers and code) and hunk-level Git operations to the buffer directly.

**Install with lazy.nvim**:

```lua
return {
  "lewis6991/gitsigns.nvim",
  opts = {
    signs = {
      add          = { text = "┃" },
      change       = { text = "┃" },
      delete       = { text = "_" },
      topdelete    = { text = "‾" },
      changedelete = { text = "~" },
      untracked    = { text = "┆" },
    },
    current_line_blame = false,       -- enable with true for always-on blame
    current_line_blame_opts = {
      delay = 1000,
    },
  },
}
```

**Signs**:

| Sign | Meaning |
|---|---|
| Green `┃` | Line added since last commit |
| Blue `┃` | Line modified since last commit |
| Red `_` | Line deleted below this point |
| Red `‾` | Line deleted above this point |
| Yellow `~` | Line changed and deleted |
| Grey `┆` | Untracked line in a new file |

**Key commands** (configure as keymaps in your config):

```lua
local gs = require("gitsigns")

-- Navigation
vim.keymap.set("n", "]h", gs.next_hunk, { desc = "Next hunk" })
vim.keymap.set("n", "[h", gs.prev_hunk, { desc = "Previous hunk" })

-- Stage/unstage
vim.keymap.set("n", "<leader>hs", gs.stage_hunk, { desc = "Stage hunk" })
vim.keymap.set("n", "<leader>hr", gs.reset_hunk, { desc = "Reset hunk" })
vim.keymap.set("v", "<leader>hs", function()
  gs.stage_hunk({ vim.fn.line("."), vim.fn.line("v") })
end, { desc = "Stage selected lines" })
vim.keymap.set("n", "<leader>hS", gs.stage_buffer, { desc = "Stage buffer" })
vim.keymap.set("n", "<leader>hu", gs.undo_stage_hunk, { desc = "Undo stage hunk" })
vim.keymap.set("n", "<leader>hR", gs.reset_buffer, { desc = "Reset buffer" })

-- Preview
vim.keymap.set("n", "<leader>hp", gs.preview_hunk, { desc = "Preview hunk" })
vim.keymap.set("n", "<leader>hi", gs.preview_hunk_inline, { desc = "Preview hunk inline" })

-- Blame
vim.keymap.set("n", "<leader>hb", gs.blame_line, { desc = "Blame line" })
vim.keymap.set("n", "<leader>hB", function() gs.blame_line({ full = true }) end, { desc = "Full blame line" })
vim.keymap.set("n", "<leader>tb", gs.toggle_current_line_blame, { desc = "Toggle line blame" })

-- Diff
vim.keymap.set("n", "<leader>hd", gs.diffthis, { desc = "Diff this" })
vim.keymap.set("n", "<leader>hD", function() gs.diffthis("~") end, { desc = "Diff this ~" })

-- Text object: select a hunk
vim.keymap.set({ "o", "x" }, "ih", ":<C-U>Gitsigns select_hunk<CR>", { desc = "Select hunk" })
```

**Current line blame**: set `current_line_blame = true` to show faded virtual text at the end of every line showing who last changed it and when. This is equivalent to GitLens' inline blame. Toggle it at runtime with `gs.toggle_current_line_blame()`.

**Word diff**: enable `word_diff = true` in the opts to highlight changed words within a modified line, not just the whole line.

---

## 7. neogit

Neogit by the NeogitOrg team is a Magit-inspired Git interface for Neovim. It provides a popup-driven, keyboard-controlled UI that feels similar to Emacs Magit. If you have used Magit and want something similar in Neovim, neogit is the answer.

**Install with lazy.nvim**:

```lua
return {
  "NeogitOrg/neogit",
  dependencies = {
    "nvim-lua/plenary.nvim",
    "sindrets/diffview.nvim",  -- Optional: better diff support
    "nvim-telescope/telescope.nvim",  -- Optional: fuzzy finding
  },
  config = true,
}
```

**Opening neogit**:

```vim
:Neogit             " Open the status buffer
:Neogit commit      " Open directly to commit
:Neogit log         " Open the log
:Neogit push        " Push
:Neogit pull        " Pull
```

**Inside the neogit status buffer**, every section is collapsible with `Tab`. The sections mirror `git status`: staged changes, unstaged changes, stashes, recent commits, untracked files. Press `?` at any time for context-sensitive help.

Key neogit bindings inside the status buffer:

| Key | Action |
|---|---|
| `s` | Stage hunk or file under cursor |
| `u` | Unstage hunk or file under cursor |
| `x` | Discard change under cursor |
| `<CR>` | Open the file or expand section |
| `cc` | Commit |
| `ca` | Amend |
| `cf` | Fixup commit |
| `P` | Push popup |
| `p` | Pull popup |
| `b` | Branch popup |
| `r` | Rebase popup |
| `Z` | Stash popup |
| `l` | Log popup |

**Popups**: neogit uses transient-style popups (borrowed from Magit's design). Pressing `P` for example opens the Push popup with options for the remote, branch and flags (`--force-with-lease`, `--tags` etc.). This makes complex Git operations discoverable without memorising flags.

---

## 8. diffview.nvim

diffview.nvim by Sindrets provides two views that complement fugitive and neogit: a full-repo diff view and a file history view.

**Install with lazy.nvim**:

```lua
return {
  "sindrets/diffview.nvim",
}
```

**The diff view**: `:DiffviewOpen` opens a split showing all changed files in the working tree on the left and their diffs on the right. `:DiffviewOpen HEAD~3` shows the diff between HEAD and HEAD~3. `:DiffviewOpen main...feature` shows the diff between the common ancestor of main and feature, and the tip of feature.

| Key (in diff view) | Action |
|---|---|
| `]f` / `[f` | Next / previous changed file |
| `<C-w>o` | Focus the diff pane |
| `<leader>co` | Accept "ours" in a conflict |
| `<leader>ct` | Accept "theirs" in a conflict |
| `<leader>cb` | Accept "base" in a conflict |
| `<leader>ca` | Accept all conflicting changes |
| `]x` / `[x` | Next / previous conflict |
| `q` | Close diffview |

**The file history view**: `:DiffviewFileHistory` shows every commit that touched the current file. `:DiffviewFileHistory %` limits to the current buffer. Each entry shows the commit and its diff for that file - equivalent to `git log -p -- <filename>` but with a navigable UI.

**Three-way merge conflict resolution**: when a merge conflict exists, `:DiffviewOpen` opens a four-panel layout with Ours (top-left), Theirs (top-right), Base (bottom-left) and Result (bottom-right). Navigate between conflicts with `]x`/`[x` and use the accept keymaps to resolve each one. Save the Result pane to mark the conflict as resolved.

---

## 9. lazygit.nvim

lazygit is a terminal UI for Git written in Go. It is not a Neovim plugin itself, but lazygit.nvim (kdheepak/lazygit.nvim) integrates it into Neovim as a floating terminal window.

**Installing lazygit** (required separately):

```bash
# macOS
brew install lazygit

# Windows
winget install JesseDuffield.lazygit

# Linux (via package manager or binary release)
# See https://github.com/jesseduffield/lazygit#installation
```

**Install the Neovim integration with lazy.nvim**:

```lua
return {
  "kdheepak/lazygit.nvim",
  cmd = {
    "LazyGit",
    "LazyGitConfig",
    "LazyGitCurrentFile",
    "LazyGitFilter",
    "LazyGitFilterCurrentFile",
  },
  dependencies = { "nvim-lua/plenary.nvim" },
  keys = {
    { "<leader>lg", "<cmd>LazyGit<cr>", desc = "LazyGit" },
  },
}
```

**Using lazygit from Neovim**: press your configured keymap (above: `<leader>lg`) to open lazygit in a floating window. lazygit handles staging, committing, branching, stashing, rebasing and much more with its own keybindings. When you commit, if you have `core.editor = nvim`, the commit message opens in Neovim. Press `q` to close lazygit and return to Neovim.

---

## 10. A recommended Git configuration

The following `lua/plugins/git.lua` gives you the full four-plugin stack with sensible defaults and a coherent set of `<leader>g` keymaps:

```lua
return {
  -- The core Git wrapper
  {
    "tpope/vim-fugitive",
    cmd = { "Git", "G", "Gdiffsplit", "Gblame", "GBrowse" },
    keys = {
      { "<leader>gs", "<cmd>Git<cr>",       desc = "Git status" },
      { "<leader>gc", "<cmd>Git commit<cr>", desc = "Git commit" },
      { "<leader>gp", "<cmd>Git push<cr>",   desc = "Git push" },
      { "<leader>gl", "<cmd>Git log<cr>",    desc = "Git log" },
      { "<leader>gb", "<cmd>Git blame<cr>",  desc = "Git blame" },
      { "<leader>gd", "<cmd>Gdiffsplit<cr>", desc = "Git diff" },
    },
  },

  -- Gutter signs and hunk operations
  {
    "lewis6991/gitsigns.nvim",
    event = { "BufReadPre", "BufNewFile" },
    opts = {
      signs = {
        add          = { text = "┃" },
        change       = { text = "┃" },
        delete       = { text = "_" },
        topdelete    = { text = "‾" },
        changedelete = { text = "~" },
      },
      current_line_blame = false,
      on_attach = function(bufnr)
        local gs = package.loaded.gitsigns
        local map = function(mode, lhs, rhs, opts)
          opts = vim.tbl_extend("force", { buffer = bufnr }, opts or {})
          vim.keymap.set(mode, lhs, rhs, opts)
        end
        map("n", "]h", gs.next_hunk,         { desc = "Next hunk" })
        map("n", "[h", gs.prev_hunk,         { desc = "Prev hunk" })
        map("n", "<leader>hs", gs.stage_hunk,  { desc = "Stage hunk" })
        map("n", "<leader>hr", gs.reset_hunk,  { desc = "Reset hunk" })
        map("n", "<leader>hb", gs.blame_line,  { desc = "Blame line" })
        map("n", "<leader>hd", gs.diffthis,    { desc = "Diff this" })
      end,
    },
  },

  -- Magit-style porcelain
  {
    "NeogitOrg/neogit",
    keys = {
      { "<leader>gn", "<cmd>Neogit<cr>", desc = "Neogit" },
    },
    dependencies = { "nvim-lua/plenary.nvim", "sindrets/diffview.nvim" },
    config = true,
  },

  -- Rich diff and file history
  {
    "sindrets/diffview.nvim",
    keys = {
      { "<leader>gv", "<cmd>DiffviewOpen<cr>",        desc = "Diffview open" },
      { "<leader>gh", "<cmd>DiffviewFileHistory<cr>", desc = "File history" },
    },
  },

  -- lazygit integration
  {
    "kdheepak/lazygit.nvim",
    keys = {
      { "<leader>lg", "<cmd>LazyGit<cr>", desc = "LazyGit" },
    },
    dependencies = { "nvim-lua/plenary.nvim" },
  },
}
```

---

## 11. Daily Git workflow in Neovim

**Checking status and staging**: press `<leader>gs` to open the fugitive status buffer. Press `=` on any file to expand its inline diff. Press `s` to stage a file or hunk, `u` to unstage. When only staged changes are ready, press `cc` to open the commit message buffer.

**Committing**: the commit message opens as a buffer in Neovim. Write the subject on the first line (Git convention: 72 characters maximum, imperative mood). Leave a blank line, then write the body if needed. Save and quit with `:wq` or `ZZ`. fugitive detects the save and creates the commit.

**Viewing blame**: open any file, press `<leader>gb` (or `:Git blame`) to open a scrolling blame panel. The blame panel is synchronised with the file - scrolling one scrolls both. Press `o` on any blame entry to jump to the commit that made that change.

**Pushing and pulling**: press `<leader>gp` (`:Git push`) to push. The output appears in a terminal buffer; press Enter when done. Pull with `:Git pull`. For complex fetch/push scenarios, open lazygit with `<leader>lg` which has a more ergonomic UI for remote management.

**Interactive rebase**: in the fugitive status buffer, navigate to a commit in the log section and press `ri` to start an interactive rebase from that commit. The rebase todo file opens in Neovim with fugitive's extended keybindings: press `r` to reword, `s` to squash, `f` to fixup, `d` to drop, and move lines with `ddp`/`ddkP` (Vim idiom for moving lines down/up). Save and close to start the rebase.

**Resolving conflicts**: open the conflicting file and run `:Gdiffsplit!` to open the three-way merge view. Or run `<leader>gv` to open diffview's merge layout. Use `do`/`dp` in the fugitive split or the diffview conflict keymaps to resolve each hunk.

---

## 12. The integrated terminal for Git

Neovim has a built-in terminal emulator accessible with `:terminal` or `:term`. Open a horizontal split terminal:

```vim
:split | terminal
```

Or a vertical split:

```vim
:vsplit | terminal
```

Inside the terminal, press `i` to enter insert mode and type commands. Press `Ctrl+\` then `Ctrl+N` to return to normal mode in the terminal buffer, allowing you to scroll, copy and navigate with Vim motions.

**Setting Neovim as your Git editor**: add this to your shell config or Git config so that `git commit` and `git rebase -i` open in Neovim:

```bash
# In .bashrc / .zshrc
export GIT_EDITOR=nvim

# Or in Git config
git config --global core.editor nvim
```

With this set, running `git rebase -i HEAD~5` in the terminal opens the rebase todo file in a new Neovim buffer. The `--wait` equivalent in Neovim is handled automatically - the terminal process waits for you to save and close the buffer.

---

## 13. Classic Vim and vim-fugitive

vim-fugitive works identically in classic Vim (9.1). All `:Git` commands, the status buffer, diff splits, blame and interactive rebase work the same way. The difference is in what you use alongside it:

- **gitsigns.nvim** is Neovim-only. Vim users use **vim-gitgutter** (airblade/vim-gitgutter) for similar sign-column indicators, though it is less feature-complete.
- **neogit** is Neovim-only. There is no equivalent in classic Vim; fugitive's status buffer is the primary interface.
- **diffview.nvim** is Neovim-only. Classic Vim users rely on `:Gdiffsplit` and `vimdiff`.
- **lazy.nvim** is Neovim-only. Classic Vim uses **vim-plug** (junegunn/vim-plug) or **pathogen** or native packages under `~/.vim/pack/*/start/`.

If you are using classic Vim and want to add vim-fugitive, the simplest install without a plugin manager is:

```bash
mkdir -p ~/.vim/pack/tpope/start
cd ~/.vim/pack/tpope/start
git clone https://tpope.io/vim/fugitive.git
vim -u NONE -c "helptags fugitive/doc" -c q
```

---

## 14. Try It Yourself

**Exercise 1 - set up the plugin stack**

Install lazy.nvim using the bootstrap snippet in this file. Create `~/.config/nvim/lua/plugins/git.lua` with the recommended configuration from section 10. Open Neovim and run `:Lazy install`. Once all plugins are installed, open a Git repository and verify that gitsigns signs appear in the signcolumn after editing a file.

**Exercise 2 - stage and commit with fugitive**

Open a Git repository in Neovim. Make changes to two different files for two different purposes. Run `:Git` to open the status buffer. Stage only the changes for the first purpose using `s`. Press `cc` to commit, write a focused commit message and save. Verify with `:Git log` that the commit appears.

**Exercise 3 - interactive rebase**

Make three commits that could be cleaner - for example, a feature commit, an "oops fix typo" commit and another feature step. In the fugitive status buffer, navigate to the third-most-recent commit in the log section and press `ri`. In the rebase todo, change the middle commit to `fixup` and save. Verify with `:Git log` that you now have two commits.

**Exercise 4 - explore a file's history with diffview**

Open any file with a reasonable commit history. Run `<leader>gh` (`:DiffviewFileHistory`). Browse through the list of commits that touched this file. Click each one to see the diff for that specific commit. This shows you how the file evolved over time without touching the terminal.

**Exercise 5 - resolve a merge conflict**

Create two branches from the same commit and edit the same line of the same file differently on each. Merge one into the other. When the conflict occurs, open the conflicting file and run `:Gdiffsplit!`. Use `do` to take the hunk from one side. Save, close the diff and commit the merge.

---

## 15. Common Mistakes

Not setting `GIT_EDITOR=nvim` means Git will open commit messages in nano or vi rather than Neovim when you run commands from the integrated terminal. Add the export to your shell config and verify with `echo $GIT_EDITOR` in a new terminal.

Forgetting to configure `on_attach` in gitsigns means the keymaps will not be buffer-local and may conflict with other plugins. The `on_attach` pattern in the recommended config above is the correct way to set per-buffer keymaps.

Using the wrong plugin manager for Neovim vs Vim is a common confusion. lazy.nvim is Neovim-only. vim-plug works in both. If you share a config between Vim and Neovim, conditional loading is needed: `if has('nvim') ... end` in VimScript or platform detection in Lua.

Trying to use neogit without its dependencies causes cryptic errors. neogit requires `nvim-lua/plenary.nvim`. If you use the diffview integration, diffview.nvim must also be installed. Declare them in the `dependencies` field of the lazy.nvim spec so they are always installed together.

Running `git commit` in the Neovim terminal without the editor configured opens a nested Neovim inside the terminal, which can confuse new users. The `:wq` in the inner Neovim triggers the commit, but the outer Neovim does not automatically update. Use fugitive's `cc` in the status buffer instead, or run `git commit -m "message"` from the terminal for one-liners.

---

## 16. Summary

Neovim's Git workflow is built from four plugins: vim-fugitive for full Git command access and a keyboard-driven status buffer, gitsigns.nvim for live signcolumn indicators and hunk-level operations, neogit for a Magit-style porcelain with popup-driven commands, and diffview.nvim for rich diff and file history views. These are managed by lazy.nvim, which provides a lockfile, lazy loading and a graphical UI. The config lives in `~/.config/nvim/init.lua` on Linux/macOS or `%LOCALAPPDATA%\nvim\init.lua` on Windows. Neovim's built-in terminal with `GIT_EDITOR=nvim` set routes commit messages and interactive rebase todo files back into the editor. lazygit.nvim adds a floating lazygit terminal for complex multi-remote and multi-stash scenarios. Classic Vim users can use vim-fugitive with vim-plug but miss the Neovim-specific plugins. The trade-off vs graphical editors is a steeper learning curve in exchange for an extremely fast, keyboard-only workflow with no GUI overhead.

---

## 17. Sources

- [vim-fugitive repository](https://github.com/tpope/vim-fugitive)
- [gitsigns.nvim repository](https://github.com/lewis6991/gitsigns.nvim)
- [neogit repository](https://github.com/NeogitOrg/neogit)
- [diffview.nvim repository](https://github.com/sindrets/diffview.nvim)
- [lazy.nvim repository](https://github.com/folke/lazy.nvim)
- [lazygit repository](https://github.com/jesseduffield/lazygit)
- [lazygit.nvim repository](https://github.com/kdheepak/lazygit.nvim)
- [Neovim documentation](https://neovim.io/doc/)
- [Neovim releases](https://github.com/neovim/neovim/releases)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

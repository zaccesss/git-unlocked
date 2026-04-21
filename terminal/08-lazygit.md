# lazygit

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

lazygit is a terminal UI for Git written in Go by Jesse Duffield. It puts a full Git interface inside your terminal - staging, committing, branching, stashing, interactive rebase, cherry-pick, conflict resolution and more - all controlled by keyboard shortcuts without typing `git` commands manually. For most day-to-day Git work, lazygit is the fastest interface available that does not require leaving the terminal.

---

## Table of Contents

1. [Installing lazygit](#1-installing-lazygit)
2. [Opening and closing](#2-opening-and-closing)
3. [The five panels](#3-the-five-panels)
4. [Files panel - staging and committing](#4-files-panel---staging-and-committing)
5. [Branches panel](#5-branches-panel)
6. [Commits panel](#6-commits-panel)
7. [Stash panel](#7-stash-panel)
8. [Push, pull and fetch](#8-push-pull-and-fetch)
9. [Conflict resolution](#9-conflict-resolution)
10. [Interactive rebase](#10-interactive-rebase)
11. [Configuration](#11-configuration)
12. [Integrating with delta](#12-integrating-with-delta)
13. [Launching from editors and tmux](#13-launching-from-editors-and-tmux)
14. [Windows note](#14-windows-note)
15. [Try It Yourself](#15-try-it-yourself)
16. [Common Mistakes](#16-common-mistakes)
17. [Summary](#17-summary)
18. [Sources](#18-sources)

---

## 1. Installing lazygit

Current version: **v0.61.1** (April 2026).

🍎 **macOS**:

```bash
brew install lazygit
```

🐧 **Linux**:

```bash
# Arch Linux
sudo pacman -S lazygit

# Fedora
sudo dnf install lazygit

# Ubuntu/Debian 24.04+ (may lag behind current release)
sudo apt install lazygit

# Any Linux - install current version from GitHub releases
LAZYGIT_VERSION=$(curl -s "https://api.github.com/repos/jesseduffield/lazygit/releases/latest" | grep -Po '"tag_name": *"v\K[^"]*')
curl -Lo lazygit.tar.gz "https://github.com/jesseduffield/lazygit/releases/download/v${LAZYGIT_VERSION}/lazygit_${LAZYGIT_VERSION}_Linux_x86_64.tar.gz"
tar xf lazygit.tar.gz lazygit
sudo install lazygit -D -t /usr/local/bin/
```

🪟 **Windows**:

```powershell
winget install -e --id JesseDuffield.lazygit
# or
scoop bucket add extras && scoop install lazygit
```

Verify the installation:

```bash
lazygit --version
```

---

## 2. Opening and closing

Open lazygit in any Git repository:

```bash
lazygit
# or the common shorthand
lg      # if you have this shell alias set
```

If you are not inside a Git repository, lazygit shows an error and exits.

**Quit**: press `q` to close lazygit and return to your terminal. Your working directory in the terminal is unchanged.

**Quit with cd**: if you want lazygit to change your terminal's directory when you quit, add a shell function. This is useful when you navigate between repositories inside lazygit:

```bash
# Add to ~/.zshrc or ~/.bashrc
lg() {
    export LAZYGIT_NEW_DIR_FILE=~/.lazygit/newdir
    lazygit "$@"
    if [ -f $LAZYGIT_NEW_DIR_FILE ]; then
        cd "$(cat $LAZYGIT_NEW_DIR_FILE)"
        rm -f $LAZYGIT_NEW_DIR_FILE > /dev/null
    fi
}
```

**Global keybindings available from anywhere in lazygit**:

| Key | Action |
| --- | ------ |
| `q` | Quit lazygit |
| `?` | Show help for the current panel |
| `x` | Open options menu for the focused item |
| `+` / `_` | Cycle through screen layouts |
| `@` | Open command log |
| `:` | Run a custom git command |
| `Ctrl+R` | Switch to a different repository |
| `P` | Push |
| `p` | Pull |
| `f` | Fetch all |

---

## 3. The five panels

lazygit has five panels arranged across the screen. Press the number keys `1` through `5` to jump directly to any panel. `Tab` cycles forward through panels, `Shift+Tab` cycles backward.

```
┌─────────────────────────────────────────────────────────────────┐
│ 1: Status  │ 2: Files                   │                       │
│            │                            │  diff / detail view   │
│ 3: Branches│ 4: Commits                 │                       │
│            │                            │                       │
│ 5: Stash   │                            │                       │
└────────────┴────────────────────────────┴───────────────────────┘
│  command log                                                     │
└──────────────────────────────────────────────────────────────────┘
```

**Status panel (1)**: shows the repository name, current branch, upstream and any ahead/behind counts. Click the repo name to switch to a different recently opened repository.

**Files panel (2)**: lists all modified files - unstaged in red, staged in green. This is where you stage and commit.

**Branches panel (3)**: lists local branches with sub-tabs for remotes and tags. Switch, create, delete, merge, rebase and push branches here.

**Commits panel (4)**: the commit log for the current branch. Review history, interactive rebase, cherry-pick and create fixup commits here.

**Stash panel (5)**: list of stashed changes. Apply, pop, drop and inspect stashes here.

---

## 4. Files panel - staging and committing

Press `2` to switch to the Files panel. Navigate with `j`/`k` (or arrow keys).

### Staging

| Key | Action |
| --- | ------ |
| `space` | Stage or unstage the selected file |
| `a` | Stage all files / unstage all files |
| `Enter` | Open the file diff; navigate hunks with `j`/`k`; stage individual hunks with `space` |
| `d` | Discard changes in the selected file |
| `e` | Open the file in your `$EDITOR` |
| `i` | Add the file to `.gitignore` |
| `o` | Open the file in your default application |

**Hunk-level staging**: press `Enter` on any file to open its diff in the main panel. Navigate between hunks with `j`/`k`. Press `space` to stage a single hunk. Press `1`/`2`/`3` to select individual lines within a hunk (requires delta's `--navigate` to be off inside lazygit). Press `=` to toggle between unstaged and staged views.

### Committing

| Key | Action |
| --- | ------ |
| `c` | Commit - opens an inline commit message prompt |
| `Shift+C` | Commit - opens your configured `$EDITOR` for a multi-line message |
| `w` | Commit without running pre-commit hooks (`--no-verify`) |
| `A` | Amend the last commit with staged changes (no message change) |
| `Ctrl+X` | Amend the last commit and edit the message |

When you press `c`, an inline message prompt appears at the bottom of the screen. Type your commit message and press `Enter` to commit. Press `Esc` to cancel.

For a multi-line commit message with a body and trailers, press `Shift+C` to open your full editor. Write the message, save and close the editor file - lazygit creates the commit.

**Copilot-style AI commit messages** are not built into lazygit. If you want AI commit message generation from the terminal, use your IDE's Source Control panel, or pipe `git diff --staged` to an LLM CLI tool.

---

## 5. Branches panel

Press `3` to switch to the Branches panel. Use `[` and `]` to switch between sub-tabs: Local, Remotes, Tags.

| Key | Action |
| --- | ------ |
| `space` | Checkout the selected branch |
| `n` | Create a new branch (prompts for name) |
| `d` | Delete the selected branch (safe - only merged branches) |
| `Shift+D` | Force delete the selected branch |
| `r` | Rebase the current branch onto the selected branch |
| `M` | Merge the selected branch into the current branch |
| `f` | Fast-forward the selected branch without checking it out |
| `o` | Open a pull request for the selected branch (opens browser) |
| `Shift+G` | View the associated GitHub/GitLab PR in the browser (v0.61+) |
| `w` | Create a Git worktree for the selected branch |
| `Shift+R` | Rename the selected branch |
| `i` | Show the branch's divergence graph (ahead/behind both sides) |
| `Enter` | View commits on the selected branch |

**Creating a branch and pushing immediately**:

1. Press `n` in the Branches panel
2. Type the branch name and press Enter
3. Press `P` (global push) to push and set the upstream

**GitHub PR icons** (lazygit v0.61.0+): if you have the `gh` CLI installed and authenticated (`gh auth login`), lazygit shows PR status icons next to branches that have an associated open pull request. Press `Shift+G` to open that PR in your browser.

---

## 6. Commits panel

Press `4` to switch to the Commits panel. This shows the commit log for the current branch with a visual graph on the left.

**Colour coding**:
- Green commits: present in the remote (already pushed)
- Yellow commits: local only (not yet pushed)
- Red: commits not reachable from any other branch

| Key | Action |
| --- | ------ |
| `Enter` | Expand the commit - see files changed |
| `space` | Checkout the selected commit (detached HEAD) |
| `y` | Copy the commit hash to clipboard |
| `Shift+C` | Cherry-pick this commit onto the current branch (copies) |
| `Shift+V` | Paste cherry-picked commits |
| `t` | Create a tag at this commit |
| `r` | Revert this commit (creates a new revert commit) |
| `Shift+R` | Reword this commit message |
| `Ctrl+R` | Reset HEAD to this commit |
| `Ctrl+F` | Find the fixup base commit for staged changes |
| `m` | Continue / abort / skip a rebase or merge |
| `i` | Start interactive rebase from this commit |

**Cherry-pick**: navigate to the Commits panel on a branch that has the commit you want. Press `Shift+C` to mark it for cherry-pick (it turns orange). Switch to your target branch (press `3`, press `space` to checkout). Go back to the Commits panel and press `Shift+V` to apply the cherry-picked commit. For multiple commits, press `Shift+C` on each one before switching branches.

---

## 7. Stash panel

Press `5` to switch to the Stash panel.

| Key | Action |
| --- | ------ |
| `s` (in Files panel) | Stash all changes |
| `Shift+S` (in Files panel) | Stash options menu (all, staged only, untracked) |
| `space` | Apply stash (keep in stash list) |
| `g` | Pop stash (apply and remove from list) |
| `d` | Drop stash |
| `Enter` | View stash contents |
| `n` | Create a new branch from this stash |

Stash names: when you press `s` in the Files panel, lazygit prompts you for a stash description. Give it a meaningful name - it makes the stash list much easier to read.

---

## 8. Push, pull and fetch

These keys work from anywhere in lazygit:

| Key | Action |
| --- | ------ |
| `P` | Push current branch |
| `p` | Pull (rebase or merge depending on your config) |
| `f` | Fetch all remotes |

**First push of a new branch**: lazygit automatically detects when a branch has no upstream and asks if you want to set the upstream to `origin/<branchname>`. Press `Enter` to confirm - this is the equivalent of `git push -u origin branchname`.

**Force push**: press `Shift+P` or press `P` and then select "force push" from the menu that appears when lazygit detects that a regular push would be rejected. lazygit uses `--force-with-lease` by default.

---

## 9. Conflict resolution

When a merge, rebase or pull produces conflicts, lazygit highlights conflicting files in red with a `UU` marker in the Files panel.

Press `Enter` on a conflicting file to open the conflict resolution view. Each conflict section is shown with its three parts:

- **Ours** (the current branch content) - shown at the top
- **Base** (the common ancestor - only with `zdiff3` conflict style)
- **Theirs** (the incoming content) - shown at the bottom

Navigate between conflicts with `j`/`k`. For each conflict:

| Key | Action |
| --- | ------ |
| `1` | Accept ours (current branch content) |
| `2` | Accept theirs (incoming content) |
| `b` | Accept both (ours first, then theirs) |
| `z` | Undo a resolution |
| `Shift+M` | Open the file in your configured merge tool |

After resolving all conflicts in a file, it is automatically staged. Once all files are resolved, press `m` to continue the rebase or merge.

For complex conflicts, press `Shift+M` to open your configured merge tool. After saving and closing, lazygit detects the resolution and marks the file as resolved.

---

## 10. Interactive rebase

lazygit's interactive rebase is one of its most powerful features - it gives you a visual interface for what `git rebase -i` does in a text file.

**Starting an interactive rebase**:

1. Go to the Commits panel (press `4`)
2. Navigate to the commit *before* the range you want to rebase (the parent commit)
3. Press `i` to start interactive rebase

The commits above your selection become the rebase range. Each commit in the Commits panel shows its rebase action. Press the action key to change it:

| Key | Rebase action | What it does |
| --- | ------------- | ------------ |
| `p` | pick | Keep this commit as-is |
| `r` | reword | Keep commit but edit its message |
| `e` | edit | Pause rebase here for amendments |
| `s` | squash | Merge into the previous commit, combine messages |
| `f` | fixup | Merge into the previous commit, discard this message |
| `d` | drop | Remove this commit entirely |

**Reordering commits**: press `Ctrl+J` to move a commit down, `Ctrl+K` to move it up.

**Applying the rebase**: press `m` to start the rebase with your configured actions. If you chose `edit` on any commit, the rebase pauses there and you can make changes, stage them and amend the commit. Press `m` again to continue to the next step.

**Auto-squash**: if you created fixup commits (using `git commit --fixup abc1234` or the `fixup` alias from [06-git-aliases.md](06-git-aliases.md)), lazygit shows a "squash all fixup commits" option in the Commits panel menu (`x`). This runs `git rebase --autosquash` and automatically positions the fixup commits.

---

## 11. Configuration

**Config file locations**:

| Platform | Path |
| -------- | ---- |
| 🐧 Linux | `~/.config/lazygit/config.yml` |
| 🍎 macOS | `~/Library/Application Support/lazygit/config.yml` |
| 🪟 Windows | `%LOCALAPPDATA%\lazygit\config.yml` |

Find the active path: `lazygit --print-config-dir`

A minimal, useful configuration:

```yaml
gui:
  nerdFontsVersion: "3"    # enables file-type icons (requires a Nerd Font)
  showFileIcons: true
  border: rounded

git:
  autoFetch: true           # fetch in the background every few minutes
  autoRefresh: true
  log:
    order: topo-order       # topological order (cleaner graph)
    showGraph: always

# Custom keybindings - example
keybinding:
  universal:
    quit: q
    confirm: enter
```

**Per-repository config**: create `.lazygit.yml` (or `.git/lazygit.yml`) in a repository to apply settings only to that project.

**Environment variable override**: `LG_CONFIG_FILE=/path/to/config.yml lazygit` or `LAZYGIT_CONFIG_DIR=/path/to/dir/` overrides the config location.

---

## 12. Integrating with delta

lazygit can use delta for diff rendering. Add to your lazygit config:

```yaml
git:
  paging:
    colorArg: always
    pager: delta --paging=never --dark
```

`--paging=never` tells delta not to use its own pager (lazygit handles scrolling). `--dark` or `--light` must be explicit because lazygit does not relay terminal background colour queries.

> [!WARNING]
> Delta with lazygit on **Windows** is not supported - see [14. Windows note](#14-windows-note). On all platforms, delta's `--navigate` flag and `n`/`N` keybindings do not work inside lazygit's PTY. Use `git diff` directly in the terminal for navigatable diffs.

---

## 13. Launching from editors and tmux

**Neovim** - via the lazygit.nvim plugin or snacks.nvim (LazyVim default):

```lua
-- With lazygit.nvim
vim.keymap.set('n', '<leader>gg', '<cmd>LazyGit<cr>', { desc = 'Open lazygit' })

-- With snacks.nvim (LazyVim)
-- Default keymaps: <leader>gg (project), <leader>gG (cwd), <leader>gl (log)
```

**tmux** - open lazygit in a floating popup overlay:

```bash
# In tmux.conf
bind g display-popup -d '#{pane_current_path}' -w 90% -h 90% -E 'lazygit'
```

Press your tmux prefix then `g` to open a full-screen lazygit popup. Press `q` to close and return to your previous pane.

**Helix** - no native plugin support; use `:sh lazygit` or configure a tmux popup keybinding in your Helix `config.toml`:

```toml
[keys.normal]
C-g = [":sh tmux popup -d '#{pane_current_path}' -xC -yC -w 90% -h 90% -E lazygit"]
```

**Zellij**:

```bash
zellij run -f -- lazygit
```

**VS Code / Cursor** - open the integrated terminal and run `lazygit`. Works the same as any terminal.

---

## 14. Windows note

lazygit works on native Windows but has two important limitations compared to macOS and Linux:

**Custom pagers are not supported on Windows**. Setting `pager: delta --paging=never` in the lazygit config has no effect on Windows - diffs display as plain uncoloured text. This is a known limitation of lazygit's Windows PTY implementation. The workaround is to use lazygit inside WSL2, where the full Linux toolchain including delta works without restriction.

**Some shell integrations do not work outside Git Bash**. The cd-on-exit shell function in section 2 requires bash or zsh. In PowerShell or cmd.exe, use the raw `lazygit` command without the wrapper function.

For the best lazygit experience on Windows, install it inside WSL2 and run it from a WSL2 terminal session.

---

## 15. Try It Yourself

**Exercise 1 - explore the panels**

Open lazygit in a repository. Press `1` through `5` to visit each panel. Press `?` in each panel to read the available keybindings. In the Commits panel, press `Enter` on a commit to see which files it changed. Press `Enter` again to see the diff for a specific file.

**Exercise 2 - stage and commit using lazygit**

Make changes to two files. Open lazygit. In the Files panel, press `space` on one file to stage it (it moves to green). Press `Enter` on the other file and use `space` to stage individual hunks. Press `c` to commit, type a message, press Enter. Verify with `git log -1` in your terminal.

**Exercise 3 - interactive rebase**

In a test repository, make four commits: a feature, a typo fix, another feature and another typo fix. Go to the Commits panel. Navigate to the commit before your first change. Press `i` to start interactive rebase. Change the two typo fix commits to `squash` or `fixup`. Press `m` to run the rebase. Verify the history is now cleaner with `git log --oneline`.

**Exercise 4 - cherry-pick**

Create a branch called `feature/a` and add a commit. Switch back to `main`. Go to the Commits panel. Switch branches to `feature/a` using the Branches panel. Find the commit you want. Press `Shift+C` to mark it. Switch back to `main` in the Branches panel. Go to Commits panel and press `Shift+V` to paste. Verify the commit is now on `main`.

**Exercise 5 - configure delta integration**

If you have delta installed, add the paging config from section 12 to your lazygit config. Reopen lazygit and look at a diff - you should see syntax highlighting. On Windows, note whether the delta colours appear or not.

---

## 16. Common Mistakes

Pressing `d` on a file in the Files panel when you meant to discard changes, but discovering it discarded all changes without asking. lazygit does ask for confirmation before discarding, but it is a single keypress to confirm. Get into the habit of reading the confirmation prompt before pressing Enter.

Confusing `P` (push) and `p` (pull). In lazygit, uppercase and lowercase are different actions. `P` pushes, `p` pulls. The convention is that uppercase operations are more "significant" or outward-facing. Check the help panel (`?`) if you are unsure.

Running lazygit from a directory that is not inside a Git repository. lazygit exits with an error if the current directory is not in a Git repo. Navigate to your project directory first, or pass a path: `lazygit -p /path/to/repo`.

Expecting delta's `n`/`N` navigation to work inside lazygit. The key presses are captured by lazygit's event loop, not passed to the delta pager. For navigatable diffs with delta, run `git diff` directly in the terminal.

Not configuring a Nerd Font when `nerdFontsVersion: "3"` is set. Without a Nerd Font installed and selected in your terminal emulator, file-type icons show as boxes or question marks. Either install a Nerd Font or remove the `nerdFontsVersion` line from your config.

---

## 17. Summary

lazygit is a keyboard-driven terminal UI for Git with five panels: Status, Files, Branches, Commits and Stash. Press `1`-`5` to jump between panels, `?` for panel help, `q` to quit. In the Files panel, `space` stages/unstages, `Enter` opens the diff for hunk-level staging, `c` commits inline and `Shift+C` commits in your editor. In Branches, `space` checks out, `n` creates, `d` deletes and `i` starts a rebase. In Commits, `i` starts interactive rebase with visual pick/reword/squash/fixup/drop/reorder, and `Shift+C`/`Shift+V` handles cherry-pick. Global keys: `P` pushes, `p` pulls, `f` fetches. Delta integration works on Mac and Linux via the `pager:` config option but not on native Windows. For the best experience on Windows, run lazygit inside WSL2.

---

## 18. Sources

- [lazygit repository](https://github.com/jesseduffield/lazygit)
- [lazygit docs](https://github.com/jesseduffield/lazygit/tree/master/docs)
- [lazygit keybindings reference](https://github.com/jesseduffield/lazygit/blob/master/docs/keybindings/Keybindings_en.md)
- [lazygit configuration docs](https://github.com/jesseduffield/lazygit/blob/master/docs/Config.md)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# delta

**Difficulty:** 🟢 Beginner | **Time:** 15 minutes

delta is a syntax-highlighting pager for `git diff`, `git log`, `git show`, `git blame` and `grep` output. It replaces Git's default diff display with one that has language-aware syntax highlighting, word-level diff highlighting, side-by-side view, line numbers, navigation between diff sections and improved blame output. Installing delta and adding six lines to your `.gitconfig` permanently improves how you read every diff from the terminal.

---

## Table of Contents

1. [What delta does](#1-what-delta-does)
2. [Installing delta](#2-installing-delta)
3. [Configuring delta in .gitconfig](#3-configuring-delta-in-gitconfig)
4. [Key features in detail](#4-key-features-in-detail)
5. [Themes and customisation](#5-themes-and-customisation)
6. [Using delta outside of git](#6-using-delta-outside-of-git)
7. [Windows note](#7-windows-note)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. What delta does

The default `git diff` output is functional but plain. Everything is printed as text with `+` and `-` markers. There is no syntax highlighting, no word-level diffing, and no line numbers.

delta changes this. Here is what you get after installing and configuring it:

- **Syntax highlighting** - code is highlighted using the same themes as bat (built on the syntect library with Sublime Text syntax definitions). A Python function shows with keyword highlighting; a TypeScript interface shows with type highlighting.
- **Word-level diff highlighting** - instead of highlighting the entire changed line, delta highlights exactly which words changed. A line where you renamed a variable shows the old name highlighted in red and the new name in green.
- **Side-by-side view** - the old and new versions of a changed section appear in left and right columns rather than interleaved.
- **Line numbers** - line numbers appear in the gutter on both sides of the diff.
- **Navigation** - press `n` to jump to the next changed section, `N` for the previous one.
- **Improved blame** - `git blame` output is syntax-highlighted with hyperlinks to commits on GitHub/GitLab.
- **Grep highlighting** - delta highlights `git grep`, `rg` and `grep` output with the same styling.

delta is written in Rust. It is fast and adds no meaningful latency to `git diff`.

---

## 2. Installing delta

The package is called `git-delta` in most package managers. The executable is just `delta`.

🍎 **macOS**:

```bash
brew install git-delta
```

🐧 **Linux**:

```bash
# Ubuntu/Debian (22.04+)
sudo apt install git-delta

# Fedora
sudo dnf install git-delta

# Arch Linux
sudo pacman -S git-delta

# Any Linux - download from GitHub releases
DELTA_VERSION=$(curl -s "https://api.github.com/repos/dandavison/delta/releases/latest" | grep -Po '"tag_name": "\K[^"]*')
curl -Lo delta.tar.gz "https://github.com/dandavison/delta/releases/latest/download/delta-${DELTA_VERSION}-x86_64-unknown-linux-gnu.tar.gz"
tar xf delta.tar.gz && sudo install delta-*/delta /usr/local/bin/
```

🪟 **Windows**:

```powershell
winget install dandavison.delta
# or
scoop install delta
```

On Windows, verify the installation and check the PATH:

```powershell
delta --version
where delta
```

> [!NOTE]
> After installing, delta is not active until you add it to your `.gitconfig`. The next section covers the required configuration.

---

## 3. Configuring delta in .gitconfig

Add these sections to your `~/.gitconfig`:

```ini
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true       # press n/N to jump between changed sections
    dark = true           # or: light = true; or omit for terminal auto-detection
    side-by-side = true   # show old/new in left/right columns
    line-numbers = true   # show line numbers in gutter

[merge]
    conflictStyle = zdiff3
```

The `[core] pager = delta` line routes all diff output through delta. The `[interactive] diffFilter = delta --color-only` line handles `git add --patch` correctly - the `--color-only` flag preserves the interactive format that `git add -p` needs.

Verify it is working:

```bash
# In any repository with changes
git diff
git log -p
git show HEAD
```

You should see syntax-highlighted, word-level diff output immediately.

---

## 4. Key features in detail

### Side-by-side view

With `side-by-side = true`, changed sections appear in two columns: the old version on the left, new version on the right. This makes it much easier to compare a modified function or block because you do not have to mentally interleave the `+` and `-` lines.

Toggle side-by-side for a single command without changing config:

```bash
git diff --no-pager | delta --side-by-side   # explicit side-by-side
git diff | delta --no-side-by-side           # disable for one command
```

### Word-level highlighting

delta uses a Levenshtein edit inference algorithm to detect exactly which words changed within a line. Instead of:

```diff
- function calculateTotal(price, qty) {
+ function calculateTotal(price, quantity) {
```

delta highlights just the changed part - `qty` in red, `quantity` in green - making the exact change immediately obvious.

### Navigation

With `navigate = true`, pressing `n` in the pager (less) jumps to the next changed file or section. `N` jumps backwards. This is particularly useful when reviewing a large diff across many files - you can move through changes without scrolling manually.

### Improved git blame

delta adds syntax highlighting to `git blame` output:

```bash
git blame src/auth.py
```

With `--hyperlinks` (available when `delta.hyperlinks = true`), commit hashes become clickable links to GitHub, GitLab, SourceHut, Codeberg or your configured remote.

### Conflict markers in merge conflicts

During a merge conflict, delta highlights the conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) in a distinct colour, making them easier to spot in a long file. This applies when you run `git diff` on a file with unresolved conflicts.

---

## 5. Themes and customisation

delta ships with many built-in syntax themes, all from bat's theme collection. To see what is available:

```bash
delta --list-themes
```

Set a theme in `.gitconfig`:

```ini
[delta]
    syntax-theme = Monokai Extended
    # Other popular choices:
    # syntax-theme = GitHub
    # syntax-theme = Dracula
    # syntax-theme = Nord
    # syntax-theme = gruvbox-dark
    # syntax-theme = OneHalfDark
    # syntax-theme = TwoDark
```

For auto dark/light switching based on your terminal's background, omit both `dark` and `light` from your delta config (auto-detection queries the terminal, supported by most modern terminals). To force one:

```ini
[delta]
    dark = true     # dark background
    # light = true  # light background
```

**Feature stacks**: delta supports defining named feature profiles and stacking them:

```ini
[delta "interactive"]
    keep-plus-minus-markers = false

[delta "decorations"]
    commit-decoration-style = blue ol
    commit-style = raw
    file-style = omit
    hunk-header-decoration-style = blue box
    hunk-header-file-style = red
    hunk-header-line-number-style = "#067a00"
    hunk-header-style = file line-number syntax

[delta]
    features = decorations
    side-by-side = true

[interactive]
    diffFilter = delta --color-only --features=interactive
```

**Full customisation reference**: `delta --help` lists every available option. Options in `.gitconfig` use the long form without the `--` prefix and with `-` replaced by `-`: `--side-by-side` becomes `side-by-side = true`.

---

## 6. Using delta outside of git

delta accepts unified diff format, so it works with any diff tool:

```bash
# Plain file diff
diff -u old.py new.py | delta

# Using diff from your terminal
diff old.json new.json | delta --side-by-side

# Mercurial
hg diff | delta

# Any patch file
cat my-changes.patch | delta
```

As a `PAGER` for other commands:

```bash
# Use delta to page any output with diff-like content
export MANPAGER="sh -c 'col -bx | bat -l man -p'"   # bat for man pages
```

---

## 7. Windows note

> [!WARNING]
> Custom pagers including delta are **not supported by lazygit on Windows**. This is a known limitation of lazygit's Windows port - it uses a PTY implementation that does not support custom pagers. Diffs inside lazygit on Windows will appear as plain uncoloured text even if delta is configured.
>
> delta works correctly with `git diff`, `git log`, `git show` and other git commands run directly in Windows Terminal, Git Bash or WSL. The limitation is specific to lazygit on native Windows.
>
> For lazygit users on Windows, the recommended approach is to use lazygit inside WSL where the full Linux tool chain including delta works without restriction.

Also on Windows: delta's **`--navigate`** flag (the `n`/`N` key bindings) does not work inside lazygit on any platform. The key bindings are captured by the lazygit PTY rather than passed to the delta pager. Run `git diff` directly from the terminal to use navigation.

---

## 8. Try It Yourself

**Exercise 1 - install and configure delta**

Install delta using the command for your platform in section 2. Add the configuration from section 3 to your `~/.gitconfig`. In any repository with uncommitted changes, run `git diff`. Compare the output to what you saw before.

**Exercise 2 - explore side-by-side view**

Make a change to a Python, JavaScript or any syntax-highlighted file. Run `git diff`. Notice the syntax highlighting and word-level diff markers. Now run `git diff | delta --no-side-by-side` to see the interleaved view. Which do you prefer for this file?

**Exercise 3 - navigate a large diff**

Find or create a commit that changes several files. Run `git show HEAD` or `git diff HEAD~3`. With `navigate = true` configured, press `n` to jump to the next changed file. Press `N` to go back. Press `q` to exit.

**Exercise 4 - try different themes**

Run `delta --list-themes | less` to browse available themes. Pick three that sound interesting. Temporarily set each one:

```bash
git -c delta.syntax-theme="Nord" diff
git -c delta.syntax-theme="GitHub" diff
git -c delta.syntax-theme="Dracula" diff
```

Set the one you like in your `.gitconfig`.

**Exercise 5 - test git blame with delta**

Run `git blame` on a file with meaningful history. Delta adds syntax highlighting to the output. If your repository is on GitHub or GitLab, add `hyperlinks = true` to `[delta]` in your config and check if the hashes become links (requires a terminal that supports hyperlinks, like iTerm2, Kitty or Windows Terminal).

---

## 9. Common Mistakes

Installing delta but forgetting to add it to `.gitconfig`. The installation alone does nothing - delta only activates when Git is configured to use it as a pager. If `git diff` still shows plain output after installing, check your `.gitconfig` for the `[core] pager = delta` line.

Forgetting `--color-only` in the `diffFilter` setting. The `[interactive] diffFilter = delta --color-only` line is required for `git add --patch` to work correctly. Without `--color-only`, delta processes the diff in a way that breaks the interactive staging format and `git add -p` stops working.

Expecting navigation to work inside lazygit on Windows. The `n`/`N` keys for jumping between diff sections only work in a direct terminal session with delta as the pager. Inside lazygit (on any platform) or inside IDE terminals with unusual PTY implementations, navigation may not function. Use delta directly from your terminal for navigation.

Setting `side-by-side = true` in a narrow terminal. Side-by-side view requires enough width to show two columns of code. In a narrow terminal (under about 120 columns), side-by-side may wrap awkwardly. Either widen your terminal or run `delta --no-side-by-side` to disable it temporarily.

Confusing delta's `dark` and `light` settings with a theme choice. `dark = true` and `light = true` tell delta whether your terminal has a dark or light background - it affects contrast settings for the built-in colour scheme, not which syntax highlighting theme to use. The theme is controlled by `syntax-theme`.

---

## 10. Summary

delta is a Rust-written pager that transforms all git diff output with syntax highlighting, word-level diff detection, side-by-side view, line numbers and navigation. Install via your package manager (the package is `git-delta`), then add four lines to your `~/.gitconfig`: `core.pager = delta`, `interactive.diffFilter = delta --color-only`, and the `[delta]` section with `navigate = true`, `side-by-side = true` and your preferred theme. It works with all git commands that produce diff output, with plain `diff`, and with other patch-format tools. Custom pagers are not supported by lazygit on Windows - use delta from the terminal directly or from WSL.

---

## 11. Sources

- [delta documentation](https://dandavison.github.io/delta/)
- [delta repository](https://github.com/dandavison/delta)
- [delta configuration reference](https://dandavison.github.io/delta/configuration.html)
- [Awesome Git Diffs with delta and fzf - Nick Janetakis](https://nickjanetakis.com/blog/awesome-git-diffs-with-delta-fzf-and-a-little-shell-scripting)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

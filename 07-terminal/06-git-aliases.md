# Git Aliases

**Difficulty:** 🟡 Intermediate | **Time:** 20 minutes

Git aliases let you create shortcuts for commands you run constantly and build complex one-liners you would never want to type from scratch. A good alias library cuts down the volume of typing in your daily workflow significantly and makes the most-used commands feel almost instant.

---

## Table of Contents

1. [How git aliases work](#1-how-git-aliases-work)
2. [Simple command aliases](#2-simple-command-aliases)
3. [Aliases with arguments](#3-aliases-with-arguments)
4. [Shell command aliases](#4-shell-command-aliases)
5. [A practical alias library](#5-a-practical-alias-library)
6. [Sharing aliases across machines](#6-sharing-aliases-across-machines)
7. [Shell aliases vs git aliases](#7-shell-aliases-vs-git-aliases)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. How git aliases work

Git aliases are stored in the `[alias]` section of your `.gitconfig`. When you type `git <alias>`, Git looks up the value in the alias table and runs it as if you had typed the full command.

```ini
# ~/.gitconfig
[alias]
    st = status --short --branch
```

Now `git st` runs `git status --short --branch`. The alias expands before any arguments you pass:

```bash
git st              # runs: git status --short --branch
git st -- src/      # runs: git status --short --branch -- src/
```

Add aliases via the command line:

```bash
git config --global alias.st "status --short --branch"
git config --global alias.lg "log --oneline --graph --all"
```

Or edit `~/.gitconfig` directly:

```bash
git config --global --edit
```

View all aliases:

```bash
git config --get-regexp '^alias\.'
# Or add this as an alias:
git config --global alias.aliases "config --get-regexp '^alias\.'"
git aliases
```

---

## 2. Simple command aliases

These are the most straightforward aliases - a short name for a longer command.

```ini
[alias]
    # Status
    s   = status --short --branch
    st  = status

    # Switching and branching
    sw  = switch
    swc = switch --create    # create and switch in one step
    co  = checkout           # older syntax, still common
    br  = branch --sort=-committerdate  # branches by most recently committed

    # Staging
    a   = add
    aa  = add --all
    ap  = add --patch        # interactive hunk staging

    # Committing
    c   = commit
    cm  = commit --message
    ca  = commit --all --message    # stage tracked files and commit
    amend = commit --amend --no-edit  # amend without changing the message
    fixup = commit --fixup           # create a fixup! commit

    # Pushing and pulling
    p   = push
    pl  = pull
    f   = fetch --all --prune

    # Log and history
    last = log -1 HEAD --stat        # show the last commit with file list
    ll   = log --oneline --all       # compact log of all branches
    undo = reset HEAD~1 --mixed      # undo last commit, keep changes staged
```

---

## 3. Aliases with arguments

Git aliases pass any extra arguments you provide at the end of the expanded command. This means most aliases naturally accept arguments:

```bash
git cm "fix login bug"
# expands to: git commit --message "fix login bug"

git swc feature/new-ui
# expands to: git switch --create feature/new-ui
```

For more complex cases where you need arguments in a specific position, use a shell function via the `!` prefix (covered in the next section).

**The `--` separator**: when you want to pass file paths, use `--` to separate Git options from file paths. Many aliases accept this naturally:

```bash
git s -- src/
# expands to: git status --short --branch -- src/
```

---

## 4. Shell command aliases

Prefixing an alias value with `!` tells Git to run it as a shell command rather than as a Git subcommand. This unlocks full scripting power.

```ini
[alias]
    # Run any shell command
    root = !pwd                          # print the repo root

    # Shell function for complex logic
    branch-name = !git rev-parse --abbrev-ref HEAD

    # Multi-step operations
    publish = !git push --set-upstream origin $(git rev-parse --abbrev-ref HEAD)
```

**Shell functions** let you use `$1`, `$2` etc. for positional arguments:

```ini
[alias]
    # Create a branch and push it immediately
    nb = "!f() { git switch --create \"$1\" && git push --set-upstream origin \"$1\"; }; f"

    # Show diff for a specific commit
    show-diff = "!f() { git diff \"$1\"^ \"$1\"; }; f"

    # Delete a branch locally and remotely
    delete-branch = "!f() { git branch -d \"$1\" && git push origin --delete \"$1\"; }; f"

    # Find commits touching a file
    file-log = "!f() { git log --follow --oneline -- \"$1\"; }; f"
```

Use these like:

```bash
git nb feature/auth          # creates and pushes feature/auth
git delete-branch old-branch # deletes locally and on origin
git file-log src/auth.py     # commits that touched auth.py
```

> [!NOTE]
> Shell aliases using `!` run in the **repository root directory**, not your current directory inside the repo. This is intentional - it makes paths predictable. If your alias needs the current directory, use `$(pwd)` explicitly.

> [!NOTE]
> On **Windows** with cmd.exe or PowerShell, shell aliases using `!` may not work unless you have Git Bash or WSL available. Shell aliases work reliably on Mac, Linux and Git Bash on Windows. If you use PowerShell primarily, consider PowerShell functions in your `$PROFILE` for complex aliases.

---

## 5. A practical alias library

Here is a comprehensive set of aliases organised by purpose. Add any that fit your workflow.

```ini
[alias]

    # ── Status and info ──────────────────────────────────────────────────────
    s     = status --short --branch
    st    = status
    root  = rev-parse --show-toplevel   # print the repo root path
    aliases = config --get-regexp '^alias\.'

    # ── Branching ────────────────────────────────────────────────────────────
    sw    = switch
    swc   = switch --create
    br    = branch --sort=-committerdate --format='%(HEAD) %(color:yellow)%(refname:short)%(color:reset) - %(color:red)%(objectname:short)%(color:reset) - %(contents:subject) - %(color:magenta)(%(committerdate:relative))%(color:reset)' --color=always
    bra   = branch --all --sort=-committerdate   # all branches including remotes
    gone  = "!git fetch -p && git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -D"

    # ── Staging ──────────────────────────────────────────────────────────────
    a     = add
    aa    = add --all
    ap    = add --patch

    # ── Committing ───────────────────────────────────────────────────────────
    c     = commit
    cm    = commit --message
    ca    = commit --all --message
    amend = commit --amend --no-edit
    reword = commit --amend --only --message   # change message without touching files
    fixup = commit --fixup
    wip   = "!git add -A && git commit -m 'WIP'"   # quick save-in-progress commit

    # ── Undoing ──────────────────────────────────────────────────────────────
    undo  = reset HEAD~1 --mixed    # undo last commit, keep changes unstaged
    unstage = restore --staged      # unstage a file
    discard = restore               # discard unstaged changes in a file

    # ── Pushing and pulling ──────────────────────────────────────────────────
    p     = push
    pf    = push --force-with-lease --force-if-includes   # safe force push
    pl    = pull
    f     = fetch --all --prune

    # ── Log and history ──────────────────────────────────────────────────────
    last  = log -1 HEAD --stat
    lg    = log --graph --abbrev-commit --decorate --date=relative --all --format=format:'%C(bold blue)%h%C(reset) %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(bold yellow)%d%C(reset)'
    ll    = log --oneline --all
    lf    = log --oneline --follow  # follow renames for a specific file (pass path)
    today = log --since=midnight --author="$(git config user.name)" --oneline
    week  = log --since=1.week.ago --author="$(git config user.name)" --oneline

    # ── Diff ─────────────────────────────────────────────────────────────────
    d     = diff
    ds    = diff --staged           # diff of what is staged (about to be committed)
    dc    = diff --cached           # same as --staged
    dt    = difftool

    # ── Stash ────────────────────────────────────────────────────────────────
    ss    = stash push --include-untracked
    sl    = stash list
    sp    = stash pop
    sa    = stash apply
    sd    = stash drop

    # ── Remotes ──────────────────────────────────────────────────────────────
    rv    = remote -v

    # ── Finding things ───────────────────────────────────────────────────────
    find  = "!git log --all --full-history -- '*$1*'"   # find commits that touched a path
    grep-commit = "!f() { git log --all --oneline --grep=\"$1\"; }; f"   # search commit messages
    who   = shortlog -sne --all    # who has committed and how many times

    # ── Cleanup ──────────────────────────────────────────────────────────────
    # Delete all local branches that have been merged into current branch
    cleanup = "!git branch --merged | grep -v '\\*\\|main\\|master\\|develop' | xargs -n1 git branch -d"
    # Prune remote-tracking refs and delete local branches whose remotes are gone
    tidy   = "!git fetch -p && git branch -vv | grep ': gone]' | awk '{print $1}' | xargs -r git branch -d"

    # ── Debugging ────────────────────────────────────────────────────────────
    # Find which commit introduced a string
    pickaxe = "!f() { git log -S\"$1\" --oneline; }; f"
    # Show the tree of a commit
    tree  = "!git ls-tree --name-only -r HEAD"
```

**The `lg` alias** is the one most developers find transformative. It shows a compact, colour-coded, graphical commit log for all branches:

```bash
git lg
# Output:
# * abc1234 (2 hours ago) fix login validation - You (HEAD -> main)
# * def5678 (3 hours ago) add OAuth provider - You
# | * ghi9012 (1 day ago) update README - Colleague (origin/feature/docs)
# |/
# * jkl3456 (2 days ago) initial commit - You (origin/main)
```

**The `gone` alias** is useful for cleaning up branches whose remote tracking branch has been deleted (typically after a PR is merged):

```bash
git gone
# Deletes: feature/login, fix/header-bug, chore/deps
```

---

## 6. Sharing aliases across machines

If you work on multiple machines (laptop, desktop, work machine), keeping your aliases in sync means not having to reconfigure everything on each one. The standard approach is to keep your dotfiles in a Git repository.

### The dotfiles approach

Create a repository to hold your configuration files:

```bash
mkdir -p ~/dotfiles
cd ~/dotfiles
git init
git remote add origin git@github.com:yourusername/dotfiles.git
```

Copy your config files in:

```bash
cp ~/.gitconfig ~/dotfiles/gitconfig
cp ~/.zshrc ~/dotfiles/zshrc
cp ~/.config/starship.toml ~/dotfiles/starship.toml
```

Create symlinks from the standard locations back to the dotfiles repo:

```bash
ln -sf ~/dotfiles/gitconfig ~/.gitconfig
ln -sf ~/dotfiles/zshrc ~/.zshrc
```

Commit and push:

```bash
cd ~/dotfiles
git add .
git commit -m "add: initial dotfiles"
git push -u origin main
```

On a new machine, clone and set up symlinks:

```bash
git clone git@github.com:yourusername/dotfiles.git ~/dotfiles
cd ~/dotfiles
ln -sf ~/dotfiles/gitconfig ~/.gitconfig
ln -sf ~/dotfiles/zshrc ~/.zshrc
```

Many developers write a short `install.sh` script that creates all the symlinks automatically. Tools like **chezmoi**, **yadm** and **stow** automate the symlink management and handle machine-specific differences (e.g. different values for `core.editor` on macOS vs Linux).

### Machine-specific config

Use the `include` directive for machine-specific settings that should not be in the shared dotfiles:

```ini
# ~/.gitconfig (in your dotfiles repo, shared across all machines)
[user]
    name = Your Name
    email = you@personal.com

[include]
    path = ~/.gitconfig-local    # machine-specific overrides, not in dotfiles repo
```

```ini
# ~/.gitconfig-local (not in dotfiles repo, different on each machine)
[user]
    email = work@company.com    # work machine uses different email

[core]
    editor = goland --wait      # JetBrains on this machine
```

---

## 7. Shell aliases vs git aliases

Git aliases (`git config --global alias.xx ...`) and shell aliases (`alias gs='git status'`) serve similar purposes but have different characteristics.

|                        | Git aliases                  | Shell aliases                             |
| ---------------------- | ---------------------------- | ----------------------------------------- |
| **Syntax**             | `git xx`                     | `xx`                                      |
| **Where stored**       | `~/.gitconfig`               | `~/.bashrc`, `~/.zshrc`                   |
| **Works in any shell** | Yes (git reads `.gitconfig`) | No (each shell has its own)               |
| **Works in IDEs**      | Yes (IDEs call `git xx`)     | No (IDEs do not source your shell config) |
| **Shell scripting**    | Limited (needs `!` prefix)   | Full                                      |
| **Autocomplete**       | Via git's own completion     | Via shell completion                      |

**Git aliases** are better for: operations that should work the same in every tool and shell, operations you want IDEs to be able to invoke and complex git-specific workflows.

**Shell aliases** are better for: two-character shortcuts you use hundreds of times a day, combining git with other Unix tools and operations that mix git with non-git commands.

Many developers use both - a set of shell aliases for the most frequently typed operations and git aliases for more complex multi-step operations.

```bash
# Shell aliases in ~/.zshrc (fast, no git prefix needed)
alias g='git'
alias gs='git status --short --branch'
alias gl='git log --oneline --graph --all -15'
alias gp='git push'
alias gpl='git pull'
alias ga='git add'
alias gc='git commit'
alias gd='git diff'
alias gco='git checkout'
alias gsw='git switch'

# Git aliases in ~/.gitconfig (works in IDEs, more complex)
# (see the library in section 5)
```

---

## 8. Try It Yourself

**Exercise 1 - add the lg alias and use it**

Add the `lg` alias from section 5 to your `~/.gitconfig`. Go to any repository with a few branches and commits and run `git lg`. Compare the output to plain `git log --oneline --all`. Notice the graph, colours, relative dates and branch labels.

**Exercise 2 - create a cleanup alias**

Add the `cleanup` alias from section 5. In a test repository, create three branches and merge two of them into main. Run `git cleanup` and verify it deletes the merged branches but not the unmerged one.

**Exercise 3 - build a custom shell function alias**

Add this alias to your `~/.gitconfig`:

```ini
[alias]
    new-branch = "!f() { git switch --create \"$1\" && echo \"Created and switched to $1\"; }; f"
```

Test it: `git new-branch feature/test-alias`. Verify you are on the new branch with `git branch`.

**Exercise 4 - set up a wip alias**

Add `wip = "!git add -A && git commit -m 'WIP'"` to your aliases. In a repository, make a few changes to files. Run `git wip`. Verify the commit was created with `git log -1`. Then undo it with `git undo` (if you have that alias) or `git reset HEAD~1 --mixed`.

**Exercise 5 - list all your aliases**

Add `aliases = config --get-regexp '^alias\.'` to your `[alias]` section. Run `git aliases`. Read through the list. Do any aliases shadow existing Git commands you use (e.g. if you have `alias.c = commit` and you type `git c`, did you know that was running your alias)?

---

## 9. Common Mistakes

Creating an alias that shadows a real Git command. If you write `alias.st = stash` and have always used `git st` expecting status, you have now broken your own muscle memory. Check what a command does before aliasing it: `git help st`.

Shell aliases with quoting errors. The value of a git alias is a string that gets passed to git (or the shell for `!` aliases). Quoting is finicky, especially in `~/.gitconfig` where the format does not use JSON. Test complex aliases on the command line with `git -c alias.test='...' test` before adding them to your config.

`!` aliases not working on Windows outside of Git Bash. The `!` prefix runs a POSIX shell command. In cmd.exe or PowerShell, this may fail or behave differently. If you use Windows natively (not WSL), keep shell aliases simple or test each one in Git Bash.

Forgetting that git aliases do not support tab completion by default for custom argument names. If you type `git sw <tab>` you get branch completions because `git switch` has completion defined. But a custom alias `git myswitch <tab>` will not get completions unless you write a shell completion function for it. For frequently used aliases, adding completion is worth the effort.

Syncing the `.gitconfig` that contains work credentials to a public dotfiles repository. If your `~/.gitconfig` contains `user.email = you@company.com` or a signing key path, and you push it to a public GitHub repo, you have exposed that information. Use `[include]` with a `~/.gitconfig-local` file for anything machine-specific or sensitive, and only commit the shared file.

---

## 10. Summary

Git aliases live in the `[alias]` section of `~/.gitconfig`. A simple alias replaces a long command with a short one: `git config --global alias.st "status --short --branch"` makes `git st` work. Aliases starting with `!` run as shell commands and can use full shell scripting including `$1` positional arguments and functions.

The most useful aliases to add immediately: `lg` for a colour-coded graphical log, `pf` for a safe force push, `amend` for quick message-free amends, `undo` for reversing a commit without losing changes, `wip` for saving in-progress work, and `cleanup`/`tidy` for removing stale branches.

Keep aliases in a dotfiles repository for easy deployment across machines. Use `[include]` with a local config file for machine-specific settings you do not want in a shared repo. Shell aliases (in `~/.zshrc`) are good for the highest-frequency two-character shortcuts; git aliases are better for complex workflows that need to work in IDEs and multiple shells.

---

## 11. Sources

- [git-config - alias documentation](https://git-scm.com/docs/git-config#Documentation/git-config.txt-alias)
- [Git aliases - Atlassian](https://www.atlassian.com/git/tutorials/git-alias)
- [Must-have Git aliases](https://www.durdn.com/blog/2012/11/22/must-have-git-aliases-advanced-examples/)
- [chezmoi dotfiles manager](https://www.chezmoi.io)
- [yadm dotfiles manager](https://yadm.io)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

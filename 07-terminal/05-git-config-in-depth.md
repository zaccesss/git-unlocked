# Git Config in Depth

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

Every Git command you run reads configuration before doing anything. Knowing how that configuration works - where the files are, which overrides which, what the most useful settings do and how to scope settings to specific projects or remotes - lets you set Git up once and have it behave exactly the way you want everywhere.

---

## Table of Contents

1. [The three configuration levels](#1-the-three-configuration-levels)
2. [File locations on every platform](#2-file-locations-on-every-platform)
3. [Reading and writing config](#3-reading-and-writing-config)
4. [The anatomy of a .gitconfig file](#4-the-anatomy-of-a-gitconfig-file)
5. [Essential settings to configure](#5-essential-settings-to-configure)
6. [Conditional includes for work and personal](#6-conditional-includes-for-work-and-personal)
7. [A complete recommended .gitconfig](#7-a-complete-recommended-gitconfig)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. The three configuration levels

Git configuration is layered across three levels. Each level can override settings from the level below it.

**System** - applies to every user on the machine and every repository. Set by an administrator. Rarely edited by individual developers.

**Global** - applies to you across all your repositories on this machine. This is where most personal settings live. Stored in `~/.gitconfig`.

**Local** - applies only to the specific repository you are in. Stored in `.git/config` inside the repository. Overrides global settings for that project.

The precedence order: **local wins over global, global wins over system**. If `user.email` is set in both global and local, Git uses the local value inside that repository.

There is also a **worktree** level for Git worktrees (advanced, requires `extensions.worktreeConfig=true`) and inline **command-line** overrides with `git -c key=value command` which override everything.

Full precedence, lowest to highest:

```
system < global (XDG) < global (~/.gitconfig) < local < worktree < command-line -c
```

---

## 2. File locations on every platform

| Level | 🐧 Linux | 🍎 macOS | 🪟 Windows |
| ----- | -------- | -------- | ---------- |
| System | `/etc/gitconfig` | `/etc/gitconfig` (CLT) or `/opt/homebrew/etc/gitconfig` (Homebrew) | `C:\Program Files\Git\etc\gitconfig` |
| Global (XDG) | `~/.config/git/config` | `~/.config/git/config` | `%USERPROFILE%\.config\git\config` |
| Global (user) | `~/.gitconfig` | `~/.gitconfig` | `C:\Users\<name>\.gitconfig` |
| Local | `<repo>/.git/config` | `<repo>/.git/config` | `<repo>\.git\config` |

If both `~/.gitconfig` and `~/.config/git/config` exist, Git reads both. `~/.gitconfig` is read last, so it takes precedence. Running `git config --global` writes to `~/.gitconfig` if it exists.

Also in `~/.config/git/` (or `~/.gitconfig`-adjacent):
- `~/.config/git/ignore` - your global gitignore (patterns ignored everywhere)
- `~/.config/git/attributes` - global gitattributes
- `~/.config/git/allowed_signers` - SSH signing verification

---

## 3. Reading and writing config

**Read a single value**:

```bash
git config user.email
git config core.editor
```

**List all resolved values** (shows what Git will actually use):

```bash
git config --list
```

**List values with their source file** (essential for debugging):

```bash
git config --list --show-origin
# Output: file:/home/user/.gitconfig  user.email=you@example.com
```

**List values with their scope** (Git 2.26+):

```bash
git config --list --show-scope
# Output: global  user.email=you@example.com
#         local   user.email=work@company.com
```

**Set a value**:

```bash
# Global (your personal settings)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Local (only this repository)
git config --local user.email "work@company.com"

# System (all users on the machine - requires admin)
git config --system core.autocrlf input
```

**Unset a value**:

```bash
git config --global --unset user.email
git config --global --unset-all alias.lg   # remove all values for a key
```

**Edit the config file directly**:

```bash
git config --global --edit    # opens ~/.gitconfig in your configured editor
git config --edit             # opens the local .git/config
```

**Get the path to a config file**:

```bash
git config --global --edit  # tells you the path in the editor title
git config --list --show-origin | head -1  # shows first file path
```

**Check a specific scope explicitly**:

```bash
git config --global user.email    # only looks in global
git config --local core.editor    # only looks in local
```

**One-time override without changing config**:

```bash
git -c user.email="temp@example.com" commit -m "message"
```

---

## 4. The anatomy of a .gitconfig file

`.gitconfig` uses an INI-style format with sections, subsections, keys and values.

```ini
# This is a comment

[section]
    key = value
    another-key = another value

[section "subsection"]
    key = value

[alias]
    st = status --short --branch
    lg = log --oneline --graph --all
```

- **Sections** are in square brackets: `[core]`, `[user]`, `[alias]`
- **Subsections** add a quoted string: `[remote "origin"]`, `[branch "main"]`, `[includeIf "gitdir:~/work/"]`
- **Keys** are indented (convention - not required) and use `=`
- Section and key names are **case-insensitive**; subsection names are **case-sensitive**
- **Booleans** accept `true`, `false`, `yes`, `no`, `on`, `off`, `1`, `0`
- **Integers** support `k`, `m`, `g` suffixes: `core.packedGitLimit = 512m`
- **Multi-valued keys** appear multiple times: `remote.origin.fetch = +refs/heads/*:refs/remotes/origin/*`
- **Comments** start with `#` or `;`

Values can reference other values with `%(key)` notation and the special value `~/` expands to the home directory. Variables set with `git config` write values without quotes; the `=` is required.

---

## 5. Essential settings to configure

These are the settings that make the most practical difference to your daily Git workflow. The table notes the Git version that introduced each setting.

### Identity

```ini
[user]
    name = Your Name
    email = you@example.com
    useConfigOnly = true    # refuse to guess identity - forces explicit config
```

`useConfigOnly = true` makes Git fail with an error if `user.email` is not set in any config scope. This is useful on machines where you forget to configure identity - Git will tell you rather than silently using a wrong email.

### Initialisation and branches

```ini
[init]
    defaultBranch = main    # Git 2.28+ (July 2020) - no more 'master' by default
```

### Push behaviour

```ini
[push]
    default = simple          # push to upstream of current branch only
    autoSetupRemote = true    # Git 2.37+ (June 2022) - no more -u on first push
    followTags = true         # push annotated tags when pushing a branch
```

`push.autoSetupRemote = true` is one of the most useful modern Git settings. Before it existed, the first push to a new branch required `git push -u origin branchname`. With this set, `git push` works the same way on the first push as on subsequent ones.

### Pull behaviour

```ini
[pull]
    rebase = true    # rebase instead of merge when pulling (cleaner history)
    # Alternative: ff = only  (only allow fast-forward pulls, error otherwise)
```

### Fetch behaviour

```ini
[fetch]
    prune = true      # delete local remote-tracking refs when remote deletes a branch
    pruneTags = true  # also prune remote-tracking tags
    all = true        # fetch from all configured remotes, not just origin
```

### Merge conflict display

```ini
[merge]
    conflictStyle = zdiff3    # Git 2.35+ (Jan 2022) - shows the original content
                               # alongside both sides, much easier to resolve
```

`zdiff3` conflict style adds a third section (the original code before both sides changed it) to conflict markers. This makes most conflicts much easier to understand. Old style:

```
<<<<<<< HEAD
your version
=======
their version
>>>>>>> feature
```

With `zdiff3`:

```
<<<<<<< HEAD
your version
||||||| original  
original code before either side changed it
=======
their version
>>>>>>> feature
```

### Rebase behaviour

```ini
[rebase]
    autoSquash = true     # auto-squash fixup! and squash! commits during rebase
    autoStash = true      # stash before rebase if needed, pop after
    updateRefs = true     # Git 2.38+ - update dependent branches during rebase
```

`rebase.updateRefs` is essential for stacked branch workflows. When you rebase a branch that has other branches stacked on top of it, Git automatically updates all the dependent branches to follow.

### Rerere (reuse recorded resolution)

```ini
[rerere]
    enabled = true       # record how you resolved a conflict
    autoupdate = true    # automatically stage resolved conflicts
```

rerere ("reuse recorded resolution") remembers how you resolved a conflict and automatically applies the same resolution if the same conflict appears again. Extremely useful during long rebases.

### Diff quality

```ini
[diff]
    algorithm = histogram    # better diff algorithm - fewer false positives
    colorMoved = zebra       # show moved code differently from changed code
    colorMovedWS = allow-indentation-change  # ignore whitespace in moved detection
    mnemonicPrefix = true    # use i/ w/ c/ instead of a/ b/ in diff headers
    renames = true           # detect renames, not just add/delete pairs
```

### Sorting and display

```ini
[branch]
    sort = -committerdate    # show most recently committed branches first

[tag]
    sort = -v:refname        # sort tags newest-first by version number

[column]
    ui = auto                # show branches/tags in columns when there are many

[log]
    date = iso               # show dates in ISO 8601 format
```

### Performance on large repositories

```ini
[core]
    fsmonitor = true         # Git 2.37+ - use OS file system monitor for speed
    untrackedCache = true    # cache untracked file results (pairs with fsmonitor)
```

On large repositories, `git status` can take several seconds because Git has to stat every file. `core.fsmonitor = true` enables a built-in filesystem monitor that watches for changes and caches the results. Combined with `core.untrackedCache`, `git status` becomes near-instantaneous even on repositories with hundreds of thousands of files.

### Commit signing

```ini
[commit]
    gpgsign = true           # sign every commit

[gpg]
    format = ssh             # use SSH key for signing (simpler than GPG)

[gpg "ssh"]
    allowedSignersFile = ~/.config/git/allowed_signers
```

SSH commit signing (Git 2.34+, November 2021) is simpler to set up than GPG. Generate an SSH key (`ssh-keygen -t ed25519`), add the public key to GitHub's signing keys (separate from authentication keys) and set `user.signingkey`:

```ini
[user]
    signingkey = ~/.ssh/id_ed25519.pub
```

Create `~/.config/git/allowed_signers` with your own key for local verification:

```
you@example.com namespaces="git" ssh-ed25519 AAAA...your_public_key...
```

---

## 6. Conditional includes for work and personal

Conditional includes let you automatically load different config files depending on which repository you are in. This is the clean solution for developers who have both work and personal repositories on the same machine with different email addresses.

```ini
# ~/.gitconfig

[user]
    name = Your Name
    email = personal@gmail.com   # default identity

# Override identity for everything inside ~/work/
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

# Override for a specific company's repos (Git 2.36+)
[includeIf "hasconfig:remote.*.url:git@github.com:mycompany/**"]
    path = ~/.gitconfig-work

# Override based on current branch
[includeIf "onbranch:release/"]
    path = ~/.gitconfig-release
```

```ini
# ~/.gitconfig-work
[user]
    email = your.name@company.com
    signingkey = ~/.ssh/id_ed25519_work.pub
```

**How `gitdir:` matching works**:

- `gitdir:~/work/` - the trailing slash is important. It expands to `~/work/**` and matches any repository anywhere under `~/work/`
- Without trailing slash: exact match only
- `gitdir/i:~/Work/` - case-insensitive match (useful on macOS/Windows)

When you clone a repository into `~/work/myproject` and commit, Git sees that `.git` is inside `~/work/`, matches the `includeIf`, loads `~/.gitconfig-work` and uses your work email automatically. No manual switching needed.

> [!IMPORTANT]
> `hasconfig:remote.*.url:` conditions (Git 2.36+) cannot reference remote URLs themselves. These conditions work by reading the local `.git/config` of the repository you are in - which means the repo must already have been cloned. They cannot be used to set the credentials needed to clone in the first place.

---

## 7. A complete recommended .gitconfig

This is a well-configured `~/.gitconfig` for 2026, incorporating all the settings discussed in this file:

```ini
[user]
    name = Your Name
    email = you@personal.com
    useConfigOnly = true

[init]
    defaultBranch = main

[core]
    editor = code --wait       # change to nvim, nano, etc.
    pager = less -FRX
    autocrlf = input           # input on Mac/Linux; true on Windows
    excludesFile = ~/.config/git/ignore
    fsmonitor = true
    untrackedCache = true

[push]
    default = simple
    autoSetupRemote = true
    followTags = true

[pull]
    rebase = true

[fetch]
    prune = true
    pruneTags = true
    all = true

[rebase]
    autoSquash = true
    autoStash = true
    updateRefs = true

[merge]
    conflictStyle = zdiff3

[rerere]
    enabled = true
    autoupdate = true

[diff]
    algorithm = histogram
    colorMoved = zebra
    colorMovedWS = allow-indentation-change
    mnemonicPrefix = true
    renames = true

[branch]
    sort = -committerdate

[tag]
    sort = -v:refname

[column]
    ui = auto

[log]
    date = iso

[status]
    branch = true
    submoduleSummary = true

[commit]
    verbose = true             # show the diff in the commit message editor
    gpgsign = true

[gpg]
    format = ssh

[gpg "ssh"]
    allowedSignersFile = ~/.config/git/allowed_signers

[help]
    autocorrect = prompt       # ask before running the suggested command

[color]
    ui = auto

[credential]
    helper = osxkeychain       # macOS; Windows: manager; Linux: /usr/share/doc/git/...

[alias]
    # Covered fully in 06-git-aliases.md
    s = status --short --branch
    sw = switch
    last = log -1 HEAD --stat
    undo = reset HEAD~1 --mixed
    aliases = config --get-regexp '^alias\.'

# Conditional includes
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work
[includeIf "gitdir:~/personal/"]
    path = ~/.gitconfig-personal
```

---

## 8. Try It Yourself

**Exercise 1 - inspect your current config**

Run `git config --list --show-origin` in a repository. Read through the output. Can you identify which settings come from your system, global and local configs? Which file is setting your `user.email`? What editor is configured?

**Exercise 2 - set up local identity override**

Create a directory called `~/work-test` and initialise a repository inside it:

```bash
mkdir -p ~/work-test/project && cd ~/work-test/project && git init
```

Set a local email override:

```bash
git config --local user.email "work-test@company.com"
```

Verify with `git config user.email` - it should show the local value. Then run `git config --list --show-scope` to see all values and their scopes. Remove the local override with `git config --local --unset user.email`.

**Exercise 3 - set up a conditional include**

Create `~/.gitconfig-work` with just a different email:

```ini
[user]
    email = you@work.com
```

Add this to your `~/.gitconfig`:

```ini
[includeIf "gitdir:~/work-test/"]
    path = ~/.gitconfig-work
```

Go into `~/work-test/project` and run `git config user.email` - it should show `you@work.com`. Go to any other repository and run the same command - it should show your default email.

**Exercise 4 - add useful settings**

Add these settings to your `~/.gitconfig` if they are not already there:

```bash
git config --global push.autoSetupRemote true
git config --global merge.conflictStyle zdiff3
git config --global rebase.autoSquash true
git config --global branch.sort -committerdate
git config --global diff.algorithm histogram
```

Verify they were written: `git config --list --show-origin | grep -E "push|merge|rebase|branch|diff"`

**Exercise 5 - test zdiff3 conflict style**

In a test repository, create a file with some content and make two conflicting changes on different branches. Merge them to create a conflict. Look at the conflict markers - you should see three sections (your version, the original, their version) instead of two. Resolve it. Then change `merge.conflictStyle` back to `diff3` (or remove it entirely) and create the same conflict again to see the difference.

---

## 9. Common Mistakes

Setting `user.email` globally and wondering why it shows the wrong email in a work repository. Use `git config --list --show-scope` inside the repository to see exactly which value is winning and where it comes from. Add a local override or set up a conditional include.

Editing the wrong config file. `git config --global --edit` opens `~/.gitconfig`. `git config --edit` (no `--global`) opens `.git/config` for the current repository. If you want your change to apply everywhere, make sure you use `--global`.

Using `merge.conflictStyle = zdiff3` on a machine with Git older than 2.35. Git 2.35 was released in January 2022, so most modern systems have it, but older machines will error with an unknown option. Check with `git --version` before adding this setting to a shared config.

Adding `push.autoSetupRemote = true` on Git older than 2.37 (June 2022). Same issue - this setting is silently ignored on older versions, which means `git push` will still fail on first push from a new branch. Verify your Git version.

Putting `GIT_EDITOR` in `~/.bashrc` instead of `core.editor` in `~/.gitconfig`. Environment variables in shell config affect all programs started from that shell, not just Git. If VS Code's integrated terminal sources your `.bashrc`, `GIT_EDITOR=vim` will cause vim to open every time VS Code runs a `git commit` internally. Use `core.editor` in `.gitconfig` for IDE compatibility.

---

## 10. Summary

Git configuration has three levels - system, global and local - with local overriding global and global overriding system. Global config lives in `~/.gitconfig`, local config in `.git/config`. `git config --list --show-origin` shows every setting and which file it came from.

The most impactful settings to configure: `push.autoSetupRemote = true` eliminates the `-u` flag on first push (Git 2.37+), `merge.conflictStyle = zdiff3` makes conflict markers much easier to read (Git 2.35+), `rebase.updateRefs = true` keeps stacked branches in sync (Git 2.38+), `core.fsmonitor = true` makes `git status` near-instantaneous on large repositories (Git 2.37+), `branch.sort = -committerdate` shows your most recent branches first and `diff.algorithm = histogram` produces cleaner diffs.

Conditional includes (`[includeIf "gitdir:~/work/"]`) automatically load different config files based on which repository you are in - the clean solution for separate work and personal email addresses on the same machine.

---

## 11. Sources

- [git-config documentation](https://git-scm.com/docs/git-config)
- [Pro Git - Customising Git](https://git-scm.com/book/en/v2/Customising-Git-Git-Configuration)
- [SSH commit signing - GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)
- [Git 2.37 release notes](https://github.blog/2022-06-27-highlights-from-git-2-37/)
- [Git 2.35 release notes - zdiff3](https://github.blog/2022-01-24-highlights-from-git-2-35/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

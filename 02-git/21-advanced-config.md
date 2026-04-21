# Advanced Git Configuration

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

Git's configuration system is more powerful than most developers realise. Beyond setting your name and email, Git config lets you create command shortcuts, apply different settings to different repositories automatically, customise the diff and merge tools, optimise performance and tailor Git's behaviour to your exact workflow. This file covers the configuration options that make experienced Git users significantly more productive.

---

## Table of Contents

- [Configuration levels](#configuration-levels)
- [Aliases](#aliases)
- [Useful built-in aliases](#useful-built-in-aliases)
- [Conditional includes](#conditional-includes)
- [Diff and merge tools](#diff-and-merge-tools)
- [Performance settings](#performance-settings)
- [Colour and formatting](#colour-and-formatting)
- [Safety settings](#safety-settings)
- [Useful configuration options](#useful-configuration-options)
- [Viewing and editing config](#viewing-and-editing-config)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Configuration Levels

Git has three configuration levels, each overriding the previous:

**System** (`/etc/gitconfig`) - applies to every user on the machine. Rarely modified directly.

**Global** (`~/.gitconfig` or `~/.config/git/config`) - applies to all repositories for the current user. This is where most personal settings go.

**Local** (`.git/config` in the repository) - applies only to the current repository. Overrides global settings.

```bash
git config --system ...    # system level
git config --global ...    # global level (most common)
git config --local ...     # local level (current repo only)
```

**Viewing all config with their origins:**

```bash
git config --list --show-origin
```

This shows every setting and which file it came from, making it easy to diagnose unexpected behaviour.

---

## Aliases

Aliases let you create shortcuts for Git commands. They are one of the highest-value configuration options - a well-chosen set of aliases can eliminate a significant amount of repetitive typing.

**Create an alias:**

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
```

Now `git st` runs `git status`, `git co` runs `git checkout` and so on.

**Create aliases for complex commands:**

```bash
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.last "log -1 HEAD --stat"
git config --global alias.unstage "restore --staged"
git config --global alias.undo "reset --soft HEAD~1"
```

**Create an alias for a shell command (prefix with `!`):**

```bash
git config --global alias.root '!pwd'
git config --global alias.open '!open .'
```

The `!` prefix runs the command in the shell rather than as a Git subcommand. This lets you run any shell command through Git.

**View all configured aliases:**

```bash
git config --global --get-regexp alias
```

---

## Useful Built-in Aliases

Here is a recommended set of aliases that many experienced developers use:

```ini
[alias]
    # Short forms of common commands
    st = status -sb
    co = checkout
    sw = switch
    br = branch
    ci = commit
    di = diff
    dc = diff --cached

    # Pretty log views
    lg = log --oneline --graph --all --decorate
    ll = log --pretty=format:'%C(yellow)%h%Creset %s %C(cyan)(%ar)%Creset %C(green)<%an>%Creset' --graph
    last = log -1 HEAD --stat

    # Undo helpers
    undo = reset --soft HEAD~1
    unstage = restore --staged
    discard = restore

    # Stash shortcuts
    ss = stash push
    sp = stash pop
    sl = stash list

    # Branch management
    gone = "!git fetch --prune && git branch -vv | grep ': gone]' | awk '{print $1}' | xargs git branch -D"

    # Show a summary of what changed in the last commit
    what = show --stat HEAD
```

To add these, open your global config with `git config --global --edit` and paste the `[alias]` block.

---

## Conditional Includes

Conditional includes let Git automatically use different configuration based on the repository's location. The most common use case: different work and personal email addresses for different repositories.

**Include a different config for all repositories in a specific directory:**

In your `~/.gitconfig`:

```ini
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work

[includeIf "gitdir:~/personal/"]
    path = ~/.gitconfig-personal
```

In `~/.gitconfig-work`:

```ini
[user]
    email = your.name@company.com
    signingkey = WORK_GPG_KEY_ID
```

In `~/.gitconfig-personal`:

```ini
[user]
    email = your@personal.com
    signingkey = PERSONAL_GPG_KEY_ID
```

Any repository under `~/work/` automatically uses your work email. Any repository under `~/personal/` uses your personal email. No manual switching required.

**Conditional include based on URL:**

```ini
[includeIf "hasconfig:remote.*.url:git@gitlab.company.com:*"]
    path = ~/.gitconfig-work
```

This applies the work config to any repository cloned from your company's GitLab server.

> [!TIP]
> Conditional includes are invaluable for developers who contribute to both work and personal projects. They eliminate the common mistake of accidentally committing to a work repository with a personal email or vice versa.

---

## Diff and Merge Tools

**Set a diff tool:**

```bash
git config --global diff.tool vscode
git config --global difftool.vscode.cmd 'code --wait --diff $LOCAL $REMOTE'
```

Run with:

```bash
git difftool
```

**Set a merge tool:**

```bash
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

Run with:

```bash
git mergetool
```

**Other popular diff/merge tools:**

| Tool | Platform | Notes |
|---|---|---|
| VS Code | All | Built-in 3-way merge editor |
| IntelliJ IDEA | All | Excellent 3-way merge view |
| vimdiff | All | Terminal-based, powerful |
| Meld | Linux/Mac | Open source, visual |
| kdiff3 | All | Open source, 3-way merge |
| Beyond Compare | All | Commercial, very powerful |
| P4Merge | All | Free from Perforce |

**Suppress the "Launch 'vscode' [Y/n]?" prompt:**

```bash
git config --global difftool.prompt false
git config --global mergetool.prompt false
```

---

## Performance Settings

**Enable the filesystem monitor for large repositories:**

```bash
git config --global core.fsmonitor true
git config --global core.untrackedCache true
```

`fsmonitor` uses the operating system's file change notifications instead of scanning every file. It significantly speeds up `git status` in large repositories.

**Enable multi-threading for index operations:**

```bash
git config --global index.threads true
```

**Enable parallel checkout (Git 2.26+):**

```bash
git config --global checkout.workers 0
```

Setting to `0` uses one worker per CPU core. Speeds up checkout in large repositories.

**Enable commit graph for faster log operations:**

```bash
git config --global fetch.writeCommitGraph true
```

---

## Colour and Formatting

**Enable colour output everywhere:**

```bash
git config --global color.ui auto
```

**Customise specific colours:**

```bash
git config --global color.diff.old "red bold"
git config --global color.diff.new "green bold"
git config --global color.status.added "green"
git config --global color.status.changed "yellow"
git config --global color.status.untracked "cyan"
```

**Customise the log format:**

```bash
git config --global format.pretty "oneline"
```

**Configure the pager:**

```bash
git config --global core.pager "less -FX"
```

`-F` exits immediately if the output fits on one screen. `-X` prevents clearing the screen after exit.

---

## Safety Settings

**Prevent force pushes from silently overwriting remote work:**

```bash
git config --global push.default current
```

This pushes the current branch to a remote branch of the same name, rather than the default which varies by Git version.

**Always use `--force-with-lease` behaviour:**

```bash
git config --global alias.fpush "push --force-with-lease"
```

**Protect against accidental `git pull` creating merge commits:**

```bash
git config --global pull.rebase false    # use merge (beginners)
# or
git config --global pull.rebase true     # use rebase (advanced)
# or
git config --global pull.ff only         # only allow fast-forward pulls
```

`pull.ff only` is the strictest - it refuses to pull if the result would not be a fast-forward, forcing you to explicitly rebase or merge.

**Set a default initial branch name:**

```bash
git config --global init.defaultBranch main
```

---

## Useful Configuration Options

**Set line ending behaviour (already covered in setup - included for reference):**

```bash
# Windows
git config --global core.autocrlf true
# Mac and Linux
git config --global core.autocrlf input
```

**Set the default editor:**

```bash
git config --global core.editor "code --wait"    # VS Code
git config --global core.editor "nano"            # Nano
git config --global core.editor "vim"             # Vim
```

**Store credentials (already covered in setup):**

```bash
git config --global credential.helper manager     # Windows
git config --global credential.helper osxkeychain # Mac
git config --global credential.helper libsecret   # Linux
```

**Automatically prune stale remote references on fetch:**

```bash
git config --global fetch.prune true
```

**Always push annotated tags with commits:**

```bash
git config --global push.followTags true
```

**Sign all commits automatically:**

```bash
git config --global commit.gpgsign true
```

**Show branch tracking info in status:**

```bash
git config --global status.showUntrackedFiles all
```

**Reuse recorded resolutions (rerere):**

```bash
git config --global rerere.enabled true
```

`rerere` (Reuse Recorded Resolution) remembers how you resolved a conflict and automatically applies the same resolution if the same conflict appears again. Extremely useful in long-running branches.

---

## Viewing and Editing Config

**View the full global config:**

```bash
git config --global --list
```

**View a specific setting:**

```bash
git config --global user.email
```

**Open the global config file in your editor:**

```bash
git config --global --edit
```

**The global config file location:**

- Mac and Linux: `~/.gitconfig`
- Windows: `C:\Users\YOUR_USERNAME\.gitconfig`

You can edit this file directly in any text editor. It uses INI format:

```ini
[user]
    name = Your Name
    email = your@email.com
    signingkey = ~/.ssh/id_ed25519.pub

[core]
    editor = code --wait
    autocrlf = input
    fsmonitor = true

[alias]
    st = status -sb
    lg = log --oneline --graph --all --decorate
    undo = reset --soft HEAD~1

[push]
    followTags = true

[fetch]
    prune = true

[pull]
    rebase = false

[init]
    defaultBranch = main
```

---

## Try It Yourself

**Step 1.** View your current global configuration:

```bash
git config --global --list
```

**Step 2.** Set up some useful aliases:

```bash
git config --global alias.st "status -sb"
git config --global alias.lg "log --oneline --graph --all --decorate"
git config --global alias.undo "reset --soft HEAD~1"
git config --global alias.unstage "restore --staged"
```

**Step 3.** Test them:

```bash
git st
git lg
```

**Step 4.** Enable performance improvements:

```bash
git config --global core.fsmonitor true
git config --global fetch.prune true
git config --global push.followTags true
```

**Step 5.** Open your config file to see everything together:

```bash
git config --global --edit
```

**Step 6.** (Optional) Set up conditional includes if you work on both personal and work projects.

Create two config files and add the `[includeIf]` blocks to your `~/.gitconfig` as shown in the Conditional Includes section.

---

## Common Mistakes

**Setting local config when you mean global.**

Running `git config user.email ...` without `--global` sets the email only for the current repository. Your other repositories still use the global email. Always specify `--global` unless you deliberately want repository-specific settings.

**Creating aliases that shadow built-in commands.**

If you create `git config --global alias.push "push --force-with-lease"`, you override the built-in `push`. This might be intentional, but it can surprise you when the behaviour differs from what you expect. Name safety aliases with a different name: `fpush` rather than `push`.

**Not knowing about conditional includes.**

Many developers manually switch their email when moving between work and personal projects. Conditional includes solve this automatically.

**Storing passwords in config.**

Never store passwords in Git config. Use a credential helper (`manager`, `osxkeychain`, `libsecret`) which stores credentials securely in the operating system's keychain.

---

## Summary

- Git has three config levels: system, global and local - local overrides global
- **Aliases** create shortcuts for commands: `git config --global alias.st status`
- **Conditional includes** apply different config to different directory paths - invaluable for separating work and personal repositories
- **Diff and merge tools** can be set with `diff.tool` and `merge.tool`
- **Performance settings** like `core.fsmonitor` and `index.threads` speed up operations in large repos
- **`rerere.enabled true`** remembers conflict resolutions and reapplies them automatically
- View all config with `git config --global --list`; edit directly with `git config --global --edit`

---

## Sources and Further Reading

- [Official git config documentation](https://git-scm.com/docs/git-config) - the complete reference for every config option
- [Pro Git book, Chapter 8.1: Git Configuration](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration) - the official guide to all configuration options
- [Pro Git book, Chapter 7.14: Git Attributes](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes) - advanced configuration using `.gitattributes`
- [Atlassian - Git config](https://www.atlassian.com/git/tutorials/setting-up-a-repository/git-config) - practical guide to common configuration scenarios

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

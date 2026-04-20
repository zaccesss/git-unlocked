# The Terminal and Git

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

This file explains exactly how Git runs when you type a command in the terminal - what the shell actually does, why your Git config applies everywhere, how IDEs tap into the same Git binary you use from the command line, and what happens when Git needs you to type text (like a commit message or a rebase todo list).

---

## Table of Contents

1. [Git is just a program](#1-git-is-just-a-program)
2. [What PATH is and why it matters](#2-what-path-is-and-why-it-matters)
3. [What happens when you type a git command](#3-what-happens-when-you-type-a-git-command)
4. [When Git opens an editor](#4-when-git-opens-an-editor)
5. [How IDEs and editors connect to Git](#5-how-ides-and-editors-connect-to-git)
6. [Line endings across platforms](#6-line-endings-across-platforms)
7. [Git credential management](#7-git-credential-management)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. Git is just a program

Git is an external program, not a built-in feature of any shell. When you type `git status`, your shell does not have any special knowledge of Git - it treats `git` exactly the same way it would treat any other command: it searches for a program called `git` in your PATH and runs it.

This has an important consequence: **any tool that calls the same `git` binary will pick up your Git configuration automatically**. Every alias you define in `~/.gitconfig`, every credential helper you configure, every hook in a repository - all of these apply to VS Code, JetBrains, GitHub Desktop, lazygit, the terminal and every other tool that uses your system Git.

This is also why a broken Git installation affects everything at once. If `git` is not on your PATH, the terminal will say "command not found", your IDE will say "Git not found", and lazygit will refuse to open. The fix is always the same: get `git` onto your PATH.

**Checking which Git you have**:

```bash
# Mac/Linux - shows the path to the git binary
which git
git --version

# Windows PowerShell - shows all git executables on PATH
Get-Command git
git --version

# Windows cmd.exe
where git
git --version
```

**Mac users - the Apple shim**: on macOS, `/usr/bin/git` is a stub that redirects to the Command Line Tools or Xcode's bundled Git. It is often months or years behind the current Git release. For a current version, install via Homebrew and ensure `/opt/homebrew/bin` comes before `/usr/bin` in your PATH:

```bash
brew install git
# Verify - should show /opt/homebrew/bin/git
which git
```

---

## 2. What PATH is and why it matters

PATH is an environment variable that lists the directories your shell searches when you type a command. It is a colon-separated list on Mac/Linux and a semicolon-separated list on Windows.

```bash
# See your current PATH (Mac/Linux)
echo $PATH
# Output example: /opt/homebrew/bin:/usr/local/bin:/usr/bin:/bin

# Windows PowerShell
$env:PATH

# Windows cmd.exe
echo %PATH%
```

When you type `git`, your shell looks in each PATH directory in order until it finds a file called `git` (or `git.exe` on Windows). It runs the first one it finds. If none is found, you get "command not found" (Mac/Linux) or "is not recognised as an internal or external command" (Windows).

**Common PATH problems with Git**:

On **macOS**, Homebrew Git (`/opt/homebrew/bin/git`) must come before Apple's shim (`/usr/bin/git`). Check with `which -a git` which shows all matches in order.

On **Windows**, Git for Windows adds `C:\Program Files\Git\cmd` to the system PATH during installation. If you chose "Use Git from the Windows Command Prompt" during install, Git is available everywhere. If you chose "Use Git from Git Bash only", it is only available inside Git Bash.

On **Linux**, if Git was installed via a package manager it is at `/usr/bin/git` and always on PATH. If compiled from source, verify the install location is on PATH.

**Diagnosing a PATH issue**:

```bash
# Does git exist somewhere on the system at all?
find /usr /opt /usr/local -name "git" -type f 2>/dev/null  # Mac/Linux

# What does PATH actually contain?
echo $PATH | tr ':' '\n'   # one entry per line, Mac/Linux
```

---

## 3. What happens when you type a git command

Let's trace exactly what happens when you type `git log --oneline -5` and press Enter.

**Step 1 - Shell parses the input**

The shell (bash, zsh, PowerShell) reads the line and splits it into tokens: `git` (the command), `log` (first argument), `--oneline` (option flag), `-5` (option argument).

**Step 2 - Shell checks for builtins and aliases**

The shell first checks if `git` is a built-in (it is not) or an alias. If you have `alias git='git --no-pager'` defined, the alias expansion happens here.

**Step 3 - Shell searches PATH**

The shell walks through each directory in PATH and looks for an executable called `git`. It finds `/usr/bin/git` or `/opt/homebrew/bin/git` depending on your setup.

**Step 4 - Shell forks and execs**

The shell creates a child process (fork) and replaces it with the `git` binary (exec). The child inherits environment variables from the shell including `HOME`, `PATH`, `GIT_EDITOR`, `GIT_AUTHOR_NAME` and any `GIT_*` overrides you have set.

**Step 5 - Git reads configuration**

Git reads its config files in order: system config (`/etc/gitconfig`), global config (`~/.gitconfig`), local config (`.git/config` in the repository). Settings in later files override earlier ones.

**Step 6 - Git runs the command**

Git runs `git log` with the arguments you provided, looking in the current directory's `.git/` folder to find the repository.

**Step 7 - Output goes to the terminal**

Git writes its output to stdout. If Git detects the output is going to a terminal (not a pipe or file), it pipes it through a pager (usually `less`) so you can scroll. Set `core.pager` in `.gitconfig` to change this. Press `q` to exit the pager.

**Step 8 - Shell gets control back**

When git finishes, the shell prints the next prompt and waits.

### Git subcommands

`git log`, `git commit`, `git push` etc. are all **subcommands**. The main `git` binary handles most of them directly as built-in functions. Some subcommands are separate executables: `git bisect`, `git stash`, `git subtree` and others live as scripts in Git's libexec directory. Find it with `git --exec-path`. You can add custom subcommands by placing an executable called `git-mycommand` anywhere on your PATH - `git mycommand` will call it.

---

## 4. When Git opens an editor

Several Git commands need you to write text: commit messages, rebase todo lists, merge commit messages, tag messages, notes. When this happens, Git does not collect input from the terminal itself - it opens your configured text editor, waits for you to save and close the file, then reads the result.

**The editor selection chain** (Git checks these in order, uses the first one set):

1. `GIT_EDITOR` environment variable
2. `core.editor` in your Git config
3. `VISUAL` environment variable
4. `EDITOR` environment variable
5. `vi` (fallback on Mac/Linux) or `notepad.exe` (fallback on Windows)

**Setting your editor** in `~/.gitconfig`:

```bash
# VS Code
git config --global core.editor "code --wait"

# Cursor
git config --global core.editor "cursor --wait"

# Neovim
git config --global core.editor nvim

# Vim
git config --global core.editor vim

# nano (simplest option for beginners)
git config --global core.editor nano

# Sublime Text
git config --global core.editor "subl -n -w"

# JetBrains (any IDE - IntelliJ, PyCharm, WebStorm, Rider, GoLand)
git config --global core.editor "idea --wait"

# Notepad++ (Windows)
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

**The `--wait` flag is critical for GUI editors**. Applications like VS Code, Cursor and Sublime Text are designed to open files quickly and return immediately to the command line (so you can keep using other programs). Without `--wait`, Git sees the editor "finish" instantly (because the launcher exited), reads an empty file, and aborts the commit with "Aborting commit due to empty commit message". The `--wait` flag tells the launcher to stay open until the file is closed inside the editor.

Terminal editors like `vim`, `nvim`, `nano`, `emacs -nw` and `helix` always block until you save and quit, so they do not need `--wait`.

**Commands that open an editor**:

| Command | What opens |
| ------- | ---------- |
| `git commit` (no `-m` flag) | The commit message template |
| `git commit --amend` (no `-m` flag) | The previous commit message for editing |
| `git rebase -i HEAD~3` | The rebase todo list |
| `git merge --edit` | The merge commit message |
| `git tag -a v1.0` (no `-m` flag) | Tag annotation |
| `git config --edit` or `-e` | The config file directly |

**Interactive rebase in the terminal**:

Running `git rebase -i HEAD~3` in your terminal opens a file that looks like this in your configured editor:

```
pick a1b2c3d First commit
pick e4f5g6h Second commit
pick i7j8k9l Third commit

# Rebase a1b2c3d..i7j8k9l onto parent (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# d, drop <commit> = remove commit
```

You edit this file as normal text, save and close. Git then runs the rebase according to your instructions. The `GIT_SEQUENCE_EDITOR` variable (or `sequence.editor` config setting) controls which editor opens specifically for the rebase todo list - useful if you want a different editor for rebasing than for commit messages.

---

## 5. How IDEs and editors connect to Git

Most code editors that have "Git integration" are not doing anything magical. They call the same `git` binary you call from the terminal, passing commands and reading the output.

**VS Code, Cursor, Windsurf, Zed, Nova** - these all shell out to the `git` binary on your system PATH for every operation. VS Code's `git.path` setting lets you specify a different binary if needed, but by default it uses whatever `git` is first on PATH. This means your `.gitconfig` settings (aliases, credential helper, hooks, signing key) all apply when you use VS Code's Source Control panel.

**JetBrains IDEs (IntelliJ, PyCharm, WebStorm, Rider, GoLand, CLion)** - these primarily shell out to your configured git binary for write operations (commit, push, pull, fetch, rebase, merge, cherry-pick, stash, submodules). They also use **JGit** (a pure-Java Git implementation) for some read-only UI operations like diff rendering and blame display, which is faster because it avoids forking a process. But all the operations that modify your repository go through the real `git` binary, so your configuration and hooks apply.

**Helix, Neovim, Vim** - these do not have built-in Git integration. Plugins like vim-fugitive and gitsigns.nvim shell out to `git`. lazygit and gitui (covered in [08-lazygit.md](08-lazygit.md)) also call the system `git` binary.

**GitHub Desktop** - this is the exception. GitHub Desktop bundles its own `git` binary at a path inside its app directory and uses that rather than your system Git. This means your system `~/.gitconfig` is read, but the specific Git binary version and any system-level Git config may differ. GitHub Desktop does not make its bundled `git` available on your PATH.

**Xcode** - uses the Git binary at `/Library/Developer/CommandLineTools/usr/bin/git` or inside the Xcode.app bundle. Xcode does not use your Homebrew-installed Git.

**Visual Studio** - bundles its own `git.exe` but can be redirected to system Git via Tools → Options → Source Control → Git Global Settings → Use the system Git instead.

**Sourcetree, Fork, Tower** - these GUI clients call the system `git` binary (or a configurable path). Your configuration applies.

### What this means for your workflow

Because most tools use the same `git` binary and the same `~/.gitconfig`, configuration is a one-time investment. Set `core.editor`, `user.email`, `user.name`, credential helper, and signing key once in `~/.gitconfig` and every tool picks it up automatically.

Git hooks in `.git/hooks/` run regardless of which tool triggered the commit or push - VS Code, the terminal, lazygit, JetBrains - if there is a `pre-commit` hook, it runs. This is why commit linting and pre-commit checks work across your whole team regardless of their preferred tool.

---

## 6. Line endings across platforms

Line endings are a frequent source of invisible problems in cross-platform teams. Windows uses CRLF (`\r\n`, two bytes), Unix and macOS use LF (`\n`, one byte), and Classic Mac OS (pre-OS X) used CR (`\r`). Git stores files as blobs hashed by content - the same file with CRLF and LF endings has a different hash and is treated as different content.

The most common symptom: a file shows as "modified" in Git even though you have not changed it. This happens when your editor or OS silently converts line endings when opening or saving a file.

**The `core.autocrlf` setting controls how Git handles line endings**:

| Value | Behaviour | Use on |
| ----- | --------- | ------ |
| `true` | Convert LF → CRLF on checkout, CRLF → LF on commit | Windows (historical recommendation) |
| `input` | Convert CRLF → LF on commit, no conversion on checkout | Mac/Linux |
| `false` | No automatic conversion | Recommended when using `.gitattributes` |

The modern recommended approach is to set `core.autocrlf=false` everywhere and use a `.gitattributes` file committed to the repository. This makes the line ending rules explicit and version-controlled rather than relying on each developer's local setting.

Add this to your repository's `.gitattributes`:

```gitattributes
# Normalise all text files to LF in the repository
* text=auto eol=lf

# Shell scripts must have LF
*.sh  text eol=lf
*.bash text eol=lf

# Python and web files
*.py  text eol=lf
*.js  text eol=lf
*.ts  text eol=lf
*.json text eol=lf
*.yml  text eol=lf
*.yaml text eol=lf
*.md   text eol=lf

# Windows-specific files should keep CRLF
*.bat  text eol=crlf
*.cmd  text eol=crlf
*.ps1  text eol=crlf
*.sln  text eol=crlf

# Binary files - no line ending processing
*.png  binary
*.jpg  binary
*.pdf  binary
*.zip  binary
*.exe  binary
*.dll  binary
```

After adding `.gitattributes`, normalise all existing files:

```bash
git add --renormalize .
git commit -m "chore: normalise line endings via .gitattributes"
```

---

## 7. Git credential management

When you push to GitHub, GitLab or any remote over HTTPS, Git needs credentials. Without a credential helper, Git asks for your username and password on every push. With a credential helper, it stores credentials securely and reuses them.

> [!CAUTION]
> GitHub disabled HTTPS password authentication for Git operations in August 2021. You can no longer use your GitHub account password to push via HTTPS. Use a personal access token (PAT) instead, or switch to SSH. A credential helper stores the PAT so you only need to enter it once.

**Git Credential Manager (GCM)** is the modern cross-platform solution. It handles OAuth for GitHub (including two-factor auth), GitLab, Azure DevOps and Bitbucket.

🪟 **Windows** - GCM is bundled with Git for Windows. It should be configured automatically. Verify:

```powershell
git config --global credential.helper
# Should output: manager
```

🍎 **macOS** - GCM is available via Homebrew or Git for Mac includes `osxkeychain`:

```bash
# Option 1: Git Credential Manager (recommended, works with GitHub OAuth)
brew install --cask git-credential-manager
git-credential-manager configure

# Option 2: Built-in macOS Keychain (simpler, stores PATs)
git config --global credential.helper osxkeychain
```

🐧 **Linux** - several options:

```bash
# Git Credential Manager (recommended for GitHub/GitLab OAuth)
# Download the .deb from github.com/git-ecosystem/git-credential-manager/releases
sudo dpkg -i gcm-linux_amd64.deb
git-credential-manager configure

# libsecret (GNOME Keyring / KWallet)
sudo apt install libsecret-1-0 libsecret-1-dev
sudo make --directory=/usr/share/doc/git/contrib/credential/libsecret
git config --global credential.helper /usr/share/doc/git/contrib/credential/libsecret/git-credential-libsecret

# In-memory cache (fallback - asks once then remembers for timeout period)
git config --global credential.helper 'cache --timeout=3600'
```

**SSH as an alternative to HTTPS**: many developers prefer SSH authentication because it never requires entering a password once your key is set up.

```bash
# Generate an SSH key (Ed25519 is the modern standard)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Add to ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519

# Copy the public key to add to GitHub/GitLab
cat ~/.ssh/id_ed25519.pub
```

Then add the public key to your GitHub/GitLab account (Settings → SSH Keys), and clone using the SSH URL: `git@github.com:username/repo.git` instead of the HTTPS URL.

---

## 8. Try It Yourself

**Exercise 1 - trace a git command**

In a terminal, run `git log --oneline -3` in any repository. Then try to explain each step: which shell are you using, where did the shell find `git`, what config files did Git read, where did the output go? Use `which git` and `git config --list --show-origin` to gather information.

**Exercise 2 - change your Git editor and test it**

Set your Git editor to `nano` (the simplest terminal editor for this test):

```bash
git config --global core.editor nano
```

Then in a repository, run `git commit --allow-empty` (commits with no changes for testing). nano should open. Write a test message, press `Ctrl+O` to save and `Ctrl+X` to exit. Verify the commit was created with `git log -1`. Then change your editor back to your preferred one.

**Exercise 3 - check your credential helper**

Run `git config --global credential.helper`. If it returns nothing, you have no credential helper configured - look up the appropriate one for your platform in section 7 and configure it.

**Exercise 4 - verify your IDE uses the same git**

Open a repository in your IDE (VS Code, JetBrains, etc.) and make a small change. Commit it from inside the IDE. Then go to your terminal and run `git log -1` - you should see the commit you just made from the IDE. Check the author name and email in `git log --format="%an <%ae>" -1` - they should match your `user.name` and `user.email` from `~/.gitconfig`.

**Exercise 5 - check line endings in your repository**

If you have a repository, run `git check-attr -a *` to see any `.gitattributes` settings in the current directory. If there is no `.gitattributes`, add one using the template from section 6. Run `git add --renormalize . && git status` to see if any files needed normalisation.

---

## 9. Common Mistakes

Setting `GIT_EDITOR` as a permanent export in `~/.bashrc` and then wondering why it conflicts with your IDE. Environment variables set in shell config affect every program started from that shell. If you set `GIT_EDITOR=vim` in `.bashrc`, every time VS Code's terminal runs `git commit`, vim will open - even if VS Code has its own editor preference. Use `core.editor` in `.gitconfig` instead; the `GIT_EDITOR` variable should only be used for temporary per-session overrides.

Forgetting `--wait` when setting a GUI editor leads to "Aborting commit due to empty commit message" errors. Every GUI editor that works by sending a file to an existing editor process needs `--wait` (or `-w` for Sublime). If git commit exits immediately with an error about an empty message, check your `core.editor` setting.

Using `git clone https://user:token@github.com/...` embeds the token in both shell history and `.git/config`. Never embed tokens in clone URLs. Configure GCM or osxkeychain and enter the token when Git prompts for a password - the helper stores it securely from there.

Assuming `.gitconfig` on one machine automatically applies on another. If you work on multiple machines, your `~/.gitconfig` is local to each machine. Many developers keep their dotfiles (`.gitconfig`, `.zshrc`, `.bashrc` and so on) in a Git repository and install them with a symlink script, which is covered in [06-git-aliases.md](06-git-aliases.md).

On Windows, having Git for Windows and a WSL Git that disagree about configuration. The `.gitconfig` in Windows (`C:\Users\username\.gitconfig`) and the one in WSL (`/home/username/.gitconfig`) are separate files. Configure both, or set up a symlink from WSL to point at the Windows config: `ln -s /mnt/c/Users/username/.gitconfig ~/.gitconfig`.

---

## 10. Summary

Git is an external program that your shell finds and runs by searching PATH. When you type a git command, the shell forks a child process, runs the `git` binary, and waits for it to finish. Git reads its configuration from system, global and local config files in that order, with local overrides winning.

When Git needs you to type text - commit messages, rebase todo lists - it opens the editor defined by `GIT_EDITOR`, `core.editor`, `VISUAL` or `EDITOR`, in that priority order. GUI editors need a `--wait` flag so Git waits for you to close the file rather than reading immediately.

Most IDEs and editors call the same `git` binary you use from the terminal, so your `.gitconfig` settings and `.git/hooks/` scripts apply everywhere. Exceptions: GitHub Desktop and Xcode bundle their own Git. Because the same binary is used everywhere, one well-configured `~/.gitconfig` serves your entire workflow.

Line endings differ between Windows (CRLF) and Unix/Mac (LF). Use a `.gitattributes` file committed to the repository rather than relying on each developer's `core.autocrlf` setting. Use Git Credential Manager, osxkeychain or libsecret to store credentials securely rather than embedding tokens in URLs.

---

## 11. Sources

- [Git documentation - git-config](https://git-scm.com/docs/git-config)
- [Git documentation - gitattributes](https://git-scm.com/docs/gitattributes)
- [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager)
- [GitHub - HTTPS authentication changes](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)
- [GitHub SSH key documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [Configuring Git editors - Baeldung](https://www.baeldung.com/ops/git-editors-select-configure)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

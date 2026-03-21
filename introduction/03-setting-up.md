# ⚙️ Setting Up

> "Well begun is half done." - Aristotle

**Difficulty:** 🟢 Beginner
**Time to complete:** 20-40 minutes depending on your operating system and IDE

This file gets you fully set up before you write a single Git command. By the end you will have Git installed, configured with your identity, connected to GitHub or GitLab and set up inside your code editor.

Do not skip this file. A properly configured Git setup prevents a huge number of the errors beginners run into.

---

## 📋 Table of Contents

- [Step 1 - Check if Git is already installed](#-step-1---check-if-git-is-already-installed)
- [Step 2 - Install Git](#-step-2---install-git)
- [Step 3 - Configure Git for the first time](#-step-3---configure-git-for-the-first-time)
- [Step 4 - Set up authentication with GitHub or GitLab](#-step-4---set-up-authentication-with-github-or-gitlab)
- [Step 5 - Set up Git in your IDE](#-step-5---set-up-git-in-your-ide)
- [Step 6 - Verify everything is working](#-step-6---verify-everything-is-working)
- [Common Setup Mistakes](#-common-setup-mistakes)
- [Summary](#-summary)
- [Sources and Further Reading](#-sources-and-further-reading)

---

## 🔍 Step 1 - Check if Git is Already Installed

Before installing anything, check if Git is already on your machine. Many computers come with Git pre-installed, especially Macs and Linux machines.

Open your terminal or command prompt and run:

🪟 **Windows - Command Prompt or PowerShell:**
```bash
git --version
```

🍎 **Mac - Terminal:**
```bash
git --version
```

🐧 **Linux - Terminal:**
```bash
git --version
```

If Git is installed you will see something like:
```
git version 2.43.0
```

If you see an error like `'git' is not recognized` or `command not found`, Git is not installed. Continue to Step 2.

💡 **Tip:** Even if Git is installed, check the version. If it is older than version 2.28 it is worth updating - some features in this course use newer Git behaviour.

---

## 📥 Step 2 - Install Git

### 🪟 Windows

You have several options on Windows. Git for Windows is the most straightforward.

**Option A - Git for Windows (recommended for beginners):**

1. Go to [git-scm.com/download/win](https://git-scm.com/download/win)
2. The download should start automatically
3. Run the installer
4. During installation, the default options are fine for most people but pay attention to these:
   - **Default editor:** Change this to your preferred editor (VS Code, Notepad++ etc.) instead of Vim if you are not comfortable with Vim
   - **Default branch name:** Select "Override the default branch name for new repositories" and type `main` - this matches GitHub and GitLab's default
   - **PATH environment:** Choose "Git from the command line and also from 3rd-party software" - this makes Git available everywhere
   - **Line ending conversions:** Choose "Checkout Windows-style, commit Unix-style line endings" - this prevents issues when collaborating across operating systems
   - **Terminal emulator:** Choose "Use Windows' default console window" if you want to use PowerShell or CMD, or "Use MinTTY" if you prefer the Git Bash terminal
5. Complete the installation
6. Open a new Command Prompt or PowerShell window and run `git --version` to confirm

**Option B - winget (Windows Package Manager):**
```powershell
winget install --id Git.Git -e --source winget
```

**Option C - Chocolatey:**
```powershell
choco install git
```

**Option D - Scoop:**
```powershell
scoop install git
```

💡 **Tip:** Git for Windows also installs Git Bash - a terminal that lets you use Unix-style commands on Windows. Many developers prefer it for Git work.

⚠️ **After installing on Windows:** Close and reopen any terminal windows so the new PATH is loaded. Git will not be recognised in terminals that were already open.

---

### 🍎 Mac

**Option A - Homebrew (recommended):**

Homebrew is a package manager for Mac. If you do not have it, install it first:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Then install Git:
```bash
brew install git
```

**Option B - Xcode Command Line Tools:**

If you use Xcode or develop for Apple platforms, the Command Line Tools include Git:
```bash
xcode-select --install
```

A dialog will appear asking you to install - click Install. This takes a few minutes.

**Option C - Official installer:**

Download the macOS installer from [git-scm.com/download/mac](https://git-scm.com/download/mac) and follow the prompts.

💡 **Tip:** Homebrew is strongly recommended for Mac developers. It makes installing and updating developer tools much easier. Once you have it, you can install almost any developer tool with a single command.

⚠️ **Apple Silicon (M1/M2/M3/M4 Macs):** Homebrew installs to `/opt/homebrew/` on Apple Silicon rather than `/usr/local/`. If you get a "command not found" error after installing, add Homebrew to your PATH:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

---

### 🐧 Linux

Use your distribution's package manager:

**Ubuntu / Debian / Linux Mint:**
```bash
sudo apt update
sudo apt install git
```

**Fedora:**
```bash
sudo dnf install git
```

**Arch Linux / Manjaro:**
```bash
sudo pacman -S git
```

**openSUSE:**
```bash
sudo zypper install git
```

**CentOS / RHEL / AlmaLinux:**
```bash
sudo yum install git
```

💡 **Tip:** The version of Git in your package manager may be slightly behind the latest release. For most users this does not matter. If you need the very latest version, you can build from source or use a PPA (Ubuntu) or COPR (Fedora).

---

## ⚙️ Step 3 - Configure Git for the First Time

This is the step most tutorials rush through. Do not skip it. Git uses this configuration to identify who made each commit - it appears in your project history, on GitHub and on GitLab.

### Set your name and email

These must match the name and email on your GitHub or GitLab account for your commits to be linked to your profile.

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Replace `Your Name` and `your.email@example.com` with your actual name and email.

The `--global` flag means this applies to all repositories on your computer. You can override it for a specific repository by running the same command without `--global` inside that repository.

### Set your default branch name

GitHub and GitLab both default to `main` as the primary branch name. Set Git to match:

```bash
git config --global init.defaultBranch main
```

⚠️ **Why this matters:** Older versions of Git default to `master`. If yours does not match GitHub or GitLab you will get confusing errors when pushing for the first time.

### Set your default editor

When Git needs you to write a message (such as for a merge commit), it opens a text editor. Set this to an editor you are comfortable with:

**VS Code:**
```bash
git config --global core.editor "code --wait"
```

**Notepad++ (Windows):**
```bash
git config --global core.editor "'C:/Program Files/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"
```

**Nano (good default for terminal users):**
```bash
git config --global core.editor "nano"
```

**Vim (if you know Vim):**
```bash
git config --global core.editor "vim"
```

**JetBrains IDEs** handle commit messages inside the IDE itself so the terminal editor matters less, but setting nano or VS Code is still a good fallback.

💡 **Tip:** If you have never used Vim and Git opens it unexpectedly, type `:q!` and press Enter to exit without saving. Then set a different editor using the commands above.

### Set line ending behaviour

Line endings differ between operating systems. This setting prevents those differences from causing problems when collaborating.

🪟 **Windows:**
```bash
git config --global core.autocrlf true
```

🍎 **Mac / 🐧 Linux:**
```bash
git config --global core.autocrlf input
```

### Set a default pull behaviour

This prevents a common warning Git shows when you pull:

```bash
git config --global pull.rebase false
```

This tells Git to use merge (not rebase) when pulling. You can change this later once you understand the difference between the two.

### Verify your configuration

Check everything looks correct:

```bash
git config --global --list
```

You should see your name, email, default branch, editor and other settings listed.

Your global configuration is stored in a file called `.gitconfig` in your home directory. You can open and edit it directly if needed:

🪟 **Windows:**
```
C:\Users\YourUsername\.gitconfig
```

🍎 **Mac / 🐧 Linux:**
```
~/.gitconfig
```

---

## 🔐 Step 4 - Set up Authentication with GitHub or GitLab

To push code to GitHub or GitLab you need to prove who you are. There are two ways to do this: SSH keys or HTTPS with a Personal Access Token. SSH is more secure and more convenient once set up.

### Option A - SSH Keys (recommended)

SSH keys work like a lock and key. You generate a key pair - a private key that stays on your computer and a public key that you give to GitHub or GitLab. When you connect, they match without you needing to type a password.

**Step 1 - Generate an SSH key:**

🪟 **Windows (Git Bash or PowerShell) / 🍎 Mac / 🐧 Linux:**
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

Replace the email with the one on your GitHub or GitLab account.

When asked where to save the key, press Enter to accept the default location (`~/.ssh/id_ed25519`).

When asked for a passphrase, either press Enter for no passphrase or enter one for extra security. A passphrase is recommended.

**Step 2 - Start the SSH agent:**

🪟 **Windows (PowerShell - run as Administrator):**
```powershell
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

🪟 **Windows (Git Bash):**
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

🍎 **Mac:**
```bash
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

🐧 **Linux:**
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

**Step 3 - Copy your public key:**

🪟 **Windows (PowerShell):**
```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | clip
```

🪟 **Windows (Git Bash):**
```bash
cat ~/.ssh/id_ed25519.pub
```
Select the output and copy it manually.

🍎 **Mac:**
```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

🐧 **Linux:**
```bash
cat ~/.ssh/id_ed25519.pub
```
Select the output and copy it manually, or install `xclip`:
```bash
xclip -selection clipboard < ~/.ssh/id_ed25519.pub
```

**Step 4 - Add the key to GitHub:**

1. Go to [github.com/settings/keys](https://github.com/settings/keys)
2. Click **New SSH key**
3. Give it a title (e.g. "My Laptop")
4. Paste your public key
5. Click **Add SSH key**

**Add the key to GitLab:**

1. Go to [gitlab.com/-/profile/keys](https://gitlab.com/-/profile/keys)
2. Paste your public key
3. Give it a title
4. Click **Add key**

**Step 5 - Test the connection:**

GitHub:
```bash
ssh -T git@github.com
```

GitLab:
```bash
ssh -T git@gitlab.com
```

You should see a success message with your username. If you set a passphrase you will be asked for it.

---

### Option B - HTTPS with a Personal Access Token

If you prefer HTTPS over SSH, you need a Personal Access Token (PAT) instead of your password. GitHub removed password authentication in August 2021 and GitLab followed a similar path.

**GitHub:**

1. Go to [github.com/settings/tokens](https://github.com/settings/tokens)
2. Click **Generate new token (classic)**
3. Give it a name (e.g. "My Laptop")
4. Set an expiry date
5. Select the `repo` scope at minimum
6. Click **Generate token**
7. Copy the token immediately - you will not see it again

⚠️ **Store your token safely.** Use a password manager. Never paste it into a file in your repository.

When Git asks for your password, use the token instead.

To avoid entering it every time, set up a credential helper:

🪟 **Windows:**
```bash
git config --global credential.helper wincred
```

🍎 **Mac:**
```bash
git config --global credential.helper osxkeychain
```

🐧 **Linux:**
```bash
git config --global credential.helper store
```

⚠️ **Linux note:** `credential.helper store` saves your token in plain text at `~/.git-credentials`. It is convenient but less secure. Consider using `libsecret` if available on your distribution.

---

## 🖥️ Step 5 - Set up Git in Your IDE

### VS Code

VS Code has Git support built in - no extensions needed to get started.

1. Open VS Code
2. Open any folder that is a Git repository (or run `git init` in a folder first)
3. Click the **Source Control** icon in the left sidebar (it looks like a branch with dots - or press `Ctrl + Shift + G` on Windows/Linux, `Cmd + Shift + G` on Mac)
4. VS Code will automatically detect Git and show your changes

**Recommended extensions:**
- **GitLens** - shows who wrote each line, commit history, blame annotations and much more. One of the most popular VS Code extensions
- **Git Graph** - visual branch graph inside VS Code
- **Git History** - view file history and compare commits
- **GitHub Pull Requests** - manage pull requests without leaving VS Code

To install an extension: press `Ctrl + Shift + X` (Windows/Linux) or `Cmd + Shift + X` (Mac), search the extension name and click Install.

**Set VS Code as your Git editor (if not done in Step 3):**
```bash
git config --global core.editor "code --wait"
```

---

### JetBrains IDEs (IntelliJ IDEA, PyCharm, WebStorm, CLion, Rider, GoLand, RubyMine, PhpStorm)

All JetBrains IDEs share the same Git integration - once you know it in one, you know it in all of them.

**Setting up Git in a JetBrains IDE:**

1. Open your IDE
2. Go to **File → Settings** (Windows/Linux) or **IDE Name → Preferences** (Mac)
3. Navigate to **Version Control → Git**
4. The IDE will usually auto-detect Git. If not, click the folder icon next to the Path to Git executable field and locate Git:
   - 🪟 **Windows:** Usually `C:\Program Files\Git\bin\git.exe`
   - 🍎 **Mac:** Usually `/usr/bin/git` or `/opt/homebrew/bin/git`
   - 🐧 **Linux:** Usually `/usr/bin/git`
5. Click **Test** to verify
6. Click **OK**

**Connecting to GitHub or GitLab:**
1. Go to **File → Settings → Version Control → GitHub** (or GitLab)
2. Click the `+` button
3. Log in with your account or paste a token

**Using Git in JetBrains:**
- The **Git** menu at the top gives access to all operations
- The **Commit** tool window (usually bottom left) shows your changes
- `Alt + 9` (Windows/Linux) or `Cmd + 9` (Mac) opens the Git tool window with log, branches and remotes
- Right-click any file for Git options

💡 **Tip:** JetBrains IDEs have one of the best merge conflict editors available. The three-panel view (your version, base version, incoming version) makes resolving conflicts much more visual and intuitive than the terminal.

---

### Visual Studio (Windows and Mac)

Visual Studio (not VS Code - these are different products) has deep Git integration built in.

**Windows:**
1. Open Visual Studio
2. Go to **View → Git Changes** (or press `Ctrl + 0, Ctrl + G`)
3. If working on an existing repository, Visual Studio detects it automatically
4. To clone a repository: **File → Clone Repository** or use the start screen

**Connecting to GitHub:**
1. Go to **File → Account Settings**
2. Click **Add** under GitHub
3. Sign in with your GitHub account

**Mac (Visual Studio for Mac - now discontinued but still used):**
Git is integrated similarly. Go to **View → Pads → Version Control**.

💡 **Tip:** Visual Studio's Team Explorer and Git Changes panels handle most everyday Git operations well. For advanced operations you may still want to use the terminal alongside it.

---

### Xcode (Mac only)

Xcode is Apple's IDE for building iOS, macOS, watchOS and tvOS apps. It has Git integration built in.

**Setting up:**
1. Open Xcode
2. When creating a new project, check **Create Git repository on my Mac** to initialise Git automatically
3. For existing projects: **Source Control → Create Git Repositories**

**Connecting to GitHub:**
1. Open **Xcode → Settings → Accounts**
2. Click the `+` button
3. Choose **GitHub** and sign in

**Everyday use:**
- **Source Control Navigator** (left sidebar, cloud icon) shows commits, branches and remotes
- **Source Control → Commit** to stage and commit changes
- Inline change indicators appear in the code editor next to modified lines

---

### Neovim and Vim

If you use Neovim or Vim, you likely prefer doing most things in the terminal. Git works perfectly from the terminal alongside any editor. However, several plugins bring Git integration inside the editor:

**Popular Git plugins for Neovim:**
- **Fugitive** (`tpope/vim-fugitive`) - the most popular Git plugin for Vim/Neovim. Run Git commands directly from inside Vim with `:Git`
- **Gitsigns** (`lewis6991/gitsigns.nvim`) - shows added, changed and removed lines in the sign column
- **Neogit** (`NeogitOrg/neogit`) - a Neovim-native Git interface inspired by Magit (Emacs)
- **LazyGit** - a terminal UI for Git that works alongside any editor including Neovim

Install plugins using your plugin manager (Lazy.nvim, Packer, vim-plug etc.).

---

### Sublime Text

Sublime Text does not have built-in Git support but the **GitSavvy** package adds comprehensive Git integration.

1. Open the Command Palette: `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac)
2. Type `Package Control: Install Package`
3. Search for `GitSavvy` and install it
4. Use `Ctrl + Shift + P` and type `GitSavvy` to access all Git commands

---

### Other Editors

If your editor is not listed here, check its documentation for Git integration. Most modern editors either have built-in Git support or a plugin available. The underlying Git commands are the same regardless of editor - the IDE just provides a visual interface for them.

For any editor, Git always works perfectly from the terminal running alongside it.

---

## ✅ Step 6 - Verify Everything is Working

Run through this checklist to confirm your setup is complete:

**Git installation:**
```bash
git --version
```
Expected: `git version 2.x.x` (any recent version)

**Your identity:**
```bash
git config --global user.name
git config --global user.email
```
Expected: Your name and email

**Default branch:**
```bash
git config --global init.defaultBranch
```
Expected: `main`

**SSH connection to GitHub:**
```bash
ssh -T git@github.com
```
Expected: `Hi YOUR_USERNAME! You've successfully authenticated.`

**SSH connection to GitLab:**
```bash
ssh -T git@gitlab.com
```
Expected: `Welcome to GitLab, @YOUR_USERNAME!`

**Create a test repository:**
```bash
mkdir git-test
cd git-test
git init
echo "hello git" > README.md
git add .
git commit -m "initial commit"
git log
```
Expected: A commit log entry showing your name, email and message

If all of these work, you are fully set up. 🎉

---

## ❌ Common Setup Mistakes

**My commits show the wrong name or email**

You either skipped Step 3 or used different details. Fix it:
```bash
git config --global user.name "Correct Name"
git config --global user.email "correct@email.com"
```
Note: This only affects future commits. To fix past commits see [git/14-reset-and-revert.md](../git/14-reset-and-revert.md).

**`git push` asks for a password and then fails**

GitHub no longer accepts passwords. You need SSH keys (Step 4, Option A) or a Personal Access Token (Step 4, Option B).

**`ssh -T git@github.com` gives "Permission denied"**

Your public key was not added to GitHub correctly, or the SSH agent is not running. Go back through Step 4 and make sure:
- You copied the public key (ending in `.pub`), not the private key
- The SSH agent is running and your key is added to it
- The key appears in your GitHub settings at github.com/settings/keys

**Git opens Vim and I do not know how to exit**

Type `:q!` and press Enter. Then set a different editor:
```bash
git config --global core.editor "nano"
```

**VS Code says "Git not found"**

Git was installed after VS Code was opened. Restart VS Code completely.

**JetBrains IDE shows "Git executable not found"**

Go to **File → Settings → Version Control → Git** and manually set the path to your Git executable.

**On Mac, `brew` command not found after installing Homebrew**

Your shell profile was not updated. Run:
```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
source ~/.zprofile
```

**Windows: Git not recognised after installation**

You need to open a new terminal window. The PATH is only updated for new terminal sessions.

---

## 📖 Summary

By now you should have:

- ✅ Git installed and up to date
- ✅ Your name and email configured
- ✅ Default branch set to `main`
- ✅ Your preferred editor set
- ✅ Line ending behaviour configured
- ✅ SSH keys set up for GitHub and/or GitLab
- ✅ Git working inside your IDE
- ✅ A successful test commit

You are ready to start learning Git properly.

---

## ➡️ Next Step

Head to [What is Version Control?](../git/01-what-is-version-control.md) to begin the course. →

---

## 🔗 Sources and Further Reading

- [Official Git download page](https://git-scm.com/downloads)
- [Git first-time setup documentation](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup)
- [GitHub SSH key documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
- [GitLab SSH key documentation](https://docs.gitlab.com/ee/user/ssh.html)
- [GitHub Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
- [GitLab Personal Access Tokens](https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html)
- [Homebrew](https://brew.sh) - package manager for Mac
- [Git for Windows](https://gitforwindows.org)
- [GitLens extension for VS Code](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
- [Fugitive - Git plugin for Vim/Neovim](https://github.com/tpope/vim-fugitive)
- [JetBrains Git integration documentation](https://www.jetbrains.com/help/idea/using-git-integration.html)
- [Xcode Source Control documentation](https://developer.apple.com/documentation/xcode/source-control-management)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

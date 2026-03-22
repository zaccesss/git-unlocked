# Setting Up

**Difficulty:** 🟢 Beginner | **Time:** 30–50 minutes

This file gets you fully set up before you write a single Git command. By the end you will have a GitHub account, a GitLab account, Git installed and configured, authentication working and Git set up inside your code editor or IDE.

Do not skip this file. A properly configured setup prevents the majority of errors beginners run into.

---

## Table of Contents

- [What is a terminal?](#what-is-a-terminal)
- [What is an IDE?](#what-is-an-ide)
- [Step 1 - Check if Git is already installed](#step-1--check-if-git-is-already-installed)
- [Step 2 - Create your GitHub account](#step-2--create-your-github-account)
- [Step 3 - Create your GitLab account](#step-3--create-your-gitlab-account)
- [Step 4 - Install Git](#step-4--install-git)
- [Step 5 - Configure Git for the first time](#step-5--configure-git-for-the-first-time)
- [Step 6 - Set up authentication](#step-6--set-up-authentication)
- [Step 7 - Set up Git in your IDE](#step-7--set-up-git-in-your-ide)
- [Step 8 - Quick test to verify everything works](#step-8--quick-test-to-verify-everything-works)
- [Common setup mistakes](#common-setup-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Terminal?

A **terminal** (also called command line, command prompt, shell or console) is a text-based window where you type commands one line at a time and press Enter to run them. It is how you talk directly to your computer.

You do not need to be a hacker to use a terminal. You just need to type the right words.

**How to open a terminal:**

**Windows** - Press the Windows key, type `cmd` and press Enter to open Command Prompt. Or type `powershell` for PowerShell (recommended). Or type `Windows Terminal` if you have it installed (the best option in 2026).

**Mac** - Press `Cmd + Space`, type `Terminal` and press Enter. Or go to **Applications → Utilities → Terminal**.

**Linux** - Press `Ctrl + Alt + T` on most distributions. Or right-click the desktop and choose "Open Terminal".

> [!WARNING]
> Type commands **one at a time**. Press Enter after each one and read the output before typing the next. Do not paste multiple commands at once until you understand what each one does.

---

## What is an IDE?

An **IDE (Integrated Development Environment)** is an all-in-one application for writing and managing code. Think of it as a super word processor for code that combines:

- A **code editor** with syntax highlighting and auto-completion
- A **file explorer** to navigate your project
- A **terminal** so you can run commands without switching windows
- **Debugger** tools to find and fix errors
- **Git integration** so you can manage version control visually

**Popular IDEs in 2026:**

| IDE                    | Best for                         | Price                              |
| ---------------------- | -------------------------------- | ---------------------------------- |
| **VS Code**            | Everything - most popular by far | Free                               |
| **IntelliJ IDEA**      | Java and Kotlin                  | Free (Community) / Paid (Ultimate) |
| **PyCharm**            | Python                           | Free (Community) / Paid (Pro)      |
| **WebStorm**           | JavaScript and TypeScript        | Paid                               |
| **Visual Studio 2026** | C# and .NET on Windows           | Free (Community) / Paid            |
| **Xcode 26**           | iOS, macOS and Apple platforms   | Free                               |
| **CLion**              | C and C++                        | Paid                               |
| **Rider**              | .NET and Unreal Engine           | Paid                               |

> [!TIP]
> If you are new to development, start with **VS Code**. It is free, works on Windows, Mac and Linux, and is used by over 75% of developers worldwide.

A text editor like Notepad only edits text with no understanding of code. A terminal is a command-line interface with no graphical editor. An IDE combines all three.

---

## Step 1 - Check if Git is Already Installed

Before installing anything, check if Git is already on your machine. Many computers come with Git pre-installed.

Open your terminal and type the following command, then press Enter:

**Windows (Command Prompt or PowerShell), Mac (Terminal), Linux (Terminal):**

```bash
git --version
```

If Git is installed you will see something like:

```
git version 2.53.0
```

If you see `'git' is not recognized` (Windows) or `command not found` (Mac/Linux), Git is not installed. Continue to Step 4 after completing Steps 2 and 3.

> [!NOTE]
> The current stable release is **Git 2.53.0** (February 2026). If your version is older than 2.28, update it - some features in this course require newer Git behaviour. Git 3.0 is expected later in 2026 and will bring significant changes including defaulting to `main` as the branch name.

---

## Step 2 - Create Your GitHub Account

> [!IMPORTANT]
> Create your account _before_ configuring Git. The email you sign up with must match the email you set in Git config so your commits link to your profile correctly.

**What is GitHub?** GitHub is the world's largest platform for hosting Git repositories. It is where you will store your code online, collaborate with others and build your public portfolio. It has over 180 million developers. You will learn everything about it in the `github/` folder - for now you just need an account.

### Creating your account

1. Go to **https://github.com** in your browser
2. Click **Sign up**
3. Enter your **email address** - use one you check regularly and will keep long term
4. Create a **strong, unique password** - use a password manager if you have one
5. Choose a **username**

> [!IMPORTANT]
> **Choosing your GitHub username matters.** It appears on every commit you make and on your public profile. Recruiters and employers will see it. Keep it professional, lowercase, short and memorable - ideally your real name or a variation. Avoid numbers at the end if possible. You _can_ change it later but it breaks existing links, so choose carefully.

6. Solve the verification puzzle
7. Click **Create account**
8. Open the verification email GitHub sends you, copy the code and paste it in
9. Skip or answer the personalisation questions

### After creating your account

**Verify your email.** Some GitHub features are locked until you do this. If the email did not arrive, check spam or go to **Settings → Emails → Resend verification**.

**Enable two-factor authentication (2FA).** Go to **Settings → Password and security → Two-factor authentication**. Use an authenticator app (Google Authenticator, Authy or 1Password) rather than SMS - SMS can be intercepted. This protects your account from being hijacked.

**Add a profile photo.** Go to **Settings → Public profile**. A real photo or professional avatar makes your profile look more trustworthy to collaborators and recruiters.

**Fill in your bio.** Add your name, location and what you are working on. It takes two minutes and makes a real difference.

> [!CAUTION]
> Never share your GitHub password. If you think your account has been compromised, go to **Settings → Password and security** immediately - change your password, regenerate your recovery codes and revoke any suspicious OAuth applications.

> [!TIP]
> **Students:** Go to **https://education.github.com** and apply for the GitHub Student Developer Pack. It gives you GitHub Copilot Pro, JetBrains IDEs, domain names, cloud credits and over 100 other tools completely free. All you need is a school or university email address. It is worth thousands of pounds or dollars and approval typically takes 1–3 days.

---

## Step 3 - Create Your GitLab Account

GitLab is a separate platform from GitHub. Many companies - especially in enterprise, government and regulated industries - use GitLab instead of or alongside GitHub. Having an account on both is worthwhile.

**What is GitLab?** GitLab is a complete DevSecOps platform that includes Git hosting, built-in CI/CD pipelines, security scanning and more - all in one place. The current version is GitLab 18.10 (March 2026). Everything about it is covered in the `gitlab/` folder.

### Creating your account

1. Go to **https://gitlab.com** in your browser
2. Click **Register now** or **Sign up**
3. You can sign up with your **GitHub account** by clicking "Continue with GitHub" - this is convenient and saves creating another password. Or sign up with an email address if you prefer to keep them separate
4. If signing up with email: enter your first name, last name, username and email address

> [!TIP]
> Use the same username as GitHub if it is available. Keeping them consistent makes life easier - people can find you on both platforms.

5. Create a password (if signing up with email)
6. Click **Register**
7. Open the confirmation email GitLab sends and click the confirmation link
8. Skip or answer the onboarding questions

### After creating your account

**Verify your email.** Required before you can push code to GitLab.

**Enable two-factor authentication.** Go to **User settings → Account → Two-factor authentication**. Same recommendation as GitHub - use an authenticator app.

**Add a profile photo and bio.** Go to **User settings → Profile**.

> [!CAUTION]
> Use a different password for GitLab than for GitHub. If one account is compromised, the other stays safe. Store both in a password manager.

> [!NOTE]
> **GitLab vs GitHub - which should you use?** You do not have to choose. Most developers use both. GitHub is better for open source and broad collaboration. GitLab is better for private projects, enterprise use and when you need powerful built-in CI/CD pipelines. A full comparison is in [gitlab/02-gitlab-vs-github.md](../gitlab/02-gitlab-vs-github.md).

---

## Step 4 - Install Git

If Step 1 confirmed Git is already installed at version 2.28 or higher, skip to Step 5.

### Windows

**Option A - Git for Windows (recommended for beginners):**

1. Go to **https://git-scm.com/install/windows** in your browser
2. The download starts automatically. If not, click the 64-bit installer link
3. Run the downloaded `.exe` file
4. Click through the installer, paying attention to these screens:
   - **Default editor** - change from Vim to VS Code ("Use Visual Studio Code as Git's default editor") or Nano if you do not have VS Code. Vim is very difficult for beginners
   - **Initial branch name** - select "Override the default branch name" and type `main`
   - **PATH environment** - choose "Git from the command line and also from 3rd-party software"
   - **Line ending conversions** - choose "Checkout Windows-style, commit Unix-style line endings"
5. Click **Install**, then **Finish**
6. **Close and reopen any terminal windows** - Git will not be recognised in terminals that were already open

**Option B - winget:**

Open PowerShell and run:

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

> [!NOTE]
> Git for Windows also installs **Git Bash** - a terminal that lets you use Unix-style commands on Windows. Many developers prefer it for Git work.

> [!WARNING]
> Always close and reopen your terminal after installing Git on Windows. The PATH update only applies to new terminal sessions.

---

### Mac

**Option A - Homebrew (recommended):**

If you do not have Homebrew, install it first. Open Terminal and run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the on-screen prompts. It will ask for your Mac password.

> [!WARNING]
> **Apple Silicon Macs (M1, M2, M3, M4 or M5 chip):** After Homebrew installs, you must run these two commands or Homebrew will not work:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Then install Git:

```bash
brew install git
```

**Option B - Xcode Command Line Tools:**

```bash
xcode-select --install
```

A dialog appears - click **Install**. This takes a few minutes.

**Option C - Official installer:**

Go to **https://git-scm.com/install/mac** and download the installer.

> [!TIP]
> To check which chip your Mac has, click the Apple logo (top left) → About This Mac. "Apple M1/M2/M3/M4/M5" means Apple Silicon. "Intel" means Intel Mac.

---

### Linux

Use your distribution's package manager. Type the relevant command, press Enter, and enter your password when prompted.

**Ubuntu, Debian or Linux Mint:**

```bash
sudo apt update
```

```bash
sudo apt install git
```

**Fedora:**

```bash
sudo dnf install git
```

**Arch Linux or Manjaro:**

```bash
sudo pacman -S git
```

**openSUSE:**

```bash
sudo zypper install git
```

**CentOS, RHEL or AlmaLinux:**

```bash
sudo yum install git
```

**Alpine Linux:**

```bash
apk add git
```

> [!TIP]
> **Ubuntu - get the latest version:** Add the official PPA for the most recent stable Git release:

```bash
sudo add-apt-repository ppa:git-core/ppa
```

```bash
sudo apt update
```

```bash
sudo apt install git
```

---

## Step 5 - Configure Git for the First Time

> [!WARNING]
> This step is essential. Without it Git cannot create commits. Use the **same email address you used for GitHub and GitLab** - this is how your commits link to your profile and contribution graph.

Run each command one at a time. Replace the example values with your own.

**Set your name:**

```bash
git config --global user.name "Your Name"
```

**Set your email:**

```bash
git config --global user.email "your.email@example.com"
```

**Set your default branch name:**

```bash
git config --global init.defaultBranch main
```

This matches GitHub and GitLab's default. Without it, older Git versions use `master`, which causes errors when pushing.

**Set your pull behaviour:**

```bash
git config --global pull.rebase false
```

This uses merge (not rebase) when pulling - simpler for beginners. You can change it later.

**Set line ending behaviour:**

**Windows only:**

```bash
git config --global core.autocrlf true
```

**Mac only:**

```bash
git config --global core.autocrlf input
```

**Linux only:**

```bash
git config --global core.autocrlf input
```

**Set your default editor (optional but recommended):**

**VS Code:**

```bash
git config --global core.editor "code --wait"
```

**Nano (simpler terminal editor):**

```bash
git config --global core.editor "nano"
```

> [!TIP]
> If Git ever opens Vim unexpectedly and you do not know how to exit, type `:q!` and press Enter to quit without saving. Then run the editor config command above.

**Verify your configuration:**

```bash
git config --global --list
```

You should see your name, email, default branch and other settings listed.

---

## Step 6 - Set up Authentication

To push code to GitHub or GitLab you need to prove who you are. There are three options. **Option A is the easiest for beginners.**

---

### Option A - GitHub CLI (easiest)

The GitHub CLI handles authentication automatically through your browser.

**Install:**

**Windows:**

```powershell
winget install --id GitHub.cli
```

**Mac:**

```bash
brew install gh
```

**Linux (Ubuntu/Debian):**

```bash
sudo apt install gh
```

**Authenticate:**

```bash
gh auth login
```

Follow the prompts - choose `GitHub.com`, then `HTTPS` or `SSH`, then `Login with a web browser`. Copy the one-time code, press Enter, and authorise in your browser.

That is it. GitHub CLI stores your credentials securely. You will never need to type a password for Git operations again.

---

### Option B - SSH Keys (recommended for regular use)

SSH keys work like a lock and key. A private key stays on your computer. A public key goes to GitHub and GitLab. They match without you typing a password.

**Step 1 - Generate a key:**

**Windows (Git Bash or PowerShell), Mac (Terminal), Linux (Terminal):**

```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```

Press **Enter** to accept the default save location. Add a passphrase for extra security (recommended).

**Step 2 - Start the SSH agent and add your key:**

**Windows (Git Bash):**

```bash
eval "$(ssh-agent -s)"
```

```bash
ssh-add ~/.ssh/id_ed25519
```

**Windows (PowerShell - run as Administrator):**

```powershell
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
```

```powershell
Start-Service ssh-agent
```

```powershell
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

**Mac:**

```bash
eval "$(ssh-agent -s)"
```

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

**Linux:**

```bash
eval "$(ssh-agent -s)"
```

```bash
ssh-add ~/.ssh/id_ed25519
```

**Step 3 - Copy your public key:**

**Windows (PowerShell):**

```powershell
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | clip
```

**Mac:**

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

**Linux:**

```bash
cat ~/.ssh/id_ed25519.pub
```

Select all the output and copy it manually.

**Step 4 - Add to GitHub:**

1. Go to **https://github.com/settings/keys**
2. Click **New SSH key**
3. Give it a title (e.g. `My Laptop`)
4. Choose **Authentication Key**
5. Paste your public key
6. Click **Add SSH key**

**Add to GitLab:**

1. Go to **https://gitlab.com/-/user_settings/ssh_keys**
2. Paste your public key
3. Give it a title
4. Click **Add key**

**Step 5 - Test the connections:**

```bash
ssh -T git@github.com
```

Expected: `Hi YOUR_USERNAME! You've successfully authenticated...`

```bash
ssh -T git@gitlab.com
```

Expected: `Welcome to GitLab, @your_username!`

---

### Option C - Personal Access Token (HTTPS alternative)

GitHub removed password authentication for Git operations in August 2021. If you prefer HTTPS, you need a Personal Access Token.

**Create a fine-grained token on GitHub:**

1. Go to **https://github.com/settings/tokens**
2. Click **Generate new token → Fine-grained token**
3. Give it a name and set an expiry date (90 days is a good balance)
4. Under Repository access, choose **All repositories**
5. Under Permissions → Contents → choose **Read and write**
6. Click **Generate token** and **copy it immediately** - you will not see it again

**Create a token on GitLab:**

1. Go to **https://gitlab.com/-/user_settings/personal_access_tokens**
2. Give it a name, set an expiry date and tick **read_repository** and **write_repository**
3. Click **Create personal access token** and copy it immediately

> [!CAUTION]
> Treat tokens like passwords. Store them in a password manager. Never commit them to a repository. Never share them.

**Set up a credential helper:**

**Windows:**

```bash
git config --global credential.helper manager
```

**Mac:**

```bash
git config --global credential.helper osxkeychain
```

**Linux:**

```bash
git config --global credential.helper libsecret
```

The next time you push, Git will ask for your username and token then save them securely.

---

## Step 7 - Set up Git in Your IDE

### VS Code

VS Code has full Git support built in.

1. Open VS Code
2. Press `Ctrl + Shift + G` (Windows/Linux) or `Cmd + Shift + G` (Mac) to open the **Source Control** panel
3. VS Code detects Git automatically

**Recommended extensions** (install via `Ctrl + Shift + X` / `Cmd + Shift + X`):

- **GitLens** by GitKraken - 46 million+ downloads. Shows who wrote each line, full commit history and visual branch graph. Free tier is generous
- **GitHub Pull Requests** by GitHub - manage pull requests without leaving VS Code

> [!NOTE]
> The Git Graph extension (13 million+ downloads) still works but is no longer actively maintained. VS Code's built-in **Source Control Graph** (added in version 1.93) provides visual branch history natively and is the recommended replacement.

---

### JetBrains IDEs

All JetBrains IDEs share the same Git integration.

1. Open Settings with `Ctrl + Alt + S` (Windows/Linux) or `Cmd + ,` (Mac)
2. Go to **Version Control → Git**
3. The IDE usually detects Git automatically. If not, set the path:
   - Windows: `C:\Program Files\Git\bin\git.exe`
   - Mac: `/opt/homebrew/bin/git` or `/usr/bin/git`
   - Linux: `/usr/bin/git`
4. Click **Test**, then **OK**

**Connect GitHub:** Settings → Version Control → **GitHub** → click `+` → **Log In via GitHub**

**Connect GitLab:** Settings → Version Control → **GitLab** → click `+` → enter `https://gitlab.com` → paste a PAT with `api` and `read_user` scopes

**Key shortcuts:**

| Action         | Windows/Linux      | Mac               |
| -------------- | ------------------ | ----------------- |
| Commit         | `Ctrl + K`         | `Cmd + K`         |
| Push           | `Ctrl + Shift + K` | `Cmd + Shift + K` |
| Pull           | `Ctrl + T`         | `Cmd + T`         |
| Git log        | `Alt + 9`          | `Cmd + 9`         |
| VCS operations | `` Alt + ` ``      | `Ctrl + V`        |

---

### Visual Studio 2026

1. Open Visual Studio 2026
2. Go to **View → Git Changes**
3. Visual Studio detects your repository automatically

**Connect GitHub:** Git → GitHub → Add Account → sign in via browser

---

### Xcode 26

1. When creating a new project, check **Create Git repository on my Mac**
2. For existing projects: **Integrate → Create Git Repositories**
3. Connect GitHub/GitLab: **Xcode → Settings → Accounts → +**

The **Source Control Navigator** (`Cmd + 2`) shows branches, commits and remotes.

---

### Neovim and Vim

Recommended plugins:

- **vim-fugitive** - the gold standard. Run any Git command with `:Git` or `:G`
- **gitsigns.nvim** - shows added, changed and deleted lines in the margin
- **Neogit** - a full interactive Git interface
- **LazyGit** - terminal UI in a floating window (`brew install lazygit` on Mac, `winget install lazygit` on Windows)

---

### Sublime Text

1. Press `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac)
2. Type `Package Control: Install Package` and press Enter
3. Search for `GitSavvy` and install it

Access all Git commands via the Command Palette - type `git:`.

---

## Step 8 - Quick Test to Verify Everything Works

Run these commands one at a time to confirm your setup is complete.

**Git is installed:**

```bash
git --version
```

Expected: `git version 2.53.0` (or similar)

**Your name is set:**

```bash
git config --global user.name
```

Expected: your name

**Your email is set:**

```bash
git config --global user.email
```

Expected: your email address

**Default branch is set:**

```bash
git config --global init.defaultBranch
```

Expected: `main`

**SSH connection to GitHub (if you used Option B):**

```bash
ssh -T git@github.com
```

Expected: `Hi YOUR_USERNAME! You've successfully authenticated...`

**SSH connection to GitLab (if you used Option B):**

```bash
ssh -T git@gitlab.com
```

Expected: `Welcome to GitLab, @your_username!`

**Create a test repository and commit:**

**Windows (PowerShell):**

```powershell
mkdir git-test
cd git-test
git init
echo "hello git" > README.md
git add .
git commit -m "initial commit"
git log --oneline
```

**Mac and Linux:**

```bash
mkdir git-test && cd git-test
git init
echo "hello git" > README.md
git add .
git commit -m "initial commit"
git log --oneline
```

Expected from `git log --oneline`: one line showing a short hash and `initial commit`.

**Clean up:**

**Windows:**

```powershell
cd ..
rmdir /s /q git-test
```

**Mac and Linux:**

```bash
cd .. && rm -rf git-test
```

If all tests pass, you are fully set up.

---

## Common Setup Mistakes

**`'git' is not recognized` on Windows after installation**

Close and reopen your terminal. The PATH update only applies to new terminal sessions. If it still fails, reinstall using `winget install --id Git.Git -e --source winget`.

**`git push` asks for a password then fails**

GitHub removed password authentication in August 2021. Use SSH keys (Step 6, Option B) or a Personal Access Token (Step 6, Option C).

**`Permission denied (publickey)` with SSH**

Check that your key files exist (`ls ~/.ssh/` - look for `id_ed25519` and `id_ed25519.pub`), the SSH agent is running (`eval "$(ssh-agent -s)"` then `ssh-add ~/.ssh/id_ed25519`), and your public key is added at https://github.com/settings/keys.

**My commits do not appear on my GitHub contribution graph**

The email in your Git config does not match your GitHub account email. Fix it:

```bash
git config --global user.email "the.same.email@as.github.com"
```

**`xcrun: error: invalid active developer path` on Mac after an OS update**

Runs after nearly every major macOS update. Fix:

```bash
xcode-select --install
```

**`zsh: command not found: brew` on Apple Silicon Mac**

Homebrew is not in your PATH. Fix:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

```bash
source ~/.zprofile
```

**`Author identity unknown` when committing**

Run the name and email config commands in Step 5.

**Git opens Vim and I cannot exit**

Type `:q!` and press Enter. Then set a different editor:

```bash
git config --global core.editor "nano"
```

**VS Code says "Git not found"**

Git was installed after VS Code was opened. Close VS Code completely and reopen it.

**I did not verify my email on GitHub or GitLab**

Check your inbox. If the email did not arrive, go to **Settings → Emails** on GitHub or **User settings → Profile → Email** on GitLab and resend.

**I am getting a 403 error when pushing**

Authentication is not set up or your token has expired. Re-run `gh auth login` (Option A) or create a new token (Option C).

---

## Summary

By now you should have:

- GitHub account created, email verified and 2FA enabled
- GitLab account created, email verified and 2FA enabled
- Git installed and up to date
- Name and email configured - matching your GitHub and GitLab email
- Default branch set to `main`
- Line endings configured for your OS
- Authentication working (GitHub CLI, SSH or PAT)
- Git working inside your IDE or editor
- All tests passing

You are ready to start learning Git properly.

---

## Next Step

Head to [What is Version Control?](../git/01-what-is-version-control.md) to begin the course.

---

## Sources and Further Reading

- [Official Git download page](https://git-scm.com/downloads) - always get the latest version here
- [Git first-time setup](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) - the official guide to first-time configuration
- [GitHub - create an account](https://github.com/join) - sign up for GitHub
- [GitLab - register](https://gitlab.com/users/sign_up) - sign up for GitLab
- [GitHub Student Developer Pack](https://education.github.com/pack) - free tools for verified students
- [GitHub SSH documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) - step-by-step SSH setup
- [GitLab SSH documentation](https://docs.gitlab.com/user/ssh/) - step-by-step SSH setup for GitLab
- [GitHub Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) - how to create and manage tokens
- [GitHub CLI documentation](https://cli.github.com/manual/) - the full reference for `gh`
- [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager) - the credential helper bundled with Git for Windows
- [Homebrew](https://brew.sh) - the package manager for Mac
- [Git for Windows](https://gitforwindows.org) - the official Git installer for Windows
- [VS Code Source Control](https://code.visualstudio.com/docs/sourcecontrol/overview) - VS Code's official Git integration guide
- [JetBrains Git integration](https://www.jetbrains.com/help/idea/using-git-integration.html) - the official JetBrains guide
- [Xcode developer page](https://developer.apple.com/xcode/) - Apple's official Xcode documentation
- [GitLens extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) - the most popular Git extension for VS Code

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

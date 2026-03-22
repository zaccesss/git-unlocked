# ⚙️ Setting Up

> "Well begun is half done." - Aristotle

**Difficulty:** 🟢 Beginner
**Time to complete:** 30-50 minutes

This file gets you fully set up before you write a single Git command. By the end you will have a GitHub account, a GitLab account, Git installed and configured, authentication working and Git set up inside your code editor or IDE.

Do not skip this file. A properly configured setup prevents a huge number of the errors beginners run into.

---

## 📋 Table of Contents

- [What is a terminal?](#️-what-is-a-terminal)
- [What is an IDE?](#-what-is-an-ide)
- [Step 1 - Check if Git is already installed](#-step-1---check-if-git-is-already-installed)
- [Step 2 - Create your GitHub account](#-step-2---create-your-github-account)
- [Step 3 - Create your GitLab account](#-step-3---create-your-gitlab-account)
- [Step 4 - Install Git](#-step-4---install-git)
- [Step 5 - Configure Git for the first time](#-step-5---configure-git-for-the-first-time)
- [Step 6 - Set up authentication](#-step-6---set-up-authentication)
- [Step 7 - Set up Git in your IDE](#-step-7---set-up-git-in-your-ide)
- [Step 8 - Quick test to verify everything works](#-step-8---quick-test-to-verify-everything-works)
- [Common setup mistakes](#-common-setup-mistakes)
- [Summary](#-summary)
- [Sources and Further Reading](#-sources-and-further-reading)

---

## 🖥️ What is a Terminal?

Before anything else - what is a terminal and where do you find it?

A **terminal** (also called command line, command prompt, shell or console) is a text-based window where you type commands one line at a time and press Enter to run them. It is like talking directly to your computer in a language it understands.

You do not need to be a hacker to use a terminal. You just need to type the right words.

**How to open a terminal:**

🪟 **Windows:**
- Press the **Windows key**, type `cmd` and press Enter to open Command Prompt
- Or type `powershell` and press Enter to open PowerShell (more modern - recommended)
- Or type `Windows Terminal` if you have it installed (the best option for Windows in 2026)

🍎 **Mac:**
- Press `Cmd + Space` to open Spotlight, type `Terminal` and press Enter
- Or go to **Applications → Utilities → Terminal**

🐧 **Linux:**
- Press `Ctrl + Alt + T` on most distributions
- Or right-click the desktop and choose "Open Terminal"

⚠️ **Important for beginners:** Type commands **one at a time**. Press Enter after each one. Read the output before typing the next command. Never paste a block of multiple commands at once until you understand what each one does.

---

## 💻 What is an IDE?

An **IDE (Integrated Development Environment)** is an all-in-one application for writing and managing code. Think of it as a "super word processor for code" that combines:

- A **code editor** with syntax highlighting and auto-completion
- A **file explorer** to navigate your project
- A **terminal** so you can run commands without switching windows
- **Debugger** tools to find and fix errors
- **Git integration** so you can manage version control visually without the terminal

**Popular IDEs in 2026 (by developer usage):**

| IDE | Best for | Price |
|---|---|---|
| **VS Code** | Everything - most popular by far | Free |
| **IntelliJ IDEA** | Java and Kotlin | Free (Community) / Paid (Ultimate) |
| **PyCharm** | Python | Free (Community) / Paid (Pro) |
| **WebStorm** | JavaScript and TypeScript | Paid |
| **Visual Studio 2026** | C# and .NET on Windows | Free (Community) / Paid |
| **Xcode 26** | iOS, macOS and Apple platforms | Free (Mac App Store) |
| **CLion** | C and C++ | Paid |
| **Rider** | .NET and Unreal Engine | Paid |

💡 **Recommendation:** If you are new to development, start with **VS Code**. It is free, works on Windows, Mac and Linux, has an enormous extension ecosystem and is used by over 75% of developers worldwide.

A **text editor** like Notepad only edits text with no understanding of code. A **terminal** is a command-line interface with no graphical editor. An IDE combines all three into one application.

---

## 🔍 Step 1 - Check if Git is Already Installed

Before installing anything, check if Git is already on your machine. Many computers come with Git pre-installed, especially Macs and Linux machines.

Open your terminal and type the following command, then press Enter:

🪟 **Windows (Command Prompt or PowerShell):**
```
git --version
```

🍎 **Mac (Terminal):**
```
git --version
```

🐧 **Linux (Terminal):**
```
git --version
```

If Git is installed you will see something like:
```
git version 2.53.0
```

If you see `'git' is not recognized` (Windows) or `command not found` (Mac/Linux), Git is not installed. That is fine - continue through the steps and you will install it in Step 4.

💡 **Tip:** Even if Git is installed, check the version number. The current stable release is **Git 2.53.0** (February 2026). If your version is older than 2.28, it is worth updating - some features in this course use newer Git behaviour. Git 3.0 is expected later in 2026 and will bring significant changes including defaulting to `main` as the branch name.

---

## 🐙 Step 2 - Create Your GitHub Account

You need a GitHub account before configuring Git. This is because when you configure Git in Step 5, you will set your email address - and that email must match the one on your GitHub account so your commits link to your profile correctly.

💡 **What is GitHub?** GitHub is the world's largest platform for hosting Git repositories. It is where you will store your code online, collaborate with others and show your work to the world. It currently has over 180 million developers. You will learn everything about it in the `github/` folder - for now you just need an account.

### Creating your account

1. Open your browser and go to **https://github.com**
2. Click **Sign up** in the top right corner
3. Enter your **email address** - use one you check regularly and will keep long term. This is the email you will use in your Git config later
4. Create a **password** - use something strong and unique. A password manager is recommended
5. Choose a **username**

💡 **Choosing a good GitHub username - this matters:**
- Your username appears on every commit you ever make and on your public profile
- Keep it professional - recruiters and employers will see it
- Use your real name or a variation of it if possible
- Lowercase letters, numbers and hyphens only - no spaces
- Keep it short and memorable
- Avoid numbers at the end like `john123` if you can - it looks less professional
- You can change it later but it breaks existing links, so choose carefully now

6. Solve the puzzle to verify you are not a robot
7. Click **Create account**
8. GitHub will send a **verification code** to your email - open your email, copy the code and paste it into GitHub
9. On the welcome screen, answer the questions or click **Skip personalisation** at the bottom

### After creating your account

**Verify your email** - GitHub will prompt you to verify. Check your inbox and click the verification link. Some features are locked until you do this. If you did not receive the email, check your spam folder or go to **Settings → Emails** and click resend.

**Set up two-factor authentication (2FA)** - go to **Settings → Password and security → Two-factor authentication** and enable it. This protects your account from being hacked. Use an authenticator app like Google Authenticator or Authy rather than SMS if possible - SMS can be intercepted.

**Add a profile photo** - go to **Settings → Public profile** and upload a photo. A real photo or professional avatar makes your profile look more trustworthy to collaborators and recruiters.

**Fill in your bio** - add your name, location and what you are working on. This takes 2 minutes and makes a real difference to how your profile appears to others.

🔒 **Security:** Never share your GitHub password. If you think your account has been compromised, go to **Settings → Password and security** immediately, change your password, regenerate your recovery codes and revoke any suspicious OAuth applications.

💡 **Student Developer Pack:** If you are a student, go to **https://education.github.com** after creating your account and apply for the GitHub Student Developer Pack. It gives you GitHub Copilot Pro, JetBrains IDEs, domain names, cloud credits and over 100 other tools completely free. All you need is a school or university email address. Approval usually takes 1-3 days. This is worth thousands of pounds or dollars in free software.

---

## 🦊 Step 3 - Create Your GitLab Account

GitLab is a separate platform from GitHub. Many companies - especially in enterprise, government and regulated industries - use GitLab instead of or alongside GitHub. You will want an account on both.

💡 **What is GitLab?** GitLab is a complete DevSecOps platform that includes Git hosting, built-in CI/CD pipelines, security scanning and more - all in one place. The current version is GitLab 18.10 (March 2026). You will learn everything about it in the `gitlab/` folder.

### Creating your account

1. Open your browser and go to **https://gitlab.com**
2. Click **Register now** or **Sign up**
3. You can sign up with your **GitHub account** - click "Continue with GitHub" for convenience. This saves creating another password and links the accounts. Or sign up with an email address if you prefer to keep them separate
4. If signing up with email: enter your **first name, last name, username and email address**

💡 **GitLab username tip:** You can use the same username as GitHub if it is available. Keeping them consistent makes life easier - people can find you on both platforms easily.

5. Create a **password** if signing up with email
6. Click **Register**
7. GitLab will send a **confirmation email** - open it and click the confirmation link
8. On the welcome screen, answer the onboarding questions or skip them

### After creating your account

**Verify your email** - required before you can push code to GitLab. Check your inbox and click the confirmation link.

**Set up two-factor authentication** - go to **User settings → Account → Two-factor authentication** and enable it. Same recommendation as GitHub - use an authenticator app rather than SMS.

**Add a profile photo and bio** - go to **User settings → Profile** and fill in your details.

**Note your username** - you will need it when setting up SSH keys in Step 6.

🔒 **Security:** Use a different password for GitLab than for GitHub. If one account is compromised, the other stays safe. Store both in a password manager.

💡 **GitLab vs GitHub - which should you use?** You do not have to choose - most developers use both. GitHub is better for open source and broad collaboration. GitLab is better for private projects, enterprise use and when you need powerful built-in CI/CD pipelines. A full comparison is in [gitlab/02-gitlab-vs-github.md](../gitlab/02-gitlab-vs-github.md).

---

## 📥 Step 4 - Install Git

Now that you have your accounts, install Git. If Step 1 confirmed Git is already installed at version 2.28 or higher, you can skip to Step 5.

### 🪟 Windows

**Option A - Git for Windows (recommended for beginners):**

1. Open your web browser and go to: **https://git-scm.com/install/windows**
2. The download should start automatically. If not, click the link for the 64-bit installer
3. Run the downloaded file (it will be named something like `Git-2.53.0-64-bit.exe`)
4. Click through the installer. The default options are fine for most people, but pay attention to these specific screens:

   - **Default editor:** Change from Vim to your preferred editor. If you have VS Code, choose "Use Visual Studio Code as Git's default editor". If you are not sure, choose Nano - it is much easier for beginners than Vim
   - **Initial branch name:** Select "Override the default branch name for new repositories" and type `main`. This matches GitHub and GitLab's default
   - **PATH environment:** Choose "Git from the command line and also from 3rd-party software" - this makes Git available in Command Prompt, PowerShell and anywhere else
   - **Line ending conversions:** Choose "Checkout Windows-style, commit Unix-style line endings" - this prevents problems when sharing code across operating systems
   - **Terminal emulator:** Choose "Use Windows' default console window" to use PowerShell/CMD, or "Use MinTTY" for Git Bash (a Unix-style terminal)

5. Click **Install** and wait for it to finish
6. Click **Finish**
7. **Close and reopen** any terminal windows. Git will not be recognised in terminals that were already open before installation

**Option B - winget (Windows Package Manager):**

Open PowerShell and type this command, then press Enter:
```
winget install --id Git.Git -e --source winget
```

**Option C - Chocolatey (if you have Chocolatey installed):**

Open PowerShell as Administrator and type:
```
choco install git
```

**Option D - Scoop (if you have Scoop installed):**
```
scoop install git
```

💡 **Git for Windows also installs Git Bash** - a terminal that lets you use Unix-style commands on Windows. Many developers prefer it for Git work.

⚠️ **After installing on Windows:** Always close and reopen your terminal after installation. The new PATH only takes effect in new terminal windows.

---

### 🍎 Mac

**Option A - Homebrew (recommended):**

Homebrew is a package manager for Mac - it lets you install developer tools with simple commands. If you do not have Homebrew yet, install it first.

Open Terminal and type this command, then press Enter:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the on-screen prompts. It will ask for your Mac password.

⚠️ **Apple Silicon Macs (M1, M2, M3, M4 or M5 chip):** After Homebrew installs, you must run these two commands or it will not work:
```
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```
Then:
```
eval "$(/opt/homebrew/bin/brew shellenv)"
```

Once Homebrew is installed, install Git by typing:
```
brew install git
```

**Option B - Xcode Command Line Tools:**

If you use Xcode or develop for Apple platforms, the Command Line Tools include Git. Type this in Terminal:
```
xcode-select --install
```

A dialog will appear - click **Install**. This takes a few minutes.

**Option C - Official installer:**

Go to **https://git-scm.com/install/mac** in your browser and download the installer.

💡 **Which Mac do I have?** Click the Apple logo (top left) → About This Mac. If it says "Apple M1", "M2", "M3", "M4" or "M5" you have Apple Silicon. If it says "Intel" you have an Intel Mac.

---

### 🐧 Linux

Use your distribution's package manager. Type the command for your distribution, press Enter, and enter your password when asked:

**Ubuntu, Debian or Linux Mint:**
```
sudo apt update
```
Then:
```
sudo apt install git
```

**Fedora:**
```
sudo dnf install git
```

**Arch Linux or Manjaro:**
```
sudo pacman -S git
```

**openSUSE:**
```
sudo zypper install git
```

**CentOS, RHEL or AlmaLinux:**
```
sudo yum install git
```

**Alpine Linux:**
```
apk add git
```

💡 **Ubuntu tip:** For the very latest Git version, add the official PPA first:
```
sudo add-apt-repository ppa:git-core/ppa
```
Then:
```
sudo apt update
```
Then:
```
sudo apt install git
```

---

## ⚙️ Step 5 - Configure Git for the First Time

This step is essential. Without it, Git cannot create commits because it does not know who you are. Use the **same email address you used to sign up for GitHub and GitLab** - this is how your commits get linked to your profile and how your contribution graph stays accurate.

Run each command below one at a time. Replace the example values with your actual name and email.

### Set your name

🪟 **Windows (Command Prompt or PowerShell) / 🍎 Mac (Terminal) / 🐧 Linux (Terminal):**
```
git config --global user.name "Your Name"
```

For example:
```
git config --global user.name "Isaac Adjei"
```

### Set your email

```
git config --global user.email "your.email@example.com"
```

⚠️ **This must be the same email you used for GitHub and GitLab.** If they do not match, your commits will not be linked to your profile and your contribution graph will not update.

### Set your default branch name

```
git config --global init.defaultBranch main
```

This sets `main` as the name of the first branch in every new repository you create - matching GitHub and GitLab's default. Without this, older versions of Git use `master`, which causes confusing errors when pushing to GitHub.

### Set your default pull behaviour

```
git config --global pull.rebase false
```

This tells Git to use merge (not rebase) when pulling changes. For beginners, merge is simpler and easier to understand. You can change this later once you know the difference.

### Set line ending behaviour

Line endings work differently on different operating systems. This setting prevents problems when sharing code across platforms.

🪟 **Windows only:**
```
git config --global core.autocrlf true
```

🍎 **Mac only:**
```
git config --global core.autocrlf input
```

🐧 **Linux only:**
```
git config --global core.autocrlf input
```

### Set your default editor (optional but recommended)

When Git needs you to write a message (for example during a merge), it opens a text editor. Set this to an editor you are comfortable with.

**VS Code:**
```
git config --global core.editor "code --wait"
```

**Nano (good beginner-friendly option for terminal users):**
```
git config --global core.editor "nano"
```

💡 **If Git ever opens Vim unexpectedly** and you do not know how to exit, type `:q!` and press Enter to quit without saving. Then set a different editor using one of the commands above.

### Verify your configuration

Check everything looks correct by typing:
```
git config --global --list
```

You should see your name, email, default branch name and other settings listed.

---

## 🔐 Step 6 - Set up Authentication

To push code to GitHub or GitLab you need to prove who you are. There are three main ways to do this. **Option A (GitHub CLI) is the easiest for beginners.**

---

### Option A - GitHub CLI (easiest - recommended for beginners)

The GitHub CLI (`gh`) handles authentication automatically through your browser. No SSH keys or tokens to manage manually.

**Install GitHub CLI:**

🪟 **Windows:**
```
winget install --id GitHub.cli
```

🍎 **Mac:**
```
brew install gh
```

🐧 **Linux (Ubuntu/Debian):**
```
sudo apt install gh
```

**Authenticate:**

Type this command:
```
gh auth login
```

Follow the prompts:
- Choose `GitHub.com`
- Choose `HTTPS` or `SSH` (either works)
- Choose `Login with a web browser`
- Copy the one-time code shown, press Enter
- Your browser opens - paste the code and authorise

That is it. GitHub CLI securely stores your credentials and you will never need to enter a password for Git operations again.

---

### Option B - SSH Keys (secure, recommended for regular use)

SSH keys work like a lock and key. You generate a key pair - a private key that stays on your computer and a public key that you give to GitHub or GitLab. They match without you ever typing a password.

**Step 1 - Generate an SSH key:**

🪟 **Windows (Git Bash or PowerShell) / 🍎 Mac (Terminal) / 🐧 Linux (Terminal):**
```
ssh-keygen -t ed25519 -C "your.email@example.com"
```

Replace the email with the one on your GitHub and GitLab accounts.

When asked "Enter file in which to save the key" - press **Enter** to accept the default location.

When asked for a passphrase - either press **Enter** for no passphrase or type one for extra security. A passphrase is recommended.

**Step 2 - Start the SSH agent and add your key:**

🪟 **Windows (Git Bash):**
```
eval "$(ssh-agent -s)"
```
Then:
```
ssh-add ~/.ssh/id_ed25519
```

🪟 **Windows (PowerShell - run as Administrator):**
```
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
```
Then:
```
Start-Service ssh-agent
```
Then:
```
ssh-add $env:USERPROFILE\.ssh\id_ed25519
```

🍎 **Mac:**
```
eval "$(ssh-agent -s)"
```
Then:
```
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

🐧 **Linux:**
```
eval "$(ssh-agent -s)"
```
Then:
```
ssh-add ~/.ssh/id_ed25519
```

**Step 3 - Copy your public key:**

🪟 **Windows (PowerShell):**
```
Get-Content $env:USERPROFILE\.ssh\id_ed25519.pub | clip
```

🍎 **Mac:**
```
pbcopy < ~/.ssh/id_ed25519.pub
```

🐧 **Linux:**
```
cat ~/.ssh/id_ed25519.pub
```
Select all the output and copy it manually.

**Step 4 - Add your key to GitHub:**

1. Open your browser and go to **https://github.com/settings/keys**
2. Click **New SSH key**
3. Give it a title (e.g. `My Laptop`)
4. Choose **Authentication Key** as the type
5. Paste your public key
6. Click **Add SSH key**

**Add your key to GitLab:**

1. Go to **https://gitlab.com/-/user_settings/ssh_keys**
2. Paste your public key
3. Give it a title (e.g. `My Laptop`)
4. Set an expiry date if you want (optional but good for security)
5. Click **Add key**

**Step 5 - Test the connection:**

For GitHub:
```
ssh -T git@github.com
```
Expected response: `Hi YOUR_USERNAME! You've successfully authenticated, but GitHub does not provide shell access.`

For GitLab:
```
ssh -T git@gitlab.com
```
Expected response: `Welcome to GitLab, @your_username!`

---

### Option C - Personal Access Token (HTTPS alternative)

Since August 2021, GitHub no longer accepts your account password for Git operations. If you prefer HTTPS over SSH, you need a Personal Access Token (PAT).

**Create a fine-grained token on GitHub (recommended):**

1. Go to **https://github.com/settings/tokens**
2. Click **Generate new token → Fine-grained token**
3. Give it a name (e.g. `My Laptop`)
4. Set an expiry date (shorter is more secure - 90 days is a good balance)
5. Under Repository access, choose **All repositories** or select specific ones
6. Under Permissions → Contents → choose **Read and write**
7. Click **Generate token**
8. **Copy the token immediately** - you will not see it again

**Create a token on GitLab:**

1. Go to **https://gitlab.com/-/user_settings/personal_access_tokens**
2. Give it a name
3. Set an expiry date
4. Under Scopes, tick **read_repository** and **write_repository**
5. Click **Create personal access token**
6. **Copy it immediately** - you will not see it again

⚠️ **Treat your tokens like passwords.** Store them in a password manager. Never write them in a file in your repository. Never share them with anyone.

**Set up a credential helper so you do not have to paste the token every time:**

🪟 **Windows:**
```
git config --global credential.helper manager
```

🍎 **Mac:**
```
git config --global credential.helper osxkeychain
```

🐧 **Linux:**
```
git config --global credential.helper libsecret
```

The next time you push, Git will ask for your username and token, then save them securely.

---

## 🖥️ Step 7 - Set up Git in Your IDE

### VS Code

VS Code has full Git support built in - no extensions needed for basic use.

1. Open VS Code
2. Open any folder by clicking **File → Open Folder**
3. Press `Ctrl + Shift + G` (Windows/Linux) or `Cmd + Shift + G` (Mac) to open the **Source Control** panel
4. VS Code automatically detects Git and shows your changes

**Set VS Code as your Git editor (if not done in Step 5):**
```
git config --global core.editor "code --wait"
```

**Recommended extensions:**

Install extensions by pressing `Ctrl + Shift + X` (Windows/Linux) or `Cmd + Shift + X` (Mac), then searching by name:

- **GitLens** by GitKraken - the most popular Git extension (46 million+ downloads). Shows who wrote each line, full commit history, visual branch graph and much more. The free tier is generous
- **GitHub Pull Requests** by GitHub - manage pull requests and issues without leaving VS Code

💡 **Note on Git Graph:** Git Graph is still available with 13 million+ downloads but is no longer actively maintained. VS Code's built-in **Source Control Graph** (added in version 1.93) now handles visual branch history and largely replaces it.

---

### JetBrains IDEs (IntelliJ IDEA, PyCharm, WebStorm, CLion, Rider, GoLand)

All JetBrains IDEs share the same Git integration.

**Setting up Git:**
1. Open your IDE
2. Open Settings: `Ctrl + Alt + S` (Windows/Linux) or `Cmd + ,` (Mac)
3. Go to **Version Control → Git**
4. The IDE usually detects Git automatically. If not, click the folder icon and find it:
   - 🪟 Windows: usually `C:\Program Files\Git\bin\git.exe`
   - 🍎 Mac: usually `/opt/homebrew/bin/git` or `/usr/bin/git`
   - 🐧 Linux: usually `/usr/bin/git`
5. Click **Test** to confirm it works
6. Click **OK**

**Connect to GitHub:**
Settings → Version Control → **GitHub** → click `+` → click **Log In via GitHub** → authorise in your browser

**Connect to GitLab:**
Settings → Version Control → **GitLab** → click `+` → enter your GitLab server URL (`https://gitlab.com`) → paste a Personal Access Token with `api` and `read_user` scopes

**Useful shortcuts:**

| Action | Windows/Linux | Mac |
|---|---|---|
| Commit | `Ctrl + K` | `Cmd + K` |
| Push | `Ctrl + Shift + K` | `Cmd + Shift + K` |
| Pull | `Ctrl + T` | `Cmd + T` |
| Git log | `Alt + 9` | `Cmd + 9` |
| VCS operations popup | `` Alt + ` `` | `Ctrl + V` |

---

### Visual Studio 2026 (Windows)

Visual Studio 2026 has full Git support built in. No extensions needed.

1. Open Visual Studio 2026
2. Go to **View → Git Changes** (or press `Ctrl + 0, Ctrl + G`)
3. Visual Studio detects your repository automatically

**Connect to GitHub:**
Go to **Git → GitHub → Add Account** and sign in via your browser.

**Connect to GitLab:**
Go to **Git → Manage Remotes** and add your GitLab repository URL manually.

---

### Xcode 26 (Mac)

1. Open Xcode 26
2. When creating a new project, check **Create Git repository on my Mac** to set it up automatically
3. For existing projects: go to **Integrate → Create Git Repositories**

**Connect to GitHub or GitLab:**
1. Go to **Xcode → Settings → Accounts**
2. Click the `+` button
3. Choose **GitHub** (or enter your GitLab server URL for GitLab)
4. Sign in

The **Source Control Navigator** (`Cmd + 2`) shows your branches, commits and remote repositories.

---

### Neovim or Vim

Git works perfectly from any terminal alongside Neovim or Vim. Recommended plugins:

- **vim-fugitive** - the gold standard Git plugin for Vim and Neovim. Run any Git command with `:Git` or `:G`
- **gitsigns.nvim** - shows added, changed and deleted lines in the editor margin
- **Neogit** - a full interactive Git interface inside Neovim
- **LazyGit** - opens the LazyGit terminal UI in a floating window. Install separately with `brew install lazygit` (Mac) or `winget install lazygit` (Windows) first

---

### Sublime Text

Install the **GitSavvy** package via Package Control:
1. Press `Ctrl + Shift + P` (Windows/Linux) or `Cmd + Shift + P` (Mac)
2. Type `Package Control: Install Package` and press Enter
3. Search for `GitSavvy` and press Enter to install

Access all Git commands through the Command Palette with `Ctrl + Shift + P` and type `git:`.

---

## ✅ Step 8 - Quick Test to Verify Everything Works

Run these commands one at a time to confirm your setup is complete.

**Test 1 - Git is installed:**
```
git --version
```
Expected output: `git version 2.53.0` (or similar)

**Test 2 - Your name is set:**
```
git config --global user.name
```
Expected output: your name

**Test 3 - Your email is set:**
```
git config --global user.email
```
Expected output: your email address

**Test 4 - Default branch is set:**
```
git config --global init.defaultBranch
```
Expected output: `main`

**Test 5 - SSH connection to GitHub (if you set up SSH in Step 6):**
```
ssh -T git@github.com
```
Expected output: `Hi YOUR_USERNAME! You've successfully authenticated...`

**Test 6 - SSH connection to GitLab (if you set up SSH in Step 6):**
```
ssh -T git@gitlab.com
```
Expected output: `Welcome to GitLab, @your_username!`

**Test 7 - Create a test repository and make a commit:**

🪟 **Windows (PowerShell):**
```
mkdir git-test
```
```
cd git-test
```
```
git init
```
```
echo "hello git" > README.md
```
```
git add .
```
```
git commit -m "initial commit"
```
```
git log --oneline
```

🍎 **Mac / 🐧 Linux:**
```
mkdir git-test
```
```
cd git-test
```
```
git init
```
```
echo "hello git" > README.md
```
```
git add .
```
```
git commit -m "initial commit"
```
```
git log --oneline
```

Expected output from `git log --oneline`: a line showing a short commit hash and the message `initial commit`.

**Clean up the test folder:**

🪟 **Windows:**
```
cd ..
```
```
rmdir /s /q git-test
```

🍎 **Mac / 🐧 Linux:**
```
cd ..
```
```
rm -rf git-test
```

If all tests pass, you are fully set up. 🎉

---

## ❌ Common Setup Mistakes

**`'git' is not recognized` on Windows after installation**

You need to open a new terminal window. Close Command Prompt or PowerShell completely and reopen it. The PATH update only applies to new terminal sessions.

If it still does not work, reinstall using `winget install --id Git.Git -e --source winget` which handles PATH automatically.

**`git push` asks for a password then fails**

GitHub no longer accepts account passwords for Git operations (removed August 2021). You need to use either SSH keys (Step 6, Option B) or a Personal Access Token (Step 6, Option C).

**`ssh -T git@github.com` returns "Permission denied (publickey)"**

Check that:
- Your key files exist: run `ls ~/.ssh/` and look for `id_ed25519` and `id_ed25519.pub`
- The SSH agent is running: run `eval "$(ssh-agent -s)"` then `ssh-add ~/.ssh/id_ed25519`
- Your public key was added to GitHub at https://github.com/settings/keys
- You copied the public key (the `.pub` file) not the private key (the file without `.pub`)

**My commits do not appear on my GitHub contribution graph**

The email in your Git config does not match the email on your GitHub account. Fix it:
```
git config --global user.email "the.email@you.used.for.github.com"
```

Note: this only affects future commits.

**`xcrun: error: invalid active developer path` on Mac after an OS update**

This happens after nearly every major macOS update. Run:
```
xcode-select --install
```

**`zsh: command not found: brew` on Apple Silicon Mac**

Homebrew is not in your PATH. Run:
```
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```
Then:
```
source ~/.zprofile
```

**`Author identity unknown` error when committing**

Git cannot commit without a name and email. Run:
```
git config --global user.name "Your Name"
```
Then:
```
git config --global user.email "your@email.com"
```

**My commits show the wrong name or email**

Fix them:
```
git config --global user.name "Correct Name"
```
```
git config --global user.email "correct@email.com"
```

Note: this only affects future commits. To fix past commits see [git/14-reset-and-revert.md](../git/14-reset-and-revert.md).

**Git opens Vim and I cannot exit**

Type `:q!` and press Enter. This quits without saving. Then set a different editor:
```
git config --global core.editor "nano"
```

**VS Code says "Git not found"**

Git was installed after VS Code was opened. Close VS Code completely and reopen it.

**JetBrains IDE shows "Git executable not found"**

Go to **File → Settings → Version Control → Git** and manually set the path to your Git executable (see Step 7 above for the correct path per OS).

**I did not verify my email on GitHub or GitLab**

Some features will not work until you do. Check your inbox for the verification email. If you did not receive it, go to **Settings → Emails** on GitHub or **User settings → Profile → Email** on GitLab and click resend.

**I cannot push to GitHub and I am getting a 403 error**

This usually means your authentication is not set up correctly or your token has expired. Re-run `gh auth login` (Option A) or generate a new token (Option C) and try again.

---

## 📖 Summary

By now you should have:

- ✅ GitHub account created, email verified and 2FA enabled
- ✅ GitLab account created, email verified and 2FA enabled
- ✅ Git installed and up to date
- ✅ Your name and email configured - matching your GitHub and GitLab email
- ✅ Default branch set to `main`
- ✅ Pull behaviour configured
- ✅ Line endings configured correctly for your OS
- ✅ Authentication set up (GitHub CLI, SSH or PAT)
- ✅ Git working inside your IDE or editor
- ✅ All tests passing

You are ready to start learning Git properly.

---

## ➡️ Next Step

Head to [What is Version Control?](../git/01-what-is-version-control.md) to begin the course. →

---

## 🔗 Sources and Further Reading

- [Official Git download page](https://git-scm.com/downloads) - always get the latest version here
- [Git first-time setup documentation](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup) - the official guide to first-time configuration
- [GitHub - join](https://github.com/join) - create your GitHub account
- [GitLab - register](https://gitlab.com/users/sign_up) - create your GitLab account
- [GitHub Student Developer Pack](https://education.github.com/pack) - free tools worth thousands for verified students
- [GitHub SSH key documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) - step-by-step SSH setup from GitHub
- [GitLab SSH key documentation](https://docs.gitlab.com/user/ssh/) - step-by-step SSH setup from GitLab
- [GitHub Personal Access Tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) - how to create and manage tokens
- [GitHub CLI documentation](https://cli.github.com/manual/) - the full reference for the `gh` command
- [Git Credential Manager](https://github.com/git-ecosystem/git-credential-manager) - the cross-platform credential helper bundled with Git for Windows
- [Homebrew](https://brew.sh) - the package manager for Mac
- [Git for Windows](https://gitforwindows.org) - the official Git installer for Windows
- [VS Code Source Control documentation](https://code.visualstudio.com/docs/sourcecontrol/overview) - the official VS Code guide to Git integration
- [JetBrains Git integration documentation](https://www.jetbrains.com/help/idea/using-git-integration.html) - the official JetBrains guide
- [Xcode developer page](https://developer.apple.com/xcode/) - Apple's official Xcode page
- [GitLens extension](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) - the most popular Git extension for VS Code

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

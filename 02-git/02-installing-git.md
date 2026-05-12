# Installing Git

**Difficulty:** 🟢 Beginner | **Time:** 10-20 minutes

This file covers installing Git on Windows, Mac and Linux in full detail. If you already followed [Setting Up](../01-introduction/03-setting-up.md) and installed Git there, you can skip to the [Verify your installation](#verify-your-installation) section to confirm everything is working correctly.

---

## Table of Contents

- [Before you install](#before-you-install)
- [Installing on Windows](#installing-on-windows)
- [Installing on Mac](#installing-on-mac)
- [Installing on Linux](#installing-on-linux)
- [Verify your installation](#verify-your-installation)
- [Keeping Git up to date](#keeping-git-up-to-date)
- [Uninstalling Git](#uninstalling-git)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Before You Install

### What version should you install?

Always install the latest stable release. The current stable version is **Git 2.53.0** (February 2026).

> [!NOTE]
> Git follows a roughly quarterly release schedule. Minor versions (2.51, 2.52, 2.53) add new features and fix bugs. You do not need to update immediately on every release, but staying within the last two or three releases is good practice. If your version is older than **2.28**, some features used in this course may not be available.

### What about Git 3.0?

Git 3.0 is expected later in 2026. It will change the default hash algorithm, default branch name and default ref storage for new repositories. Your existing repositories will continue to work. This course will be updated when 3.0 releases. For now, install 2.53.0.

### Do you already have Git?

Check first. Open your terminal and run:

**Windows (Command Prompt or PowerShell), Mac (Terminal), Linux (Terminal):**

```bash
git --version
```

If you see `git version 2.x.x`, Git is already installed. If the version is 2.28 or higher, you can skip straight to [Verify your installation](#verify-your-installation). If it is older, follow the update steps for your operating system below.

---

## Installing on Windows

Windows does not come with Git pre-installed. You have four installation options. **Option A is recommended for beginners.**

### Option A - Git for Windows (recommended)

Git for Windows is the official installer. It includes Git, Git Bash (a Unix-style terminal for Windows), Git GUI (a basic graphical interface) and Git Credential Manager.

**Step 1.** Open your browser and go to **https://git-scm.com/install/windows**

The download starts automatically. If it does not, click the link for the **64-bit Git for Windows Setup** installer.

**Step 2.** Run the downloaded `.exe` file. You may see a User Account Control prompt asking for permission - click **Yes**.

**Step 3.** Work through the installer. Most screens can be left at their defaults, but pay close attention to these specific ones:

**Select Components** - leave the defaults selected. "Git Bash Here" and "Git GUI Here" add useful right-click context menu options.

**Choosing the default editor used by Git** - this is important. By default Git uses Vim, which is extremely difficult for beginners. Change this to:
- **Use Visual Studio Code as Git's default editor** if you have VS Code installed (recommended)
- **Use Notepad++ as Git's default editor** if you have Notepad++
- **Use Nano as Git's default editor** if you are not sure - Nano is much simpler than Vim

**Adjusting the name of the initial branch in new repositories** - select **Override the default branch name for new repositories** and type `main`. This matches GitHub and GitLab's default and avoids the confusing mismatch that happens when your local branch is called `master` but GitHub expects `main`.

**Adjusting your PATH environment** - choose **Git from the command line and also from 3rd-party software**. This makes Git available in Command Prompt, PowerShell and any other terminal. The other options are either too restrictive or too permissive.

**Choosing the SSH executable** - leave as **Use bundled OpenSSH**.

**Choosing HTTPS transport backend** - leave as **Use the OpenSSL library**.

**Configuring the line ending conversions** - choose **Checkout Windows-style, commit Unix-style line endings**. This automatically handles the difference between Windows (CRLF) and Unix (LF) line endings, preventing formatting issues when you share code with Mac or Linux developers.

**Configuring the terminal emulator** - choose **Use Windows' default console window** to use PowerShell and CMD, or **Use MinTTY** if you prefer Git Bash's terminal. Both work fine.

**Choose the default behavior of `git pull`** - leave as **Default (fast-forward or merge)**.

**Choose a credential helper** - leave as **Git Credential Manager**. This stores your GitHub and GitLab credentials securely in Windows Credential Manager so you do not need to type your password on every push.

**Configuring extra options** - leave the defaults.

**Step 4.** Click **Install** and wait for it to complete.

**Step 5.** Click **Finish**. You can leave "View Release Notes" unchecked.

**Step 6.** **Close and reopen any terminal windows.** This is essential. Git is added to your PATH during installation, but existing terminal sessions do not pick up PATH changes. If you try to run `git` in a terminal that was open before you installed, it will say "not recognized".

> [!WARNING]
> Always close and reopen your terminal after installing or updating Git on Windows. This is the most common cause of "git is not recognized" errors immediately after installation.

---

### Option B - winget (Windows Package Manager)

winget is built into Windows 10 (version 1809+) and Windows 11. It installs and manages software from the command line.

Open PowerShell and run:

```powershell
winget install --id Git.Git -e --source winget
```

winget handles the PATH automatically. Close and reopen your terminal when it finishes.

To update Git using winget later:

```powershell
winget upgrade --id Git.Git
```

---

### Option C - Chocolatey

If you have Chocolatey installed, open PowerShell as Administrator and run:

```powershell
choco install git
```

To update later:

```powershell
choco upgrade git
```

---

### Option D - Scoop

If you have Scoop installed:

```powershell
scoop install git
```

To update later:

```powershell
scoop update git
```

---

### What Git for Windows installs

After installing Git for Windows, you will have:

- **Git** - the version control system itself, available in all terminals
- **Git Bash** - a terminal that lets you use Unix-style commands on Windows. Right-click any folder and choose "Git Bash Here" to open it
- **Git GUI** - a basic graphical interface (most people use VS Code or a dedicated tool instead)
- **Git Credential Manager** - securely stores your GitHub and GitLab credentials

> [!TIP]
> **Git Bash** is useful if you are following tutorials written for Mac or Linux, as Unix commands like `ls`, `touch`, `cat` and `grep` work inside Git Bash but not in standard Windows CMD or PowerShell.

---

## Installing on Mac

Macs come with a very old version of Git pre-installed via Xcode Command Line Tools, but it is almost always outdated. Install a current version using one of the options below.

### Option A - Homebrew (recommended)

Homebrew is the most popular package manager for Mac. If you do not have it yet, install it first.

Open Terminal (`Cmd + Space`, type `Terminal`, press Enter) and run:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow the on-screen prompts. It will ask for your Mac password. The installation takes a few minutes.

> [!WARNING]
> **Apple Silicon Macs (M1, M2, M3, M4 or M5 chip):** After Homebrew installs, you must add it to your PATH or it will not work. Run these two commands:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

```bash
eval "$(/opt/homebrew/bin/brew shellenv)"
```

You only need to do this once. After this, Homebrew will work in every new terminal session automatically.

> [!TIP]
> To check which chip your Mac has, click the Apple logo (top left) and select **About This Mac**. If it says "Apple M1", "M2", "M3", "M4" or "M5" under Chip, you have Apple Silicon. If it says "Intel" under Processor, you have an Intel Mac.

Once Homebrew is installed, install Git:

```bash
brew install git
```

To update Git later:

```bash
brew upgrade git
```

---

### Option B - Xcode Command Line Tools

The Xcode Command Line Tools include Git. This is the simplest option if you develop for Apple platforms and already have Xcode, or if you just want Git with no other tools.

```bash
xcode-select --install
```

A dialog appears asking if you want to install the command line developer tools. Click **Install**. This takes a few minutes.

> [!NOTE]
> The version of Git bundled with Xcode Command Line Tools is not always the latest. As of 2026, it lags behind the current Git release. For the most up-to-date version, use Homebrew.

> [!WARNING]
> After every major macOS update, the Xcode Command Line Tools path often breaks. If Git suddenly stops working after a macOS update, run `xcode-select --install` again to repair it.

---

### Option C - Official installer

Go to **https://git-scm.com/install/mac** in your browser and download the `.pkg` installer. Run it and follow the prompts.

---

### Option D - MacPorts

If you use MacPorts:

```bash
sudo port install git
```

---

## Installing on Linux

Linux distributions vary in their package managers. Use the command for your distribution below. You will be prompted for your password.

### Ubuntu, Debian or Linux Mint

```bash
sudo apt update
```

```bash
sudo apt install git
```

> [!TIP]
> For the very latest stable Git version on Ubuntu, add the official Git maintainers' PPA first:

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

### Fedora

```bash
sudo dnf install git
```

---

### Arch Linux or Manjaro

```bash
sudo pacman -S git
```

---

### openSUSE

```bash
sudo zypper install git
```

---

### CentOS, RHEL or AlmaLinux

```bash
sudo yum install git
```

For a more recent version on RHEL-based systems, enable the IUS or SCL repository first.

---

### Alpine Linux

```bash
apk add git
```

---

### Raspberry Pi OS

Raspberry Pi OS is Debian-based:

```bash
sudo apt update
```

```bash
sudo apt install git
```

---

## Verify Your Installation

After installing, confirm Git is working correctly. Open a new terminal window and run these commands one at a time.

**Check the version:**

```bash
git --version
```

Expected output:

```
git version 2.53.0
```

The exact number may differ slightly depending on your platform and when you installed, but it should be 2.28 or higher.

**Check Git is accessible from your terminal:**

**Windows (Command Prompt):**

```
where git
```

Expected output: a path like `C:\Program Files\Git\cmd\git.exe`

**Mac and Linux:**

```bash
which git
```

Expected output: a path like `/usr/local/bin/git` or `/opt/homebrew/bin/git`

If either of these commands returns nothing or an error, Git is not correctly on your PATH. See the Common Mistakes section below.

**Check the help system works:**

```bash
git help
```

You should see a list of common Git commands. This confirms Git is fully functional.

---

## Keeping Git Up to Date

Git releases updates approximately every 3 months. Staying reasonably current means you get bug fixes, security patches and new features.

### Windows

**Using Git for Windows installer:** Download the latest installer from **https://git-scm.com/install/windows** and run it. It will update your existing installation.

**Using winget:**

```powershell
winget upgrade --id Git.Git
```

**Using Chocolatey:**

```powershell
choco upgrade git
```

### Mac

**Using Homebrew:**

```bash
brew upgrade git
```

To check if an upgrade is available without installing it:

```bash
brew outdated git
```

### Linux

**Ubuntu/Debian:**

```bash
sudo apt update
```

```bash
sudo apt upgrade git
```

**Fedora:**

```bash
sudo dnf upgrade git
```

**Arch Linux:**

```bash
sudo pacman -Syu git
```

---

## Uninstalling Git

You should rarely need to uninstall Git, but here is how if you do.

### Windows

**Via Settings:** Go to **Settings -> Apps -> Installed apps**, search for "Git" and click **Uninstall**.

**Via winget:**

```powershell
winget uninstall --id Git.Git
```

### Mac

**Via Homebrew:**

```bash
brew uninstall git
```

### Linux

**Ubuntu/Debian:**

```bash
sudo apt remove git
```

**Fedora:**

```bash
sudo dnf remove git
```

---

## Try It Yourself

Complete these steps to confirm your installation is fully working.

**Step 1.** Open your terminal and run:

```bash
git --version
```

Write down the version number you see.

**Step 2.** Run:

```bash
git help commit
```

This opens the manual page for the `git commit` command. Read the first few lines. Press `q` to exit.

**Step 3.** Create a temporary test folder and initialise a Git repository:

**Windows (PowerShell):**

```powershell
mkdir git-install-test
cd git-install-test
git init
```

**Mac and Linux:**

```bash
mkdir git-install-test && cd git-install-test
git init
```

You should see:

```
Initialized empty Git repository in .../git-install-test/.git/
```

**Step 4.** Check the repository status:

```bash
git status
```

You should see:

```
On branch main
No commits yet
nothing to commit
```

**Step 5.** Clean up the test folder:

**Windows:**

```powershell
cd ..
rmdir /s /q git-install-test
```

**Mac and Linux:**

```bash
cd .. && rm -rf git-install-test
```

If all five steps worked, Git is installed and ready to use.

---

## Common Mistakes

**`'git' is not recognized as an internal or external command` on Windows**

Git was installed but the terminal was not reopened afterwards. Close your Command Prompt or PowerShell window completely and open a new one. The PATH update only applies to new terminal sessions.

If reopening the terminal does not fix it, Git may not have been added to PATH during installation. Reinstall using `winget install --id Git.Git -e --source winget` which handles PATH automatically.

**`command not found: git` on Mac after installing with Homebrew**

Homebrew installed correctly but is not in your PATH. This is most common on Apple Silicon Macs. Run:

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
```

```bash
source ~/.zprofile
```

Then try `git --version` again.

**`xcrun: error: invalid active developer path` on Mac**

This happens after nearly every major macOS update. It means the Xcode Command Line Tools path has been reset. Fix it:

```bash
xcode-select --install
```

**Git is installed but shows a very old version on Mac**

Your terminal is using the system Git that comes pre-installed with macOS (often version 2.24 or older) instead of the Homebrew version. Check which Git your terminal is using:

```bash
which git
```

If it says `/usr/bin/git`, your terminal is using the system version. If Homebrew is installed, the Homebrew Git should be at `/opt/homebrew/bin/git` (Apple Silicon) or `/usr/local/bin/git` (Intel). Make sure Homebrew is in your PATH using the `brew shellenv` command above.

**`sudo: git: command not found` on Linux**

This can happen if Git is installed in a location that is not in the sudo PATH. Try:

```bash
sudo $(which git) --version
```

Or install Git system-wide using your package manager with `sudo apt install git`.

**The installer keeps asking me about Vim**

During the Git for Windows installation, you can change the default editor on the "Choosing the default editor used by Git" screen. If you already installed and want to change it now, run this in your terminal to switch to VS Code:

```bash
git config --global core.editor "code --wait"
```

Or to Nano:

```bash
git config --global core.editor "nano"
```

---

## Summary

- The current stable Git version is 2.53.0 (February 2026)
- On **Windows**, use the Git for Windows installer from git-scm.com/install/windows, or `winget install --id Git.Git -e --source winget`
- On **Mac**, use Homebrew (`brew install git`) for the latest version, or Xcode Command Line Tools (`xcode-select --install`) for a simpler setup
- On **Linux**, use your distribution's package manager (`sudo apt install git`, `sudo dnf install git` etc.)
- Always close and reopen your terminal after installing on Windows
- On Apple Silicon Macs, always add Homebrew to your PATH after installing
- After macOS updates, run `xcode-select --install` if Git stops working
- Verify with `git --version` - you should see 2.28 or higher

---

## Next Step

Now that Git is installed, the next file configures it with your identity and preferences: [How Git Works](03-how-git-works.md)

---

## Sources and Further Reading

- [Official Git download page](https://git-scm.com/downloads) - always the most up-to-date download links for every platform
- [Git for Windows](https://gitforwindows.org) - the official Git installer for Windows, with release notes and documentation
- [Homebrew](https://brew.sh) - the package manager for Mac, used to install and manage Git and hundreds of other developer tools
- [Git installation documentation](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) - the official Pro Git book chapter on installation across all platforms
- [winget documentation](https://learn.microsoft.com/en-us/windows/package-manager/winget/) - Microsoft's official documentation for the Windows Package Manager
- [Git release notes](https://github.com/git/git/tree/master/Documentation/RelNotes) - detailed notes for every Git release, useful for understanding what changed between versions

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

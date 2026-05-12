# Editing on GitHub

**Difficulty:** 🟢 Beginner to 🟡 Intermediate | **Time:** 25 minutes

You do not always need a local Git setup to work with code on GitHub. GitHub provides three ways to edit files directly from your browser - from quick single-file edits through to a full cloud development environment with a terminal and debugger. Knowing which tool to use and when can save significant time, especially for documentation changes, quick fixes and remote work.

---

## Table of Contents

- [The three editing options](#the-three-editing-options)
- [The web editor](#the-web-editor)
- [Creating a new file](#creating-a-new-file)
- [Uploading files](#uploading-files)
- [Deleting files on GitHub](#deleting-files-on-github)
- [Committing directly vs creating a branch](#committing-directly-vs-creating-a-branch)
- [github.dev - VS Code in the browser](#githubdev---vs-code-in-the-browser)
- [GitHub Codespaces](#github-codespaces)
- [Choosing the right tool](#choosing-the-right-tool)
- [Editing via GitHub CLI](#editing-via-github-cli)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## The Three Editing Options

GitHub provides three distinct tools for editing code without a local Git installation:

| Tool | Access | Terminal | Extensions | Cost | Best for |
|---|---|---|---|---|---|
| **Web editor** | Pencil icon on any file | No | No | Free | Single file, quick edits |
| **github.dev** | Press `.` on any repo | No | Web extensions only | Free | Multi-file edits, navigation |
| **Codespaces** | Green Code button | Yes | Full VS Code extensions | Free tier (limited) | Full development work |

---

## The Web Editor

The simplest editing tool is GitHub's built-in web editor. It opens directly in the browser without any setup.

**How to access:**

Navigate to any file in a repository and click the **pencil icon** (Edit file) in the top-right corner of the file view.

Or navigate to any file and press `E` (GitHub keyboard shortcut for edit).

**What the web editor provides:**

- Syntax highlighting for most programming languages
- Basic text editing (no autocomplete, no linting)
- File content preview (for Markdown)
- Commit controls at the bottom of the page

**What it does not provide:**

- Multiple file editing (one file at a time)
- File navigation between files
- Terminal access
- Extension support

The web editor is best for: fixing a typo in documentation, updating a README, making a small configuration change, editing a single file quickly when you are not at your main machine.

---

## Creating a New File

You can create files directly on GitHub without cloning the repository.

**Step 1.** Navigate to the folder where you want to create the file (or stay at the root).

**Step 2.** Click **Add file** → **Create new file** in the file browser above the list of files.

**Step 3.** Type the file name in the field that appears at the top. To create the file inside a new folder, type the folder name followed by a forward slash:

```
docs/getting-started.md
```

Typing the `/` automatically creates the folder structure and moves the cursor into the file name field.

**Step 4.** Type or paste the file content in the editor below.

**Step 5.** Commit the file (see [Committing directly vs creating a branch](#committing-directly-vs-creating-a-branch)).

> [!TIP]
> Press the **Preview** tab to see how Markdown will render before committing. This is particularly useful for README files, documentation and course content.

---

## Uploading Files

GitHub allows you to upload existing files directly through the web interface without using Git.

**Step 1.** Navigate to the folder where you want to upload files.

**Step 2.** Click **Add file** → **Upload files**.

**Step 3.** Drag and drop files into the upload area, or click **choose your files** to open a file picker. You can upload multiple files at once.

**Limits:**

- Maximum file size for browser upload: **25 MB** per file
- Files larger than 25 MB must be pushed via Git or Git LFS
- Files larger than 100 MB cannot be pushed via Git at all and require Git LFS

**Step 4.** Commit the upload (see next section).

> [!NOTE]
> Uploading through the web interface does not support uploading entire folders with their structure. To upload a folder, you need to use Git push or drag-and-drop individual files. GitHub Desktop is an alternative that supports folder uploads.

---

## Deleting Files on GitHub

**Delete a single file:**

1. Navigate to the file
2. Click the **three dots (...)** menu in the top-right corner of the file view
3. Select **Delete file**
4. Commit the deletion

Or click the **trash icon** if it is visible in the file toolbar.

**Delete a folder:**

GitHub does not have a "delete folder" button. Deleting all files within a folder removes the folder automatically (Git does not track empty directories). To delete a folder, delete all its files one by one through the web interface, or use Git locally:

```bash
git rm -r folder-name/
git commit -m "remove: delete folder-name folder"
git push
```

---

## Committing Directly vs Creating a Branch

Every edit through the GitHub web interface or github.dev ends with a commit. GitHub gives you two options:

### Option 1: Commit directly to the current branch

```
Commit directly to the main branch.
```

The change goes immediately to `main` (or whichever branch you were viewing). No pull request. No review. The change is live immediately.

**Use this when:** You are the sole owner of the repository, working on a personal project, making a trivial documentation fix that needs no review, or on a branch that is already your working branch.

### Option 2: Create a new branch and start a pull request

```
Create a new branch for this commit and start a pull request.
Branch name: YOUR_USERNAME-patch-1
```

GitHub creates a new branch, commits your change there and opens the pull request form. You can rename the branch to something more descriptive.

**Use this when:** You are contributing to a shared repository, the branch has protection rules requiring pull requests, you want your change reviewed before it goes live, or you are contributing to someone else's repository (including forked repositories).

> [!IMPORTANT]
> In repositories with branch protection rules on `main`, the "commit directly" option may be greyed out or unavailable. You will always need to use the pull request route.

---

## github.dev - VS Code in the Browser

**github.dev** is a full VS Code interface running entirely in your browser. It gives you multi-file editing, source control integration, GitHub Copilot support and the ability to install web-compatible extensions - without any local installation.

**How to access:**

- Press `.` (period) on any GitHub repository page
- Change `github.com` to `github.dev` in the URL: `github.dev/OWNER/REPO`
- Press `.` on a specific pull request to review and edit its files

**What github.dev provides:**

- Full VS Code interface with the familiar layout
- Multi-file editing with tabs
- File explorer sidebar
- Source Control panel (Stage, commit, push, create branches)
- Search across the entire repository
- GitHub Copilot (if enabled on your account)
- Settings Sync (your VS Code settings carry over)
- Web-compatible extensions (GitHub Pull Requests, GitHub Copilot, GitLens Inspect and many more)

**What github.dev does NOT provide:**

- A terminal (no shell access)
- Code execution (you cannot run Python, Node, tests etc.)
- Debugging
- Most non-web VS Code extensions (anything that requires native binaries)
- Live preview for web applications

**Committing from github.dev:**

1. Make your changes across as many files as needed
2. Click the **Source Control** icon in the left sidebar (or press `Ctrl+Shift+G` / `Cmd+Shift+G`)
3. Stage changes by clicking the `+` next to each file or staging all at once
4. Type a commit message
5. Click the **checkmark** to commit
6. Click **Publish changes** or **Push** to push to the remote

You can create branches, switch branches and manage pull requests from within github.dev using the Source Control panel and the GitHub Pull Requests extension.

**Opening a pull request for review in github.dev:**

When reviewing a pull request, press `.` on the PR page. github.dev opens with the PR's changed files loaded. You can read the diff, leave comments and approve or request changes - all from the browser with full syntax highlighting and file context.

---

## GitHub Codespaces

**GitHub Codespaces** is a full cloud development environment. Unlike github.dev which is purely a web-based editor, Codespaces provisions an actual Linux virtual machine running VS Code Server. You get a complete development environment with a terminal, all VS Code extensions and the ability to run, build, test and debug your code.

**How to open a Codespace:**

1. Click the green **Code** button on any repository
2. Select the **Codespaces** tab
3. Click **Create codespace on main** (or choose a branch)

Or via CLI:

```bash
gh codespace create --repo OWNER/REPO
gh codespace code   # open most recent codespace in VS Code
```

**What Codespaces provides:**

- Full Linux terminal (Ubuntu by default)
- All VS Code extensions (not limited to web extensions)
- Port forwarding (access running web servers from your browser)
- Git pre-configured and authenticated
- Persistent storage across sessions
- Pre-built environments (fast startup via prebuilds)
- Customisable dev containers

**Dev containers:**

The environment is configured via a `.devcontainer/devcontainer.json` file in the repository. This specifies the base Docker image, installed tools, VS Code extensions, environment variables and startup commands. When a Codespace starts, it uses this configuration to build your environment automatically.

Example `.devcontainer/devcontainer.json`:

```json
{
  "name": "Node.js Project",
  "image": "mcr.microsoft.com/devcontainers/node:20",
  "features": {},
  "customizations": {
    "vscode": {
      "extensions": [
        "esbenp.prettier-vscode",
        "dbaeumer.vscode-eslint"
      ]
    }
  },
  "postCreateCommand": "npm install"
}
```

**Pricing and free tier:**

| Plan | Free compute per month | Free storage per month |
|---|---|---|
| **GitHub Free** | 120 core-hours (60 hours on 2-core) | 15 GB |
| **GitHub Pro / Student** | 180 core-hours (90 hours on 2-core) | 20 GB |

Beyond the free tier, compute is billed at:

| Machine size | Price per hour |
|---|---|
| 2-core (4 GB RAM) | $0.18 |
| 4-core (8 GB RAM) | $0.36 |
| 8-core (16 GB RAM) | $0.72 |
| 16-core (32 GB RAM) | $1.44 |
| 32-core (64 GB RAM) | $2.88 |

Storage is billed at $0.07/GB/month beyond the free tier.

> [!TIP]
> **Stop your Codespace when not actively using it.** A running Codespace consumes compute hours even if you are not typing. Go to [github.com/codespaces](https://github.com/codespaces) to see all your active Codespaces and stop any that are running. Stopped Codespaces only consume storage, not compute.

**Managing Codespaces:**

- View all Codespaces: [github.com/codespaces](https://github.com/codespaces)
- Stop a Codespace: From the list or the Command Palette inside the Codespace (`Ctrl+Shift+P` → "Codespaces: Stop Current Codespace")
- Delete a Codespace: From [github.com/codespaces](https://github.com/codespaces) → three dots menu → Delete
- Change machine type: From [github.com/codespaces](https://github.com/codespaces) → three dots → Change machine type

**Codespaces vs github.dev:**

| Feature | github.dev | Codespaces |
|---|---|---|
| Terminal | No | Yes |
| Run code | No | Yes |
| Debug | No | Yes |
| All extensions | No (web only) | Yes |
| Port forwarding | No | Yes |
| Cost | Free | Free tier + paid |
| Startup time | Instant | 30-60 seconds (instant with prebuilds) |

---

## Choosing the Right Tool

| Situation | Use |
|---|---|
| Fix a typo in one file | Web editor (pencil icon) |
| Update documentation across multiple files | github.dev |
| Review a pull request with full file context | github.dev (press `.` on the PR) |
| Build, run and test code | Codespaces |
| You are on a locked-down machine with no Git installed | Codespaces |
| Quick change to README or config | Web editor |
| Explore a repository's structure | github.dev |
| Onboard a new contributor with zero local setup | Codespaces with devcontainer |
| You need a terminal | Codespaces |

---

## Editing via GitHub CLI

For changes you want to make from the terminal without a full local clone, the GitHub CLI offers some useful shortcuts:

**Edit a file and open a pull request from the terminal:**

```bash
# View a file
gh api repos/OWNER/REPO/contents/PATH/TO/FILE --jq '.content' | base64 --decode

# Clone, edit, push and open PR in one flow
gh repo clone OWNER/REPO
cd REPO
# make changes
gh pr create --title "fix: your change" --body "Description"
```

**Browse the repository in the terminal:**

```bash
gh browse           # opens the current repository in the browser
gh browse --branch main
gh browse PATH/TO/FILE
```

---

## Try It Yourself

**Part 1 - Web editor**

**Step 1.** Go to any repository you own on GitHub (use `hello-github` from the previous file if you created it).

**Step 2.** Click on `README.md`.

**Step 3.** Click the **pencil icon** to edit.

**Step 4.** Add a new line: `## About this project` followed by a short description.

**Step 5.** Click **Preview** to see how it renders.

**Step 6.** Scroll down to **Commit changes**. Add a commit message: `docs: add about section to README`.

**Step 7.** Select **Create a new branch** and name it `docs/add-about-section`.

**Step 8.** Click **Propose changes**.

**Step 9.** Review the pull request preview and click **Create pull request**.

**Step 10.** Merge the pull request. Delete the branch after merging.

---

**Part 2 - github.dev**

**Step 1.** Go to your `hello-github` repository (or any repository).

**Step 2.** Press `.` on your keyboard. github.dev opens in the same tab.

**Step 3.** Explore the file tree on the left sidebar.

**Step 4.** Create a new file: click the **New File** icon → name it `CONTRIBUTING.md`.

**Step 5.** Add this content:

```markdown
# Contributing

Contributions are welcome. Please open an issue before submitting a pull request.
```

**Step 6.** Click the **Source Control** icon (left sidebar).

**Step 7.** Stage `CONTRIBUTING.md` by clicking the `+`.

**Step 8.** Type a commit message: `docs: add CONTRIBUTING.md`.

**Step 9.** Click the **checkmark** to commit and push.

**Step 10.** Press `Ctrl+W` or `Cmd+W` to close the tab and return to GitHub.com. Your file should appear in the repository.

---

**Part 3 - Codespaces (optional - uses free tier compute)**

**Step 1.** Go to your repository on GitHub.

**Step 2.** Click the green **Code** button → **Codespaces** tab → **Create codespace on main**.

**Step 3.** Wait for the environment to build (30-60 seconds).

**Step 4.** Open the terminal at the bottom (`Ctrl+backtick` or `Cmd+backtick`).

**Step 5.** Run:

```bash
git log --oneline
ls -la
```

**Step 6.** Make a change to a file via the editor.

**Step 7.** In the terminal:

```bash
git add .
git commit -m "test: codespace commit"
git push
```

**Step 8.** When finished, open the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) → type "Stop" → **Codespaces: Stop Current Codespace**.

---

## Common Mistakes

**Making large changes with the web editor when github.dev would be better.**

The web editor is one file at a time. If your change touches multiple files, open github.dev by pressing `.` first. Editing multiple files in the web editor means multiple separate commits, which makes your change harder to review.

**Committing directly to `main` in shared repositories.**

The web editor's default option is to commit directly to the current branch. In shared repositories, this bypasses review. Always choose "Create a new branch" when editing shared repositories.

**Not stopping Codespaces when finished.**

A running Codespace consumes compute hours from your free allowance even when idle. Always stop it when you are done working. Forgotten Codespaces can exhaust your monthly free tier within days.

**Using github.dev when you actually need to run code.**

github.dev has no terminal and cannot execute code. If you need to run tests, start a development server or debug, use Codespaces instead.

**Uploading files over 25 MB through the web interface.**

The browser upload limit is 25 MB per file. For larger files, use Git push from a local clone or Git LFS for binary files.

**Expecting all VS Code extensions to work in github.dev.**

github.dev only supports extensions that run in a web context. Extensions that require native binaries (debuggers, language servers for compiled languages) will not work. Use Codespaces for full extension support.

---

## Summary

- The **web editor** (pencil icon) edits one file at a time in the browser with no setup required
- **github.dev** (press `.` on any repo) provides a full VS Code interface in the browser for multi-file editing without a terminal
- **GitHub Codespaces** provides a full cloud development environment with terminal, all extensions, port forwarding and code execution
- When committing from the web, choose "Create a new branch" in shared repositories to trigger a review workflow
- Create files on GitHub via **Add file → Create new file**, including nested paths using forward slashes
- Upload files via **Add file → Upload files** with a 25 MB per file browser limit
- Delete files via the three-dot menu on the file view
- Stop Codespaces when not in use to preserve your free compute allowance
- github.dev is instant and free; Codespaces has a free tier but costs compute hours beyond that

---

## Sources and Further Reading

- [github.dev documentation](https://docs.github.com/en/codespaces/the-githubdev-web-based-editor) - official guide to the github.dev web editor
- [GitHub Codespaces documentation](https://docs.github.com/en/codespaces) - complete Codespaces reference
- [Dev containers documentation](https://containers.dev) - the open standard for development container configuration
- [GitHub Codespaces billing](https://docs.github.com/en/billing/managing-billing-for-github-codespaces/about-billing-for-github-codespaces) - pricing and free tier details
- [Editing files on GitHub](https://docs.github.com/en/repositories/working-with-files/managing-files/editing-files) - official guide to the web editor

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

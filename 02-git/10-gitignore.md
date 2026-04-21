# .gitignore

**Difficulty:** 🟡 Intermediate | **Time:** 20 minutes

A `.gitignore` file tells Git which files and folders to ignore - to never track, never stage and never commit. Getting this right from the start of a project is one of the most important habits you can build. It keeps your repository clean, prevents accidental exposure of sensitive data and stops irrelevant files cluttering your commit history.

---

## Table of Contents

- [What is .gitignore?](#what-is-gitignore)
- [Creating a .gitignore file](#creating-a-gitignore-file)
- [Syntax and patterns](#syntax-and-patterns)
- [Common patterns by language and tool](#common-patterns-by-language-and-tool)
- [Security - what you must always ignore](#security---what-you-must-always-ignore)
- [The global .gitignore](#the-global-gitignore)
- [The .gitignore for this course](#the-gitignore-for-this-course)
- [Ignoring files that are already tracked](#ignoring-files-that-are-already-tracked)
- [Checking why a file is ignored](#checking-why-a-file-is-ignored)
- [The .gitkeep convention](#the-gitkeep-convention)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is .gitignore?

`.gitignore` is a plain text file you place in your repository that lists files and patterns Git should not track. Any file matching a pattern in `.gitignore` will:

- Not appear in `git status` as an untracked file
- Not be staged by `git add .`
- Not be committed

The `.gitignore` file itself is committed to the repository so that everyone working on the project ignores the same files.

**Common things to ignore:**

- Compiled output and build artefacts (`*.exe`, `*.o`, `dist/`, `build/`)
- Dependency folders (`node_modules/`, `vendor/`, `.venv/`)
- IDE and editor configuration files (`.vscode/`, `.idea/`, `*.suo`)
- Operating system files (`.DS_Store` on Mac, `Thumbs.db` on Windows)
- Log files (`*.log`, `logs/`)
- Environment files with credentials (`.env`, `*.pem`, `*.key`)
- Cache and temporary files (`*.tmp`, `__pycache__/`, `.cache/`)

**What not to ignore:**

- Source code
- Configuration files that are part of the project (not the ones with secrets)
- The `.gitignore` file itself
- Documentation

---

## Creating a .gitignore File

Create `.gitignore` in the root of your repository. It must be named exactly `.gitignore` with a dot at the start and no file extension.

**Windows (PowerShell):**

```powershell
New-Item .gitignore -ItemType File
```

Or simply create a new file in VS Code named `.gitignore`.

**Mac and Linux:**

```bash
touch .gitignore
```

Then open it and add your patterns. Commit it with your first commit:

```bash
git add .gitignore
git commit -m "add: .gitignore"
```

> [!TIP]
> Add your `.gitignore` as the very first thing you do in a new project, before any other files. This prevents you from accidentally committing files you meant to ignore. Removing a committed file from history later is much harder than ignoring it from the start.

### Generating a .gitignore automatically

You do not need to write your `.gitignore` from scratch. Several tools generate them based on your project type:

**gitignore.io (also at toptal.com/developers/gitignore):**

Go to the website, type your languages and tools (e.g. `Python`, `Node`, `macOS`, `Windows`, `VisualStudioCode`) and it generates a complete `.gitignore`. This is the fastest approach.

**GitHub's templates:**

When creating a repository on GitHub, select a language from the "Add .gitignore" dropdown. GitHub uses the official templates from [github.com/github/gitignore](https://github.com/github/gitignore).

**VS Code extension:**

The `.gitignore Generator` extension (by Piotr Palarz) generates `.gitignore` files from within VS Code.

---

## Syntax and Patterns

`.gitignore` uses a simple pattern language. Each line is a pattern. Blank lines and lines starting with `#` are ignored.

### Basic patterns

**Ignore a specific file:**

```
secrets.txt
```

**Ignore all files with a specific extension:**

```
*.log
*.tmp
*.pyc
```

**Ignore a specific folder:**

```
node_modules/
build/
dist/
```

The trailing `/` tells Git this is a directory. Without it, Git matches both files and directories with that name.

**Ignore a file in a specific folder:**

```
config/database.yml
```

**Ignore all files in a specific folder but not the folder itself:**

```
logs/*
```

### Wildcards

`*` matches anything except a `/`

```
*.log          # ignores all .log files anywhere
build/*.js     # ignores .js files directly in build/ but not subdirectories
```

`**` matches anything including `/` (matches across directories)

```
**/logs        # ignores any folder named logs anywhere in the tree
**/*.log       # ignores all .log files anywhere in the tree
src/**/*.test  # ignores all .test files anywhere under src/
```

`?` matches any single character except `/`

```
config?.yml    # matches config1.yml, configA.yml but not config.yml
```

### Negation

A `!` prefix negates a pattern - it un-ignores files that would otherwise be ignored:

```
*.log
!important.log
```

This ignores all `.log` files except `important.log`.

**Important limitation:** You cannot un-ignore a file if its parent directory is ignored. If `build/` is ignored, adding `!build/output.txt` will not work. You need to un-ignore the parent directory first:

```
build/
!build/
build/*
!build/output.txt
```

Or restructure to not ignore the parent.

### Comments

Lines starting with `#` are comments:

```
# Dependency directories
node_modules/
vendor/

# Build output
dist/
build/
```

### Escaping special characters

If you need to ignore a file whose name starts with `#` or `!`, escape it with a backslash:

```
\#special-file.txt
\!not-a-negation.txt
```

---

## Common Patterns by Language and Tool

### Python

```gitignore
# Byte-compiled files
__pycache__/
*.py[cod]
*$py.class
*.pyc

# Virtual environments
.env
.venv
env/
venv/

# Distribution and packaging
dist/
build/
*.egg-info/
*.egg

# Testing
.pytest_cache/
.coverage
htmlcov/

# Jupyter Notebooks checkpoints
.ipynb_checkpoints/

# mypy
.mypy_cache/
```

### Node.js / JavaScript / TypeScript

```gitignore
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Build output
dist/
build/
.next/
out/

# Environment variables
.env
.env.local
.env.development.local
.env.test.local
.env.production.local

# Testing
coverage/

# Cache
.cache/
.parcel-cache/
.eslintcache
```

### Java

```gitignore
# Compiled output
*.class
*.jar
*.war
*.ear

# Build tools
target/
build/
.gradle/
gradle/

# IDE files
*.iml
.idea/
```

### C and C++

```gitignore
# Compiled output
*.o
*.obj
*.exe
*.out
*.app
*.so
*.dll
*.dylib

# Build directories
build/
cmake-build-*/
```

### macOS

```gitignore
.DS_Store
.AppleDouble
.LSOverride
._*
.Spotlight-V100
.Trashes
```

### Windows

```gitignore
Thumbs.db
Thumbs.db:encryptable
ehthumbs.db
Desktop.ini
$RECYCLE.BIN/
*.lnk
```

### VS Code

```gitignore
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
!.vscode/*.code-snippets
```

This ignores most VS Code folder contents but keeps the shared project settings that teammates should use.

### JetBrains IDEs

```gitignore
.idea/
*.iws
*.iml
*.ipr
out/
```

---

## Security - What You Must Always Ignore

This section is more important than all the others combined. Accidental exposure of credentials is one of the most common and damaging mistakes developers make.

> [!CAUTION]
> Bots scan GitHub continuously looking for exposed credentials. Exposed API keys and tokens are typically found and abused within minutes of being pushed to a public repository. Even if you delete the file in a later commit, the credentials remain in the commit history and are still accessible.

**Always add these to your `.gitignore`:**

```gitignore
# Environment variables and secrets
.env
.env.*
!.env.example
*.env

# Private keys and certificates
*.pem
*.key
*.p12
*.pfx
*.crt
*.cer
id_rsa
id_ed25519
*.ppk

# API keys and tokens (common file names)
secrets.json
secrets.yml
secrets.yaml
config/secrets.*
credentials.json

# AWS credentials
.aws/credentials
.aws/config

# Google Cloud credentials
*-credentials.json
service-account*.json

# Firebase
.firebase/
firebase-debug.log

# Terraform state (may contain sensitive outputs)
*.tfstate
*.tfstate.*
.terraform/
```

**Create a `.env.example` file instead of committing `.env`:**

Your `.env` file contains real credentials. Commit a `.env.example` file instead that shows the structure with placeholder values:

```
# .env.example - copy this to .env and fill in your values
DATABASE_URL=postgresql://user:password@localhost:5432/dbname
API_KEY=your_api_key_here
SECRET_KEY=your_secret_key_here
```

This tells other developers what environment variables they need to set without exposing real values. The `!.env.example` pattern in your `.gitignore` ensures `.env.example` is tracked even if you have `*.env` ignored.

> [!CAUTION]
> If you accidentally commit a file containing real credentials:
> 1. Immediately revoke and rotate the exposed credential on the service it belongs to
> 2. Remove it from history using `git filter-repo`
> 3. Force push all branches
> 4. Assume the credential is compromised regardless - it may already have been seen

---

## The Global .gitignore

A global `.gitignore` applies to every repository on your machine, not just one project. Use it for OS files and IDE files that you always want to ignore regardless of the project.

**Set up a global `.gitignore`:**

**Windows (PowerShell):**

```powershell
git config --global core.excludesFile "$env:USERPROFILE\.gitignore_global"
New-Item "$env:USERPROFILE\.gitignore_global" -ItemType File
```

**Mac and Linux:**

```bash
git config --global core.excludesFile ~/.gitignore_global
touch ~/.gitignore_global
```

Then open `~/.gitignore_global` (or `%USERPROFILE%\.gitignore_global` on Windows) and add OS and IDE patterns:

```gitignore
# macOS
.DS_Store
.AppleDouble

# Windows
Thumbs.db
Desktop.ini

# VS Code (personal settings only - shared settings go in project .gitignore)
.vscode/settings.json

# JetBrains
.idea/

# Vim swap files
*.swp
*.swo

# Temporary files
*.tmp
*.bak
```

> [!NOTE]
> Put OS and personal IDE files in the global `.gitignore`, not the project `.gitignore`. The project `.gitignore` is committed and shared with everyone. Not everyone on your team uses the same OS or IDE. A `.DS_Store` entry in the project `.gitignore` is fine but is cleaner in the global file.

---

## The .gitignore for this Course

For a markdown-based course like git-unlocked, a simple `.gitignore` is sufficient:

```gitignore
# macOS
.DS_Store

# Windows
Thumbs.db
Desktop.ini

# VS Code
.vscode/*
!.vscode/settings.json
!.vscode/extensions.json

# JetBrains
.idea/

# Temporary files
*.tmp
*.bak

# Node (if any tooling is added)
node_modules/
```

---

## Ignoring Files That Are Already Tracked

If you add a pattern to `.gitignore` for a file that Git is already tracking, Git will continue tracking it. `.gitignore` only prevents untracked files from being tracked - it cannot retroactively untrack files.

To stop tracking a file that is already committed:

```bash
git rm --cached filename.txt
```

This removes the file from Git's tracking (the index) without deleting it from your working directory. After this, the file will be ignored by Git going forward.

To stop tracking an entire folder:

```bash
git rm --cached -r foldername/
```

Then commit the removal:

```bash
git commit -m "remove: stop tracking filename.txt (added to .gitignore)"
```

> [!WARNING]
> `git rm --cached` removes the file from the repository's history going forward, but the file remains in all previous commits. If the file contained sensitive data, removing it from tracking is not enough - you need to rewrite the history using `git filter-repo`. This is covered in [git/25-git-internals-advanced.md](25-git-internals-advanced.md).

---

## Checking Why a File is Ignored

**Check whether a specific file is ignored and why:**

```bash
git check-ignore -v filename.txt
```

Output shows the line in the `.gitignore` file responsible:

```
.gitignore:5:*.txt      filename.txt
```

This means line 5 of `.gitignore` with the pattern `*.txt` is causing `filename.txt` to be ignored.

**List all ignored files:**

```bash
git ls-files --ignored --exclude-standard
```

**Check the status of ignored files:**

```bash
git status --ignored
```

This shows ignored files in addition to the normal status output.

---

## The .gitkeep Convention

Git does not track empty directories. If you create an empty folder, Git ignores it completely and it will not appear in the repository.

A common workaround is to place a file called `.gitkeep` inside the empty directory. This file has no content and no special meaning to Git - it is just a placeholder that ensures the directory is committed.

**Create a `.gitkeep` file:**

**Windows (PowerShell):**

```powershell
New-Item logs/.gitkeep -ItemType File -Force
```

**Mac and Linux:**

```bash
mkdir -p logs && touch logs/.gitkeep
```

Then ignore the actual contents of the directory while keeping the folder:

```gitignore
logs/*
!logs/.gitkeep
```

This is exactly how the git-unlocked repository was set up initially - every empty folder received a `.gitkeep` file so the structure was preserved in the repository before any content was added.

> [!NOTE]
> `.gitkeep` is a widely-used convention but it is not an official Git feature. Some projects use `.githold` or `.keep` instead. Any filename works as long as you are consistent.

---

## Try It Yourself

**Step 1.** Create a project directory:

**Windows (PowerShell):**

```powershell
mkdir gitignore-practice && cd gitignore-practice && git init
```

**Mac and Linux:**

```bash
mkdir gitignore-practice && cd gitignore-practice && git init
```

**Step 2.** Create some files to simulate a project:

**Windows:**

```powershell
echo "print('hello')" > app.py
echo "DATABASE_URL=secret123" > .env
echo "" > error.log
mkdir __pycache__
echo "" > __pycache__/app.cpython-311.pyc
```

**Mac and Linux:**

```bash
echo "print('hello')" > app.py
echo "DATABASE_URL=secret123" > .env
echo "" > error.log
mkdir __pycache__
echo "" > __pycache__/app.cpython-311.pyc
```

**Step 3.** Check the status - all files appear as untracked:

```bash
git status
```

**Step 4.** Create a `.gitignore` file:

**Windows:**

```powershell
New-Item .gitignore -ItemType File
```

**Mac and Linux:**

```bash
touch .gitignore
```

**Step 5.** Add patterns to the `.gitignore`:

Open `.gitignore` in your editor and add:

```
.env
*.log
__pycache__/
```

**Step 6.** Check the status again:

```bash
git status
```

Only `app.py` and `.gitignore` should appear now. The `.env`, `error.log` and `__pycache__/` are all ignored.

**Step 7.** Verify why a file is being ignored:

```bash
git check-ignore -v .env
```

**Step 8.** Stage and commit the project files:

```bash
git add .
git commit -m "add: initial project with .gitignore"
```

**Step 9.** Confirm the sensitive file was not committed:

```bash
git show HEAD --name-only
```

Only `app.py` and `.gitignore` should be listed.

**Step 10.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q gitignore-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf gitignore-practice
```

---

## Common Mistakes

**Adding `.gitignore` after already committing files you wanted to ignore.**

Once a file is committed, adding it to `.gitignore` does not stop Git from tracking it. You must run `git rm --cached filename` to untrack it, then commit that change. Add your `.gitignore` before your first commit.

**Committing a `.env` file with real credentials.**

The most common and serious mistake. Add `.env` to `.gitignore` before creating it. If you have already committed it, revoke the exposed credentials immediately and rewrite the history with `git filter-repo`.

**Ignoring the `.gitignore` file itself.**

`.gitignore` should be committed to the repository so everyone on the team uses the same rules. Do not add `.gitignore` to itself.

**Using `*` when you meant `**`.**

`*.log` ignores `.log` files in all directories. But `logs/*` only ignores files directly inside `logs/` - not in subdirectories. Use `logs/**` if you want to ignore everything inside `logs/` recursively.

**Trying to un-ignore a file inside an ignored directory.**

If `build/` is in your `.gitignore`, adding `!build/output.txt` will not work. Git does not look inside ignored directories for negation patterns. Un-ignore the directory first, then selectively re-ignore its contents.

**Putting personal IDE files in the project `.gitignore`.**

The project `.gitignore` is shared with the team. If you add `.idea/` (JetBrains) and a teammate uses VS Code, you are adding a rule they do not need to their environment. Put personal IDE and OS files in your global `.gitignore` instead.

**Forgetting to ignore OS-specific files.**

`.DS_Store` (Mac) and `Thumbs.db` (Windows) files appear constantly and clutter `git status`. Add them to your global `.gitignore` once and never see them again.

---

## Summary

- `.gitignore` is a committed file that tells Git which files and folders never to track
- Common things to ignore: build output, dependency folders, IDE files, OS files, log files and environment files with credentials
- Patterns use `*` for single-level wildcards, `**` for recursive wildcards, `!` for negation and `/` to indicate directories
- Use gitignore.io or GitHub's templates to generate a starting `.gitignore` for any project
- Always ignore `.env` and any file containing real credentials - accidental exposure is one of the most common and serious mistakes in version control
- Set up a global `.gitignore` for OS and personal IDE files
- If a file is already tracked, use `git rm --cached filename` to untrack it, then commit the change
- Use `git check-ignore -v filename` to find out why a file is being ignored
- Use `.gitkeep` placeholder files to track otherwise empty directories

---

## Sources and Further Reading

- [Official gitignore documentation](https://git-scm.com/docs/gitignore) - the complete reference for pattern syntax
- [github/gitignore repository](https://github.com/github/gitignore) - the official collection of `.gitignore` templates for hundreds of languages and tools
- [gitignore.io](https://www.toptal.com/developers/gitignore) - generate `.gitignore` files for any combination of languages, tools and operating systems
- [Pro Git book, Chapter 2.2: Recording Changes](https://git-scm.com/book/en/v2/Git-Basics-Recording-Changes-to-the-Repository#_ignoring) - the official guide to `.gitignore` patterns
- [GitHub - Ignoring files](https://docs.github.com/en/get-started/getting-started-with-git/ignoring-files) - GitHub's guide including global ignore files
- [git filter-repo](https://github.com/newren/git-filter-repo) - the recommended tool for removing sensitive data from Git history

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

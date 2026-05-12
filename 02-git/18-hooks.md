# Git Hooks

**Difficulty:** 🔴 Advanced | **Time:** 25 minutes

Git hooks are scripts that run automatically when specific Git events occur - before a commit, after a push, before a merge and more. They let you automate quality checks, enforce standards and integrate tools directly into your Git workflow without relying on developers to remember to run them manually.

---

## Table of Contents

- [What are Git hooks?](#what-are-git-hooks)
- [Where hooks live](#where-hooks-live)
- [Client-side hooks](#client-side-hooks)
- [Server-side hooks](#server-side-hooks)
- [Creating your first hook](#creating-your-first-hook)
- [Practical hook examples](#practical-hook-examples)
- [Sharing hooks with your team](#sharing-hooks-with-your-team)
- [Husky - hooks for JavaScript projects](#husky---hooks-for-javascript-projects)
- [pre-commit - hooks for Python and beyond](#pre-commit---hooks-for-python-and-beyond)
- [Bypassing hooks](#bypassing-hooks)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What are Git hooks?

A Git hook is a script placed in a specific location that Git executes automatically at a particular point in a Git operation. When you run `git commit`, Git checks whether a script called `pre-commit` exists in `.git/hooks/`. If it does, Git runs it before creating the commit. If the script exits with a non-zero status, the commit is aborted.

Hooks can be written in any scripting language available on the system - Bash, Python, Ruby, Node.js or PowerShell. The file just needs to be executable.

**Common uses:**

- Run linting or formatting checks before committing
- Run tests before pushing
- Enforce commit message format
- Prevent direct commits to protected branches
- Notify a chat system when code is pushed
- Automatically update version numbers

---

## Where Hooks Live

Hooks are stored in `.git/hooks/` in your repository. This directory contains sample hook scripts with `.sample` extensions:

```
.git/hooks/
├── applypatch-msg.sample
├── commit-msg.sample
├── pre-applypatch.sample
├── pre-commit.sample
├── pre-merge-commit.sample
├── pre-push.sample
├── pre-rebase.sample
├── prepare-commit-msg.sample
├── post-commit.sample
├── post-merge.sample
├── post-update.sample
└── update.sample
```

To activate a hook, remove the `.sample` extension and make the file executable. On Windows with Git Bash, executable permissions are handled differently - see the note below.

> [!NOTE]
> The `.git/hooks/` directory is not committed to the repository. This means hooks are local to your machine and are not automatically shared with teammates. Sharing hooks requires an additional approach - covered in [Sharing hooks with your team](#sharing-hooks-with-your-team).

---

## Client-Side Hooks

Client-side hooks run on your local machine in response to operations you perform.

### pre-commit

Runs before Git creates a commit. If it exits with a non-zero status, the commit is aborted. Used for linting, formatting, running quick tests or blocking commits that do not meet standards.

Receives no arguments. Cannot modify the commit.

### prepare-commit-msg

Runs before the commit message editor opens, after the default message is created. Can modify the commit message template. Receives the path to the file containing the commit message as an argument.

Used for automatically prepending a ticket number to commit messages, or inserting a template.

### commit-msg

Runs after the developer writes the commit message but before the commit is created. Receives the path to the file containing the commit message. If it exits non-zero, the commit is aborted.

Used for enforcing commit message format - for example, requiring Conventional Commits format or a ticket number.

### post-commit

Runs after the commit is created. Cannot affect the commit. Used for notifications - for example, sending a desktop notification when a commit is made.

### pre-push

Runs before `git push` sends data to the remote. Receives the remote name and URL. If it exits non-zero, the push is aborted.

Used for running the full test suite before allowing a push, or preventing force pushes to protected branches.

### pre-rebase

Runs before a rebase starts. Can abort the rebase by exiting non-zero.

### post-merge

Runs after a successful `git merge`. Used for tasks like reinstalling dependencies when `package.json` or `requirements.txt` has changed.

### post-checkout

Runs after `git checkout` or `git switch`. Used for setting up the environment for the checked-out branch.

---

## Server-Side Hooks

Server-side hooks run on the remote repository (GitHub, GitLab, your own Git server) in response to network operations. You cannot add these to GitHub or GitLab directly - they provide their own equivalents (GitHub Actions, GitLab CI/CD, webhooks).

If you are running your own Git server, the server-side hooks are:

**pre-receive** - runs when the server receives a push, before updating any references. Can reject a push entirely.

**update** - runs once per branch being updated. Can reject pushes to specific branches.

**post-receive** - runs after a push completes. Used for notifications, deployments and CI triggers.

---

## Creating Your First Hook

**Step 1.** Navigate to your repository's hooks directory:

**Windows (PowerShell):**

```powershell
cd .git/hooks
```

**Mac and Linux:**

```bash
cd .git/hooks
```

**Step 2.** Create a `pre-commit` file:

**Windows (Git Bash):**

```bash
touch pre-commit
```

**Mac and Linux:**

```bash
touch pre-commit
chmod +x pre-commit
```

**Step 3.** Open the file and add your script. Here is a simple example that prevents committing if any file contains `TODO: REMOVE BEFORE COMMIT`:

**Bash (Mac, Linux, Git Bash on Windows):**

```bash
#!/bin/bash

if git diff --cached | grep -q "TODO: REMOVE BEFORE COMMIT"; then
    echo "Error: commit contains TODO: REMOVE BEFORE COMMIT"
    echo "Remove the flagged lines before committing."
    exit 1
fi

exit 0
```

**Step 4.** Make it executable (Mac and Linux only - Git Bash on Windows handles this differently):

```bash
chmod +x .git/hooks/pre-commit
```

**Step 5.** Test it - stage a file containing the forbidden text and try to commit. The commit should be rejected.

> [!NOTE]
> **Windows users:** Git Bash is the easiest way to use Bash hooks on Windows. PowerShell hooks are also possible - save the file without an extension and use a `#!/usr/bin/env pwsh` shebang, but this requires PowerShell Core to be on the PATH. The simplest approach on Windows is to use a hook management tool like Husky or pre-commit which handle the cross-platform complexity.

---

## Practical Hook Examples

### Enforce commit message format (commit-msg)

```bash
#!/bin/bash

commit_msg=$(cat "$1")
pattern="^(add|fix|update|remove|rename|refactor|docs|style|test|chore): .+"

if ! echo "$commit_msg" | grep -qE "$pattern"; then
    echo "Error: commit message does not follow Conventional Commits format."
    echo "Expected format: type: description"
    echo "Valid types: add, fix, update, remove, rename, refactor, docs, style, test, chore"
    echo "Your message: $commit_msg"
    exit 1
fi

exit 0
```

### Run tests before pushing (pre-push)

```bash
#!/bin/bash

echo "Running tests before push..."

npm test

if [ $? -ne 0 ]; then
    echo "Tests failed. Push aborted."
    exit 1
fi

echo "All tests passed."
exit 0
```

### Prevent direct commits to main (pre-commit)

```bash
#!/bin/bash

branch=$(git symbolic-ref HEAD 2>/dev/null | sed 's|refs/heads/||')

if [ "$branch" = "main" ] || [ "$branch" = "master" ]; then
    echo "Error: direct commits to $branch are not allowed."
    echo "Create a feature branch and open a pull request."
    exit 1
fi

exit 0
```

### Auto-format Python files on commit (pre-commit)

```bash
#!/bin/bash

staged_python=$(git diff --cached --name-only --diff-filter=ACM | grep '\.py$')

if [ -n "$staged_python" ]; then
    echo "Formatting Python files with black..."
    echo "$staged_python" | xargs black
    echo "$staged_python" | xargs git add
fi

exit 0
```

### Remind about dependency installation after merge (post-merge)

```bash
#!/bin/bash

changed=$(git diff-tree -r --name-only --no-commit-id ORIG_HEAD HEAD)

if echo "$changed" | grep -q "package.json"; then
    echo "package.json changed - run 'npm install' to update dependencies."
fi

if echo "$changed" | grep -q "requirements.txt"; then
    echo "requirements.txt changed - run 'pip install -r requirements.txt' to update dependencies."
fi
```

---

## Sharing Hooks with Your Team

Because `.git/hooks/` is not committed, hooks are not automatically shared. Three approaches:

**Option 1 - A tracked hooks directory with a setup script.**

Create a `hooks/` directory at the root of your repository, put your hook scripts there and commit it. Add a setup script (`scripts/install-hooks.sh`) that symlinks or copies the hooks into `.git/hooks/`:

```bash
#!/bin/bash
cp hooks/pre-commit .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
echo "Hooks installed."
```

Document in `CONTRIBUTING.md` that developers should run this script after cloning.

**Option 2 - Configure a custom hooks path (Git 2.9+):**

```bash
git config core.hooksPath hooks/
```

Or set it globally:

```bash
git config --global core.hooksPath ~/.git-hooks/
```

This tells Git to look for hooks in the specified directory instead of `.git/hooks/`. Commit the `hooks/` directory. Every developer on the project needs to run this config command - document it in `CONTRIBUTING.md` or your setup script.

**Option 3 - Use a hook management tool** (see next sections).

---

## Husky - Hooks for JavaScript Projects

Husky is the most popular hook management tool for JavaScript and TypeScript projects. It integrates with npm scripts, stores hooks in a tracked directory and installs them automatically when developers run `npm install`.

**Install:**

```bash
npm install --save-dev husky
npx husky init
```

This creates a `.husky/` directory and configures npm to run `husky` on `npm install`.

**Add a pre-commit hook:**

```bash
echo "npm test" > .husky/pre-commit
```

**Add a commit-msg hook to enforce Conventional Commits:**

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional
echo "npx --no -- commitlint --edit \$1" > .husky/commit-msg
```

The `.husky/` directory is committed to the repository. When any team member runs `npm install`, hooks are installed automatically.

---

## pre-commit - Hooks for Python and Beyond

The `pre-commit` framework is a popular cross-language hook management tool. It defines hooks in a `.pre-commit-config.yaml` file, automatically installs the required tools in isolated environments and runs checks before commits.

**Install:**

```bash
pip install pre-commit
```

**Create `.pre-commit-config.yaml`:**

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: https://github.com/psf/black
    rev: 24.1.1
    hooks:
      - id: black

  - repo: https://github.com/pycqa/flake8
    rev: 7.0.0
    hooks:
      - id: flake8
```

**Install the hooks:**

```bash
pre-commit install
```

**Run against all files manually:**

```bash
pre-commit run --all-files
```

Commit `.pre-commit-config.yaml` to the repository. Team members install the hooks with `pre-commit install` after cloning.

---

## Bypassing Hooks

Hooks can be bypassed when necessary. Use this sparingly - bypassing a hook defeats its purpose.

**Skip all hooks for a single commit:**

```bash
git commit --no-verify -m "wip: emergency fix, bypassing hooks"
```

**Skip hooks for a push:**

```bash
git push --no-verify
```

> [!WARNING]
> Bypassing hooks too frequently undermines their value. If a hook is being bypassed regularly, the hook is probably too strict, too slow or incorrectly configured. Fix the hook rather than bypassing it repeatedly.

---

## Try It Yourself

**Step 1.** Create a repository:

**Windows (PowerShell):**

```powershell
mkdir hooks-practice && cd hooks-practice && git init
echo "# Hooks Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Mac and Linux:**

```bash
mkdir hooks-practice && cd hooks-practice && git init
echo "# Hooks Practice" > README.md
git add README.md && git commit -m "add: initial README"
```

**Step 2.** Create a pre-commit hook that checks for debug statements:

**Windows (Git Bash):**

```bash
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
if git diff --cached | grep -q "console.log\|debugger\|print('debug')\|breakpoint()"; then
    echo "Error: debug statements found in staged files."
    echo "Remove them before committing."
    exit 1
fi
exit 0
EOF
chmod +x .git/hooks/pre-commit
```

**Mac and Linux:**

```bash
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
if git diff --cached | grep -q "console.log\|debugger\|print('debug')\|breakpoint()"; then
    echo "Error: debug statements found in staged files."
    echo "Remove them before committing."
    exit 1
fi
exit 0
EOF
chmod +x .git/hooks/pre-commit
```

**Step 3.** Try committing a file with a debug statement:

**Windows:**

```powershell
echo "console.log('debug')" > app.js
git add app.js
git commit -m "add: app"
```

**Mac and Linux:**

```bash
echo "console.log('debug')" > app.js
git add app.js
git commit -m "add: app"
```

The commit should be rejected.

**Step 4.** Fix the file and try again:

**Windows:**

```powershell
echo "// clean code" > app.js
git add app.js
git commit -m "add: app"
```

**Mac and Linux:**

```bash
echo "// clean code" > app.js
git add app.js
git commit -m "add: app"
```

This commit should succeed.

**Step 5.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q hooks-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf hooks-practice
```

---

## Common Mistakes

**Writing hooks that are too slow.**

A `pre-commit` hook that runs the entire test suite will be bypassed immediately by anyone who values their time. Keep pre-commit hooks fast - linting and formatting checks are fine, full test suites belong in `pre-push` or better yet in CI.

**Not making hook files executable.**

On Mac and Linux, a hook script that is not executable is silently ignored. Always run `chmod +x .git/hooks/hookname` after creating a hook. This is a very common reason hooks appear to "not work".

**Assuming hooks run on all operating systems the same way.**

Bash hooks do not work natively in Windows Command Prompt or PowerShell. Use Git Bash on Windows, or use a cross-platform tool like Husky or pre-commit.

**Not documenting hook setup in CONTRIBUTING.md.**

Because hooks are not committed automatically, new team members will not have them after cloning. Document the setup process clearly.

**Bypassing hooks habitually.**

If `--no-verify` appears in your commit history regularly, the hooks are not doing their job. Review and fix the hooks, not the habit.

---

## Summary

- Git hooks are scripts in `.git/hooks/` that run automatically at specific Git events
- Client-side hooks include `pre-commit`, `commit-msg`, `pre-push`, `post-merge` and others
- Create a hook by adding an executable script (no `.sample` extension) to `.git/hooks/`
- A hook that exits with a non-zero status aborts the Git operation
- Hooks are not committed with the repository - share them via a tracked directory with `core.hooksPath` or a management tool
- **Husky** is the standard hook manager for JavaScript projects
- **pre-commit** is a popular cross-language hook framework
- Bypass hooks with `--no-verify` when necessary, but use sparingly

---

## Sources and Further Reading

- [Official git hooks documentation](https://git-scm.com/docs/githooks) - the complete reference for all available hooks
- [Pro Git book, Chapter 8.3: Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) - the official guide to hooks with examples
- [Husky](https://typicode.github.io/husky/) - the most popular hook manager for JavaScript projects
- [pre-commit framework](https://pre-commit.com) - cross-language hook management with a large library of ready-made hooks
- [Atlassian - Git hooks](https://www.atlassian.com/git/tutorials/git-hooks) - practical examples of hooks for common use cases

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

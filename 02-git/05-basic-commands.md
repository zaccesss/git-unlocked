# Basic Commands

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

This file covers the Git commands you will use every single day. Learn these ten commands well and you can handle the majority of real-world Git work. Everything else builds on top of them.

Run every command in your terminal - Command Prompt, PowerShell or Git Bash on Windows, Terminal on Mac, or your terminal of choice on Linux.

---

## Table of Contents

- [Before you start](#before-you-start)
- [git init](#git-init)
- [git clone](#git-clone)
- [git status](#git-status)
- [git add](#git-add)
- [git commit](#git-commit)
- [git log](#git-log)
- [git diff](#git-diff)
- [git push](#git-push)
- [git pull](#git-pull)
- [git switch](#git-switch)
- [Putting it all together](#putting-it-all-together)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Before You Start

Make sure you have completed the setup in [Setting Up](../01-introduction/03-setting-up.md). Specifically you need:

- Git installed (`git --version` should return 2.28 or higher)
- Your name and email configured (`git config --global user.name` and `git config --global user.email`)

If either of those is not set up, go back and complete the setup guide first. Every commit you make will use your name and email - you want those to be correct from the start.

---

## git init

`git init` turns a regular folder into a Git repository. It creates the hidden `.git` folder that stores all of Git's data for that project.

**Syntax:**

```bash
git init
```

**Or create and initialise a new folder in one step:**

```bash
git init project-name
```

**Example - starting a new project:**

**Windows (PowerShell):**

```powershell
mkdir my-project
cd my-project
git init
```

**Mac and Linux:**

```bash
mkdir my-project && cd my-project && git init
```

**Output:**

```
Initialized empty Git repository in /path/to/my-project/.git/
```

After running `git init`, your folder now contains a hidden `.git` subfolder. Git is tracking this directory. You can verify this at any time by running `git status`.

> [!NOTE]
> `git init` only needs to be run once per project, at the very beginning. If you are joining an existing project, you will use `git clone` instead.

> [!CAUTION]
> Never run `git init` inside an existing Git repository or inside a folder that is already inside a Git repository. This creates a nested repository which causes confusing behaviour. If you accidentally do this, delete the inner `.git` folder.

---

## git clone

`git clone` downloads a complete copy of a remote repository to your local machine - including all files, all branches and the entire commit history.

**Syntax:**

```bash
git clone <url>
```

**Clone into a folder with the repository's name (most common):**

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
```

This creates a folder called `git-unlocked` in your current directory.

**Clone into a specific folder name:**

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git my-folder-name
```

**Clone using SSH (if you have SSH set up):**

```bash
git clone git@github.com:YOUR_USERNAME/YOUR_REPO.git
```

> [!TIP]
> You can find the clone URL for any GitHub repository by clicking the green **Code** button on the repository page. It shows both HTTPS and SSH options. Use HTTPS if you are not sure which to pick.

**What `git clone` does:**

1. Downloads the entire repository
2. Creates the folder and checks out the default branch (usually `main`)
3. Sets up `origin` as a remote pointing to the URL you cloned from

You do not need to run `git init` after cloning - the repository is already fully initialised.

---

## git status

`git status` shows you the current state of your working directory and staging area. It is the most useful command for understanding what is happening in your repository at any moment.

**Syntax:**

```bash
git status
```

Run this constantly. Before `git add`. After `git add`. Before `git commit`. It is completely safe - it never changes anything, it only reports what it sees.

**Example output on a clean repository:**

```
On branch main
Your branch is up to date with 'origin/main'.

nothing to commit, working tree clean
```

**Example output with untracked and modified files:**

```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        notes.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

**Reading the output:**

- **Changes not staged for commit** - files Git is tracking that have been modified but not yet staged
- **Changes to be committed** - files in the staging area, ready for the next commit
- **Untracked files** - files in the working directory that Git has never seen before and is not tracking

**Short status (compact view):**

```bash
git status -s
```

Output uses two-character codes: `M` for modified, `A` for added to staging, `??` for untracked.

---

## git add

`git add` moves changes from your working directory into the staging area. It tells Git which changes to include in the next commit.

**Add a specific file:**

```bash
git add README.md
```

**Add multiple specific files:**

```bash
git add README.md index.html style.css
```

**Add all changes in the current directory and subdirectories:**

```bash
git add .
```

**Add all changes in the entire repository (from any subfolder):**

```bash
git add -A
```

**Add parts of a file interactively (stage specific lines, not the whole file):**

```bash
git add -p README.md
```

This opens an interactive prompt for each change in the file, letting you choose which individual changes to stage. Useful when you have made several unrelated changes to the same file and want to commit them separately.

> [!WARNING]
> Before running `git add .`, always run `git status` first to see exactly what will be staged. It is easy to accidentally stage files you did not intend to commit - log files, compiled output, files with passwords or half-finished work.

> [!TIP]
> `git add .` stages changes to tracked files AND new untracked files. `git add -u` stages only changes to already-tracked files, ignoring new files. This distinction matters when you want to commit changes but not add new files yet.

---

## git commit

`git commit` saves everything in the staging area as a permanent snapshot in Git's history.

**Commit with a message (the standard way):**

```bash
git commit -m "add: initial project structure"
```

**Commit with a multi-line message:**

```bash
git commit -m "add: user authentication system

Implements JWT-based login and registration.
Includes password hashing with bcrypt.
Adds session management middleware."
```

The first line is the subject (keep it under 72 characters). Leave a blank line, then add a longer description if needed.

**Stage all tracked files and commit in one step:**

```bash
git commit -am "fix: resolve null pointer on login page"
```

The `-a` flag automatically stages all modified tracked files before committing. It does **not** include untracked (new) files - those still need `git add` first.

**Amend the last commit (before pushing):**

```bash
git commit --amend -m "corrected commit message"
```

Use this to fix a typo in your last commit message or to add a file you forgot to stage. Only use `--amend` on commits that have not been pushed yet.

> [!WARNING]
> Never amend a commit that has already been pushed to a shared remote. Amending rewrites the commit's hash, which causes conflicts for anyone who has already pulled that commit.

### Writing good commit messages

This course uses the Conventional Commits format:

```
type: short description in present tense
```

| Type | Use when |
|---|---|
| `add` | Adding new files, features or content |
| `fix` | Fixing a bug or error |
| `update` | Improving or expanding existing content |
| `remove` | Deleting files or features |
| `rename` | Renaming files or folders |
| `refactor` | Restructuring code without changing behaviour |
| `docs` | Documentation only changes |
| `style` | Formatting, whitespace - no logic changes |
| `test` | Adding or updating tests |
| `chore` | Build process, dependency updates, tooling |

**Good examples:**

```
add: dark mode toggle to settings page
fix: session expiry on mobile browsers
update: expand branching section with rebase comparison
remove: deprecated v1 API endpoints
```

---

## git log

`git log` shows the commit history of the current branch.

**Full log (most detailed):**

```bash
git log
```

Shows each commit with its full hash, author, date and message. Press `q` to exit.

**One line per commit (most useful for a quick overview):**

```bash
git log --oneline
```

Output:

```
a1b2c3d add: dark mode toggle
e4f5g6h fix: session expiry on mobile
i7j8k9l add: initial project structure
```

**Show the last N commits:**

```bash
git log --oneline -5
```

**Show a graph of branches and merges:**

```bash
git log --oneline --graph --all
```

This is extremely useful for understanding how branches relate to each other. The `--all` flag shows commits from all branches, not just the current one.

**Show commits by a specific author:**

```bash
git log --author="Your Name"
```

**Show commits containing a specific word in the message:**

```bash
git log --grep="fix"
```

**Show commits that changed a specific file:**

```bash
git log -- README.md
```

**Show what changed in each commit (patch view):**

```bash
git log -p
```

This is the most detailed view - it shows the full diff for every commit. Useful for understanding exactly what changed and when. Press `q` to exit.

---

## git diff

`git diff` shows the exact line-by-line changes between versions of your files.

**Show unstaged changes (working directory vs staging area):**

```bash
git diff
```

Shows what has changed in your files since the last `git add`. If you have already staged everything, this will show nothing.

**Show staged changes (staging area vs last commit):**

```bash
git diff --staged
```

Shows what will be included in your next commit. Run this before `git commit` to review exactly what you are about to save.

**Show all changes since the last commit (staged and unstaged):**

```bash
git diff HEAD
```

**Compare two commits:**

```bash
git diff a1b2c3d e4f5g6h
```

**Compare two branches:**

```bash
git diff main feature/user-login
```

**Show only the names of changed files, not the full diff:**

```bash
git diff --name-only
```

### Reading diff output

```diff
diff --git a/README.md b/README.md
index a1b2c3d..e4f5g6h 100644
--- a/README.md
+++ b/README.md
@@ -1,3 +1,4 @@
 # My Project
-This is the old description.
+This is the new description.
+Added a second line.
```

- Lines starting with `-` were removed
- Lines starting with `+` were added
- Lines with no prefix are unchanged context

> [!TIP]
> In VS Code, you can see a visual diff by clicking any modified file in the Source Control panel. This is often easier to read than the terminal output, especially for large changes.

---

## git push

`git push` sends your local commits to the remote repository.

**Push the current branch to its remote counterpart:**

```bash
git push
```

**Push a specific branch:**

```bash
git push origin main
```

**Push a branch for the first time and set the upstream tracking:**

```bash
git push -u origin feature/user-login
```

The `-u` flag (short for `--set-upstream`) tells Git to remember that this local branch corresponds to the remote branch of the same name. After doing this once, you can just run `git push` on that branch without specifying the remote and branch name every time.

**Push all local branches:**

```bash
git push --all
```

**Push tags:**

```bash
git push --tags
```

> [!WARNING]
> Never force-push to a shared branch without coordinating with your team first. `git push --force` rewrites the remote history and will cause serious problems for anyone who has already pulled from that branch. If you need to force push, use `git push --force-with-lease` instead - it fails if the remote has commits you do not have locally, protecting against accidental overwrites.

> [!CAUTION]
> Before pushing, make sure you have not accidentally staged files containing passwords, API keys or tokens. Once pushed to a public repository, credentials are compromised even if you delete them in a later commit. The full history remains accessible.

---

## git pull

`git pull` downloads the latest commits from the remote and merges them into your current branch.

**Pull from the tracked remote branch:**

```bash
git pull
```

**Pull from a specific remote and branch:**

```bash
git pull origin main
```

**Pull using rebase instead of merge:**

```bash
git pull --rebase
```

This is covered in depth in [git/13-rebase.md](13-rebase.md).

> [!TIP]
> Always pull before starting new work. If your local branch is behind the remote, pulling first reduces the chance of conflicts when you push later.

> [!NOTE]
> `git pull` is `git fetch` followed by `git merge`. If you want to see what is on the remote before merging it into your local branch, use `git fetch` first and then inspect the changes with `git log origin/main` before merging manually.

---

## git switch

`git switch` changes which branch you are working on. It was introduced in Git 2.23 as a clearer alternative to `git checkout` for switching branches, and became officially stable in Git 2.51.

**Switch to an existing branch:**

```bash
git switch main
```

**Create a new branch and switch to it immediately:**

```bash
git switch -c feature/user-login
```

The `-c` flag stands for "create". This is the most common way to start working on something new.

**Switch back to the previous branch:**

```bash
git switch -
```

The `-` is shorthand for "the branch I was on before this one". Very useful for switching back and forth between two branches.

> [!NOTE]
> You may see older tutorials using `git checkout branch-name` to switch branches. Both commands work. `git switch` is the current recommendation for switching branches because it does only one thing clearly. `git checkout` does multiple things (switching branches, restoring files, detaching HEAD) which makes it confusing. This course uses `git switch` throughout.

---

## Putting It All Together

Here is the complete everyday workflow using the commands above:

**Starting work on a new feature:**

```bash
git switch main
git pull
git switch -c feature/my-feature
```

Switch to main, get the latest changes, create a new branch.

**Making changes and saving them:**

```bash
git status
git add .
git status
git commit -m "add: my new feature"
```

Check what has changed, stage everything, check what is staged, commit.

**Sharing your work:**

```bash
git push -u origin feature/my-feature
```

Push the branch to GitHub or GitLab for the first time.

**Continuing work the next day:**

```bash
git switch feature/my-feature
git pull
```

Switch back to your branch, get any updates.

**Getting a colleague's latest changes on main:**

```bash
git switch main
git pull
git switch feature/my-feature
```

---

## Try It Yourself

This exercise takes you through the complete basic workflow on a real repository.

**Step 1.** Create a new repository:

**Windows:**

```powershell
mkdir git-practice && cd git-practice && git init
```

**Mac and Linux:**

```bash
mkdir git-practice && cd git-practice && git init
```

**Step 2.** Create some files:

**Windows:**

```powershell
echo "# Git Practice" > README.md
echo "print('hello')" > hello.py
```

**Mac and Linux:**

```bash
echo "# Git Practice" > README.md
echo "print('hello')" > hello.py
```

**Step 3.** Check the status:

```bash
git status
```

Both files should appear as untracked.

**Step 4.** Stage only the README:

```bash
git add README.md
git status
```

README is staged. `hello.py` is still untracked.

**Step 5.** Make the first commit:

```bash
git commit -m "add: initial README"
```

**Step 6.** Stage and commit the Python file:

```bash
git add hello.py
git commit -m "add: hello world script"
```

**Step 7.** View the log:

```bash
git log --oneline
```

You should see two commits.

**Step 8.** Modify the README and see the diff:

**Windows:**

```powershell
echo "A repository for practising Git commands." >> README.md
```

**Mac and Linux:**

```bash
echo "A repository for practising Git commands." >> README.md
```

```bash
git diff
```

**Step 9.** Stage and check what is staged:

```bash
git add README.md
git diff --staged
```

**Step 10.** Commit the change:

```bash
git commit -m "update: add description to README"
```

**Step 11.** View the full history:

```bash
git log --oneline --graph
```

**Step 12.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q git-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf git-practice
```

---

## Common Mistakes

**Running `git init` in the wrong folder.**

If you accidentally run `git init` in your home directory or Documents folder, Git will start tracking everything in that directory. Check where you are with `pwd` (Mac/Linux) or `cd` (Windows) before running `git init`. If you did this by accident, delete the `.git` folder that was created.

**Forgetting to `git add` before `git commit`.**

`git commit` only commits what is in the staging area. If you modified files but did not run `git add`, the commit will be empty (or will fail with "nothing to commit"). Always check `git status` before committing.

**Using `git add .` from the wrong directory.**

`git add .` adds everything in the current directory and below. If you run it from the root of your repository, it stages everything. If you run it from a subdirectory, it only stages files in that subdirectory. Be aware of where you are.

**Committing without a message.**

Running `git commit` without `-m` opens your configured text editor for you to write the message. If that editor is Vim and you do not know how to use it, type `:q!` and press Enter to exit, then run `git commit -m "your message"` instead. Set a different default editor with `git config --global core.editor "nano"` or `git config --global core.editor "code --wait"`.

**Pushing without pulling first.**

If someone else has pushed to the remote branch since you last pulled, your push will be rejected with "rejected - non-fast-forward". The fix is to run `git pull` first to merge their changes into yours, then push again.

**Using `git checkout` to switch branches.**

`git checkout branch-name` works but `git checkout` does many things - it can switch branches, restore files and detach HEAD depending on what arguments you give it. Use `git switch branch-name` for switching branches and `git restore filename` for discarding changes in a file. These commands were introduced specifically to make these operations clearer.

**Mixing up `git diff` and `git diff --staged`.**

`git diff` shows unstaged changes. `git diff --staged` shows staged changes (what will go into the next commit). A common mistake is running `git diff` after `git add` and seeing no output, then thinking nothing changed - the changes were staged, so `git diff` shows nothing. Run `git diff --staged` to see them.

---

## Summary

- **`git init`** - create a new Git repository in the current folder
- **`git clone <url>`** - download a complete copy of a remote repository
- **`git status`** - see the current state of your working directory and staging area
- **`git add`** - move changes to the staging area ready for commit
- **`git commit -m "message"`** - save a snapshot of the staging area to Git history
- **`git log`** - view the commit history; `--oneline` for a compact view
- **`git diff`** - see line-by-line changes; `--staged` to see what will be committed
- **`git push`** - send local commits to the remote repository
- **`git pull`** - download and merge remote commits into the current branch
- **`git switch`** - change branches; `-c` to create a new branch

---

## Next Step

Now that you know the basic commands, the next file covers branching in depth: [Branching](06-branching.md)

---

## Sources and Further Reading

- [Official Git documentation](https://git-scm.com/doc) - the complete reference for every command covered in this file
- [Pro Git book, Chapter 2: Git Basics](https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository) - the official in-depth guide to the basic Git workflow
- [Conventional Commits specification](https://www.conventionalcommits.org/en/v1.0.0/) - the standard for structured commit messages used throughout this course
- [git switch documentation](https://git-scm.com/docs/git-switch) - official docs for the `git switch` command
- [git add documentation](https://git-scm.com/docs/git-add) - official docs including the interactive `-p` flag
- [Oh Shit, Git!?!](https://ohshitgit.com) - practical fixes for when the basic workflow goes wrong

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

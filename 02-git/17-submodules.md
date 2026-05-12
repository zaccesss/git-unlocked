# Submodules

**Difficulty:** 🔴 Advanced | **Time:** 25 minutes

Submodules let you embed one Git repository inside another. They are the solution to a specific problem: when your project depends on another project's source code and you want to track a specific version of that dependency inside your own repository. Understanding submodules - including their significant limitations - is essential for working on projects that use them.

---

## Table of Contents

- [What are submodules?](#what-are-submodules)
- [When to use submodules](#when-to-use-submodules)
- [When not to use submodules](#when-not-to-use-submodules)
- [Adding a submodule](#adding-a-submodule)
- [Cloning a repository with submodules](#cloning-a-repository-with-submodules)
- [Updating submodules](#updating-submodules)
- [Making changes inside a submodule](#making-changes-inside-a-submodule)
- [Removing a submodule](#removing-a-submodule)
- [The .gitmodules file](#the-gitmodules-file)
- [Common submodule commands](#common-submodule-commands)
- [Alternatives to submodules](#alternatives-to-submodules)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What are Submodules?

A submodule is a Git repository embedded inside another Git repository. The outer repository (the superproject) tracks a specific commit of the inner repository (the submodule). It does not track the submodule's branch or its latest changes - only a specific commit hash.

When you look at a submodule in `git status` or `git log`, Git shows a single commit reference, not the submodule's files. The submodule has its own `.git` directory, its own history and its own branches - completely independent from the outer repository.

```
my-project/           <- outer repository
├── .git/
├── .gitmodules       <- submodule configuration
├── src/
└── vendor/
    └── some-library/ <- submodule (another git repo)
        ├── .git/
        └── ...
```

---

## When to Use Submodules

Submodules are appropriate in a narrow set of situations:

**Shared libraries across multiple projects.** If your organisation has a shared utility library that multiple projects depend on and you want each project to track a specific version of that library with its source code included.

**Embedded firmware or hardware description files.** Projects involving hardware often need specific versions of vendor SDKs or hardware description files maintained in separate repositories.

**Large projects with clearly separable components.** Some projects split naturally into independent sub-repositories with their own release cycles.

**When you need the full source of a dependency.** If you need to modify a dependency or if including its source is a requirement (for example, in regulated industries), submodules make the included version explicit and auditable.

---

## When Not to Use Submodules

Submodules are one of Git's most frequently misused features. Consider alternatives first.

**Do not use submodules for package management.** If your language has a package manager (npm, pip, cargo, maven, go modules), use it. Package managers handle versioning, resolution and updates far better than submodules.

**Do not use submodules when a simple dependency reference is enough.** A `package.json`, `requirements.txt` or `go.mod` file that specifies a version is cleaner and easier to maintain than a submodule.

**Do not use submodules if your team is not experienced with Git.** Submodules are one of the most common sources of confusion and lost work in Git. The learning curve is steep and the error states are painful.

---

## Adding a Submodule

**Add a submodule to your repository:**

```bash
git submodule add https://github.com/OWNER/REPO.git vendor/repo-name
```

This clones the repository into `vendor/repo-name` and creates a `.gitmodules` file (or adds to an existing one). Git also stages two changes: the `.gitmodules` file and a new directory entry for the submodule.

**Commit the submodule addition:**

```bash
git commit -m "add: repo-name as submodule"
```

**Add a submodule and track a specific branch:**

```bash
git submodule add -b main https://github.com/OWNER/REPO.git vendor/repo-name
```

---

## Cloning a Repository with Submodules

When you clone a repository that contains submodules, the submodule directories are created but empty by default.

**Clone and initialise all submodules in one step:**

```bash
git clone --recurse-submodules https://github.com/OWNER/REPO.git
```

**If you already cloned without submodules, initialise them afterwards:**

```bash
git submodule update --init
```

**Initialise nested submodules (submodules within submodules) recursively:**

```bash
git submodule update --init --recursive
```

> [!WARNING]
> Forgetting `--recurse-submodules` when cloning is the single most common submodule mistake. The outer repository clones fine, but the submodule directories are empty. Always use `--recurse-submodules` when cloning a project you know has submodules.

---

## Updating Submodules

A submodule is pinned to a specific commit. When the upstream repository has new commits, your submodule does not update automatically - you must explicitly update it.

**Update all submodules to their latest remote commit on the tracked branch:**

```bash
git submodule update --remote
```

**Update a specific submodule:**

```bash
git submodule update --remote vendor/repo-name
```

After updating, Git stages the new commit reference. Commit the change to record that the superproject now tracks the newer commit:

```bash
git add vendor/repo-name
git commit -m "update: bump repo-name submodule to latest"
```

**Pull changes including submodule updates:**

```bash
git pull --recurse-submodules
```

---

## Making Changes Inside a Submodule

Working inside a submodule is like working in any other Git repository, but with an important caveat: **submodules are in detached HEAD state by default**.

When you enter a submodule directory, Git is not on any branch - it is checked out at the specific commit the superproject references.

**To make changes inside a submodule:**

```bash
cd vendor/repo-name
git switch main           # switch to a branch first
# make changes
git add .
git commit -m "fix: something in the submodule"
git push
cd ../..
git add vendor/repo-name  # stage the new commit reference
git commit -m "update: advance repo-name submodule to latest fix"
git push
```

Both the submodule change and the superproject reference update must be committed and pushed separately.

> [!NOTE]
> If you commit changes inside a submodule but forget to commit the updated reference in the superproject, other developers who pull the superproject will not get your submodule changes. The superproject still points to the old commit.

---

## Removing a Submodule

Removing a submodule requires several steps. Git does not have a single command to remove one cleanly.

**Step 1.** Remove the submodule entry from `.gitmodules`:

```bash
git submodule deinit -f vendor/repo-name
```

**Step 2.** Remove the submodule from the Git index:

```bash
git rm -f vendor/repo-name
```

**Step 3.** Remove the submodule's `.git` directory:

**Windows (PowerShell):**

```powershell
Remove-Item -Recurse -Force .git/modules/vendor/repo-name
```

**Mac and Linux:**

```bash
rm -rf .git/modules/vendor/repo-name
```

**Step 4.** Commit the removal:

```bash
git commit -m "remove: repo-name submodule"
```

---

## The .gitmodules File

When you add a submodule, Git creates or updates `.gitmodules` at the root of the repository. This file records the submodule's path and URL.

```ini
[submodule "vendor/repo-name"]
    path = vendor/repo-name
    url = https://github.com/OWNER/REPO.git
    branch = main
```

This file is committed to the repository so everyone who clones it knows where to find the submodules.

---

## Common Submodule Commands

| Command | What it does |
|---|---|
| `git submodule add <url> <path>` | Add a new submodule |
| `git submodule init` | Initialise submodule configuration from `.gitmodules` |
| `git submodule update` | Check out the commit recorded in the superproject |
| `git submodule update --init` | Init and update in one step |
| `git submodule update --remote` | Update to the latest commit on the tracked branch |
| `git submodule update --recursive` | Update including nested submodules |
| `git submodule status` | Show the current commit of each submodule |
| `git submodule foreach <command>` | Run a command in every submodule |
| `git submodule deinit <path>` | Unregister a submodule |

**Run a command in every submodule:**

```bash
git submodule foreach git pull origin main
```

This is useful for pulling the latest changes in all submodules at once.

---

## Alternatives to Submodules

Before reaching for submodules, consider these alternatives.

**Package managers.** For software libraries: npm, pip, cargo, maven, gem, go modules. These are purpose-built for dependency management and far easier to work with.

**Git subtree.** `git subtree` merges another repository's history into a subdirectory of your project. Unlike submodules, the included files are part of your repository and no special commands are needed to clone or work with them. The trade-off is that the included repository's history is merged into yours.

```bash
git subtree add --prefix vendor/repo-name https://github.com/OWNER/REPO.git main --squash
```

**Monorepo.** For tightly coupled projects within the same organisation, a monorepo (a single repository containing multiple projects) is often simpler than submodules. Covered in [git/23-monorepos.md](23-monorepos.md).

**Vendoring.** Simply copying the dependency's source code into your repository at a specific version, with no ongoing Git connection. Simple and robust, at the cost of having to manually update.

---

## Try It Yourself

**Step 1.** Create two repositories - one will be the submodule:

**Windows (PowerShell):**

```powershell
mkdir submodule-lib && cd submodule-lib && git init
echo "# Shared Library" > README.md
git add README.md && git commit -m "add: initial library"
cd ..
mkdir main-project && cd main-project && git init
echo "# Main Project" > README.md
git add README.md && git commit -m "add: initial project"
```

**Mac and Linux:**

```bash
mkdir submodule-lib && cd submodule-lib && git init
echo "# Shared Library" > README.md
git add README.md && git commit -m "add: initial library"
cd ..
mkdir main-project && cd main-project && git init
echo "# Main Project" > README.md
git add README.md && git commit -m "add: initial project"
```

**Step 2.** Add the library as a submodule (using a local path):

```bash
git submodule add ../submodule-lib vendor/lib
git status
```

**Step 3.** Commit the submodule addition:

```bash
git commit -m "add: shared library as submodule"
```

**Step 4.** Check the submodule status:

```bash
git submodule status
```

**Step 5.** Make a change in the library:

```bash
cd ../submodule-lib
echo "function hello() {}" > utils.js
git add utils.js && git commit -m "add: hello utility"
cd ../main-project
```

**Step 6.** Update the submodule to the new commit:

```bash
git submodule update --remote
git status
git add vendor/lib
git commit -m "update: advance lib submodule to latest"
```

**Step 7.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q main-project && rmdir /s /q submodule-lib
```

**Mac and Linux:**

```bash
cd .. && rm -rf main-project submodule-lib
```

---

## Common Mistakes

**Cloning without `--recurse-submodules`.**

The most common mistake. The repository clones but all submodule directories are empty. Fix with `git submodule update --init --recursive`.

**Making changes inside a submodule while in detached HEAD state.**

If you make and commit changes inside a submodule without first switching to a branch, those commits may be lost when you later run `git submodule update`. Always run `git switch main` (or the appropriate branch) inside the submodule before making changes.

**Forgetting to push submodule changes before pushing the superproject.**

If you commit changes inside a submodule and commit the updated reference in the superproject, but only push the superproject, others will get a reference to a commit that does not exist on the remote. Always push the submodule first, then the superproject.

**Forgetting to commit the updated submodule reference.**

After running `git submodule update --remote`, the new commit is staged but not committed. You must run `git commit` to record that the superproject now uses the newer submodule commit.

**Using submodules where a package manager would do.**

If your language has a package manager, use it. Submodules are a solution for when no package manager option exists or when you genuinely need the full source of a dependency tracked at a specific commit.

---

## Summary

- A submodule is a Git repository embedded inside another repository at a specific commit
- The superproject tracks a commit hash, not a branch - submodules do not update automatically
- `git submodule add <url> <path>` adds a submodule
- Always clone with `--recurse-submodules` or run `git submodule update --init` after cloning
- `git submodule update --remote` updates to the latest commit on the tracked branch
- When working inside a submodule, switch to a branch first to avoid detached HEAD state
- Removing a submodule requires `git submodule deinit`, `git rm` and manually deleting `.git/modules/`
- Consider alternatives first: package managers, `git subtree`, monorepos or vendoring

---

## Sources and Further Reading

- [Official git submodule documentation](https://git-scm.com/docs/git-submodule) - the complete reference
- [Pro Git book, Chapter 7.11: Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) - the comprehensive official guide to submodules
- [Atlassian - Git submodules](https://www.atlassian.com/git/tutorials/git-submodule) - practical guide with common workflows
- [git subtree documentation](https://git-scm.com/docs/git-subtree) - the alternative to submodules

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

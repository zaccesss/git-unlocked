# Monorepo patterns

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 45 minutes

---

## Table of contents

- [Introduction](#introduction)
- [Should you use a monorepo?](#should-you-use-a-monorepo)
- [The Git scaling triad](#the-git-scaling-triad)
- [Partial clone](#partial-clone)
- [Sparse checkout](#sparse-checkout)
- [fsmonitor and commit-graph](#fsmonitor-and-commit-graph)
- [Scalar: the all-in-one setup](#scalar-the-all-in-one-setup)
- [Package manager workspaces](#package-manager-workspaces)
- [The task runner tier](#the-task-runner-tier)
- [Monorepo-specific Git commands](#monorepo-specific-git-commands)
- [How the giants do it](#how-the-giants-do-it)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources](#sources)

---

## Introduction

A monorepo stores multiple projects, packages or services in a single Git repository. The alternative - separate repositories per project - is called polyrepo or multi-repo. Neither is universally better. Monorepos solve specific problems (atomic cross-cutting changes, unified dependency management, consistent tooling) and create others (Git performance at scale, CI build times, access control granularity).

This file covers both the Git-level techniques that make monorepos fast on large machines (partial clone, sparse checkout, fsmonitor) and the tooling tier that handles the build-graph problem (Nx, Turborepo, Bazel). It closes with real examples from the teams operating at the largest scale in the world.

**What you need:**

- Git 2.27+ for sparse-checkout cone mode
- Git 2.37+ for the built-in fsmonitor daemon
- Git 2.38+ for Scalar (bundled with Git)
- Node.js 20+ for Nx and Turborepo examples

---

## Should you use a monorepo?

### Reasons to use a monorepo

- **Atomic cross-cutting changes.** When you rename a function used across 20 packages, you can make the change, test it and merge it in a single PR. With separate repos, you need a choreographed sequence of PRs across many repos with version bumps at each step.
- **Unified dependency management.** One `package.json` (or `go.mod`, or `requirements.txt`) governs all packages. Dependency drift between packages is impossible.
- **Consistent tooling and CI.** One linter config, one formatter, one test runner, one CI definition. No "this package uses ESLint 8 and that one uses 9 and they conflict" problems.
- **Better code reuse.** Shared utilities are a directory away, not a versioned package with a publish-and-install cycle.

### Reasons not to use a monorepo

- **Access control is per-repository on GitHub and GitLab.** There is no native "give this team read access to `/services/payments` but not to `/services/auth`." CODEOWNERS can route reviews but cannot restrict reads.
- **Git performance degrades at large scale.** `git status` on a 400,000-file working tree without optimisations can take 20+ seconds.
- **CI build time explodes** unless you build only what changed. Without affected-build tooling (Nx, Turborepo, Bazel), every PR rebuilds everything.
- **Learning curve.** Workspace tooling, affected commands and sparse checkout add complexity that small teams do not need.

---

## The Git scaling triad

Three Git features combine to make large repositories usable. Apply all three together for the best results.

### The canonical monorepo clone

```bash
# Blobless partial clone + sparse checkout + cone mode
git clone --filter=blob:none --sparse \
    https://github.com/org/monorepo.git
cd monorepo

# Initialise cone-mode sparse checkout
git sparse-checkout init --cone

# Check out only the directories you need
git sparse-checkout set frontend shared tools/cli

# Complete the checkout
git checkout main
```

This sequence:
1. Clones all commits and trees but downloads blobs (file contents) only on demand
2. Checks out only the directories you specified, ignoring the rest of the working tree
3. Reduces a 50 GB repository to a few hundred MB initial clone

---

## Partial clone

Partial clone defers downloading Git objects until they are needed, reducing clone time and disk usage dramatically on large repositories.

### Three flavours

```bash
# Blobless: downloads all commits and trees, blobs on demand
# Best for: developers who work across the repository
git clone --filter=blob:none https://github.com/org/repo.git

# Treeless: downloads all commits, trees and blobs on demand
# Best for: single-build CI jobs that only touch one path
git clone --filter=tree:0 https://github.com/org/repo.git

# Blob size limit: omits blobs larger than 1 MB
# Best for: repos with large binary assets mixed with code
git clone --filter=blob:limit=1m https://github.com/org/repo.git
```

When you access a file whose blob was not downloaded, Git fetches it automatically from the remote. There is no manual step.

> [!NOTE]
> Treeless clone (`--filter=tree:0`) breaks `git log -- <path>` because path-based history requires tree objects. Use blobless for developer clones; use treeless only in throw-away CI environments.

### Changing the filter after cloning

```bash
# Re-download everything (convert a partial clone to a full clone)
git fetch --refetch
```

---

## Sparse checkout

Sparse checkout tells Git to populate only a subset of the working tree. Files outside the sparse set exist in the repository (and are included in commits from others) but are not materialised on your disk.

### Cone mode vs non-cone mode

**Always use cone mode.** Non-cone sparse checkout uses gitignore-style patterns and is much slower on large trees because Git must evaluate every file against every pattern. Cone mode works exclusively with directory prefixes, which Git can evaluate in constant time using the tree structure.

```bash
# Enable cone mode
git sparse-checkout init --cone

# Set the directories you want
git sparse-checkout set frontend/src shared/utils

# Add more directories without replacing the current set
git sparse-checkout add backend/api

# See what is currently checked out
git sparse-checkout list

# Go back to a full checkout
git sparse-checkout disable
```

### Sparse checkout with CI

In CI, you often only need the code for one service. Combining partial clone with sparse checkout produces a minimal footprint:

```yaml
# GitHub Actions example
- uses: actions/checkout@v4
  with:
    filter: blob:none
    sparse-checkout: |
      services/payments
      shared/proto
    sparse-checkout-cone-mode: true
```

---

## fsmonitor and commit-graph

These two features make day-to-day Git operations fast on large working trees.

### fsmonitor

`git status` on a large tree is slow because Git must stat every tracked file to find what changed. The **file system monitor** daemon watches for file system events and tells Git exactly which files changed, reducing `git status` from seconds to milliseconds.

```bash
# Enable the built-in fsmonitor daemon (Git 2.37+)
git config core.fsmonitor true

# Also enable the untracked cache (complements fsmonitor)
git config core.untrackedCache true
```

The daemon starts automatically on the first `git status` and runs in the background. No manual process management is needed.

**Real-world numbers:** Microsoft reports `git status` dropping from ~17 seconds to ~1 second on the Windows repository (3.5 million files) after enabling fsmonitor.

### commit-graph with changed-path Bloom filters

The commit-graph is a binary cache of the commit graph stored in `.git/objects/info/commit-graphs/`. Git uses it to speed up `git log`, `git merge-base`, `git push` reachability checks and more.

**Changed-path Bloom filters** (added in Git 2.27) extend the commit-graph to answer "which commits touched `<path>`?" without scanning all trees. This dramatically speeds up `git log -- <path>` on large histories.

```bash
# Write the commit-graph with Bloom filters
git commit-graph write --reachable --changed-paths

# Update it incrementally after fetching
git fetch && git commit-graph write --reachable --changed-paths --split

# Automate it (runs in background during other Git operations)
git config fetch.writeCommitGraph true
git maintenance start
```

---

## Scalar: the all-in-one setup

**Scalar** is a Git addon developed by Microsoft that applies all monorepo optimisations in a single command. It has been bundled with Git since version 2.38.

```bash
# Clone with all optimisations enabled automatically
scalar clone https://github.com/org/monorepo.git

# Register an existing clone for background maintenance
scalar register /path/to/repo
```

`scalar clone` automatically configures:
- Partial clone (`--filter=blob:none`)
- Sparse checkout cone mode
- Multi-pack index
- Commit-graph with Bloom filters
- fsmonitor
- Background maintenance via `git maintenance start`

For most monorepo use cases, `scalar clone` is all you need at the Git level before adding workspace tooling on top.

### Recommended global config for monorepos

```bash
git config --global core.fsmonitor true
git config --global core.untrackedCache true
git config --global index.sparse true
git config --global feature.manyFiles true
git config --global fetch.writeCommitGraph true
git config --global pack.useSparse true
git config --global checkout.workers 0           # use all CPU cores
git maintenance start
```

---

## Package manager workspaces

Before adding a task runner like Nx or Turborepo, set up **package manager workspaces**. They handle installation, dependency linking and cross-package scripts with no extra tooling.

### pnpm workspaces (recommended)

pnpm is the strongest default for monorepos. Its content-addressable global store saves 50-70% disk compared to npm, and its strict isolation prevents phantom dependencies.

```yaml
# pnpm-workspace.yaml
packages:
  - 'apps/*'
  - 'packages/*'
  - 'tools/*'
```

```bash
# Install all packages
pnpm install

# Run a script in a specific package
pnpm --filter my-app run build

# Run a script in all packages affected by changes since main
pnpm --filter "...[origin/main]" run build

# Run a script in a package and all its dependencies
pnpm --filter "my-app..." run build
```

### npm workspaces

```json
// package.json
{
  "workspaces": ["apps/*", "packages/*"]
}
```

```bash
npm install
npm run build --workspace=apps/my-app
```

> [!NOTE]
> npm workspaces hoist all dependencies to the root `node_modules`. This can cause phantom dependencies (a package works locally because a sibling installed the dependency, but fails in isolation). pnpm's strict mode prevents this.

### Yarn 4 workspaces

Yarn 4 adds **workspaces** (same as before), **Plug'n'Play** (no `node_modules`), **Constraints** (enforce cross-workspace rules), and **catalogs** (centrally define dependency versions). It is the most opinionated of the three package managers and has the steepest learning curve, but the strictest isolation.

---

## The task runner tier

Package manager workspaces handle installation. Task runners handle **build graph awareness**: knowing that building `app-a` requires first building `shared-utils`, caching completed builds by content hash, and running only the builds affected by a given Git diff.

### Nx 🟡

**Current version: 22.x (Nx 22.5.0, released 2026-02-09)**

Nx ([nx.dev](https://nx.dev)) builds a dependency graph from your project structure and import analysis. Its key primitives are:

- **`nx run <project>:<target>`** - run a single task (build, test, lint)
- **`nx run-many -t build`** - run a task across all projects, respecting the dependency graph
- **`nx affected -t build --base=origin/main`** - run a task only on projects affected by changes since `main`
- **Nx Cache** - stores task output (files + terminal output) keyed by content hash; re-running an unchanged task restores output from cache in milliseconds

```bash
# Add Nx to an existing pnpm workspace
pnpm add -D nx@latest -w

# Initialise (detects projects automatically)
npx nx init

# Run builds only for projects affected by your changes
npx nx affected -t build --base=origin/main --head=HEAD

# Visualise the dependency graph
npx nx graph
```

> [!IMPORTANT]
> Nx changed its remote cache API in 2024-2025. The old `tasksRunnerOptions` key in `nx.json` for custom self-hosted caches is deprecated. Self-hosted remote caching now requires **Nx Powerpack** or **Nx Cloud**. Tutorials older than mid-2024 that show `tasksRunnerOptions` with a custom runner are out of date.

### Turborepo 🟡

**Current version: 2.8.x**

Turborepo ([turbo.build](https://turbo.build)) is Vercel's Rust-written task runner, optimised for JavaScript and TypeScript monorepos. It is simpler to configure than Nx (one `turbo.json` file) and faster at pure task running due to the Rust core.

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "tasks": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": []
    },
    "lint": {
      "outputs": []
    }
  }
}
```

```bash
# Run build for everything affected since main
turbo run build --filter="...[origin/main]"

# Run build for a specific app and all its dependencies
turbo run build --filter="my-app..."

# Run build in parallel across all packages
turbo run build
```

Turborepo uses **Vercel Remote Cache** by default; you can self-host with the open-source `ducktors/turborepo-remote-cache` package.

### Bazel 🔴

**Current LTS: Bazel 9 (announced January 2026); Bazel 8 (Active LTS, supported through December 2027)**

Bazel ([bazel.build](https://bazel.build)) is Google's open-sourced build system (internal name: Blaze). It supports hermetic, reproducible builds across any language and scales to billions of lines of code. The learning curve is significantly steeper than Nx or Turborepo.

Key concepts:
- **`BUILD` files** define targets (libraries, binaries, tests) using Starlark (a Python dialect)
- **`WORKSPACE` file** declares external dependencies (removed in Bazel 9; use `MODULE.bazel` with Bzlmod)
- **Remote execution** via the Remote Execution API (REAPI): BuildBuddy, EngFlow or Buildbarn provide the execution backend
- **Hermetic sandboxing**: each build action runs in isolation; outputs are content-addressed

```python
# Example BUILD file (Java)
java_library(
    name = "auth",
    srcs = glob(["src/main/java/**/*.java"]),
    deps = [
        "//shared/proto:auth_proto_java",
        "@maven//:com_google_guava_guava",
    ],
)

java_test(
    name = "auth_test",
    srcs = glob(["src/test/java/**/*.java"]),
    deps = [":auth", "@maven//:junit_junit"],
)
```

```bash
# Build a specific target
bazel build //services/auth:auth

# Test only targets affected by recent changes
bazel test $(bazel query 'rdeps(//..., set(//services/auth))')

# Build everything
bazel build //...
```

> [!NOTE]
> **Bazel 9 removes the `WORKSPACE` file.** All dependencies must use `MODULE.bazel` with Bzlmod. Tutorials using `WORKSPACE` and `http_archive` are out of date for Bazel 9+.

### Lerna (JavaScript only)

**Current version: 9.x**

Lerna is maintained by Nrwl (the Nx team) and delegates task running to Nx since Lerna v6. Its primary use today is **`lerna version`** and **`lerna publish`** for versioning and publishing npm packages.

> [!IMPORTANT]
> Lerna's `bootstrap`, `add` and `link` commands are **removed in Lerna v9**. Use package manager workspaces (`pnpm install`, `npm install --workspaces`) for dependency installation. Any tutorial teaching `lerna bootstrap` is out of date.

---

## Monorepo-specific Git commands

These commands are especially useful in monorepos because they scope to paths and leverage the commit-graph for speed.

```bash
# Pathspec-scoped log: show only commits that touched packages/auth
git log --oneline -- packages/auth/

# Exclude generated files and docs from history
git log --oneline -- . \
  ':(exclude)docs/' \
  ':(exclude,glob)**/*.generated.ts'

# The "affected since main" diff (feeds task runners)
git diff --name-only origin/main...HEAD

# Three-dot diff: changes on your branch since diverging from main
git diff origin/main...HEAD -- packages/ui/

# Track a file across renames (essential in refactored monorepos)
git log -p --follow -- packages/shared/src/utils.ts

# Cross-file copy detection in blame (removes whitespace noise, tracks moves)
git blame -w -M -C -C -C packages/shared/src/utils.ts

# Pathspec magic: anchor to repo root to avoid ambiguity
git log -- ':(top)README.md'

# Case-insensitive match (useful on case-sensitive Linux when macOS devs used different casing)
git log -- ':(icase)*.MD'

# Search content across the monorepo, excluding vendor
git grep -n 'TODO' -- ':(exclude)vendor/'

# Feed affected packages to your task runner
AFFECTED=$(git diff --name-only origin/main...HEAD)
npx nx affected -t build --base=origin/main --head=HEAD
turbo run build --filter="...[origin/main]"
pnpm --filter "...[origin/main]" run build
```

---

## How the giants do it

### Google

Google's primary codebase (**Piper**) is not Git. It holds approximately 2 billion lines of code across 86 TB, with around 40,000 commits per day. The build system is **Blaze** (internal Bazel). Developers use **CitC** (Clients in the Cloud), a FUSE-based virtual filesystem that makes the entire Piper tree appear local without downloading it. Chrome and Android are the exceptions - they use separate Git repositories.

The core argument for Google's monorepo is in a 2016 ACM paper: visibility (everyone can see and use any code), unified versioning (no diamond dependency problem), and large-scale refactoring (one commit can update every caller of a function across the entire company).

### Meta

Meta outgrew Git and built its own VCS stack. **Sapling** (open-sourced November 2022 at [sapling-scm.com](https://sapling-scm.com)) is the Git-compatible client. **Mononoke** is the Rust server. **EdenFS** is a virtual filesystem similar to Microsoft's VFS for Git. The build system is **Buck2** (open-sourced April 2023).

A 2025 engineering post ([engineering.fb.com](https://engineering.fb.com/2025/10/16/developer-tools/branching-in-a-sapling-monorepo/)) introduced full-repo branching and mergeable directory branching in Sapling, giving engineers the flexibility of feature branches without the scale problems Git branches have in a multi-million-file tree.

### Microsoft

The Windows repository is approximately 3.5 million files at 300 GB - too large for standard Git. Microsoft's journey: Source Depot (internal) to Git with **VFS for Git** (GVFS) to **Scalar** to upstream Git. Scalar is now bundled with Git since 2.38; the innovations are part of the Git core. About 4,000 engineers commit to the Windows repo generating ~1,760 builds per day.

Microsoft's blog post on the migration remains one of the most referenced engineering posts on Git at scale: [devblogs.microsoft.com/bharry/the-largest-git-repo-on-the-planet](https://devblogs.microsoft.com/bharry/the-largest-git-repo-on-the-planet/).

### Shopify

Shopify Core is a 2.8 million-line Ruby on Rails monolith with 500,000+ commits and 150,000+ tests. The team ships ~40 times per day from trunk with a custom continuous deployment tool (**Shipit**) plus GitHub's merge queue. Shopify also maintains separate monorepos for the CLI (Go) and React Native mobile apps.

---

## Try it yourself

This exercise works with the `microsoft/vscode` repository, which is large enough to demonstrate the techniques without requiring special access.

```bash
# Step 1: blobless clone (much faster than a full clone)
git clone --filter=blob:none \
    https://github.com/microsoft/vscode.git
cd vscode

# Step 2: check clone size vs full clone
du -sh .git
# Compare to: git clone https://github.com/microsoft/vscode.git (no filter)

# Step 3: enable fsmonitor and commit-graph
git config core.fsmonitor true
git config core.untrackedCache true
git commit-graph write --reachable --changed-paths

# Step 4: time git status before and after
time git status    # should be fast with fsmonitor

# Step 5: use pathspec-scoped log
git log --oneline -- src/vs/editor/ | head -20

# Step 6: find files changed in the last 10 commits
git diff --name-only HEAD~10..HEAD | head -30
```

**Bonus:** Enable sparse checkout and check out only the `src/vs/editor/` directory:

```bash
git sparse-checkout init --cone
git sparse-checkout set src/vs/editor
git sparse-checkout list
```

---

## Common mistakes

**Using non-cone sparse checkout.**
Non-cone mode uses gitignore-style patterns and is slow on large trees. Always use `git sparse-checkout init --cone`. If you have existing non-cone patterns, convert with `git sparse-checkout reapply --cone`.

**Treeless clone for developer workflows.**
`--filter=tree:0` is fast but breaks `git log -- <path>` because path history requires tree objects. Use `--filter=blob:none` for developer clones; reserve `--filter=tree:0` for disposable CI jobs.

**Using `lerna bootstrap` in 2026.**
Lerna's `bootstrap`, `add` and `link` commands are removed in Lerna v9. Use `pnpm install` or `npm install --workspaces` for dependency management and Lerna only for versioning and publishing.

**Using Nx's deprecated `tasksRunnerOptions` for remote cache.**
The `tasksRunnerOptions` key in `nx.json` for configuring custom cache runners is deprecated. Self-hosted remote caching now requires Nx Powerpack or Nx Cloud. If you need free self-hosted caching, use Turborepo with `ducktors/turborepo-remote-cache`.

**Relying on `git log -- path` without commit-graph Bloom filters.**
Without Bloom filters, `git log -- packages/auth/` on a large repo requires opening every tree in history to check whether `packages/auth/` was modified. With Bloom filters enabled (`git commit-graph write --reachable --changed-paths`), this is answered from the commit-graph cache without opening any trees. Enable it.

**Forgetting `git maintenance start`.**
`git maintenance start` registers a background cron (or launchd/systemd on Mac/Linux) that runs `git fetch --prune`, `git commit-graph write`, `git pack-refs` and `git repack` incrementally. Without it, your commit-graph goes stale and performance degrades as new commits arrive.

**Building everything in CI instead of using affected commands.**
The whole point of a task runner like Nx or Turborepo is that only affected projects build. If your CI job runs `nx run-many -t build` (all projects) instead of `nx affected -t build --base=origin/main`, you are paying the monorepo performance cost without getting the benefit.

---

## Summary

Monorepos solve the atomic-change and dependency-unification problems at the cost of Git performance degradation at scale and CI build time explosion. The solutions to both problems are well-established and freely available.

At the Git level, the three tools are: **partial clone** (`--filter=blob:none`) to avoid downloading blobs you will never touch, **sparse checkout cone mode** to materialise only the directories you need, and **fsmonitor + commit-graph** to make status checks and history queries fast. **Scalar** applies all of these in one command.

Above the Git level, the task runner tier (Nx, Turborepo, Bazel) provides build graph awareness and content-addressed caching so CI builds only what changed. The right tool depends on your language and scale: Nx or Turborepo for JavaScript/TypeScript, Bazel for multi-language or Google/Meta-scale hermetic builds.

---

## Sources and Further Reading

- [GitHub: get up to speed with partial clone and shallow clone](https://github.blog/open-source/git/get-up-to-speed-with-partial-clone-and-shallow-clone/)
- [git-scm.com: partial-clone documentation](https://git-scm.com/docs/partial-clone)
- [kernel.org: git-sparse-checkout documentation](https://www.kernel.org/pub/software/scm/git/docs/git-sparse-checkout.html)
- [GitHub: improve Git monorepo performance with a file system monitor](https://github.blog/engineering/infrastructure/improve-git-monorepo-performance-with-a-file-system-monitor/)
- [GitHub: the story of Scalar](https://github.blog/open-source/git/the-story-of-scalar/)
- [Microsoft: the largest Git repo on the planet](https://devblogs.microsoft.com/bharry/the-largest-git-repo-on-the-planet/)
- [Nx documentation](https://nx.dev/docs)
- [Nx: run only tasks affected by a PR](https://nx.dev/docs/features/ci-features/affected)
- [Turborepo documentation](https://turbo.build/repo/docs)
- [Bazel documentation](https://bazel.build/docs)
- [Bazel 8.0 LTS release](https://blog.bazel.build/2024/12/09/bazel-8-release.html)
- [ACM: why Google stores billions of lines of code in a single repository](https://cacm.acm.org/research/why-google-stores-billions-of-lines-of-code-in-a-single-repository/)
- [Meta: Sapling source control](https://engineering.fb.com/2022/11/15/open-source/sapling-source-control-scalable/)
- [Sapling documentation](https://sapling-scm.com/docs/introduction/)
- [Shopify: successfully merging the work of 1000+ developers](https://shopify.engineering/successfully-merging-work-1000-developers)
- [Trunkbaseddevelopment.com: monorepos](https://trunkbaseddevelopment.com/monorepos/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

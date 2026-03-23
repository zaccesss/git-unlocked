# Monorepos

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

A monorepo is a single Git repository that contains multiple projects, packages or services that could otherwise be separate repositories. Google, Meta, Microsoft, Airbnb and many others run large engineering organisations from a single repository. Understanding monorepos - what they are, why teams choose them and the tools that make them practical - is increasingly valuable as software projects grow in complexity.

---

## Table of Contents

- [What is a monorepo?](#what-is-a-monorepo)
- [Monorepo vs polyrepo](#monorepo-vs-polyrepo)
- [When to use a monorepo](#when-to-use-a-monorepo)
- [Monorepo challenges with Git](#monorepo-challenges-with-git)
- [JavaScript and TypeScript monorepos](#javascript-and-typescript-monorepos)
- [Python monorepos](#python-monorepos)
- [General monorepo tools](#general-monorepo-tools)
- [Git techniques for monorepos](#git-techniques-for-monorepos)
- [Monorepos at scale](#monorepos-at-scale)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a Monorepo?

A monorepo (short for monolithic repository) stores multiple distinct projects in a single Git repository. The key characteristic is that the projects could be independent but are kept together intentionally.

A typical monorepo structure:

```
company-monorepo/
├── apps/
│   ├── web/              <- web frontend
│   ├── mobile/           <- mobile app
│   └── api/              <- backend API
├── packages/
│   ├── ui-components/    <- shared UI library
│   ├── utils/            <- shared utilities
│   └── types/            <- shared TypeScript types
├── services/
│   ├── auth/             <- authentication service
│   └── payments/         <- payments service
├── docs/
└── tools/
```

All of these live in one repository, share the same version history and are committed, reviewed and deployed together (or selectively).

---

## Monorepo vs Polyrepo

**Polyrepo** means each project, service or package has its own repository. This was the traditional approach before monorepo tooling matured.

| Aspect | Monorepo | Polyrepo |
|---|---|---|
| Code sharing | Easy - shared packages in the same repo | Hard - requires publishing packages to a registry |
| Atomic changes | One commit can change multiple packages | Cross-repo changes require coordinating multiple PRs |
| Dependency management | Internal dependencies are always in sync | Versions can diverge between repos |
| CI/CD | Can be complex - need selective builds | Simple per-repo, complex across repos |
| Repository size | Grows large over time | Stays manageable per repo |
| Access control | Coarser - whole repo or not | Finer - per repo permissions |
| Onboarding | One clone gets everything | Multiple clones needed |
| Tooling | Requires monorepo-specific tools | Standard Git workflows |

Neither approach is universally better. The right choice depends on your team size, project structure and workflow requirements.

---

## When to Use a Monorepo

**Consider a monorepo when:**

- Multiple projects share code that changes frequently together
- You want atomic commits that touch multiple packages simultaneously
- You want to enforce consistent tooling, linting and testing across all projects
- You are building a platform where packages have strong dependencies on each other
- Your team needs to review cross-cutting changes in a single pull request

**Consider a polyrepo when:**

- Projects are genuinely independent with different release cycles
- Different teams need different access control per repository
- Projects use very different technology stacks with no shared code
- You want to keep Git history focused per project

---

## Monorepo Challenges with Git

Pure Git without additional tooling struggles with monorepos at scale.

**Performance.** As the repository grows, `git status`, `git add` and `git checkout` slow down because Git scans all files. Solutions: `core.fsmonitor`, sparse checkout, Git Watchman.

**Build and test efficiency.** Running all tests for every commit is wasteful when a commit only touches one package. You need tooling that understands the dependency graph and runs only what is affected.

**Versioning.** Should all packages share a version or have independent versions? Both approaches are valid but require different tooling.

**CI/CD complexity.** You need pipelines that can detect which packages changed and only build and deploy those.

---

## JavaScript and TypeScript Monorepos

The JavaScript ecosystem has the most mature monorepo tooling.

### npm/yarn/pnpm workspaces

All three major JavaScript package managers support workspaces natively - a built-in mechanism for managing multiple packages in a single repository.

**npm workspaces (`package.json`):**

```json
{
  "name": "my-monorepo",
  "workspaces": [
    "packages/*",
    "apps/*"
  ]
}
```

**pnpm workspaces (`pnpm-workspace.yaml`):**

```yaml
packages:
  - 'packages/*'
  - 'apps/*'
```

With workspaces, running `npm install` at the root installs all packages and sets up symlinks between internal dependencies.

### Turborepo

Turborepo (by Vercel) is the leading build system for JavaScript monorepos. It understands the dependency graph between packages and only rebuilds what changed. It also caches build outputs locally and remotely.

**Install:**

```bash
npx create-turbo@latest
```

**`turbo.json` configuration:**

```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"]
    },
    "lint": {}
  }
}
```

**Run builds only for affected packages:**

```bash
turbo run build --filter=...[HEAD^1]
```

This builds only packages affected by commits since `HEAD^1`.

### Nx

Nx (by Nrwl) is a more opinionated build system with generators, code sharing patterns and deep integration with many frameworks.

```bash
npx create-nx-workspace@latest
```

Nx provides:
- Affected command detection (`nx affected:test`)
- Distributed task execution
- Computation caching
- Dependency graph visualisation (`nx graph`)

---

## Python Monorepos

Python monorepos typically use one of these approaches:

**Poetry with workspaces:**

```toml
# pyproject.toml at root
[tool.poetry.dependencies]
python = "^3.11"

[tool.poetry.dev-dependencies]
packages = [{include = "packages/"}]
```

**Hatch:**

Hatch supports workspaces natively in `hatch.toml`.

**Pants build system:**

Pants is designed for large Python monorepos. It detects which tests to run based on what changed and supports incremental builds.

```bash
pants test packages/utils/tests::
pants lint packages/api/src::
```

---

## General Monorepo Tools

### Bazel

Bazel (open sourced from Google's internal Blaze build system) is the most powerful build system for monorepos. It supports any language, uses a hermetic build model (builds are reproducible and cached) and scales to millions of files.

Used by Google, Stripe, Twitter and others. Steep learning curve but excellent scalability.

### Lerna

Lerna was the original JavaScript monorepo tool. It handles versioning and publishing of packages. In 2022, Nx adopted Lerna and the projects merged. If you see Lerna in an existing project, it is likely paired with Nx or Turborepo now.

### Changesets

Changesets handles versioning and changelog generation in monorepos. Developers add "changeset" files describing what changed and at which semver level. A release process collects these and bumps versions accordingly.

```bash
npx changeset add
npx changeset version
npx changeset publish
```

---

## Git Techniques for Monorepos

### Sparse checkout for large monorepos

If the monorepo is very large, use sparse checkout to only check out the directories you work in:

```bash
git clone --no-checkout https://github.com/YOUR_ORG/monorepo.git
cd monorepo
git sparse-checkout init --cone
git sparse-checkout set packages/utils apps/web
git checkout main
```

Only `packages/utils` and `apps/web` are present in your working directory.

### Filtering commits by directory

View the commit history for a specific package only:

```bash
git log --oneline -- packages/ui-components/
```

### Tagging in a monorepo

Two common approaches:

**Unified versioning** - one tag covers all packages:

```bash
git tag v2.0.0
```

**Independent versioning** - each package has its own tags:

```bash
git tag ui-components-v3.1.0
git tag utils-v1.4.2
git tag api-v5.0.0
```

Changesets and Lerna both handle independent versioning automatically.

### CODEOWNERS for monorepos

Use a `CODEOWNERS` file to automatically assign reviewers based on which directories changed:

```
# .github/CODEOWNERS
packages/ui-components/ @frontend-team
packages/api/           @backend-team
apps/mobile/            @mobile-team
```

Pull requests that touch `packages/ui-components/` automatically request review from `@frontend-team`.

---

## Monorepos at Scale

Google's monorepo (called Piper) contains billions of lines of code. Microsoft's Windows repository contains around 3.5 million files and 300GB of data. Standard Git cannot handle these.

**Microsoft's contribution to Git for monorepos:**

Microsoft developed several Git features specifically for the Windows repository:
- **Scalar** - a Git client that configures a set of optimisations for large repos (sparse checkout, background prefetch, commit graph, fsmonitor)
- **VFS for Git (GVFS)** - virtualises the Git working directory so files are only downloaded when accessed
- **Git Watchman integration** - uses Facebook's Watchman file watcher for faster `git status`

**Enabling Scalar:**

```bash
scalar clone https://github.com/YOUR_ORG/large-monorepo.git
```

Scalar automatically configures sparse checkout, background maintenance, commit graph and other optimisations.

---

## Try It Yourself

This exercise creates a simple JavaScript monorepo with npm workspaces.

**Prerequisites:** Node.js installed.

**Step 1.** Create the monorepo structure:

**Windows (PowerShell):**

```powershell
mkdir monorepo-practice && cd monorepo-practice && git init
```

**Mac and Linux:**

```bash
mkdir monorepo-practice && cd monorepo-practice && git init
```

**Step 2.** Create the root `package.json` with workspaces:

```json
{
  "name": "monorepo-practice",
  "private": true,
  "workspaces": ["packages/*"]
}
```

Save this as `package.json` in the root.

**Step 3.** Create two packages:

**Windows:**

```powershell
mkdir packages/utils && mkdir packages/app
```

**Mac and Linux:**

```bash
mkdir -p packages/utils packages/app
```

Create `packages/utils/package.json`:

```json
{
  "name": "@practice/utils",
  "version": "1.0.0",
  "main": "index.js"
}
```

Create `packages/utils/index.js`:

```javascript
module.exports.greet = (name) => `Hello, ${name}!`;
```

Create `packages/app/package.json`:

```json
{
  "name": "@practice/app",
  "version": "1.0.0",
  "dependencies": {
    "@practice/utils": "*"
  }
}
```

**Step 4.** Install dependencies (sets up workspace symlinks):

```bash
npm install
```

**Step 5.** Check the dependency is linked:

```bash
ls packages/app/node_modules/@practice/
```

You should see `utils` linked from the workspace.

**Step 6.** Commit the structure:

```bash
git add .
git commit -m "add: monorepo structure with utils and app packages"
```

**Step 7.** View the log filtered to one package:

```bash
git log --oneline -- packages/utils/
```

**Step 8.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q monorepo-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf monorepo-practice
```

---

## Common Mistakes

**Trying to use a monorepo without monorepo tooling.**

A monorepo without build tooling (Turborepo, Nx, Pants etc.) means rebuilding everything on every commit and running all tests every time. This is unsustainable beyond a handful of packages. Choose a build tool before committing to a monorepo structure.

**Not considering CI/CD complexity upfront.**

Monorepo CI/CD requires affected detection - knowing which packages to build and test based on what changed. If your CI runs everything on every commit, it will become progressively slower. Plan for affected detection from the start.

**Putting genuinely independent services in a monorepo.**

A monorepo works well for packages with shared code and coordinated releases. If two services have no shared code and completely independent release cycles, a polyrepo is simpler.

**Ignoring `.gitattributes` for line endings in cross-platform monorepos.**

Large monorepos with contributors on Windows and Mac often suffer from spurious line-ending conflicts. Configure `.gitattributes` to normalise line endings consistently.

---

## Summary

- A monorepo stores multiple projects in a single Git repository to enable code sharing, atomic changes and unified tooling
- The choice between monorepo and polyrepo depends on how much code is shared, team structure and tooling maturity
- JavaScript monorepos use npm/yarn/pnpm workspaces combined with Turborepo or Nx for affected builds and caching
- Python monorepos use Poetry workspaces, Hatch or the Pants build system
- Git features for monorepos include sparse checkout, `core.fsmonitor`, Scalar and CODEOWNERS
- Monorepos at scale require specialised tooling - Bazel, Scalar or VFS for Git

---

## Sources and Further Reading

- [Turborepo documentation](https://turbo.build/repo/docs) - the leading monorepo build tool for JavaScript
- [Nx documentation](https://nx.dev) - full-featured monorepo tooling with generators and plugins
- [Changesets](https://github.com/changesets/changesets) - versioning and changelog tool for monorepos
- [Pants build system](https://www.pantsbuild.org) - scalable build for Python monorepos
- [Bazel](https://bazel.build) - Google's monorepo build system for any language
- [Microsoft Scalar](https://github.com/microsoft/scalar) - Git enhancements for large repositories
- [GitHub - CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) - automatic review assignment based on file paths

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

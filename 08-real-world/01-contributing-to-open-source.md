# Contributing to open source

**Difficulty:** 🟢 Beginner to 🟡 Intermediate | **Time:** 30 minutes

---

## Table of contents

- [Introduction](#introduction)
- [How open source contribution works](#how-open-source-contribution-works)
- [Before you contribute](#before-you-contribute)
- [The fork-and-PR workflow](#the-fork-and-pr-workflow)
- [Keeping your fork up to date](#keeping-your-fork-up-to-date)
- [Handling review feedback](#handling-review-feedback)
- [Platform differences: GitHub vs GitLab](#platform-differences-github-vs-gitlab)
- [Project-specific workflows](#project-specific-workflows)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources](#sources)

---

## Introduction

Open source contribution is one of the most valuable things a developer can do. You learn from production codebases, build a public record of your work and directly improve tools that millions of people use. It also has a reputation for being intimidating - complex contribution guides, maintainers requesting changes you do not understand, PRs sitting unanswered for weeks.

This file demystifies the process. The underlying Git workflow is the same on almost every project. Once you understand the fork-and-PR sequence, the `upstream` remote, and how to handle review feedback cleanly, you can contribute to any project on GitHub or GitLab with confidence.

**What you need:**

- Git installed and configured (`git config --global user.name` and `git config --global user.email`)
- A GitHub or GitLab account
- The GitHub CLI (`gh`) or GitLab CLI (`glab`) - optional but recommended
- Basic familiarity with `git clone`, `git add`, `git commit` and `git push`

---

## How open source contribution works

Most open source projects on GitHub and GitLab use the **fork-and-PR model**:

1. You create a personal copy of the repository (a **fork**) under your own account
2. You clone your fork to your local machine
3. You create a branch, make changes and push to your fork
4. You open a **pull request** (GitHub) or **merge request** (GitLab) asking the maintainers to pull your changes into the original repository
5. Maintainers review your changes, request modifications if needed and eventually merge or close the PR

You never push directly to the original repository (called **upstream**). Your fork is your personal workspace. The upstream repository is the source of truth.

This model exists because most open source maintainers cannot and should not give write access to every contributor. The fork model lets anyone contribute without needing special permissions.

---

## Before you contribute

### Read the project's contribution guide

Almost every serious project has a `CONTRIBUTING.md` file at the root of the repository. Read it before doing anything else. It will tell you:

- How to set up the development environment
- What the branch naming convention is
- Whether commits need a specific format (many projects use [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/))
- Whether you need to sign a Contributor Licence Agreement (CLA) - projects like VS Code (Microsoft CLA), React (Meta CLA) and React Native require this before your PR can be merged
- The code review process and typical response times

### Check for an existing issue

Before writing code, search the issue tracker for your bug or feature. If an issue exists, comment to say you are working on it. If it does not exist, open one. This avoids duplicate work and gives maintainers a chance to tell you if the change fits the project's direction.

### Start small

Your first contribution to a project does not need to be a major feature. Fixing a typo in the docs, improving an error message, or adding a test for an uncovered edge case is a perfectly valid first PR. Small PRs are reviewed faster, merged more often, and teach you the project's conventions without the risk of a large rework request.

---

## The fork-and-PR workflow

### Step 1: Fork the repository

**Using the GitHub CLI (recommended):**

```bash
gh repo fork OWNER/REPO --clone=true --remote=true
cd REPO
```

The `--remote=true` flag automatically sets up:

- `origin` pointing to your fork (`https://github.com/YOU/REPO.git`)
- `upstream` pointing to the original (`https://github.com/OWNER/REPO.git`)

**Using the web interface then CLI:**

On GitHub: click **Fork** at the top right of the repository page, then **Create fork**.

On GitLab: click **Fork** at the top right of the project page.

Then clone your fork:

```bash
# 🪟 Windows / 🍎 Mac / 🐧 Linux
git clone git@github.com:YOU/REPO.git
cd REPO
git remote add upstream https://github.com/OWNER/REPO.git
```

Verify both remotes are set correctly:

```bash
git remote -v
# origin    git@github.com:YOU/REPO.git (fetch)
# origin    git@github.com:YOU/REPO.git (push)
# upstream  https://github.com/OWNER/REPO.git (fetch)
# upstream  https://github.com/OWNER/REPO.git (push)
```

### Step 2: Create a branch

Always branch off the upstream default branch, not your local `main`. This ensures your branch starts from the latest state of the project.

```bash
git fetch upstream
git switch -c feat/short-description upstream/main
```

> [!TIP]
> Use a branch name that describes what you are doing, not who you are. `feat/add-dark-mode` is good. `isaacs-changes` is not. Many projects require a specific prefix like `feat/`, `fix/`, `docs/` or `chore/`.

### Step 3: Make your changes

Work on your branch. Commit regularly with clear messages. The [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) format is widely adopted:

```
<type>[optional scope]: <description>

feat(auth): add OAuth2 login support
fix(parser): handle empty input without crashing
docs(readme): update installation instructions
```

Stage changes carefully with `git add -p` (interactive patch mode) to review every chunk before committing. This prevents accidentally committing debug logs, temporary files or unrelated changes.

```bash
git add -p
git commit -m "feat(auth): add OAuth2 login support"
```

### Step 4: Push your branch

```bash
git push -u origin feat/short-description
```

The `-u` flag sets `origin/feat/short-description` as the tracking branch so future `git push` commands need no arguments.

### Step 5: Open the pull request

**Using the GitHub CLI:**

```bash
gh pr create --fill --web
```

`--fill` uses the branch name and last commit message to pre-fill the title and body. `--web` opens the PR form in your browser so you can review and edit before submitting.

To open a draft PR (not ready for review):

```bash
gh pr create --fill --draft
```

**Using the GitLab CLI:**

```bash
glab mr create --fill --draft --remove-source-branch
```

**Manually via the web:**

After pushing, both GitHub and GitLab will show a banner at the top of your fork with a button to open a PR/MR. Click it, fill in the title and description and submit.

### Writing a good PR description

A good PR description answers three questions:

1. What does this change do?
2. Why is this change needed?
3. How did you test it?

If there is a related issue, link it: `Closes #123` on GitHub automatically closes the issue when the PR merges. On GitLab use `Closes #123` in the MR description or `git commit -m "fix: ... Closes #123"`.

---

## Keeping your fork up to date

Over time the upstream repository will receive commits that your fork does not have. If you do not sync regularly, your branches will fall behind and your PRs will have conflicts.

### Syncing your fork's main branch

```bash
git fetch upstream
git switch main
git rebase upstream/main
git push origin main
```

> [!NOTE]
> Some guides use `git merge upstream/main` instead of `git rebase`. Rebase produces a cleaner linear history on your fork. Either works for syncing `main`.

**Using the GitHub CLI (one command):**

```bash
gh repo sync YOU/REPO -b main
```

**Using the GitLab web interface:**

Open your fork on GitLab, click the **...** menu next to the default branch, and select **Update fork**.

### Rebasing your feature branch onto the latest upstream

When `upstream/main` has moved on while you were working on your branch:

```bash
git fetch upstream
git switch feat/short-description
git rebase upstream/main
```

If there are conflicts, Git will pause and list them:

```bash
# Resolve conflicts in your editor, then:
git add <resolved-files>
git rebase --continue

# To abandon the rebase and go back:
git rebase --abort
```

After rebasing a pushed branch, the local and remote histories have diverged. Push with `--force-with-lease`:

```bash
git push --force-with-lease
```

> [!CAUTION]
> Never use plain `git push --force` on a branch someone else may have cloned. `--force-with-lease` checks that the remote has not moved since your last fetch and refuses if it has. This prevents overwriting a collaborator's work.

---

## Handling review feedback

When a maintainer requests changes, the professional pattern is **fixup commits during review, squash before merge**.

### Adding fixup commits

For each review comment you address, create a fixup commit targeting the specific original commit:

```bash
# Find the SHA of the commit the reviewer is commenting on
git log --oneline

# Create a fixup commit
git commit --fixup=abc1234
git push --force-with-lease
```

A fixup commit is automatically named `fixup! <original commit message>`. The reviewer sees your incremental changes rather than a monolithic re-push, which makes re-review much faster.

### Squashing before merge

Once the PR is approved, squash the fixup commits before the maintainer merges:

```bash
git rebase -i --autosquash upstream/main
git push --force-with-lease
```

`--autosquash` moves all `fixup!` commits immediately after their targets and marks them as `fixup` in the interactive rebase editor. You review the result, save and Git does the rest.

> [!TIP]
> Set `git config --global rebase.autoSquash true` once and you never need to pass `--autosquash` again.

### For a single small fix

If the reviewer points out a single typo or small nit, amending is fine:

```bash
git commit --amend --no-edit
git push --force-with-lease
```

### Responding to review comments

Always respond to every comment, even if you just write "Done" or "Fixed in abc1234". Maintainers review many PRs and need to know which threads are resolved. On GitHub you can click **Resolve conversation** on each thread. On GitLab you can click **Resolve thread**.

If you disagree with a suggestion, say so politely and explain your reasoning. Good maintainers welcome discussion. What they do not welcome is silence.

---

## Platform differences: GitHub vs GitLab

| Feature                                 | GitHub                                       | GitLab                                                                         |
| --------------------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------ |
| Term for a contribution request         | Pull request (PR)                            | Merge request (MR)                                                             |
| CLI tool                                | `gh`                                         | `glab`                                                                         |
| Maintainer can push to your fork branch | Opt-in per PR ("Allow edits by maintainers") | Default-on via "Allow commits from members who can merge to the target branch" |
| Automatic issue closing                 | `Closes #123` in PR body                     | `Closes #123` in MR body or commit message                                     |
| Draft contributions                     | Draft PR                                     | Draft MR                                                                       |
| Fork sync                               | `gh repo sync YOU/REPO -b main` or UI button | UI button or `glab repo sync`                                                  |

> [!NOTE]
> GitHub's "Allow edits by maintainers" setting is silently ignored on PRs from organisation-owned forks. If you fork a repo owned by an organisation (not a personal account), maintainers cannot push to your fork branch even if the option is ticked. This is a known GitHub limitation.

---

## Project-specific workflows

Not every open source project uses the GitHub/GitLab fork-and-PR model. Here are the three most common alternatives.

### The Linux kernel - email patches

The Linux kernel does not accept PRs from most contributors. Changes are submitted as email patches to mailing lists archived at [lore.kernel.org](https://lore.kernel.org/).

The modern tool for this is **b4** (`pip install b4`), which handles patch preparation, version bumps, trailer collection and sending.

```bash
# Find who to send your patch to
./scripts/get_maintainer.pl -f drivers/net/ethernet/intel/e1000/

# Prepare patches (commits on your branch vs origin/master)
git format-patch -o outgoing/ origin/master..

# Send with b4 (handles threading, cover letters, version markers)
b4 send outgoing/

# Each commit needs a DCO sign-off
git commit -s -m "net: e1000: fix descriptor ring overflow"
```

Signed-off-by lines declare that your contribution is covered by the Developer's Certificate of Origin (DCO). The full process is documented at [docs.kernel.org/process/submitting-patches.html](https://docs.kernel.org/process/submitting-patches.html).

### Kubernetes - Prow and OWNERS files

Kubernetes uses **Prow** (a Kubernetes-native CI/CD system) and chat-ops commands on PRs. A bot called `@k8s-ci-robot` handles automation. The approval flow:

1. A reviewer comments `/lgtm` (Looks Good To Me) - adds the `lgtm` label
2. An owner (listed in the relevant `OWNERS` file) comments `/approve` - adds the `approved` label
3. **Tide** (the Prow merge bot) automatically merges the PR when it has both labels and all required checks pass

> [!IMPORTANT]
> On Kubernetes, never click the green Merge button on GitHub. Always let Tide handle merges. Manual merges bypass Tide's batching and can break the merge queue.

All contributors must sign the CNCF CLA via the PR bot before their first PR can be merged.

### Rust - bors

The Rust project uses **bors** for merge automation. After a reviewer approves:

```
@bors r+           # approve and queue for merge
@bors r+ rollup    # approve and include in the next batch merge
@bors try          # trigger a speculative CI build without merging
@bors delegate     # grant the PR author permission to approve their own PR
```

Bors tests PRs against the current `master` tip before merging, preventing CI from going red due to ordering issues between concurrent PRs.

---

## Try it yourself

This exercise uses the [git-unlocked first-contribution sandbox](https://github.com/zaccesss/git-unlocked/tree/main/11-first-contribution) - a file specifically set up for this purpose.

### Exercise

1. Fork the `git-unlocked` repository using `gh repo fork zaccesss/git-unlocked --clone=true --remote=true`
2. Create a branch: `git switch -c add-my-name upstream/main`
3. Open `11-first-contribution/contributors.md` and add your GitHub username on a new line
4. Commit: `git commit -am "docs(first-contribution): add YOUR_USERNAME to contributors"`
5. Push: `git push -u origin add-my-name`
6. Open a PR: `gh pr create --fill`
7. Check your PR appears on the repository's PR list

**Bonus:** After opening the PR, make a small change (fix a typo, improve your entry) and practice the fixup commit workflow: `git commit --fixup=HEAD`, then `git push --force-with-lease`.

---

## Common mistakes

**Branching off your local `main` instead of `upstream/main`.**
If `upstream/main` has moved on and your local `main` is stale, you will include commits in your PR that the maintainer did not ask for. Always `git fetch upstream` first, then `git switch -c feat/name upstream/main`.

**Committing directly to `main` on your fork.**
This creates a mess when you need to sync with upstream. Keep `main` clean and always work on feature branches.

**Using `git push --force` instead of `git push --force-with-lease`.**
Plain `--force` overwrites the remote branch unconditionally. If a maintainer pushed a suggestion to your branch (allowed on some projects), `--force` destroys it. `--force-with-lease` checks first and refuses if the remote moved.

**Opening a PR against the wrong base branch.**
By default, GitHub opens PRs against the upstream default branch, which is usually what you want. But some projects have a `dev` or `next` branch as the contribution target. Check `CONTRIBUTING.md` for the correct base.

**Squashing commits before review is complete.**
Wait until the PR is approved before squashing fixup commits. Reviewers need to see the incremental changes to re-review efficiently. Squashing mid-review makes their job harder.

**Forgetting to sign commits on projects that require DCO.**
The Linux kernel, CNCF projects and many others require a `Signed-off-by:` trailer. Add it to every commit with `git commit -s`. You cannot retroactively add it easily after pushing, so build the habit from the start.

**Not reading `CONTRIBUTING.md`.**
This is the most common mistake. Every project is different. Some require squash merges, some require merge commits, some have a specific test command to run before opening a PR, some have a CLA to sign. Read it first.

---

## Summary

Contributing to open source follows the same workflow on almost every project:

- Fork the repository so you have your own copy to push to
- Add the original as `upstream` and always branch from `upstream/main`
- Use `git fetch upstream && git rebase upstream/main` to keep your branch current
- Push with `--force-with-lease` after any history-rewriting operation, never with plain `--force`
- Use fixup commits during review so maintainers can re-review incrementally, then squash before merge with `git rebase -i --autosquash`
- Always read `CONTRIBUTING.md` - every project has its own conventions

Three major projects diverge from the standard model: the Linux kernel uses email patches via `b4`, Kubernetes uses chat-ops through Prow, and Rust uses bors for merge automation. All three are well-documented and follow predictable patterns once you know what to look for.

---

## Sources and Further Reading

- [GitHub: forking a repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)
- [GitHub: syncing a fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork)
- [GitHub: about pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)
- [GitLab: merge requests](https://docs.gitlab.com/user/project/merge_requests/)
- [Conventional Commits specification](https://www.conventionalcommits.org/en/v1.0.0/)
- [Linux kernel: submitting patches](https://docs.kernel.org/process/submitting-patches.html)
- [b4 documentation](https://b4.docs.kernel.org/)
- [Kubernetes: contributor guide](https://www.kubernetes.dev/docs/guide/)
- [Kubernetes: OWNERS files](https://www.kubernetes.dev/docs/guide/owners/)
- [Rust: forge documentation](https://forge.rust-lang.org/)
- [VS Code: how to contribute](https://github.com/microsoft/vscode/wiki/How-to-Contribute)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

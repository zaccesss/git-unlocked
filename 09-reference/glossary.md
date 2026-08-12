# Git glossary

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

Every term you will encounter in Git, GitHub, GitLab and version control work - defined plainly.

---

## Table of contents

- [A](#a)
- [B](#b)
- [C](#c)
- [D](#d)
- [E](#e)
- [F](#f)
- [G](#g)
- [H](#h)
- [I](#i)
- [J](#j)
- [K](#k)
- [L](#l)
- [M](#m)
- [N](#n)
- [O](#o)
- [P](#p)
- [R](#r)
- [S](#s)
- [T](#t)
- [U](#u)
- [V](#v)
- [W](#w)
- [Sources and Further Reading](#sources-and-further-reading)

---

## A

**Annotated tag**
A tag that stores extra metadata: the tagger's name and email, a timestamp and a message. Annotated tags are full Git objects and can be signed. Created with `git tag -a`. Compare with lightweight tag.

**Atomic commit**
A commit that represents a single, complete, self-contained change. The code compiles and tests pass after applying the commit on its own. Atomic commits make `git bisect`, code review and `git revert` dramatically simpler.

**Author**
The person who originally wrote the changes in a commit. Stored alongside the author date in the commit object. Distinguished from the committer, which is the person who actually created the commit object (they differ when patches are applied by a maintainer).

---

## B

**Bare repository**
A Git repository with no working tree. It contains only the `.git` directory contents at the root level. Used for central servers and remote hosting. Created with `git init --bare`. You cannot commit directly to a bare repo.

**Base branch**
The branch a pull request or merge request targets. Commonly `main` or `develop`. Changes in the head branch will be merged into the base branch on approval.

**Binary file**
A file whose content is not human-readable text (images, compiled objects, archives, PDFs). Git stores each version as a complete blob rather than a diff. Large binary files in Git history are the most common cause of repository bloat; use Git LFS for them.

**Bisect**
A Git command (`git bisect`) that uses binary search to find the commit that introduced a bug or regression. You mark commits as `good` or `bad`; Git checks out the midpoint each time. Can be automated with `git bisect run <script>`.

**Blob**
A Git object type that stores the raw content of a file at a specific point in time. The blob contains no filename or path - that relationship is stored in the tree object. The blob's SHA-1 (or SHA-256 in newer repos) is derived from the content, so identical file content always produces the same blob.

**Branch**
A lightweight, movable pointer to a commit. Creating a branch creates a new pointer; it does not copy any files. The active branch pointer advances automatically when you commit. Branches are nearly free in Git - create them liberally.

**Branch protection**
Rules that control who can push to a branch, whether PRs are required, whether CI must pass and whether force pushes are allowed. On GitHub, implemented via Rulesets (current) or classic branch protection (legacy). On GitLab, via protected branches and push rules.

---

## C

**Checkout**
Historically, the `git checkout` command that switches branches or restores files. Split into `git switch` (change branches) and `git restore` (restore file content) in Git 2.23. Still works but the new commands are clearer.

**Cherry-pick**
Applying the changes from a specific commit onto the current branch, creating a new commit with the same diff but a different SHA. Used for backporting fixes and selectively applying changes. See `git cherry-pick`.

**CI/CD**
Continuous Integration / Continuous Delivery (or Deployment). CI runs automated builds and tests on every push. CD automates delivery to staging or production. Git triggers CI by push events; the results of CI checks frequently gate merges via branch protection rules.

**Clone**
A local copy of a remote repository, including all history, branches and tags. `git clone` creates the copy and configures `origin` as the remote pointing back to the source. Compare with fork.

**Commit**
A Git object that records a snapshot of the repository at a point in time. Contains a pointer to a tree (the root directory snapshot), one or more parent commit SHAs, author, committer, timestamps and a message. Commits are immutable - amending or rebasing creates new commit objects.

**Commit graph**
A binary cache of the commit DAG (directed acyclic graph) stored in `.git/objects/info/commit-graphs/`. Speeds up operations like `git log`, `git merge-base` and reachability checks. Built with `git commit-graph write --reachable`. Extended with changed-path Bloom filters to accelerate `git log -- <path>`.

**Commit SHA**
The unique identifier for a commit, derived by hashing the commit object's contents with SHA-1 (or SHA-256 in newer repos). Displayed as a 40-character hex string; usually abbreviated to the first 7-12 characters. Because it's content-derived, the same SHA always represents the same commit.

**Committer**
The person who created a commit object. Differs from the author when patches submitted by email are applied by a maintainer (common in the Linux kernel workflow). Both author and committer are stored with name, email and timestamp.

**Conflict**
A situation where Git cannot automatically merge changes because two branches modified the same part of the same file in incompatible ways. Git marks the conflicting lines with `<<<<<<<`, `=======` and `>>>>>>>` markers. You resolve conflicts by editing the file and staging the result.

**CODEOWNERS**
A file that maps repository paths to the GitHub users or teams responsible for reviewing changes to those paths. Stored at `.github/CODEOWNERS`, `CODEOWNERS` or `docs/CODEOWNERS`. Also supported by GitLab (Premium/Ultimate). When a PR touches a CODEOWNERS path, listed owners are auto-requested as reviewers.

**Conventional Commits**
A commit message format: `<type>[scope]: <description>`. Types include `feat`, `fix`, `docs`, `refactor`, `test`, `chore`. A `!` suffix or `BREAKING CHANGE:` footer marks breaking changes. Enables automatic CHANGELOG generation and semantic versioning.

---

## D

**DAG (Directed Acyclic Graph)**
The data structure that represents Git history. Each commit points to one or more parent commits, forming a graph where edges go in one direction and there are no cycles. The DAG structure is why merges and rebases work the way they do.

**DCO (Developer Certificate of Origin)**
A declaration that you have the right to submit a contribution under the project's licence. Added with `git commit -s`, which appends a `Signed-off-by: Name <email>` trailer. Required by the Linux kernel, CNCF projects and many open source foundations.

**Default branch**
The branch a repository shows by default and that new clones check out. Historically `master`; the current convention is `main`. Changed at repository creation or via platform settings.

**Delta compression**
A storage optimisation in pack files where Git stores similar objects as a base object plus a binary diff (delta). Happens automatically during garbage collection and `git push`. Reduces repository size dramatically for codebases with many similar versions of files.

**Deploy key**
An SSH key granted access to a single repository (read or read-write). Safer than using a personal account's SSH key or a PAT when a server needs to clone or push to one repository.

**Detached HEAD**
A state where HEAD points directly to a commit SHA rather than to a named branch. Caused by `git checkout <sha>` or `git checkout <tag>`. Any commits made in this state are unreachable once you switch away unless you create a branch first. Use `git switch -c rescue-branch` to save work from a detached HEAD.

**Diff**
The difference between two versions of a file or repository. `git diff` shows unstaged changes; `git diff --staged` shows staged changes; `git diff A..B` compares two refs. The `--word-diff`, `--stat` and `--histogram` options modify output style.

**Draft PR / Draft MR**
A pull request or merge request explicitly marked as not ready for review. Useful for sharing work in progress, getting early CI feedback or unblocking dependent work. On GitHub: "Convert to draft"; on GitLab: prefix title with `Draft:` or click "Mark as draft".

---

## E

**Environment branch**
A long-lived branch representing a deployment environment (e.g. `staging`, `production`). Part of GitLab Flow. Changes flow from `main` upstream; environment branches only receive cherry-picks or merges from upstream, never direct feature commits.

---

## F

**Fast-forward**
A merge where the target branch's HEAD can simply be moved forward to the incoming branch's tip, because there is no divergence. No merge commit is created. Happens when you have not made any commits since branching. `git merge --ff-only` aborts if a fast-forward is not possible.

**Feature flag**
A runtime condition that shows or hides a feature without a code deployment. Used in trunk-based development to merge incomplete features to `main` without exposing them to users. Allows incremental delivery and instant rollback without a Git revert.

**Fetch**
Downloading objects and refs from a remote repository into your local copy without integrating them into your working tree or current branch. `git fetch` is safe - it never modifies your local branches. Compare with `git pull`, which fetches and then merges or rebases.

**Filter-repo**
The current recommended tool (`git filter-repo`) for rewriting Git history, replacing the deprecated `git filter-branch`. Used to remove files from history, replace sensitive text or restructure a repository. Much faster and safer than filter-branch.

**Force push**
Overwriting a remote branch with your local state regardless of what is on the remote (`git push --force`). Destructive if others have fetched the branch. Always prefer `git push --force-with-lease`, which aborts if the remote has moved since your last fetch.

**Fork**
A copy of a repository under a different account, connected to the original (upstream) at the platform level. Used in open source contribution: you fork, clone your fork, push to your fork, then open a pull request to the upstream. Compare with clone, which is a local copy with no platform-level connection.

**fsmonitor**
A daemon that watches the filesystem for changes and tells Git exactly which files changed, avoiding the need to stat every tracked file. Reduces `git status` from seconds to milliseconds on large repositories. Enable with `git config core.fsmonitor true` (Git 2.37+ for the built-in daemon).

---

## G

**Garbage collection**
The process of cleaning up unreachable or loose objects and repacking the repository for efficiency. Run automatically by Git as a background task. Triggered manually with `git gc`. Important: do not run `git gc` immediately after a disaster - it permanently removes unreachable objects that the reflog might still help you recover.

**GitFlow**
A branching strategy using five branch types: `main`, `develop`, `feature/*`, `release/*` and `hotfix/*`. Designed for software that ships versioned releases. Not recommended for teams doing continuous deployment.

**GitHub Flow**
A simple branching strategy: `main` is always deployable; all work happens on short-lived feature branches merged via pull requests. Well-suited to SaaS and web applications.

**GitLab Flow**
An extension of GitHub Flow that adds environment branches or release branches for staged deployments. Changes flow upstream-first from `main` to environment branches via cherry-picks.

**GitOps**
The practice of using a Git repository as the single source of truth for the desired state of infrastructure and applications. A software agent continuously reconciles actual state to match the repository. The four OpenGitOps principles: declarative, versioned, pulled automatically, continuously reconciled.

---

## H

**HEAD**
A special reference that points to the current branch (or directly to a commit in detached HEAD state). Moving HEAD is what happens when you commit, switch branches or check out a specific commit. `HEAD~1` means "one commit before HEAD"; `HEAD^` is equivalent.

**Hook**
A script that Git runs automatically at specific points in its workflow. Client-side hooks include `pre-commit` (before a commit is created), `commit-msg` (after the message is entered) and `pre-push` (before a push). Server-side hooks include `pre-receive` and `post-receive`. Hooks live in `.git/hooks/` and must be executable. Not version-controlled by default; use the `pre-commit` framework to manage them.

**Hunk**
A contiguous block of changed lines within a diff. `git add -p` lets you stage individual hunks rather than whole files.

---

## I

**Index**
Another name for the staging area - the intermediate state between your working tree and your repository. `git add` moves changes into the index. `git commit` creates a commit from the index. `git diff --staged` shows the difference between the index and HEAD.

**Interactive rebase**
`git rebase -i` opens an editor listing commits you can reorder, rename (`reword`), edit, squash together, fixup (squash without keeping message) or drop entirely. The most powerful tool for cleaning up commit history before opening a pull request.

---

## J

**Just-in-time merge**
A pattern where feature branches stay open until the last possible moment before release, merging only when the feature is fully tested and approved. Common in GitFlow.

---

## K

**Keyless signing**
Commit signing using short-lived certificates from a public certificate authority (Sigstore's Fulcio) rather than long-lived GPG or SSH keys. The certificate is bound to an OIDC identity (GitHub Actions, Google, Microsoft) and the signature is logged to a transparency log (Rekor). Implemented by `gitsign`.

---

## L

**Large File Storage (LFS)**
A Git extension that stores large binary files (videos, images, models, compiled artifacts) outside the Git object database, replacing them in the repo with pointer files. The actual files live on a separate LFS server. Avoids the repository size problems that come from committing large binaries directly. Install with the `git-lfs` package.

**Lightweight tag**
A tag that is simply a named pointer to a commit, with no extra metadata or signature. Created with `git tag v1.0`. Compare with annotated tag.

---

## M

**Mainline**
The primary integration branch - typically `main` or `master`. In trunk-based development, everyone integrates to the mainline frequently (daily or more). The parent number passed to `git revert -m` specifies which parent is the mainline when reverting a merge commit.

**Merge**
Combining the history of two branches. A merge commit has two or more parents. `git merge --no-ff` always creates a merge commit; `git merge --ff-only` fails if one cannot be avoided; `git merge --squash` collapses the incoming branch to a single commit.

**Merge conflict**
See Conflict.

**Merge queue**
A CI system feature that queues approved PRs and tests each one against the actual target-branch state it will land in (including earlier queued PRs). Prevents the case where two individually-green PRs combine to break the branch. GitHub Merge Queue (GA July 2023); GitLab Merge Trains (Premium).

**Merge request (MR)**
GitLab's term for a pull request. A proposal to merge changes from one branch into another, with code review, CI checks and discussion attached.

**Monorepo**
A single Git repository containing multiple projects, packages or services. Requires performance optimisations (sparse checkout, partial clone, fsmonitor) and task runner tooling (Nx, Turborepo, Bazel) at scale.

**Multi-pack index (MIDX)**
A single index file covering multiple pack files, improving performance for repositories with many pack files. Enabled automatically by `git maintenance start`.

---

## N

**Non-fast-forward push**
A push that would overwrite history on the remote (because your branch has diverged). Git rejects this by default. Requires `--force` or `--force-with-lease` to proceed.

---

## O

**Object**
The fundamental storage unit in Git. There are four types: blob (file content), tree (directory listing), commit (snapshot with metadata) and tag (annotated tag). Each object is identified by the SHA hash of its content.

**ORIG_HEAD**
A reference Git sets automatically before destructive operations like merge, rebase and reset. Allows you to undo immediately with `git reset --hard ORIG_HEAD`.

**Orphan branch**
A branch with no parent commits - a completely independent history. Created with `git switch --orphan new-branch`. Used for deploying to GitHub Pages (`gh-pages` branch) or separating documentation from code history.

**Ort**
The default merge strategy since Git 2.34, replacing the older "recursive" strategy. Significantly faster and more correct on complex merge scenarios.

---

## P

**Pack file**
A binary file storing multiple Git objects in compressed, delta-encoded form. Created by `git gc`, `git repack` and automatically during `git push`. A repository with many pack files is slower; `git maintenance start` handles periodic repacking.

**Partial clone**
Cloning a repository while deferring the download of certain object types. `--filter=blob:none` downloads blobs on demand; `--filter=tree:0` downloads trees and blobs on demand. Objects are fetched transparently when accessed. Dramatically reduces clone time on large repositories.

**PAT (Personal Access Token)**
A token used to authenticate with GitHub or GitLab in place of a password. Fine-grained PATs (GitHub, GA March 2025) are scoped to specific repositories and permissions with mandatory expiry. Classic PATs are broader and should be retired in favour of fine-grained PATs.

**Patch**
A text file representing a diff that can be applied with `git am` or `patch`. The traditional contribution format for the Linux kernel workflow. Created with `git format-patch`.

**Pipeline**
An automated sequence of jobs triggered by a Git event (push, PR, tag). On GitHub this is a GitHub Actions workflow (`.github/workflows/`); on GitLab it is a CI/CD pipeline (`.gitlab-ci.yml`); on Bitbucket it is a Bitbucket Pipeline (`bitbucket-pipelines.yml`).

**Pull request (PR)**
GitHub's term for a merge request. A proposal to merge changes from one branch into another, with code review, CI checks, comments and approval tracking attached. Also used by Bitbucket and Gitea.

**Push**
Sending local commits to a remote repository. `git push` updates the remote branch to match your local branch. Rejected by default if the push would overwrite history.

---

## R

**Reachable**
A commit is reachable if it can be reached by traversing parent links from a named ref (branch, tag, HEAD). Unreachable commits are candidates for deletion by `git gc`. The reflog keeps unreachable commits accessible for a grace period (30 days by default).

**Rebase**
Replaying commits from one branch on top of another base commit, producing a linear history. Each replayed commit gets a new SHA because its parent has changed. `git rebase main` moves your branch on top of the latest `main`. Interactive rebase (`git rebase -i`) allows reordering, squashing and editing commits.

**Reflog**
A local log of every movement of HEAD and branch tips. Records operations like commits, resets, rebases, merges and branch switches. The primary recovery tool after accidental data loss. Retains reachable entries for 90 days and unreachable entries for 30 days.

**Remote**
A named reference to another Git repository, typically on a server. `origin` is the conventional name for the repository you cloned from. `upstream` is the conventional name for the original repository when working from a fork. Managed with `git remote`.

**Remote-tracking ref**
A local reference that records the last known state of a branch on a remote. Stored as `refs/remotes/origin/main` etc. Updated by `git fetch`. Not a local branch - you cannot commit to it directly.

**rerere**
"Reuse Recorded Resolution". When enabled (`git config rerere.enabled true`), Git remembers how you resolved a merge conflict and automatically reapplies the same resolution the next time it encounters the same conflict. Especially useful during long-lived rebases.

**Repository**
A directory containing a `.git` folder. The `.git` folder is the actual Git repository (objects, refs, config, hooks). The directory containing it is the working tree. A bare repository has no working tree.

**Ruleset**
GitHub's current branch and tag protection system (GA 2023), superseding classic branch protection. Rulesets stack (multiple can apply to one branch), apply at the organisation level, enforce commit metadata, cover the fork network and support an Evaluate mode for dry-running rules.

---

## S

**Scalar**
A Git addon from Microsoft (bundled since Git 2.38) that applies all monorepo performance optimisations in a single command. `scalar clone <url>` configures partial clone, sparse checkout, commit-graph, multi-pack index, fsmonitor and background maintenance automatically.

**Shallow clone**
A clone that downloads only a limited number of recent commits rather than the full history. Created with `--depth N`. Useful for CI builds that only need the latest code. Does not support `git bisect` or other history operations that need full reachability.

**Signing**
Attaching a cryptographic signature to a commit or tag to prove it was created by a specific key. Supported via GPG, SSH (Git 2.34+) or Sigstore/gitsign. GitHub and GitLab show a "Verified" badge on signed commits. Required by some organisations via branch protection rules.

**Sparse checkout**
Populating only a subset of the working tree. Files outside the sparse set are not materialised on disk but still exist in the repository. Cone mode (directory-prefix matching) is much faster than non-cone mode on large repositories. Enabled with `git sparse-checkout init --cone`.

**Squash**
Combining multiple commits into one. `git merge --squash` collapses an entire branch to a single commit before merging. `git rebase -i` with `squash` or `fixup` actions combines selected commits. Squash-and-merge is a common pull request merge strategy.

**Staging area**
See Index.

**Submodule**
A pointer from one Git repository to a specific commit in another repository. Used to include external dependencies or shared libraries in a project. The submodule pointer is a file in the parent repository. Updated with `git submodule update --remote`.

---

## T

**Tag**
A named ref pointing to a specific commit. Used to mark releases (`v1.2.3`). Lightweight tags are simple pointers; annotated tags are full objects with metadata and optional signatures.

**Three-dot diff (`A...B`)**
A diff showing what changed on branch B since it diverged from branch A. Equivalent to `git diff $(git merge-base A B) B`. This is the "PR diff" - it shows only the changes in the feature branch, not changes on `main` that arrived after branching.

**Tracking branch**
A local branch configured to follow a remote-tracking ref. When you run `git push` or `git pull` without arguments, Git uses the tracking relationship to know where to push or pull from. Set with `git push -u origin branch-name` or `git branch --set-upstream-to`.

**Tree**
A Git object that represents a directory. Contains a list of entries, each being either a blob (file) or another tree (subdirectory), with mode and name. The root tree is pointed to by each commit.

**Trunk-based development**
A branching strategy where all developers commit to a single long-lived branch (the "trunk", usually `main`), either directly or via short-lived branches (lifetime: hours to 2 days). Incomplete work is hidden behind feature flags. Correlated with elite DevOps performance by DORA research.

**Two-dot diff (`A..B`)**
A diff showing the difference between the tips of two refs. `git log A..B` shows commits in B that are not in A. Different from three-dot, which anchors at the merge base.

---

## U

**Untracked file**
A file in the working tree that Git is not tracking (has never been `git add`-ed). Shown in `git status`. Ignored if the filename matches a pattern in `.gitignore`.

**Upstream**
The remote repository that a fork was created from. By convention, the remote named `upstream` points to the original repository; `origin` points to your fork. Used to sync your fork: `git fetch upstream && git rebase upstream/main`.

---

## V

**Verified commit**
A commit with a cryptographic signature that the platform (GitHub or GitLab) has validated against a known public key. Shown with a green "Verified" badge. Can be required via branch protection rules or push rules.

**Vigilant mode**
A GitHub setting that marks all commits attributed to your account as "Verified", "Partially verified" or "Unverified". Useful for detecting impersonation - commits not signed with your key show as unverified even if the author name and email match yours.

---

## W

**Working tree**
The directory containing your actual files, checked out from the repository. What you see in your file manager. Distinguished from the repository (the `.git` folder). Modified files in the working tree show as unstaged changes in `git status`.

**Worktree**
An additional working directory linked to the same repository. Created with `git worktree add ../path branch-name`. Allows working on multiple branches simultaneously without stashing or committing. Each worktree has its own HEAD, index and working tree but shares the object store.

---

## Sources and Further Reading

- [git-scm.com: Git glossary](https://git-scm.com/docs/gitglossary)
- [git-scm.com: Pro Git - Git internals](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain)
- [GitHub: glossary](https://docs.github.com/en/get-started/quickstart/github-glossary)
- [GitLab: glossary](https://docs.gitlab.com/ee/user/project/merge_requests/reviews/)
- [OpenGitOps: glossary](https://github.com/open-gitops/documents/blob/main/GLOSSARY.md)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

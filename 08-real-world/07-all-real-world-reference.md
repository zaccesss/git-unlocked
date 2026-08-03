# Real-world Git reference

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

A consolidated quick-reference for every command and pattern from this section.

---

## Table of contents

- [Open source contribution](#open-source-contribution)
- [Team workflows](#team-workflows)
- [GitOps](#gitops)
- [Monorepos](#monorepos)
- [Platform migration](#platform-migration)
- [Disaster recovery](#disaster-recovery)
- [Secret detection](#secret-detection)
- [Pro Git config](#pro-git-config)
- [Essential aliases](#essential-aliases)
- [Quick decision guide](#quick-decision-guide)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Open source contribution

### Fork and clone

```bash
# Fork and clone in one step (GitHub CLI)
gh repo fork OWNER/REPO --clone=true --remote=true

# Manual equivalent
git clone git@github.com:YOU/REPO.git
cd REPO
git remote add upstream https://github.com/OWNER/REPO.git
git remote -v
```

### Branch from upstream

```bash
git fetch upstream
git switch -c feat/short-description upstream/main
```

### Sync fork with upstream

```bash
# Rebase approach (cleaner history)
git fetch upstream
git switch feat/my-branch
git rebase upstream/main

# One-liner via GitHub CLI
gh repo sync YOU/REPO -b main
```

### Push safely after rebase

```bash
git push --force-with-lease       # never plain --force
```

### Fixup commits during review

```bash
git commit --fixup=<sha>          # create fixup targeting specific commit
git push --force-with-lease       # reviewer sees incremental diff

# Squash before merge
git rebase -i --autosquash upstream/main
git push --force-with-lease
```

### Sign commits with DCO

```bash
git commit -s -m "fix: correct null pointer"   # adds Signed-off-by trailer
```

### Format patches (Linux kernel style)

```bash
git format-patch -o outgoing/ origin/master..
b4 send outgoing/
```

---

## Team workflows

### Branching strategy decision

```
Deploy continuously from one version?   → GitHub Flow or trunk-based
Need multiple live versions?            → GitFlow or GitLab Flow (release branches)
Staged environments (dev/staging/prod)? → GitLab Flow
Small fast-moving team?                 → GitHub Flow
Versioned installers or mobile apps?   → GitFlow
```

### Conventional Commits format

```
feat(scope): add new feature            → MINOR version bump
fix(scope): fix a bug                   → PATCH version bump
docs(scope): update documentation       → no version bump
refactor(scope): restructure code       → no version bump
feat(scope)!: breaking change           → MAJOR version bump

BREAKING CHANGE: description in footer → MAJOR version bump
```

### CODEOWNERS (GitHub)

```
# .github/CODEOWNERS
*                          @org/core-team
/frontend/                 @org/frontend-team
/src/auth/                 @alice @bob
*.md                       @org/docs-team
/.github/workflows/        @org/devops-team
```

### Merge queue trigger (GitHub Actions)

```yaml
on:
  push:
    branches: [main]
  pull_request:
  merge_group:             # required for merge queue CI
```

### GitHub Ruleset: recommended settings for `main`

```
Target: main
- Require pull request: 1 approval, require CODEOWNERS review
- Require status checks: ci/build, ci/test
- Require branches to be up to date
- Block force pushes
- Require linear history
- Require signed commits
```

---

## GitOps

### ArgoCD: install (v3.3.7)

```bash
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/v3.3.7/manifests/install.yaml

kubectl port-forward svc/argocd-server -n argocd 8080:443
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
```

### ArgoCD: create an Application

```bash
argocd app create myapp \
  --repo https://github.com/org/config-repo.git \
  --path envs/prod \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace myapp \
  --sync-policy automated \
  --auto-prune \
  --self-heal
```

### Flux: bootstrap (v2.8.2)

```bash
export GITHUB_TOKEN=<pat>
flux bootstrap github \
  --owner=YOUR_ORG \
  --repository=fleet-infra \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
```

### Kustomize overlay structure

```
config-repo/
├── base/
│   ├── deployment.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/kustomization.yaml
    ├── staging/kustomization.yaml
    └── prod/kustomization.yaml
```

### Environment promotion (Kustomize)

```yaml
# overlays/prod/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
images:
  - name: myapp
    newTag: v2.3.1    # bump this in a PR to promote
```

### Tool choice

```
ArgoCD  → rich UI, central multi-cluster management, easier onboarding
Flux    → composable controllers, first-class SOPS, image automation
```

---

## Monorepos

### The canonical monorepo clone

```bash
git clone --filter=blob:none --sparse \
    https://github.com/org/monorepo.git
cd monorepo
git sparse-checkout init --cone
git sparse-checkout set frontend shared
git checkout main
```

### Scalar (all-in-one)

```bash
scalar clone https://github.com/org/monorepo.git
```

### Enable all optimisations manually

```bash
git config core.fsmonitor true
git config core.untrackedCache true
git config index.sparse true
git config feature.manyFiles true
git config fetch.writeCommitGraph true
git config checkout.workers 0
git maintenance start
git commit-graph write --reachable --changed-paths
```

### Partial clone flavours

```bash
--filter=blob:none     # blobless: best for developers
--filter=tree:0        # treeless: best for single-build CI only
--filter=blob:limit=1m # omit blobs larger than 1 MB
```

### Sparse checkout commands

```bash
git sparse-checkout init --cone
git sparse-checkout set path/to/dir another/dir
git sparse-checkout add path/to/more
git sparse-checkout list
git sparse-checkout disable               # restore full checkout
```

### Affected build commands

```bash
# Get changed files since main
git diff --name-only origin/main...HEAD

# Nx
npx nx affected -t build --base=origin/main --head=HEAD

# Turborepo
turbo run build --filter="...[origin/main]"

# pnpm
pnpm --filter "...[origin/main]" run build
```

### Monorepo pathspec commands

```bash
git log --oneline -- packages/auth/
git diff origin/main...HEAD -- packages/ui/
git log -p --follow -- packages/shared/src/utils.ts
git blame -w -M -C -C -C packages/shared/src/utils.ts
git grep -n 'TODO' -- ':(exclude)vendor/'
```

### Tool versions (April 2026)

```
Nx          22.x (22.5.0)   - JS/TS, affected graph, content-addressed cache
Turborepo   2.8.x           - JS/TS, Rust core, Vercel remote cache
Bazel       9 LTS / 8 LTS   - multi-language, hermetic, remote execution
Lerna       9.x             - versioning and publishing only (no bootstrap/add/link)
```

---

## Platform migration

### Mirror clone and push

```bash
git clone --mirror https://source.example.com/user/repo.git
cd repo.git
git push --mirror https://dest.example.com/user/repo.git
```

### LFS migration (always do this)

```bash
git clone --mirror https://source.example.com/user/repo.git
cd repo.git
git lfs fetch --all origin
git push --mirror https://dest.example.com/user/repo.git
git lfs push --all https://dest.example.com/user/repo.git
```

### Check whether a repo uses LFS

```bash
git lfs ls-files
cat .gitattributes | grep lfs
```

### Platform importer summary

```
GitHub → GitLab          GitLab built-in importer (strongest - issues, PRs, labels, milestones, releases)
GitLab → Gitea/Forgejo   Gitea/Forgejo built-in importer or API
GitHub → GitHub          gh gei migrate-repo
Azure DevOps → GitHub    gh ado2gh
Bitbucket Server → GitHub gh bbs2gh
GitLab → GitHub          No first-party importer - use git push --mirror + manual issue migration
```

### What GEI does NOT transfer

```
Webhooks
Deploy keys
Actions secrets and variables
GitHub Packages
Rulesets and branch protection
Pages configuration
Environments
```

### Update local remotes after migration

```bash
git remote set-url origin https://new.platform.com/user/repo.git
git remote -v
```

### Migration checklist (condensed)

**Before:** document webhooks, deploy keys, secrets (names only), protection rules, LFS usage, submodules. Announce cutover date.

**During:** mirror clone, LFS fetch, create empty destination, push mirror, LFS push, run platform importer for issues/PRs.

**After:** recreate webhooks, rotate and recreate deploy keys and secrets, recreate branch protection, update submodule URLs, update hardcoded URLs in CI/README/badges, test CI, keep source repo for two weeks.

---

## Disaster recovery

### The reflog

```bash
git reflog                          # all HEAD movements
git reflog show main                # specific branch
git reflog --date=iso               # with timestamps
```

### Lost commits

```bash
git reset --hard HEAD@{1}           # one step back in reflog
git reset --hard <sha>              # to a specific commit
git branch rescue HEAD@{3}          # create branch at reflog position
git reset --hard ORIG_HEAD          # immediately after a bad rebase or merge
```

### Find dangling objects (when reflog has expired)

```bash
git fsck --full --no-reflogs --unreachable --lost-found
ls .git/lost-found/commit/
git show <sha>
git branch recovered <sha>
```

### Bad merge

```bash
git merge --abort                   # in progress, not committed
git reset --hard ORIG_HEAD          # committed but not pushed
git revert -m 1 <merge-sha>         # pushed - always use revert
```

### Accidental force push

```bash
git reflog show origin/main         # find old SHA on any local clone
git push origin <old-sha>:main --force-with-lease
```

### Deleted branch

```bash
git reflog | grep <branch-name>
git branch <n> <sha>
git push -u origin <n>
```

### Detached HEAD

```bash
git switch -c rescue-branch         # save uncommitted work
git reflog                          # find commits made in detached HEAD
git branch rescue <sha>
```

### Force push safely

```bash
git push --force-with-lease         # checks remote has not moved
git push --force-with-lease --force-if-includes   # stricter (Git 2.30+)
```

### Revert vs reset rule

```
Commits local only, not pushed      → git reset --hard (safe)
Commits pushed, others may have it  → git revert (safe)
Merge commit, pushed                → git revert -m 1 <sha>
```

---

## Secret detection

### gitleaks

```bash
# Scan entire history
gitleaks detect --source . --log-opts="--all"

# Scan staged changes only (pre-commit use)
gitleaks protect --staged

# Pre-commit hook config (.pre-commit-config.yaml)
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.30.1
    hooks:
      - id: gitleaks
```

### TruffleHog

```bash
# Scan GitHub repo with live credential verification
trufflehog github --repo https://github.com/OWNER/REPO --only-verified

# Scan local history
trufflehog git file://. --since-commit HEAD~50 --only-verified

# Scan entire organisation
trufflehog github --org YOUR_ORG --token $GITHUB_TOKEN --only-verified
```

### Secret removal: git filter-repo

```bash
git clone --mirror https://github.com/OWNER/REPO.git
cd REPO.git

# Remove a file from all history
git filter-repo --path config/secrets.json --invert-paths

# Replace text throughout history
cat > replacements.txt << 'EOF'
AKIAIOSFODNN7EXAMPLE==>REDACTED
regex:ghp_[A-Za-z0-9]{36}==>REDACTED
EOF
git filter-repo --replace-text replacements.txt

git remote add origin https://github.com/OWNER/REPO.git
git push --force --mirror origin
# All collaborators must re-clone after this
```

### Recommended secret detection architecture

```
1. gitleaks pre-commit hook          - catch before commit, zero latency
2. gitleaks in CI on every push      - catch anything that bypassed pre-commit
3. TruffleHog weekly schedule        - deep scan with live verification
4. GitHub/GitLab push protection     - server-side final gate
```

---

## Pro Git config

```bash
git config --global init.defaultBranch main
git config --global pull.rebase true
git config --global rebase.autoStash true
git config --global rebase.autoSquash true
git config --global rebase.updateRefs true       # update refs in a stack (Git 2.38+)
git config --global rerere.enabled true          # remember conflict resolutions
git config --global merge.conflictStyle zdiff3   # best conflict markers
git config --global diff.algorithm histogram
git config --global diff.colorMoved default
git config --global fetch.prune true
git config --global push.autoSetupRemote true
git config --global column.ui auto
git config --global branch.sort -committerdate
git config --global core.fsmonitor true
git config --global feature.manyFiles true

# SSH commit signing (Git 2.34+, preferred over GPG)
git config --global gpg.format ssh
git config --global user.signingkey ~/.ssh/id_ed25519.pub
git config --global commit.gpgsign true
```

---

## Essential aliases

```bash
git config --global alias.lg \
  "log --graph --abbrev-commit --decorate \
  --format=format:'%C(bold blue)%h%C(reset) %C(bold green)(%ar)%C(reset) \
  %s %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)' --all"

git config --global alias.amend "commit --amend --no-edit"
git config --global alias.please "push --force-with-lease"
git config --global alias.ri "rebase -i --autosquash"
git config --global alias.sync "!git fetch --all --prune && git pull --rebase"
git config --global alias.unstage "restore --staged"
git config --global alias.st "status -sb"
git config --global alias.last "log -1 HEAD --stat"
```

---

## Quick decision guide

| Situation | Command |
|---|---|
| Where did my commits go? | `git reflog` |
| Undo last commit, keep changes staged | `git reset --soft HEAD~1` |
| Undo last commit, discard changes | `git reset --hard HEAD~1` |
| Undo a pushed commit safely | `git revert HEAD` |
| Undo a pushed merge safely | `git revert -m 1 <merge-sha>` |
| Get a specific file from another branch | `git restore --source=other-branch -- path/to/file` |
| Get a specific file from 3 commits ago | `git restore --source=HEAD~3 -- path/to/file` |
| Find which commit introduced a bug | `git bisect start; git bisect bad; git bisect good v1.0; git bisect run npm test` |
| Find which commit deleted a function | `git log -S"functionName" --source --all` |
| See what changed in a PR since last review | `git range-diff old-tip...new-tip` |
| Apply one commit from another branch | `git cherry-pick <sha>` |
| Apply one commit but do not commit yet | `git cherry-pick --no-commit <sha>` |
| Temporarily save work without committing | `git stash push -m "description"` |
| Work on two things at once without stashing | `git worktree add ../hotfix main` |
| Force push safely | `git push --force-with-lease` |
| Who changed this line? (ignoring reformats) | `git blame -w -C -C -C <file>` |
| See the diff this PR will produce | `git diff main...feature` |
| Remove a file from all history | `git filter-repo --path <file> --invert-paths` |

---

## Summary

This reference consolidates the commands and patterns from all seven files in the `real-world/` section. Use it as a lookup when you remember what you need to do but not the exact syntax.

For full explanations, context and step-by-step exercises, return to the individual files:

- [00-real-world-overview.md](00-real-world-overview.md) - section map and reading order
- [01-contributing-to-open-source.md](01-contributing-to-open-source.md) - fork workflow, PRs, upstream sync
- [02-working-in-a-team.md](02-working-in-a-team.md) - branching strategies, code review, rulesets, merge queues
- [03-gitops-workflows.md](03-gitops-workflows.md) - ArgoCD, Flux, environment promotion
- [04-monorepo-patterns.md](04-monorepo-patterns.md) - partial clone, sparse checkout, Nx, Turborepo, Bazel
- [05-migrating-between-platforms.md](05-migrating-between-platforms.md) - mirror clone, LFS migration, platform importers
- [06-recovering-from-disasters.md](06-recovering-from-disasters.md) - reflog, lost commits, force push, secrets

---

## Sources and Further Reading

- [git-scm.com: official Git documentation](https://git-scm.com/doc)
- [GitHub Docs](https://docs.github.com/)
- [GitLab Docs](https://docs.gitlab.com/)
- [OpenGitOps principles](https://opengitops.dev/)
- [Trunk Based Development](https://trunkbaseddevelopment.com/)
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
- [git-filter-repo](https://github.com/newren/git-filter-repo)
- [gitleaks](https://github.com/gitleaks/gitleaks)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog)
- [Nx documentation](https://nx.dev/docs)
- [Turborepo documentation](https://turbo.build/repo/docs)
- [Bazel documentation](https://bazel.build/docs)
- [ArgoCD documentation](https://argo-cd.readthedocs.io/)
- [Flux documentation](https://fluxcd.io/flux/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

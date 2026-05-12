# Recovering from disasters

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** 40 minutes

Step-by-step recovery for the most common Git disasters - from lost commits to committed secrets.

---

## Table of contents

- [Introduction](#introduction)
- [The reflog: your safety net](#the-reflog-your-safety-net)
- [Lost commits after reset, rebase or amend](#lost-commits-after-reset-rebase-or-amend)
- [Bad merge](#bad-merge)
- [Accidental force push](#accidental-force-push)
- [Deleted branch](#deleted-branch)
- [Detached HEAD](#detached-head)
- [Committed secrets](#committed-secrets)
- [Detecting secrets before they are committed](#detecting-secrets-before-they-are-committed)
- [Corrupted repository](#corrupted-repository)
- [The revert vs reset rule](#the-revert-vs-reset-rule)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Introduction

Most Git disasters are recoverable. Git's design as an append-only content-addressed store means that data is almost never truly deleted - it is just temporarily unreachable. The reflog records every movement of HEAD and every branch tip for 90 days. Commits survive `reset --hard`, deleted branches, botched rebases and accidental amends.

This file covers the six most common disasters in order of frequency, starting with the least destructive recovery approach each time. The one exception where recovery is not guaranteed is a committed secret: once it is pushed, it is compromised and rewriting history does not un-compromise it.

**The golden rules before anything else:**

1. Do not run `git gc` after a disaster. It permanently deletes unreachable objects
2. Run `git reflog` before assuming anything is lost
3. Rotate secrets immediately, before rewriting history

---

## The reflog: your safety net

The reflog is Git's local audit log. It records every movement of HEAD and every branch tip - including changes from `reset --hard`, `rebase`, `merge`, `commit --amend` and branch deletion. It retains reachable commits for 90 days and unreachable commits for 30 days by default.

```bash
# Show all recent HEAD movements
git reflog

# Sample output:
# abc1234 HEAD@{0}: reset: moving to HEAD~3
# def5678 HEAD@{1}: commit: Add payment retry logic
# ghi9012 HEAD@{2}: commit: Fix null pointer in auth
# jkl3456 HEAD@{3}: merge feat/login: Merge made by 'ort'

# Show reflog for a specific branch
git reflog show main

# Show reflog with timestamps
git reflog --date=iso
```

The `HEAD@{n}` notation means "where HEAD was n moves ago." `HEAD@{0}` is the current position. `HEAD@{1}` is the previous position.

**Before panicking about lost work, always run `git reflog` first.**

---

## Lost commits after reset, rebase or amend

### After `git reset --hard`

```bash
# Find the commit you want to recover
git reflog
# HEAD@{0}: reset: moving to HEAD~3
# HEAD@{1}: commit: Add payment retry logic   <-- this is what you want

# Recover by resetting to the position before the reset
git reset --hard HEAD@{1}

# Or recover to a specific SHA
git reset --hard abc1234
```

### After a bad rebase

```bash
# Find the pre-rebase state in the reflog
git reflog
# HEAD@{0}: rebase (finish): returning to refs/heads/feat/login
# HEAD@{1}: rebase (pick): fix typo
# HEAD@{2}: rebase (start): checkout main
# HEAD@{3}: commit: Add login form   <-- pre-rebase state

# Create a rescue branch at the pre-rebase state
git branch rescue-pre-rebase HEAD@{3}

# Or reset the current branch back
git reset --hard HEAD@{3}
```

Git also sets `ORIG_HEAD` before destructive operations. You can use it immediately after a bad rebase if you have not done anything else since:

```bash
git reset --hard ORIG_HEAD
```

### After `git commit --amend` that lost content

```bash
# The pre-amend commit is still in the reflog
git reflog
# HEAD@{0}: commit (amend): Updated commit message
# HEAD@{1}: commit: Original commit with all the content  <-- this

# Recover
git reset --soft HEAD@{1}    # keeps changes staged
# or
git reset --hard HEAD@{1}    # restores exactly as it was
```

### If the reflog has expired

If the reflog entry is older than 30 days (unreachable objects) or 90 days (reachable objects), the commit may still exist as a dangling object. Use `git fsck` to find it:

```bash
git fsck --full --no-reflogs --unreachable --lost-found
# Writes dangling objects to .git/lost-found/

ls .git/lost-found/commit/
# Each file is a SHA of a dangling commit - inspect them:
git show <sha>
git log --oneline <sha>

# Recover by creating a branch
git branch recovered-commit <sha>
```

---

## Bad merge

### Merge in progress, not yet committed

```bash
git merge --abort
```

This restores the working tree and index to the pre-merge state.

### Unpushed merge commit

```bash
# ORIG_HEAD points to the pre-merge state
git reset --hard ORIG_HEAD
```

This works immediately after a merge. If you have made commits since the merge, use the reflog instead:

```bash
git reflog
# Find the commit just before the merge
git reset --hard <pre-merge-sha>
```

### Pushed merge commit

Never use `reset --hard` on a pushed merge. Other people may have fetched it. Use `git revert` instead:

```bash
# Revert a merge commit, keeping the mainline parent
git revert -m 1 <merge-commit-sha>
git push
```

The `-m 1` flag specifies which parent is the mainline. Parent 1 is the branch you merged into (usually `main`); parent 2 is the feature branch. `-m 1` keeps the `main` side and undoes the changes brought in by the merge.

> [!IMPORTANT]
> `git revert -m 1` records in Git history that you do not want the tree changes from the merged branch. If you later want to re-merge that same branch (for example, after fixing the problem), you must first revert the revert:
>
> ```bash
> git revert <revert-commit-sha>
> git merge feat/the-branch
> ```
>
> Merging the original branch directly after a revert-of-merge will appear to produce no changes, because Git sees the merge point as already having been merged.

---

## Accidental force push

An accidental `git push --force` overwrites the remote branch with your local state, potentially destroying commits that other people pushed after your last fetch.

### Recovery from a local clone

If you (or a teammate) have a local clone that fetched before the force push, the old commits are in its reflog:

```bash
# On the machine that has the old state
git reflog show origin/main
# Find the SHA of the last good commit

# Push it back
git push origin <old-sha>:main --force-with-lease
```

### Recovery from GitHub's Events API

GitHub retains push event data for approximately 90 days. You can query it to find the SHA before the force push:

```bash
curl -H "Authorization: token $GITHUB_TOKEN" \
  "https://api.github.com/repos/OWNER/REPO/events" \
  | python3 -c "
import json, sys
events = json.load(sys.stdin)
for e in events:
    if e['type'] == 'PushEvent':
        print('Before:', e['payload']['before'])
        print('After:', e['payload']['after'])
        print('Ref:', e['payload']['ref'])
        print()
"
```

Once you have the old SHA, restore the branch:

```bash
# Create a branch at the old SHA (this works on GitHub even without a local copy)
curl -X POST \
  -H "Authorization: token $GITHUB_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"ref":"refs/heads/recovered","sha":"<old-sha>"}' \
  "https://api.github.com/repos/OWNER/REPO/git/refs"
```

### GitHub's "Restore branch" button

For force pushes that closed a pull request, GitHub shows a **Restore branch** button on the closed PR page. This reinstates the branch at its last known SHA.

### Prevention

```bash
# Always use --force-with-lease instead of --force
git push --force-with-lease

# For extra safety, also add --force-if-includes (Git 2.30+)
# This checks that any remote commits are already in your local history
git push --force-with-lease --force-if-includes
```

Enable "Block force pushes" in your branch Ruleset or protection rules on any shared branch.

---

## Deleted branch

### If you just deleted it locally

```bash
# Find the last SHA the branch pointed to
git reflog
# HEAD@{3}: checkout: moving from feat/payment to main  <-- just before checkout
# Means feat/payment was at the commit shown on the line above

# Or search the reflog explicitly
git reflog | grep feat/payment

# Recreate the branch
git branch feat/payment <sha>
```

### If it was deleted on the remote

```bash
# Check if any local copy still has the ref
git branch -a | grep feat/payment

# If you have it locally, push it back
git push origin feat/payment

# If you do not have it locally, check the reflog for the remote-tracking ref
git reflog show origin/feat/payment
git branch feat/payment <sha>
git push origin feat/payment
```

### If the reflog has no record

```bash
git fsck --full --no-reflogs --unreachable --lost-found
# Inspect .git/lost-found/commit/ for the content you need
```

---

## Detached HEAD

A detached HEAD means HEAD points directly to a commit SHA rather than to a branch name. Any commits you make in this state are not on any branch and will be garbage-collected unless you save them.

```bash
# Check if you are in detached HEAD state
git status
# HEAD detached at abc1234

# Option 1: if you want to keep commits you made in detached HEAD
git switch -c rescue-from-detached-head
git push -u origin rescue-from-detached-head

# Option 2: if you switched away and the commits are now unreachable
git reflog
# Find the SHA of the last commit you made in detached HEAD
git branch rescue-from-detached-head <sha>
```

> [!TIP]
> Use `git switch` instead of `git checkout` for changing branches. `git switch` refuses to silently enter a detached HEAD state when switching to a branch name. It only detaches when you explicitly pass `--detach`.

---

## Committed secrets

> [!CAUTION]
> Rotate the secret **immediately**, before doing anything else. Once a secret is pushed to a remote - even a private repository - it is compromised. Forks, clones, the GitHub Events API and various caching layers may have already captured it. History rewriting is hygiene, not mitigation.

### The mandatory two-step response

**Step 1: Rotate the secret (do this first, always)**

- API keys: revoke the key in the provider's dashboard and generate a new one
- GitHub tokens: go to Settings > Developer settings > Personal access tokens > Revoke
- AWS credentials: go to IAM > Users > Security credentials > Make inactive then delete
- SSH private keys: remove the public key from every authorised location and generate a new keypair
- Database passwords: change the password in the database and update all connection strings

**Step 2: Rewrite history to remove the secret**

The current tool is **`git filter-repo`** v2.47+. `git filter-branch` is officially deprecated - its manpage redirects to filter-repo.

```bash
# Install git-filter-repo
# 🍎 Mac
brew install git-filter-repo

# 🐧 Linux
pip install git-filter-repo --break-system-packages

# 🪟 Windows (pip)
pip install git-filter-repo
```

```bash
# Step 1: mirror-clone the repository (filter-repo requires a fresh clone)
git clone --mirror https://github.com/OWNER/REPO.git
cd REPO.git

# Step 2: delete a specific file from all history
git filter-repo --path config/secrets.json --invert-paths

# Step 3: delete a file by name anywhere in history
git filter-repo --use-base-name --path id_rsa --invert-paths

# Step 4: replace secret text throughout history
cat > replacements.txt << 'EOF'
AKIAIOSFODNN7EXAMPLE==>REDACTED_AWS_KEY
ghp_abc123def456ghi789jkl012mno345pqr6==>REDACTED_GITHUB_TOKEN
literal:password123==>REDACTED_PASSWORD
regex:ghp_[A-Za-z0-9]{36}==>REDACTED_GITHUB_TOKEN
EOF
git filter-repo --replace-text replacements.txt

# Step 5: re-add the remote (filter-repo removes it for safety)
git remote add origin https://github.com/OWNER/REPO.git

# Step 6: force-push all branches and tags
git push --force --mirror origin
```

> [!WARNING]
> After a force-push following filter-repo, every collaborator's local clone has a history that diverges from the remote. They must re-clone:
>
> ```bash
> git clone https://github.com/OWNER/REPO.git
> ```
>
> They cannot simply `git pull` - the histories are incompatible.

### BFG Repo-Cleaner: the simpler alternative

**BFG Repo-Cleaner v1.14.0** (requires Java 11+) is faster than filter-repo for two specific tasks: removing files larger than a size limit, and replacing text from a list.

```bash
# Download BFG
# 🍎 Mac
brew install bfg

# 🐧 Linux / 🪟 Windows
# Download from https://rtyley.github.io/bfg-repo-cleaner/
# Run with: java -jar bfg.jar

# Mirror-clone first
git clone --mirror https://github.com/OWNER/REPO.git
cd REPO.git

# Remove all files larger than 10 MB
bfg --strip-blobs-bigger-than 10M

# Replace passwords from a list
echo "password123" > banned.txt
bfg --replace-text banned.txt

# Clean up and push
git reflog expire --expire=now --all
git gc --prune=now --aggressive
git push --force --mirror origin
```

Choose filter-repo for precise control and complex text patterns. Choose BFG for bulk size stripping or simple text replacement on very large repositories where speed matters.

### GitHub push protection

**GitHub push protection** blocks pushes containing known secret patterns before they land on the remote. It became default-on for all public repositories on **1 March 2024** and for all new personal public repositories on **11 March 2024**. It covers 200+ token types from 180+ providers.

If push protection blocks your push, you will see:

```
remote: error: GH013: Repository rule violations found for refs/heads/main.
remote: - GITHUB PUSH PROTECTION
remote:   —————————————————————————————————————
remote:     Affected file:  config/app.yml
remote:     Secret:         GitHub Personal Access Token
```

You can bypass it for false positives in the GitHub UI, but you cannot bypass it with a command-line flag alone on organisation repositories with required protection.

**GitLab Secret Push Protection** (available from GitLab 19.0+) provides equivalent functionality.

---

## Detecting secrets before they are committed

Prevention is better than recovery. Two tools cover the pre-commit and CI layers.

### gitleaks 🟡

**gitleaks** ([github.com/gitleaks/gitleaks](https://github.com/gitleaks/gitleaks)) is a Go-based secret scanner that uses regex rules against your Git history, staged changes or working directory. It outputs SARIF for integration with GitHub Security tab.

```bash
# Install
# 🍎 Mac
brew install gitleaks

# 🐧 Linux
curl -sSfL https://raw.githubusercontent.com/gitleaks/gitleaks/master/scripts/install.sh \
  | sh -s -- -b /usr/local/bin

# 🪟 Windows (winget)
winget install gitleaks

# Scan the entire history
gitleaks detect --source . --log-opts="--all"

# Scan only staged changes (use as a pre-commit hook)
gitleaks protect --staged

# Scan and output SARIF (for GitHub Security tab)
gitleaks detect --report-format sarif --report-path gitleaks.sarif
```

**As a pre-commit hook** (using the pre-commit framework):

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.24.2
    hooks:
      - id: gitleaks
```

```bash
# Install pre-commit and the hook
pip install pre-commit --break-system-packages
pre-commit install
```

### TruffleHog 🔴

**TruffleHog** ([github.com/trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog)) goes further than regex matching. It has 800+ detectors and actively verifies found credentials against provider APIs to confirm they are live, not just plausible-looking strings.

```bash
# Install
# 🍎 Mac
brew install trufflesecurity/trufflehog/trufflehog

# 🐧 Linux
curl -sSfL https://raw.githubusercontent.com/trufflesecurity/trufflehog/main/scripts/install.sh \
  | sh -s -- -b /usr/local/bin

# 🪟 Windows (winget)
winget install TruffleHog

# Scan a GitHub repository (including history)
trufflehog github --repo https://github.com/OWNER/REPO

# Scan local Git history
trufflehog git file://. --since-commit HEAD~50

# Scan and only report verified (live) credentials
trufflehog git file://. --only-verified

# Run as a GitHub Actions job (weekly scan)
trufflehog github --org YOUR_ORG --token $GITHUB_TOKEN --only-verified
```

**Recommended architecture:**

- gitleaks as a `pre-commit` hook - catches secrets before commit, zero network access needed
- gitleaks in CI as a push check - catches anything that bypassed the pre-commit hook
- TruffleHog on a weekly schedule - deep scan with live credential verification across all repository history
- GitHub/GitLab native push protection as the final server-side gate

---

## Corrupted repository

Repository corruption is rare but does occur, most often from interrupted writes (power loss, network failure during clone) or filesystem errors.

### Diagnosing corruption

```bash
# Run the full integrity check
git fsck --full --strict --verbose
```

Look for lines like:

- `error: object file .git/objects/ab/cdef... is empty` - truncated object
- `error: sha1 mismatch` - content does not match its SHA (corruption)
- `dangling commit` - unreachable but not corrupt (harmless)

### Fetching missing objects from the remote

```bash
# Fetch everything including pack files
git fetch --all

# Prune broken remote-tracking refs
git fetch -p origin

# If the remote has the missing objects, this is usually enough
git fsck --full
```

### Repairing a corrupt pack file

```bash
# Identify the broken pack
git fsck --full 2>&1 | grep "corrupt"

# Unpack it
cd .git/objects/pack/
mv pack-<sha>.pack /tmp/
git unpack-objects -r < /tmp/pack-<sha>.pack

# Remove the broken index
rm pack-<sha>.idx

# Re-pack everything cleanly
cd ../../../
git gc
git fsck --full
```

### Last resort

If the repository is too corrupt to repair:

```bash
# Clone fresh from the remote
git clone https://github.com/OWNER/REPO.git fresh-clone

# Extract any unique commits from the broken repo
cd broken-repo
git fsck --full --no-reflogs --unreachable --lost-found
git log --all --oneline > /tmp/broken-history.txt

# Cherry-pick commits that exist in broken repo but not in fresh clone
cd fresh-clone
git cherry-pick <unique-sha-from-broken>
```

---

## The revert vs reset rule

This is the most important rule in disaster recovery:

| Situation                                   | Use                          | Why                                                                          |
| ------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------- |
| Commits are local only, not pushed          | `git reset --hard`           | Safe - no one else has seen these commits                                    |
| Commits are pushed, others may have fetched | `git revert`                 | Creates a new commit that undoes the changes; preserves history for everyone |
| Merge commit needs undoing, already pushed  | `git revert -m 1`            | Revert the merge safely without rewriting shared history                     |
| Merge commit needs undoing, not yet pushed  | `git reset --hard ORIG_HEAD` | Safe - history is not shared yet                                             |

**Never use `git reset --hard` plus `git push --force` on a branch that other people have fetched.** This is exactly how accidental force push disasters happen. If in doubt, use `git revert`.

---

## Try it yourself

These exercises create real disasters and walk you through the recovery. Use a disposable test repository.

```bash
# Set up a test repository
mkdir disaster-lab && cd disaster-lab
git init
git commit --allow-empty -m "Initial commit"
echo "Important work" > important.txt
git add . && git commit -m "Add important.txt"
echo "More important work" >> important.txt
git add . && git commit -m "Update important.txt"
echo "Critical feature" > critical.txt
git add . && git commit -m "Add critical.txt"
```

### Exercise 1: recover from `git reset --hard`

```bash
git reset --hard HEAD~2
# Oh no - lost two commits
git log --oneline    # only "Initial commit" and "Add important.txt"

git reflog           # find the lost commits
git reset --hard HEAD@{1}   # or the SHA of "Add critical.txt"
git log --oneline    # all four commits are back
```

### Exercise 2: recover a deleted branch

```bash
git switch -c experiment
echo "Experimental feature" > experiment.txt
git add . && git commit -m "Add experimental feature"
git switch main
git branch -D experiment
# Branch is deleted

git reflog | grep experiment    # find the last SHA
git branch experiment <sha>     # recreate it
git log --oneline experiment    # commit is back
```

### Exercise 3: practice gitleaks

```bash
# Create a fake secret
echo "AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYSECRETKEY123" > .env
git add . && git commit -m "Add config"

# Scan for secrets
gitleaks detect --source . --log-opts="--all"
# gitleaks should flag the AWS key pattern
```

---

## Common mistakes

**Running `git gc` after losing commits.**
`git gc` prunes unreachable objects. If you run it after accidentally deleting a branch or resetting too far, the commits may be permanently gone. Always run `git reflog` and `git fsck --lost-found` before ever running `git gc` on a repository with a suspected disaster.

**Using `git reset --hard` + `git push --force` on a shared branch.**
This is how data loss disasters happen on teams. Other people's local clones still have the old commits, creating diverged histories. Use `git revert` for any commit that has been pushed and may have been fetched by others.

**Rewriting history before rotating secrets.**
Rewriting history does not revoke a compromised credential. The secret may already be in forks, clones, build logs, GitHub's Events API or third-party CI systems. Rotate first, always.

**Using `git filter-branch` instead of `git filter-repo`.**
`git filter-branch` is deprecated. Its manpage explicitly redirects to `git filter-repo`. It is also much slower and more error-prone on complex rewrites. Always use `git filter-repo` v2.47+ for history rewriting.

**Not re-cloning after a force-push following history rewrite.**
After filter-repo rewrites history and you force-push, every existing clone has a diverged history. Telling people to `git pull` will not work. They must re-clone. Communicate this clearly to all contributors.

**Assuming push protection means you cannot commit secrets.**
Push protection blocks secrets at the push step. You can still commit a secret locally. It will be blocked when you push, but it is now in your local history. You still need to rewrite the local history before it can be pushed. Use gitleaks pre-commit hooks to catch secrets before they enter the local history at all.

---

## Summary

The reflog is the recovery tool for almost every Git disaster. Before concluding that data is lost, run `git reflog` and `git fsck --lost-found`. Do not run `git gc` until you are certain nothing needs recovering.

The six most common disasters and their primary recovery tools:

- **Lost commits** - `git reflog` then `git reset --hard HEAD@{n}`
- **Bad merge** - `git merge --abort` (in-progress), `git reset --hard ORIG_HEAD` (local), `git revert -m 1` (pushed)
- **Accidental force push** - reflog on another clone, GitHub Events API, GitHub's "Restore branch" button
- **Deleted branch** - `git reflog | grep <branch>` then `git branch <n> <sha>`
- **Detached HEAD** - `git switch -c rescue-branch` if you have uncommitted work
- **Committed secrets** - rotate first, then `git filter-repo` to rewrite history, then force-push, then all collaborators re-clone

For secret detection, use **gitleaks** as a pre-commit hook and in CI, and **TruffleHog** on a weekly schedule for deep scanning with live credential verification. GitHub push protection provides the server-side final gate.

Always use `git push --force-with-lease` instead of `git push --force`, and always use `git revert` instead of `git reset --hard` on any branch other people have fetched.

---

## Sources and Further Reading

- [git-scm.com: git reflog](https://git-scm.com/docs/git-reflog)
- [git-scm.com: git fsck](https://git-scm.com/docs/git-fsck)
- [git-filter-repo documentation](https://github.com/newren/git-filter-repo)
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [GitHub: removing sensitive data from a repository](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [GitHub: about secret scanning](https://docs.github.com/en/code-security/secret-scanning/about-secret-scanning)
- [GitHub: push protection](https://docs.github.com/en/code-security/secret-scanning/push-protection-for-repositories-and-organizations)
- [gitleaks](https://github.com/gitleaks/gitleaks)
- [TruffleHog](https://github.com/trufflesecurity/trufflehog)
- [git-scm.com: git revert](https://git-scm.com/docs/git-revert)
- [Linus Torvalds on reverting a faulty merge](https://github.com/git/git/blob/master/Documentation/howto/revert-a-faulty-merge.adoc)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

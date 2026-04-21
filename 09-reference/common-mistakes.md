# Common mistakes

**Difficulty:** 🟢 Beginner to 🔴 Advanced | **Time:** Reference - use as needed

The most common Git errors and mistakes, with exact commands to fix each one.

---

## Table of contents

- [Commit mistakes](#commit-mistakes)
- [Branch mistakes](#branch-mistakes)
- [Remote and push mistakes](#remote-and-push-mistakes)
- [Merge and rebase mistakes](#merge-and-rebase-mistakes)
- [Configuration mistakes](#configuration-mistakes)
- [Secret and credential mistakes](#secret-and-credential-mistakes)
- [History rewriting mistakes](#history-rewriting-mistakes)
- [Submodule mistakes](#submodule-mistakes)
- [Large file mistakes](#large-file-mistakes)
- [Platform mistakes](#platform-mistakes)
- [Team workflow mistakes](#team-workflow-mistakes)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Commit mistakes

**Committed to the wrong branch**

```bash
# Save the commit SHA
git log --oneline -1

# Move to the correct branch
git switch correct-branch

# Apply the commit
git cherry-pick <sha>

# Remove it from the wrong branch
git switch wrong-branch
git reset --hard HEAD~1
```

**Committed with the wrong name or email**

```bash
# Fix the most recent commit
git commit --amend --reset-author --no-edit

# Or set explicitly
git commit --amend --author="Correct Name <correct@email.com>" --no-edit
```

**Committed too many files - need to remove one**

```bash
# Remove a file from the last commit but keep it on disk
git reset HEAD~1 -- path/to/accidental-file.txt
git commit --amend --no-edit

# Or remove it from staging and recommit
git reset --soft HEAD~1
git restore --staged path/to/accidental-file.txt
git commit -m "original message"
```

**Wrote a bad commit message**

```bash
# Fix the most recent commit (not pushed yet)
git commit --amend -m "Better message following conventions"

# Fix an older message using interactive rebase (not pushed)
git rebase -i HEAD~3
# Change 'pick' to 'reword' on the relevant line
```

**Accidentally committed a large file**

```bash
# Remove from last commit and gitignore it
git rm --cached path/to/large-file.bin
echo "path/to/large-file.bin" >> .gitignore
git add .gitignore
git commit --amend --no-edit

# If already pushed, use git-filter-repo
git filter-repo --path path/to/large-file.bin --invert-paths
```

**Empty commit blocking a pipeline**

```bash
# Check if the commit is empty
git show --stat HEAD

# Create a skip-CI commit if needed
git commit --allow-empty -m "ci: trigger pipeline [skip ci]"
```

---

## Branch mistakes

**Deleted a branch by accident**

```bash
# Find the last SHA the branch pointed to
git reflog | grep <branch-name>

# Recreate it
git branch <n> <sha>
git push -u origin <n>
```

**Created a branch from the wrong starting point**

```bash
# Rebase onto the correct base
git rebase --onto correct-base wrong-base feat/my-branch

# Or reset and replay from scratch
git switch feat/my-branch
git reset --hard correct-base
# ...re-apply your changes
```

**Committed directly to `main` instead of a feature branch**

```bash
# Create the branch you should have used
git branch feat/my-feature

# Reset main to where it was
git reset --hard origin/main

# Switch to the new branch (your commits are there)
git switch feat/my-feature
```

**Branch name has a typo**

```bash
git branch -m typo-branch-nam correct-branch-name

# If already pushed
git push origin -u correct-branch-name
git push origin --delete typo-branch-nam
```

**Local branch is out of sync with remote**

```bash
git fetch origin
git switch my-branch
git rebase origin/my-branch
```

---

## Remote and push mistakes

**Pushed to the wrong remote branch**

```bash
# Delete the pushed branch from the wrong remote
git push origin --delete wrong-branch-name

# Push to the correct branch
git push origin feat/my-feature:feat/my-feature
```

**Accidentally force-pushed and lost commits**

```bash
# Recover using the reflog of another clone that had the old state
git reflog show origin/main
git push origin <old-sha>:main --force-with-lease

# If nobody has the old state, check GitHub Events API
curl -H "Authorization: token $GITHUB_TOKEN" \
  "https://api.github.com/repos/OWNER/REPO/events" | \
  python3 -c "
import json, sys
for e in json.load(sys.stdin):
    if e['type'] == 'PushEvent':
        print('Before:', e['payload']['before'])
"
```

**Push rejected: non-fast-forward**

```bash
# This means the remote has commits you do not have
# Fetch and rebase (preferred)
git fetch origin
git rebase origin/main

# Then push
git push

# If you are certain the remote is wrong (e.g. your own feature branch)
git push --force-with-lease
```

**Push rejected: large file**

```bash
# GitHub rejects files over 100 MB with GH001 error
# Remove the file from history first
git filter-repo --path path/to/large-file.bin --invert-paths

# Then use Git LFS for it going forward
git lfs install
git lfs track "*.bin"
git add .gitattributes
git add path/to/large-file.bin
git commit -m "chore: track large files with LFS"
git push
```

**Remote URL is wrong**

```bash
git remote set-url origin git@github.com:YOUR_USERNAME/REPO.git
git remote -v   # verify
```

**Credentials rejected on push**

```bash
# Clear cached credentials
# Windows
cmdkey /delete:git:https://github.com

# macOS
git credential-osxkeychain erase <<< "protocol=https
host=github.com"

# All platforms (GCM)
git credential reject <<< "protocol=https
host=github.com"

# Then try again - browser OAuth will re-authenticate
git push
```

---

## Merge and rebase mistakes

**Merge conflict: how to resolve**

```bash
# See which files are conflicted
git status

# Open conflicted files - resolve the <<<<< ===== >>>>> markers
# Then stage the resolved files
git add path/to/resolved-file.txt

# Complete the merge
git commit

# Or abort entirely to go back to the pre-merge state
git merge --abort
```

**Applied the wrong merge strategy**

```bash
# Undo a just-completed merge (before pushing)
git reset --hard ORIG_HEAD

# Undo a pushed merge safely
git revert -m 1 <merge-commit-sha>
git push
```

**Rebase went wrong**

```bash
# Abort and return to the pre-rebase state
git rebase --abort

# Or if you have already finished but it is wrong
git reset --hard ORIG_HEAD

# Or use the reflog
git reflog
git reset --hard HEAD@{n}  # where n is the pre-rebase position
```

**Squashed too many commits together**

```bash
# The individual commits still exist in the reflog
git reflog
# Find the SHA before the squash
git reset --hard <pre-squash-sha>
```

**`revert -m 1` then trying to re-merge the same branch**

```bash
# Must revert the revert first
git revert <revert-commit-sha>

# Now the original branch can be merged again
git merge feat/original-branch
```

---

## Configuration mistakes

**Wrong global email or name**

```bash
git config --global user.name "Your Correct Name"
git config --global user.email "correct@email.com"

# To fix already-made commits (not pushed)
git rebase -i --root
# Change 'pick' to 'edit' on each commit, then:
git commit --amend --reset-author --no-edit
git rebase --continue
```

**`pull.rebase` not set - getting unexpected merge commits**

```bash
git config --global pull.rebase true
# Now git pull will rebase instead of merge by default
```

**Line endings causing spurious diffs (common on Windows)**

```bash
# Set globally
git config --global core.autocrlf input   # Mac/Linux
git config --global core.autocrlf true    # Windows

# Or use .gitattributes (preferred - works for the whole team)
echo "* text=auto" >> .gitattributes
git add .gitattributes
git commit -m "chore: normalise line endings"

# Re-normalise the working tree
git rm --cached -r .
git reset --hard
```

**Case-insensitive filesystem causing confusion (macOS/Windows)**

```bash
# Check if this affects you
git config core.ignoreCase

# Two files that differ only in case appear as the same on macOS/Windows
# Rename via Git (not the OS) to avoid this
git mv File.txt file.txt
```

**`push.default` causing unexpected pushes**

```bash
# Set the safest default
git config --global push.default current
# or
git config --global push.autoSetupRemote true
```

---

## Secret and credential mistakes

**Accidentally committed a secret**

```bash
# Step 1: ROTATE THE SECRET IMMEDIATELY (always first)
# Step 2: Remove from history

# Install git-filter-repo (replaces deprecated git filter-branch)
pip install git-filter-repo   # or: brew install git-filter-repo

# Mirror-clone (required by filter-repo)
git clone --mirror https://github.com/OWNER/REPO.git
cd REPO.git

# Remove a file containing the secret
git filter-repo --path config/secrets.json --invert-paths

# Replace secret text throughout history
echo "ACTUAL_SECRET_VALUE==>REDACTED" > replacements.txt
git filter-repo --replace-text replacements.txt

# Push
git remote add origin https://github.com/OWNER/REPO.git
git push --force --mirror origin

# Tell all collaborators to re-clone (they cannot git pull)
```

> [!CAUTION]
> Rewriting history does not un-compromise a pushed secret. Rotate the credential immediately before doing anything else. Assume it was captured by forks, clones, the GitHub Events API and any CI system that ran against the repository.

**Secret detected by GitHub push protection**

```bash
# You will see:
# remote: error: GH013: Repository rule violations found
# remote:   Secret: GitHub Personal Access Token
# remote:   File: config/app.yml

# Option 1: remove the secret from the commit
git reset HEAD~1
# Edit the file to remove the secret
git add config/app.yml
git commit -m "fix: remove secret from config"
git push

# Option 2: use environment variables instead
# Never hardcode secrets in files - use env vars or a secret manager
```

**`.env` file was tracked and needs to be untracked**

```bash
# Remove from Git tracking without deleting the file
git rm --cached .env
echo ".env" >> .gitignore
git add .gitignore
git commit -m "chore: untrack .env and gitignore it"

# If it has been pushed, also clean history
git filter-repo --path .env --invert-paths
```

**Credentials stored in plaintext (`credential.helper=store`)**

```bash
# Check what helper is set
git config --global credential.helper

# Replace with a secure helper
# Windows
git config --global credential.helper manager

# macOS
git config --global credential.helper osxkeychain

# Linux
git config --global credential.helper /usr/lib/git-core/git-credential-libsecret

# Delete the plaintext store file
rm ~/.git-credentials
```

---

## History rewriting mistakes

**Using `git filter-branch` in 2026**

`git filter-branch` is officially deprecated. Its own manual page redirects to `git filter-repo`. It is slower, error-prone and has known security issues. Always use `git filter-repo` instead.

```bash
# Wrong (deprecated)
git filter-branch --tree-filter 'rm -f config/secrets.json' HEAD

# Correct
git filter-repo --path config/secrets.json --invert-paths
```

**Rewriting history that others have already fetched**

```bash
# After any force-push that rewrites history,
# all collaborators must re-clone (not pull)
git clone https://github.com/OWNER/REPO.git

# They cannot run git pull because the histories are incompatible
# Communicate this clearly before rewriting shared history
```

**Running `git gc` after a disaster**

```bash
# DO NOT run git gc after losing commits
# It permanently deletes unreachable objects

# Instead, check the reflog first
git reflog

# Then check for dangling objects
git fsck --full --no-reflogs --unreachable --lost-found
ls .git/lost-found/commit/

# Only run gc after you have recovered everything you need
```

---

## Submodule mistakes

**Submodule shows as modified after clone**

```bash
# The submodule was not initialised
git submodule init
git submodule update

# Or do both at once
git submodule update --init --recursive
```

**Submodule URL changed (e.g. platform migration)**

```bash
git submodule set-url path/to/submodule https://new.url/repo.git
git submodule sync
git submodule update --init
git add .gitmodules
git commit -m "chore: update submodule URL after migration"
```

**Committed inside a submodule instead of the parent**

```bash
# In the submodule directory, check which repo you are in
git remote -v

# If you committed to the submodule accidentally,
# the parent still points to the old submodule commit
# You need to push the submodule change first, then update the parent pointer
cd submodule-dir
git push

cd ..
git add submodule-dir
git commit -m "chore: update submodule pointer"
git push
```

---

## Large file mistakes

**Repository is slow because of large binary files in history**

```bash
# Find large objects in history
git rev-list --objects --all | sort -k 2 | \
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | \
  grep '^blob' | sort -k 3 -rn | head -20

# Remove them with filter-repo
git filter-repo --path path/to/large-file.bin --invert-paths

# Add to LFS going forward
git lfs track "*.bin"
git add .gitattributes
git commit -m "chore: track large files with LFS"
```

**LFS objects missing after clone**

```bash
# Install Git LFS if not installed
git lfs install

# Pull LFS objects
git lfs pull

# Or clone with LFS from the start
GIT_LFS_SKIP_SMUDGE=0 git clone <url>
```

**LFS objects lost during platform migration**

```bash
# The correct LFS migration sequence
git clone --mirror https://source.example.com/user/repo.git
cd repo.git
git lfs fetch --all origin
git push --mirror https://dest.example.com/user/repo.git
git lfs push --all https://dest.example.com/user/repo.git
# LFS must be pushed SEPARATELY - it is not part of git push --mirror
```

---

## Platform mistakes

**GitHub: using classic branch protection instead of Rulesets**

Classic branch protection is legacy. Rulesets stack, apply at the organisation level, cover the fork network and have more controls. Migrate via Settings > Rules > Rulesets > New branch ruleset, or import from existing branch protection.

**GitLab: `git filter-branch` tutorial for removing secrets**

GitLab's own older documentation still references `git filter-branch` in some places. Use `git filter-repo` instead - it is faster, safer and officially recommended.

**GitHub Actions: pinning actions by tag instead of SHA**

The `tj-actions/changed-files` incident (March 2025) showed that tags are mutable - an attacker can move a tag to a malicious commit. Always pin by full commit SHA:

```yaml
# Wrong - tag can be moved
- uses: actions/checkout@v4

# Correct - SHA is immutable
- uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683
```

Use Dependabot to keep SHA pins current:

```yaml
# .github/dependabot.yml
updates:
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule: { interval: "weekly" }
```

**GitHub: long-lived npm/PyPI tokens in Actions secrets**

Long-lived publish tokens are a supply chain risk. Use OIDC Trusted Publishing instead - no token stored anywhere:

```yaml
permissions:
  id-token: write
  contents: read

- uses: pypa/gh-action-pypi-publish@release/v1
  # No token needed - OIDC handles authentication
```

**Bitbucket: app passwords after June 2026**

Atlassian is deprecating Bitbucket app passwords on June 9, 2026. Migrate all integrations to API tokens before that date.

---

## Team workflow mistakes

**Opening a PR against the wrong base branch**

```bash
# On GitHub, change the base in the PR UI before merging
# Or close the PR and reopen targeting the correct branch

# If the branch needs to be rebased onto the correct base
git rebase --onto correct-base wrong-base feat/my-branch
git push --force-with-lease
```

**Squashing commits before review is complete**

Wait until the PR is approved before squashing fixup commits. Reviewers need to see the incremental changes to re-review. Squashing mid-review forces reviewers to start from scratch.

**Using `git push --force` instead of `git push --force-with-lease`**

```bash
# Wrong - overwrites remote unconditionally
git push --force

# Correct - refuses if remote moved since your last fetch
git push --force-with-lease

# Add to your shell aliases to prevent accidents
alias gpf="git push --force-with-lease"
```

**Not running `git fetch` before branching**

```bash
# Always fetch before creating a branch to start from the latest
git fetch upstream
git switch -c feat/my-feature upstream/main

# Not:
git switch -c feat/my-feature main  # main might be stale
```

**Long-lived feature branches**

The longer a branch lives, the harder the eventual merge. Aim for branches that live hours to a few days. For large features:
- Use feature flags to merge incomplete work to `main` safely
- Use stacked PRs: split the feature into a series of small reviewable PRs, each building on the last
- Merge main into your branch (or rebase) at least daily to stay current

**Not setting up `upstream` remote on forks**

```bash
# After forking and cloning, always add upstream
git remote add upstream https://github.com/OWNER/REPO.git
git remote -v
# Verify: origin = your fork, upstream = original repo
```

---

## Sources and Further Reading

- [git-scm.com: undoing things](https://git-scm.com/book/en/v2/Git-Basics-Undoing-Things)
- [GitHub: removing sensitive data](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [git-filter-repo documentation](https://github.com/newren/git-filter-repo)
- [GitHub: about push protection](https://docs.github.com/en/code-security/secret-scanning/push-protection-for-repositories-and-organizations)
- [GitHub: about rulesets](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets)
- [Git LFS documentation](https://git-lfs.com/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

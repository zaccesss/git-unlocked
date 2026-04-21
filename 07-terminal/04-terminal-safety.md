# Terminal Safety

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

The terminal is powerful because it does exactly what you tell it, with no confirmation dialogs, no Recycle Bin, and no undo button. That same directness makes certain commands genuinely dangerous. This file covers which Git commands can destroy work, which shell commands have no recovery, how secrets end up in your shell history and what to do about it, and what safety nets actually exist.

---

## Table of Contents

1. [Why the terminal has no undo](#1-why-the-terminal-has-no-undo)
2. [Dangerous Git commands](#2-dangerous-git-commands)
3. [Dangerous shell commands](#3-dangerous-shell-commands)
4. [Your safety nets](#4-your-safety-nets)
5. [API keys and secrets in shell history](#5-api-keys-and-secrets-in-shell-history)
6. [What to do if you commit a secret](#6-what-to-do-if-you-commit-a-secret)
7. [Building safe habits](#7-building-safe-habits)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. Why the terminal has no undo

When you delete a file in a graphical file manager on Windows or macOS, it goes to the Recycle Bin or Trash. You can recover it. When you delete a file with `rm` in the terminal, the deletion is immediate and permanent - the kernel calls `unlink()`, removes the directory entry, and the filesystem marks that space as available. There is no staging area for deletion, no bin, no undo. Recovery tools like `extundelete` exist but are forensic-grade - unreliable on SSDs with TRIM, journaling filesystems, and modern COW filesystems.

Git adds a partial safety net for work that has been committed - the reflog (covered in section 4). But the reflog only tracks commits and reference changes. If you have uncommitted work and destroy it, the reflog cannot help you.

This is not a reason to avoid the terminal. It is a reason to understand which commands are safe, which are dangerous, and how to build habits that protect you.

---

## 2. Dangerous Git commands

These Git commands can permanently destroy work. Each one is useful - the goal is not to avoid them but to understand exactly what they do before running them.

### git reset --hard

```bash
git reset --hard HEAD      # discard all uncommitted changes
git reset --hard HEAD~3    # move back 3 commits, discard everything since
git reset --hard abc1234   # move to a specific commit, discard everything since
```

`git reset --hard` moves the branch pointer to a different commit AND updates the working tree and staging area to match. Any uncommitted changes - files you edited, files you staged - are gone. Not in the reflog. Not recoverable.

The commits you "lost" by moving the branch pointer are not deleted immediately - they remain as dangling objects and are recoverable via `git reflog` for 30-90 days. But your **uncommitted work** that was in the working tree is gone permanently.

> [!WARNING]
> Always stash or commit before running `git reset --hard`. Run `git status` first to see what you have. If you have untracked files you care about, `git reset --hard` does not touch them - but `git clean` does.

### git clean

`git clean` removes untracked files - files Git has never seen. These are not in any commit and have never been staged, so there is nothing to recover them from.

```bash
git clean -n               # dry run - shows what WOULD be deleted
git clean -f               # delete untracked files (respects .gitignore)
git clean -fd              # delete untracked files AND directories
git clean -fdx             # delete everything including .gitignore'd files
git clean -fdX             # delete ONLY .gitignore'd files (build output etc.)
```

> [!CAUTION]
> `git clean -fdx` is the most dangerous form. It deletes your `node_modules`, `.env`, `dist`, `.venv`, build output - everything. Use `-n` first to see what will be removed.

Git requires `-f` (force) by default because `clean.requireForce=true` is set. The `-n` dry run flag bypasses this. Always run a dry run first.

### git checkout -- . and git restore .

```bash
git checkout -- .          # discard all unstaged changes (older syntax)
git restore .              # discard all unstaged changes (current syntax)
git restore --staged .     # unstage all staged changes (keeps files modified)
git restore --staged --worktree .  # unstage AND discard
```

`git restore .` discards all changes in tracked files that have not been staged. This is the equivalent of clicking "discard all changes" in a GUI. Irrecoverable.

### git push --force

```bash
git push --force                     # force push, overwrites remote unconditionally
git push --force-with-lease          # safer: rejects if remote has changed
git push --force-with-lease --force-if-includes  # safest: also checks your reflog
```

A regular `git push --force` overwrites the remote branch with your local branch, regardless of what the remote contains. If teammates have committed and pushed since your last pull, their commits are gone from the remote. They still have them locally, but anyone who pulled after them has lost the commits too.

`--force-with-lease` is safer - it rejects the push if the remote ref is not where you expect (i.e. if someone else pushed). Use this instead of `--force`.

`--force-if-includes` (Git 2.30+) is even safer - it additionally verifies via your local reflog that your branch includes the remote tip. This prevents a subtle failure mode where a background `git fetch --all` silently updates your remote-tracking refs, defeating `--force-with-lease`.

> [!IMPORTANT]
> Never force push to a shared branch (main, develop, release). Only force push to branches that are yours alone. The right use case is: you rebased a feature branch, now you need to update the remote to match.

### git branch -D

```bash
git branch -d feature/login   # delete only if merged (safe)
git branch -D feature/login   # force delete even if not merged
```

`-D` deletes a branch even if it has commits not present in any other branch. Those commits are not immediately deleted - they are dangling objects recoverable via `git reflog` and `git fsck` for about 30 days. But the branch name is gone, and finding the commits is inconvenient.

---

## 3. Dangerous shell commands

These are shell-level commands that can cause permanent, unrecoverable data loss.

### rm -rf

```bash
rm -rf directory/    # delete a directory and everything inside it
rm -rf *             # delete everything in the current directory
rm -rf /             # on unprotected systems: delete the entire filesystem
```

`rm -rf` is permanent. No bin, no recovery. GNU coreutils since version 6.4 (2006) refuse `rm -rf /` without `--no-preserve-root`, but `rm -rf /home` or `rm -rf ~/` works without complaint.

The classic accidents:

```bash
# Variable is empty - becomes rm -rf /
rm -rf $MYVAR/subfolder

# The safe version - errors if MYVAR is unset or empty
rm -rf "${MYVAR:?MYVAR is unset}/subfolder"
```

```bash
# Typo - space between ~ and / turns into two separate arguments
rm -rf ~ /builds   # deletes home directory AND /builds
```

**Safer alternatives**: the `trash` command sends files to the system Trash/Recycle Bin instead of deleting immediately.

```bash
# macOS
brew install trash
alias rm=trash

# Linux
pip install trash-cli
alias rm=trash-put
```

### Pipes that delete

```bash
# Broken - fails on filenames with spaces
find . -name "*.tmp" | xargs rm

# Safe version
find . -name "*.tmp" -print0 | xargs -0 rm

# Even cleaner
find . -name "*.tmp" -delete
```

### Output redirection truncation

```bash
# The > operator truncates the file BEFORE the command runs
# This deletes the content of important.txt even if the command fails
sort important.txt > important.txt   # file is now empty
```

Use `set -o noclobber` in bash/zsh to prevent accidental overwrites with `>`. Use `>|` when you intentionally want to overwrite.

### Other dangerous commands

`dd` - copies raw data between devices. `dd if=/dev/zero of=/dev/sda` overwrites a drive with zeros. Always verify the `of=` target before running.

`chmod -R 777 /` or `chmod -R 777 .` applied at the wrong level - many system tools (ssh, sudo, cron) require specific permissions on their key files and will refuse to operate if permissions are too open. Applying world-write to system directories can make a system non-functional.

---

## 4. Your safety nets

These mechanisms exist to help you recover from mistakes - but each has limits.

### git reflog

The reflog records every time HEAD moves: commits, resets, checkouts, rebases, merges, cherry-picks. It is stored locally in `.git/logs/HEAD` and `.git/logs/refs/heads/`.

```bash
git reflog                    # show recent HEAD movements
git reflog --all              # include all refs
git reflog show feature/login # show reflog for a specific branch
```

Output looks like:

```
abc1234 HEAD@{0}: commit: add login validation
def5678 HEAD@{1}: reset: moving to HEAD~1
ghi9012 HEAD@{2}: commit: initial login form
```

Recover a commit you reset away:

```bash
# Find the commit in the reflog
git reflog

# Recover it - create a new branch at that commit
git checkout -b recovered-work HEAD@{2}

# Or cherry-pick a specific commit onto your current branch
git cherry-pick abc1234
```

Reflog entries expire after 90 days (for reachable commits) or 30 days (for unreachable/dangling commits). The defaults are set by `gc.reflogExpire` and `gc.reflogExpireUnreachable`.

> [!IMPORTANT]
> The reflog is **local only**. It is not cloned, not pushed, not shared. Your teammate's reflog cannot help you recover your lost commits, and vice versa. Cloning a repository does not give you the original reflog.

### git stash

Stashing before a risky operation is the best habit you can build. It takes two seconds and means you always have a recovery point.

```bash
git stash push -m "WIP before reset"    # stash with a descriptive name
git stash push -u -m "WIP including untracked"  # include untracked files
git stash list                           # see all stashes
git stash show -p stash@{0}             # see the diff of the most recent stash
git stash pop                            # apply and remove most recent stash
git stash apply stash@{1}               # apply a specific stash without removing
git stash branch recovered stash@{0}    # create a branch from a stash
```

Stashes survive `git reset --hard`. They do not survive `git stash drop` or `git stash clear`.

### Dry runs

Many destructive commands support a dry run mode that shows what would happen without doing it.

```bash
git clean -n              # preview what git clean would delete
git rm --dry-run *.log    # preview what git rm would remove
git merge --no-commit --no-ff feature  # test merge without creating the commit
rsync -n source/ dest/    # preview rsync before running it
```

---

## 5. API keys and secrets in shell history

Shell history is a persistent record of every command you run, stored as plain text on disk. If you paste an API key into a command, it lives in your history file where anyone with access to your account can read it.

### How history works on each platform

🐧🍎 **bash** stores history in `~/.bash_history`. The file is written when the shell exits. Commands containing your API key are recorded verbatim.

**Control what bash records**:

```bash
# In ~/.bashrc
export HISTCONTROL=ignoreboth:erasedups
# ignorespace - skip commands starting with a space
# ignoredups - skip consecutive duplicate commands
# erasedups - delete all previous duplicates of a command

export HISTIGNORE='ls:cd:pwd:clear:history'  # never record these
export HISTSIZE=100000        # commands in memory
export HISTFILESIZE=200000    # commands on disk
shopt -s histappend           # append, do not overwrite
PROMPT_COMMAND='history -a'   # save every command immediately
```

**Skip recording a command**: prefix with a space. With `HISTCONTROL=ignorespace`, any command starting with a space is not saved to history.

```bash
 export GITHUB_TOKEN=ghp_abc123...   # leading space - not saved to history
```

🐧🍎 **zsh** stores history in `~/.zsh_history`. Set `HISTFILE`, `HISTSIZE` (in-memory), and `SAVEHIST` (file size - zsh writes nothing without this).

```zsh
# In ~/.zshrc
HISTFILE=~/.zsh_history
HISTSIZE=100000
SAVEHIST=100000
setopt HIST_IGNORE_SPACE     # skip space-prefixed commands
setopt HIST_IGNORE_ALL_DUPS  # remove old duplicates
setopt INC_APPEND_HISTORY    # write immediately, not on exit
setopt SHARE_HISTORY         # share across terminal tabs
```

🐟 **fish** stores history in `~/.local/share/fish/fish_history`. Commands starting with a space are automatically excluded - no configuration needed. `fish --private` starts a session with no history at all.

🪟 **PowerShell (PSReadLine)** stores history at `%APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt` (Windows) or `~/.local/share/powershell/PSReadLine/ConsoleHost_history.txt` (macOS/Linux). History is written after every command, not on exit.

> [!CAUTION]
> `Clear-History` in PowerShell clears the in-session list but **does not delete the PSReadLine history file**. Your secrets are still on disk. To actually clear history: `Remove-Item (Get-PSReadLineOption).HistorySavePath`

Add a sensitive-data filter to your PowerShell profile:

```powershell
Set-PSReadLineOption -AddToHistoryHandler {
    param([string]$line)
    $sensitive = 'password|asplaintext|token|apikey|secret|bearer|client_secret'
    return ($line -notmatch $sensitive)
}
```

### The right way to use secrets in commands

**Never** embed a secret directly in a command:

```bash
# WRONG - token saved to history
curl -H "Authorization: Bearer ghp_mytoken123" https://api.github.com/user

# WRONG - token saved to .git/config and history
git clone https://user:mytoken123@github.com/org/repo
```

**Always** use environment variables:

```bash
# Set securely - note the leading space to skip history (or use a separate .env file)
 export GITHUB_TOKEN=ghp_mytoken123

# Then use it in commands - only the variable name is saved to history
curl -H "Authorization: Bearer $GITHUB_TOKEN" https://api.github.com/user
git clone https://x-access-token:${GITHUB_TOKEN}@github.com/org/repo
```

**Use `.env` files** to store environment variables for a project:

```bash
# .env (never commit this - add to .gitignore)
GITHUB_TOKEN=ghp_mytoken123
DATABASE_URL=postgres://user:pass@localhost/mydb
AWS_ACCESS_KEY_ID=AKIA...
```

Load them without adding to history:

```bash
# bash/zsh
set -a; source .env; set +a

# Or use direnv (automatically loads .env when you cd into the directory)
brew install direnv
eval "$(direnv hook zsh)"   # add to .zshrc
direnv allow .              # run once per project to approve the .envrc
```

**Add `.env` to your global gitignore** so you never accidentally commit it:

```bash
echo ".env" >> ~/.config/git/ignore
echo ".env.local" >> ~/.config/git/ignore
echo ".env.*.local" >> ~/.config/git/ignore
```

**Use password managers for interactive secrets**:

```bash
# 1Password CLI
export GITHUB_TOKEN=$(op read "op://Personal/GitHub PAT/credential")

# Bitwarden CLI
export GITHUB_TOKEN=$(bw get password "GitHub PAT")
```

### Deleting a secret from history

If a secret slipped into your history file, remove it:

```bash
# bash - delete a specific line number
history -d 42

# bash - delete a range
history -d 42 50

# bash - clear entire session history
history -c

# bash - write cleared session to file
history -c && history -w

# zsh - delete from file
fc -W && grep -v 'mytoken' ~/.zsh_history > /tmp/clean_history && mv /tmp/clean_history ~/.zsh_history

# fish
history delete --prefix "curl -H Authorization"

# PowerShell - remove the file entirely and start fresh
Remove-Item (Get-PSReadLineOption).HistorySavePath
```

After clearing history, **rotate the exposed secret immediately**. Clearing history does not un-expose a secret that was already visible to someone who read the file.

---

## 6. What to do if you commit a secret

If you commit a secret and push it to a public repository, treat it as compromised immediately - bots scrape GitHub continuously and can capture exposed credentials within seconds. The correct order of operations is:

**Step 1 - Rotate first, rewrite second**. Go to the service that issued the credential (GitHub, AWS, Stripe, etc.) and revoke or rotate it immediately. Do this before anything else. Once revoked, the secret is useless even if it remains visible in history.

**Step 2 - Check if GitHub caught it**. GitHub's Secret Scanning alerts you when it detects known secret patterns pushed to public repositories. Check Security → Secret Scanning Alerts in your repository settings.

**Step 3 - Rewrite history with git-filter-repo**. `git-filter-repo` is the official replacement for the deprecated `git filter-branch`.

```bash
# Install
pip install git-filter-repo   # or: brew install git-filter-repo

# Remove a specific file from all history
git filter-repo --path secrets.yml --invert-paths

# Replace a specific secret string everywhere
echo 'ghp_oldtoken==>***REMOVED***' > replacements.txt
git filter-repo --replace-text replacements.txt

# Force push all branches and tags after rewriting
git push --force --all
git push --force --tags
```

**Step 4 - Expire the old objects**:

```bash
git reflog expire --expire=now --all
git gc --prune=now --aggressive
```

**Step 5 - Alert collaborators**. Anyone who cloned or forked the repository before you rewrote history still has the old history. They need to re-clone or carefully rebase their local work. Coordinate this - do not quietly rewrite history on a shared repository without telling everyone.

**BFG Repo-Cleaner** is an alternative that is faster on large repositories for common cases:

```bash
# Install
brew install bfg

# Remove a file from all history (except the current commit)
bfg --delete-files secrets.yml

# Replace text
bfg --replace-text passwords.txt

# Clean up
git reflog expire --expire=now --all && git gc --prune=now --aggressive
git push --force
```

> [!NOTE]
> BFG protects your most recent commit by default. Fix the secret in the working tree first, commit that fix, then run BFG to clean historical copies.

---

## 7. Building safe habits

A small set of habits eliminates most terminal accidents.

**Always check before resetting**:

```bash
# See what you have before you throw it away
git status
git stash list
git log --oneline -5
```

**Stash before risky operations**:

```bash
git stash push -u -m "WIP - before reset experiment"
git reset --hard HEAD~2
# If it went wrong:
git stash pop
```

**Use dry runs for destructive commands**:

```bash
git clean -n      # before git clean -f
git rm --dry-run  # before git rm
find . -name "*.tmp" -print  # before piping to rm
```

**Use `--force-with-lease` instead of `--force`**:

```bash
# Add an alias so you never accidentally use bare --force
git config --global alias.fpush 'push --force-with-lease --force-if-includes'
git fpush
```

**Never store secrets in shell commands**. Use environment variables from `.env` files. Use leading spaces to skip history for one-off sensitive commands. Use a password manager CLI for programmatic access.

**Separate `.gitignore` patterns for secrets**:

```gitignore
# Add to every project's .gitignore
.env
.env.local
.env.*.local
*.pem
*.key
*.p12
*.pfx
secrets.yml
secrets.json
config/credentials.yml.enc
```

---

## 8. Try It Yourself

**Exercise 1 - use git reflog to find a lost commit**

In a test repository, make two commits. Run `git reset --hard HEAD~2` to move back. Now run `git reflog` and find the commits you "lost". Create a branch at that point: `git checkout -b recovered HEAD@{1}`. Verify your commits are there with `git log --oneline`.

**Exercise 2 - practice safe git clean**

In a test repository, create several untracked files and directories:

```bash
touch untracked1.txt untracked2.log
mkdir temp-dir && touch temp-dir/file.txt
```

Run `git clean -n` first to see what would be deleted. Then run `git clean -fd` to actually delete. Notice that `.log` files were also cleaned. Check which files remain.

**Exercise 3 - add history protection to your shell**

Add `HISTCONTROL=ignoreboth:erasedups` to your `~/.bashrc` or the equivalent zsh settings to your `~/.zshrc`. Reload your config. Test that a command starting with a space is not saved: type a space then `echo secretvalue` (with a leading space before the backtick). Check `history | tail -5` - the command should not appear.

**Exercise 4 - create a .env workflow**

Create a test `.env` file with a fake API key:

```bash
echo "TEST_API_KEY=fake_key_12345" > .env
echo ".env" >> .gitignore
```

Load it with `set -a; source .env; set +a`. Verify with `echo $TEST_API_KEY`. Then check `git status` - confirm `.env` is ignored and will not be committed.

**Exercise 5 - practice the stash-before-reset habit**

Make some changes to files in a test repository but do not commit. Run `git stash push -m "my test stash"`. Run `git reset --hard HEAD`. Run `git stash pop` to get your work back. Confirm the files are restored.

---

## 9. Common Mistakes

Running `git clean -fdx` without a dry run first. The `-x` flag deletes `.gitignore`d files, which includes your `node_modules`, `.env`, `dist`, build output and anything else you intentionally excluded. Always run `git clean -n` first and read the output carefully before running the actual clean.

Using `git push --force` on a shared branch. Any force push to `main` or `develop` rewrites the branch's history on the remote. Anyone who has pulled since your last push now has commits that are no longer in the remote branch. Use `--force-with-lease` and only force push to branches that are exclusively yours.

Thinking that clearing shell history makes a secret safe. If a secret was in your history file and your machine has been accessed by others, or your files are synced to a cloud service, or your shell history was logged somewhere else, the secret is already exposed. Rotating the credential is the only real fix.

Embedding tokens directly in git remote URLs: `git remote set-url origin https://mytoken@github.com/...`. This saves the token to `.git/config` which is a plain text file in the repository directory. Anyone with filesystem access to the repo can read it. Use a credential helper instead.

Forgetting that `git reset --hard` destroys uncommitted work. The reflog records ref changes but not working tree state. If you have files modified but not staged, or staged but not committed, `git reset --hard` removes them permanently. The stash-first habit eliminates this risk entirely.

---

## 10. Summary

The terminal has no undo for file deletion and no Recycle Bin for `rm`. The most dangerous Git commands are `git reset --hard` (destroys uncommitted changes), `git clean -fdx` (destroys untracked files including ignored files), `git checkout -- .` and `git restore .` (discard unstaged changes), and `git push --force` (overwrites remote history). Use `--force-with-lease --force-if-includes` instead of bare `--force`.

The reflog is your primary Git safety net - it records every HEAD movement and lets you recover commits for 30-90 days. It only tracks commits, not uncommitted working tree changes. Stashing before risky operations gives you a reliable recovery point. Dry run flags (`git clean -n`, `git rm --dry-run`) let you preview before destroying.

Shell history records every command you type to a plain text file. API keys and tokens must never appear directly in commands. Use environment variables loaded from `.env` files, leading spaces to skip history, or password manager CLIs. If a secret is committed and pushed, rotate the credential immediately, then rewrite history with `git-filter-repo` or BFG Repo-Cleaner.

---

## 11. Sources

- [git-reflog documentation](https://git-scm.com/docs/git-reflog)
- [git-clean documentation](https://git-scm.com/docs/git-clean)
- [git-filter-repo documentation](https://github.com/newren/git-filter-repo)
- [BFG Repo-Cleaner](https://rtyley.github.io/bfg-repo-cleaner/)
- [GitHub Secret Scanning](https://docs.github.com/en/code-security/secret-scanning)
- [Removing sensitive data from a repository - GitHub Docs](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository)
- [direnv documentation](https://direnv.net)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

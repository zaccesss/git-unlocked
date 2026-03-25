# GitHub CLI

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

GitHub CLI (`gh`) is the official command-line tool for GitHub. It lets you create repositories, open pull requests, manage issues, trigger workflows, review code and do almost everything you can do on GitHub.com - without leaving your terminal. This file covers installation on all platforms, authentication, every major command and practical workflows.

---

## Table of Contents

- [What GitHub CLI is](#what-github-cli-is)
- [Installing GitHub CLI](#installing-github-cli)
- [Authenticating](#authenticating)
- [Repositories](#repositories)
- [Issues](#issues)
- [Pull requests](#pull-requests)
- [GitHub Actions from the CLI](#github-actions-from-the-cli)
- [Gists](#gists)
- [Releases](#releases)
- [SSH keys and GPG keys](#ssh-keys-and-gpg-keys)
- [Aliases](#aliases)
- [GitHub CLI extensions](#github-cli-extensions)
- [Scripting with gh](#scripting-with-gh)
- [gh vs git - what each one does](#gh-vs-git---what-each-one-does)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What GitHub CLI Is

`gh` is a separate tool from `git`. Git manages your local version history. `gh` talks to the GitHub API and manages the GitHub-specific layer on top: repositories, pull requests, issues, Actions, releases and so on.

You can use both together in the same terminal session. A typical workflow might be:

```bash
git add . && git commit -m "feat: add login page"  # git manages the commit
git push                                            # git pushes to GitHub
gh pr create --title "Add login page" --body ""    # gh opens a pull request
```

---

## Installing GitHub CLI

🪟 **Windows:**

```powershell
winget install --id GitHub.cli
```

Or download the `.msi` installer from `cli.github.com`.

🍎 **Mac:**

```bash
brew install gh
```

🐧 **Linux (Debian/Ubuntu):**

```bash
sudo apt update && sudo apt install gh
```

🐧 **Linux (Fedora/RHEL):**

```bash
sudo dnf install gh
```

🐧 **Linux (any - binary download):**

```bash
# Replace VERSION and ARCH as needed
curl -LO https://github.com/cli/cli/releases/latest/download/gh_Linux_amd64.tar.gz
tar -xzf gh_Linux_amd64.tar.gz
sudo mv gh_*/bin/gh /usr/local/bin/
```

**Verify installation:**

```bash
gh --version
```

---

## Authenticating

Before you can use `gh`, authenticate with your GitHub account:

```bash
gh auth login
```

Follow the prompts:

1. Select **GitHub.com** (or GitHub Enterprise if your organisation uses it)
2. Select **HTTPS** as the protocol (recommended for most users)
3. Select **Login with a web browser** (easiest) or paste a token
4. If using the browser: copy the one-time code shown, press Enter, and paste the code into the browser page that opens

**Check authentication status:**

```bash
gh auth status
```

**Log out:**

```bash
gh auth logout
```

**Using a Personal Access Token instead of browser login:**

```bash
gh auth login --with-token < token.txt
```

Where `token.txt` contains your PAT on a single line.

---

## Repositories

**Create a new repository:**

```bash
gh repo create my-project --public --clone
gh repo create my-project --private --description "My private project"
```

**Clone a repository:**

```bash
gh repo clone USERNAME/REPO
gh repo clone REPO           # if the repo belongs to you
```

**View repository info:**

```bash
gh repo view
gh repo view USERNAME/REPO
gh repo view --web           # open in browser
```

**Fork a repository:**

```bash
gh repo fork USERNAME/REPO
gh repo fork USERNAME/REPO --clone    # fork and clone in one step
```

**List your repositories:**

```bash
gh repo list
gh repo list --limit 50
gh repo list --public
gh repo list --private
```

**Delete a repository:**

```bash
gh repo delete USERNAME/REPO --yes
```

---

## Issues

**List issues:**

```bash
gh issue list
gh issue list --state open
gh issue list --state closed
gh issue list --label bug
gh issue list --assignee @me
```

**View an issue:**

```bash
gh issue view 42
gh issue view 42 --web      # open in browser
```

**Create an issue:**

```bash
gh issue create --title "Login button broken on mobile" --body "Steps to reproduce..."
gh issue create                   # opens interactive prompts
```

**Close an issue:**

```bash
gh issue close 42
gh issue close 42 --reason "not planned"
```

**Reopen an issue:**

```bash
gh issue reopen 42
```

**Comment on an issue:**

```bash
gh issue comment 42 --body "Fixed in commit abc1234"
```

---

## Pull Requests

**Create a pull request:**

```bash
gh pr create
gh pr create --title "Add login page" --body "Closes #12"
gh pr create --base main --head feature/login
gh pr create --draft
gh pr create --web              # opens the PR form in browser
```

**List pull requests:**

```bash
gh pr list
gh pr list --state open
gh pr list --state closed
gh pr list --author @me
gh pr list --reviewer @me
```

**View a pull request:**

```bash
gh pr view 15
gh pr view 15 --web
```

**Check out a pull request locally:**

```bash
gh pr checkout 15
```

This fetches the PR branch and checks it out locally - useful for testing someone else's PR.

**Review a pull request:**

```bash
gh pr review 15 --approve
gh pr review 15 --request-changes --body "Please add tests"
gh pr review 15 --comment --body "Looks good but see inline notes"
```

**Merge a pull request:**

```bash
gh pr merge 15
gh pr merge 15 --squash
gh pr merge 15 --rebase
gh pr merge 15 --merge --delete-branch
```

**Close a PR without merging:**

```bash
gh pr close 15
```

---

## GitHub Actions from the CLI

**List workflow runs:**

```bash
gh run list
gh run list --workflow ci.yml
gh run list --branch main
```

**View a run:**

```bash
gh run view 1234567890
gh run view --log               # view full logs
```

**Watch a run in real time:**

```bash
gh run watch 1234567890
```

**Trigger a workflow manually:**

```bash
gh workflow run ci.yml
gh workflow run ci.yml --ref feature/my-branch
gh workflow run ci.yml -f environment=staging
```

**List workflows:**

```bash
gh workflow list
```

**Enable or disable a workflow:**

```bash
gh workflow enable ci.yml
gh workflow disable ci.yml
```

---

## Gists

**Create a gist:**

```bash
gh gist create myfile.py
gh gist create myfile.py --public --description "Useful script"
gh gist create --web
echo "print('hello')" | gh gist create -
```

**List your gists:**

```bash
gh gist list
```

**View a gist:**

```bash
gh gist view GIST_ID
```

**Clone a gist:**

```bash
gh gist clone GIST_ID
```

---

## Releases

**Create a release:**

```bash
gh release create v1.0.0
gh release create v1.0.0 --title "Version 1.0.0" --notes "First stable release"
gh release create v1.0.0 --notes-file CHANGELOG.md
gh release create v1.0.0 dist/*.zip   # attach files
```

**List releases:**

```bash
gh release list
```

**Download a release asset:**

```bash
gh release download v1.0.0
gh release download v1.0.0 --pattern "*.zip"
```

**Delete a release:**

```bash
gh release delete v1.0.0 --yes
```

---

## SSH Keys and GPG Keys

**Add an SSH key to your GitHub account:**

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub --title "My laptop"
```

**List SSH keys:**

```bash
gh ssh-key list
```

**Add a GPG key:**

```bash
gh gpg-key add my-key.pub
```

**List GPG keys:**

```bash
gh gpg-key list
```

---

## Aliases

Create shorthand aliases for long commands you use frequently:

```bash
gh alias set prc "pr create --fill"
gh alias set prl "pr list --author @me"
```

Use them:

```bash
gh prc
gh prl
```

**List all aliases:**

```bash
gh alias list
```

**Delete an alias:**

```bash
gh alias delete prc
```

---

## GitHub CLI Extensions

`gh` supports community extensions that add new commands.

**Install an extension:**

```bash
gh extension install github/gh-copilot        # Copilot in the terminal
gh extension install dlvhdr/gh-dash           # dashboard TUI
```

**List installed extensions:**

```bash
gh extension list
```

**Upgrade all extensions:**

```bash
gh extension upgrade --all
```

**Browse available extensions:**

```bash
gh extension browse
```

---

## Scripting with gh

`gh` outputs JSON with `--json` for scripting and automation:

```bash
gh issue list --json number,title,state
gh pr list --json number,title,headRefName --jq '.[].headRefName'
```

Use `--jq` to filter JSON output with jq syntax directly.

**Example - list all open PR branch names:**

```bash
gh pr list --state open --json headRefName --jq '.[].headRefName'
```

**Example - count open issues:**

```bash
gh issue list --state open --json number | jq length
```

**Using gh in a shell script:**

```bash
#!/bin/bash
PR_NUMBER=$(gh pr create --title "$1" --body "$2" --json number --jq '.number')
echo "Created PR #$PR_NUMBER"
gh pr merge "$PR_NUMBER" --squash --delete-branch
```

---

## gh vs git - What Each One Does

| Task | Tool |
|---|---|
| Stage and commit changes | `git` |
| Push and pull code | `git` |
| Manage branches locally | `git` |
| Create a GitHub repository | `gh` |
| Open a pull request | `gh` |
| Review and merge a PR | `gh` |
| Create and close issues | `gh` |
| Trigger GitHub Actions | `gh` |
| Create releases | `gh` |
| Manage SSH keys on GitHub | `gh` |

They are complementary. Use both in the same workflow.

---

## Try It Yourself

**Step 1.** Install `gh` using the command for your OS above.

**Step 2.** Authenticate:

```bash
gh auth login
```

**Step 3.** Create a repository and clone it in one step:

```bash
gh repo create gh-cli-practice --public --clone
cd gh-cli-practice
```

**Step 4.** Create a file, commit and push with git:

🪟 **Windows:**

```powershell
echo "# Practice repo" > README.md
git add README.md
git commit -m "add: README"
git push -u origin main
```

🍎 **Mac and 🐧 Linux:**

```bash
echo "# Practice repo" > README.md
git add README.md && git commit -m "add: README" && git push -u origin main
```

**Step 5.** Create an issue:

```bash
gh issue create --title "Add contributing guide" --body "We need a CONTRIBUTING.md file"
```

**Step 6.** Create a branch, push it and open a PR:

```bash
git checkout -b feature/contributing
echo "# Contributing" > CONTRIBUTING.md
git add CONTRIBUTING.md && git commit -m "add: contributing guide"
git push -u origin feature/contributing
gh pr create --title "Add contributing guide" --body "Closes #1"
```

**Step 7.** View the PR:

```bash
gh pr list
gh pr view --web
```

---

## Common Mistakes

**Confusing `gh` and `git`.** `git push` and `gh pr create` are different tools for different layers. `gh` does not replace `git`.

**Forgetting to authenticate.** If you get authentication errors, run `gh auth status` to check. You may need to re-run `gh auth login`.

**Using `gh repo clone` when you mean `git clone`.** Both work but `gh repo clone` is shorter and sets up authentication automatically. Either is fine.

**Not using `--fill` on `gh pr create`.** The `--fill` flag populates the PR title and body from your commit messages automatically. It is faster than writing them manually every time.

**Expecting `gh` to be available on servers without installing it.** GitHub Actions runners do not have `gh` pre-installed on all runner images. Check the image documentation or install it as a step.

---

## Summary

- `gh` is the official GitHub CLI - it manages the GitHub layer (PRs, issues, Actions, releases) separately from `git` which manages version history
- Install with `winget` (🪟), `brew` (🍎) or `apt`/`dnf` (🐧); authenticate with `gh auth login`
- Core commands: `gh repo`, `gh issue`, `gh pr`, `gh run`, `gh release`, `gh gist`
- Use `--json` and `--jq` for scripting and automation
- Use `gh extension install` to add community tools like the Copilot extension
- Create aliases for long commands you use repeatedly with `gh alias set`

---

## Sources and Further Reading

- [GitHub CLI documentation](https://cli.github.com/manual/) - full command reference
- [GitHub CLI repository](https://github.com/cli/cli) - source code and issue tracker
- [gh extensions](https://github.com/topics/gh-extension) - browse community extensions
- [GitHub CLI releases](https://github.com/cli/cli/releases) - download the latest version

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

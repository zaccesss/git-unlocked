# GitHub CLI

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

GitHub CLI (`gh`) is the official command-line tool for GitHub. It lets you create repositories, open pull requests, manage issues, trigger workflows, review code, create releases and do almost everything you can do on GitHub.com - without leaving your terminal. This file covers installation on all platforms, authentication, every major command group with real usage examples, scripting with JSON output, extensions and practical multi-step workflows.

---

## Table of Contents

- [What GitHub CLI is](#what-github-cli-is)
- [gh vs git - the difference](#gh-vs-git---the-difference)
- [Installing GitHub CLI](#installing-github-cli)
- [Upgrading GitHub CLI](#upgrading-github-cli)
- [Authenticating](#authenticating)
- [Switching between accounts](#switching-between-accounts)
- [Repositories](#repositories)
- [Issues](#issues)
- [Pull requests](#pull-requests)
- [Code review from the CLI](#code-review-from-the-cli)
- [GitHub Actions from the CLI](#github-actions-from-the-cli)
- [Releases](#releases)
- [Gists](#gists)
- [SSH keys and GPG keys](#ssh-keys-and-gpg-keys)
- [Labels](#labels)
- [Milestones](#milestones)
- [Secrets and variables](#secrets-and-variables)
- [Codespaces from the CLI](#codespaces-from-the-cli)
- [Aliases](#aliases)
- [GitHub CLI extensions](#github-cli-extensions)
- [Scripting with gh - JSON output and jq](#scripting-with-gh---json-output-and-jq)
- [Practical multi-step workflows](#practical-multi-step-workflows)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What GitHub CLI Is

`gh` is a separate tool from `git`. Git manages your local version history - commits, branches, merges, remotes. `gh` talks to the GitHub API and manages the GitHub-specific layer on top: repositories, pull requests, issues, Actions, releases and so on.

You use both together in the same workflow. A typical session looks like:

```bash
git checkout -b feature/user-auth          # git: create a branch
# ... write code ...
git add . && git commit -m "feat: add auth"  # git: commit
git push -u origin feature/user-auth        # git: push
gh pr create --title "Add user auth"        # gh: open a PR on GitHub
gh pr view --web                            # gh: open the PR in browser
```

---

## gh vs git - The Difference

| Task | Tool |
|---|---|
| Initialise a repository locally | `git init` |
| Stage and commit changes | `git add` / `git commit` |
| Push and pull code | `git push` / `git pull` |
| Manage branches locally | `git branch` / `git checkout` |
| Create a GitHub repository | `gh repo create` |
| Clone with auth handled automatically | `gh repo clone` |
| Open a pull request | `gh pr create` |
| Review and merge a PR | `gh pr review` / `gh pr merge` |
| Check out a PR branch locally | `gh pr checkout` |
| Create and manage issues | `gh issue create` / `gh issue list` |
| Trigger GitHub Actions workflows | `gh workflow run` |
| View Actions run logs | `gh run view --log` |
| Create a release with assets | `gh release create` |
| Manage SSH keys on your account | `gh ssh-key add` |
| Manage secrets on a repository | `gh secret set` |

---

## Installing GitHub CLI

🪟 **Windows - winget (recommended):**

```powershell
winget install --id GitHub.cli
```

🪟 **Windows - Scoop:**

```powershell
scoop install gh
```

🪟 **Windows - manual:** Download the `.msi` installer from `cli.github.com` and run it.

🍎 **Mac - Homebrew (recommended):**

```bash
brew install gh
```

🍎 **Mac - MacPorts:**

```bash
sudo port install gh
```

🐧 **Linux - Debian/Ubuntu:**

```bash
sudo apt update && sudo apt install gh
```

If `gh` is not in the default apt repositories, add GitHub's official repository first:

```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
  && sudo mkdir -p -m 755 /etc/apt/keyrings \
  && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg \
     | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
  && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
  && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" \
     | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
  && sudo apt update && sudo apt install gh -y
```

🐧 **Linux - Fedora/RHEL/CentOS:**

```bash
sudo dnf install gh
```

🐧 **Linux - any distro (binary):**

```bash
# Get the latest version number first
VERSION=$(curl -s https://api.github.com/repos/cli/cli/releases/latest | grep tag_name | cut -d '"' -f 4 | tr -d 'v')
curl -LO "https://github.com/cli/cli/releases/latest/download/gh_${VERSION}_linux_amd64.tar.gz"
tar -xzf "gh_${VERSION}_linux_amd64.tar.gz"
sudo mv "gh_${VERSION}_linux_amd64/bin/gh" /usr/local/bin/
```

**Verify installation:**

```bash
gh --version
```

---

## Upgrading GitHub CLI

🪟 **Windows:**

```powershell
winget upgrade --id GitHub.cli
```

🍎 **Mac:**

```bash
brew upgrade gh
```

🐧 **Linux (apt):**

```bash
sudo apt update && sudo apt upgrade gh
```

---

## Authenticating

Before using `gh`, authenticate with your GitHub account:

```bash
gh auth login
```

The interactive prompts ask:

1. **Account type:** `GitHub.com` (or GitHub Enterprise Server if your organisation uses it)
2. **Protocol:** HTTPS (recommended for most users) or SSH
3. **Authentication method:** `Login with a web browser` (easiest) or `Paste an authentication token`

If you choose browser login: copy the one-time code shown in the terminal, press Enter and a browser tab opens. Paste the code into the browser and authorise the app.

**Check authentication status:**

```bash
gh auth status
```

This shows which account is active, which scopes are authorised and whether the token is valid.

**Log out:**

```bash
gh auth logout
```

**Authenticate with a Personal Access Token (PAT):**

```bash
gh auth login --with-token <<< "ghp_yourTokenHere"
# Or from a file:
gh auth login --with-token < token.txt
```

**Refresh scopes (if you need to add new permissions to an existing auth):**

```bash
gh auth refresh --scopes read:org,write:packages
```

---

## Switching Between Accounts

If you have multiple GitHub accounts (personal and work), you can authenticate both and switch between them.

**Add a second account:**

```bash
gh auth login --hostname github.com
```

This prompts for auth on the same host with a different account. `gh` stores both tokens.

**Switch the active account:**

```bash
gh auth switch
```

**Or set for a single command:**

```bash
GH_TOKEN=ghp_workToken gh pr list --repo work-org/work-repo
```

---

## Repositories

**Create a new repository:**

```bash
gh repo create my-project --public
gh repo create my-project --private --description "My private project"
gh repo create my-project --public --clone           # create and clone in one step
gh repo create my-project --public --add-readme      # initialise with README
gh repo create my-project --public --license mit     # add MIT licence
gh repo create my-project --public --gitignore Python # add Python .gitignore
```

**Clone a repository:**

```bash
gh repo clone USERNAME/REPO           # clones to ./REPO
gh repo clone REPO                    # if the repo belongs to you
gh repo clone USERNAME/REPO my-dir    # clone to a specific directory
```

`gh repo clone` automatically sets up authentication and uses your preferred protocol (HTTPS or SSH) from your `gh auth` settings.

**View repository information:**

```bash
gh repo view                          # current repository
gh repo view USERNAME/REPO            # specific repository
gh repo view --json name,description,url,stargazerCount
gh repo view --web                    # open in browser
```

**Fork a repository:**

```bash
gh repo fork USERNAME/REPO
gh repo fork USERNAME/REPO --clone            # fork and clone immediately
gh repo fork USERNAME/REPO --remote           # add upstream remote
gh repo fork USERNAME/REPO --clone --remote   # both
```

**List your repositories:**

```bash
gh repo list
gh repo list --limit 50
gh repo list --public
gh repo list --private
gh repo list --fork
gh repo list --source                         # exclude forks
gh repo list --language python
gh repo list USERNAME                         # list another user's public repos
```

**Rename a repository:**

```bash
gh repo rename new-name
```

**Archive a repository:**

```bash
gh repo archive USERNAME/REPO
```

**Delete a repository:**

```bash
gh repo delete USERNAME/REPO --yes
```

**Sync a fork with its upstream:**

```bash
gh repo sync                          # sync current fork with upstream
gh repo sync USERNAME/REPO            # sync a specific fork
```

**Edit repository settings:**

```bash
gh repo edit --description "New description"
gh repo edit --homepage "https://mysite.com"
gh repo edit --visibility public
gh repo edit --enable-issues
gh repo edit --disable-wiki
gh repo edit --default-branch main
```

---

## Issues

**List issues:**

```bash
gh issue list
gh issue list --state open
gh issue list --state closed
gh issue list --state all
gh issue list --label bug
gh issue list --label "good first issue" --label bug   # multiple labels (AND)
gh issue list --assignee @me
gh issue list --assignee USERNAME
gh issue list --author USERNAME
gh issue list --milestone "v2.0"
gh issue list --limit 100
gh issue list --search "login button"         # search in title and body
```

**View an issue:**

```bash
gh issue view 42
gh issue view 42 --comments               # show all comments
gh issue view 42 --web                    # open in browser
```

**Create an issue:**

```bash
gh issue create
gh issue create --title "Login button broken on mobile" --body "Steps to reproduce: 1. Open app on iOS..."
gh issue create --title "Bug" --label bug --label "high priority" --assignee USERNAME
gh issue create --title "Feature request" --milestone "v2.0"
gh issue create --template bug_report.md  # use a specific template
```

**Edit an issue:**

```bash
gh issue edit 42 --title "Updated title"
gh issue edit 42 --add-label bug
gh issue edit 42 --remove-label "needs triage"
gh issue edit 42 --add-assignee USERNAME
gh issue edit 42 --remove-assignee USERNAME
gh issue edit 42 --milestone "v2.0"
```

**Close an issue:**

```bash
gh issue close 42
gh issue close 42 --reason "not planned"
gh issue close 42 --reason completed --comment "Fixed in #67"
```

**Reopen an issue:**

```bash
gh issue reopen 42
```

**Comment on an issue:**

```bash
gh issue comment 42 --body "Fixed in commit abc1234, will be in v1.2.0"
gh issue comment 42 --body-file comment.md    # read body from a file
```

**Pin an issue:**

```bash
gh issue pin 42
gh issue unpin 42
```

**Transfer an issue to another repository:**

```bash
gh issue transfer 42 OWNER/OTHER-REPO
```

**Delete an issue:**

```bash
gh issue delete 42 --yes
```

---

## Pull Requests

**Create a pull request:**

```bash
gh pr create
gh pr create --title "Add login page" --body "Closes #12"
gh pr create --fill                           # use commit messages for title/body
gh pr create --base main --head feature/login
gh pr create --draft
gh pr create --reviewer USERNAME,TEAM
gh pr create --assignee @me
gh pr create --label enhancement
gh pr create --milestone "v2.0"
gh pr create --web                            # open the form in browser
```

**List pull requests:**

```bash
gh pr list
gh pr list --state open
gh pr list --state closed
gh pr list --state merged
gh pr list --author @me
gh pr list --reviewer @me
gh pr list --assignee @me
gh pr list --label "needs review"
gh pr list --base main
gh pr list --search "auth"
```

**View a pull request:**

```bash
gh pr view 15
gh pr view 15 --comments
gh pr view 15 --web
gh pr view                                    # view PR for current branch
```

**Check out a PR branch locally:**

```bash
gh pr checkout 15
gh pr checkout 15 --branch local-name        # check out under a different local name
```

This fetches the PR branch from the contributor's fork and checks it out locally. Extremely useful for testing someone else's PR before approving.

**Edit a pull request:**

```bash
gh pr edit 15 --title "Updated title"
gh pr edit 15 --body "Updated description"
gh pr edit 15 --add-label bug
gh pr edit 15 --remove-assignee USERNAME
gh pr edit 15 --base develop                  # change the base branch
```

**Mark a PR ready for review (take out of draft):**

```bash
gh pr ready 15
```

**Convert a PR to draft:**

```bash
gh pr ready 15 --undo
```

**Merge a pull request:**

```bash
gh pr merge 15
gh pr merge 15 --squash
gh pr merge 15 --rebase
gh pr merge 15 --merge
gh pr merge 15 --squash --delete-branch      # merge and delete the branch
gh pr merge 15 --auto                        # enable auto-merge (merges when checks pass)
gh pr merge                                  # merge PR for current branch
```

**Close a PR without merging:**

```bash
gh pr close 15
gh pr close 15 --comment "Superseded by #18"
```

**Reopen a PR:**

```bash
gh pr reopen 15
```

**Check PR status (CI checks):**

```bash
gh pr checks 15
gh pr checks 15 --watch                      # watch checks update live
gh pr checks                                 # checks for current branch's PR
```

**Diff a PR:**

```bash
gh pr diff 15
gh pr diff 15 --patch                        # output as a .patch file
```

---

## Code Review from the CLI

**Submit a review:**

```bash
gh pr review 15 --approve
gh pr review 15 --request-changes --body "Please add unit tests for the new functions"
gh pr review 15 --comment --body "Looks good overall, left a few inline suggestions"
```

**Add an inline review comment on a specific file and line:**

```bash
gh api repos/OWNER/REPO/pulls/15/comments \
  --method POST \
  --field body="This should handle null inputs" \
  --field path="src/auth.py" \
  --field position=42 \
  --field commit_id="abc1234"
```

For complex inline reviews, use the web interface. For approve/request-changes decisions, the CLI is fast.

---

## GitHub Actions from the CLI

**List workflows:**

```bash
gh workflow list
gh workflow list --all                        # include disabled workflows
```

**View a workflow:**

```bash
gh workflow view ci.yml
gh workflow view ci.yml --web
```

**Enable or disable a workflow:**

```bash
gh workflow enable ci.yml
gh workflow disable ci.yml
```

**Trigger a workflow manually:**

```bash
gh workflow run ci.yml
gh workflow run ci.yml --ref feature/my-branch
gh workflow run ci.yml -f environment=staging -f version=2.0.0
```

The `-f` flag passes inputs to `workflow_dispatch` inputs defined in the YAML.

**List workflow runs:**

```bash
gh run list
gh run list --workflow ci.yml
gh run list --branch main
gh run list --status failure
gh run list --limit 20
gh run list --user USERNAME
```

**View a run:**

```bash
gh run view 1234567890
gh run view 1234567890 --log                  # full log output
gh run view 1234567890 --log-failed           # only failed step logs
gh run view 1234567890 --web
```

**Watch a run in real time:**

```bash
gh run watch 1234567890
gh run watch                                  # watch the most recent run
```

**Download run artefacts:**

```bash
gh run download 1234567890
gh run download 1234567890 --name build-output
gh run download 1234567890 --dir ./downloads
```

**Re-run a failed workflow:**

```bash
gh run rerun 1234567890
gh run rerun 1234567890 --failed              # re-run only failed jobs
```

**Cancel a running workflow:**

```bash
gh run cancel 1234567890
```

---

## Releases

**Create a release:**

```bash
gh release create v1.0.0
gh release create v1.0.0 --title "Version 1.0.0" --notes "First stable release"
gh release create v1.0.0 --notes-file CHANGELOG.md
gh release create v1.0.0 dist/app-linux dist/app-macos dist/app-windows.exe
gh release create v1.0.0 --draft                    # create as draft
gh release create v1.0.0 --prerelease               # mark as pre-release
gh release create v1.0.0 --target main              # create from a specific branch
gh release create v1.0.0 --generate-notes           # auto-generate notes from PRs
```

**List releases:**

```bash
gh release list
gh release list --limit 20
```

**View a release:**

```bash
gh release view v1.0.0
gh release view v1.0.0 --web
```

**Download release assets:**

```bash
gh release download v1.0.0
gh release download v1.0.0 --pattern "*.zip"
gh release download v1.0.0 --dir ./downloads
gh release download v1.0.0 --asset app-linux
```

**Edit a release:**

```bash
gh release edit v1.0.0 --title "Version 1.0.0 - Stable"
gh release edit v1.0.0 --notes "Updated release notes"
gh release edit v1.0.0 --draft=false            # publish a draft release
```

**Delete a release:**

```bash
gh release delete v1.0.0 --yes
gh release delete v1.0.0 --cleanup-tag --yes    # also delete the git tag
```

**Upload additional assets to an existing release:**

```bash
gh release upload v1.0.0 new-asset.zip
```

---

## Gists

**Create a gist:**

```bash
gh gist create myfile.py
gh gist create myfile.py --public --description "Useful Python helper"
gh gist create myfile.py myfile2.py             # multiple files in one gist
echo "print('hello')" | gh gist create -        # from stdin
gh gist create --web                            # open the creation form in browser
```

**List your gists:**

```bash
gh gist list
gh gist list --limit 50
gh gist list --public
gh gist list --secret
```

**View a gist:**

```bash
gh gist view GIST_ID
gh gist view GIST_ID --raw                      # raw content without formatting
gh gist view GIST_ID --web
```

**Edit a gist:**

```bash
gh gist edit GIST_ID
gh gist edit GIST_ID --filename myfile.py       # edit a specific file in the gist
```

**Clone a gist (as a git repository):**

```bash
gh gist clone GIST_ID
gh gist clone GIST_ID ./my-gist-dir
```

**Delete a gist:**

```bash
gh gist delete GIST_ID --yes
```

---

## SSH Keys and GPG Keys

**List SSH keys on your account:**

```bash
gh ssh-key list
```

**Add an SSH key to your GitHub account:**

```bash
gh ssh-key add ~/.ssh/id_ed25519.pub --title "My laptop"
gh ssh-key add ~/.ssh/id_ed25519.pub --title "Work MacBook" --type authentication
gh ssh-key add ~/.ssh/id_ed25519.pub --title "Signing key" --type signing
```

**Delete an SSH key:**

```bash
gh ssh-key delete KEY_ID --yes
```

**List GPG keys on your account:**

```bash
gh gpg-key list
```

**Add a GPG key:**

```bash
gh gpg-key add my-public-key.gpg
```

**Delete a GPG key:**

```bash
gh gpg-key delete KEY_ID --yes
```

---

## Labels

**List labels in a repository:**

```bash
gh label list
gh label list --search "bug"
```

**Create a label:**

```bash
gh label create "performance" --color "e4e669" --description "Performance improvements"
```

**Edit a label:**

```bash
gh label edit "bug" --name "confirmed-bug" --color "d73a4a"
```

**Delete a label:**

```bash
gh label delete "wontfix" --yes
```

**Clone labels from one repository to another:**

```bash
gh label clone SOURCE_OWNER/SOURCE_REPO
gh label clone SOURCE_OWNER/SOURCE_REPO --force  # overwrite existing labels
```

This is very useful for standardising labels across multiple repositories in an organisation.

---

## Milestones

**List milestones:**

```bash
gh api repos/OWNER/REPO/milestones --jq '.[].title'
```

Milestones are not yet a top-level `gh` subcommand - use the API. See the Scripting section for more.

---

## Secrets and Variables

**List secrets:**

```bash
gh secret list
gh secret list --env production           # environment secrets
gh secret list --org ORG_NAME             # organisation secrets
```

**Set a secret:**

```bash
gh secret set MY_SECRET
gh secret set MY_SECRET --body "secret-value"
gh secret set MY_SECRET < secret.txt
gh secret set MY_SECRET --env production  # environment secret
gh secret set MY_SECRET --org ORG_NAME --visibility all
```

**Delete a secret:**

```bash
gh secret delete MY_SECRET
gh secret delete MY_SECRET --env production
```

**List variables (non-secret configuration values):**

```bash
gh variable list
gh variable list --env production
```

**Set a variable:**

```bash
gh variable set NODE_VERSION --body "20"
gh variable set APP_ENV --body "staging" --env staging
```

**Delete a variable:**

```bash
gh variable delete NODE_VERSION
```

---

## Codespaces from the CLI

**List your codespaces:**

```bash
gh codespace list
```

**Create a codespace:**

```bash
gh codespace create
gh codespace create --repo OWNER/REPO
gh codespace create --repo OWNER/REPO --branch feature/my-branch
gh codespace create --machine basicLinux32gb   # specify machine type
```

**Open a codespace in VS Code:**

```bash
gh codespace code
gh codespace code --codespace CODESPACE_NAME
```

**SSH into a codespace:**

```bash
gh codespace ssh
gh codespace ssh --codespace CODESPACE_NAME
```

**Stop a codespace:**

```bash
gh codespace stop
gh codespace stop --codespace CODESPACE_NAME
```

**Delete a codespace:**

```bash
gh codespace delete --codespace CODESPACE_NAME
gh codespace delete --all                      # delete all codespaces
```

**Port forwarding from a codespace:**

```bash
gh codespace ports forward 3000:3000
```

---

## Aliases

Aliases create shorthand commands for long `gh` invocations you use frequently.

**Create an alias:**

```bash
gh alias set prc "pr create --fill"
gh alias set prl "pr list --author @me --state open"
gh alias set prm "pr merge --squash --delete-branch"
gh alias set co "pr checkout"
gh alias set myissues "issue list --assignee @me"
```

**Use an alias:**

```bash
gh prc                   # runs: gh pr create --fill
gh prl                   # runs: gh pr list --author @me --state open
gh co 42                 # runs: gh pr checkout 42
```

**Aliases with parameters using shell expansion:**

```bash
gh alias set 'clone!' 'gh repo clone "$1" && cd "$(basename "$1")"'
```

The `!` prefix means the alias runs as a shell command rather than a `gh` subcommand, enabling shell features like variable substitution.

**List all aliases:**

```bash
gh alias list
```

**Delete an alias:**

```bash
gh alias delete prc
```

**Where aliases are stored:**

Aliases are stored in your `gh` config file. On all platforms: `~/.config/gh/config.yml` (🍎🐧) or `%APPDATA%\GitHub CLI\config.yml` (🪟).

---

## GitHub CLI Extensions

Extensions add new subcommands to `gh`. They are community-built tools distributed as GitHub repositories.

**Install an extension:**

```bash
gh extension install github/gh-copilot        # Copilot in the terminal
gh extension install dlvhdr/gh-dash           # TUI dashboard for PRs and issues
gh extension install meiji163/gh-notify       # desktop notifications
gh extension install nicholasgasior/ghdash    # alternative dashboard
```

**List installed extensions:**

```bash
gh extension list
```

**Upgrade an extension:**

```bash
gh extension upgrade github/gh-copilot
gh extension upgrade --all                    # upgrade all extensions
```

**Remove an extension:**

```bash
gh extension remove github/gh-copilot
```

**Browse available extensions:**

```bash
gh extension browse
```

This opens an interactive TUI to search and install extensions. Browse the full catalogue at `github.com/topics/gh-extension`.

**The gh-copilot extension:**

```bash
gh extension install github/gh-copilot
gh copilot suggest "list all files modified in the last week"
gh copilot explain "git rebase -i HEAD~3"
```

`suggest` takes a natural language description and returns the shell command to do it (asking which shell you use first for correct syntax). `explain` takes any shell command and explains every part of it in plain English.

---

## Scripting with gh - JSON Output and jq

`gh` outputs machine-readable JSON with the `--json` flag. Combine with `--jq` to filter and transform the output inline using jq syntax, or pipe to `jq` separately.

**Get JSON output:**

```bash
gh pr list --json number,title,state,author
gh issue list --json number,title,labels,assignees,createdAt
gh repo list --json name,description,stargazerCount,isPrivate
```

**Filter with --jq:**

```bash
# List all open PR branch names
gh pr list --state open --json headRefName --jq '.[].headRefName'

# Get the number of open issues
gh issue list --state open --json number --jq 'length'

# Get titles of PRs that need review
gh pr list --json title,reviewDecision \
  --jq '.[] | select(.reviewDecision == null) | .title'

# Get repository names and star counts, sorted by stars
gh repo list --json name,stargazerCount \
  --jq 'sort_by(-.stargazerCount) | .[] | "\(.stargazerCount) \(.name)"'

# Get all issue labels used in the last 50 issues
gh issue list --limit 50 --json labels \
  --jq '[.[].labels[].name] | unique | sort[]'
```

**Using the GitHub API directly:**

For anything not covered by `gh`'s top-level commands, use `gh api` to call any GitHub REST or GraphQL endpoint:

```bash
# GET request
gh api repos/OWNER/REPO

# POST request
gh api repos/OWNER/REPO/issues \
  --method POST \
  --field title="Bug: login fails" \
  --field body="Steps to reproduce..."

# GraphQL query
gh api graphql -f query='
  query {
    repository(owner: "zaccessss", name: "git-unlocked") {
      issues(states: OPEN, first: 5) {
        nodes {
          number
          title
          createdAt
        }
      }
    }
  }
'

# Paginate through all results
gh api repos/OWNER/REPO/issues --paginate --jq '.[].title'
```

**Using gh in shell scripts:**

```bash
#!/bin/bash
set -e

REPO="zaccessss/git-unlocked"

# Get number of open issues
OPEN_ISSUES=$(gh issue list --repo "$REPO" --state open --json number --jq 'length')
echo "Open issues: $OPEN_ISSUES"

# Create a PR and capture its number
PR_NUMBER=$(gh pr create \
  --repo "$REPO" \
  --title "Automated: update dependencies" \
  --body "Auto-generated dependency update" \
  --json number \
  --jq '.number')
echo "Created PR #$PR_NUMBER"

# Enable auto-merge on the new PR
gh pr merge "$PR_NUMBER" --auto --squash
echo "Auto-merge enabled on PR #$PR_NUMBER"
```

**Using --template for custom output formatting:**

```bash
gh issue list --template '{{range .}}#{{.number}} {{.title}} ({{.author.login}}){{"\n"}}{{end}}'
```

---

## Practical Multi-Step Workflows

**Complete PR workflow from feature branch to merged:**

```bash
# 1. Create branch (git)
git checkout -b feature/add-search

# 2. Work and commit (git)
git add . && git commit -m "feat: add search functionality"
git push -u origin feature/add-search

# 3. Create PR with auto-filled title/body from commit
gh pr create --fill --reviewer TEAMMATE

# 4. Check CI status
gh pr checks --watch

# 5. After approval, merge with squash and delete branch
gh pr merge --squash --delete-branch
```

**Batch close stale issues:**

```bash
# Close all issues with label "stale" older than 30 days
gh issue list --label stale --state open --json number,createdAt \
  --jq '.[] | select(.createdAt < (now - 30*24*3600 | todate)) | .number' \
  | xargs -I {} gh issue close {} --reason "not planned" \
    --comment "Closing as stale. Reopen if still relevant."
```

**Release workflow:**

```bash
# Tag and create release with auto-generated notes
git tag -a v2.0.0 -m "Release v2.0.0"
git push --tags
gh release create v2.0.0 \
  --title "Version 2.0.0" \
  --generate-notes \
  dist/app-linux-amd64 dist/app-darwin-arm64 dist/app-windows-amd64.exe
```

**Clone, setup and open PR in one flow:**

```bash
gh repo fork OWNER/REPO --clone --remote
cd REPO
git checkout -b fix/typo-in-readme
sed -i 's/occured/occurred/g' README.md
git add README.md && git commit -m "fix: correct typo in README"
git push -u origin fix/typo-in-readme
gh pr create --fill --web
```

---

## Try It Yourself

**Step 1.** Install and authenticate:

```bash
gh auth login
gh auth status
```

**Step 2.** Create a practice repository:

```bash
gh repo create gh-cli-practice --public --clone --add-readme
cd gh-cli-practice
```

**Step 3.** Create an issue:

```bash
gh issue create --title "Add contributing guide" \
  --body "We need a CONTRIBUTING.md file explaining how to contribute."
gh issue list
```

**Step 4.** Create a branch, make a change and open a PR:

🪟 **Windows:**

```powershell
git checkout -b feature/contributing
echo "# Contributing" > CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "add: contributing guide"
git push -u origin feature/contributing
gh pr create --title "Add contributing guide" --body "Closes #1" --fill
```

🍎 **Mac / 🐧 Linux:**

```bash
git checkout -b feature/contributing
echo "# Contributing" > CONTRIBUTING.md
git add CONTRIBUTING.md && git commit -m "add: contributing guide"
git push -u origin feature/contributing
gh pr create --fill
```

**Step 5.** Check the PR and merge it:

```bash
gh pr list
gh pr view --web
gh pr merge --squash --delete-branch
```

**Step 6.** Try JSON scripting:

```bash
gh repo list --json name,stargazerCount --jq '.[] | "\(.name): \(.stargazerCount) stars"'
gh issue list --json number,title --jq '.[] | "#\(.number) \(.title)"'
```

---

## Common Mistakes

**Confusing `gh` and `git`.** `gh pr create` and `git push` are different tools. `gh` does not push your commits - always `git push` before `gh pr create`.

**Forgetting `--fill` on `gh pr create`.** Without `--fill`, you must type the PR title and body manually every time. `--fill` populates them from your commit messages automatically and is almost always the right default.

**Not using `gh pr checkout` for reviewing PRs.** When someone asks you to review their PR, `gh pr checkout 42` fetches their branch and checks it out locally in one command - much faster than manually adding their fork as a remote.

**Using `gh` in CI without a token.** In GitHub Actions, `gh` uses the `GITHUB_TOKEN` automatically. In other CI systems, set `GH_TOKEN` as an environment variable.

**Expecting `gh` to handle authentication for `git push`.** `gh auth login` configures `gh` but may not configure the credential helper for `git`. Run `gh auth setup-git` once to configure `git` to use `gh` as its credential helper.

```bash
gh auth setup-git
```

**Not setting up aliases for frequent commands.** Commands like `gh pr create --fill --squash --delete-branch` are typed many times a day. Alias them once and save significant time.

---

## Summary

- `gh` manages GitHub's API layer (PRs, issues, Actions, releases); `git` manages local version history - they are complementary, not alternatives
- Install with `winget` (🪟), `brew` (🍎) or `apt`/`dnf` (🐧); authenticate with `gh auth login`
- Run `gh auth setup-git` once to configure `git` to use `gh` as its credential helper
- **Repositories:** `gh repo create`, `clone`, `fork`, `view`, `edit`, `sync`, `delete`
- **Issues:** `gh issue create`, `list`, `view`, `edit`, `close`, `comment`, `pin`
- **Pull requests:** `gh pr create --fill`, `checkout`, `review`, `merge --squash --delete-branch`
- **Actions:** `gh workflow run`, `gh run list`, `gh run view --log`, `gh run watch`
- **Releases:** `gh release create --generate-notes` with attached binary assets
- **Scripting:** `--json` + `--jq` for machine-readable output; `gh api` for direct REST/GraphQL access
- **Extensions:** `gh extension install github/gh-copilot` adds Copilot to the terminal
- **Aliases:** `gh alias set prc "pr create --fill"` for your most-used commands

---

## Sources and Further Reading

- [GitHub CLI documentation](https://cli.github.com/manual/) - full command reference with all flags
- [GitHub CLI repository](https://github.com/cli/cli) - source code, issue tracker and releases
- [gh extensions catalogue](https://github.com/topics/gh-extension) - community-built extensions
- [jq manual](https://jqlang.github.io/jq/manual/) - full jq syntax reference for `--jq` filtering
- [GitHub REST API](https://docs.github.com/en/rest) - all endpoints accessible via `gh api`
- [GitHub CLI releases](https://github.com/cli/cli/releases) - download latest version

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

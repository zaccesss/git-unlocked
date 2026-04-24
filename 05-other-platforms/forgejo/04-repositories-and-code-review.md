# Repositories and Code Review in Forgejo

**Difficulty:** 🟢 Beginner | **Time:** 30 minutes

Forgejo repositories and pull requests work identically to Gitea's - same creation flow, same branch protection model, same merge strategies, same review interface. This file covers the complete workflow in one place rather than splitting repositories and code review across separate files, since the Forgejo-specific differences are minimal and what matters most is understanding the platform as a unified system.

---

## Table of Contents

1. [Creating a repository](#creating-a-repository)
2. [Cloning and pushing](#cloning-and-pushing)
3. [Mirroring and migration](#mirroring-and-migration)
4. [Branches and tags](#branches-and-tags)
5. [Pull requests](#pull-requests)
6. [Code review](#code-review)
7. [Protected branches](#protected-branches)
8. [Releases and Git LFS](#releases-and-git-lfs)
9. [Repository settings and visibility](#repository-settings-and-visibility)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## Creating a repository

### Via the web interface

1. Click **+** in the top navigation > **New Repository**
2. Set:
   - **Owner**: your account or an organisation
   - **Repository name**: appears in all URLs (`forgejo.example.com/owner/name`)
   - **Visibility**: Public, Private or Internal
   - **Initialise with**: README, `.gitignore` template, licence
   - **Default branch**: `main` is the current convention
3. Click **Create Repository**

### Via the API

```bash
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-project",
    "description": "My new project",
    "private": true,
    "auto_init": true,
    "default_branch": "main",
    "gitignores": "Python",
    "license": "MIT"
  }' \
  https://forgejo.example.com/api/v1/user/repos
```

### Pushing an existing local repository

```bash
cd my-existing-project
git init
git add .
git commit -m "Initial commit"
git remote add origin https://forgejo.example.com/username/my-project.git
git push -u origin main
```

---

## Cloning and pushing

### HTTPS clone

```bash
git clone https://forgejo.example.com/username/repository.git
# Username: your Forgejo username
# Password: your access token (not account password)
```

### SSH clone

```bash
git clone git@forgejo.example.com:username/repository.git
```

Requires your SSH public key added at Settings > SSH / GPG Keys.

### Updating remotes

After a repository transfer or rename:

```bash
git remote set-url origin https://forgejo.example.com/new-username/new-repo.git
```

### Shallow clone for large repositories

```bash
git clone --depth 1 git@forgejo.example.com:username/large-repo.git
```

Fetches only the latest commit. Useful in CI/CD for speed.

---

## Mirroring and migration

### Importing from GitHub, GitLab or Gitea

1. Click **+** > **New Migration**
2. Choose the source platform
3. Enter the repository URL and authentication credentials
4. Configure what to import: code, issues, pull requests, labels, milestones, wiki, releases
5. Click **Migrate Repository**

Forgejo can import from:

- GitHub
- GitLab
- Bitbucket
- Gitea and Forgejo
- Any generic Git URL

### Pull mirrors (one-way sync)

Create a mirror that periodically pulls from an external repository:

1. **+** > **New Migration** > select source
2. Enable **Mirror**: Forgejo polls the upstream on a schedule (default every 8 hours, minimum 10 minutes)
3. The mirrored repository is read-only in Forgejo

Use mirrors for:

- Keeping a local backup of external repositories
- Providing fast internal access to frequently used open source dependencies
- Air-gapped environments that cannot reach the public internet directly

### Push mirrors

Configure Forgejo to push to an external repository whenever you push to Forgejo:

1. Repository **Settings** > **Mirror** > **Push Mirrors**
2. Enter the remote URL and credentials
3. Set the interval

Push mirrors enable dual-hosting: code lives on Forgejo and is automatically mirrored to GitHub for visibility.

---

## Branches and tags

### Viewing branches

Repository > **Branches** shows all branches with last commit author, date and how many commits ahead or behind the default branch.

### Creating a branch

Via web: click the branch dropdown > type a new name > **Create branch from current**.

Via command line:

```bash
git checkout -b feature/my-feature
git push origin feature/my-feature
```

### Deleting branches

After merging a pull request, delete the source branch to keep the list clean:

```bash
git push origin --delete feature/my-feature
```

Or via web: Branches > trash icon next to the merged branch.

### Tags and releases

Tags mark specific commits with a version label. Releases wrap tags with a human-readable description and optional binary assets.

Create a tag:

```bash
git tag -a v1.2.0 -m "Release 1.2.0"
git push origin v1.2.0
```

Create a release via web:

1. Repository > **Releases** > **Draft a new release**
2. Select or create a tag
3. Add title, description (Markdown supported) and binary attachments
4. Click **Publish release**

---

## Pull requests

### Creating a pull request

**After pushing a branch**, Forgejo shows a banner suggesting you create a PR. Click it.

Or: Repository > **Pull Requests** > **New Pull Request** > select branches.

### Pull request form fields

**Title**: what the change does. Becomes the merge commit message for squash merges. Use present tense.

**Description**: context for reviewers. Explain what, why and how to test. Markdown is fully supported including task lists, code blocks and images.

**Reviewers**: users to notify and request review from.

**Assignees**: who is responsible for completing the PR.

**Labels**: tags for categorising (bug, enhancement, breaking change).

**Milestone**: link to a project milestone.

**Projects**: associate with a Kanban board.

**Draft**: mark as work in progress, not ready for review.

### Creating via API

```bash
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Add payment processing",
    "body": "Implements Stripe integration.\n\nCloses #45",
    "head": "feature/stripe-payments",
    "base": "main",
    "assignees": ["devuser"],
    "labels": [2]
  }' \
  https://forgejo.example.com/api/v1/repos/username/repo/pulls
```

### PR templates

Create `.forgejo/PULL_REQUEST_TEMPLATE.md` to pre-fill the PR description:

```markdown
## Summary

<!-- What does this PR do? -->

## Type of change

- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation

## Testing

- [ ] Unit tests added/updated
- [ ] Manual testing completed

## Related issues

<!-- Closes #123 -->
```

---

## Code review

### Reading the diff

The **Files Changed** tab shows the complete diff:

- Green lines: additions
- Red lines: deletions
- Context lines: unchanged surrounding code

Toggle between unified and split view using the buttons at the top right of the diff.

### Commenting on specific lines

1. Hover over any line in the diff
2. Click the **+** icon that appears on the left
3. Write your comment
4. Click **Comment** or add to a pending review

### Code suggestions

Propose specific edits that the author can apply with one click:

````markdown
```suggestion
func validateEmail(email string) bool {
    return strings.Contains(email, "@") && strings.Contains(email, ".")
}
```
````

### Submitting a review

1. **Files Changed** tab > **Review changes** button
2. Add an overall comment
3. Select verdict:
   - **Comment**: general feedback, no verdict
   - **Approve**: code is ready to merge
   - **Request changes**: changes needed before merging
4. Click **Submit review**

### Resolving conversations

When feedback has been addressed, click **Resolve Conversation** on the comment. Resolved threads collapse but remain visible. Unresolved threads can block merge if the repository has the **Check for comment resolution** requirement set in branch protection.

---

## Protected branches

Protected branches prevent direct pushes and enforce review requirements.

### Configuring branch protection

Repository **Settings** > **Protected Branches** > enter a branch name or pattern:

| Setting                     | Effect                                                |
| --------------------------- | ----------------------------------------------------- |
| **Enable push protection**  | Blocks all direct pushes; changes must go through PRs |
| **Enable merge protection** | PRs must satisfy all requirements before merging      |
| **Required approvals**      | Minimum number of approvals before merge              |
| **Required reviewers**      | Specific users or teams who must approve              |
| **Dismiss stale approvals** | Approvals reset when new commits are pushed           |
| **Required status checks**  | Named CI status checks must pass                      |
| **Restrict force push**     | Prevents `git push --force` on this branch            |

### Example: protect main

```
Branch: main

Push protection: enabled
Merge protection: enabled
Required approvals: 2
Dismiss stale approvals: enabled
Required status checks: forgejo-ci/test, forgejo-ci/build
Restrict force push: enabled
```

With this configuration: no direct pushes, PRs need 2 approvals that remain valid after any code changes and CI must pass.

### Wildcard patterns

Protect all release branches at once:

- `release/*` - protects any branch starting with `release/`
- `v*` - protects branches starting with `v` (e.g. version branches)

---

## Releases and Git LFS

### Git LFS setup

Git LFS replaces large binary files with pointer files, keeping the Git history compact.

🐧 **Linux**:

```bash
sudo apt install git-lfs
git lfs install
```

🍎 **Mac**:

```bash
brew install git-lfs
git lfs install
```

Configure tracking:

```bash
git lfs track "*.psd"
git lfs track "assets/**"
git add .gitattributes
git commit -m "Configure Git LFS tracking"
git push
```

Forgejo supports LFS natively. Enable in `app.ini`:

```ini
[server]
LFS_START_SERVER = true

[lfs]
PATH = /var/lib/forgejo/data/lfs
```

### Release binaries

Releases store binary assets separately from the Git repository. No per-project LFS quota exists in Forgejo - storage is limited only by available disk space on the server.

---

## Repository settings and visibility

### Visibility levels

**Public**: any visitor can view and clone. Appropriate for open source projects.

**Private**: only the owner and invited collaborators can access. All operations require authentication.

**Internal**: visible to all authenticated users on the instance. Not visible to unauthenticated visitors. Useful for internal company repositories where any employee should have read access without explicit invitation.

> [!NOTE]
> **Internal visibility** requires the instance administrator to have enabled it in `app.ini`. It does not exist on GitHub and maps to GitLab's "Internal" visibility level on self-managed instances.

### Important settings

**Repository Settings** > **Repository**:

- Default branch name
- Which merge strategies are available (merge commit, squash, rebase)
- Wiki enabled/disabled
- Issues enabled/disabled
- Projects enabled/disabled
- Fork policy

**Danger zone:**

- **Archive**: make the repository read-only without deleting
- **Transfer**: move to a different user or organisation (breaks all existing URLs)
- **Delete**: permanent, irreversible, no recovery

---

## Try It Yourself

**Exercise 1 - Create and push**

```bash
# Create a repo via web, then:
git clone https://forgejo.example.com/username/practice.git
cd practice
echo "# Forgejo Practice" > README.md
git add README.md
git commit -m "Initial commit"
git push
```

**Exercise 2 - Create a pull request**

```bash
git checkout -b feature/add-licence
curl -sL "https://raw.githubusercontent.com/nicowillis/open-licence/main/MIT" > LICENCE
git add LICENCE
git commit -m "Add MIT licence"
git push origin feature/add-licence
```

Go to the repository in Forgejo and create a PR from the banner that appears.

**Exercise 3 - Enable branch protection**

1. Settings > Protected Branches
2. Enter `main`
3. Enable push protection and set required approvals to 1
4. Try to push directly to main:

```bash
git checkout main
echo "test" >> README.md
git add README.md
git commit -m "Direct push attempt"
git push  # should be rejected
```

**Exercise 4 - Set up LFS**

```bash
git lfs install
git lfs track "*.png"
git add .gitattributes
git commit -m "Configure LFS"

# Add and push an image file
cp /path/to/any/image.png test.png
git add test.png
git commit -m "Add test image via LFS"
git push
```

Check the file in Forgejo - it shows as an LFS pointer.

---

## Common Mistakes

**Creating a repository with a README when pushing an existing project**

An initialised repository has a commit that diverges from your local history. Push will be rejected. Either initialise empty, or fetch and merge the remote README before pushing your local commits.

**Not committing .gitattributes after `git lfs track`**

The `.gitattributes` changes must be committed. Without committing, LFS tracking applies only locally. Other developers and CI systems do not have it configured.

**Forgetting that transfers break all remotes**

Transferring a repository changes its URL. All local `git remote` references, CI/CD pipeline configurations, webhook URLs and bookmarks break. Communicate transfers in advance and update all downstream references promptly.

**Reviewing your own PR without a second person**

With required approvals set to 1 and "allow requestors to approve own changes" on, the author can self-approve. Disable self-approval for branches where independent review is the point.

---

## Summary

Forgejo repositories and pull requests work identically to Gitea. Create repositories from the web UI or API, clone via HTTPS or SSH and use standard Git workflow for branches and commits.

Pull requests combine diff, code review, reviewer status, CI results and merge controls. Reviewers comment on specific lines, submit reviews with approve/request-changes verdicts and apply code suggestions. Protected branches enforce quality requirements before merging.

Merge strategies (merge commit, squash, rebase) are configurable per repository. PR templates in `.forgejo/PULL_REQUEST_TEMPLATE.md` prompt for consistent information.

Releases attach binary assets to Git tags. Git LFS handles large binary files. Repository visibility (public, private, internal) controls access. Transfers and deletions are permanent - update all references before transferring.

---

## Sources

- [Forgejo: Repository documentation](https://forgejo.org/docs/latest/user/repos/)
- [Forgejo: Pull requests](https://forgejo.org/docs/latest/user/pull-request/)
- [Forgejo: Protected branches](https://forgejo.org/docs/latest/user/protected-branches/)
- [Forgejo: Git LFS](https://forgejo.org/docs/latest/user/git-lfs/)
- [Forgejo: Migrations](https://forgejo.org/docs/latest/user/migration/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

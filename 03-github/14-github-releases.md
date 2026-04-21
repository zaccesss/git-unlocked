# GitHub Releases

**Difficulty:** 🟡 Intermediate | **Time:** 20 minutes

GitHub Releases are deployable packages built on top of Git tags. They give users a clear download point for your software - with release notes, binary attachments, pre-release flags and a permanent changelog. For open source projects, releases are how users know when new versions are available and where they go to download them.

---

## Table of Contents

- [What is a GitHub Release?](#what-is-a-github-release)
- [Releases and Git tags](#releases-and-git-tags)
- [Creating a release](#creating-a-release)
- [Release fields in detail](#release-fields-in-detail)
- [Auto-generated release notes](#auto-generated-release-notes)
- [Customising release note generation](#customising-release-note-generation)
- [Attaching binary assets](#attaching-binary-assets)
- [Pre-releases and draft releases](#pre-releases-and-draft-releases)
- [Immutable releases](#immutable-releases)
- [Editing and deleting releases](#editing-and-deleting-releases)
- [Creating releases with GitHub Actions](#creating-releases-with-github-actions)
- [Creating releases with GitHub CLI](#creating-releases-with-github-cli)
- [The releases page](#the-releases-page)
- [Subscribing to releases](#subscribing-to-releases)
- [Release API](#release-api)
- [Semantic versioning reminder](#semantic-versioning-reminder)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is a GitHub Release?

A GitHub Release is a way to package and distribute a version of your software. It combines:

- A **Git tag** marking a specific commit as a version
- A **title** for the release
- **Release notes** describing what changed
- Optional **binary files** - compiled executables, installers, zip archives
- Automatic **source code archives** (zip and tar.gz of the repository at that tag)

Releases appear prominently on your repository's homepage in the right sidebar. Users can subscribe to release notifications. The Releases page provides a clean changelog history.

---

## Releases and Git Tags

Every GitHub Release is built on a Git tag. The tag marks the exact commit that was released.

**Tags covered in depth:** Full Git tag coverage (annotated vs lightweight, pushing tags, signing tags) is in [git/16-tags-and-releases.md](../02-git/16-tags-and-releases.md).

**In the GitHub context:**

When creating a release, you either:
- **Select an existing tag** that you already pushed to the repository
- **Create a new tag** as part of the release creation process (GitHub creates it when you publish)

GitHub strongly recommends **annotated tags** for releases. Annotated tags store tagger name, date, message and can be signed - they are full objects in the Git database. Lightweight tags are just pointers.

**The relationship:**

```
Git tag v2.1.0 → points to commit abc123
        ↓
GitHub Release "Version 2.1.0"
  - Release notes
  - Binary attachments
  - Source code archives
  - Pre-release flag
```

The tag is the version marker. The release is the GitHub wrapper around it with extra information and files.

---

## Creating a Release

**From GitHub.com:**

1. Go to your repository
2. Click **Releases** in the right sidebar (or the **Releases** link at the top of the Code tab if no releases exist yet)
3. Click **Create a new release** or **Draft a new release**

**Step 1 - Choose a tag:**

In the **Choose a tag** dropdown:
- Type a new tag name (e.g., `v2.1.0`) and click **Create new tag: v2.1.0 on publish**
- Or select an existing tag from the list

**Step 2 - Choose the target:**

Select which branch or commit the tag should point to. Usually `main` for a production release.

**Step 3 - Set the release title:**

Convention: `Version 2.1.0` or just `v2.1.0`. Be consistent across all your releases.

**Step 4 - Write release notes:**

Describe what changed. See [Auto-generated release notes](#auto-generated-release-notes) for GitHub's automatic generation feature.

**Step 5 - Attach files (optional):**

Drag and drop binary files to the attachment area.

**Step 6 - Set flags:**

- **Set as pre-release** if this is an alpha, beta or release candidate
- **Set as latest release** (checked by default unless it is a pre-release)

**Step 7 - Publish or save as draft:**

- **Publish release** - makes it live immediately
- **Save draft** - saves without publishing

---

## Release Fields in Detail

### Tag

The Git tag this release is based on. Follow **semantic versioning** (see the [Semantic versioning reminder](#semantic-versioning-reminder) section). Always prefix with `v`: `v1.0.0`, `v2.3.1`, `v3.0.0-beta.1`.

### Target

The branch or commit the tag should be created on. For normal releases, this is `main`. For backport releases to an older version, this would be a maintenance branch like `release/v1.x`.

### Previous tag

Used for auto-generating release notes. GitHub compares commits between the previous tag and the new tag to generate the changelog.

### Release title

The human-readable name for this release. Common conventions:
- `v2.1.0`
- `Version 2.1.0`
- `Version 2.1.0 - Dark Mode`
- `2.1.0 - The Performance Release`

### Release notes body

Full Markdown. A good release description includes:

```markdown
## What's new
- Added dark mode support (#42)
- Improved dashboard loading speed by 60% (#89)

## Bug fixes
- Fixed login redirect on mobile Safari (#91)
- Fixed tooltip positioning on Windows (#95)

## Breaking changes
- The `oldFunction()` API has been removed. Use `newFunction()` instead.

## Upgrade guide
Run `npm install` to get the latest dependencies.

## Full changelog
https://github.com/YOUR_USERNAME/YOUR_REPO/compare/v2.0.0...v2.1.0
```

---

## Auto-generated Release Notes

GitHub can automatically generate release notes from the pull requests merged since the last release.

**How to use:**

Click **Generate release notes** in the release form. GitHub creates a list of merged pull requests with their titles and authors, grouped by label.

**What auto-generation produces:**

```markdown
## What's Changed
* feat: add dark mode by @contributor-a in #42
* fix: login redirect on mobile by @contributor-b in #91
* docs: update README by @contributor-c in #100

## New Contributors
* @contributor-a made their first contribution in #42

**Full Changelog**: https://github.com/owner/repo/compare/v2.0.0...v2.1.0
```

**Quality depends on PR titles.** Well-named PRs produce useful release notes. PRs named `fix stuff` or `update` produce unhelpful notes. This is another reason to use clear, consistent PR titles.

---

## Customising Release Note Generation

Create `.github/release.yml` to configure how auto-generated notes are grouped.

**Example configuration:**

```yaml
changelog:
  exclude:
    labels:
      - ignore-for-release     # PRs with this label are excluded
  categories:
    - title: "🚀 New features"
      labels:
        - enhancement
        - feature
    - title: "🐛 Bug fixes"
      labels:
        - bug
        - fix
    - title: "📖 Documentation"
      labels:
        - documentation
        - docs
    - title: "🔧 Maintenance"
      labels:
        - chore
        - maintenance
    - title: "Other changes"
      labels:
        - "*"           # catch-all for anything not matched above
```

With this config, PRs are grouped by their labels under the defined headings. Unlabelled PRs fall into "Other changes".

---

## Attaching Binary Assets

Releases can include binary files alongside the automatic source code archives. Users download these directly from the release page.

**Common examples:**

- Compiled executables: `myapp-windows-x64.exe`, `myapp-linux-amd64`, `myapp-macos-arm64`
- Installers: `.dmg`, `.msi`, `.deb`, `.rpm`
- Compiled archives: `myapp-v2.1.0.zip`, `myapp-v2.1.0.tar.gz`
- Checksums: `checksums.txt` with SHA256 hashes of all files

**Attaching files:**

In the release form, drag and drop files into the **Attach binaries** area, or click to browse.

**Asset naming conventions:**

Use clear, consistent names that include the version number and platform:

```
myapp-2.1.0-windows-amd64.exe
myapp-2.1.0-linux-amd64.tar.gz
myapp-2.1.0-darwin-arm64.tar.gz
myapp-2.1.0-darwin-amd64.tar.gz
checksums-2.1.0.txt
```

**Automatic source archives:**

GitHub automatically creates two source archives for every release:
- `Source code (zip)` - full repository at that tag as a zip
- `Source code (tar.gz)` - full repository as a tar.gz

These appear automatically and cannot be removed.

---

## Pre-releases and Draft Releases

### Pre-releases

Check **Set as pre-release** for alpha, beta and release candidate versions. Pre-releases:

- Are shown on the releases page with a "Pre-release" badge
- Are not set as the "Latest release"
- Are excluded from `latest` references in download links
- Are visible to subscribers who watch all releases

Pre-release versioning by convention:

```
v3.0.0-alpha.1
v3.0.0-beta.1
v3.0.0-beta.2
v3.0.0-rc.1
v3.0.0           ← stable release
```

### Draft releases

Draft releases are saved but not published. They are invisible to users and do not trigger notifications. Use drafts to:

- Prepare release notes in advance
- Build assets and attach them before going live
- Get internal review of release notes before publishing

Drafts are only visible to repository collaborators with write access.

**Publishing a draft:**

Open the draft → click **Edit** → click **Publish release**.

---

## Immutable Releases

Repositories can enable **immutable releases** to prevent modification of release assets after publishing.

**Enable:** Settings → General → Check **Enable immutable releases**.

With immutable releases enabled, once a release is published its assets cannot be replaced or deleted. The release title and notes can still be edited. This provides stronger guarantees for users who verify checksums.

> [!TIP]
> When using immutable releases, create releases as drafts first, attach all assets, verify checksums and then publish. Once published, the assets are permanently fixed.

---

## Editing and Deleting Releases

**Edit a release:**

Go to Releases → click the pencil icon on the release → modify title, notes or assets → click **Update release**.

Editing a release does not change the underlying Git tag.

**Delete a release:**

Go to Releases → click the pencil icon → scroll to the bottom → **Delete this release**.

Deleting a release removes the GitHub release page and its assets. The underlying Git tag is **not** deleted. Users who already downloaded assets are not affected, but download links will break.

**Delete a tag separately:**

After deleting a release, delete its tag if needed:

```bash
git tag -d v2.1.0
git push origin --delete v2.1.0
```

---

## Creating Releases with GitHub Actions

Automating releases via Actions ensures consistency - every release is created the same way, with the same process.

**Trigger a release on tag push:**

```yaml
name: Create Release

on:
  push:
    tags:
      - 'v*'       # triggered by any tag starting with v

permissions:
  contents: write   # needed to create releases

jobs:
  release:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Build the project
        run: |
          npm ci
          npm run build

      - name: Create release archive
        run: |
          tar -czf myapp-${{ github.ref_name }}-linux.tar.gz dist/

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
          files: |
            myapp-${{ github.ref_name }}-linux.tar.gz
```

**Using GitHub's official release action:**

```yaml
- name: Create Release
  uses: actions/create-release@v1
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  with:
    tag_name: ${{ github.ref_name }}
    release_name: Release ${{ github.ref_name }}
    body: |
      Changes in this release:
      - Bug fixes and improvements
    draft: false
    prerelease: false
```

**Full automated workflow: build → test → release:**

A common pattern runs tests on every push, but creates a release only when a version tag is pushed:

```yaml
on:
  push:
    branches: [main]
    tags: ['v*']

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test

  release:
    needs: test
    if: startsWith(github.ref, 'refs/tags/v')
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run build
      - uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
```

---

## Creating Releases with GitHub CLI

```bash
# Create a release from an existing tag
gh release create v2.1.0 --title "Version 2.1.0" --notes "Bug fixes and improvements"

# Create with auto-generated notes
gh release create v2.1.0 --title "Version 2.1.0" --generate-notes

# Create from notes file
gh release create v2.1.0 --title "Version 2.1.0" --notes-file CHANGELOG.md

# Create a pre-release
gh release create v3.0.0-beta.1 --prerelease --generate-notes

# Create a draft
gh release create v2.1.0 --draft --title "Version 2.1.0"

# Create with binary assets
gh release create v2.1.0 dist/myapp-linux dist/myapp-macos dist/myapp-windows.exe

# Upload assets to an existing release
gh release upload v2.1.0 checksums.txt

# View a release
gh release view v2.1.0

# List all releases
gh release list

# Delete a release
gh release delete v2.1.0
```

---

## The Releases Page

The releases page at `github.com/YOUR_USERNAME/YOUR_REPO/releases` shows all releases in reverse chronological order. Each entry shows:

- Version tag
- Release title
- "Latest release" badge (on the most recent stable release)
- "Pre-release" badge (on pre-releases)
- Release date
- Release notes (truncated with a "more" link)
- Assets (source code archives always included plus any attached binaries)
- Asset download counts

**Linking to the latest release:**

```
https://github.com/YOUR_USERNAME/YOUR_REPO/releases/latest
```

This always redirects to the most recent non-pre-release release. Use this in documentation and README files.

**Linking to a specific release:**

```
https://github.com/YOUR_USERNAME/YOUR_REPO/releases/tag/v2.1.0
```

**Direct asset download links:**

```
https://github.com/YOUR_USERNAME/YOUR_REPO/releases/download/v2.1.0/myapp-linux.tar.gz
```

These links work in scripts, install instructions and package manager configurations.

---

## Subscribing to Releases

Users can choose to receive notifications for new releases without being subscribed to all repository activity.

**How to subscribe:**

On the repository homepage → **Watch** dropdown → **Custom** → check **Releases** only.

This sends a notification only when a new release is published - no notifications for issues, commits or pull requests.

Releases page also has an **RSS feed** at:

```
https://github.com/YOUR_USERNAME/YOUR_REPO/releases.atom
```

---

## Release API

The GitHub REST API provides full release management programmatically.

**List releases:**

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" \
  https://api.github.com/repos/OWNER/REPO/releases
```

**Get the latest release:**

```bash
curl https://api.github.com/repos/OWNER/REPO/releases/latest
```

**Create a release:**

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  https://api.github.com/repos/OWNER/REPO/releases \
  -d '{
    "tag_name": "v2.1.0",
    "name": "Version 2.1.0",
    "body": "Release notes here",
    "draft": false,
    "prerelease": false,
    "generate_release_notes": true
  }'
```

---

## Semantic Versioning Reminder

GitHub Releases work best with **semantic versioning** (`MAJOR.MINOR.PATCH`). Full coverage is in [git/16-tags-and-releases.md](../02-git/16-tags-and-releases.md). Quick reference:

| Change type | Version bump | Example |
|---|---|---|
| Breaking changes | MAJOR | `1.5.2` → `2.0.0` |
| New backwards-compatible features | MINOR | `1.5.2` → `1.6.0` |
| Backwards-compatible bug fixes | PATCH | `1.5.2` → `1.5.3` |

Always prefix release tags with `v`: `v1.0.0`, `v2.3.1`.

---

## Try It Yourself

**Step 1.** Make sure you have a repository with at least one commit.

**Step 2.** Create and push a tag:

```bash
git tag -a v0.1.0 -m "First release"
git push origin v0.1.0
```

**Step 3.** Create a release on GitHub:

1. Go to your repository → Releases → **Create a new release**
2. Click **Choose a tag** → select `v0.1.0`
3. Title: `Version 0.1.0`
4. Click **Generate release notes**
5. Review the auto-generated notes
6. Click **Publish release**

**Step 4.** View your release:

Go to `github.com/YOUR_USERNAME/YOUR_REPO/releases`. Your release appears with source code archives available for download.

**Step 5.** Set up auto-release notes config:

Create `.github/release.yml`:

```yaml
changelog:
  categories:
    - title: "New features"
      labels: [enhancement]
    - title: "Bug fixes"
      labels: [bug]
    - title: "Other changes"
      labels: ["*"]
```

Commit and push. Future releases will use this grouping.

**Step 6.** Create a pre-release:

```bash
git tag -a v0.2.0-beta.1 -m "Beta release"
git push origin v0.2.0-beta.1
```

Create the release on GitHub, check **Set as pre-release** and publish.

---

## Common Mistakes

**Not using semantic versioning.**

Tags like `release-jan`, `v2`, `final` or `1.0` make releases hard to understand and compare. Use `MAJOR.MINOR.PATCH` consistently.

**Creating releases without attached binaries for compiled projects.**

For compiled languages (Go, Rust, C, Swift), users cannot easily build from source. Always attach pre-compiled binaries for all supported platforms. Without them, your releases provide little practical value beyond a tag.

**Not using auto-generated release notes.**

Writing release notes manually for every PR is time-consuming. Click **Generate release notes** - the quality is good if your PR titles are descriptive. Use `.github/release.yml` to customise the grouping.

**Forgetting checksums.**

For binary distributions, include a `checksums.txt` file with SHA256 hashes of all release assets. This lets users verify their downloads were not corrupted or tampered with.

**Not deleting the release branch after tagging.**

Tags and release branches serve different purposes. After releasing `v2.1.0`, the release tag points to the exact commit. There is no need to keep a `release/v2.1.0` branch unless you actively maintain that version.

**Using lightweight tags instead of annotated tags.**

Lightweight tags have no metadata. Annotated tags store who created them, when and why. Use `git tag -a v2.1.0 -m "Release"` always.

---

## Summary

- A GitHub Release packages a Git tag with release notes, binary assets and metadata
- Every release is based on a Git tag - create a new tag during release creation or select an existing one
- **Generate release notes** automatically creates a changelog from merged PR titles since the last release
- Customise note generation with `.github/release.yml` to group PRs by label
- Attach compiled binaries, installers and checksums as release assets
- **Pre-releases** are tagged alpha/beta/RC versions; **draft releases** are unpublished work in progress
- **Immutable releases** lock assets permanently after publishing
- Automate releases with GitHub Actions triggered by tag pushes using `softprops/action-gh-release` or `actions/create-release`
- GitHub CLI: `gh release create`, `gh release upload`, `gh release list`
- The latest release permalink: `github.com/YOUR_USERNAME/YOUR_REPO/releases/latest`
- Always use semantic versioning (`MAJOR.MINOR.PATCH`) with a `v` prefix

---

## Sources and Further Reading

- [About releases - GitHub Docs](https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases) - official reference
- [Managing releases - GitHub Docs](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository) - step-by-step guide
- [Automatically generated release notes - GitHub Docs](https://docs.github.com/en/repositories/releasing-projects-on-github/automatically-generated-release-notes) - auto-notes configuration
- [softprops/action-gh-release](https://github.com/softprops/action-gh-release) - the most popular Actions release action
- [Semantic versioning specification](https://semver.org) - the complete SemVer standard

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

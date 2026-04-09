# Codeberg Pages

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Codeberg Pages provides free static site hosting directly from a Codeberg repository. Deploy a documentation site, personal portfolio, project landing page or any static web application - no separate hosting account required. HTTPS is provided automatically via Let's Encrypt and custom domains are supported.

Codeberg Pages differs from GitHub Pages in one significant way: deployment comes from a dedicated `pages` branch rather than from a `gh-pages` branch or a docs folder on the default branch. This file covers the complete setup, custom domains, static site generator integration and the limitations specific to Codeberg Pages.

---

## Table of Contents

1. [How Codeberg Pages works](#how-codeberg-pages-works)
2. [Basic setup: the pages branch](#basic-setup-the-pages-branch)
3. [URL structure](#url-structure)
4. [Deploying from CI with Woodpecker](#deploying-from-ci-with-woodpecker)
5. [Custom domains](#custom-domains)
6. [Static site generators](#static-site-generators)
7. [Single Page Applications (SPA)](#single-page-applications-spa)
8. [Limitations and considerations](#limitations-and-considerations)
9. [Try It Yourself](#try-it-yourself)
10. [Common Mistakes](#common-mistakes)
11. [Summary](#summary)
12. [Sources](#sources)

---

## How Codeberg Pages works

Codeberg Pages serves the contents of a special `pages` branch from your repository. When someone visits your Pages URL, Codeberg Pages serves the files from that branch as a static website.

Unlike GitHub Pages, where you can deploy from any branch or a `/docs` folder:
- Codeberg Pages only reads from the `pages` branch (the branch must be named exactly `pages`)
- There is no option to deploy from the default branch or a subdirectory
- Deployment is automatic - whenever you push to the `pages` branch, the site updates within a few seconds

No configuration files, no deployment workflows, no build servers needed for basic usage. Push HTML to the `pages` branch, your site is live.

---

## Basic setup: the pages branch

### Option 1 - Static files directly in the repository

If your project already produces static files (or is a pure HTML/CSS/JS project with no build step):

```bash
# Create and switch to the pages branch
git checkout --orphan pages

# Remove any tracked files from the working tree
git rm -rf .

# Add your static files
echo "<h1>Hello from Codeberg Pages!</h1>" > index.html

# Commit and push
git add index.html
git commit -m "Initial Pages deployment"
git push origin pages
```

Your site is live at `https://username.codeberg.page/repo-name`.

### Option 2 - Dedicated pages repository

For a personal site or portfolio that lives at `username.codeberg.page`:

1. Create a repository named **pages** (exactly) under your username
2. Add your static files to the `pages` branch of that repository

The URL is simply `https://username.codeberg.page`.

For an organisation:
1. Create a repository named **pages** under the organisation
2. Add files to the `pages` branch

URL: `https://org-name.codeberg.page`

### Verifying deployment

After pushing to the `pages` branch:
1. Wait 30-60 seconds
2. Visit your Pages URL
3. The site should be live

If the site does not appear, check:
- The branch is named exactly `pages` (lowercase, no typos)
- There is an `index.html` at the root of the branch
- The repository is public (Codeberg Pages requires public repositories for free accounts)

---

## URL structure

| Repository type | URL pattern |
|---|---|
| User repository named `pages` | `https://username.codeberg.page` |
| Organisation repository named `pages` | `https://org-name.codeberg.page` |
| Any other user repository | `https://username.codeberg.page/repo-name` |
| Any other org repository | `https://org-name.codeberg.page/repo-name` |

### Examples

- User `zaccessss`, repository `zaccessss/pages` → `https://zaccessss.codeberg.page`
- User `zaccessss`, repository `zaccessss/my-project` → `https://zaccessss.codeberg.page/my-project`
- Org `myteam`, repository `myteam/docs` → `https://myteam.codeberg.page/docs`

---

## Deploying from CI with Woodpecker

For projects that require a build step (static site generators, compiled documentation, bundled JavaScript), use Woodpecker CI to build and deploy to the `pages` branch automatically.

### Setup

1. Generate a Codeberg access token with `repository:write` scope
2. Add it as a secret in Woodpecker CI: `CODEBERG_TOKEN`
3. Add a deploy step to `.woodpecker.yml`

### Woodpecker deploy pipeline

```yaml
steps:
  - name: build
    image: node:20-alpine
    commands:
      - npm ci
      - npm run build        # produces output in ./dist/

  - name: deploy-pages
    image: alpine/git
    environment:
      CODEBERG_TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - git config --global user.email "ci@codeberg.org"
      - git config --global user.name "Codeberg CI"
      # Clone the pages branch (or create it if new)
      - |
        if git ls-remote --exit-code https://codeberg.org/username/repo.git pages; then
          git clone --branch pages \
            https://username:${CODEBERG_TOKEN}@codeberg.org/username/repo.git pages-deploy
        else
          git clone https://username:${CODEBERG_TOKEN}@codeberg.org/username/repo.git pages-deploy
          cd pages-deploy
          git checkout --orphan pages
          git rm -rf .
          cd ..
        fi
      # Copy build output
      - cp -r dist/* pages-deploy/
      # Commit and push
      - cd pages-deploy
      - git add -A
      - git diff --staged --quiet || git commit -m "Deploy ${CI_COMMIT_SHA:0:8}"
      - git push origin pages
    when:
      branch: main
      event: push
```

### Simplified deploy using a plugin

```yaml
steps:
  - name: build
    image: node:20-alpine
    commands:
      - npm ci && npm run build

  - name: deploy
    image: woodpeckerci/plugin-git-action
    settings:
      action: push
      remote: https://codeberg.org/username/repo.git
      branch: pages
      path: dist/
      commit_message: "Deploy from CI ${CI_COMMIT_SHA:0:8}"
      author_email: ci@noreply.codeberg.org
      author_name: Codeberg CI
      netrc_machine: codeberg.org
      netrc_username: username
      netrc_password:
        from_secret: CODEBERG_TOKEN
    when:
      branch: main
      event: push
```

---

## Custom domains

You can serve your Codeberg Pages site from your own domain (e.g. `docs.yourproject.org`).

### Step 1 - Add a `.domains` file

Create a file named `.domains` (note the leading dot) in the root of your `pages` branch:

```
docs.yourproject.org
```

For multiple domains:
```
docs.yourproject.org
www.yourproject.org
```

Commit and push this file to the `pages` branch.

### Step 2 - Configure your DNS

Add a CNAME record at your DNS provider pointing to Codeberg Pages:

| Type | Name | Value |
|---|---|---|
| CNAME | `docs` | `username.codeberg.page` |

Or for a root/apex domain, use an ALIAS or ANAME record:

| Type | Name | Value |
|---|---|---|
| ALIAS | `@` | `username.codeberg.page` |

DNS propagation typically takes a few minutes to a few hours.

### Step 3 - HTTPS provisioning

Codeberg Pages automatically provisions a Let's Encrypt certificate for your custom domain once:
- The `.domains` file lists the domain
- The DNS CNAME/ALIAS record points to `username.codeberg.page`
- The DNS has propagated

Certificate provisioning may take up to 24 hours for the first time. After that, renewal is automatic.

> [!NOTE]
> Custom domains require the DNS to point to Codeberg Pages before the certificate is issued. If the DNS is not configured, the HTTPS provisioning will fail and your site will not be accessible on the custom domain.

---

## Static site generators

Any static site generator that outputs HTML files works with Codeberg Pages. Set up your CI pipeline to build and push to the `pages` branch.

### Hugo

```yaml
steps:
  - name: build
    image: klakegg/hugo:ext-alpine
    commands:
      - hugo --minify

  - name: deploy
    image: alpine/git
    environment:
      TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - cd public/    # Hugo outputs to public/ by default
      - git init
      - git remote add origin https://username:${TOKEN}@codeberg.org/username/repo.git
      - git checkout -b pages
      - git add -A
      - git commit -m "Deploy ${CI_COMMIT_SHA:0:8}"
      - git push --force origin pages
    when:
      branch: main
      event: push
```

Hugo configuration (`config.toml`):
```toml
baseURL = "https://username.codeberg.page/repo-name/"
```

Or for a custom domain:
```toml
baseURL = "https://docs.yourproject.org/"
```

### MkDocs

```yaml
steps:
  - name: build
    image: python:3.12-slim
    commands:
      - pip install mkdocs mkdocs-material
      - mkdocs build    # outputs to site/

  - name: deploy
    image: alpine/git
    environment:
      TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - cd site/
      - git init
      - git remote add origin https://username:${TOKEN}@codeberg.org/username/repo.git
      - git checkout -b pages
      - git add -A
      - git commit -m "Deploy docs ${CI_COMMIT_SHA:0:8}"
      - git push --force origin pages
    when:
      branch: main
      event: push
```

`mkdocs.yml`:
```yaml
site_url: https://username.codeberg.page/repo-name/
theme:
  name: material
```

### Eleventy (11ty)

```yaml
steps:
  - name: build
    image: node:20-alpine
    commands:
      - npm ci
      - npx @11ty/eleventy    # outputs to _site/

  - name: deploy
    image: alpine/git
    environment:
      TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - cd _site/
      - git init
      - git remote add origin https://username:${TOKEN}@codeberg.org/username/repo.git
      - git checkout -b pages
      - git add -A
      - git commit -m "Deploy ${CI_COMMIT_SHA:0:8}"
      - git push --force origin pages
    when:
      branch: main
      event: push
```

---

## Single Page Applications (SPA)

For SPA frameworks (React, Vue, Svelte) that use client-side routing, all routes need to be handled by `index.html`. Since Codeberg Pages serves static files, a 404 for any unknown path returns the standard 404 page rather than your app's `index.html`.

### The 404 trick

Create a `404.html` file that is identical to or redirects to `index.html`. When a user navigates directly to a deep path (e.g. `/dashboard`), they get `404.html` which loads your SPA, which then handles routing on the client side.

In your build pipeline:

```bash
cp dist/index.html dist/404.html
```

This works for simple SPAs. For more sophisticated routing requirements, consider server-side rendering (SSR) or a static generation approach where each route generates its own HTML file.

---

## Limitations and considerations

### Public repositories only

Codeberg Pages only serves sites from public repositories on the free tier. Private repository Pages requires financial support of Codeberg.

### The `pages` branch naming is strict

The branch must be named exactly `pages` - lowercase, no variants. `gh-pages`, `docs`, `website` or any other name will not work.

### No server-side code

Codeberg Pages serves static files only. No PHP, Node.js, Python or other server-side runtime. For dynamic functionality, use client-side JavaScript calling external APIs.

### No .htaccess or server configuration

Unlike Apache or Nginx deployments, you cannot configure redirects, headers or URL rewriting through configuration files. Custom HTTP headers and redirect rules are not supported. The `.domains` file is the only server-side configuration available.

### Storage limits

Pages storage is subject to Codeberg's fair use policy. Very large sites (many GiB of assets) are discouraged. For large binary assets, use external CDN hosting.

### Build times and CI limits

If you use Woodpecker CI for building, Codeberg's shared CI infrastructure has resource limits. For sites with long build times (generating thousands of pages), consider using self-hosted Woodpecker agents or reducing build scope.

---

## Try It Yourself

**Exercise 1 - Hello world site**

```bash
# Clone your hello-codeberg repository
git clone git@codeberg.org:your-username/hello-codeberg.git
cd hello-codeberg

# Create the pages branch
git checkout --orphan pages
git rm -rf .

# Create a simple site
cat > index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hello Codeberg Pages</title>
  <style>
    body { font-family: sans-serif; max-width: 800px; margin: 2rem auto; padding: 0 1rem; }
    h1 { color: #2185d0; }
  </style>
</head>
<body>
  <h1>Hello from Codeberg Pages!</h1>
  <p>This site is hosted on <a href="https://codeberg.org">Codeberg</a>.</p>
</body>
</html>
EOF

git add index.html
git commit -m "Initial Pages site"
git push origin pages
```

Visit `https://your-username.codeberg.page/hello-codeberg` after 30-60 seconds.

**Exercise 2 - Add automatic deployment with CI**

Return to the main branch and set up Woodpecker deployment:

```bash
git checkout main
```

Create `.woodpecker.yml`:

```yaml
steps:
  - name: deploy-pages
    image: alpine/git
    environment:
      TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - git config --global user.email "ci@noreply.codeberg.org"
      - git config --global user.name "CI Deploy"
      - git clone --branch pages
          https://your-username:${TOKEN}@codeberg.org/your-username/hello-codeberg.git
          deploy
      - echo "<p>Last deployed at $(date)</p>" >> deploy/index.html
      - cd deploy
      - git add -A
      - git commit -m "Update timestamp"
      - git push origin pages
    when:
      branch: main
      event: push
```

Add `CODEBERG_TOKEN` as a secret in Woodpecker CI, push and watch the deployment update.

---

## Common Mistakes

**Naming the branch anything other than `pages`**

The branch must be named exactly `pages`. `gh-pages`, `docs`, `website` or any other name will not trigger Codeberg Pages. This is the most common setup mistake.

**Pushing to the wrong repository**

If you are setting up a user Pages site (at `username.codeberg.page`), the repository must be named `pages`. Any other repository name creates a sub-path site (`username.codeberg.page/repo-name`).

**Missing index.html at the branch root**

If there is no `index.html` at the root of the `pages` branch, visitors get a 404. Static site generators output to a subdirectory - make sure you are pushing the contents of the output directory, not the directory itself.

**Broken base URL in static site generators**

Hugo, MkDocs and others need the correct `baseURL` set. If the base URL does not match the actual Pages URL (including the repository name sub-path), internal links, CSS and JavaScript will load from wrong paths. Always set `baseURL` to your exact Pages URL.

**Force-pushing over someone else's commits in a shared repository**

CI pipelines commonly use `git push --force origin pages` to deploy. In a shared repository where multiple people might push to the `pages` branch, force-push can overwrite others' changes. Use `git push` without force if the `pages` branch has human-managed content alongside generated content.

---

## Summary

Codeberg Pages serves static files from the `pages` branch of any public Codeberg repository. Basic deployment is manual: create the `pages` branch, add `index.html`, push. The site is live at `https://username.codeberg.page/repo-name` within seconds.

For projects with a build step, use Woodpecker CI to build and push the output to the `pages` branch on each merge to main. Any static site generator (Hugo, MkDocs, Eleventy, Astro, plain HTML) works.

Custom domains are configured with a `.domains` file in the `pages` branch root. Codeberg automatically provisions Let's Encrypt HTTPS certificates once DNS is configured correctly.

Key limitations: public repositories only for free tier, the branch must be named exactly `pages`, no server-side code or configuration files, and fair use storage limits apply.

---

## Sources

- [Codeberg Pages: Documentation](https://codeberg.org/Codeberg/Community/wiki/Codeberg-Pages)
- [Codeberg Pages: Custom domains](https://codeberg.org/Codeberg/Community/wiki/Codeberg-Pages#custom-domains)
- [Woodpecker CI: Documentation](https://woodpecker-ci.org/docs/intro)
- [Hugo: Documentation](https://gohugo.io/documentation/)
- [MkDocs: Documentation](https://www.mkdocs.org)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

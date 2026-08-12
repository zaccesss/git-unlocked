# GitHub Pages

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

GitHub Pages is a free static site hosting service built directly into GitHub. It serves HTML, CSS and JavaScript files from a repository as a live website - with no server to configure, no hosting bills and no deployment pipeline to set up. It is one of the fastest ways to get a project, portfolio or documentation site online.

---

## Table of Contents

- [What is GitHub Pages?](#what-is-github-pages)
- [Types of GitHub Pages sites](#types-of-github-pages-sites)
- [Setting up Pages from a branch](#setting-up-pages-from-a-branch)
- [Setting up Pages with GitHub Actions](#setting-up-pages-with-github-actions)
- [The gh-pages branch](#the-gh-pages-branch)
- [Jekyll and static site generators](#jekyll-and-static-site-generators)
- [Custom domains](#custom-domains)
- [Enforcing HTTPS](#enforcing-https)
- [Visibility and access control](#visibility-and-access-control)
- [What GitHub Pages can and cannot do](#what-github-pages-can-and-cannot-do)
- [Pages limits](#pages-limits)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitHub Pages?

GitHub Pages takes files from a GitHub repository and serves them as a website. You push HTML, CSS and JavaScript - GitHub serves them publicly at a `github.io` URL or your own custom domain.

It is ideal for: personal portfolios, project documentation, open source project landing pages, course materials, blogs (with Jekyll) and any static content that does not require a backend or database.

GitHub Pages is free for public repositories on all plans. Private repository Pages requires Pro, Team or Enterprise.

---

## Types of GitHub Pages Sites

### User or organisation site

One special site per GitHub account, served at `YOUR_USERNAME.github.io`:

- Repository must be named **exactly** `YOUR_USERNAME.github.io` (replace with your actual username)
- Files are served from the `main` branch by default
- URL: `https://YOUR_USERNAME.github.io`

Example: a user with the username `YOUR_USERNAME` creates a repository called `YOUR_USERNAME.github.io`. Visiting `https://YOUR_USERNAME.github.io` serves the contents of that repository.

### Project site

One site per repository, served at `YOUR_USERNAME.github.io/REPO_NAME`:

- Any repository can have a Pages site
- Configure which branch and folder to serve from (see next section)
- URL: `https://YOUR_USERNAME.github.io/REPO_NAME`

Most repositories that have a Pages site are project sites.

---

## Setting up Pages from a Branch

The simplest setup - GitHub serves files directly from a branch in your repository.

**Step 1.** Go to your repository on GitHub.

**Step 2.** Click **Settings** → **Pages** (in the left sidebar under "Code and automation").

**Step 3.** Under **Build and deployment**, set **Source** to **Deploy from a branch**.

**Step 4.** Choose the **branch** to deploy from:

- `main` - deploy from your main branch
- `gh-pages` - deploy from a dedicated Pages branch (common for generated sites)
- Any other branch

**Step 5.** Choose the **folder**:

- `/ (root)` - serve from the root of the branch
- `/docs` - serve from a `docs/` folder in the root

**Step 6.** Click **Save**.

GitHub builds and deploys the site. After a minute or two, a link to your live site appears at the top of the Pages settings page.

> [!NOTE]
> When you push new commits to the configured branch, GitHub automatically redeploys the site. There is usually a delay of 1-2 minutes between pushing and the live site updating.

---

## Setting up Pages with GitHub Actions

For more control - using a static site generator, custom build steps or a specific build tool - deploy Pages via GitHub Actions instead of directly from a branch.

**Step 1.** In Settings → Pages, set **Source** to **GitHub Actions**.

**Step 2.** GitHub suggests workflow templates. Choose one for your framework (Next.js, Gatsby, Hugo, Astro, Eleventy, Jekyll, Vue, React, plain HTML and more).

**Step 3.** The workflow builds your site and uses the official `actions/upload-pages-artifact` and `actions/deploy-pages` actions to deploy it.

**Example workflow for a plain HTML site:**

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/configure-pages@v5

      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public # the folder containing your built site

      - id: deployment
        uses: actions/deploy-pages@v4
```

**Example for a Jekyll site:**

```yaml
name: Deploy Jekyll Site

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: "3.2"
          bundler-cache: true
      - run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production
      - uses: actions/upload-pages-artifact@v3

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

**Advantages of Actions deployment over branch deployment:**

- Use any static site generator (Hugo, Astro, Eleventy, Docusaurus etc.)
- Run linting or validation before deploying
- Deploy from a monorepo where the site is in a subdirectory
- Deploy different branches to different environments
- Full control over the build environment and Node/Ruby/Python versions

---

## The gh-pages Branch

`gh-pages` is a conventional branch name used for Pages deployments. Some tools (like the `gh-pages` npm package) automatically build and push your site to a `gh-pages` branch, which GitHub Pages then serves.

**Create a gh-pages branch manually:**

```bash
git switch --orphan gh-pages
git rm -rf .
echo "<h1>My site</h1>" > index.html
git add index.html
git commit -m "add: initial gh-pages content"
git push origin gh-pages
```

An orphan branch has no history - it is completely separate from your `main` branch. This is appropriate for built output that should not be mixed with source history.

**Using the gh-pages npm package:**

```bash
npm install --save-dev gh-pages
```

In `package.json`:

```json
{
  "scripts": {
    "deploy": "gh-pages -d dist"
  }
}
```

Running `npm run deploy` builds to `dist/` and pushes it to the `gh-pages` branch automatically.

---

## Jekyll and Static Site Generators

GitHub Pages has built-in support for **Jekyll** - a static site generator written in Ruby. Push Markdown and layout templates, GitHub builds the HTML automatically.

**Starting a Jekyll site:**

```bash
gem install jekyll bundler
jekyll new my-site
cd my-site
bundle exec jekyll serve
```

Push the Jekyll project to your repository and GitHub Pages builds it automatically (no workflow needed for basic Jekyll sites).

**Supported Jekyll themes** (usable without a custom workflow):

Architect, Cayman, Dinky, Hacker, Leap day, Merlot, Midnight, Minimal, Modernist, Slate, Solo, Tactile, Time machine.

Select in `_config.yml`:

```yaml
theme: minima
```

Or use `jekyll-remote-theme` to use any GitHub-hosted theme:

```yaml
remote_theme: pages-themes/cayman@v0.2.0
```

**Limitations of built-in Jekyll:** GitHub Pages runs Jekyll in safe mode - only supported plugins are allowed. For full Jekyll 4+ support or custom plugins, use a GitHub Actions workflow to build and deploy instead.

**Other static site generators** (require GitHub Actions workflow):

| Generator  | Language   | Best for                  |
| ---------- | ---------- | ------------------------- |
| Hugo       | Go         | Speed, simplicity         |
| Astro      | JavaScript | Modern web, content sites |
| Eleventy   | JavaScript | Flexibility               |
| Docusaurus | React      | Project documentation     |
| VitePress  | Vue        | Documentation             |
| Gatsby     | React      | Content-heavy sites       |
| Next.js    | React      | Static export             |
| MkDocs     | Python     | Documentation             |

---

## Custom Domains

Replace `YOUR_USERNAME.github.io/REPO` with your own domain like `docs.example.com` or `example.com`.

### Setting up a subdomain (e.g., www.example.com)

**Step 1.** Go to your domain registrar (Namecheap, Cloudflare, GoDaddy etc.) and add a CNAME record:

```
Type:  CNAME
Name:  www
Value: YOUR_USERNAME.github.io
TTL:   Auto (or 3600)
```

**Step 2.** In GitHub Pages settings, enter `www.example.com` in the **Custom domain** field.

**Step 3.** Click **Save**. GitHub verifies the DNS record and configures HTTPS automatically via Let's Encrypt.

### Setting up an apex/root domain (e.g., example.com)

Apex domains require A records (CNAME is not supported at the root):

**Step 1.** Add four A records at your registrar:

```
Type:  A
Name:  @  (or blank, depends on registrar)
Values:
  185.199.108.153
  185.199.109.153
  185.199.110.153
  185.199.111.153
```

**Step 2.** Optionally add a CNAME for `www` pointing to `YOUR_USERNAME.github.io` so both `example.com` and `www.example.com` work.

**Step 3.** Enter `example.com` in the Pages custom domain field.

### Verifying a custom domain

GitHub recommends verifying your custom domain to prevent domain takeover attacks. Verification proves you own the domain before someone else can use it with their Pages site.

**Verify at:** Settings → Pages → Verified domains → Add a domain.

GitHub gives you a TXT record to add to your DNS. Once verified, no other GitHub user can use that domain for their Pages site.

### DNS propagation

DNS changes take time to propagate globally - typically 10-30 minutes but sometimes up to 48 hours. GitHub Pages settings will show an error until propagation completes.

---

## Enforcing HTTPS

GitHub Pages provides free SSL/TLS certificates via **Let's Encrypt** for all sites including custom domains.

**Enable HTTPS:**

Settings → Pages → Check **Enforce HTTPS**.

Once HTTPS is enforced, HTTP requests redirect automatically to HTTPS. The **Enforce HTTPS** option only becomes available after the SSL certificate has been issued, which can take a few minutes after configuring a custom domain.

> [!IMPORTANT]
> Always enforce HTTPS. HTTP pages can be intercepted and modified by man-in-the-middle attacks. Modern browsers show security warnings for non-HTTPS sites. There is no reason not to use HTTPS - it is free.

---

## Visibility and Access Control

**Public repositories:** GitHub Pages sites are publicly accessible to anyone on the internet by default, even without a GitHub account.

**Private repositories:** Available on Pro, Team and Enterprise plans. The Pages site can be made accessible only to authenticated GitHub users with repository access. Configure under Settings → Pages → visibility settings.

> [!NOTE]
> GitHub Pages is designed for static content. It does not support server-side processing, databases or authentication. If you need these features, Pages is not the right hosting solution. Consider Vercel, Netlify, Railway or a full cloud provider instead.

---

## What GitHub Pages Can and Cannot Do

### Can do

- Serve HTML, CSS, JavaScript and any static assets
- Serve from a branch or from a GitHub Actions deployment
- Use Jekyll for Markdown-based content
- Support custom domains with free HTTPS
- Serve single-page applications (with routing handled client-side)
- Redirect using meta tags or JavaScript
- Store static API responses (pre-generated JSON files)

### Cannot do

- Run server-side code (no PHP, Python, Node.js, Ruby on the server)
- Query databases
- Handle form submissions natively (use a third-party service like Formspree)
- Send emails
- Authenticate users
- Process payments
- Serve dynamic content that changes without a new deployment

---

## Pages Limits

| Limit                               | Value            |
| ----------------------------------- | ---------------- |
| Recommended repository size         | Under 1 GB       |
| Published site maximum size         | 1 GB             |
| Bandwidth (soft limit)              | 100 GB per month |
| Builds per hour (branch deployment) | 10               |
| Build timeout                       | 10 minutes       |

If your site exceeds the bandwidth soft limit, GitHub contacts you rather than taking your site offline immediately.

> [!TIP]
> For sites with large media files (images, videos), host those files elsewhere (a CDN, GitHub Releases or a dedicated media host) and reference them by URL. This keeps your repository small and fast to deploy.

---

## Try It Yourself

**Part 1 - Simple HTML site**

**Step 1.** Create a new public repository called `YOUR_USERNAME.github.io` (replace `YOUR_USERNAME` with your actual GitHub username).

**Step 2.** Clone it locally:

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_USERNAME.github.io.git
cd YOUR_USERNAME.github.io
```

**Step 3.** Create a simple `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>YOUR_NAME - Developer</title>
    <style>
      body {
        font-family: sans-serif;
        max-width: 800px;
        margin: 4rem auto;
        padding: 0 1rem;
      }
      h1 {
        color: #0d1117;
      }
      a {
        color: #0366d6;
      }
    </style>
  </head>
  <body>
    <h1>Hi, I am YOUR_NAME</h1>
    <p>I am learning Git, GitHub and software development.</p>
    <p>
      <a href="https://github.com/YOUR_USERNAME">My GitHub</a>
    </p>
  </body>
</html>
```

**Step 4.** Commit and push:

```bash
git add index.html
git commit -m "add: initial portfolio page"
git push
```

**Step 5.** Go to your repository Settings → Pages. The source should already be set to deploy from `main`. Your site will be live at `https://YOUR_USERNAME.github.io` within 1-2 minutes.

---

**Part 2 - Project site from docs/ folder**

**Step 1.** In any existing repository, create a `docs/` folder with an `index.html` or `index.md`.

**Step 2.** Go to Settings → Pages → Source: Deploy from a branch → Branch: `main` → Folder: `/docs` → Save.

Your project documentation is now live at `https://YOUR_USERNAME.github.io/REPO_NAME`.

---

## Common Mistakes

**Naming the user site repository incorrectly.**

The repository for your user site must be named exactly `YOUR_USERNAME.github.io` - all lowercase, matching your username precisely. Any other name creates a project site at a subpath, not a root user site.

**Missing `index.html` at the root.**

GitHub Pages looks for `index.html` (or `index.md` with Jekyll) as the entry point. If it does not exist, visitors see a 404 page or a raw directory listing. Always have an `index.html` at the root of whichever folder you are deploying from.

**Pushing to the wrong branch.**

If you configured Pages to deploy from `gh-pages` but you push your site to `main`, nothing updates. Always push to the configured branch.

**Using relative paths that break on project sites.**

A project site is served at `/REPO_NAME/`, not at `/`. CSS, JavaScript and image paths that start with `/` (absolute paths from root) will break. Use relative paths (`./style.css`) or set the correct `base` URL in your static site generator's config.

**Forgetting to set baseurl in Jekyll for project sites.**

In `_config.yml` for a project site:

```yaml
baseurl: "/REPO_NAME"
url: "https://YOUR_USERNAME.github.io"
```

Without this, all internal links break because Jekyll generates paths from `/` instead of `/REPO_NAME/`.

**Expecting server-side features to work.**

GitHub Pages is static hosting only. PHP files, `.htaccess` redirects, server-side includes and any backend processing will not work. For dynamic features, use a serverless function service or a different hosting provider.

---

## Summary

- GitHub Pages serves static HTML, CSS and JavaScript from a repository as a live website at `YOUR_USERNAME.github.io` or a custom domain
- **User sites** live at `YOUR_USERNAME.github.io` from a repo named exactly `YOUR_USERNAME.github.io`
- **Project sites** live at `YOUR_USERNAME.github.io/REPO_NAME` from any repository
- Deploy from a branch (simple) or via GitHub Actions (full control, any static site generator)
- Built-in Jekyll support for Markdown-based sites; use Actions for Hugo, Astro, Eleventy, Docusaurus and others
- Custom domains are free - add a CNAME record (subdomains) or A records (apex domains) at your registrar
- HTTPS is free via Let's Encrypt - always enforce it
- Pages cannot run server-side code, query databases or handle authentication
- Site limit: 1 GB published size, 100 GB bandwidth per month, 10 builds per hour

---

## Sources and Further Reading

- [GitHub Pages documentation](https://docs.github.com/en/pages) - complete official reference
- [Getting started with GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages) - step-by-step setup guides
- [Custom domains for GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site) - DNS configuration for all domain types
- [Jekyll documentation](https://jekyllrb.com/docs/) - full Jekyll reference
- [GitHub Pages themes](https://pages.github.com/themes/) - the official supported themes
- [actions/deploy-pages](https://github.com/actions/deploy-pages) - the official Pages deployment action

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

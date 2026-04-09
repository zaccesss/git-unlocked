# GitLab Pages

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

GitLab Pages is a free feature that lets you host any static website directly from a GitLab project. Whether you are publishing project documentation, a personal portfolio, a course site, a marketing landing page or an API reference, GitLab Pages deploys it automatically through a CI/CD pipeline every time you push. There is no separate hosting account to manage, no monthly bill for the hosting itself and no server to configure.

This file covers everything from your first HTML deployment to framework-specific pipeline configurations, custom domains, automatic HTTPS, access control for private sites and how GitLab Pages compares to GitHub Pages.

---

## Table of Contents

- [What is GitLab Pages?](#what-is-gitlab-pages)
- [How GitLab Pages works](#how-gitlab-pages-works)
- [Your first Pages deployment](#your-first-pages-deployment)
- [How the pages job has changed](#how-the-pages-job-has-changed)
- [Using built-in templates](#using-built-in-templates)
- [Framework-specific pipelines](#framework-specific-pipelines)
- [Testing the build on merge requests](#testing-the-build-on-merge-requests)
- [The Pages URL structure](#the-pages-url-structure)
- [Unique domains](#unique-domains)
- [Custom domains](#custom-domains)
- [HTTPS and SSL certificates](#https-and-ssl-certificates)
- [Access control](#access-control)
- [Pages settings and limits](#pages-settings-and-limits)
- [Pages on self-managed GitLab](#pages-on-self-managed-gitlab)
- [GitLab Pages vs GitHub Pages](#gitlab-pages-vs-github-pages)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitLab Pages?

GitLab Pages hosts **static websites** — sites composed of HTML, CSS, JavaScript and static assets with no server-side processing at request time. The site files are pre-built (either by hand or by a static site generator) and served directly from GitLab's infrastructure.

**What it is:**
- Free on all GitLab plans — Free, Premium, Ultimate
- Available on GitLab.com and on self-managed instances (admin must enable it)
- Triggered automatically by your CI/CD pipeline on every push to the default branch
- Compatible with any static site generator: Jekyll, Hugo, Gatsby, MkDocs, Eleventy, Sphinx, Pelican, Middleman, Hexo, Astro, VitePress, plain HTML — anything that produces static files
- Custom domain capable with automatic HTTPS via Let's Encrypt
- Access-controllable — restrict a Pages site to project members only

**What it is not:**
- A server for dynamic applications (no Node.js, Django, Rails, PHP at runtime)
- A substitute for a backend API
- A paid hosting service — it is a free feature included with every project

**Common uses:**
- Project documentation (auto-generated from code comments or written in Markdown)
- Personal developer portfolios and CVs
- Course or tutorial sites (like git-unlocked itself could use)
- Open source project landing pages
- API reference documentation
- Company knowledge bases and internal wikis (with access control)
- Client-facing project status pages

---

## How GitLab Pages Works

The mechanism is straightforward:

1. Your CI/CD pipeline runs a job that produces a directory called `public/` containing your site files
2. That job has `pages: true` set (since GitLab 17.5) — or was historically named `pages`
3. The job declares `public/` as an artifact
4. When the pipeline completes, GitLab takes the contents of `public/` and deploys them to the Pages server
5. Your site becomes accessible at `https://NAMESPACE.gitlab.io/PROJECT-NAME`

The entire deployment happens through your standard CI/CD pipeline. There is no separate deployment command, no FTP upload, no SSH into a server. You push code, the pipeline runs, the site updates.

**The two mandatory requirements for Pages deployment:**

1. The job must produce a `public/` directory containing your site files
2. The `public/` directory must be declared as an artifact in the job

If either of these is missing, the deployment does not happen even if the pipeline passes.

> [!NOTE]
> Pages deployments only go live from the **default branch** (usually `main`). Pipeline jobs on other branches can build and test the site, but they do not update the live Pages deployment. This is intentional — only tested, reviewed code should appear on the live site.

---

## Your First Pages Deployment

The simplest possible Pages site: plain HTML with no build step.

**Step 1.** Create your project directory structure:

```
my-site/
├── .gitlab-ci.yml
└── public/
    ├── index.html
    └── style.css
```

**Step 2.** Create `public/index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My GitLab Pages Site</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <h1>Hello from GitLab Pages</h1>
  <p>This site is deployed automatically every time I push to main.</p>
  <p>No server configuration. No hosting bill. Just a pipeline.</p>
</body>
</html>
```

**Step 3.** Create `public/style.css`:

```css
body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  max-width: 800px;
  margin: 2rem auto;
  padding: 0 1rem;
  color: #333;
}
h1 { color: #6b4fbb; }
```

**Step 4.** Create `.gitlab-ci.yml`:

```yaml
deploy-pages:
  stage: deploy
  script:
    - echo "Deploying static site to GitLab Pages"
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

**Step 5.** Commit and push:

```bash
git add .
git commit -m "add: initial GitLab Pages site"
git push
```

**Step 6.** Watch the pipeline: left sidebar → **Build → Pipelines**. When the pipeline completes (usually 30-60 seconds for a static site with no build step), go to **Deploy → Pages** to see your site URL and click it.

Your site is live.

---

## How the Pages Job Has Changed

Understanding this history prevents confusion when reading older tutorials.

### Old approach (before GitLab 17.5): job must be named `pages`

In older configurations, GitLab required the deployment job to be named exactly `pages`. The job name was the signal that triggered Pages deployment:

```yaml
# Old syntax — still works but not recommended for new projects
pages:
  script:
    - echo "Deploy"
  artifacts:
    paths:
      - public/
```

### New approach (GitLab 17.5+): use `pages: true`

Since GitLab 17.5 (released August 2025), you can name the job anything and add `pages: true` to trigger Pages deployment. This is the recommended approach for new projects:

```yaml
# New syntax — any job name, uses pages: true
deploy-to-pages:
  script:
    - echo "Deploy"
  pages: true
  artifacts:
    paths:
      - public/
```

> [!NOTE]
> Both approaches work simultaneously. The old `pages` job name still triggers deployment. Projects created before 17.5 do not need to change. New projects should use `pages: true` with a descriptive job name.

---

## Using Built-in Templates

GitLab provides ready-to-use project templates for many static site frameworks. Each template includes example content and a pre-written `.gitlab-ci.yml`. The fastest way to get a Pages site up and running.

**Create a project from a Pages template:**

1. Click **Create new (+)** → **New project/repository**
2. Select **Create from template**
3. Click the **Pages** tab
4. Choose a framework: Jekyll, Hugo, Hexo, Gatsby, Pelican, Middleman, Eleventy, MkDocs, plain HTML and others
5. Fill in project name, namespace and visibility
6. Click **Use template**

The project is created immediately with everything in place. Push any change to trigger the first deployment.

---

## Framework-Specific Pipelines

Each static site generator has different requirements. Here are production-ready pipeline configurations for the most popular ones.

### Plain HTML (no build step)

When all your site files are already in `public/` and need no processing:

```yaml
deploy-pages:
  stage: deploy
  script:
    - echo "No build step needed — deploying static files"
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

If your source files are in the project root and need to be moved into `public/`:

```yaml
deploy-pages:
  stage: deploy
  script:
    - mkdir -p public
    - cp -r *.html *.css *.js images/ public/ 2>/dev/null || true
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

### Jekyll

Jekyll is a Ruby-based static site generator widely used for documentation and blogs. It is the engine behind GitHub Pages' default build.

```yaml
image: ruby:3.3

stages:
  - test
  - deploy

variables:
  JEKYLL_ENV: production

# Cache gems between pipeline runs
cache:
  paths:
    - vendor/ruby

before_script:
  - gem install bundler --no-document
  - bundle config set --local path 'vendor/ruby'
  - bundle install --jobs 4 --retry 3

# Test build on merge requests (does not deploy)
test-build:
  stage: test
  script:
    - bundle exec jekyll build -d test-public/ --strict_front_matter
  artifacts:
    paths:
      - test-public/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

# Deploy to Pages only from default branch
deploy-pages:
  stage: deploy
  script:
    - bundle exec jekyll build -d public/
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

> [!TIP]
> The `--strict_front_matter` flag in the test job causes Jekyll to fail on YAML front matter errors. This catches mistakes before they reach the live site.

### Hugo

Hugo is a Go-based static site generator — one of the fastest available. Build times are typically under a second even for large sites.

```yaml
image: registry.gitlab.com/pages/hugo:latest

stages:
  - test
  - deploy

variables:
  GIT_SUBMODULE_STRATEGY: recursive   # required if your theme is a submodule

# Test build on MRs
test-build:
  stage: test
  script:
    - hugo --minify --baseURL "https://$CI_PROJECT_NAMESPACE.gitlab.io/$CI_PROJECT_NAME/"
  artifacts:
    paths:
      - public/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

# Deploy to Pages from default branch
deploy-pages:
  stage: deploy
  script:
    - hugo --minify --baseURL "https://$CI_PROJECT_NAMESPACE.gitlab.io/$CI_PROJECT_NAME/"
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

> [!NOTE]
> Hugo outputs to `public/` by default — no `-d` flag needed. The `--minify` flag compresses HTML, CSS and JS output. Set `--baseURL` to your actual Pages URL so internal links work correctly.

### MkDocs

MkDocs is a Python-based documentation framework. The Material theme (by squidfunk) is extremely popular and produces professional-quality documentation sites.

```yaml
image: python:3.12-slim

stages:
  - test
  - deploy

cache:
  paths:
    - .cache/pip

before_script:
  - pip install mkdocs mkdocs-material --cache-dir .cache/pip --quiet

# Test build on MRs
test-build:
  stage: test
  script:
    - mkdocs build --strict --site-dir test-site/
  artifacts:
    paths:
      - test-site/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

# Deploy to Pages from default branch
deploy-pages:
  stage: deploy
  script:
    - mkdocs build --strict --site-dir public/
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

> [!TIP]
> `--strict` causes MkDocs to fail on warnings (broken links, missing files). This is strongly recommended — it catches broken internal links before they reach the live site.

### Gatsby

Gatsby is a React-based static site generator with a rich plugin ecosystem. Build times are longer but the output is highly optimised.

```yaml
image: node:20-alpine

stages:
  - install
  - test
  - deploy

cache:
  key: $CI_COMMIT_REF_SLUG
  paths:
    - node_modules/
    - .cache/           # Gatsby build cache — dramatically speeds up repeat builds

install:
  stage: install
  script:
    - npm ci
  artifacts:
    paths:
      - node_modules/
    expire_in: 1 hour

test-build:
  stage: test
  needs: ["install"]
  script:
    - npm run build -- --prefix-paths
  environment:
    name: test
  artifacts:
    paths:
      - public/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

deploy-pages:
  stage: deploy
  needs: ["install"]
  script:
    - npm run build -- --prefix-paths
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

> [!NOTE]
> Gatsby outputs to `public/` by default. The `--prefix-paths` flag is needed if your site is deployed to a subpath (e.g. `namespace.gitlab.io/project-name`). Configure `pathPrefix` in `gatsby-config.js`.

### Sphinx (Python documentation)

Sphinx is the standard documentation tool for Python projects. Widely used for scientific, technical and API documentation.

```yaml
image: python:3.12

stages:
  - test
  - deploy

cache:
  paths:
    - .pip-cache/

before_script:
  - pip install sphinx sphinx-rtd-theme --cache-dir .pip-cache

test-build:
  stage: test
  script:
    - sphinx-build -b html docs/ test-site/ -W   # -W treats warnings as errors
  artifacts:
    paths:
      - test-site/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

deploy-pages:
  stage: deploy
  script:
    - sphinx-build -b html docs/ public/ -W
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

### Eleventy (11ty)

Eleventy is a fast, flexible JavaScript static site generator with no client-side JavaScript by default.

```yaml
image: node:20-alpine

stages:
  - test
  - deploy

cache:
  key: $CI_COMMIT_REF_SLUG
  paths:
    - node_modules/

before_script:
  - npm ci

test-build:
  stage: test
  script:
    - npx @11ty/eleventy --output=test-site
  artifacts:
    paths:
      - test-site/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

deploy-pages:
  stage: deploy
  script:
    - npx @11ty/eleventy --output=public
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

---

## Testing the Build on Merge Requests

A critical practice: test your site build on every merge request so errors are caught before they reach the live site.

The pattern is the same across all frameworks:

1. Add a `test` stage before `deploy`
2. Add a job in the `test` stage that builds the site to a **different directory** (not `public/`)
3. Run this job only on merge request pipelines
4. Run the deploy job only on the default branch

```yaml
stages:
  - test
  - deploy

# Test job — runs on MRs, builds to test-site/ (not public/)
test-build:
  stage: test
  image: python:3.12-slim
  script:
    - pip install mkdocs mkdocs-material --quiet
    - mkdocs build --strict --site-dir test-site/
  artifacts:
    paths:
      - test-site/
    expire_in: 1 hour
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

# Deploy job — runs only on default branch, builds to public/
deploy-pages:
  stage: deploy
  image: python:3.12-slim
  script:
    - pip install mkdocs mkdocs-material --quiet
    - mkdocs build --strict --site-dir public/
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

> [!IMPORTANT]
> Build to `test-site/` (or any name other than `public/`) in the test job. If the test job builds to `public/`, GitLab may attempt to deploy it even on MR pipelines, overwriting your live site with untested code.

---

## The Pages URL Structure

### Default URL on GitLab.com

```
https://NAMESPACE.gitlab.io/PROJECT-SLUG
```

Where:
- `NAMESPACE` is your username or group name
- `PROJECT-SLUG` is your project's URL slug

**Examples:**
- Username `zaccess`, project `my-portfolio` → `https://zaccess.gitlab.io/my-portfolio`
- Group `acme-corp`, project `documentation` → `https://acme-corp.gitlab.io/documentation`

### Special case: namespace project

If your project slug matches your namespace exactly, the site is served at the root domain with no path:

- Username `zaccess`, project also named `zaccess` → `https://zaccess.gitlab.io`
- Group `acme-corp`, project also named `acme-corp` → `https://acme-corp.gitlab.io`

This is useful for personal sites or organisation root sites where you want the clean root URL.

### Finding your Pages URL

After a successful Pages deployment: left sidebar → **Deploy → Pages**. The URL is shown at the top of the page. If you have multiple domains (custom + default), all are listed here.

---

## Unique Domains

Since GitLab 15.11, each project gets a unique random domain in addition to the standard path-based URL. The format varies but looks something like:

```
https://NAMESPACE-PROJECT-RANDOMSTRING.gitlab.io
```

**Why unique domains exist:**

The path-based URL `namespace.gitlab.io/project` shares the root domain `namespace.gitlab.io` with every other Pages project in that namespace. This creates a cross-site scripting risk: a malicious project in the same namespace could potentially set cookies that affect other projects under the same domain.

Unique domains isolate each project on its own subdomain, eliminating this risk entirely.

Both the unique domain and the path-based URL work simultaneously. You do not need to choose one — both serve the same site. The unique domain is enabled by default for all new projects.

---

## Custom Domains

Replace the default `.gitlab.io` URL with your own domain (e.g. `docs.example.com` or `example.com`).

### Step 1: Add the domain in GitLab

Left sidebar → **Deploy → Pages** → **New domain**

Enter your domain name. GitLab immediately shows you:
1. A **TXT verification record** to prove you own the domain
2. DNS configuration instructions

### Step 2: Configure DNS

**For a subdomain** (e.g. `docs.example.com`):

Add a CNAME record at your DNS provider:
```
docs.example.com.    CNAME    NAMESPACE.gitlab.io.
```

**For an apex/root domain** (e.g. `example.com`):

Add an A record pointing to GitLab's Pages IP addresses. Check `docs.gitlab.com/user/project/pages/custom_domains_ssl_tls_certification/` for the current IPs — they can change and must be kept up to date.

Alternatively, use an ALIAS or ANAME record if your DNS provider supports it (same effect as CNAME but works for apex domains).

**Add the TXT verification record** (required for all domains):
```
_gitlab-pages-verification-code.docs.example.com.    TXT    "gitlab-pages-verification-code=YOURCODE"
```

GitLab generates the exact record name and value for your specific domain.

### Step 3: Verify ownership

DNS changes can take from a few minutes to 48 hours to propagate. Once propagated:

Left sidebar → **Deploy → Pages** → click your domain → **Verify domain**

After verification passes, your custom domain serves your Pages site.

### Setting a primary domain

If you have both a custom domain and the default `.gitlab.io` URL, you can designate one as primary. GitLab issues **308 Permanent Redirect** responses from all non-primary domains to the primary one.

Left sidebar → **Deploy → Pages** → click a domain → **Set as primary domain**.

---

## HTTPS and SSL Certificates

### Automatic Let's Encrypt (default and recommended)

GitLab automatically provisions free SSL certificates from Let's Encrypt for all verified custom domains. Certificates are:
- **Issued**: within minutes of domain verification
- **Renewed**: automatically, typically 60 days before expiry (Let's Encrypt certificates last 90 days)
- **Zero configuration**: nothing to do after adding and verifying your domain

**Requirements for Let's Encrypt to work:**
- DNS must point to GitLab's servers (Let's Encrypt verifies via HTTP challenge)
- The domain must be verified in GitLab first

### Force HTTPS

Redirect all HTTP requests to HTTPS with a 301 redirect:

Left sidebar → **Deploy → Pages** → toggle **Force HTTPS** → save.

> [!TIP]
> Always enable Force HTTPS once your certificate is provisioned. HTTP access to your site is unnecessary and potentially insecure. Modern browsers flag HTTP sites as "Not Secure".

### Manual SSL certificate

If you have a certificate from a commercial CA (for example, a wildcard certificate covering `*.example.com` that you already own), you can use it instead of Let's Encrypt:

When adding a custom domain: disable **Automatic certificate management using Let's Encrypt** → paste your certificate and private key into the fields.

The certificate must be in PEM format. Include the full certificate chain (your certificate + any intermediate CA certificates).

---

## Access Control

By default, a GitLab Pages site is **publicly accessible** — anyone with the URL can visit it, regardless of whether the underlying project is private.

This is appropriate for most documentation and portfolio sites. But for internal documentation, staging sites or anything you want to keep internal, you can restrict Pages access.

### Restrict to project members

Settings → General → Visibility, project features, permissions → **Pages** → change from **Everyone** to **Project members**.

With this restriction:
- Visitors must be signed in to a GitLab account
- The signed-in account must have at least Guest access to the project (either directly or via a group)
- Unauthenticated visitors see a sign-in prompt, not the site content

> [!NOTE]
> On GitLab.com, Pages access control is available on all plans. On self-managed GitLab, the instance administrator must enable Pages access control in the GitLab configuration file before this option appears in project settings.

### Use cases for access-controlled Pages

- **Internal documentation**: team wikis and technical docs visible only to employees
- **Staging/preview sites**: show clients a work-in-progress site without making it public
- **Training materials**: course content gated to enrolled students (who have project access)
- **Internal knowledge bases**: company procedures and standards accessible only to staff

---

## Pages Settings and Limits

### Limits on GitLab.com

| Limit | Value |
|---|---|
| Maximum unpacked site size | 100 MiB (approximately 100 MB) |
| Maximum number of files | 200,000 |
| Maximum single file size | 100 MiB |
| HTTPS | Required (HTTP redirected to HTTPS) |
| Custom domains per project | Unlimited |

The 100 MiB limit catches teams off-guard more often than expected. Common culprits:
- Uncompressed images (compress before adding to the repository)
- Videos embedded directly (use YouTube/Vimeo embeds instead)
- Large downloadable assets (consider a file host or GitLab releases for large downloads)
- Unminified CSS/JS bundles

On self-managed GitLab, all of these limits are configurable by the administrator.

### Cache-Control headers

GitLab Pages serves files with:
```
Cache-Control: max-age=600, s-maxage=600, public
```

This tells browsers and CDNs to cache content for 10 minutes. For most documentation sites this is fine. If you need custom cache headers (e.g. for a web app that uses service workers), you need to serve through a CDN or reverse proxy in front of GitLab Pages.

### Viewing Pages settings

Left sidebar → **Deploy → Pages** shows:
- All active Pages URLs (default and custom)
- Access control setting
- Custom domain list with verification status
- Deployment history (when and which pipeline deployed each version)
- Link to the Pages configuration in project settings

---

## Pages on Self-Managed GitLab

GitLab Pages must be explicitly enabled by the instance administrator on self-managed installations. Once enabled, the feature works identically to GitLab.com from the project perspective.

**Admin configuration (in `/etc/gitlab/gitlab.rb`):**

```ruby
pages_external_url "https://pages.example.com"
gitlab_pages['enable'] = true
gitlab_pages['access_control'] = true  # required for Pages access control feature
```

Apply: `sudo gitlab-ctl reconfigure`

**Wildcard DNS**: self-managed Pages requires a wildcard DNS record so that `*.pages.example.com` resolves to the Pages server:

```
*.pages.example.com.    A    YOUR_GITLAB_PAGES_SERVER_IP
```

Each project's Pages site then gets a URL like `https://namespace.pages.example.com/project`.

**Custom domains** on self-managed also require a wildcard certificate or per-domain certificates. Let's Encrypt automatic issuance works on self-managed instances with internet access.

---

## GitLab Pages vs GitHub Pages

Both platforms offer free static site hosting, but they differ in meaningful ways.

| Feature | GitLab Pages | GitHub Pages |
|---|---|---|
| **Cost** | Free (all tiers) | Free |
| **Build system** | Full CI/CD pipeline | GitHub Actions or built-in Jekyll |
| **Framework flexibility** | Any framework (via CI/CD pipeline) | Any via Actions; Jekyll only with built-in builder |
| **Custom domains** | Yes, with DNS verification | Yes |
| **HTTPS** | Automatic Let's Encrypt | Automatic |
| **Force HTTPS** | Yes (toggle in settings) | Yes |
| **Access control** | Restrict to project members (free) | Public only; private Pages requires GitHub Enterprise |
| **Maximum site size** | **100 MiB** | **1 GiB** |
| **Maximum files** | 200,000 | No published limit |
| **Pipeline integration** | Native — Pages is a CI/CD job | Via GitHub Actions |
| **Build time limit** | Standard job timeout (60 min default) | 10 minutes per build |
| **Private project + public Pages** | Supported (Pages can be public even if project is private) | Not supported (Pages mirrors repo visibility) |
| **Self-hosted** | Yes (GitLab CE/EE) | No (GitHub Enterprise Server supports it) |

**Key practical differences:**

**Build flexibility**: Both platforms can now use any static site generator via their respective CI/CD systems (GitHub Actions for GitHub, `.gitlab-ci.yml` for GitLab). The difference is mostly syntax.

**Size limit**: GitHub Pages' 1 GiB limit vs GitLab's 100 MiB is a real constraint for large sites. If your documentation includes many screenshots, diagrams or downloadable PDFs, you will hit GitLab's limit sooner.

**Access control**: GitLab's ability to restrict Pages to project members is available free on all plans. GitHub requires Enterprise to make Pages private. This makes GitLab significantly more useful for internal documentation at organisations that cannot afford Enterprise.

**Private project + public site**: On GitLab, you can have a private project (code not visible to the public) with a public Pages site (documentation visible to everyone). This is useful for commercial projects that want public docs but private source code.

---

## Try It Yourself

**Exercise 1: Deploy a plain HTML site**

```bash
# Create a new project on GitLab, then clone it locally
git clone git@gitlab.com:YOUR_USERNAME/YOUR_PROJECT.git
cd YOUR_PROJECT

# Create the site structure
mkdir -p public
cat > public/index.html << 'EOF'
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My Pages Site</title>
  <style>
    body { font-family: sans-serif; max-width: 600px; margin: 3rem auto; }
    h1 { color: #6b4fbb; }
  </style>
</head>
<body>
  <h1>My GitLab Pages Site</h1>
  <p>Deployed automatically via CI/CD on every push to main.</p>
  <p>Built on: <strong id="date"></strong></p>
  <script>document.getElementById('date').textContent = new Date().toLocaleDateString();</script>
</body>
</html>
EOF

# Create the pipeline
cat > .gitlab-ci.yml << 'EOF'
deploy-pages:
  stage: deploy
  script:
    - echo "Deploying static site"
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
EOF

git add .
git commit -m "add: GitLab Pages site"
git push
```

Navigate to **Build → Pipelines** to watch it run. Then **Deploy → Pages** to find your URL.

**Exercise 2: Add a test stage**

Update `.gitlab-ci.yml` to test the build on merge requests:

```yaml
stages:
  - test
  - deploy

test-build:
  stage: test
  script:
    - echo "Testing HTML is valid"
    - test -f public/index.html || (echo "index.html missing" && exit 1)
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"

deploy-pages:
  stage: deploy
  script:
    - echo "Deploying"
  pages: true
  artifacts:
    paths:
      - public/
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

Create a new branch, make a change, push and open a merge request. The test job runs on the MR. Merge it and the deploy job runs on main.

---

## Common Mistakes

**Not outputting to `public/`.** This is the single most common Pages mistake. GitLab Pages serves only the `public/` directory. If your generator outputs to `_site/` (Jekyll), `out/` (Next.js static export), `dist/` or any other directory, you must either configure the generator to output to `public/` or copy the output: `cp -r _site/ public/`. Many new users spend hours debugging a pipeline that runs successfully but produces no Pages output because the output went to the wrong directory.

**Forgetting to declare `public/` as an artifact.** Setting `pages: true` alone is not enough. The pipeline must also have `artifacts: paths: - public/`. Without the artifact declaration, GitLab has nothing to deploy even if `public/` was created.

**Running the deploy job on non-default branches.** If you leave out the `rules:` block or write rules incorrectly, every push to every branch attempts to deploy to Pages. This means feature branch work immediately overwrites the live site. Always scope the deploy job to `$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH`.

**Building to `public/` in the MR test job.** The test job should build to a directory with a different name (e.g. `test-site/`). If it builds to `public/`, GitLab detects the artifact and may deploy it even on MR pipelines, overwriting the live site with potentially unreviewed code.

**Not setting the base URL.** Many generators (Hugo, Gatsby, 11ty) produce internal links relative to the root. If your site is at `namespace.gitlab.io/project` rather than `namespace.gitlab.io/`, all internal links break because the generator assumed root deployment. Set the base URL during build: Hugo uses `--baseURL`, Gatsby uses `pathPrefix` in config, MkDocs uses `site_url` in `mkdocs.yml`.

**Exceeding the 100 MiB size limit.** If your pipeline job succeeds but the site does not appear, check whether the artifact size exceeds 100 MiB. The pipeline logs include the artifact size. Audit your `public/` directory for large images, PDFs or compiled assets. Use image compression tools and consider linking to large files hosted elsewhere.

**Not enabling Force HTTPS.** After adding a custom domain and verifying the Let's Encrypt certificate, some users forget to enable Force HTTPS. HTTP access continues to work and browsers mark it as not secure. Enable Force HTTPS immediately after the certificate is provisioned.

---

## Summary

- GitLab Pages hosts any static site for free on all plans — no separate hosting account or monthly fee
- Deploy by producing a `public/` directory in a CI/CD job with `pages: true` and declaring it as an artifact
- Since GitLab 17.5, any job name works with `pages: true`. Before 17.5, the job had to be named exactly `pages`
- Deployments only go live from the **default branch**. MR pipelines can test the build without deploying
- Compatible with any static site generator: Jekyll, Hugo, Gatsby, MkDocs, Eleventy, Sphinx, Astro, plain HTML — anything that produces static files
- Always add a separate test job that builds to a different directory (not `public/`) and runs on MR pipelines to catch errors before they reach the live site
- Default URL: `https://NAMESPACE.gitlab.io/PROJECT-SLUG`. Unique random domain also provided automatically since 15.11
- Custom domains: add via **Deploy → Pages → New domain**, configure DNS (CNAME or A record), add TXT verification record, then verify
- HTTPS: automatic via Let's Encrypt after domain verification. Enable **Force HTTPS** to redirect HTTP visitors
- Access control: restrict Pages to project members only (free feature — GitHub requires Enterprise for this)
- Size limits: 100 MiB maximum site size, 200,000 maximum files. Significantly smaller than GitHub Pages (1 GiB)
- Key advantage over GitHub Pages: access control is free; private project with public Pages is supported; full CI/CD pipeline flexibility

---

## Sources and Further Reading

- [GitLab Pages documentation](https://docs.gitlab.com/user/project/pages/) - complete Pages reference
- [Custom domains and SSL](https://docs.gitlab.com/user/project/pages/custom_domains_ssl_tls_certification/) - domain setup and certificate configuration
- [GitLab Pages access control](https://docs.gitlab.com/user/project/pages/pages_access_control/) - restricting Pages to members
- [GitLab Pages examples repository](https://gitlab.com/pages) - official example projects for every major framework
- [Self-managed Pages configuration](https://docs.gitlab.com/administration/pages/) - admin setup guide
- [Jekyll documentation](https://jekyllrb.com/docs/) - the most widely used Pages generator
- [Hugo documentation](https://gohugo.io/documentation/) - fastest build times
- [MkDocs Material theme](https://squidfunk.github.io/mkdocs-material/) - professional documentation theme
- [Eleventy documentation](https://www.11ty.dev/docs/) - flexible JavaScript-based generator

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

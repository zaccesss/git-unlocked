# GitHub Packages and Container Registry

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 30 minutes

GitHub Packages is a package registry built into GitHub. It lets you host and distribute software packages - npm modules, Python packages, Docker container images, Maven artifacts, NuGet packages and more - alongside your source code in the same platform. Instead of maintaining a separate registry or paying for a third-party hosting service, your packages live in the same place as your code, with the same permissions model and integrated with GitHub Actions.

---

## Table of Contents

- [What is GitHub Packages?](#what-is-github-packages)
- [Supported package registries](#supported-package-registries)
- [GitHub Container Registry (GHCR)](#github-container-registry-ghcr)
- [Authentication](#authentication)
- [Publishing an npm package](#publishing-an-npm-package)
- [Publishing a Python package](#publishing-a-python-package)
- [Publishing a Docker image to GHCR](#publishing-a-docker-image-to-ghcr)
- [Publishing a Maven package](#publishing-a-maven-package)
- [Publishing a NuGet package](#publishing-a-nuget-package)
- [Managing packages](#managing-packages)
- [Package visibility and access control](#package-visibility-and-access-control)
- [Linking packages to repositories](#linking-packages-to-repositories)
- [Deleting and restoring packages](#deleting-and-restoring-packages)
- [Automating package publishing with GitHub Actions](#automating-package-publishing-with-github-actions)
- [Pricing and storage limits](#pricing-and-storage-limits)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitHub Packages?

GitHub Packages is a package hosting service that integrates directly with GitHub. It combines your source code and packages in one place - same permissions, same teams, same billing, same interface.

**Key benefits:**

- Packages and source code live together in the same ecosystem
- Permissions are inherited from the repository or set independently
- Works natively with GitHub Actions (publish on release, on push, on tag)
- No separate account or login for your package registry
- The `GITHUB_TOKEN` in Actions can authenticate to Packages automatically
- Free for public packages

**What you can host:**

- Docker and OCI container images (via GHCR at `ghcr.io`)
- npm packages (JavaScript/TypeScript)
- Apache Maven packages (Java)
- Gradle packages (Java/Kotlin)
- NuGet packages (.NET)
- RubyGems (Ruby)

---

## Supported Package Registries

| Registry | Ecosystem | Namespace |
|---|---|---|
| Container registry | Docker, OCI | `ghcr.io/OWNER/IMAGE` |
| npm registry | JavaScript, TypeScript | `@OWNER/package-name` |
| Apache Maven | Java | `github.com/OWNER/REPO` |
| Gradle | Java, Kotlin | `github.com/OWNER/REPO` |
| NuGet | .NET | `nuget.pkg.github.com/OWNER` |
| RubyGems | Ruby | `rubygems.pkg.github.com/OWNER` |

Each registry uses a different package format and client tool. The underlying storage and permissions are all part of GitHub Packages.

---

## GitHub Container Registry (GHCR)

The **GitHub Container Registry** at `ghcr.io` is the most widely used part of GitHub Packages. It stores Docker images and OCI-compatible artifacts.

**Key features of GHCR:**

- Supports Docker images, Helm charts (OCI format) and other OCI artifacts
- Fine-grained permissions separate from repository permissions
- Anonymous access for public images (no account required to pull)
- Free for public images
- Integrated with GitHub Actions via `GITHUB_TOKEN`
- Cross-organisation sharing
- Automatic migration from the old `docker.pkg.github.com` Docker registry (which is now retired)

**GHCR vs Docker Hub:**

| Feature | GHCR | Docker Hub (Free) |
|---|---|---|
| Cost for public images | Free | Free |
| Cost for private images | Included in GitHub plan | Limited free tier |
| Rate limits (unauthenticated pull) | None for public | 100 pulls/6 hours |
| Integration with GitHub Actions | Native | Requires login step |
| Permissions model | GitHub permissions | Docker Hub permissions |

For projects already on GitHub, GHCR is the natural choice. Docker Hub remains more widely known and is the default for many tools.

---

## Authentication

GitHub Packages requires authentication to publish packages and to pull private packages. Public packages can be pulled without authentication.

**Authentication methods:**

**1. GITHUB_TOKEN in GitHub Actions (recommended for CI/CD):**

The `GITHUB_TOKEN` automatically has permission to push to Packages for the repository running the workflow. No additional setup needed.

```yaml
- uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
```

**2. Personal Access Token (PAT) for local development:**

Create a PAT at Settings → Developer settings → Personal access tokens.

Required scopes:
- `read:packages` - download packages
- `write:packages` - publish packages
- `delete:packages` - delete packages
- `repo` - needed for private repository packages

Store the token securely - never commit it to a repository.

**Authenticate locally with Docker:**

**Windows (PowerShell):**

```powershell
$env:CR_PAT = "YOUR_TOKEN_HERE"
echo $env:CR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin
```

**Mac and Linux:**

```bash
export CR_PAT="YOUR_TOKEN_HERE"
echo $CR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin
```

**Authenticate locally with npm:**

Create or edit `~/.npmrc`:

```
//npm.pkg.github.com/:_authToken=YOUR_TOKEN_HERE
```

**Authenticate locally with pip (twine for PyPI-style publishing):**

```bash
pip install twine
python -m twine upload --repository-url https://upload.pypi.org/legacy/ dist/*
```

For GitHub Packages, use the GitHub npm registry for JavaScript and the Container registry for Python wheel distributions or publish to PyPI directly.

---

## Publishing an npm Package

**Step 1.** Configure your package to point to GitHub Packages registry.

In `package.json`, set the `name` with the GitHub owner scope and add a `publishConfig`:

```json
{
  "name": "@YOUR_USERNAME/my-package",
  "version": "1.0.0",
  "description": "My npm package on GitHub Packages",
  "publishConfig": {
    "registry": "https://npm.pkg.github.com"
  }
}
```

**Step 2.** Create or edit `.npmrc` in the project root:

```
@YOUR_USERNAME:registry=https://npm.pkg.github.com
//npm.pkg.github.com/:_authToken=${NPM_TOKEN}
```

**Step 3.** Authenticate and publish:

**Mac and Linux:**

```bash
export NPM_TOKEN="YOUR_PAT_HERE"
npm publish
```

**Windows (PowerShell):**

```powershell
$env:NPM_TOKEN = "YOUR_PAT_HERE"
npm publish
```

**Step 4.** Install the package from GitHub Packages:

```bash
npm install @YOUR_USERNAME/my-package
```

Users need their own `.npmrc` configured with a PAT that has `read:packages` scope.

---

## Publishing a Python Package

GitHub Packages does not have a dedicated PyPI-compatible registry. For Python packages, the options are:

**Option 1 - Publish to PyPI (recommended for public packages):**

PyPI is the standard Python package index. Packages published there are installable with `pip install my-package` without any special configuration.

```bash
pip install build twine
python -m build
python -m twine upload dist/*
```

**Option 2 - Publish Python wheel as a GitHub Release asset:**

For private or internal packages, attach the wheel (`.whl`) and source distribution (`.tar.gz`) as release assets. Users install with:

```bash
pip install https://github.com/YOUR_USERNAME/REPO/releases/download/v1.0.0/my_package-1.0.0-py3-none-any.whl
```

**Option 3 - Docker image containing Python package:**

For complex environments, build a Docker image with your package installed and publish it to GHCR.

---

## Publishing a Docker Image to GHCR

Docker images are the most common use of GitHub Packages.

**Build and push manually:**

**Windows (PowerShell), Mac and Linux:**

```bash
# Build the image
docker build -t ghcr.io/YOUR_USERNAME/my-app:latest .

# Log in to GHCR
echo YOUR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin

# Push the image
docker push ghcr.io/YOUR_USERNAME/my-app:latest

# Push with a version tag too
docker tag ghcr.io/YOUR_USERNAME/my-app:latest ghcr.io/YOUR_USERNAME/my-app:v1.0.0
docker push ghcr.io/YOUR_USERNAME/my-app:v1.0.0
```

**Pull a public image (no authentication needed):**

```bash
docker pull ghcr.io/YOUR_USERNAME/my-app:latest
```

**Image naming convention:**

```
ghcr.io/OWNER/IMAGE_NAME:TAG

ghcr.io/your-username/my-app:latest
ghcr.io/your-username/my-app:v1.0.0
ghcr.io/your-org/service-name:2.1.0
```

**Adding image metadata labels (in Dockerfile):**

```dockerfile
FROM ubuntu:22.04

LABEL org.opencontainers.image.source="https://github.com/YOUR_USERNAME/YOUR_REPO"
LABEL org.opencontainers.image.description="My application container"
LABEL org.opencontainers.image.licenses="MIT"
```

The `org.opencontainers.image.source` label links the image to its GitHub repository. This enables the package to appear in the repository's right sidebar on GitHub.

---

## Publishing a Maven Package

**Step 1.** Configure `pom.xml`:

```xml
<project>
  <distributionManagement>
    <repository>
      <id>github</id>
      <name>GitHub YOUR_USERNAME Apache Maven Packages</name>
      <url>https://maven.pkg.github.com/YOUR_USERNAME/YOUR_REPO</url>
    </repository>
  </distributionManagement>
</project>
```

**Step 2.** Add authentication to `~/.m2/settings.xml`:

```xml
<settings>
  <servers>
    <server>
      <id>github</id>
      <username>YOUR_USERNAME</username>
      <password>YOUR_PAT_HERE</password>
    </server>
  </servers>
</settings>
```

**Step 3.** Publish:

```bash
mvn deploy
```

**Install from GitHub Packages:**

```xml
<dependency>
  <groupId>com.example</groupId>
  <artifactId>my-package</artifactId>
  <version>1.0.0</version>
</dependency>

<repositories>
  <repository>
    <id>github</id>
    <url>https://maven.pkg.github.com/YOUR_USERNAME/YOUR_REPO</url>
  </repository>
</repositories>
```

---

## Publishing a NuGet Package

**Step 1.** Add the GitHub Packages source:

```bash
dotnet nuget add source \
  --username YOUR_USERNAME \
  --password YOUR_PAT_HERE \
  --store-password-in-clear-text \
  --name github \
  "https://nuget.pkg.github.com/YOUR_USERNAME/index.json"
```

**Step 2.** Pack and publish:

```bash
dotnet pack --configuration Release
dotnet nuget push "bin/Release/*.nupkg" --source "github"
```

**Install from GitHub Packages:**

```bash
dotnet add package YOUR_PACKAGE_NAME --source "github"
```

---

## Managing Packages

**View your packages:**

Go to `github.com/YOUR_USERNAME` → **Packages** tab. Or at the organisation level: `github.com/orgs/YOUR_ORG/packages`.

Each package page shows:
- All published versions
- Download statistics
- Installation instructions
- Linked repository
- README (if provided)

**View packages for a repository:**

The repository homepage shows linked packages in the right sidebar under **Packages**.

---

## Package Visibility and Access Control

Package visibility is separate from repository visibility and can be configured independently.

**Visibility options:**

- **Public:** Anyone can pull the package without authentication
- **Private:** Only authenticated users with explicit access can pull
- **Internal (organisation):** All organisation members can access

**Changing visibility:**

Package → Package settings → Change visibility.

**Access control for container packages (GHCR):**

Container packages have fine-grained permissions separate from any repository:

Package → Package settings → Manage access:
- Add specific users or teams
- Set their role: Read, Write or Admin

**Inheriting repository permissions:**

Link a package to a repository to inherit its access settings. Anyone with repository access automatically gets package access.

Package → Package settings → Connect repository → select the repository.

---

## Linking Packages to Repositories

Linking connects a package to its source repository. Benefits:

- Package appears in the repository's right sidebar
- Repository collaborators get automatic package access
- Users can find the package by browsing the repository
- The package README can reference repository files

**Link via Docker label (container images):**

Add to your Dockerfile:

```dockerfile
LABEL org.opencontainers.image.source="https://github.com/YOUR_USERNAME/YOUR_REPO"
```

When you push the image, GHCR automatically links it to the repository.

**Link via package settings:**

Package → Package settings → Connect repository.

---

## Deleting and Restoring Packages

**Delete a specific package version:**

Package → Version → Delete this version.

**Delete an entire package:**

Package → Package settings → Delete this package.

> [!WARNING]
> Deleting a package version that others depend on will break their builds. For public packages especially, unpublishing a version should be a last resort. Consider deprecating the version instead if possible.

**Restore a deleted package (within 30 days):**

GitHub retains deleted packages for 30 days. Contact GitHub Support to restore within that window.

---

## Automating Package Publishing with GitHub Actions

Automating package publishing on every release is the recommended approach.

### Publish Docker image to GHCR on release

```yaml
name: Publish Docker Image

on:
  release:
    types: [published]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - uses: docker/setup-buildx-action@v3

      - uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - uses: docker/metadata-action@v5
        id: meta
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=raw,value=latest

      - uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

### Publish npm package on release

```yaml
name: Publish npm Package

on:
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          registry-url: 'https://npm.pkg.github.com'
          scope: '@YOUR_USERNAME'

      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Multi-platform Docker builds

Build for multiple architectures simultaneously (useful for Apple Silicon support):

```yaml
- uses: docker/setup-qemu-action@v3    # enables ARM emulation

- uses: docker/build-push-action@v6
  with:
    platforms: linux/amd64,linux/arm64
    push: true
    tags: ${{ steps.meta.outputs.tags }}
```

---

## Pricing and Storage Limits

### Free for public packages

All public packages on GitHub Packages are completely free - no storage or bandwidth limits.

### Private packages

| Plan | Free storage | Free data transfer |
|---|---|---|
| Free | 500 MB | 1 GB/month |
| Pro | 2 GB | 10 GB/month |
| Team | 2 GB | 10 GB/month |
| Enterprise | 50 GB | 100 GB/month |

Beyond the free tier, storage costs $0.008/GB/day and data transfer costs $0.50/GB.

**Newer billing model (2025+):**

GitHub has been moving to a consumption-based model for packages. Some accounts see storage billed as $0.25/GiB/month and bandwidth as $0.50/GiB. Check your billing settings for the current model on your account.

> [!TIP]
> Delete old package versions you no longer need to keep storage costs low. Use GitHub Actions to automatically delete versions older than a specified number of days. The `actions/delete-package-versions` action handles this.

---

## Try It Yourself

**Publishing a Docker image to GHCR**

**Step 1.** Create a simple Dockerfile in any repository:

```dockerfile
FROM nginx:alpine
LABEL org.opencontainers.image.source="https://github.com/YOUR_USERNAME/YOUR_REPO"
COPY index.html /usr/share/nginx/html/index.html
```

Create `index.html`:

```html
<!DOCTYPE html>
<html><body><h1>Hello from GHCR!</h1></body></html>
```

**Step 2.** Create a PAT with `write:packages` and `read:packages` scopes at Settings → Developer settings → Personal access tokens → Fine-grained tokens.

**Step 3.** Log in to GHCR:

**Windows (PowerShell):**

```powershell
$env:CR_PAT = "YOUR_PAT_HERE"
echo $env:CR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin
```

**Mac and Linux:**

```bash
export CR_PAT="YOUR_PAT_HERE"
echo $CR_PAT | docker login ghcr.io -u YOUR_USERNAME --password-stdin
```

**Step 4.** Build and push:

```bash
docker build -t ghcr.io/YOUR_USERNAME/hello-ghcr:latest .
docker push ghcr.io/YOUR_USERNAME/hello-ghcr:latest
```

**Step 5.** View the package:

Go to `github.com/YOUR_USERNAME` → Packages. Your `hello-ghcr` package should appear.

**Step 6.** Make it public:

Package → Package settings → Change visibility → Public.

**Step 7.** Pull it without authentication:

```bash
docker pull ghcr.io/YOUR_USERNAME/hello-ghcr:latest
```

**Step 8.** Run it:

```bash
docker run -p 8080:80 ghcr.io/YOUR_USERNAME/hello-ghcr:latest
```

Visit `http://localhost:8080` to see your page served from a container hosted on GHCR.

---

## Common Mistakes

**Using `docker.pkg.github.com` instead of `ghcr.io`.**

The old Docker registry at `docker.pkg.github.com` has been replaced by GHCR at `ghcr.io`. All existing images were automatically migrated and existing pull links to `docker.pkg.github.com` still work, but all new images should go to `ghcr.io`.

**Publishing packages without linking them to a repository.**

Unlinked packages are hard to find and do not appear in the repository sidebar. Always add the `org.opencontainers.image.source` label for Docker images or link manually in package settings.

**Not adding required labels to Docker images.**

The `org.opencontainers.image.source` label is what connects the image to its repository on GitHub. Without it, the package appears as an orphan under your profile rather than being associated with its repository.

**Committing authentication tokens.**

Never put PATs or passwords in Dockerfiles, `pom.xml`, `package.json`, `.npmrc` or any committed file. Use environment variables, GitHub Actions secrets or your operating system's credential manager.

**Not cleaning up old package versions.**

Package storage is not unlimited. Old versions of frequently built images accumulate rapidly. Set up an automated retention policy using GitHub Actions to delete versions older than a set number of days or beyond a maximum count.

**Forgetting `permissions: packages: write` in Actions workflows.**

The `GITHUB_TOKEN` does not have write access to Packages by default in newer repositories. Always add `permissions: packages: write` explicitly in workflows that publish packages.

---

## Summary

- GitHub Packages hosts npm, Maven, NuGet, RubyGems and Docker/OCI packages alongside source code
- The **GitHub Container Registry (GHCR)** at `ghcr.io` is the most widely used registry - supports Docker and OCI images
- Authentication uses `GITHUB_TOKEN` in GitHub Actions or a Personal Access Token locally
- Public packages are completely free; private packages use storage from the plan allowance
- Add `LABEL org.opencontainers.image.source` to Dockerfiles to link images to their repository
- Package visibility and access can be set independently from repository visibility
- Automate publishing with GitHub Actions on release events using `docker/build-push-action`, `softprops/action-gh-release` or the npm publish workflow
- Use `docker/metadata-action` to automatically generate semantic version tags from GitHub releases
- The old `docker.pkg.github.com` registry is retired - use `ghcr.io` for all new container images
- Delete old package versions regularly to manage storage costs

---

## Sources and Further Reading

- [GitHub Packages documentation](https://docs.github.com/en/packages) - complete official reference
- [Working with the Container registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry) - GHCR guide
- [Publishing Node.js packages](https://docs.github.com/en/actions/use-cases-and-examples/publishing-packages/publishing-nodejs-packages) - npm guide with Actions
- [Publishing Docker images](https://docs.github.com/en/actions/use-cases-and-examples/publishing-packages/publishing-docker-images) - Docker/GHCR Actions guide
- [docker/metadata-action](https://github.com/docker/metadata-action) - automatic Docker image tagging from GitHub metadata
- [docker/build-push-action](https://github.com/docker/build-push-action) - the standard Docker build and push action
- [actions/delete-package-versions](https://github.com/actions/delete-package-versions) - clean up old package versions automatically

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

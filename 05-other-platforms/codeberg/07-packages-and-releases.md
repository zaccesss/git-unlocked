# Packages and Releases on Codeberg

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Codeberg provides two mechanisms for distributing versioned software: the **package registry** (inherited from Forgejo, supporting 23+ package formats) and **releases** (versioned snapshots with binary asset attachments). This file covers both, with Codeberg-specific considerations around storage, authentication and CI integration with Woodpecker.

---

## Table of Contents

1. [The Codeberg package registry](#the-codeberg-package-registry)
2. [Container images (Docker/OCI)](#container-images-dockeroci)
3. [npm packages](#npm-packages)
4. [Python packages (PyPI)](#python-packages-pypi)
5. [Generic packages](#generic-packages)
6. [Publishing packages from Woodpecker CI](#publishing-packages-from-woodpecker-ci)
7. [Releases](#releases)
8. [Publishing release assets from CI](#publishing-release-assets-from-ci)
9. [Codeberg-specific considerations](#codeberg-specific-considerations)
10. [Try It Yourself](#try-it-yourself)
11. [Common Mistakes](#common-mistakes)
12. [Summary](#summary)
13. [Sources](#sources)

---

## The Codeberg package registry

Codeberg's package registry is Forgejo's package registry, supporting the same 23+ formats documented in [../forgejo/06-issues-packages-and-federation.md](../forgejo/06-issues-packages-and-federation.md). The URL pattern is:

```
https://codeberg.org/api/packages/{owner}/{type}/
```

Where `{owner}` is your Codeberg username or organisation name and `{type}` is the package format (npm, pypi, nuget, maven, cargo, etc.).

All packages require authentication for publishing. Consuming packages from a public namespace may or may not require authentication depending on the package type.

Use an access token with `package:write` scope for publishing and `package:read` for downloading.

---

## Container images (Docker/OCI)

Codeberg's container registry serves Docker and OCI images at `codeberg.org/{owner}/{repo}`.

### Logging in

```bash
docker login codeberg.org \
  --username your-username \
  --password YOUR_ACCESS_TOKEN
```

### Building and pushing

```bash
# Build
docker build -t codeberg.org/your-username/my-app:1.0.0 .

# Push
docker push codeberg.org/your-username/my-app:1.0.0

# Also tag as latest
docker tag codeberg.org/your-username/my-app:1.0.0 \
           codeberg.org/your-username/my-app:latest
docker push codeberg.org/your-username/my-app:latest
```

### Pulling an image

```bash
docker pull codeberg.org/your-username/my-app:1.0.0
```

### Using in Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
        - image: codeberg.org/username/my-app:1.0.0
      imagePullSecrets:
        - name: codeberg-registry-secret
```

Create the pull secret:
```bash
kubectl create secret docker-registry codeberg-registry-secret \
  --docker-server=codeberg.org \
  --docker-username=your-username \
  --docker-password=YOUR_TOKEN
```

---

## npm packages

### Configuring npm

Add to your project's `.npmrc`:

```
@your-scope:registry=https://codeberg.org/api/packages/your-username/npm/
//codeberg.org/api/packages/your-username/npm/:_authToken=YOUR_ACCESS_TOKEN
```

### Publishing

In `package.json`:
```json
{
  "name": "@your-scope/my-package",
  "version": "1.0.0",
  "publishConfig": {
    "@your-scope:registry": "https://codeberg.org/api/packages/your-username/npm/"
  }
}
```

```bash
npm publish
```

### Consuming

With the `.npmrc` configured:
```bash
npm install @your-scope/my-package
```

---

## Python packages (PyPI)

### Publishing with twine

```bash
pip install build twine
python -m build

twine upload \
  --repository-url https://codeberg.org/api/packages/your-username/pypi \
  --username your-username \
  --password YOUR_ACCESS_TOKEN \
  dist/*
```

### Consuming with pip

```bash
pip install \
  --index-url https://your-username:YOUR_TOKEN@codeberg.org/api/packages/your-username/pypi/simple/ \
  your-package
```

Or add to `pip.conf`:
```ini
[global]
index-url = https://your-username:YOUR_TOKEN@codeberg.org/api/packages/your-username/pypi/simple/
```

---

## Generic packages

Generic packages store arbitrary versioned files - compiled binaries, configuration bundles, datasets.

### Publishing

```bash
curl -X PUT \
  -H "Authorization: token YOUR_TOKEN" \
  --upload-file my-tool-v1.2.3-linux-amd64 \
  "https://codeberg.org/api/packages/your-username/generic/my-tool/1.2.3/my-tool-linux-amd64"
```

Multiple files per version:
```bash
for binary in dist/*; do
  filename=$(basename "$binary")
  curl -X PUT \
    -H "Authorization: token YOUR_TOKEN" \
    --upload-file "$binary" \
    "https://codeberg.org/api/packages/your-username/generic/my-tool/1.2.3/$filename"
done
```

### Downloading

```bash
curl -L \
  -H "Authorization: token YOUR_TOKEN" \
  "https://codeberg.org/api/packages/your-username/generic/my-tool/1.2.3/my-tool-linux-amd64" \
  -o my-tool
chmod +x my-tool
```

---

## Publishing packages from Woodpecker CI

### Docker build and push

```yaml
steps:
  - name: build-and-push
    image: woodpeckerci/plugin-docker-buildx
    settings:
      repo: codeberg.org/your-username/my-app
      registry: codeberg.org
      username:
        from_secret: CODEBERG_USERNAME
      password:
        from_secret: CODEBERG_TOKEN
      platforms:
        - linux/amd64
        - linux/arm64
      tags:
        - latest
        - ${CI_COMMIT_TAG}
    when:
      event: tag
```

### Publish Python package

```yaml
steps:
  - name: build
    image: python:3.12-slim
    commands:
      - pip install build
      - python -m build

  - name: publish
    image: python:3.12-slim
    environment:
      CODEBERG_TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - pip install twine
      - twine upload
          --repository-url https://codeberg.org/api/packages/your-username/pypi
          --username your-username
          --password ${CODEBERG_TOKEN}
          dist/*
    when:
      event: tag
```

### Publish npm package

```yaml
steps:
  - name: test
    image: node:20-alpine
    commands:
      - npm ci && npm test

  - name: publish
    image: node:20-alpine
    environment:
      NODE_AUTH_TOKEN:
        from_secret: CODEBERG_TOKEN
    commands:
      - |
        cat > .npmrc << EOF
        @your-scope:registry=https://codeberg.org/api/packages/your-username/npm/
        //codeberg.org/api/packages/your-username/npm/:_authToken=${NODE_AUTH_TOKEN}
        EOF
      - npm publish
    when:
      event: tag
```

---

## Releases

Releases provide versioned software distributions with binary assets, attached to a Git tag. They appear in the repository's Releases page and are accessible via the API.

### Creating a release via the web

1. Go to repository > **Releases** > **Draft a new release**
2. Set:
   - **Tag**: create a new tag (e.g. `v1.2.0`) or select an existing one
   - **Target**: the branch or commit to tag
   - **Title**: human-readable release name (e.g. "v1.2.0 - Performance improvements")
   - **Description**: changelog in Markdown
   - **Attachments**: upload binary files, installers, checksums
3. Choose **Pre-release** if this is a release candidate
4. Click **Publish release**

### Release description best practices

A good release description includes:
- **What changed**: list of features, fixes and breaking changes
- **Migration notes**: if upgrading requires user action
- **Download instructions**: which file to download for which platform
- **Checksum verification**: SHA256 checksums for the distributed files

Example:
```markdown
## What is new

### Features
- Added support for ARM64 binaries
- New `--config` flag for specifying a configuration file

### Bug fixes
- Fixed crash on empty input (#42)
- Corrected help text for `--output` flag (#45)

## Downloads

| Platform | File | SHA256 |
|---|---|---|
| Linux amd64 | `myapp-v1.2.0-linux-amd64` | `abc123...` |
| Linux arm64 | `myapp-v1.2.0-linux-arm64` | `def456...` |
| macOS arm64 | `myapp-v1.2.0-darwin-arm64` | `ghi789...` |

## Upgrading

No breaking changes in this release. Update by replacing the binary.
```

### Creating a release via API

```bash
curl -X POST \
  -H "Authorization: token YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "tag_name": "v1.2.0",
    "name": "v1.2.0 - Performance improvements",
    "body": "### Changes\n\n- Improved startup time by 40%\n- Fixed memory leak",
    "draft": false,
    "prerelease": false
  }' \
  "https://codeberg.org/api/v1/repos/username/repo/releases"
```

---

## Publishing release assets from CI

Automatically build and attach binaries to releases using Woodpecker CI.

### Go multi-platform release pipeline

```yaml
steps:
  - name: test
    image: golang:1.22-alpine
    commands:
      - go test ./...

  - name: build-binaries
    image: golang:1.22
    commands:
      - |
        VERSION=${CI_COMMIT_TAG}
        for os in linux darwin; do
          for arch in amd64 arm64; do
            GOOS=$os GOARCH=$arch go build \
              -ldflags "-X main.Version=${VERSION}" \
              -o dist/myapp-${VERSION}-${os}-${arch} .
          done
        done
        # Checksums
        cd dist && sha256sum * > SHA256SUMS
    when:
      event: tag

  - name: publish-release
    image: woodpeckerci/plugin-gitea-release
    settings:
      url: https://codeberg.org
      api_key:
        from_secret: CODEBERG_TOKEN
      title: "Release ${CI_COMMIT_TAG}"
      note: "Automated release for ${CI_COMMIT_TAG}"
      files:
        - dist/*
    when:
      event: tag
```

### Upload assets to an existing release

```yaml
- name: upload-assets
  image: alpine
  environment:
    TOKEN:
      from_secret: CODEBERG_TOKEN
  commands:
    - apk add --no-cache curl jq
    # Get the release ID for the current tag
    - |
      RELEASE_ID=$(curl -s \
        -H "Authorization: token ${TOKEN}" \
        "https://codeberg.org/api/v1/repos/username/repo/releases/tags/${CI_COMMIT_TAG}" \
        | jq -r .id)
    # Upload each binary
    - |
      for file in dist/*; do
        filename=$(basename "$file")
        curl -X POST \
          -H "Authorization: token ${TOKEN}" \
          -H "Content-Type: application/octet-stream" \
          -T "$file" \
          "https://codeberg.org/api/v1/repos/username/repo/releases/${RELEASE_ID}/assets?name=${filename}"
      done
  when:
    event: tag
```

---

## Codeberg-specific considerations

### Storage and fair use

Codeberg operates on fair use principles. The package registry and release assets are stored on Codeberg's infrastructure funded by community donations. Consider:

- **Remove old pre-release versions** when final releases are published
- **Do not use the package registry as a general file hosting service** for non-package artifacts
- **Large binary distributions** (hundreds of MiB per release) across many releases accumulate significant storage. Evaluate whether all platforms and variants are necessary.

### Package registry vs releases for binaries

**Use the package registry** for installable packages (npm packages, Python wheels, Go modules, Docker images). These integrate with package managers.

**Use releases** for end-user binary distributions (compiled executables, installers, archives). These are for people downloading and running software directly.

The two mechanisms serve different audiences and use cases. A project might publish both: a Docker image to the container registry and a binary installer as a release asset.

### Authentication requirements

All package publishing requires authentication. Read access to packages in public namespaces is typically allowed without authentication for most package types.

Check the Forgejo package documentation for specific authentication requirements per format, as some (Cargo, Go modules) have specific configuration requirements.

---

## Try It Yourself

**Exercise 1 - Create a release**

1. In your `hello-codeberg` repository, go to **Releases** > **Draft a new release**
2. Create a new tag `v0.1.0` on the main branch
3. Title: "v0.1.0 - Initial release"
4. Description: "First public release of hello-codeberg"
5. Upload any small file as a release asset (even a text file)
6. Publish the release

**Exercise 2 - Push a Docker image**

```bash
# Create a minimal Dockerfile
cat > Dockerfile << 'EOF'
FROM alpine:latest
CMD ["echo", "Hello from Codeberg registry!"]
EOF

# Build and push
docker login codeberg.org -u your-username -p YOUR_TOKEN
docker build -t codeberg.org/your-username/hello-codeberg:0.1.0 .
docker push codeberg.org/your-username/hello-codeberg:0.1.0

# Pull and run
docker pull codeberg.org/your-username/hello-codeberg:0.1.0
docker run --rm codeberg.org/your-username/hello-codeberg:0.1.0
```

Check your Codeberg profile > **Packages** to see the image listed.

**Exercise 3 - Upload a generic package**

```bash
echo '#!/bin/sh\necho "myapp v1.0.0"' > myapp.sh
chmod +x myapp.sh

curl -X PUT \
  -H "Authorization: token YOUR_TOKEN" \
  --upload-file myapp.sh \
  "https://codeberg.org/api/packages/your-username/generic/myapp/1.0.0/myapp.sh"
```

Visit `https://codeberg.org/your-username/-/packages` to see the package listed.

---

## Common Mistakes

**Using the repository name instead of the owner name in the package URL**

The package registry URL is `/api/packages/{owner}/...` where `{owner}` is your username or organisation name, not the repository name. Package namespaces belong to users and organisations, not individual repositories.

**Publishing without `package:write` scope**

Creating an access token without the `package:write` scope results in 403 errors when publishing. Always check token scopes before debugging publishing failures.

**Publishing massive release assets without considering storage**

Codeberg runs on community donations. Releasing 500 MB binaries for 10 platforms across 50 versions accumulates 250 GiB of storage on a donation-funded service. Be considerate. Publish the formats your users actually need, delete pre-release assets when superseded and remove very old releases.

**Not verifying releases with checksums**

Release assets without checksums cannot be verified for integrity. Always generate and publish SHA256SUMS (or equivalent) with every release. Users and automated installers need this for secure downloads.

---

## Summary

Codeberg's package registry supports 23+ formats through Forgejo's package system. The registry URL is `https://codeberg.org/api/packages/{owner}/{type}/`. Authenticate with an access token with appropriate scopes.

Container images live at `codeberg.org/{owner}/{repo}` and are pushed with standard Docker commands. npm, PyPI, NuGet, Maven, Cargo and other formats follow the standard toolchain configuration with the Codeberg registry URL substituted.

Releases attach versioned binary assets to Git tags. Create them via the web UI or API. Woodpecker CI can automate release creation and asset upload when tags are pushed.

Use the package registry for distributable packages (Docker images, npm modules, Python packages). Use releases for end-user binaries and distributions. Both are storage resources on a donation-funded platform - be considerate about what and how long you store.

---

## Sources

- [Forgejo: Package registry documentation](https://forgejo.org/docs/latest/user/packages/)
- [Forgejo: Container registry](https://forgejo.org/docs/latest/user/packages/container/)
- [Codeberg Pages: Community wiki](https://codeberg.org/Codeberg/Community/wiki)
- [Woodpecker CI: Plugin registry](https://woodpecker-ci.org/plugins)
- [Woodpecker CI: Gitea release plugin](https://woodpecker-ci.org/plugins/Gitea%20Release)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

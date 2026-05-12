# Gitea Package Registry

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Gitea includes a built-in package registry that supports 23 package formats - from npm and PyPI to Docker images and Cargo crates. Teams can publish and consume internal packages directly from their Gitea instance without running separate registry infrastructure. This file covers every supported package type with practical publish and consume examples.

---

## Table of Contents

1. [How the package registry works](#how-the-package-registry-works)
2. [Enabling the package registry](#enabling-the-package-registry)
3. [Container / Docker images (OCI)](#container--docker-images-oci)
4. [npm packages](#npm-packages)
5. [Python packages (PyPI)](#python-packages-pypi)
6. [NuGet (.NET)](#nuget-net)
7. [Maven (Java)](#maven-java)
8. [Cargo (Rust)](#cargo-rust)
9. [Go modules](#go-modules)
10. [Debian packages](#debian-packages)
11. [Alpine packages](#alpine-packages)
12. [RPM packages](#rpm-packages)
13. [Helm charts](#helm-charts)
14. [RubyGems](#rubygems)
15. [Generic packages](#generic-packages)
16. [Other supported formats](#other-supported-formats)
17. [Using packages in Gitea Actions](#using-packages-in-gitea-actions)
18. [Package management and retention](#package-management-and-retention)
19. [Try It Yourself](#try-it-yourself)
20. [Common Mistakes](#common-mistakes)
21. [Summary](#summary)
22. [Sources](#sources)

---

## How the package registry works

Every user and organisation in Gitea has a package namespace. Packages published to a user namespace are at `gitea.example.com/{username}/packages`. Organisation packages are at `gitea.example.com/{org}/packages`.

The registry is package-manager native: you configure your package manager (npm, pip, cargo, etc.) to use your Gitea instance as a registry, then use the package manager's normal commands to publish and consume. There is no separate web UI workflow - the package manager talks to Gitea directly.

All packages require authentication for publishing. Consuming packages from a public namespace may or may not require authentication depending on the package type and the instance configuration.

Authentication uses standard access tokens. Use a token with `package:read` scope for consuming and `package:write` scope for publishing.

---

## Enabling the package registry

The package registry is enabled by default since Gitea v1.17. Verify in `app.ini`:

```ini
[packages]
ENABLED = true
```

Administrators can set storage configuration:

```ini
[packages]
ENABLED           = true
CHUNKED_UPLOAD_PATH = temp/package-uploads
MAX_BLOB_SIZE     = 104857600   ; 100 MiB max per blob
```

For production, configure storage to use a dedicated disk partition or object storage (S3, MinIO) to prevent packages from filling the system disk:

```ini
[storage.packages]
STORAGE_TYPE = minio
MINIO_ENDPOINT = minio.example.com:9000
MINIO_ACCESS_KEY_ID = minioaccess
MINIO_SECRET_ACCESS_KEY = miniosecret
MINIO_BUCKET = gitea-packages
MINIO_USE_SSL = true
```

---

## Container / Docker images (OCI)

Gitea's container registry is OCI-compliant. It stores Docker images and any OCI artefact (Helm charts, WASM modules, etc. stored as OCI artefacts).

### Logging in

```bash
docker login gitea.example.com \
  --username your-username \
  --password YOUR_ACCESS_TOKEN
```

### Pushing an image

```bash
# Build an image
docker build -t gitea.example.com/your-username/my-app:1.0.0 .

# Push to Gitea
docker push gitea.example.com/your-username/my-app:1.0.0

# Also tag as latest and push
docker tag gitea.example.com/your-username/my-app:1.0.0 \
           gitea.example.com/your-username/my-app:latest
docker push gitea.example.com/your-username/my-app:latest
```

For an organisation:
```bash
docker build -t gitea.example.com/my-org/my-app:1.0.0 .
docker push gitea.example.com/my-org/my-app:1.0.0
```

### Pulling an image

```bash
docker pull gitea.example.com/your-username/my-app:1.0.0
```

### Using in Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
        - image: gitea.example.com/my-org/my-app:1.0.0
      imagePullSecrets:
        - name: gitea-registry-secret
```

Create the pull secret:
```bash
kubectl create secret docker-registry gitea-registry-secret \
  --docker-server=gitea.example.com \
  --docker-username=deploy-user \
  --docker-password=YOUR_TOKEN
```

---

## npm packages

### Configuring npm

Create or update `.npmrc` in your project root:

```
@your-scope:registry=https://gitea.example.com/api/packages/your-username/npm/
//gitea.example.com/api/packages/your-username/npm/:_authToken=YOUR_ACCESS_TOKEN
```

For an organisation:
```
@org-scope:registry=https://gitea.example.com/api/packages/your-org/npm/
```

### Publishing

In `package.json`, add the scope and publishConfig:

```json
{
  "name": "@your-scope/my-package",
  "version": "1.0.0",
  "publishConfig": {
    "@your-scope:registry": "https://gitea.example.com/api/packages/your-username/npm/"
  }
}
```

```bash
npm publish
```

### Consuming

```bash
npm install @your-scope/my-package
```

With the `.npmrc` registry configured, npm fetches from your Gitea instance automatically for that scope.

---

## Python packages (PyPI)

### Publishing with twine

```bash
pip install build twine

# Build
python -m build

# Upload to Gitea PyPI registry
twine upload \
  --repository-url https://gitea.example.com/api/packages/your-username/pypi \
  --username your-username \
  --password YOUR_ACCESS_TOKEN \
  dist/*
```

### Consuming with pip

```bash
pip install \
  --index-url https://your-username:YOUR_TOKEN@gitea.example.com/api/packages/your-username/pypi/simple/ \
  your-package-name
```

Configure permanently in `pip.conf`:

🍎 **Mac / 🐧 Linux** (`~/.pip/pip.conf`):
```ini
[global]
index-url = https://your-username:YOUR_TOKEN@gitea.example.com/api/packages/your-username/pypi/simple/
```

🪟 **Windows** (`%APPDATA%\pip\pip.ini`):
```ini
[global]
index-url = https://your-username:YOUR_TOKEN@gitea.example.com/api/packages/your-username/pypi/simple/
```

### Using `pyproject.toml` with hatch or poetry

**Poetry:**
```toml
[[tool.poetry.source]]
name = "gitea"
url = "https://gitea.example.com/api/packages/your-username/pypi/simple/"
priority = "primary"
```

```bash
poetry config http-basic.gitea your-username YOUR_TOKEN
```

---

## NuGet (.NET)

### Publishing

```bash
# Authenticate
dotnet nuget add source \
  --name gitea \
  --username your-username \
  --password YOUR_ACCESS_TOKEN \
  --store-password-in-clear-text \
  https://gitea.example.com/api/packages/your-username/nuget/index.json

# Pack
dotnet pack --configuration Release

# Push
dotnet nuget push bin/Release/*.nupkg \
  --source gitea \
  --api-key YOUR_ACCESS_TOKEN
```

### Consuming

In `nuget.config`:
```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <add key="gitea" value="https://gitea.example.com/api/packages/your-username/nuget/index.json" />
  </packageSources>
  <packageSourceCredentials>
    <gitea>
      <add key="Username" value="your-username" />
      <add key="ClearTextPassword" value="YOUR_ACCESS_TOKEN" />
    </gitea>
  </packageSourceCredentials>
</configuration>
```

Then restore normally:
```bash
dotnet restore
```

---

## Maven (Java)

### Configuring Maven

Add the Gitea repository to `pom.xml`:

```xml
<repositories>
  <repository>
    <id>gitea</id>
    <url>https://gitea.example.com/api/packages/your-username/maven</url>
  </repository>
</repositories>

<distributionManagement>
  <repository>
    <id>gitea</id>
    <url>https://gitea.example.com/api/packages/your-username/maven</url>
  </repository>
</distributionManagement>
```

Add credentials to `~/.m2/settings.xml`:
```xml
<settings>
  <servers>
    <server>
      <id>gitea</id>
      <username>your-username</username>
      <password>YOUR_ACCESS_TOKEN</password>
    </server>
  </servers>
</settings>
```

### Publishing

```bash
mvn deploy
```

### Consuming

```bash
mvn dependency:get \
  -Dartifact=com.example:my-library:1.0.0
```

### Gradle

`build.gradle`:
```groovy
repositories {
    maven {
        url "https://gitea.example.com/api/packages/your-username/maven"
        credentials {
            username "your-username"
            password "YOUR_ACCESS_TOKEN"
        }
    }
}
```

---

## Cargo (Rust)

### Configuring Cargo

Add to `.cargo/config.toml`:

```toml
[registries]
gitea = { index = "sparse+https://gitea.example.com/api/packages/your-username/cargo/index/" }
```

Add credentials to `~/.cargo/credentials.toml`:
```toml
[registries.gitea]
token = "Bearer YOUR_ACCESS_TOKEN"
```

### Publishing

```bash
cargo publish --registry gitea
```

### Consuming

In `Cargo.toml`:
```toml
[dependencies]
my-crate = { version = "1.0", registry = "gitea" }
```

---

## Go modules

Gitea provides a Go module proxy for hosting private Go modules.

### Configuring Go

```bash
# Set the Gitea instance as the module proxy
go env -w GOPROXY="https://gitea.example.com/api/packages/your-username/go,direct"
go env -w GONOSUMCHECK="gitea.example.com/*"
go env -w GONOSUMDB="gitea.example.com/*"
```

### Publishing

Go modules are published via the API:

```bash
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  "https://gitea.example.com/api/packages/your-username/go/upload" \
  --upload-file my-module-v1.0.0.zip
```

For standard module releases, set up a Gitea Actions workflow that creates the module archive from a tagged release.

### Consuming

```bash
go get gitea.example.com/your-username/my-module@v1.0.0
```

---

## Debian packages

### Publishing

```bash
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-package_1.0.0_amd64.deb \
  "https://gitea.example.com/api/packages/your-username/debian/pool/bookworm/main/upload"
```

The URL includes the distribution (`bookworm`) and component (`main`).

### Consuming

```bash
# Add the repository
echo "deb https://your-username:YOUR_TOKEN@gitea.example.com/api/packages/your-username/debian bookworm main" \
  | sudo tee /etc/apt/sources.list.d/gitea.list

# Add the GPG key (if configured)
curl -fsSL "https://gitea.example.com/api/packages/your-username/debian/repository.key" \
  | sudo gpg --dearmor -o /etc/apt/keyrings/gitea.gpg

# Update and install
sudo apt update
sudo apt install my-package
```

---

## Alpine packages

### Publishing

```bash
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-package-1.0.0-r0.apk \
  "https://gitea.example.com/api/packages/your-username/alpine/v3.19/main/x86_64/upload"
```

### Consuming

```bash
# Add the repository
echo "https://gitea.example.com/api/packages/your-username/alpine/v3.19/main" \
  >> /etc/apk/repositories

# Add the key
curl -fsSL "https://gitea.example.com/api/packages/your-username/alpine/key" \
  -o /etc/apk/keys/gitea.rsa.pub

apk update
apk add my-package
```

---

## RPM packages

### Publishing

```bash
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-package-1.0.0.x86_64.rpm \
  "https://gitea.example.com/api/packages/your-username/rpm/upload"
```

### Consuming

```bash
# Add the repository
cat > /etc/yum.repos.d/gitea.repo << EOF
[gitea]
name=Gitea Packages
baseurl=https://your-username:YOUR_TOKEN@gitea.example.com/api/packages/your-username/rpm
enabled=1
gpgcheck=0
EOF

dnf install my-package
```

---

## Helm charts

Gitea can store and serve Helm charts as OCI artefacts or via the native Helm registry API.

### Publishing via OCI

```bash
helm registry login gitea.example.com \
  --username your-username \
  --password YOUR_ACCESS_TOKEN

helm push my-chart-1.0.0.tgz oci://gitea.example.com/your-username
```

### Consuming via OCI

```bash
helm install my-release oci://gitea.example.com/your-username/my-chart --version 1.0.0
```

### Publishing via Helm registry API

```bash
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-chart-1.0.0.tgz \
  "https://gitea.example.com/api/packages/your-username/helm/upload"
```

### Consuming via Helm repository

```bash
helm repo add gitea \
  "https://gitea.example.com/api/packages/your-username/helm" \
  --username your-username \
  --password YOUR_ACCESS_TOKEN

helm install my-release gitea/my-chart
```

---

## RubyGems

### Publishing

```bash
# Configure credentials
curl -u "your-username:YOUR_ACCESS_TOKEN" \
  https://gitea.example.com/api/packages/your-username/rubygems/api/v1/api_key.yaml \
  > ~/.gem/credentials
chmod 0600 ~/.gem/credentials

# Push gem
gem push --host https://gitea.example.com/api/packages/your-username/rubygems \
  my-gem-1.0.0.gem
```

### Consuming

In `Gemfile`:
```ruby
source "https://your-username:YOUR_TOKEN@gitea.example.com/api/packages/your-username/rubygems"

gem "my-gem"
```

---

## Generic packages

Generic packages store arbitrary versioned files - compiled executables, configuration bundles, datasets, anything that needs versioning but does not fit a specific package format.

### Publishing

```bash
# Upload a generic package
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-tool-v1.2.3-linux-amd64 \
  "https://gitea.example.com/api/packages/your-username/generic/my-tool/1.2.3/my-tool-linux-amd64"
```

The URL structure is: `generic/{package-name}/{version}/{filename}`

Multiple files can belong to one package version:
```bash
curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-tool-v1.2.3-windows-amd64.exe \
  "https://gitea.example.com/api/packages/your-username/generic/my-tool/1.2.3/my-tool-windows-amd64.exe"

curl -X PUT \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  --upload-file my-tool-v1.2.3-darwin-arm64 \
  "https://gitea.example.com/api/packages/your-username/generic/my-tool/1.2.3/my-tool-darwin-arm64"
```

### Consuming

```bash
curl -L \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  "https://gitea.example.com/api/packages/your-username/generic/my-tool/1.2.3/my-tool-linux-amd64" \
  -o my-tool
chmod +x my-tool
```

---

## Other supported formats

A complete list of formats supported as of Gitea v1.25:

| Format | Use case |
|---|---|
| Alpine | Alpine Linux packages |
| Arch | Arch Linux packages |
| Cargo | Rust crates |
| Chef | Chef cookbooks |
| Composer | PHP packages |
| Conan | C/C++ packages |
| Conda | Python/data science environments |
| Container/OCI | Docker images and OCI artefacts |
| CRAN | R packages |
| Debian | .deb packages |
| Generic | Any versioned file |
| Go modules | Go packages |
| Helm | Kubernetes charts |
| Maven | Java packages |
| npm | JavaScript packages |
| NuGet | .NET packages |
| Pub | Dart/Flutter packages |
| PyPI | Python packages |
| RPM | Red Hat / Fedora packages |
| RubyGems | Ruby packages |
| Swift | Swift packages |
| Vagrant | VM boxes |

---

## Using packages in Gitea Actions

### Docker image in Actions

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Log in to Gitea Registry
        uses: docker/login-action@v3
        with:
          registry: gitea.example.com
          username: ${{ gitea.actor }}
          password: ${{ secrets.GITEA_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: gitea.example.com/${{ github.repository }}:${{ github.sha }}
```

### Publishing npm from a workflow

```yaml
jobs:
  publish:
    runs-on: ubuntu-latest
    if: startsWith(github.ref, 'refs/tags/v')
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Publish to Gitea npm registry
        run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITEA_TOKEN }}
```

---

## Package management and retention

### Viewing packages

Go to your user profile or organisation page > **Packages** to see all packages with their versions, download counts and sizes.

### Deleting package versions

From the package web page:
1. Click the package name
2. Find the version to delete
3. Click **Delete Version**

Via the API:
```bash
curl -X DELETE \
  -H "Authorization: token YOUR_ACCESS_TOKEN" \
  "https://gitea.example.com/api/packages/your-username/{type}/{name}/{version}"
```

### Storage considerations

Package storage accumulates over time. Regularly delete:
- Old pre-release or snapshot versions
- Versions older than your retention policy
- Test/throwaway packages

Gitea does not have built-in automatic retention policies (unlike some commercial registries). Build a cleanup workflow with the API if automated retention is needed.

---

## Try It Yourself

**Exercise 1 - Docker push and pull**

```bash
# Create a minimal Dockerfile
cat > Dockerfile << 'EOF'
FROM alpine:latest
CMD ["echo", "Hello from Gitea registry!"]
EOF

# Build and push
docker build -t gitea.example.com/your-username/hello-gitea:1.0.0 .
docker login gitea.example.com -u your-username -p YOUR_TOKEN
docker push gitea.example.com/your-username/hello-gitea:1.0.0

# Pull and run
docker pull gitea.example.com/your-username/hello-gitea:1.0.0
docker run --rm gitea.example.com/your-username/hello-gitea:1.0.0
```

**Exercise 2 - Upload a generic package**

```bash
# Create a test file to package
echo '#!/bin/bash\necho "My tool v1.0.0"' > my-tool.sh
chmod +x my-tool.sh

# Upload as a generic package
curl -X PUT \
  -H "Authorization: token YOUR_TOKEN" \
  --upload-file my-tool.sh \
  "https://gitea.example.com/api/packages/your-username/generic/my-tool/1.0.0/my-tool.sh"

# Download it back
curl -L \
  -H "Authorization: token YOUR_TOKEN" \
  "https://gitea.example.com/api/packages/your-username/generic/my-tool/1.0.0/my-tool.sh" \
  -o downloaded-tool.sh
```

Check the **Packages** tab on your Gitea profile to see the uploaded package.

---

## Common Mistakes

**Publishing to a user namespace when using an organisation token**

Packages published to `gitea.example.com/api/packages/username/...` are in the user namespace. For organisation packages, use `gitea.example.com/api/packages/org-name/...`. Using the wrong namespace means packages appear in the wrong place and access control does not work as expected.

**Using an access token without the `package:write` scope for publishing**

Creating a token without selecting the `package:write` scope results in 403 errors when publishing. Always check token scopes before debugging package publishing failures.

**Not handling Docker credentials for private images in Kubernetes**

Pulling private images from Gitea in Kubernetes requires an `imagePullSecret`. Without it, pods fail to start with `ImagePullBackOff`. Create the secret and reference it in your deployment spec.

**Filling disk space with unmanaged packages**

Without a retention policy, packages accumulate indefinitely. A Docker registry in particular can grow rapidly. Set up periodic cleanup using the Gitea API to delete old versions, or monitor disk usage and set alerts.

---

## Summary

Gitea's built-in package registry supports 23 formats, from Docker/OCI to npm, PyPI, NuGet, Maven, Cargo and generic files. Configure your package manager to use the Gitea registry URL, authenticate with an access token and use normal package manager commands to publish and consume.

All package types use the same authentication model: access tokens with appropriate scopes. Publishing requires `package:write`; consuming public packages may require `package:read` or no authentication depending on the instance configuration.

Generic packages handle arbitrary versioned files - useful for binary distributions, tooling bundles and anything that does not fit a specific format.

Gitea Actions workflows can publish packages as part of the build pipeline. Store registry credentials as Gitea Actions secrets.

---

## Sources

- [Gitea: Package registry documentation](https://docs.gitea.com/usage/packages/overview)
- [Gitea: Container registry](https://docs.gitea.com/usage/packages/container)
- [Gitea: npm packages](https://docs.gitea.com/usage/packages/npm)
- [Gitea: PyPI packages](https://docs.gitea.com/usage/packages/pypi)
- [Gitea: NuGet packages](https://docs.gitea.com/usage/packages/nuget)
- [Gitea: Cargo packages](https://docs.gitea.com/usage/packages/cargo)
- [Gitea: Generic packages](https://docs.gitea.com/usage/packages/generic)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

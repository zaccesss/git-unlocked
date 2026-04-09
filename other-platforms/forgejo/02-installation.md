# Installing Forgejo

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

Forgejo installs as a single binary or via Docker. The process is nearly identical to Gitea - the same app.ini configuration format, the same work directory structure, the same installation wizard. The key differences are the binary download location, the Docker image name and the addition of SAML configuration options. This file covers binary and Docker installation, the initial setup wizard, core app.ini settings and setting up the Forgejo runner for CI/CD.

> [!IMPORTANT]
> Forgejo does not support Windows. Install on 🐧 Linux or 🍎 macOS only.

---

## Table of Contents

1. [Choosing an installation method](#choosing-an-installation-method)
2. [System requirements](#system-requirements)
3. [Installation: binary](#installation-binary)
4. [Installation: Docker](#installation-docker)
5. [The installation wizard](#the-installation-wizard)
6. [The app.ini configuration file](#the-appini-configuration-file)
7. [Database configuration](#database-configuration)
8. [Reverse proxy and HTTPS](#reverse-proxy-and-https)
9. [Running as a systemd service](#running-as-a-systemd-service)
10. [Setting up the Forgejo runner](#setting-up-the-forgejo-runner)
11. [Upgrading Forgejo](#upgrading-forgejo)
12. [Try It Yourself](#try-it-yourself)
13. [Common Mistakes](#common-mistakes)
14. [Summary](#summary)
15. [Sources](#sources)

---

## Choosing an installation method

**Binary** is best for production: a single executable, no container runtime dependency, straightforward upgrade process.

**Docker** is best for quick evaluation, development environments or teams that standardise on containers. Docker Compose makes multi-service setups simple.

Both produce identical running applications. Choose based on your operational preferences.

---

## System requirements

| Use case | CPU | RAM | Notes |
|---|---|---|---|
| Personal / home lab | 1 core | 256 MB | SQLite works fine |
| Small team (up to 20 users) | 1-2 cores | 512 MB | PostgreSQL recommended |
| Medium team (20-100 users) | 2-4 cores | 2 GB | SSD strongly recommended |
| Larger deployment | 4+ cores | 4+ GB | Separate DB server |

**Supported operating systems:**
- 🐧 Linux (x86_64, ARM64, ARMv6/v7)
- 🍎 macOS (x86_64, Apple Silicon)
- Windows: **not supported**

**Supported databases:**
- SQLite 3 (built-in, personal use only)
- PostgreSQL 12 or later (recommended for production)
- MySQL 8.0 or later

---

## Installation: binary

### 🐧 Linux

Download the latest release from Forgejo's own Forgejo instance:

```bash
# Check the latest version at codeberg.org/forgejo/forgejo/releases
FORGEJO_VERSION="9.0.1"   # replace with current version

# Download binary for Linux amd64
wget -O forgejo \
  "https://codeberg.org/forgejo/forgejo/releases/download/v${FORGEJO_VERSION}/forgejo-${FORGEJO_VERSION}-linux-amd64"

chmod +x forgejo

# Verify
./forgejo --version
```

For ARM64 (Raspberry Pi 4/5, Apple Silicon Linux VMs):
```bash
wget -O forgejo \
  "https://codeberg.org/forgejo/forgejo/releases/download/v${FORGEJO_VERSION}/forgejo-${FORGEJO_VERSION}-linux-arm64"
```

**Set up the service account and directories:**

```bash
# Create a git user
sudo adduser \
  --system \
  --shell /bin/bash \
  --gecos 'Git Version Control' \
  --group \
  --disabled-password \
  --home /home/git \
  git

# Create directories
sudo mkdir -p /var/lib/forgejo/{custom,data,log}
sudo chown -R git:git /var/lib/forgejo/
sudo chmod -R 750 /var/lib/forgejo/
sudo mkdir /etc/forgejo
sudo chown root:git /etc/forgejo
sudo chmod 770 /etc/forgejo

# Install binary
sudo cp forgejo /usr/local/bin/forgejo
```

### 🍎 macOS

```bash
FORGEJO_VERSION="9.0.1"

# Intel Mac
curl -L \
  "https://codeberg.org/forgejo/forgejo/releases/download/v${FORGEJO_VERSION}/forgejo-${FORGEJO_VERSION}-darwin-amd64" \
  -o /usr/local/bin/forgejo

# Apple Silicon
curl -L \
  "https://codeberg.org/forgejo/forgejo/releases/download/v${FORGEJO_VERSION}/forgejo-${FORGEJO_VERSION}-darwin-arm64" \
  -o /usr/local/bin/forgejo

chmod +x /usr/local/bin/forgejo
forgejo --version
```

### Starting the binary

```bash
# Linux: run as git user
sudo -u git FORGEJO_WORK_DIR=/var/lib/forgejo \
  forgejo web --config /etc/forgejo/app.ini

# macOS: run directly
forgejo web
```

Navigate to `http://localhost:3000` on first run to complete the installation wizard.

---

## Installation: Docker

### Quick start (SQLite, evaluation only)

```bash
docker run -d \
  --name forgejo \
  -p 3000:3000 \
  -p 2222:22 \
  -v forgejo_data:/data \
  --restart unless-stopped \
  codeberg.org/forgejo/forgejo:9
```

Note the image is from `codeberg.org/forgejo/forgejo`, not Docker Hub.

### Production setup with Docker Compose

```yaml
version: "3"

networks:
  forgejo:
    external: false

services:
  server:
    image: codeberg.org/forgejo/forgejo:9
    container_name: forgejo
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - FORGEJO__database__DB_TYPE=postgres
      - FORGEJO__database__HOST=db:5432
      - FORGEJO__database__NAME=forgejo
      - FORGEJO__database__USER=forgejo
      - FORGEJO__database__PASSWD=forgejo_password
      - FORGEJO__server__DOMAIN=forgejo.example.com
      - FORGEJO__server__ROOT_URL=https://forgejo.example.com/
    restart: always
    networks:
      - forgejo
    volumes:
      - ./forgejo:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "2222:22"
    depends_on:
      - db

  db:
    image: postgres:15
    restart: always
    environment:
      - POSTGRES_USER=forgejo
      - POSTGRES_PASSWORD=forgejo_password
      - POSTGRES_DB=forgejo
    networks:
      - forgejo
    volumes:
      - ./postgres:/var/lib/postgresql/data
```

```bash
docker compose up -d
```

### Environment variable configuration

Docker deployments configure Forgejo via environment variables using `FORGEJO__{SECTION}__{KEY}`:

```bash
FORGEJO__server__DOMAIN=forgejo.example.com
FORGEJO__server__ROOT_URL=https://forgejo.example.com/
FORGEJO__service__DISABLE_REGISTRATION=true
FORGEJO__mailer__ENABLED=true
FORGEJO__mailer__SMTP_ADDR=smtp.example.com
FORGEJO__mailer__SMTP_PORT=587
```

Note: Forgejo uses `FORGEJO__` prefix. Gitea uses `GITEA__`. If you have existing Gitea Docker Compose files, update the prefix.

---

## The installation wizard

On first run, navigate to `http://localhost:3000` (or your configured URL). Forgejo shows the installation wizard.

### Wizard sections

**Database settings**: Choose SQLite for evaluation, PostgreSQL for production. Enter connection details.

**General settings**:
- **Site title**: shown in the browser tab and emails
- **Repository root**: where Git repositories are stored (e.g. `/home/git/forgejo-repositories`)
- **Forgejo working directory**: where uploads, logs and config are stored

**Administrator account**: Create the first admin user. Choose a strong password.

Click **Install Forgejo**. The wizard writes `app.ini` and redirects to the login page.

> [!IMPORTANT]
> The wizard is shown only once. After completion, all configuration changes must be made by editing `app.ini` directly and restarting Forgejo.

---

## The app.ini configuration file

Forgejo's `app.ini` is structurally identical to Gitea's, with additions for SAML and Forgejo-specific features. Location:

- Linux binary: `/etc/forgejo/app.ini`
- Docker: `/data/forgejo/conf/app.ini` (inside container, mapped from your volume)
- macOS: `~/Library/Application Support/forgejo/custom/conf/app.ini`

### Essential configuration

```ini
[server]
DOMAIN           = forgejo.example.com
ROOT_URL         = https://forgejo.example.com/
HTTP_PORT        = 3000
SSH_DOMAIN       = forgejo.example.com
SSH_PORT         = 22

[database]
DB_TYPE  = postgres
HOST     = localhost:5432
NAME     = forgejo
USER     = forgejo
PASSWD   = your_password

[repository]
ROOT = /home/git/forgejo-repositories

[security]
SECRET_KEY     = generate-a-long-random-string
INTERNAL_TOKEN = generate-another-long-random-string
INSTALL_LOCK   = true

[service]
DISABLE_REGISTRATION   = false
REQUIRE_SIGNIN_VIEW    = false
REGISTER_EMAIL_CONFIRM = true

[log]
MODE      = file
LEVEL     = Info
ROOT_PATH = /var/lib/forgejo/log

[mailer]
ENABLED  = false
; Configure SMTP for email notifications
```

### Generating secrets

```bash
forgejo generate secret SECRET_KEY
forgejo generate secret INTERNAL_TOKEN
```

---

## Database configuration

### PostgreSQL (recommended for production)

```bash
sudo -u postgres psql
CREATE USER forgejo WITH PASSWORD 'your_password';
CREATE DATABASE forgejo OWNER forgejo;
\q
```

```ini
[database]
DB_TYPE  = postgres
HOST     = localhost:5432
NAME     = forgejo
USER     = forgejo
PASSWD   = your_password
SSL_MODE = disable
```

### SQLite (personal use only)

```ini
[database]
DB_TYPE = sqlite3
PATH    = /var/lib/forgejo/data/forgejo.db
```

SQLite is unsuitable for concurrent multi-user deployments. Under simultaneous writes it produces database lock errors. Use PostgreSQL for any team installation.

---

## Reverse proxy and HTTPS

Forgejo should run behind a reverse proxy for HTTPS in production.

### Nginx

```nginx
server {
    listen 80;
    server_name forgejo.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name forgejo.example.com;

    ssl_certificate     /etc/ssl/certs/forgejo.example.com.crt;
    ssl_certificate_key /etc/ssl/private/forgejo.example.com.key;

    client_max_body_size 512M;

    location / {
        proxy_pass         http://localhost:3000;
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

### Caddy (automatic HTTPS)

```
forgejo.example.com {
    reverse_proxy localhost:3000
}
```

Caddy handles Let's Encrypt certificate provisioning and renewal automatically.

### app.ini for reverse proxy

```ini
[server]
ROOT_URL          = https://forgejo.example.com/
USE_PROXY_HEADERS = true
PROXY_HEADERS     = X-Forwarded-For, X-Real-IP
```

---

## Running as a systemd service

Create `/etc/systemd/system/forgejo.service`:

```ini
[Unit]
Description=Forgejo (Beyond coding. We forge.)
After=syslog.target
After=network.target
Requires=postgresql.service
After=postgresql.service

[Service]
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/var/lib/forgejo/
ExecStart=/usr/local/bin/forgejo web --config /etc/forgejo/app.ini
Restart=always
Environment=USER=git HOME=/home/git FORGEJO_WORK_DIR=/var/lib/forgejo

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable forgejo
sudo systemctl start forgejo
sudo systemctl status forgejo
```

---

## Setting up the Forgejo runner

Forgejo Actions requires the **Forgejo runner** - a separate binary maintained by the Forgejo project (a fork of act_runner). Download it from Forgejo's releases, not from Gitea's releases.

### Installing the Forgejo runner

🐧 **Linux**:
```bash
RUNNER_VERSION="3.5.1"   # check codeberg.org/forgejo/runner/releases for latest

wget -O forgejo-runner \
  "https://codeberg.org/forgejo/runner/releases/download/v${RUNNER_VERSION}/forgejo-runner-${RUNNER_VERSION}-linux-amd64"

chmod +x forgejo-runner
sudo mv forgejo-runner /usr/local/bin/forgejo-runner
forgejo-runner --version
```

🍎 **macOS**:
```bash
curl -L \
  "https://codeberg.org/forgejo/runner/releases/download/v${RUNNER_VERSION}/forgejo-runner-${RUNNER_VERSION}-darwin-arm64" \
  -o /usr/local/bin/forgejo-runner
chmod +x /usr/local/bin/forgejo-runner
```

### Registering the runner

1. In Forgejo, go to **Site Administration** > **Runners** (for instance-wide) or **Repository** > **Settings** > **Actions** > **Runners** (for repository-specific)
2. Click **Create new runner**
3. Copy the registration token

```bash
forgejo-runner register \
  --no-interactive \
  --instance https://forgejo.example.com \
  --token REGISTRATION_TOKEN \
  --name "Linux Runner 1" \
  --labels "ubuntu-latest:docker://node:20-bullseye,linux:docker://ubuntu:22.04"
```

### Starting the runner

```bash
forgejo-runner daemon
```

As a systemd service (`/etc/systemd/system/forgejo-runner.service`):

```ini
[Unit]
Description=Forgejo Actions Runner
After=network.target

[Service]
ExecStart=/usr/local/bin/forgejo-runner daemon
WorkingDirectory=/opt/forgejo-runner
Restart=always
User=runner

[Install]
WantedBy=multi-user.target
```

---

## Upgrading Forgejo

```bash
# Stop Forgejo
sudo systemctl stop forgejo

# Back up database
pg_dump forgejo > /backup/forgejo-db-$(date +%Y%m%d).sql

# Back up work directory
tar czf /backup/forgejo-data-$(date +%Y%m%d).tar.gz /var/lib/forgejo/

# Download new binary
FORGEJO_NEW="9.0.2"
wget -O /tmp/forgejo \
  "https://codeberg.org/forgejo/forgejo/releases/download/v${FORGEJO_NEW}/forgejo-${FORGEJO_NEW}-linux-amd64"
chmod +x /tmp/forgejo
sudo mv /tmp/forgejo /usr/local/bin/forgejo

# Start - runs migrations automatically
sudo systemctl start forgejo

# Check logs
sudo journalctl -u forgejo -n 50
```

Always check the release notes for breaking changes before upgrading. Forgejo's database schema changes between major versions (v8 to v9) may require additional migration steps documented in the release notes.

---

## Try It Yourself

**Exercise 1 - Quick Docker install**

```bash
docker run -d \
  --name forgejo-test \
  -p 3000:3000 \
  -v forgejo_test_data:/data \
  codeberg.org/forgejo/forgejo:9

# Open http://localhost:3000
# Complete the wizard with SQLite
```

**Exercise 2 - Complete setup and create a repository**

1. Complete the installation wizard
2. Create a repository called `hello-forgejo`
3. Clone and push:

```bash
git clone http://localhost:3000/your-username/hello-forgejo.git
cd hello-forgejo
echo "# Hello Forgejo" > README.md
git add README.md
git commit -m "Initial commit"
git push
```

**Exercise 3 - Set up the runner**

```bash
# Download
wget -O forgejo-runner \
  "https://codeberg.org/forgejo/runner/releases/download/v3.5.1/forgejo-runner-3.5.1-linux-amd64"
chmod +x forgejo-runner

# Register (get token from Site Admin > Runners)
./forgejo-runner register \
  --no-interactive \
  --instance http://localhost:3000 \
  --token YOUR_TOKEN \
  --name "Local Runner" \
  --labels "ubuntu-latest:docker://node:20-bullseye"

# Start
./forgejo-runner daemon
```

**Exercise 4 - Test a workflow**

Create `.forgejo/workflows/test.yml`:

```yaml
name: Hello
on: [push]
jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Hello from Forgejo Actions!"
```

Push and watch the Actions tab.

---

## Common Mistakes

**Downloading the Gitea act_runner instead of the Forgejo runner**

The Forgejo runner is maintained separately at `codeberg.org/forgejo/runner`. Using Gitea's act_runner against a Forgejo instance may work for basic cases but is not officially supported and may have compatibility issues with Forgejo-specific features.

**Using `GITEA__` prefix in Docker environment variables**

Forgejo uses `FORGEJO__` as the environment variable prefix for Docker configuration. Using `GITEA__` (Gitea's prefix) will not configure Forgejo correctly. Update any Gitea Docker Compose files you are adapting.

**Not reading release notes before a major version upgrade**

Forgejo major versions (v7, v8, v9) may include breaking database schema changes that require migration steps beyond the automatic migration. Always read the release notes for any major version upgrade before proceeding.

**Running Forgejo on a machine without Docker for the runner**

The Forgejo runner executes jobs inside Docker containers by default. The machine running the runner must have Docker installed. A runner without Docker will fail all jobs that use container-based execution.

**Forgetting to back up before upgrading**

Database migrations are one-way. A failed migration without a backup is data loss. Back up both the database and the work directory before every upgrade.

---

## Summary

Forgejo installs as a single binary or via Docker on Linux and macOS. Windows is not supported. Download binaries from `codeberg.org/forgejo/forgejo/releases` - not from Gitea's download site.

The Docker image is `codeberg.org/forgejo/forgejo:9` (or the specific major version). Docker environment variables use `FORGEJO__SECTION__KEY` prefix - update any Gitea Docker Compose files you adapt.

Configuration is identical to Gitea in structure: `app.ini` with the same section/key format. Generate secrets with `forgejo generate secret`. Use PostgreSQL for any multi-user deployment - SQLite is personal use only.

Run Forgejo behind Nginx or Caddy for HTTPS. Set `ROOT_URL` to match the actual URL users access. Run as a systemd service with a dedicated non-root user.

Forgejo Actions requires the Forgejo runner (separate from Gitea's act_runner), downloaded from `codeberg.org/forgejo/runner/releases`. Register with a token from the admin panel and start with `forgejo-runner daemon`.

---

## Sources

- [Forgejo: Installation documentation](https://forgejo.org/docs/latest/admin/installation-binary/)
- [Forgejo: Docker installation](https://forgejo.org/docs/latest/admin/installation-docker/)
- [Forgejo: Configuration reference](https://forgejo.org/docs/latest/admin/config-cheat-sheet/)
- [Forgejo runner: releases](https://codeberg.org/forgejo/runner/releases)
- [Forgejo: Releases](https://codeberg.org/forgejo/forgejo/releases)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

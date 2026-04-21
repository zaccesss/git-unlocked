# Installing Gitea

**Difficulty:** 🟡 Intermediate | **Time:** 40 minutes

Gitea installs in one of three ways: as a single binary directly on the host, via Docker, or from a system package. All three approaches result in the same running application - the difference is in how you manage upgrades, configuration and the surrounding infrastructure. This file covers all three methods across Windows, Mac and Linux, then walks through the initial configuration, the app.ini configuration file and recommended production settings.

---

## Table of Contents

1. [Choosing an installation method](#choosing-an-installation-method)
2. [System requirements](#system-requirements)
3. [Installation: binary](#installation-binary)
4. [Installation: Docker](#installation-docker)
5. [Installation: system packages](#installation-system-packages)
6. [The installation wizard](#the-installation-wizard)
7. [The app.ini configuration file](#the-appini-configuration-file)
8. [Database configuration](#database-configuration)
9. [Reverse proxy setup](#reverse-proxy-setup)
10. [Running Gitea as a service](#running-gitea-as-a-service)
11. [Setting up act_runner for Gitea Actions](#setting-up-act_runner-for-gitea-actions)
12. [Upgrading Gitea](#upgrading-gitea)
13. [Try It Yourself](#try-it-yourself)
14. [Common Mistakes](#common-mistakes)
15. [Summary](#summary)
16. [Sources](#sources)

---

## Choosing an installation method

**Binary installation** is best for production deployments where you want maximum control, predictable behaviour and easy upgrades. You manage a single executable file. Updates are as simple as replacing the binary. No container runtime required.

**Docker installation** is best for development, quick evaluation or organisations that standardise on container deployments. The official Docker image bundles everything. Docker Compose makes multi-service setups (Gitea + PostgreSQL + act_runner) easy to manage as a unit.

**System package installation** is best when you want your operating system's package manager to handle installation, updates and service management. Available on some Linux distributions.

---

## System requirements

Gitea is exceptionally resource-efficient. These are genuine minimums, not "recommended for best performance" inflated numbers:

| Use case | CPU | RAM | Disk |
|---|---|---|---|
| Personal / home lab | 1 core | 256 MB | SSD/HDD with free space for repos |
| Small team (up to 10 users) | 1-2 cores | 512 MB | SSD recommended |
| Medium team (10-50 users) | 2-4 cores | 1-2 GB | SSD |
| Larger deployment (50+ users) | 4+ cores | 4+ GB | NVMe SSD |

Gitea officially states it can run on a Raspberry Pi. In practice, a Raspberry Pi 4 with 1-2 GB RAM handles a small team without difficulty, though large repository operations (cloning, searching) are slower than on dedicated hardware.

**Supported operating systems:**
- 🐧 Linux (x86_64, ARM, ARM64) - primary platform
- 🪟 Windows (x86_64) - fully supported
- 🍎 macOS (x86_64, Apple Silicon) - supported

**Supported databases:**
- SQLite 3 (built-in, suitable for personal use and small teams)
- PostgreSQL 12 or later (recommended for production)
- MySQL 8.0 or later
- MSSQL 2008 or later (Windows environments)

---

## Installation: binary

### 🐧 Linux

```bash
# Download the latest binary for Linux amd64
# Check https://dl.gitea.com/gitea/ for the latest version
GITEA_VERSION="1.25.5"

wget -O gitea "https://dl.gitea.com/gitea/${GITEA_VERSION}/gitea-${GITEA_VERSION}-linux-amd64"
chmod +x gitea

# Create a git user to run Gitea
sudo adduser \
  --system \
  --shell /bin/bash \
  --gecos 'Git Version Control' \
  --group \
  --disabled-password \
  --home /home/git \
  git

# Create directories
sudo mkdir -p /var/lib/gitea/{custom,data,log}
sudo chown -R git:git /var/lib/gitea/
sudo chmod -R 750 /var/lib/gitea/
sudo mkdir /etc/gitea
sudo chown root:git /etc/gitea
sudo chmod 770 /etc/gitea

# Move binary to a system location
sudo cp gitea /usr/local/bin/gitea

# Verify
gitea --version
```

### 🪟 Windows

```powershell
# Create a directory for Gitea
New-Item -ItemType Directory -Path "C:\gitea"

# Download the binary
$version = "1.25.5"
Invoke-WebRequest -Uri "https://dl.gitea.com/gitea/$version/gitea-$version-windows-4.0-amd64.exe" `
  -OutFile "C:\gitea\gitea.exe"

# Verify
C:\gitea\gitea.exe --version
```

### 🍎 macOS

```bash
# Using Homebrew
brew install gitea

# Or download the binary directly
GITEA_VERSION="1.25.5"
curl -L "https://dl.gitea.com/gitea/${GITEA_VERSION}/gitea-${GITEA_VERSION}-darwin-12.0-arm64" \
  -o /usr/local/bin/gitea  # Use amd64 if not Apple Silicon
chmod +x /usr/local/bin/gitea

gitea --version
```

### Running the binary

```bash
# Run Gitea (Linux/Mac)
GITEA_WORK_DIR=/var/lib/gitea gitea web --config /etc/gitea/app.ini

# Or set up the work directory and config as described below,
# then simply run:
gitea web
```

🪟 **Windows**:
```powershell
cd C:\gitea
.\gitea.exe web
```

On first run without an existing configuration, Gitea starts an installation wizard on port 3000. Navigate to `http://localhost:3000` to complete setup.

---

## Installation: Docker

Docker is the quickest way to get Gitea running, especially for evaluation.

### Quick start (SQLite)

```bash
# Run Gitea with SQLite (good for testing, not production)
docker run -d \
  --name gitea \
  -p 3000:3000 \
  -p 2222:22 \
  -v /path/to/gitea/data:/data \
  --restart unless-stopped \
  gitea/gitea:latest
```

Navigate to `http://localhost:3000` to complete the installation wizard.

### Production setup with Docker Compose

Create `docker-compose.yml`:

```yaml
version: "3"

networks:
  gitea:
    external: false

services:
  server:
    image: gitea/gitea:1.25.5
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
      - GITEA__database__DB_TYPE=postgres
      - GITEA__database__HOST=db:5432
      - GITEA__database__NAME=gitea
      - GITEA__database__USER=gitea
      - GITEA__database__PASSWD=gitea_password
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
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
      - POSTGRES_USER=gitea
      - POSTGRES_PASSWORD=gitea_password
      - POSTGRES_DB=gitea
    networks:
      - gitea
    volumes:
      - ./postgres:/var/lib/postgresql/data
```

Start:
```bash
docker compose up -d
```

### Environment variables in Docker

Docker deployments can set Gitea configuration via environment variables instead of app.ini. The naming convention is `GITEA__{SECTION}__{KEY}`:

```bash
GITEA__server__DOMAIN=git.example.com
GITEA__server__ROOT_URL=https://git.example.com/
GITEA__service__DISABLE_REGISTRATION=true
GITEA__mailer__ENABLED=true
GITEA__mailer__HOST=smtp.example.com:587
```

---

## Installation: system packages

### 🐧 Arch Linux (AUR)

```bash
# Using yay or paru
yay -S gitea
# or
paru -S gitea
```

### 🐧 Alpine Linux

```bash
apk add gitea
```

### 🐧 FreeBSD

```bash
pkg install gitea
```

> [!NOTE]
> Package availability and version currency varies by distribution. The binary installation method always provides the latest version. Package repositories may lag a few versions behind.

---

## The installation wizard

When Gitea starts for the first time without a configuration file, it serves an installation wizard at the root URL (default: `http://localhost:3000`).

### Wizard sections

**Database settings:**
- Database type: SQLite3 (default), PostgreSQL, MySQL, MSSQL
- For SQLite: the database file path (default: in the work directory)
- For PostgreSQL/MySQL: host, port, database name, username, password

**General settings:**
- **Site title**: shown in the browser tab and header
- **Repository root path**: where Git repositories are stored on disk (default: `/home/git/gitea-repositories`)
- **Git hooks custom path**: leave default unless you have specific needs
- **Gitea working directory**: where uploads, logs and configuration live

**Optional settings:**
- Email setup (SMTP configuration for notifications)
- Server and third-party service settings

**Administrator account:**
The wizard creates the first administrator account. Choose a strong password. This account has full access to all repositories and instance settings.

> [!IMPORTANT]
> After completing the installation wizard, Gitea writes the configuration to app.ini. The wizard is only shown once. If you need to reconfigure, edit app.ini directly and restart Gitea.

---

## The app.ini configuration file

The `app.ini` file is the single source of truth for all Gitea configuration. It uses standard INI format with sections in square brackets.

### Location

- Linux binary: `/etc/gitea/app.ini`
- Linux package: `/etc/gitea/app.ini`
- Docker: `/data/gitea/conf/app.ini` (inside the container, mapped from your volume)
- Windows: `C:\gitea\custom\conf\app.ini`

### Essential configuration sections

```ini
[server]
DOMAIN           = git.example.com
ROOT_URL         = https://git.example.com/
HTTP_PORT        = 3000
SSH_DOMAIN       = git.example.com
SSH_PORT         = 22
START_SSH_SERVER = false   ; set true if not using system sshd

[database]
DB_TYPE  = postgres
HOST     = localhost:5432
NAME     = gitea
USER     = gitea
PASSWD   = your_password
SSL_MODE = disable

[repository]
ROOT = /home/git/gitea-repositories

[log]
MODE      = file
LEVEL     = Info
ROOT_PATH = /var/lib/gitea/log

[security]
SECRET_KEY           = generate_a_long_random_string_here
INTERNAL_TOKEN       = generate_another_long_random_string_here
INSTALL_LOCK         = true   ; set after installation

[service]
DISABLE_REGISTRATION        = false  ; set true after initial setup if desired
REQUIRE_SIGNIN_VIEW         = false  ; set true for private instances
REGISTER_EMAIL_CONFIRM      = false  ; require email confirmation to register
ENABLE_NOTIFY_MAIL          = false

[mailer]
ENABLED    = false
; Set to true and configure for email notifications
HOST       = smtp.example.com:587
FROM       = gitea@example.com
USER       = gitea@example.com
PASSWD     = smtp_password
PROTOCOL   = smtp+starttls

[oauth2]
ENABLE = true
JWT_SECRET = generate_a_long_random_string_here

[admin]
DEFAULT_EMAIL_NOTIFICATIONS = enabled
```

### Generating secrets

Generate the required secret strings:

```bash
# Generate a SECRET_KEY
gitea generate secret SECRET_KEY

# Generate an INTERNAL_TOKEN
gitea generate secret INTERNAL_TOKEN

# Generate an OAUTH2_JWT_SECRET
gitea generate secret JWT_SECRET
```

Or use any cryptographically random string generator:

```bash
openssl rand -hex 32
```

---

## Database configuration

### SQLite (small deployments)

SQLite requires no separate server. The database is a single file in the work directory. Suitable for personal use, home labs and small teams (under 10 active users).

```ini
[database]
DB_TYPE = sqlite3
PATH    = /var/lib/gitea/data/gitea.db
```

### PostgreSQL (recommended for production)

Install PostgreSQL separately, create a database and user:

```bash
sudo -u postgres psql

CREATE USER gitea WITH PASSWORD 'your_password';
CREATE DATABASE gitea OWNER gitea;
\q
```

```ini
[database]
DB_TYPE  = postgres
HOST     = localhost:5432
NAME     = gitea
USER     = gitea
PASSWD   = your_password
SSL_MODE = disable   ; or require for encrypted connections
```

### MySQL

```bash
mysql -u root -p

CREATE DATABASE gitea CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'gitea'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON gitea.* TO 'gitea'@'localhost';
FLUSH PRIVILEGES;
```

```ini
[database]
DB_TYPE = mysql
HOST    = localhost:3306
NAME    = gitea
USER    = gitea
PASSWD  = your_password
CHARSET = utf8mb4
```

---

## Reverse proxy setup

In production, Gitea should sit behind a reverse proxy (Nginx or Caddy) that handles HTTPS termination.

### Nginx configuration

```nginx
server {
    listen 80;
    server_name git.example.com;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name git.example.com;

    ssl_certificate     /etc/ssl/certs/git.example.com.crt;
    ssl_certificate_key /etc/ssl/private/git.example.com.key;

    location / {
        proxy_pass         http://localhost:3000;
        proxy_set_header   Host              $host;
        proxy_set_header   X-Real-IP         $remote_addr;
        proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

### Caddy configuration (automatic HTTPS)

```
git.example.com {
    reverse_proxy localhost:3000
}
```

Caddy automatically obtains and renews Let's Encrypt certificates.

### Update app.ini for the proxy

```ini
[server]
ROOT_URL         = https://git.example.com/
DOMAIN           = git.example.com
HTTP_PORT        = 3000
; Tell Gitea it is behind a reverse proxy
USE_PROXY_HEADERS = true
PROXY_HEADERS     = X-Forwarded-For, X-Real-IP
```

---

## Running Gitea as a service

### 🐧 Linux (systemd)

Create `/etc/systemd/system/gitea.service`:

```ini
[Unit]
Description=Gitea (Git with a cup of tea)
After=syslog.target
After=network.target
Requires=postgresql.service
After=postgresql.service

[Service]
RestartSec=2s
Type=simple
User=git
Group=git
WorkingDirectory=/var/lib/gitea/
ExecStart=/usr/local/bin/gitea web --config /etc/gitea/app.ini
Restart=always
Environment=USER=git HOME=/home/git GITEA_WORK_DIR=/var/lib/gitea

[Install]
WantedBy=multi-user.target
```

Enable and start:
```bash
sudo systemctl enable gitea
sudo systemctl start gitea
sudo systemctl status gitea
```

### 🪟 Windows (as a service)

```powershell
# Register as Windows service
sc create gitea `
  binPath="C:\gitea\gitea.exe web --config C:\gitea\custom\conf\app.ini" `
  start= auto `
  displayname="Gitea"

# Start the service
sc start gitea
```

---

## Setting up act_runner for Gitea Actions

Gitea Actions requires a separate **act_runner** binary. This runner registers with your Gitea instance and executes workflow jobs in Docker containers.

### Installing act_runner

🐧 **Linux**:
```bash
# Download the latest act_runner
wget -O act_runner "https://dl.gitea.com/act_runner/latest/act_runner-latest-linux-amd64"
chmod +x act_runner
sudo mv act_runner /usr/local/bin/act_runner
```

🪟 **Windows** (PowerShell):
```powershell
Invoke-WebRequest -Uri "https://dl.gitea.com/act_runner/latest/act_runner-latest-windows-amd64.exe" `
  -OutFile "C:\act_runner\act_runner.exe"
```

🍎 **Mac**:
```bash
wget -O act_runner "https://dl.gitea.com/act_runner/latest/act_runner-latest-darwin-arm64"
# Use darwin-amd64 for Intel Macs
chmod +x act_runner
```

### Registering the runner

1. In Gitea, go to **Site Administration** > **Runners** (or for a repository: Settings > Actions > Runners)
2. Click **Create new runner**
3. Copy the registration token

Register the runner:
```bash
act_runner register \
  --no-interactive \
  --instance https://your-gitea-instance.com \
  --token REGISTRATION_TOKEN_FROM_GITEA \
  --name "My Runner" \
  --labels "ubuntu-latest:docker://node:20-bullseye"
```

This creates a `.runner` file in the current directory containing the runner's credentials.

### Starting the runner

```bash
act_runner daemon
```

The runner connects to Gitea and starts accepting jobs. For production, set it up as a systemd service:

```ini
[Unit]
Description=Gitea Actions Runner
After=network.target

[Service]
ExecStart=/usr/local/bin/act_runner daemon
WorkingDirectory=/opt/act_runner
Restart=always

[Install]
WantedBy=multi-user.target
```

### Runner labels

When registering, the `--labels` flag defines which workflow `runs-on` values this runner accepts. The format is `label:executor://image`:

```bash
# Accept ubuntu-latest jobs using Docker node:20
--labels "ubuntu-latest:docker://node:20-bullseye"

# Accept multiple labels
--labels "ubuntu-latest:docker://node:20-bullseye,ubuntu-22.04:docker://ubuntu:22.04"

# Use host executor (runs directly on the machine, no Docker)
--labels "native:host"
```

In your workflow files, `runs-on: ubuntu-latest` will be matched to this runner.

---

## Upgrading Gitea

### Binary upgrade

```bash
# Stop Gitea
sudo systemctl stop gitea

# Back up the database and work directory
pg_dump gitea > gitea_backup_$(date +%Y%m%d).sql
tar czf gitea_data_backup_$(date +%Y%m%d).tar.gz /var/lib/gitea/

# Download new binary
GITEA_NEW_VERSION="1.26.0"
wget -O /tmp/gitea "https://dl.gitea.com/gitea/${GITEA_NEW_VERSION}/gitea-${GITEA_NEW_VERSION}-linux-amd64"
chmod +x /tmp/gitea

# Replace the binary
sudo mv /tmp/gitea /usr/local/bin/gitea

# Start Gitea - it runs migrations automatically on startup
sudo systemctl start gitea

# Check logs for any migration issues
sudo journalctl -u gitea -n 50
```

### Docker upgrade

```bash
# Pull the new image
docker pull gitea/gitea:1.26.0

# Update the version in docker-compose.yml, then:
docker compose pull
docker compose up -d
```

Gitea runs database migrations automatically on startup after an upgrade. Check the logs to confirm migrations completed successfully.

> [!WARNING]
> Always back up before upgrading. Gitea's database schema changes between versions and the migration is automatic but irreversible. A backup is your safety net if something goes wrong.

---

## Try It Yourself

**Exercise 1 - Quick Docker install**

```bash
# Ensure Docker is installed, then:
docker run -d \
  --name gitea-test \
  -p 3000:3000 \
  -v gitea_data:/data \
  gitea/gitea:latest

# Open http://localhost:3000 and complete the wizard
# Use SQLite for this test instance
```

**Exercise 2 - Complete the wizard**

1. Navigate to `http://localhost:3000`
2. Keep SQLite as the database
3. Set the site title to "My Gitea"
4. Create an admin account
5. Click "Install Gitea"

**Exercise 3 - Create a repository and push code**

```bash
# After setup, create a repository via the web UI
# Then clone and push:
git clone http://localhost:3000/your-username/test-repo.git
cd test-repo
echo "# Hello Gitea" > README.md
git add README.md
git commit -m "Initial commit"
git push origin main
```

**Exercise 4 - Set up act_runner (Linux/Mac)**

```bash
# Download act_runner
wget -O act_runner "https://dl.gitea.com/act_runner/latest/act_runner-latest-linux-amd64"
chmod +x act_runner

# In Gitea: Site Administration > Runners > Create new runner
# Copy the token, then:
./act_runner register \
  --no-interactive \
  --instance http://localhost:3000 \
  --token YOUR_TOKEN \
  --name "Local Runner" \
  --labels "ubuntu-latest:docker://node:20-bullseye"

# Start the runner
./act_runner daemon
```

---

## Common Mistakes

**Using SQLite for a production team deployment**

SQLite is single-writer and does not handle concurrent writes well. Under load from multiple users pushing simultaneously, you will see lock errors. Use PostgreSQL for any deployment with more than a handful of occasional users.

**Not generating proper secret keys**

The installation wizard auto-generates `SECRET_KEY` and `INTERNAL_TOKEN`. These are fine for the initial setup but if you are deploying from a Docker Compose template or configuration management, ensure you generate unique secrets per deployment. Do not use the same secrets across multiple Gitea instances.

**Forgetting to configure ROOT_URL**

If `ROOT_URL` does not match the actual URL users access Gitea from, clone URLs shown in the interface will be wrong, email links will be broken, and OAuth redirect flows will fail. Always set `ROOT_URL` to the exact URL (including trailing slash) that users use.

**Running Gitea directly on port 80/443 as root**

Running Gitea on privileged ports (80, 443) requires root privileges. Do not run Gitea as root. Use a reverse proxy (Nginx, Caddy) to handle the privileged ports and proxy to Gitea on a non-privileged port (3000).

**Not backing up before upgrading**

Gitea migrations are automatic and usually smooth, but occasionally cause issues. A backup takes minutes. A failed migration without a backup can cost hours. Always back up the database and work directory before upgrading.

**Not updating act_runner after upgrading Gitea**

The act_runner binary should be kept reasonably close to the Gitea version. A very old runner against a new Gitea instance may fail to connect or execute jobs incorrectly. Update act_runner when you update Gitea.

---

## Summary

Gitea installs as a single binary, via Docker or from system packages. The binary method gives maximum control; Docker Compose is the quickest path to a production-ready setup with PostgreSQL.

Minimum requirements are genuinely minimal: 512 MB RAM and any modern OS. For production use, PostgreSQL is strongly recommended over SQLite.

Configuration lives in `app.ini`. The most important settings are `ROOT_URL` (must match the actual access URL), database connection, secret keys and SMTP configuration for email. In Docker, settings can also be passed as environment variables using the `GITEA__{SECTION}__{KEY}` convention.

For production, run Gitea behind a reverse proxy (Nginx or Caddy) that handles HTTPS. Run Gitea as a non-root system service (systemd on Linux, Windows Service on Windows).

Gitea Actions requires a separate `act_runner` binary that registers with your instance and executes workflow jobs. Install Docker on the runner machine for container-based workflows.

Always back up the database and work directory before upgrading. Migrations run automatically on startup.

---

## Sources

- [Gitea: Installation documentation](https://docs.gitea.com/installation/install-from-binary)
- [Gitea: Docker installation](https://docs.gitea.com/installation/install-with-docker)
- [Gitea: Configuration cheat sheet](https://docs.gitea.com/administration/config-cheat-sheet)
- [Gitea: act_runner documentation](https://docs.gitea.com/usage/actions/act-runner)
- [Gitea: Reverse proxy documentation](https://docs.gitea.com/administration/reverse-proxies)
- [Gitea: Database preparation](https://docs.gitea.com/installation/database-prep)

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

# Security Features

**Difficulty:** 🔴 Advanced | **Time:** 45 minutes

GitLab's security scanning is one of the most significant reasons organisations choose the platform over alternatives. Rather than connecting a third-party scanner to your CI/CD pipeline, security scans run as native pipeline jobs, fully integrated with merge requests, vulnerability dashboards and compliance frameworks. A vulnerability found in a SAST scan links directly to the merge request that introduced it, which links to the issue that requested the feature, which links to the sprint that planned it. The entire audit trail is in one place.

This file covers every security scanning capability GitLab offers: what each scanner does, which tier it requires, how to enable it in your pipeline, what the output looks like and how vulnerabilities are managed from detection to remediation.

---

## Table of Contents

- [Security scanning overview](#security-scanning-overview)
- [Tier requirements at a glance](#tier-requirements-at-a-glance)
- [SAST - Static Application Security Testing](#sast---static-application-security-testing)
- [Secret Detection](#secret-detection)
- [Dependency Scanning](#dependency-scanning)
- [Container Scanning](#container-scanning)
- [IaC Scanning](#iac-scanning)
- [DAST - Dynamic Application Security Testing](#dast---dynamic-application-security-testing)
- [API Security Testing](#api-security-testing)
- [Fuzz Testing](#fuzz-testing)
- [License Compliance](#license-compliance)
- [Vulnerability Management](#vulnerability-management)
- [Security Policies](#security-policies)
- [GitLab Advisory Database](#gitlab-advisory-database)
- [Comparing with GitHub's security offering](#comparing-with-githubs-security-offering)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## Security Scanning Overview

GitLab's security scanning works by running specialised jobs inside your existing CI/CD pipeline. You include a template in your `.gitlab-ci.yml`, GitLab runs the scanner as a standard pipeline job, and the results are uploaded as a structured artifact. GitLab then parses that artifact and surfaces findings in:

- **The merge request security widget**: a panel in the MR that shows new vulnerabilities introduced by this branch compared to the target branch
- **The pipeline security tab**: all findings for the entire pipeline run
- **The vulnerability report** (Ultimate): a centralised dashboard showing all vulnerabilities across the project
- **The security dashboard** (Ultimate): aggregated vulnerabilities across multiple projects in a group

**How to include a scanner:**

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
  - template: Jobs/Container-Scanning.gitlab-ci.yml
  - template: Security/SAST-IaC.gitlab-ci.yml
```

Each template adds pre-configured jobs to your pipeline. GitLab maintains these templates and updates them as new vulnerabilities are discovered and scanning tools are improved.

**Scanning output format:**

All GitLab security scanners output a report in GitLab's standard JSON format (`gl-sast-report.json`, `gl-secret-detection-report.json` etc.). This standardised format means GitLab can parse and display all scanner results consistently, regardless of which underlying scanning engine is used.

---

## Tier Requirements at a Glance

| Scanner                                   | Free | Premium | Ultimate |
| ----------------------------------------- | ---- | ------- | -------- |
| SAST (basic, open-source analysers)       | ✓    | ✓       | ✓        |
| SAST Advanced (cross-file/cross-function) | ✗    | ✗       | ✓        |
| Secret Detection (pipeline scanning)      | ✓    | ✓       | ✓        |
| Secret Detection (push protection)        | ✓    | ✓       | ✓        |
| Dependency Scanning (basic)               | ✓    | ✓       | ✓        |
| Container Scanning (basic)                | ✓    | ✓       | ✓        |
| IaC Scanning                              | ✓    | ✓       | ✓        |
| DAST (web app scanning)                   | ✗    | ✗       | ✓        |
| API Security Testing                      | ✗    | ✗       | ✓        |
| Coverage-guided Fuzz Testing              | ✗    | ✗       | ✓        |
| License Compliance                        | ✗    | ✗       | ✓        |
| Vulnerability Report (full dashboard)     | ✗    | ✗       | ✓        |
| Security Policies                         | ✗    | ✗       | ✓        |
| Security Compliance Dashboard             | ✗    | ✗       | ✓        |
| MR security widget (basic findings)       | ✓    | ✓       | ✓        |
| MR security widget (comparison, dismiss)  | ✗    | ✗       | ✓        |

---

## SAST - Static Application Security Testing

SAST analyses your source code **without running it**, looking for patterns that indicate security vulnerabilities: SQL injection, cross-site scripting, insecure cryptography, hardcoded credentials, path traversal and many more.

### How SAST works

GitLab runs language-specific analysers against your source code. Each analyser understands the semantics of a particular language - not just pattern matching, but understanding how data flows through the code, whether user input is sanitised before being used in a database query, whether a cryptographic function is called with safe parameters.

**Supported languages and analysers:**

| Language                | Analyser |
| ----------------------- | -------- |
| C / C++                 | Semgrep  |
| C#                      | Semgrep  |
| Go                      | Semgrep  |
| Java                    | Semgrep  |
| JavaScript / TypeScript | Semgrep  |
| Python                  | Semgrep  |
| Ruby                    | Semgrep  |
| PHP                     | Semgrep  |
| Kotlin                  | Semgrep  |
| Scala                   | Semgrep  |
| Swift                   | Semgrep  |
| Apex (Salesforce)       | PMD      |
| Elixir                  | Credo    |
| Groovy                  | SpotBugs |
| Objective-C             | Semgrep  |

GitLab migrated most SAST analysers to **Semgrep** as the unified engine. GitLab maintains its own set of Semgrep rules covering OWASP Top 10 and additional vulnerability classes.

### Enabling SAST

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml

stages:
  - test # SAST jobs run in the test stage by default
```

GitLab automatically detects your project's languages and runs only the relevant analysers. No per-language configuration needed.

### SAST output in merge requests

On Free/Premium: SAST findings appear in the **Pipeline** tab of the MR. Click into the security job to see findings in the job log.

On Ultimate: a **Security** panel appears in the MR showing new vulnerabilities introduced by this branch compared to the target branch. Findings are categorised by severity (Critical, High, Medium, Low, Info, Unknown) and include:

- The vulnerability name and description
- The file and line number
- The CWE (Common Weakness Enumeration) identifier
- A remediation suggestion
- Links to further reading

### Advanced SAST (Ultimate)

Basic SAST analyses each file independently. Advanced SAST (available in Ultimate) performs **cross-file and cross-function analysis** - it understands how data flows between functions, across files and through libraries. This catches a broader class of vulnerabilities that basic per-file analysis misses.

For example: a user input enters the system in `routes/login.js`, passes through a validation function in `utils/auth.js` and is used in a database query in `models/user.js`. Basic SAST may miss this. Advanced SAST traces the data flow across all three files.

GitLab 18.10 introduced **SAST false positive detection** using the Duo Agent Platform. When Advanced SAST flags a finding, the AI analyses the finding in context and can identify false positives - flagging them so security teams spend less time on noise.

### Customising SAST

Override default settings using CI/CD variables:

```yaml
variables:
  SAST_EXCLUDED_PATHS: "spec,test,tests,vendor,node_modules" # skip test files
  SAST_EXCLUDED_ANALYZERS: "semgrep" # skip specific analysers
  SAST_SEVERITY_LEVEL: "medium" # only report medium and above
  SCAN_KUBERNETES_MANIFESTS: "true" # include Kubernetes manifests
```

Customise Semgrep rules by adding a `.semgrep.yml` file to your repository or by setting custom rule paths.

### Security configuration UI

Enable SAST directly from the GitLab UI without writing any YAML:

**Secure → Security configuration → Static Application Security Testing → Enable SAST**

GitLab generates the appropriate `.gitlab-ci.yml` changes and creates a merge request for you to review and merge.

---

## Secret Detection

Secret Detection scans your code and Git history for accidentally committed credentials: API keys, tokens, passwords, private keys, certificates and connection strings.

**Tier**: Free - all tiers.

### Why secret detection matters

Secrets committed to a repository are exposed to everyone with repository access. For a public repository, they are exposed to the entire internet. Automated bots scan public repositories on all major platforms continuously - a valid AWS key or GitHub token will typically be used within minutes of exposure.

Secret Detection catches these before they cause damage. Push protection catches them before the push reaches the remote server at all.

### Pipeline scanning

Scans all files in the current commit plus the recent Git history.

```yaml
include:
  - template: Jobs/Secret-Detection.gitlab-ci.yml
```

The scanner checks over 90 secret patterns including:

- AWS Access Keys and Secret Keys
- Google Cloud API keys
- GitHub and GitLab Personal Access Tokens
- Stripe API keys
- Twilio auth tokens
- SSH private keys
- Generic high-entropy strings (likely random secrets)
- Database connection strings with embedded credentials
- JWT tokens
- Kubernetes secrets
- npm auth tokens
- Docker registry credentials

### Push protection

Push protection catches secrets **before they reach the remote repository** - at the point of `git push`. If a push contains a secret matching a known pattern, the push is blocked entirely and the developer sees a detailed error message explaining which file and line the secret was found on.

**Enable push protection**: Secure → Security configuration → Secret Detection → **Enable push protection**

Or in settings: Settings → Security and compliance → Secret detection push protection → Enable.

> [!NOTE]
> Push protection on GitLab.com currently requires the repository to have secret detection configured. Unlike GitHub's push protection which is a global platform feature, GitLab's push protection is opt-in per project.

### Automatic revocation

For some secret types, GitLab automatically notifies the issuing service when a secret is detected, triggering auto-revocation. This partnership reduces the window between detection and revocation. Supported providers include some major cloud and SaaS vendors (the list expands over time).

### Configuring secret detection

```yaml
variables:
  SECRET_DETECTION_EXCLUDED_PATHS: "tests/,spec/,vendor/"
  SECRET_DETECTION_HISTORIC_SCAN: "true" # scan entire Git history (slow)
```

By default, the scanner checks only the current commit and recent history. Set `SECRET_DETECTION_HISTORIC_SCAN: "true"` to scan the entire commit history - useful when first enabling the scanner on an existing repository.

### What to do when a secret is found

1. **Immediately revoke the compromised credential** at the issuing service. Do this before anything else. Bots scan public repositories continuously.
2. **Generate a replacement credential**.
3. **Store the new credential as a CI/CD variable** (masked and protected), not in the code.
4. **Remove the secret from history** using `git-filter-repo`. Removing it from the current HEAD is not enough - it remains in every commit where it appeared.
5. **Force-push** to overwrite the remote history on all branches.
6. **Notify your team** so anyone who has cloned the repository rotates any local copies.

---

## Dependency Scanning

Dependency Scanning checks your project's third-party dependencies against known vulnerability databases. Every npm package, Python library, Ruby gem, Java JAR or Go module you depend on may have known security vulnerabilities - Dependency Scanning identifies them.

**Tier**: Basic scanning is Free. Advanced vulnerability management requires Ultimate.

### How it works

GitLab runs dependency scanning against your project's lock files and manifest files:

| Ecosystem            | Files scanned                                                 |
| -------------------- | ------------------------------------------------------------- |
| JavaScript/npm       | `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml`            |
| Python               | `requirements.txt`, `Pipfile.lock`, `poetry.lock`, `setup.py` |
| Ruby                 | `Gemfile.lock`                                                |
| Java/Kotlin (Maven)  | `pom.xml`                                                     |
| Java/Kotlin (Gradle) | `build.gradle`, `build.gradle.kts`                            |
| Go                   | `go.sum`, `go.mod`                                            |
| PHP (Composer)       | `composer.lock`                                               |
| .NET/C# (NuGet)      | `*.csproj`, `packages.lock.json`                              |
| Rust                 | `Cargo.lock`                                                  |
| Conan (C/C++)        | `conan.lock`                                                  |

Each dependency version is checked against the **GitLab Advisory Database (GLAD)** and other sources including the National Vulnerability Database (NVD) and GitHub Advisory Database.

### Enabling dependency scanning

```yaml
include:
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml
```

GitLab uses **Trivy** as the primary dependency scanning engine (adopted as the standard analyser across multiple scanning types in GitLab 18.x).

### Reading dependency findings

Each finding includes:

- Package name and vulnerable version
- CVE (Common Vulnerabilities and Exposures) identifier
- CVSS score (severity rating from 0.0 to 10.0)
- Description of the vulnerability
- Fixed version (if available)
- Link to the advisory

### Dependency review in MRs

On Ultimate, the MR security widget shows:

- Dependencies introduced by this MR (packages added)
- Vulnerabilities in new dependencies
- Comparison against the target branch to show new vs existing vulnerabilities

### CycloneDX SBOM generation

GitLab can generate a **Software Bill of Materials** (SBOM) in CycloneDX format - a machine-readable inventory of all dependencies and their versions. This is increasingly required for supply chain security compliance (e.g. US Executive Order 14028).

```yaml
variables:
  CS_CYCLONEDX_OUTPUT: "true" # generate CycloneDX SBOM alongside scanning
```

The SBOM is uploaded as a pipeline artifact and accessible from the pipeline page.

---

## Container Scanning

Container Scanning scans Docker images in your container registry for known operating system package vulnerabilities.

**Tier**: Basic scanning is Free. Vulnerability management dashboard requires Ultimate.

### How it works

After building a Docker image and pushing it to the registry, Container Scanning pulls the image and inspects every package installed in the OS layer and in application layers. Each package version is checked against vulnerability databases.

**Scanner**: GitLab uses **Trivy** for container scanning.

```yaml
include:
  - template: Jobs/Container-Scanning.gitlab-ci.yml

# Container Scanning needs to know which image to scan
variables:
  CS_IMAGE: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

# Typical pattern: build the image first, then scan it
stages:
  - build
  - scan

build-image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

container-scanning:
  stage: scan
  needs: ["build-image"]
```

### What container scanning finds

- OS-level vulnerabilities: CVEs in packages installed via `apt`, `yum`, `apk` or similar
- Application-level vulnerabilities in packages detected in the image (e.g. npm packages installed during image build)
- Misconfigurations in the image (exposed ports, running as root, etc.)

### Reducing container vulnerabilities

- Use minimal base images (`alpine`, `distroless`, `slim` variants) - fewer packages means fewer potential vulnerabilities
- Update base images regularly - most container vulnerabilities are in outdated base image packages
- Use multi-stage builds to exclude build tools from the final image
- Run as a non-root user (`USER nonroot` in Dockerfile)

---

## IaC Scanning

Infrastructure as Code Scanning detects security misconfigurations in your infrastructure definitions before they are deployed. Misconfigured cloud resources are responsible for many high-profile data breaches.

**Tier**: Free - all tiers.

### Supported IaC formats

- **Terraform** (`.tf` files)
- **CloudFormation** (YAML and JSON)
- **Kubernetes manifests** (YAML)
- **Ansible playbooks** (YAML)
- **Dockerfile** (via separate Dockerfile analyser)
- **Helm charts**

### Enabling IaC scanning

```yaml
include:
  - template: Security/SAST-IaC.gitlab-ci.yml
```

### What IaC scanning detects

**Terraform examples:**

- S3 buckets with public access enabled
- Security groups with unrestricted inbound rules (0.0.0.0/0)
- RDS instances without encryption at rest
- IAM policies with overly permissive actions (`*`)
- CloudTrail logging disabled

**Kubernetes examples:**

- Containers running as root
- Containers with `privileged: true`
- Missing resource limits (CPU and memory)
- Services exposed on unnecessary ports
- Missing network policies

**Dockerfile examples:**

- Running as root user
- Using `ADD` instead of `COPY` for non-URL sources
- `COPY --chown` not used when needed
- Sensitive files copied into the image

---

## DAST - Dynamic Application Security Testing

DAST tests your **running application** by sending HTTP requests and analysing responses - like a controlled penetration test run automatically in your pipeline.

**Tier**: Ultimate only.

### How DAST works

Unlike SAST (which reads code), DAST actually runs your application and attacks it with a wide range of payloads and techniques:

- Cross-site scripting (XSS) - injecting JavaScript into form fields and URL parameters
- SQL injection - attempting to manipulate database queries
- Command injection - trying to execute shell commands through input fields
- Directory traversal - attempting to access files outside the web root
- Authentication bypass - testing login forms for weaknesses
- Sensitive data exposure - checking for data in responses that should not be there
- Missing security headers - checking for Content-Security-Policy, X-Frame-Options, etc.

DAST catches vulnerabilities that SAST misses because SAST analyses code while DAST analyses behaviour. A vulnerability in a third-party library might not be visible in your code but shows up when DAST attacks the running application.

### DAST v5 (browser-based)

GitLab 17.3 replaced the older proxy-based DAST with **DAST v5**, which uses a real browser (Chromium) to interact with the application. This handles JavaScript-heavy applications, SPAs and modern web frameworks much more accurately than the proxy-based approach.

```yaml
include:
  - template: DAST.gitlab-ci.yml

dast:
  variables:
    DAST_WEBSITE: https://staging.example.com
    DAST_BROWSER_SCAN: "true"
    DAST_FULL_SCAN_ENABLED: "true" # thorough scan (slower)
    # Or for a quick scan:
    # DAST_FULL_SCAN_ENABLED: "false"
```

### DAST in the pipeline

DAST requires a running instance of your application to test. The typical setup:

1. Build and deploy to a staging or review environment in an earlier pipeline stage
2. Run DAST against that environment
3. Report findings

```yaml
stages:
  - build
  - deploy
  - dast

deploy-review:
  stage: deploy
  script: ./deploy-staging.sh
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.staging.example.com

dast:
  stage: dast
  needs: ["deploy-review"]
  variables:
    DAST_WEBSITE: https://$CI_COMMIT_REF_SLUG.staging.example.com
```

### DAST API scanning

Dedicated API scanning for REST APIs using OpenAPI/Swagger spec, GraphQL schemas or Postman collections. Provides more targeted scanning than generic web scanning.

```yaml
include:
  - template: DAST-API.gitlab-ci.yml

dast_api:
  variables:
    DAST_API_OPENAPI: api-spec.yml
    DAST_API_TARGET_URL: https://staging-api.example.com
```

---

## API Security Testing

> [!IMPORTANT]
> API Security Testing is an **Ultimate feature**, separate from basic DAST API scanning.

GitLab's API Security Testing provides more comprehensive API testing than standard DAST, including:

- Full API schema-aware fuzzing (sends random and malformed data)
- Authentication testing across OAuth, API key, JWT and basic auth
- Rate limiting detection
- Data validation testing
- Business logic testing

```yaml
include:
  - template: API-Security.gitlab-ci.yml

api_security:
  variables:
    APISEC_PROFILE: Quick
    APISEC_OPENAPI: path/to/openapi.json
    APISEC_TARGET_URL: https://api.staging.example.com
```

---

## Fuzz Testing

Fuzz testing feeds random, unexpected, malformed or boundary-case inputs to your application and monitors for crashes, unhandled exceptions, memory leaks and unexpected behaviour. It finds bugs and vulnerabilities that rule-based scanners miss.

**Tier**: Ultimate only.

### Coverage-guided fuzzing

Instruments your application binary so the fuzzer can see which code paths each input exercises. It then generates new inputs that explore previously uncovered code paths - guided exploration rather than random spraying.

Supported languages: C, C++, Go, Java, JavaScript, Python, Rust, Swift.

```yaml
include:
  - template: Coverage-Fuzzing.gitlab-ci.yml

my_fuzz_target:
  extends: .fuzz_base
  script:
    - compile_fuzzing_target # compile with fuzzing instrumentation
    - run_fuzzing_target ./my-fuzz-target
  variables:
    FUZZ_CORPUS_DIR: fuzz/corpus # optional: seed corpus of example inputs
```

### Web API fuzzing

Sends automatically generated or mutated requests to a web API, looking for error responses, crashes and unexpected behaviour.

```yaml
include:
  - template: API-Fuzzing.gitlab-ci.yml

apifuzzer_fuzz:
  variables:
    FUZZAPI_PROFILE: Quick
    FUZZAPI_OPENAPI: api-spec.yml
    FUZZAPI_TARGET_URL: https://api.staging.example.com
```

> [!NOTE]
> Unlike other scanners, fuzz testing **fails the pipeline** when it finds a crash or unexpected behaviour. This is intentional - fuzz findings represent real bugs that need fixing before the code ships.

---

## License Compliance

License Compliance scans your dependencies and identifies the open source licences they use. This is critical for legal and compliance teams who need to ensure the organisation does not inadvertently ship software that violates a licence restriction.

**Tier**: Ultimate only.

### Why licence compliance matters

- **Copyleft licences** (GPL, AGPL, LGPL): may require you to open-source your own code if you distribute software using these libraries
- **Permissive licences** (MIT, Apache 2.0, BSD): generally safe for commercial use but have attribution requirements
- **Proprietary licences**: may restrict redistribution entirely
- **Incompatible licences**: some licence combinations are legally incompatible

### Enabling licence compliance

```yaml
include:
  - template: Jobs/License-Scanning.gitlab-ci.yml
```

### Licence policies

Define which licences are approved (allowed) and which are denied (blocked):

Secure → **License compliance** → **Policies** → Add policy.

- **Allowed**: acceptable licences. No action needed.
- **Denied**: unacceptable licences. The MR security widget flags any dependency using a denied licence.

**Example policy**: allow MIT, Apache 2.0 and BSD. Deny GPL, AGPL, LGPL (to protect commercial code from copyleft requirements).

---

## Vulnerability Management

**Tier**: Full vulnerability management dashboard requires Ultimate. Basic finding display in pipelines and MRs is available on all tiers.

### The vulnerability report

**Navigate**: Secure → **Vulnerability report**

The vulnerability report is a centralised view of all security findings across the project. It shows every open vulnerability found by any scanner, with:

- **Severity**: Critical, High, Medium, Low, Info, Unknown
- **Scanner**: which tool found it (SAST, DAST, Dependency Scanning, etc.)
- **Status**: Detected, Confirmed, Dismissed, Resolved, All
- **Tool**: the specific analyser that found it
- **Identifiers**: CVE number, CWE number, GitLab internal ID
- **File and line**: for SAST findings

### Vulnerability statuses

| Status        | Meaning                                                                                                   |
| ------------- | --------------------------------------------------------------------------------------------------------- |
| **Detected**  | New finding, not yet triaged                                                                              |
| **Confirmed** | Team has reviewed and confirmed this is a real vulnerability                                              |
| **Dismissed** | Team has reviewed and decided this is acceptable risk, a false positive or not applicable to this project |
| **Resolved**  | The vulnerability has been fixed                                                                          |

### Dismissing a vulnerability

When a finding is a false positive or not applicable, dismiss it with an explanation. Dismissed vulnerabilities are hidden from the active list but remain in the history.

Click a vulnerability → **Dismiss vulnerability** → select a reason:

- Acceptable risk
- False positive
- Used in tests only
- Not applicable

Add a comment explaining the decision. This creates an audit trail.

### Creating issues from vulnerabilities

For real vulnerabilities that need tracking, create a linked issue directly from the vulnerability:

Click a vulnerability → **Create issue**. GitLab creates an issue pre-populated with the vulnerability details and links it to the vulnerability record.

### Vulnerability statistics

The vulnerability report includes:

- Count by severity (Critical/High/Medium/Low)
- Trend over time (are vulnerabilities increasing or decreasing?)
- Scanner breakdown (where are findings coming from?)
- Age of findings (how long have vulnerabilities been open?)

### Group security dashboard

At the group level, the security dashboard aggregates vulnerability data across all projects in the group:

Group → **Secure → Security dashboard**

Shows organisation-wide vulnerability trends, top-vulnerability projects and severity breakdowns. Useful for security teams that need a bird's-eye view.

---

## Security Policies

**Tier**: Ultimate only.

Security policies enforce security requirements automatically - ensuring that specific scans always run before code can merge, regardless of what individual developers configure in their `.gitlab-ci.yml`.

**Navigate**: Secure → **Policies**

### Scan Execution Policies

Force specific scans to run on all pipelines in a project or group, regardless of the project's own `.gitlab-ci.yml`. Even if a developer removes the SAST job from their configuration, the scan execution policy ensures SAST still runs.

```yaml
# Example scan execution policy
name: Require SAST on all MRs
enabled: true
rules:
  - type: pipeline
    branches:
      - main
      - "release/*"
actions:
  - scan: sast
  - scan: secret_detection
```

### Merge Request Approval Policies

Require additional approvals before an MR can merge if security findings exceed a threshold.

```yaml
# Example approval policy
name: Require security approval for critical findings
enabled: true
rules:
  - type: any_merge_request
    branch_type: protected
actions:
  - type: require_approval
    approvals_required: 1
    approvers:
      - security-team
    when:
      severity:
        - critical
      scanner:
        - sast
        - dast
      status:
        - detected
```

With this policy: if any MR introduces a critical SAST or DAST finding, a member of the `security-team` group must approve before it can merge.

### Pipeline Execution Policies

Force specific CI/CD pipeline configurations to run across projects, similar to scan execution policies but more general-purpose.

---

## GitLab Advisory Database

GitLab maintains its own vulnerability database: the **GitLab Advisory Database (GLAD)**.

- Publicly accessible at `advisories.gitlab.com`
- Open source: the public edition (with a 30-day delay from discovery to publication) is MIT-licensed
- GitLab subscribes to NVD, GitHub Advisory Database, RubySec, Safety DB, OSS-Index and others, consolidating them
- GitLab is a recognised **CVE Numbering Authority (CNA)** - it can assign CVE identifiers to vulnerabilities it discovers

This database powers GitLab's dependency scanning and is kept continuously updated as new vulnerabilities are discovered and disclosed.

---

## Comparing with GitHub's Security Offering

| Feature                     | GitHub                                                  | GitLab                           |
| --------------------------- | ------------------------------------------------------- | -------------------------------- |
| **SAST**                    | CodeQL (free for public; Advanced Security for private) | Semgrep-based (Free for all)     |
| **Secret scanning**         | Free for public; Advanced Security for private          | Free for all tiers               |
| **Push protection**         | Free (global platform feature)                          | Free (opt-in per project)        |
| **Dependency scanning**     | Dependabot (free, very good)                            | Trivy-based (free)               |
| **Container scanning**      | Available (Advanced Security)                           | Free basic, Ultimate advanced    |
| **IaC scanning**            | Via community Actions                                   | Native, free                     |
| **DAST**                    | Not natively available                                  | Ultimate only                    |
| **Fuzz testing**            | Not natively available                                  | Ultimate only                    |
| **License compliance**      | Advanced Security                                       | Ultimate only                    |
| **Vulnerability dashboard** | Advanced Security                                       | Ultimate only                    |
| **Security policies**       | Advanced Security                                       | Ultimate only                    |
| **Advanced Security cost**  | $49/active committer/month                              | Included in Ultimate (~$99/user) |

**Key takeaway**: for public repositories, GitHub's security feature access is comparable to GitLab's Free tier. For private repositories requiring comprehensive security scanning, GitHub Advanced Security at $49/committer/month adds up quickly. GitLab Ultimate includes DAST and fuzz testing that GitHub does not offer at any price.

A team of 20 paying for GitHub Team + GitHub Advanced Security pays approximately $1,340/month. GitLab Ultimate for the same team is approximately $1,980/month. The gap is smaller than headline prices suggest, and GitLab includes features GitHub does not have at any price.

---

## Try It Yourself

**Exercise: Enable security scanning in 5 minutes**

Add this to your project's `.gitlab-ci.yml`:

```yaml
include:
  - template: Jobs/SAST.gitlab-ci.yml
  - template: Jobs/Secret-Detection.gitlab-ci.yml
  - template: Jobs/Dependency-Scanning.gitlab-ci.yml

stages:
  - test
  - security # templates add jobs to the "test" stage by default
    # no additional configuration needed

# Your existing jobs continue working alongside the security jobs
unit-tests:
  stage: test
  script: npm test
```

Commit and push. Navigate to **Build → Pipelines** → click the pipeline → click any security job to see its findings. Then go to **Secure → Security configuration** to see the full picture of what is enabled and what is available.

**Exercise: Test secret detection**

> [!CAUTION]
> Use a fake key for this exercise. Never commit real credentials.

1. Create a branch: `git checkout -b test/secret-detection`
2. Add a file with a fake API key:

```bash
echo 'FAKE_AWS_KEY = "AKIAIOSFODNN7EXAMPLE"' > test-config.txt
git add test-config.txt
git commit -m "test: check secret detection"
git push origin test/secret-detection
```

3. Open a merge request from that branch
4. Navigate to the pipeline → Secret Detection job
5. The fake AWS key should be flagged as a finding
6. Delete the branch and close the MR - this was just a test

---

## Common Mistakes

**Not including security templates alongside existing jobs.** Teams sometimes add `include: template: Jobs/SAST.gitlab-ci.yml` but forget to add the `test` stage (or whatever stage the template uses) to their `stages:` list. SAST jobs then fail with "stage not found". Always verify your stages list includes the stages the templates expect.

**Scanning only the default branch.** If your `rules:` restrict the security scan job to `$CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH`, you only catch vulnerabilities after merging. Include MR pipelines too: `$CI_PIPELINE_SOURCE == "merge_request_event"`. Catching vulnerabilities in the MR is far more valuable than catching them after merge.

**Not triaging findings.** Enabling scanners and then ignoring the output is worse than not scanning at all - it creates false confidence. Build a process: who reviews findings? What is the SLA for Critical findings? Use dismiss with documented reasons for false positives.

**Committing actual secrets to test secret detection.** Test with clearly fake credential patterns. Actual secrets exposed even briefly in a repository (especially a public one) may be picked up by bots within seconds. Rotate any credential before you use it in a test.

**Expecting DAST or fuzz testing on the Free tier.** DAST and fuzz testing are Ultimate only. Teams sometimes enable them based on outdated documentation and wonder why the jobs do not appear. Check your plan level at: **Settings → Usage quotas**.

**Using `CS_IMAGE: latest` in container scanning.** The `latest` tag makes it impossible to trace which image version was actually scanned. Always tag images with a specific version (`$CI_COMMIT_SHORT_SHA` is a good default) so findings can be correlated to exact builds.

**Not acting on high-severity findings.** Security scanning only helps if findings are resolved. A Critical SAST finding that sits open for months provides no protection. Create issues from critical and high findings immediately, prioritise them and track them through resolution.

---

## Summary

- GitLab security scanning runs as native CI/CD pipeline jobs, outputs structured JSON artifacts and surfaces findings in MR widgets, pipeline views and vulnerability dashboards
- **SAST** (Free): scans source code without running it. Basic: per-file. Advanced (Ultimate): cross-file, cross-function analysis. Semgrep-based for most languages. False positive detection via Duo Agent Platform in 18.10
- **Secret Detection** (Free): scans for leaked credentials in code and Git history. Push protection blocks secrets before they reach the server. 90+ secret patterns. Auto-revocation partnerships
- **Dependency Scanning** (Free basic): checks third-party packages against the GitLab Advisory Database. CycloneDX SBOM generation. Trivy-based
- **Container Scanning** (Free basic): scans Docker images for OS-level CVEs. Trivy-based. Set `CS_IMAGE` to the image you want to scan
- **IaC Scanning** (Free): detects misconfigurations in Terraform, CloudFormation, Kubernetes, Ansible, Dockerfile. Catches S3 public access, open security groups, containers running as root and more
- **DAST** (Ultimate): browser-based (Chromium) dynamic testing of running applications. DAST v5 since GitLab 17.3. Catches XSS, SQLi, auth bypass and more that SAST misses
- **Fuzz Testing** (Ultimate): coverage-guided and web API fuzzing. Fails the pipeline on crashes. Finds bugs rule-based scanners miss
- **License Compliance** (Ultimate): identifies dependency licences, enforces approved/denied lists
- **Vulnerability Management** (Ultimate): central dashboard across all scanners. Statuses: Detected, Confirmed, Dismissed, Resolved. Create issues from vulnerabilities. Group-level security dashboard
- **Security Policies** (Ultimate): enforce scan execution regardless of project configuration. Require approval for MRs introducing critical findings
- **GitLab Advisory Database**: open source (30-day delay), continuously maintained, powers dependency and container scanning. GitLab is a CVE Numbering Authority

---

## Sources and Further Reading

- [GitLab security documentation](https://docs.gitlab.com/user/application_security/) - complete security scanning reference
- [SAST documentation](https://docs.gitlab.com/user/application_security/sast/) - analyser details and configuration
- [Secret Detection](https://docs.gitlab.com/user/application_security/secret_detection/) - pipeline scanning and push protection
- [Dependency Scanning](https://docs.gitlab.com/user/application_security/dependency_scanning/) - supported languages and configuration
- [Container Scanning](https://docs.gitlab.com/user/application_security/container_scanning/) - image scanning setup
- [DAST documentation](https://docs.gitlab.com/user/application_security/dast/) - web application dynamic testing
- [Fuzz Testing](https://docs.gitlab.com/user/application_security/coverage_fuzzing/) - coverage-guided fuzzing setup
- [Vulnerability Report](https://docs.gitlab.com/user/application_security/vulnerability_report/) - managing findings
- [Security Policies](https://docs.gitlab.com/user/application_security/policies/) - enforcing security requirements
- [GitLab Advisory Database](https://advisories.gitlab.com) - public vulnerability database

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

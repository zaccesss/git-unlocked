# GitOps

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

GitOps is a way of managing infrastructure and deployments using Git as the single source of truth. Instead of manually running commands to deploy software or configure servers, you commit changes to a Git repository and automated systems apply those changes to your infrastructure. Everything is version controlled, auditable and reversible. This approach has become standard at cloud-native companies and is increasingly required knowledge for anyone working in DevOps, platform engineering or software delivery.

---

## Table of Contents

- [What is GitOps?](#what-is-gitops)
- [Core principles](#core-principles)
- [GitOps vs traditional deployment](#gitops-vs-traditional-deployment)
- [Push-based vs pull-based GitOps](#push-based-vs-pull-based-gitops)
- [GitOps with Kubernetes](#gitops-with-kubernetes)
- [GitOps tools](#gitops-tools)
- [GitOps workflow in practice](#gitops-workflow-in-practice)
- [GitOps for infrastructure with Terraform](#gitops-for-infrastructure-with-terraform)
- [GitHub Actions as a GitOps tool](#github-actions-as-a-gitops-tool)
- [GitLab CI/CD as a GitOps tool](#gitlab-cicd-as-a-gitops-tool)
- [Security in GitOps](#security-in-gitops)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitOps?

GitOps was coined by Alexis Richardson, CEO of Weaveworks, in 2017. The term describes a set of practices where:

1. **The entire system state is declared in Git.** Infrastructure configuration, Kubernetes manifests, application deployment configuration, environment variables - all stored in a Git repository.

2. **Git is the single source of truth.** The live system should always match what is in Git. If they diverge, that is a problem to be fixed.

3. **Changes are made exclusively through Git.** To change what is running in production, you open a pull request. You do not SSH into servers and run commands. You do not click buttons in a web console.

4. **Automated systems reconcile.** Software watches the Git repository and automatically applies changes to the live system when the repository changes.

The name combines Git with operations - it is Git-driven operations.

---

## Core Principles

The OpenGitOps project (a CNCF working group) defines four core principles:

**Declarative.** The desired state of the system is expressed declaratively - you describe what you want, not how to achieve it. Kubernetes YAML manifests are a canonical example.

**Versioned and immutable.** Desired state is stored in Git. All changes have commit history. Previous states can be retrieved. History is immutable.

**Pulled automatically.** Automated software agents continuously pull the desired state from Git and apply it to the live system.

**Continuously reconciled.** The agents continuously compare the live state to the desired state and correct any drift automatically.

---

## GitOps vs Traditional Deployment

**Traditional deployment (imperative):**

1. Developer merges code to main
2. CI pipeline builds and tests the code
3. Developer or CI runs a deployment script
4. Script runs `kubectl apply`, `terraform apply` or similar commands against the cluster
5. Changes are applied immediately, often without review
6. If something goes wrong, someone manually runs rollback commands

**GitOps deployment (declarative):**

1. Developer opens a PR changing the Kubernetes manifest or Terraform config
2. CI pipeline validates the change (linting, security scanning, plan output)
3. Reviewer approves the PR after reviewing the diff
4. PR is merged to main
5. GitOps operator detects the change in Git
6. Operator applies the change to the cluster automatically
7. If the live state drifts from Git (someone manually changed something), the operator detects and corrects it

The key differences: GitOps changes go through code review, have a clear audit trail and can be rolled back with `git revert`.

---

## Push-Based vs Pull-Based GitOps

**Push-based GitOps** - the CI/CD pipeline pushes changes to the environment after a merge.

```
Developer -> Git -> CI Pipeline -> Deploy to Cluster
```

GitHub Actions, GitLab CI/CD and Jenkins are push-based. When code merges, the pipeline runs and pushes the new configuration to the cluster. This is simpler to set up but requires giving the CI system credentials to access the cluster.

**Pull-based GitOps** - an agent running inside the cluster watches Git and pulls changes.

```
Developer -> Git <- Agent watches <- Cluster applies
```

Flux and ArgoCD are pull-based. An agent runs inside the cluster and continuously polls the Git repository. When it detects a change, it applies it from within the cluster. The cluster reaches out to Git rather than Git reaching into the cluster. This is more secure because no external system needs cluster credentials.

Pull-based is the dominant pattern in production GitOps implementations.

---

## GitOps with Kubernetes

Kubernetes is the most common target for GitOps. Kubernetes itself is declarative - you describe the desired state in YAML and Kubernetes works to achieve it. GitOps adds Git as the authoritative store for those YAML files.

**A typical repository structure for a GitOps Kubernetes deployment:**

```
infra-repo/
├── clusters/
│   ├── production/
│   │   ├── apps/
│   │   │   ├── web-app.yaml
│   │   │   └── api.yaml
│   │   └── infrastructure/
│   │       ├── cert-manager.yaml
│   │       └── ingress.yaml
│   └── staging/
│       ├── apps/
│       └── infrastructure/
├── base/
│   └── web-app/
│       ├── deployment.yaml
│       ├── service.yaml
│       └── kustomization.yaml
└── README.md
```

**Example Kubernetes deployment manifest (`web-app.yaml`):**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    spec:
      containers:
      - name: web-app
        image: registry.example.com/web-app:v2.1.0
        ports:
        - containerPort: 3000
```

To deploy version `v2.2.0`, you change `v2.1.0` to `v2.2.0` in this file, open a PR, get it reviewed and merge. The GitOps operator detects the change and rolls out the new version.

---

## GitOps Tools

### Flux

Flux (CNCF graduated project) is a set of controllers that runs inside a Kubernetes cluster and continuously reconciles the cluster state with a Git repository.

**Key components:**
- **Source Controller** - watches Git repositories, Helm repositories and OCI registries for changes
- **Kustomize Controller** - applies Kustomize overlays from Git
- **Helm Controller** - manages Helm chart releases
- **Image Reflector and Automation Controllers** - automatically updates image tags in Git when new container images are published

**Installing Flux:**

```bash
flux install
flux create source git my-app \
  --url=https://github.com/YOUR_ORG/infra-repo \
  --branch=main
flux create kustomization my-app \
  --source=GitRepository/my-app \
  --path="./clusters/production"
```

### ArgoCD

ArgoCD is a popular GitOps tool with a rich web UI for visualising application state and managing deployments.

**Key features:**
- Web dashboard showing live vs desired state for all applications
- Automatic or manual sync modes
- Rollback to any previous Git commit
- Multi-cluster management
- SSO integration

**Deploying an application with ArgoCD:**

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: web-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/YOUR_ORG/infra-repo
    targetRevision: main
    path: clusters/production/apps
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

`selfHeal: true` means ArgoCD automatically corrects any manual changes made directly to the cluster.

---

## GitOps for Infrastructure with Terraform

GitOps applies to infrastructure provisioning (cloud resources, databases, networking) as well as application deployment. Terraform is the most widely used infrastructure-as-code tool.

**A typical GitOps Terraform workflow:**

1. Developer opens a PR modifying a `.tf` file
2. CI runs `terraform plan` and posts the plan output as a PR comment
3. Reviewer sees exactly what will change in the cloud provider
4. PR is approved and merged
5. CI runs `terraform apply` against production
6. All changes are tracked in Git history

**Atlantis** automates this workflow - it runs as a server that listens for GitHub/GitLab webhooks and automatically runs `terraform plan` on pull requests and `terraform apply` on merge.

**Terraform Cloud** (HashiCorp's hosted service) also provides this workflow with remote state management, policy enforcement and audit logging.

---

## GitHub Actions as a GitOps Tool

GitHub Actions can implement push-based GitOps for Kubernetes deployments.

**Example: deploy to Kubernetes on push to main:**

```yaml
name: Deploy to Production

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Configure kubectl
        uses: azure/k8s-set-context@v3
        with:
          method: kubeconfig
          kubeconfig: ${{ secrets.KUBE_CONFIG }}

      - name: Deploy
        run: kubectl apply -f k8s/production/
```

---

## GitLab CI/CD as a GitOps Tool

GitLab CI/CD provides similar capabilities with built-in Kubernetes integration.

```yaml
deploy-production:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl apply -f k8s/production/
  environment:
    name: production
    url: https://app.example.com
  only:
    - main
```

GitLab also has native Kubernetes cluster integration and an **Agent for Kubernetes** that provides pull-based GitOps without exposing cluster credentials to GitLab.

---

## Security in GitOps

GitOps inherently improves security through auditability, but introduces specific security considerations.

**Secrets management.** Never store secrets (API keys, passwords, certificates) in Git, even in a private repository. Use:
- **Sealed Secrets** (Kubernetes) - encrypts secrets that can only be decrypted by the cluster
- **External Secrets Operator** - fetches secrets from AWS Secrets Manager, HashiCorp Vault etc.
- **SOPS** (Mozilla) - encrypts files in Git using GPG or cloud KMS keys

**Branch protection.** The Git repository controlling production infrastructure must have strict branch protection:
- Require pull requests for all changes to `main`
- Require at least one reviewer approval
- Require passing CI checks
- Prevent force pushes
- Restrict who can merge

**Least privilege.** The GitOps agent's service account should have only the permissions it needs. It should not have cluster-admin unless genuinely required.

> [!CAUTION]
> A Git repository that controls production infrastructure is a high-value target. Treat it with the same security rigour as production system access. Enable branch protection, require signed commits, audit all access and rotate credentials regularly.

---

## Try It Yourself

A full GitOps setup requires a Kubernetes cluster. This exercise demonstrates the Git side of GitOps - structuring a repository for GitOps and understanding the workflow - without requiring a live cluster.

**Step 1.** Create a GitOps repository structure:

**Windows (PowerShell):**

```powershell
mkdir gitops-practice && cd gitops-practice && git init
mkdir -p clusters/production/apps clusters/staging/apps base/web-app
```

**Mac and Linux:**

```bash
mkdir gitops-practice && cd gitops-practice && git init
mkdir -p clusters/production/apps clusters/staging/apps base/web-app
```

**Step 2.** Create a base deployment manifest:

Create `base/web-app/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web-app
  template:
    spec:
      containers:
      - name: web-app
        image: nginx:1.25.0
```

**Step 3.** Create environment-specific overlays:

Create `clusters/production/apps/web-app.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: production
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: web-app
        image: nginx:1.25.0
```

Create `clusters/staging/apps/web-app.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: staging
spec:
  replicas: 1
  template:
    spec:
      containers:
      - name: web-app
        image: nginx:1.25.0
```

**Step 4.** Commit the initial state:

```bash
git add .
git commit -m "add: initial GitOps repository structure"
```

**Step 5.** Simulate a deployment change - update the image version:

Edit `clusters/production/apps/web-app.yaml` and change `nginx:1.25.0` to `nginx:1.26.0`.

```bash
git add clusters/production/apps/web-app.yaml
git commit -m "update: bump nginx to 1.26.0 in production"
```

**Step 6.** Simulate a rollback - revert the change:

```bash
git revert HEAD --no-edit
git log --oneline
```

The rollback is a new commit. The history is preserved. In a real GitOps setup, the operator would detect this revert commit and roll back the running deployment automatically.

**Step 7.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q gitops-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf gitops-practice
```

---

## Common Mistakes

**Storing secrets in the GitOps repository.**

This is the most critical mistake. Even encrypted at rest, a Git repository with production secrets is a high-value target. Use Sealed Secrets, External Secrets Operator or SOPS to manage secrets outside Git.

**Not protecting the infrastructure repository.**

The Git repository that controls production is more sensitive than most application code. It needs the same branch protection rules as you would apply to production code - PR required, approvals required, CI required, no force pushes.

**Applying changes manually to the cluster.**

If you need to debug an issue in production and make a change directly with `kubectl`, you have created drift between Git and the live state. Always make the same change in Git (even via a quick PR) so the repository stays accurate. Pull-based tools like Flux and ArgoCD will actually revert your manual changes if `selfHeal` is enabled.

**Not having a clear environment promotion strategy.**

Most teams have staging and production environments. Define how changes flow between them - feature branches to staging, PRs to merge staging to production, etc. Without a clear strategy, GitOps becomes confusing.

**Using GitOps for everything before the team is ready.**

GitOps requires discipline and tooling. For a small team not yet using Kubernetes or infrastructure-as-code, traditional CI/CD may be more appropriate. Adopt GitOps when the complexity is justified by the benefits.

---

## Summary

- GitOps uses Git as the single source of truth for system state - infrastructure and application configuration are stored in Git and automated systems apply changes
- Core principles: declarative desired state, version controlled in Git, pulled automatically by agents, continuously reconciled
- **Pull-based GitOps** (Flux, ArgoCD) is more secure - agents inside the cluster pull from Git
- **Push-based GitOps** (GitHub Actions, GitLab CI/CD) pushes from the CI pipeline to the cluster
- Flux and ArgoCD are the leading Kubernetes GitOps operators
- Terraform + Atlantis or Terraform Cloud implement GitOps for infrastructure provisioning
- Secrets must never be stored in Git - use Sealed Secrets, External Secrets Operator or SOPS
- Over 64% of organisations surveyed in 2025 had adopted GitOps practices

---

## Sources and Further Reading

- [OpenGitOps principles](https://opengitops.dev) - the official GitOps principles defined by the CNCF working group
- [Flux documentation](https://fluxcd.io/docs/) - the official guide to Flux
- [ArgoCD documentation](https://argo-cd.readthedocs.io) - the official guide to ArgoCD
- [OpenGitOps specification](https://github.com/open-gitops/documents) - the CNCF working group's GitOps principles and specification documents
- [Atlantis](https://www.runatlantis.io) - pull request automation for Terraform
- [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets) - Kubernetes secrets management for GitOps
- [SOPS](https://github.com/getsops/sops) - Mozilla's secrets management tool for Git
- [CNCF GitOps Working Group](https://github.com/cncf/tag-app-delivery/tree/main/gitops-wg) - the standards body for GitOps

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

# GitOps workflows

**Difficulty:** 🟡 Intermediate to 🔴 Advanced | **Time:** 45 minutes

---

## Table of contents

- [Introduction](#introduction)
- [What GitOps is - and what it is not](#what-gitops-is---and-what-it-is-not)
- [The four OpenGitOps principles](#the-four-opengitops-principles)
- [ArgoCD](#argocd)
- [Flux CD](#flux-cd)
- [ArgoCD vs Flux: choosing between them](#argocd-vs-flux-choosing-between-them)
- [Environment promotion patterns](#environment-promotion-patterns)
- [Kargo: the promotion layer](#kargo-the-promotion-layer)
- [Real-world adoption](#real-world-adoption)
- [Try it yourself](#try-it-yourself)
- [Common mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources](#sources)

---

## Introduction

GitOps is the practice of using Git as the single source of truth for the desired state of your infrastructure and applications. Instead of running `kubectl apply` commands or clicking through a cloud console, you commit the desired state to a Git repository and let a software agent continuously reconcile the live system to match it.

The result is infrastructure that is version-controlled, auditable, reviewable (via PRs), automatically recovered when someone makes a manual change and consistent across environments.

This file assumes basic familiarity with Kubernetes. If you have not used Kubernetes before, the concepts still apply but the tooling examples will make more sense after you have worked through a basic Kubernetes tutorial.

**What you need:**

- A Kubernetes cluster (local: `kind`, `k3d` or Docker Desktop; cloud: any managed Kubernetes service)
- `kubectl` installed and configured
- A GitHub or GitLab account for the configuration repository

---

## What GitOps is - and what it is not

**GitOps is:**

- The Git repository is the source of truth for desired state
- A software agent running inside the cluster watches the repository and applies changes
- The agent continuously reconciles actual state to desired state, even if someone makes a manual change
- All changes go through Git (PRs, review, history)

**GitOps is not:**

- CI/CD pipelines that push deployments (`kubectl apply` from a GitHub Actions job is **CIOps**, not GitOps)
- Just storing your YAML files in Git
- A specific tool - it is a set of principles that multiple tools implement

The critical distinction is **pull-based vs push-based** reconciliation. In a push-based CI/CD pipeline, the CI system holds credentials to the cluster and pushes changes into it. In GitOps, the agent inside the cluster pulls desired state from Git and applies it locally - the cluster is never exposed to external push credentials.

---

## The four OpenGitOps principles

The formal GitOps specification is maintained by the **OpenGitOps** project, a CNCF Sandbox initiative. Version 1.0.0 was published at [opengitops.dev](https://opengitops.dev/) and the principles live at [github.com/open-gitops/documents](https://github.com/open-gitops/documents/blob/main/PRINCIPLES.md).

**1. Declarative**

The desired state of the system must be expressed as data (YAML, JSON, HCL), not as imperative scripts or commands. "The deployment should have three replicas of image X" is declarative. "Run `kubectl scale deployment X --replicas=3`" is imperative and cannot be reconciled.

**2. Versioned and immutable**

Desired state is stored in an immutable versioned system - a Git repository. Every change has a commit hash, an author, a timestamp and a message. You can `git revert` any change. You can `git diff` between any two states.

**3. Pulled automatically**

Software agents automatically pull the desired state from the versioned store and apply it to the system. The system does not wait for a deployment trigger. The agent polls continuously (or receives a webhook) and applies any new desired state it finds.

**4. Continuously reconciled**

The agent continuously observes actual state and corrects any drift. If someone runs `kubectl edit deployment myapp` and changes the replica count, the agent will detect the drift and restore the desired state within seconds. This eliminates configuration drift as a problem class.

---

## ArgoCD

ArgoCD ([argo-cd.readthedocs.io](https://argo-cd.readthedocs.io/)) is a Kubernetes-native GitOps controller with a web UI, CLI and rich RBAC. It is the most widely adopted GitOps tool as of 2026, maintained by the Argo Project under CNCF and heavily supported by Akuity (founded by the original Intuit engineers who created it).

**Current stable version: v3.3.7 (released 2026-04-16)**

### Core concepts

- **Application** - an ArgoCD resource that maps a Git source (repository URL, path, revision) to a Kubernetes destination (cluster, namespace) with a sync policy
- **AppProject** - groups Applications with shared access controls, source restrictions and destination restrictions
- **ApplicationSet** - a template that generates many Applications from a list, cluster inventory, Git directory scan, pull request list or matrix generator
- **Sync** - the act of applying desired state from Git to the cluster
- **Self-heal** - automatically syncing when drift is detected
- **Auto-prune** - deleting Kubernetes resources that are no longer in Git

### Installing ArgoCD

```bash
# 🪟 Windows / 🍎 Mac / 🐧 Linux (kubectl required)
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/v3.3.7/manifests/install.yaml

# Wait for all pods to be running
kubectl wait --for=condition=Ready pods --all -n argocd --timeout=300s

# Port-forward the UI
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Get the initial admin password
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d
```

Navigate to `https://localhost:8080`, log in with `admin` and the password above.

### Installing the ArgoCD CLI

```bash
# 🍎 Mac
brew install argocd

# 🐧 Linux
curl -sSL -o /usr/local/bin/argocd \
  https://github.com/argoproj/argo-cd/releases/download/v3.3.7/argocd-linux-amd64
chmod +x /usr/local/bin/argocd

# 🪟 Windows (winget)
winget install Argo.ArgoCD
```

### Creating an Application

```bash
# Log in via CLI
argocd login localhost:8080 --username admin --password <password> --insecure

# Create an application pointing to the guestbook example
argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps.git \
  --path guestbook \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace guestbook \
  --sync-policy automated \
  --auto-prune \
  --self-heal

# View status
argocd app get guestbook
argocd app sync guestbook
```

The equivalent as a Kubernetes manifest (the GitOps way - store this in Git and apply it once):

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps.git
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: guestbook
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

### ApplicationSet for multi-environment deployments

```yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: guestbook-environments
  namespace: argocd
spec:
  generators:
    - list:
        elements:
          - environment: dev
            cluster: https://dev-cluster.example.com
          - environment: staging
            cluster: https://staging-cluster.example.com
          - environment: prod
            cluster: https://prod-cluster.example.com
  template:
    metadata:
      name: "guestbook-{{environment}}"
    spec:
      project: default
      source:
        repoURL: https://github.com/org/config-repo.git
        targetRevision: HEAD
        path: "envs/{{environment}}/guestbook"
      destination:
        server: "{{cluster}}"
        namespace: guestbook
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

---

## Flux CD

Flux ([fluxcd.io](https://fluxcd.io/)) is the other dominant GitOps tool, a CNCF-graduated project maintained by the community after Weaveworks (who coined "GitOps") shut down in February 2024. Flux's key philosophical difference from ArgoCD is that it is a **toolkit of composable controllers** rather than a monolithic application.

**Current stable version: v2.8.2 (released 2026-02-24)**

### Core controllers

- `source-controller` - watches Git repositories, Helm repositories and OCI registries for new versions
- `kustomize-controller` - applies Kustomize manifests from a `GitRepository` or `OCIRepository` source
- `helm-controller` - reconciles Helm releases declaratively
- `notification-controller` - sends alerts and receives webhook triggers
- `image-reflector-controller` and `image-automation-controller` - scan container registries for new image tags and commit image-tag bumps back to Git automatically

### Installing Flux

```bash
# Install the Flux CLI
# 🍎 Mac
brew install fluxcd/tap/flux

# 🐧 Linux
curl -s https://fluxcd.io/install.sh | sudo bash

# 🪟 Windows (winget)
winget install fluxcd.flux

# Verify prerequisites
flux check --pre

# Bootstrap Flux onto your cluster (GitHub example)
export GITHUB_TOKEN=<your-pat>
flux bootstrap github \
  --owner=YOUR_ORG \
  --repository=fleet-infra \
  --branch=main \
  --path=clusters/my-cluster \
  --personal
```

`flux bootstrap` installs the Flux controllers, creates a `fleet-infra` repository (or uses an existing one), commits the Flux manifests to `clusters/my-cluster/` and configures the cluster to reconcile from that path. From this point, any change to `clusters/my-cluster/` in the `fleet-infra` repo is automatically applied to the cluster.

### Defining a GitRepository and Kustomization

```yaml
# clusters/my-cluster/apps/source.yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: my-app
  namespace: flux-system
spec:
  interval: 1m
  url: https://github.com/org/my-app-config.git
  ref:
    branch: main
---
# clusters/my-cluster/apps/kustomization.yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1
kind: Kustomization
metadata:
  name: my-app
  namespace: flux-system
spec:
  interval: 10m
  path: ./envs/prod
  prune: true
  sourceRef:
    kind: GitRepository
    name: my-app
  targetNamespace: my-app
```

Commit these files to `fleet-infra`. Flux detects the new manifests and begins reconciling `my-app-config` into the cluster.

---

## ArgoCD vs Flux: choosing between them

| Dimension | ArgoCD | Flux |
|---|---|---|
| UI | Rich web UI built in | No built-in UI; use Capacitor or Flux Operator UI |
| Architecture | Monolithic controller + API server | Composable toolkit of controllers |
| Multi-cluster | ApplicationSet generators | Tenant model; Flux on each cluster |
| SOPS secrets | Plugin (argocd-vault-plugin or built-in 3.0+) | First-class native SOPS integration |
| Helm support | Native (no extra controller) | `helm-controller` |
| Image automation | External tool needed | Built-in `image-automation-controller` |
| Onboarding | Easier (UI, richer docs) | More Kubernetes-native, steeper curve |
| Stars (April 2026) | ~22,700 | ~6,500 |
| CNCF status | Graduated | Graduated |

**Choose ArgoCD if:** you want a UI, you are managing many clusters from a central place or you are onboarding a team that is new to GitOps.

**Choose Flux if:** you want pure Kubernetes-native CRDs with no API server dependency, you need first-class SOPS secrets integration or you want image-tag automation that writes back to Git.

Both are production-proven at scale. Both are CNCF-graduated. Either is a solid choice.

---

## Environment promotion patterns

### Folder-per-environment with Kustomize overlays (recommended)

The most widely adopted pattern. Your configuration repository has a `base/` directory with common manifests and an `overlays/` directory with environment-specific patches.

```
config-repo/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   ├── kustomization.yaml       # patches base, sets dev image tag
    │   └── replica-count.yaml      # dev: 1 replica
    ├── staging/
    │   ├── kustomization.yaml
    │   └── replica-count.yaml      # staging: 2 replicas
    └── prod/
        ├── kustomization.yaml
        └── replica-count.yaml      # prod: 3 replicas
```

```yaml
# overlays/prod/kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
images:
  - name: myapp
    newTag: v2.3.1
patches:
  - path: replica-count.yaml
```

Promotion is a PR: change the `newTag` in `overlays/staging/kustomization.yaml` from `v2.3.0` to `v2.3.1`. After review and merge, ArgoCD/Flux detects the change and updates staging.

### Hydrated/rendered manifests pattern (ArgoCD Source Hydrator)

Available since ArgoCD v3.1+. CI renders your templates (Helm, Kustomize, CUE) into final YAML and commits the output to a `hydrated` branch. ArgoCD deploys from the hydrated branch, not the template source. This decouples the templating tool from the GitOps controller and makes auditing simpler (the deployed manifests are exactly what is in Git).

### Branch-per-environment (falling out of favour)

Maintaining a `dev`, `staging` and `prod` branch and promoting via merge or cherry-pick worked when teams had two or three environments. At more than three environments or with feature flags, branch-per-environment breeds merge conflicts and cognitive overhead. Folder-per-environment scales better.

---

## Kargo: the promotion layer

**Kargo** ([kargo.io](https://kargo.io/)) from Akuity addresses the multi-stage promotion problem that ArgoCD's `Application` model does not solve directly. Its primitives are:

- `Warehouse` - subscribes to artifact sources (Git commits, container image tags, Helm chart versions)
- `Stage` - a deployment target (dev, staging, prod)
- `Freight` - a bundle of specific artifact versions (a Git commit + an image tag + a chart version) that moves through stages together
- `Promotion` - the act of advancing a `Freight` from one stage to the next

```yaml
apiVersion: kargo.akuity.io/v1alpha1
kind: Stage
metadata:
  name: staging
  namespace: my-app
spec:
  requestedFreight:
    - origin:
        kind: Warehouse
        name: my-app-warehouse
      sources:
        stages: [dev]          # only promote freight that passed dev
  promotionTemplate:
    spec:
      steps:
        - uses: git-clone
        - uses: kustomize-set-image
          config:
            images:
              - image: myapp
        - uses: git-commit
        - uses: git-push
        - uses: argocd-update
```

Kargo v1.9 (2026) added infrastructure-aware promotions integrating with Terraform and OpenTofu.

---

## Real-world adoption

**Intuit** created ArgoCD in 2018 (via the Applatix acquisition) and runs it across hundreds of Kubernetes clusters to handle tax-season traffic spikes at massive scale. The ArgoCD founders now run Akuity, the commercial support company.

**Red Hat OpenShift GitOps** ships ArgoCD as a supported component, bringing GitOps to every OpenShift cluster by default.

**Adobe, BlackRock, Capital One, the New York Times and Goldman Sachs** all publicly use ArgoCD.

**Weaveworks** coined the term "GitOps" in 2017 and created Flux. The company shut down in February 2024, but Flux survived as a CNCF-graduated community project. GitLab, Microsoft (AKS), AWS (EKS Anywhere) and Aenix continue to back it.

**Progressive delivery** - deploying to a small percentage of traffic first, then expanding - is the natural extension of GitOps. **Argo Rollouts** provides explicit step-based canary and blue-green deployments in the Argo ecosystem. **Flagger** provides automatic canary analysis over standard `Deployment`s in the Flux ecosystem.

---

## Try it yourself

This exercise uses `kind` (Kubernetes in Docker) to run a local cluster. If you have Docker installed, this works on 🪟 Windows, 🍎 Mac and 🐧 Linux.

```bash
# Install kind
# 🍎 Mac
brew install kind

# 🐧 Linux
curl -Lo /usr/local/bin/kind \
  https://kind.sigs.k8s.io/dl/v0.27.0/kind-linux-amd64
chmod +x /usr/local/bin/kind

# 🪟 Windows (winget)
winget install Kubernetes.kind

# Create a cluster
kind create cluster --name gitops-demo

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/v3.3.7/manifests/install.yaml

# Wait for ArgoCD to be ready
kubectl wait --for=condition=Ready pods --all -n argocd --timeout=300s

# Port-forward and log in
kubectl port-forward svc/argocd-server -n argocd 8080:443 &
kubectl get secret argocd-initial-admin-secret -n argocd \
  -o jsonpath="{.data.password}" | base64 -d

# Deploy the guestbook example
argocd login localhost:8080 --username admin --insecure \
  --password $(kubectl get secret argocd-initial-admin-secret -n argocd \
    -o jsonpath="{.data.password}" | base64 -d)

argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps.git \
  --path guestbook \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace guestbook \
  --sync-policy automated --auto-prune --self-heal

# Watch ArgoCD sync the app
argocd app get guestbook
```

**Bonus exercise:** manually change the replica count with `kubectl scale deployment guestbook-ui --replicas=5 -n guestbook`, then watch ArgoCD detect the drift and restore it to the desired count from Git.

---

## Common mistakes

**Pushing directly to the cluster from CI (CIOps, not GitOps).**
Running `kubectl apply` from a GitHub Actions job is the most common GitOps anti-pattern. It requires the CI system to hold cluster credentials, removes the reconciliation loop and does not heal drift. Move the `kubectl apply` into an ArgoCD `Application` or Flux `Kustomization` and commit changes to Git instead.

**Storing secrets in Git unencrypted.**
Git commits are permanent and often public. Never commit raw Kubernetes `Secret` YAML to a GitOps repository. Use **SOPS** with age or AWS KMS (first-class in Flux; supported via plugins in ArgoCD), **External Secrets Operator** (syncs from Vault, AWS Secrets Manager, GCP Secret Manager) or **Sealed Secrets** (encrypt before committing, decrypt in-cluster).

**Branch-per-environment at scale.**
Branch-per-environment makes sense for two or three environments. With six or more, maintaining merge consistency becomes a full-time job. Switch to folder-per-environment with Kustomize overlays.

**Forgetting `--auto-prune` in ArgoCD.**
Without auto-prune, resources deleted from Git persist in the cluster. This causes environment drift and can leave security-sensitive resources running after they have been intentionally removed from the desired state. Enable prune in your sync policy.

**Not bootstrapping ArgoCD/Flux itself via GitOps.**
A common mistake is to install ArgoCD manually and then manage its configuration via the UI. If your ArgoCD installation is not itself managed by Git (the "app of apps" or ApplicationSet bootstrap pattern), you lose the audit trail and repeatability that GitOps promises. Manage ArgoCD's own configuration as an ArgoCD `Application`.

---

## Summary

GitOps means using a Git repository as the single source of truth for desired system state, with a software agent continuously reconciling actual state to match it. The four OpenGitOps principles - declarative, versioned, pulled automatically, continuously reconciled - distinguish true GitOps from CI pipelines that merely store YAML in Git.

The two dominant tools are **ArgoCD** (rich UI, central multi-cluster management, strongest adoption) and **Flux** (composable controllers, first-class SOPS, image automation). Both are CNCF-graduated and production-proven at scale.

The recommended environment promotion pattern is folder-per-environment with Kustomize overlays. Promotion is a PR that updates an image tag or configuration value in the target environment's overlay. For multi-stage promotion with gates between stages, **Kargo** from the ArgoCD creators provides a dedicated promotion layer.

---

## Sources and Further Reading

- [OpenGitOps principles](https://github.com/open-gitops/documents/blob/main/PRINCIPLES.md)
- [opengitops.dev](https://opengitops.dev/)
- [ArgoCD documentation](https://argo-cd.readthedocs.io/)
- [ArgoCD v3.3 release notes](https://blog.argoproj.io/argo-cd-3-3-release-candidate-00e99f7b7daa)
- [Flux documentation](https://fluxcd.io/flux/)
- [Flux v2.8 release notes](https://github.com/fluxcd/flux2/releases/tag/v2.8.2)
- [Kargo](https://kargo.io/)
- [kind - Kubernetes in Docker](https://kind.sigs.k8s.io/)
- [SOPS: Mozilla Secrets OPerationS](https://github.com/getsops/sops)
- [External Secrets Operator](https://external-secrets.io/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

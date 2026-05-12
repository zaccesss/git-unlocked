# Azure Pipelines

**Difficulty:** 🔴 Advanced | **Time:** 60 minutes

Azure Pipelines is Microsoft's CI/CD service. It is one of the most capable CI/CD systems available - supporting any language, any platform and any cloud provider. Pipelines are defined in YAML files committed to your repository, giving you pipeline-as-code with version history and code review just like your application code.

This file covers the complete Azure Pipelines YAML syntax, Microsoft-hosted agent images, self-hosted agents, multi-stage pipelines with deployment environments, pipeline templates for reuse, variables and variable groups, service connections and the key features that make Azure Pipelines enterprise-grade.

---

## Table of Contents

1. [How Azure Pipelines works](#how-azure-pipelines-works)
2. [The azure-pipelines.yml file](#the-azure-pipelinesyml-file)
3. [Triggers](#triggers)
4. [Agents and pools](#agents-and-pools)
5. [Stages, jobs and steps](#stages-jobs-and-steps)
6. [Tasks](#tasks)
7. [Variables](#variables)
8. [Conditions](#conditions)
9. [Artifacts](#artifacts)
10. [Environments and deployments](#environments-and-deployments)
11. [Templates for reuse](#templates-for-reuse)
12. [Service connections](#service-connections)
13. [Pipeline security](#pipeline-security)
14. [Managed DevOps Pools](#managed-devops-pools)
15. [Classic pipelines (legacy)](#classic-pipelines-legacy)
16. [Practical pipeline examples](#practical-pipeline-examples)
17. [Try It Yourself](#try-it-yourself)
18. [Common Mistakes](#common-mistakes)
19. [Summary](#summary)
20. [Sources](#sources)

---

## How Azure Pipelines works

When you push a commit to Azure Repos (or a connected GitHub repository), Azure Pipelines evaluates whether a pipeline should run based on trigger conditions defined in `azure-pipelines.yml`. If triggered, it:

1. Allocates an agent (Microsoft-hosted or self-hosted)
2. Checks out the repository onto the agent
3. Executes stages, jobs and steps in the order defined
4. Reports results back to Azure DevOps (pass/fail, logs, test results, artifacts)

The pipeline result appears on the commit, on any linked pull request and on the Pipelines page in Azure DevOps.

**Parallel jobs** determine how many pipeline runs can execute simultaneously. One parallel job means only one pipeline runs at a time across your entire organisation - others queue. The free tier includes one Microsoft-hosted parallel job.

---

## The azure-pipelines.yml file

The pipeline configuration is a YAML file at the root of your repository. The file is committed alongside your code and versioned with it.

### Minimal valid pipeline

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - script: echo "Hello from Azure Pipelines"
    displayName: 'Say hello'
```

This runs whenever a commit is pushed to `main`, using a Microsoft-hosted Ubuntu 24.04 agent, and executes a single shell command.

### Full structure overview

```yaml
name: $(BuildDefinitionName)_$(Date:yyyyMMdd)$(Rev:.r)

trigger:              # what events run this pipeline
  branches:
    include:
      - main
      - release/*

pr:                   # pull request triggers
  branches:
    include:
      - main

variables:            # pipeline-level variables
  buildConfiguration: Release
  dotnetVersion: '8.x'

stages:               # top-level grouping
  - stage: Build
    jobs:
      - job: BuildJob
        pool:
          vmImage: windows-latest
        steps:
          - task: DotNetCoreCLI@2
            inputs:
              command: build

  - stage: Deploy
    dependsOn: Build
    condition: succeeded()
    jobs:
      - deployment: DeployToStaging
        environment: staging
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploying..."
```

---

## Triggers

### CI triggers (push)

```yaml
trigger:
  branches:
    include:
      - main
      - develop
      - release/*
    exclude:
      - feature/experimental-*
  paths:
    include:
      - src/**
      - tests/**
    exclude:
      - docs/**
      - '*.md'
  tags:
    include:
      - v*
```

**Branch filters**: Run only on specific branches. Use `*` as a wildcard within a segment, `**` for multi-segment matching.

**Path filters**: Run only when specific files change. If all changed files match the exclude list, the pipeline does not run.

**Tag triggers**: Run when a tag is pushed. Useful for release pipelines triggered by version tags.

**Disable CI trigger:**
```yaml
trigger: none
```

### Pull request triggers

```yaml
pr:
  branches:
    include:
      - main
      - develop
  paths:
    exclude:
      - docs/**
```

PR pipelines run when a pull request is opened or updated with new commits. The pipeline result appears as a status check on the PR. Branch policies can require this check to pass before the PR can be merged.

**Disable PR trigger:**
```yaml
pr: none
```

### Scheduled triggers

```yaml
schedules:
  - cron: '0 2 * * 1-5'    # 2 AM UTC Monday-Friday
    displayName: 'Nightly build'
    branches:
      include:
        - main
    always: true             # run even if no new commits
```

Cron syntax follows standard cron format: `minute hour day-of-month month day-of-week`.

### Manual triggers

Pipelines can always be triggered manually from the Azure Pipelines UI regardless of trigger configuration. Click **Run pipeline** on any pipeline to trigger it manually, optionally overriding variables.

---

## Agents and pools

An **agent** is the compute resource that runs your pipeline. An **agent pool** is a collection of agents.

### Microsoft-hosted agents

Microsoft manages these agents - they are provisioned fresh for each job and torn down after. No maintenance required.

| Image alias | Operating system | Includes |
|---|---|---|
| `ubuntu-latest` | Ubuntu 24.04 LTS | Python, Node, Java, Go, Docker, Git, common tools |
| `ubuntu-22.04` | Ubuntu 22.04 LTS | Same |
| `windows-latest` | Windows Server 2025 | VS 2022, .NET SDK, NuGet, PowerShell, Chocolatey |
| `windows-2022` | Windows Server 2022 | VS 2022, .NET SDK |
| `macos-latest` | macOS 15 Sequoia | Xcode, Homebrew, Python, Node, Java |
| `macos-14` | macOS 14 Sonoma | Xcode 15, same tools |

> [!WARNING]
> **Retired agents**: Windows Server 2019 (`windows-2019`) was retired December 31, 2025. Ubuntu 20.04 was retired May 2025. macOS 13 was retired December 2025. Using retired image names causes pipelines to fail. Update to current images.

### Specifying the agent

```yaml
pool:
  vmImage: ubuntu-latest      # Microsoft-hosted

# or

pool:
  name: Default               # self-hosted pool named "Default"

# or

pool:
  name: MyAgents
  demands:
    - Agent.OS -equals Windows_NT
    - VisualStudio -isinstalled
```

### Self-hosted agents

For Windows/macOS builds, builds requiring private network access, specialised hardware or builds that run too slowly on Microsoft-hosted agents, set up self-hosted agents.

**Installing an agent:**

🪟 **Windows** (PowerShell):
```powershell
# Download agent
mkdir agent ; cd agent
Invoke-WebRequest -Uri https://vstsagentpackage.azureedge.net/agent/latest/vsts-agent-win-x64-latest.zip -OutFile agent.zip
Expand-Archive agent.zip -DestinationPath .

# Configure (replace with your org URL and PAT)
.\config.cmd --url https://dev.azure.com/your-org `
             --auth pat `
             --token YOUR_PAT `
             --pool Default `
             --agent MyWindowsAgent `
             --runAsService
```

🍎 **Mac**:
```bash
mkdir myagent && cd myagent
curl -O https://vstsagentpackage.azureedge.net/agent/latest/vsts-agent-osx-x64-latest.tar.gz
tar zxvf vsts-agent-osx-x64-latest.tar.gz

./config.sh --url https://dev.azure.com/your-org \
            --auth pat \
            --token YOUR_PAT \
            --pool Default \
            --agent MyMacAgent

./run.sh
```

🐧 **Linux**:
```bash
mkdir myagent && cd myagent
curl -O https://vstsagentpackage.azureedge.net/agent/latest/vsts-agent-linux-x64-latest.tar.gz
tar zxvf vsts-agent-linux-x64-latest.tar.gz

./config.sh --url https://dev.azure.com/your-org \
            --auth pat \
            --token YOUR_PAT \
            --pool Default \
            --agent MyLinuxAgent

./run.sh
```

After running `./run.sh` (or the Windows equivalent), the agent appears in the agent pool in Azure DevOps and begins picking up jobs.

---

## Stages, jobs and steps

Azure Pipelines has three levels of execution:

```
Stage
  └── Job
        └── Step
```

**Stage**: The highest level grouping. Stages run sequentially by default. Use stages to separate Build, Test, Deploy to Staging and Deploy to Production.

**Job**: A set of steps that run on a single agent. Jobs within a stage run in parallel by default. Use jobs to parallelise work within a stage (e.g. multiple test suites running concurrently).

**Step**: An individual action - a script command or a task. Steps within a job run sequentially.

### Stages

```yaml
stages:
  - stage: Build
    displayName: 'Build the application'
    jobs:
      - job: Compile
        steps:
          - script: dotnet build

  - stage: Test
    displayName: 'Run tests'
    dependsOn: Build          # run after Build completes
    condition: succeeded()    # only run if Build succeeded
    jobs:
      - job: UnitTests
        steps:
          - script: dotnet test
      - job: IntegrationTests   # runs in parallel with UnitTests
        steps:
          - script: dotnet test --filter Category=Integration

  - stage: DeployStaging
    displayName: 'Deploy to Staging'
    dependsOn: Test
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployToStaging
        environment: staging
        strategy:
          runOnce:
            deploy:
              steps:
                - script: ./deploy.sh staging
```

### Jobs

```yaml
jobs:
  - job: BuildLinux
    displayName: 'Build on Linux'
    pool:
      vmImage: ubuntu-latest
    steps:
      - script: make build

  - job: BuildWindows
    displayName: 'Build on Windows'
    pool:
      vmImage: windows-latest
    steps:
      - script: msbuild MyApp.sln

  - job: TestAfterBuild
    displayName: 'Test'
    dependsOn:
      - BuildLinux
      - BuildWindows        # waits for both builds to complete
    condition: succeeded()
    steps:
      - script: make test
```

**Job timeout**: Set maximum job duration:
```yaml
- job: LongBuild
  timeoutInMinutes: 120    # default is 60, max is 360
  steps:
    - script: make all
```

**Cancel timeout**: How long to wait for cancellation to complete:
```yaml
cancelTimeoutInMinutes: 5
```

### Steps

Steps are the individual commands or tasks that execute within a job.

```yaml
steps:
  - checkout: self          # check out the repository (done automatically if omitted)

  - script: npm install     # inline shell command
    displayName: 'Install dependencies'
    workingDirectory: frontend/

  - bash: |                 # multi-line bash
      echo "Building..."
      npm run build
      echo "Done"
    displayName: 'Build frontend'
    condition: succeededOrFailed()   # run even if previous steps failed

  - pwsh: |                 # PowerShell Core (cross-platform)
      Write-Host "PowerShell on any OS"
    displayName: 'PowerShell step'

  - task: CopyFiles@2       # built-in task
    inputs:
      sourceFolder: '$(Build.SourcesDirectory)'
      contents: '**/*.dll'
      targetFolder: '$(Build.ArtifactStagingDirectory)'
```

---

## Tasks

Tasks are pre-built steps for common operations. They abstract complex operations into simple YAML blocks. Microsoft provides hundreds of tasks; more are available in the Azure DevOps Marketplace.

### Common built-in tasks

**Checkout:**
```yaml
- checkout: self
  clean: true              # wipe workspace before checkout
  fetchDepth: 1            # shallow clone (faster for large repos)
  lfs: true                # fetch LFS objects
  submodules: true         # initialise submodules
```

**Script tasks:**
```yaml
- script: echo "Hello"     # bash on Linux/Mac, cmd on Windows
- bash: echo "Hello"       # always bash
- pwsh: Write-Host "Hello" # PowerShell Core (cross-platform)
- powershell: Write-Host   # Windows PowerShell only
```

**.NET tasks:**
```yaml
- task: UseDotNet@2
  inputs:
    version: '8.x'
    includePreviewVersions: false

- task: DotNetCoreCLI@2
  inputs:
    command: restore
    projects: '**/*.csproj'

- task: DotNetCoreCLI@2
  inputs:
    command: build
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration)'

- task: DotNetCoreCLI@2
  inputs:
    command: test
    projects: '**/*Tests.csproj'
    arguments: '--configuration $(buildConfiguration) --collect:"XPlat Code Coverage"'
    publishTestResults: true
```

**Node.js tasks:**
```yaml
- task: NodeTool@0
  inputs:
    versionSpec: '20.x'

- script: npm ci
  displayName: 'npm install'

- script: npm test
  displayName: 'Run tests'
```

**Docker tasks:**
```yaml
- task: Docker@2
  inputs:
    containerRegistry: 'myDockerHubServiceConnection'
    repository: 'myorg/myapp'
    command: buildAndPush
    Dockerfile: '**/Dockerfile'
    tags: |
      $(Build.BuildNumber)
      latest
```

**Azure deployment tasks:**
```yaml
- task: AzureWebApp@1
  inputs:
    azureSubscription: 'myAzureServiceConnection'
    appName: 'my-web-app'
    package: '$(Build.ArtifactStagingDirectory)/**/*.zip'

- task: AzureFunctionApp@2
  inputs:
    azureSubscription: 'myAzureServiceConnection'
    appType: functionApp
    appName: 'my-function-app'
    package: '$(Build.ArtifactStagingDirectory)/**/*.zip'

- task: KubernetesManifest@1
  inputs:
    action: deploy
    kubernetesServiceConnection: 'myAKSConnection'
    namespace: production
    manifests: k8s/deployment.yaml
```

**Publish test results:**
```yaml
- task: PublishTestResults@2
  inputs:
    testResultsFormat: JUnit
    testResultsFiles: '**/test-results.xml'
    mergeTestResults: true
    failTaskOnFailedTests: true
```

**Publish code coverage:**
```yaml
- task: PublishCodeCoverageResults@2
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '**/coverage.cobertura.xml'
```

---

## Variables

Variables provide reusable values across the pipeline. They can be defined at pipeline level, stage level, job level or as library variable groups.

### Inline variables

```yaml
variables:
  buildConfiguration: 'Release'
  appName: 'my-application'
  isMain: $[eq(variables['Build.SourceBranch'], 'refs/heads/main')]

stages:
  - stage: Build
    jobs:
      - job: BuildJob
        steps:
          - script: dotnet build --configuration $(buildConfiguration)
```

### Predefined variables

Azure Pipelines provides a large set of predefined variables:

| Variable | Value |
|---|---|
| `Build.BuildNumber` | The pipeline run number |
| `Build.BuildId` | Unique numeric ID of the build |
| `Build.SourceBranch` | Full branch ref: `refs/heads/main` |
| `Build.SourceBranchName` | Short branch name: `main` |
| `Build.SourceVersion` | The triggering commit SHA |
| `Build.Repository.Name` | Repository name |
| `Build.ArtifactStagingDirectory` | Staging area for artifacts |
| `Build.SourcesDirectory` | Where the code is checked out |
| `System.TeamProject` | Project name |
| `System.DefaultWorkingDirectory` | Working directory for steps |
| `Agent.OS` | `Windows_NT`, `Darwin` or `Linux` |
| `Pipeline.Workspace` | Root workspace directory |

### Variable groups

Variable groups are shared sets of variables that can be used across multiple pipelines. Define them once, reference them from many pipelines.

**Creating a variable group:**
1. Go to **Pipelines** > **Library** > **Variable groups**
2. Click **+ Variable group**
3. Name the group and add variables
4. Mark sensitive values as **Secret** (they are encrypted and masked in logs)
5. Save

**Using a variable group in a pipeline:**
```yaml
variables:
  - group: MyVariableGroup           # reference by name
  - name: localVar
    value: 'local-value'

steps:
  - script: echo $(variableFromGroup)
```

### Secrets

Secret variables are encrypted and masked in pipeline logs. They cannot be read by pipeline tasks unless explicitly mapped. Define secrets as:
- Individual secret pipeline variables (set in the pipeline editor)
- Secret variables in a variable group
- Azure Key Vault secrets (via Key Vault task or variable group integration)

```yaml
# Link a variable group backed by Azure Key Vault
variables:
  - group: 'KeyVault-Production-Secrets'
```

To link a variable group to Azure Key Vault, the variable group settings allow selecting a Key Vault and choosing which secrets to expose as variables.

### Setting variables dynamically

Set a variable value during a pipeline run to use in later steps or jobs:

```bash
# In a script step
echo "##vso[task.setvariable variable=MyVar]myvalue"

# Set across jobs (isOutput=true)
echo "##vso[task.setvariable variable=MyVar;isOutput=true]myvalue"
```

Reference an output variable from a previous job:
```yaml
- job: JobB
  dependsOn: JobA
  variables:
    myVarFromA: $[ dependencies.JobA.outputs['stepName.MyVar'] ]
```

---

## Conditions

Conditions control whether a stage, job or step runs based on the state of previous steps or variable values.

### Built-in condition functions

```yaml
condition: succeeded()           # run if all previous succeeded
condition: failed()              # run only if something failed
condition: succeededOrFailed()   # always run
condition: always()              # always run (even if cancelled)
condition: canceled()            # run only if pipeline was cancelled
```

### Expression conditions

```yaml
# Run only on main branch
condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')

# Run only on tags
condition: startsWith(variables['Build.SourceBranch'], 'refs/tags/v')

# Combine conditions
condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))

# Run if any of several conditions are true
condition: or(
  eq(variables['Build.SourceBranch'], 'refs/heads/main'),
  startsWith(variables['Build.SourceBranch'], 'refs/tags/')
)

# Use a runtime expression (evaluated when the stage starts)
condition: $[ eq(variables.isDeploymentApproved, 'true') ]
```

### Conditional deployment example

```yaml
stages:
  - stage: Deploy
    condition: and(
      succeeded(),
      in(variables['Build.Reason'], 'Manual', 'Schedule'),
      eq(variables['Build.SourceBranch'], 'refs/heads/main')
    )
```

---

## Artifacts

**Pipeline artifacts** are files produced by one job and consumed by another - or published for download after the pipeline completes.

### Publishing artifacts

```yaml
- task: PublishPipelineArtifact@1
  inputs:
    targetPath: '$(Build.ArtifactStagingDirectory)'
    artifact: 'drop'             # name of the artifact
    publishLocation: 'pipeline'
```

Or using the shorthand:
```yaml
- publish: $(Build.ArtifactStagingDirectory)
  artifact: drop
```

### Downloading artifacts in a later job

```yaml
- job: Deploy
  dependsOn: Build
  steps:
    - download: current          # download from this pipeline run
      artifact: drop

    # Files are available at $(Pipeline.Workspace)/drop/
    - script: ls $(Pipeline.Workspace)/drop
```

### Caching dependencies

Caching stores downloaded dependencies (npm packages, NuGet packages, pip packages) between pipeline runs for faster subsequent builds:

```yaml
variables:
  npm_config_cache: $(Pipeline.Workspace)/.npm

steps:
  - task: Cache@2
    inputs:
      key: 'npm | "$(Agent.OS)" | package-lock.json'
      restoreKeys: |
        npm | "$(Agent.OS)"
      path: $(npm_config_cache)
    displayName: 'Cache npm packages'

  - script: npm ci
    displayName: 'Install dependencies'
```

The cache is stored keyed by the hash of `package-lock.json`. When the lock file changes, the cache misses and a fresh install runs.

---

## Environments and deployments

**Environments** are named deployment targets that track what has been deployed where. They provide deployment history, approval gates and resource management.

### Creating an environment

1. Go to **Pipelines** > **Environments**
2. Click **New environment**
3. Name it (e.g. `staging`, `production`)
4. Optionally add resources (Kubernetes namespaces, virtual machines)

### Deployment jobs

Deployment jobs replace regular jobs when targeting an environment:

```yaml
stages:
  - stage: Deploy
    jobs:
      - deployment: DeployToProduction
        displayName: 'Deploy to Production'
        environment: production           # links to the environment
        strategy:
          runOnce:
            deploy:
              steps:
                - script: ./deploy.sh production
```

### Approval gates

Add manual approval requirements to an environment:

1. Go to the environment in **Pipelines** > **Environments**
2. Click the **...** menu > **Approvals and checks**
3. Click **+** > **Approvals**
4. Add approvers (users or groups)
5. Set timeout and instructions

When a pipeline reaches the deployment job targeting this environment, it pauses. The designated approvers receive a notification and must approve before the deployment proceeds. This is the "human-in-the-loop" gate before production deployments.

### Deployment strategies

```yaml
strategy:
  runOnce:          # deploy once, no rollback support
    deploy:
      steps:
        - script: ./deploy.sh

  rolling:          # deploy to subsets of resources progressively
    maxParallel: 2  # deploy to 2 resources at a time
    deploy:
      steps:
        - script: ./deploy.sh

  canary:           # deploy to a small percentage first
    increments: [10, 50, 100]  # 10%, then 50%, then 100%
    deploy:
      steps:
        - script: ./deploy.sh $(CANARY_PERCENTAGE)
```

---

## Templates for reuse

Templates allow you to define reusable pipeline components. Instead of copying the same 20-line test job into every pipeline, define it once as a template and reference it.

### Step templates

Create a file `templates/run-tests.yml`:
```yaml
parameters:
  - name: testProjects
    type: string
    default: '**/*Tests.csproj'

steps:
  - task: DotNetCoreCLI@2
    displayName: 'Run tests'
    inputs:
      command: test
      projects: ${{ parameters.testProjects }}
      arguments: '--configuration Release --collect:"XPlat Code Coverage"'
      publishTestResults: true

  - task: PublishCodeCoverageResults@2
    displayName: 'Publish coverage'
    inputs:
      codeCoverageTool: Cobertura
      summaryFileLocation: '**/coverage.cobertura.xml'
```

Reference in your pipeline:
```yaml
steps:
  - template: templates/run-tests.yml
    parameters:
      testProjects: '**/MyApp.Tests.csproj'
```

### Job templates

```yaml
# templates/build-job.yml
parameters:
  - name: buildConfiguration
    type: string
    default: Release
  - name: vmImage
    type: string
    default: ubuntu-latest

jobs:
  - job: Build
    pool:
      vmImage: ${{ parameters.vmImage }}
    steps:
      - task: DotNetCoreCLI@2
        inputs:
          command: build
          arguments: '--configuration ${{ parameters.buildConfiguration }}'
```

Reference:
```yaml
jobs:
  - template: templates/build-job.yml
    parameters:
      buildConfiguration: Debug
      vmImage: windows-latest
```

### Stage templates

```yaml
# templates/deploy-stage.yml
parameters:
  - name: environment
    type: string
  - name: serviceConnection
    type: string

stages:
  - stage: Deploy_${{ parameters.environment }}
    jobs:
      - deployment: Deploy
        environment: ${{ parameters.environment }}
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: ${{ parameters.serviceConnection }}
                    appName: 'myapp-${{ parameters.environment }}'
```

### Extending templates (security)

Templates can be used as a security control to enforce required steps in all pipelines. The `extends` keyword forces a pipeline to extend a specific template rather than defining its own structure:

```yaml
# In your pipeline:
extends:
  template: secure-pipeline-template.yml@templates
  parameters:
    environment: production
```

This ensures every pipeline passes through the mandatory steps defined in the template (security scanning, compliance checks) and cannot bypass them.

---

## Service connections

Service connections store credentials for connecting Azure Pipelines to external services. They prevent credentials from being hardcoded in pipeline YAML.

### Creating a service connection

1. Go to **Project settings** > **Service connections**
2. Click **New service connection**
3. Choose the connection type:

| Type | Connects to |
|---|---|
| Azure Resource Manager | Azure subscriptions and resources |
| Docker Registry | Docker Hub, Azure Container Registry, private registries |
| GitHub | GitHub repositories (for triggering builds or checking out code) |
| Kubernetes | Kubernetes clusters |
| npm | npm registries |
| NuGet | NuGet feeds |
| SSH | SSH servers |
| Generic | Any HTTP service |

4. Authenticate (the process varies by type)
5. Name the connection (this name is used in pipeline YAML)
6. Save

### Using service connections in pipelines

```yaml
- task: AzureWebApp@1
  inputs:
    azureSubscription: 'MyAzureConnection'   # service connection name
    appName: 'my-web-app'
    package: $(Build.ArtifactStagingDirectory)

- task: Docker@2
  inputs:
    containerRegistry: 'MyDockerHubConnection'
    repository: 'myorg/myapp'
    command: push
```

### Workload identity federation (secretless)

Modern Azure service connections support **workload identity federation** - instead of storing a client secret, Azure DevOps authenticates using a short-lived OIDC token. This eliminates the need to rotate client secrets and reduces the risk of credential exposure.

When creating an Azure Resource Manager service connection, choose **Workload identity federation** instead of Service principal (classic).

---

## Pipeline security

### Protected resources

Mark pipelines and resources (environments, service connections, variable groups, agent pools) as protected. Protected resources require explicit approval before any pipeline can use them.

For environments:
1. Go to the environment > **Approvals and checks**
2. Add an approval requirement

For service connections and variable groups:
1. Go to **Project settings** > **Service connections** or **Library**
2. Select the resource > **Security**
3. Set which pipelines can use it

### YAML pipeline permissions

By default, any pipeline in the project can use any service connection or variable group (subject to permissions). Lock down access:

1. For a service connection: **Project settings** > **Service connections** > select > **Security** > restrict to specific pipelines
2. For a variable group: **Library** > select group > **Pipeline permissions** > limit to specific pipelines

### Preventing secrets from leaking

Secrets in variable groups are masked in logs (`***`). However:
- Do not `echo` secrets explicitly
- Do not use secrets as part of file or directory names
- Be aware that scripts that print environment variables (e.g. `printenv`) will be masked only for recognised secret variable names

---

## Managed DevOps Pools

**Managed DevOps Pools** is a new capability (2025-2026) that gives teams the benefits of self-hosted agents (customisation, consistent environment) with the convenience of managed hosting.

Key features:
- Scales to zero when not in use (no cost when idle)
- Gen 2 VMs with faster boot times
- Azure Key Vault integration for certificates
- Standard images based on Microsoft-hosted images
- Custom images for specialised builds
- **Spot VM instances** (coming mid-2026) for up to 90% cost savings on non-time-sensitive builds

### Creating a Managed DevOps Pool

1. Go to **Organisation settings** > **Managed DevOps Pools**
2. Click **Create pool**
3. Link to an Azure subscription
4. Choose the VM SKU and image
5. Configure scaling settings (min/max instances)
6. The pool appears in the agent pool list and can be used in pipelines

---

## Classic pipelines (legacy)

Before YAML pipelines, Azure Pipelines had a visual designer called **Classic pipelines**. Classic pipelines are defined through a GUI interface rather than YAML files. They still work but are in maintenance mode - no new features are being added.

If you encounter a classic pipeline (identifiable by the visual editor and the `.json` build definition):
- You can continue using it
- Microsoft recommends migrating to YAML
- Use the **View YAML** button in the classic editor to see the equivalent YAML

The migration from classic to YAML is a one-time effort but significantly improves maintainability, code review integration and version history.

---

## Practical pipeline examples

### Node.js application

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

variables:
  nodeVersion: '20.x'

stages:
  - stage: CI
    jobs:
      - job: BuildAndTest
        steps:
          - task: NodeTool@0
            inputs:
              versionSpec: $(nodeVersion)

          - task: Cache@2
            inputs:
              key: 'npm | "$(Agent.OS)" | package-lock.json'
              path: $(npm_config_cache)
            variables:
              npm_config_cache: $(Pipeline.Workspace)/.npm

          - script: npm ci
            displayName: 'Install dependencies'

          - script: npm run lint
            displayName: 'Lint'

          - script: npm test -- --ci --reporters=jest-junit
            displayName: 'Test'

          - task: PublishTestResults@2
            inputs:
              testResultsFormat: JUnit
              testResultsFiles: '**/junit.xml'

          - script: npm run build
            displayName: 'Build'

          - publish: dist
            artifact: webapp

  - stage: Deploy
    dependsOn: CI
    condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
    jobs:
      - deployment: DeployToAzure
        environment: production
        strategy:
          runOnce:
            deploy:
              steps:
                - task: AzureWebApp@1
                  inputs:
                    azureSubscription: 'MyAzureConnection'
                    appName: 'my-node-app'
                    package: $(Pipeline.Workspace)/webapp
```

### .NET application with NuGet restore and Docker

```yaml
trigger:
  - main
  - 'release/*'

variables:
  buildConfiguration: Release
  imageRepository: 'myorg/myapp'
  dockerfilePath: 'src/MyApp/Dockerfile'

stages:
  - stage: Build
    jobs:
      - job: BuildAndTest
        pool:
          vmImage: windows-latest
        steps:
          - task: UseDotNet@2
            inputs:
              version: '8.x'

          - task: DotNetCoreCLI@2
            displayName: 'Restore'
            inputs:
              command: restore
              projects: '**/*.csproj'
              feedsToUse: config
              nugetConfigPath: NuGet.config

          - task: DotNetCoreCLI@2
            displayName: 'Build'
            inputs:
              command: build
              projects: '**/*.csproj'
              arguments: '--configuration $(buildConfiguration) --no-restore'

          - task: DotNetCoreCLI@2
            displayName: 'Test'
            inputs:
              command: test
              projects: '**/*Tests.csproj'
              arguments: '--configuration $(buildConfiguration) --no-build'
              publishTestResults: true

      - job: BuildDockerImage
        pool:
          vmImage: ubuntu-latest
        steps:
          - task: Docker@2
            displayName: 'Build and push image'
            inputs:
              containerRegistry: 'MyACRConnection'
              repository: $(imageRepository)
              command: buildAndPush
              Dockerfile: $(dockerfilePath)
              tags: |
                $(Build.BuildNumber)
                latest
```

### Multi-environment deployment with approvals

```yaml
trigger:
  - main

stages:
  - stage: Build
    jobs:
      - job: Build
        pool:
          vmImage: ubuntu-latest
        steps:
          - script: make build
          - publish: build/output
            artifact: app

  - stage: DeployDev
    dependsOn: Build
    jobs:
      - deployment: Deploy
        environment: development     # no approvals configured
        strategy:
          runOnce:
            deploy:
              steps:
                - download: current
                  artifact: app
                - script: ./deploy.sh development

  - stage: DeployStaging
    dependsOn: DeployDev
    jobs:
      - deployment: Deploy
        environment: staging         # may have approval configured
        strategy:
          runOnce:
            deploy:
              steps:
                - download: current
                  artifact: app
                - script: ./deploy.sh staging

  - stage: DeployProduction
    dependsOn: DeployStaging
    jobs:
      - deployment: Deploy
        environment: production      # requires approval from release manager
        strategy:
          runOnce:
            deploy:
              steps:
                - download: current
                  artifact: app
                - script: ./deploy.sh production
```

---

## Try It Yourself

**Exercise 1 - Create your first pipeline**

1. In your Azure DevOps project, go to **Pipelines** > **New pipeline**
2. Select where your code is (Azure Repos Git)
3. Select your repository
4. Select **Starter pipeline**
5. Azure DevOps creates a basic `azure-pipelines.yml`
6. Click **Save and run**

**Exercise 2 - Add a real build step**

Edit the pipeline YAML to add a meaningful step. For a Node.js project:

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - task: NodeTool@0
    inputs:
      versionSpec: '20.x'
    displayName: 'Install Node.js'

  - script: |
      echo "Node version:"
      node --version
      echo "npm version:"
      npm --version
    displayName: 'Check versions'
```

**Exercise 3 - Add a second stage**

```yaml
trigger:
  - main

stages:
  - stage: Build
    jobs:
      - job: BuildJob
        pool:
          vmImage: ubuntu-latest
        steps:
          - script: echo "Building..."
          - script: echo "Build complete"
            displayName: 'Simulate build'

  - stage: Test
    dependsOn: Build
    jobs:
      - job: TestJob
        pool:
          vmImage: ubuntu-latest
        steps:
          - script: echo "Running tests..."
          - script: echo "Tests passed"
            displayName: 'Simulate tests'
```

**Exercise 4 - Create a variable group**

1. Go to **Pipelines** > **Library** > **+ Variable group**
2. Name it `MySecrets`
3. Add a variable: `API_URL` = `https://api.example.com`
4. Add a secret variable: `API_KEY` = `secret123` (click the lock icon)
5. Save

Update your pipeline to use the group:
```yaml
variables:
  - group: MySecrets

steps:
  - script: echo "Connecting to $(API_URL)"
  - script: echo "API key is masked in logs: $(API_KEY)"
```

---

## Common Mistakes

**Using `ubuntu-latest` and expecting it to never change**

`ubuntu-latest` changes when Microsoft updates the default. Code that relied on a specific tool version installed on the image can break. For reproducible builds, pin to a specific version: `ubuntu-22.04` or `ubuntu-24.04`.

**Putting secrets in pipeline YAML**

Never put API keys, passwords or tokens directly in `azure-pipelines.yml`. The file is checked into source control. Use secret variables or variable groups with secrets marked as secret.

**Not handling the free tier parallel job limit**

With one free parallel job, if you trigger many pipelines simultaneously (e.g. many PRs at once), they queue. This can cause confusion as pipelines appear to "hang" - they are actually waiting for the agent. Purchase additional parallel jobs or set up self-hosted agents for active teams.

**Not setting timeouts**

Jobs without explicit timeouts run for up to 60 minutes by default. A hung test suite or infinite loop runs for an hour before timing out. Set shorter timeouts on jobs you know should complete quickly.

**Deploying to production from pull request pipelines**

PR pipelines should run tests and validate code. Production deployment should only trigger from the main branch or specific tags. Always check `Build.SourceBranch` in conditions before deploying.

**Not using templates for repeated pipeline sections**

Copy-pasting the same 20-line test configuration into 10 pipelines means 10 places to update when something changes. Extract common patterns into templates immediately.

---

## Summary

Azure Pipelines is defined in `azure-pipelines.yml` committed to your repository. Pipelines are triggered by branch pushes, pull requests, scheduled runs or manually.

The execution hierarchy is Stage → Job → Step. Stages run sequentially, jobs within a stage run in parallel, steps within a job run sequentially. Conditions control when each level executes.

Microsoft-hosted agents currently run Ubuntu 24.04, Windows Server 2025 and macOS 15. Retired agents (Windows 2019, Ubuntu 20.04, macOS 13) cause pipeline failures - update to current images.

Tasks are pre-built steps for common operations: .NET builds, Docker, Azure deployments, test publishing. Variables store reusable values; variable groups share variables across pipelines; secrets are masked in logs.

Environments and deployment jobs track what has been deployed where. Approval gates pause the pipeline for human sign-off before production deployments. Templates enable reuse across pipelines. Service connections store credentials for external services.

Managed DevOps Pools offer managed self-hosted agents that scale to zero, reducing costs for teams that need custom environments.

---

## Sources

- [Microsoft Learn: Azure Pipelines documentation](https://learn.microsoft.com/azure/devops/pipelines/)
- [Microsoft Learn: YAML schema reference](https://learn.microsoft.com/azure/devops/pipelines/yaml-schema/)
- [Microsoft Learn: Microsoft-hosted agents](https://learn.microsoft.com/azure/devops/pipelines/agents/hosted)
- [Microsoft Learn: Self-hosted agents](https://learn.microsoft.com/azure/devops/pipelines/agents/agents)
- [Microsoft Learn: Variables](https://learn.microsoft.com/azure/devops/pipelines/process/variables)
- [Microsoft Learn: Environments](https://learn.microsoft.com/azure/devops/pipelines/process/environments)
- [Microsoft Learn: Templates](https://learn.microsoft.com/azure/devops/pipelines/process/templates)
- [Microsoft Learn: Service connections](https://learn.microsoft.com/azure/devops/pipelines/library/service-endpoints)
- [Microsoft Learn: Managed DevOps Pools](https://learn.microsoft.com/azure/devops/managed-devops-pools/)
- [Microsoft Learn: Predefined variables](https://learn.microsoft.com/azure/devops/pipelines/build/variables)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

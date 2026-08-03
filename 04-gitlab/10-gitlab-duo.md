# GitLab Duo

**Difficulty:** 🟡 Intermediate | **Time:** 35 minutes

GitLab Duo is the umbrella name for GitLab's suite of AI features. Unlike GitHub Copilot, which focuses primarily on individual code completion and chat, GitLab Duo integrates AI across the entire software development lifecycle - from planning work in issues, through writing and reviewing code, to diagnosing failed pipelines and resolving security vulnerabilities. The goal is not just to make individual developers faster, but to make the whole delivery process more consistent, more secure and less dependent on manual handoffs.

This file covers every Duo feature in depth: Code Suggestions, Duo Chat, the Agent Platform and its built-in agents, the underlying AI models, the credits system, IDE integration, privacy guarantees and how Duo compares to GitHub Copilot.

---

## Table of Contents

- [What is GitLab Duo?](#what-is-gitlab-duo)
- [Duo tiers and pricing](#duo-tiers-and-pricing)
- [The credits system](#the-credits-system)
- [Code Suggestions](#code-suggestions)
- [Duo Chat](#duo-chat)
- [Duo Agent Platform](#duo-agent-platform)
- [Built-in agents](#built-in-agents)
- [Flows](#flows)
- [The AI Catalog](#the-ai-catalog)
- [MCP Client - connecting external tools](#mcp-client---connecting-external-tools)
- [AI models used by Duo](#ai-models-used-by-duo)
- [IDE extensions and setup](#ide-extensions-and-setup)
- [Privacy and data security](#privacy-and-data-security)
- [Self-hosted AI models](#self-hosted-ai-models)
- [GitLab Duo vs GitHub Copilot](#gitlab-duo-vs-github-copilot)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitLab Duo?

GitLab Duo is a collection of AI-powered features embedded throughout the GitLab platform. Each feature targets a specific pain point in the development lifecycle and can be used independently - you do not need to adopt the entire suite to benefit from individual features.

**The problem Duo addresses:**

The GitLab Global DevSecOps Report 2026 found that while AI tools speed up individual coding tasks, teams still lose an average of **seven hours per person per week** to fragmented tools and manual handoffs between stages of the development process. Duo's goal is to reduce those handoffs by embedding AI at every stage - not just in the code editor.

**What makes Duo different from general AI assistants:**

Duo has access to your GitLab context. When you ask Duo Chat about a merge request, it can read the MR description, the code diff, the linked issues and the pipeline results. When the Root Cause Analysis agent investigates a failing pipeline, it reads the actual job logs, the `.gitlab-ci.yml` configuration and the recent changes. General AI assistants like ChatGPT have none of this context - they know only what you paste into the chat window.

**First introduced**: GitLab 16.0 (May 2023)

**Duo Agent Platform GA**: GitLab 18.8 (January 15, 2026)

---

## Duo Tiers and Pricing

Duo features are distributed across three add-on tiers, each requiring a base GitLab plan.

| Add-on             | Monthly price  | Requires                           | What it includes                                                                                                                                            |
| ------------------ | -------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Duo Core**       | Included free  | Premium or Ultimate (GitLab 18.0+) | Basic Code Suggestions in IDE, Duo Chat (non-agentic)                                                                                                       |
| **Duo Pro**        | $19/user/month | Premium or Ultimate                | Advanced Code Suggestions, full Duo Chat, test generation, MR summaries, Duo Agent Platform access                                                          |
| **Duo Enterprise** | $39/user/month | Ultimate only                      | Everything in Pro + Root Cause Analysis, vulnerability explanation and resolution, AI Code Review, issue description generation, advanced agentic workflows |

> [!NOTE]
> **Duo Core** was introduced in GitLab 18.0 (May 2025) as a way to provide basic AI features at no extra cost to Premium and Ultimate subscribers. Before 18.0, all Duo features required a paid add-on. Free tier users receive a limited number of Duo credits (see [The credits system](#the-credits-system)).

**GitLab Duo with Amazon Q** is a separate add-on (self-managed only) that integrates Amazon Q Developer's AI capabilities - including IDE plugins for Visual Studio, VS Code, JetBrains and Eclipse, plus command-line tools and AWS Console integration - into the GitLab workflow.

---

## The Credits System

GitLab Duo Agent Platform uses a credits-based consumption model introduced in GitLab 18.8 alongside the Agent Platform GA.

### How credits work

Credits are a shared currency that your organisation's users draw from when performing agentic AI actions. Different AI models consume credits at different rates:

| Model                                       | Requests per credit    |
| ------------------------------------------- | ---------------------- |
| Claude Sonnet 4 (default for most features) | 2 requests per credit  |
| GPT-5-mini, Claude 3 Haiku                  | 20 requests per credit |

This means agentic features using Claude Sonnet consume credits faster than quick chat responses using lighter models.

### Monthly included credits

Credits are included based on your GitLab plan:

| Plan                     | Included credits               |
| ------------------------ | ------------------------------ |
| Free                     | Limited (some credits granted) |
| Premium (with Duo Core)  | 12 credits per user per month  |
| Ultimate (with Duo Core) | 24 credits per user per month  |

> [!NOTE]
> The included credit amounts are a launch promotion and subject to change. Check `about.gitlab.com/pricing/` for current figures.

### Purchasing additional credits

Credits beyond the monthly inclusion cost **$1 per credit** (on-demand). Organisations can also purchase a **Monthly Commitment Pool** - a pre-purchased shared pool of credits at volume discounts. The pool is shared across all users rather than allocated per seat, which benefits teams where usage is uneven (some users are heavy AI users, others use it rarely).

### Who draws from credits

Credits are consumed for:

- Agentic chat sessions (multi-step reasoning)
- Agent flows (automated multi-step tasks)
- Root Cause Analysis
- Vulnerability resolution
- AI Code Review
- Any Duo Agent Platform feature

**Not drawing from credits** (these are included with subscriptions):

- Basic Code Suggestions (inline completions)
- Simple non-agentic Duo Chat responses

### Tracking credit usage

Group → **Settings → GitLab Duo** → **Credits** shows current consumption, remaining credits and usage breakdown by feature and user.

---

## Code Suggestions

Code Suggestions is the inline code completion and generation feature - the part of Duo most similar to GitHub Copilot.

### What Code Suggestions does

**Inline completions**: as you type, Duo predicts what you are about to write and shows a greyed-out suggestion. Press `Tab` to accept, `Escape` to dismiss, or keep typing to ignore and refine.

**Multi-line generation**: Duo generates entire functions, classes or blocks when prompted. Write a comment describing what you want and Duo fills in the implementation.

**Code completion in 25+ languages**: best quality results for C++, C#, Go, Java, JavaScript, Python, Ruby, Rust, Scala, Swift, TypeScript. Good results for most other common languages.

### How it differs from Copilot

Code Suggestions uses GitLab's context more deeply than Copilot uses GitHub's context:

- Code Suggestions considers files open in your editor simultaneously
- It understands your project's coding style from the surrounding codebase
- In JetBrains and VS Code with the GitLab extension, it has access to the repository context

### Enabling Code Suggestions

Code Suggestions are enabled by default when you have a Duo Core or higher subscription and the GitLab extension installed in your IDE.

**In VS Code**: install the **GitLab Workflow** extension → sign in to your GitLab account → Code Suggestions activate automatically.

**In JetBrains**: install the **GitLab Duo** plugin from the JetBrains Marketplace → sign in → completions activate.

### Accepting, modifying and rejecting suggestions

| Action                 | VS Code  | JetBrains |
| ---------------------- | -------- | --------- |
| Accept full suggestion | `Tab`    | `Tab`     |
| Accept word by word    | `Ctrl+→` | `Ctrl+→`  |
| Reject / dismiss       | `Escape` | `Escape`  |
| Request new suggestion | `Alt+]`  | `Alt+]`   |

### Feedback

Rate suggestions using the thumbs up/down feedback UI that appears after accepting or dismissing. GitLab uses this feedback to improve the models and rules.

---

## Duo Chat

Duo Chat is a conversational AI assistant that you interact with in natural language - ask questions, request code generation, get explanations, write tests, debug problems.

### Chat modes

**Non-agentic chat** (included in Duo Core): a single-turn conversation where Duo answers your question based on its training and the context you provide. Fast, does not consume credits.

**Agentic chat** (Duo Pro and above): multi-step reasoning where Duo breaks your question into steps, uses tools to gather information from your GitLab environment and synthesises a comprehensive answer. Takes longer, consumes credits, but handles complex questions that require looking up multiple pieces of information.

### Accessing Duo Chat

**In GitLab web UI**: look for the Duo Chat icon (speech bubble with sparkle) in the left sidebar or top navigation. Click it to open a chat panel that slides in from the right without leaving the current page.

**In VS Code**: open the GitLab Workflow extension sidebar → click the Duo Chat tab.

**In JetBrains**: open the GitLab Duo plugin sidebar → click the Chat tab.

**In the Web IDE**: click the Duo Chat icon in the activity bar.

### What you can ask Duo Chat

**About code in your editor:**

- "Explain what this function does"
- "What are the potential security issues in this code?"
- "Refactor this to use async/await instead of callbacks"
- "Write unit tests for this class"
- "What is the time complexity of this algorithm?"

**About your GitLab project:**

- "Summarise what changed in merge request !42"
- "Why is pipeline #1234 failing?"
- "What issues are blocking the Q2 milestone?"
- "Who are the code owners of the `/src/auth/` directory?"

**About the codebase:**

- "How does the authentication flow work in this project?"
- "Where is the database connection configured?"
- "What does the `user_service.rb` file do?"

**General programming questions:**

- "What is the difference between `Promise.all` and `Promise.allSettled` in JavaScript?"
- "How do I configure CORS in a Django REST Framework API?"
- "Explain the Builder design pattern with a Python example"

### Slash commands in chat

Type `/` in the chat input to see available commands:

| Command     | Action                                         |
| ----------- | ---------------------------------------------- |
| `/explain`  | Explain selected code or the current file      |
| `/refactor` | Suggest refactoring for selected code          |
| `/tests`    | Generate tests for selected code               |
| `/fix`      | Suggest a fix for the selected code            |
| `/reset`    | Clear the conversation history and start fresh |

### Context in chat

Duo Chat automatically picks up context from:

- The file currently open in your editor
- Selected text in the editor
- The current MR, issue or pipeline page you are viewing in the web UI

You can also explicitly attach context by mentioning file paths, MR numbers or issue numbers in your message.

---

## Duo Agent Platform

The Duo Agent Platform (GA January 15, 2026 - GitLab 18.8) extends Duo from answering questions to **taking actions**. Agents are AI components that can use tools, make multi-step decisions and execute tasks autonomously within defined boundaries.

**Navigate to agents**: left sidebar → **Duo** → **Agent Platform** (or click the Duo icon → **Agents**)

### The key difference from chat

Duo Chat: you ask a question, Duo gives an answer.

Duo Agent Platform: you describe a task, the agent breaks it into steps, executes each step using GitLab tools (reading issues, creating branches, running searches, opening MRs) and completes the task.

For example, "Convert issue #42 into a working implementation" triggers the Developer Flow agent to:

1. Read the issue description and comments
2. Identify the acceptance criteria
3. Create a feature branch
4. Write the implementation code
5. Write tests
6. Open a merge request with a description linking to the issue

This is fundamentally different from chat - the agent takes real actions in your GitLab project.

### Agent governance

Agents operate within GitLab's existing permission model:

- An agent can only take actions the triggering user has permission to take
- Actions are visible in GitLab - pipeline logs, commit history, issue comments
- You can pause, review and cancel agent actions
- The **AI Catalog** controls which agents are available in which projects

---

## Built-in Agents

These agents are built by GitLab and available with appropriate Duo subscriptions.

### Planner Agent (Duo Pro)

The Planner Agent helps with project planning tasks:

- Structuring epics and breaking them into issues
- Prioritising backlog items using frameworks like RICE (Reach, Impact, Confidence, Effort) or MoSCoW (Must have, Should have, Could have, Won't have)
- Identifying stale backlog items that have not been updated
- Generating issue descriptions from high-level feature requests
- Suggesting milestone assignments based on workload

**Use it for**: getting from a vague feature idea to a structured set of issues ready for sprint planning.

### Security Analyst Agent (Duo Pro/Enterprise)

The Security Analyst Agent works with the vulnerability management system:

- Reviews vulnerability findings from SAST, DAST and dependency scanning
- Explains the impact of each vulnerability in plain language
- Prioritises which vulnerabilities to address first based on severity, exploitability and business context
- Generates fix suggestions for SAST findings
- Summarises the security posture of the project

**Use it for**: triaging vulnerability reports and understanding which findings require immediate attention.

### Root Cause Analysis (Duo Enterprise)

When a CI/CD pipeline job fails, Root Cause Analysis examines the job logs and suggests the cause and fix.

**Access**: on any failed pipeline job → click **Root cause analysis** button in the job log view.

The agent:

1. Reads the full job log (potentially thousands of lines)
2. Identifies the specific error or failure
3. Traces back through the log to find the root cause
4. Suggests a fix with context explaining why the fix addresses the root cause

**Before RCA**: developer opens job log, scrolls through hundreds of lines of output, eventually finds the relevant error, searches documentation for the fix. Time: 15-45 minutes.

**With RCA**: developer clicks "Root cause analysis". Duo identifies the issue and suggests a fix in 30 seconds. Developer reviews and applies.

### Vulnerability Resolution (Duo Enterprise)

When a SAST scanner finds a vulnerability, Vulnerability Resolution generates a code fix:

**Access**: Secure → Vulnerability report → click a vulnerability → **Resolve with AI**

The agent:

1. Reads the vulnerable code
2. Understands the vulnerability class (SQLi, XSS, SSRF, etc.)
3. Generates a patched version of the code
4. Creates a merge request with the fix
5. Includes an explanation in the MR description

The generated MR goes through your normal review process - a human still reviews and approves before the fix merges.

### AI Code Review (Duo Enterprise)

When an MR is opened, the Code Review agent analyses the changed code and leaves automated review comments:

- Potential bugs and logic errors
- Code style issues
- Security concerns
- Performance implications
- Suggested improvements with explanations

**Access**: open an MR → click **Start AI review** in the MR actions.

This does not replace human review - it supplements it. The AI catches mechanical issues (null pointer risks, missing error handling, obvious performance problems) so human reviewers can focus on architecture, business logic and design decisions.

---

## Flows

Flows are pre-built automated sequences of agent actions for common multi-step workflows. Where agents are interactive (you guide them), flows are more automated - you trigger them and they run.

### Developer Flow (Issue to MR)

Converts a well-described issue into a working code implementation:

1. Reads the issue description, acceptance criteria and any linked issues
2. Identifies which files need to change
3. Creates a feature branch
4. Writes the implementation code
5. Writes unit tests
6. Opens a draft MR linked to the issue with a descriptive description

**Best for**: well-scoped issues with clear acceptance criteria. Works best when the issue includes technical context about how the feature should be implemented.

**Not suitable for**: vague or under-specified issues ("make the app faster"), issues requiring significant architectural decisions, or frontend changes that require visual judgement.

### Convert to GitLab CI/CD Flow

Migrates CI/CD configurations from other platforms (GitHub Actions, Jenkins, CircleCI, etc.) to GitLab CI/CD:

1. Reads the existing CI/CD configuration
2. Maps concepts to GitLab equivalents
3. Generates a `.gitlab-ci.yml` equivalent
4. Opens an MR with the generated file and migration notes

### Fix CI/CD Pipeline Flow

When a pipeline repeatedly fails, this flow:

1. Reads the pipeline configuration and job logs
2. Identifies systematic issues (not one-off transient failures)
3. Generates fixes to the `.gitlab-ci.yml`
4. Opens an MR with the proposed fixes and explanations

### Code Review Flow

Similar to AI Code Review but more thorough - runs as a flow rather than an inline review, producing a detailed review report as an MR comment.

---

## The AI Catalog

The AI Catalog is the central place to discover, manage and share agents and flows across your organisation.

**Navigate**: Duo → **AI Catalog**

### What the catalog provides

- Browse available built-in agents and flows
- Publish custom agents built by your team (using GitLab's agent creation tools)
- Assign agents and flows to specific projects or groups
- Set which users can trigger which agents
- Version custom agents for controlled rollout

### Creating custom agents

GitLab provides tools to build custom agents with specific skills. A custom agent could:

- Automatically respond to support tickets (integrating Service Desk with external knowledge bases)
- Generate release notes from merged MR descriptions
- Enforce team-specific code review standards
- Integrate with company-specific tools via MCP

Custom agents are published to the AI Catalog and governed by the same permission model as built-in agents.

---

## MCP Client - Connecting External Tools

The **MCP (Model Context Protocol) Client** (GA in GitLab 18.8) allows Duo agents to connect to external systems via the MCP standard. This means agents are not limited to GitLab's own data - they can query and act on external tools that your team already uses.

### Supported external integrations

MCP-compatible systems that Duo agents can connect to include:

- **Jira**: read issues, update status, create tickets
- **Slack**: send messages, query channel history
- **Confluence**: read documentation pages
- **Grafana**: query metrics and alerts
- **Linear**: manage issues and roadmaps
- **Any MCP-compatible service**: the protocol is open and growing

### Governance

MCP connections are configured at the group level by administrators, with access controls determining which users and which agents can use each external connection. No individual developer can connect arbitrary external services without admin approval.

---

## AI Models Used by Duo

GitLab Duo does not use a single AI model. Different features use different models optimised for their specific tasks.

### Current models (GitLab 18.10)

**Anthropic Claude** (primary model):

- Claude Sonnet 4: used for most Duo Chat responses and code generation
- Claude 3 Haiku: used for faster, lower-cost features
- Claude Opus: used for complex agentic reasoning tasks
- Contract: zero-day data retention (data is not stored after the request completes)

**Google Vertex AI**:

- Codey APIs for code completion (inline suggestions)
- Gemini models for some analysis tasks
- Contract: Google's enterprise data processing terms apply; no model training on customer data

**Fireworks AI**:

- Codestral (by Mistral AI): an optimised code completion model
- Contract: zero-day data retention

**OpenAI** (removed from most features in GitLab 16.6): OpenAI models were previously used but removed. As of 18.x, OpenAI is not used in the standard Duo features. GitLab Duo with Amazon Q is the exception, which uses Amazon Bedrock models.

### Model selection (GitLab 18.4+)

Group administrators can choose which model vendors are used for different feature categories. This is important for organisations with data residency requirements - for example, choosing only Google Vertex AI models for EU data residency compliance.

**Configure**: Group → Settings → **GitLab Duo** → **Model selection**

Separate model selection is available for:

- Chat features (18.4+)
- Agent features (18.7+)

### Zero-day retention

GitLab has negotiated **zero-day data retention** agreements with Anthropic, Fireworks AI, AWS and Google. This means:

- Your prompts and code are sent to the AI model provider for processing
- The provider returns a response
- The provider **immediately discards both the input and the output**
- Nothing is stored on the provider's systems after the request completes

This is a contractual guarantee, not just a policy. GitLab publishes the sub-processor data processing terms at `docs.gitlab.com/user/gitlab_duo/data_usage/`.

---

## IDE Extensions and Setup

Duo features are available in the GitLab web UI and through IDE extensions.

### VS Code - GitLab Workflow Extension

The **GitLab Workflow** extension provides Code Suggestions and Duo Chat in VS Code.

**Installation**:

1. Open VS Code
2. Extensions marketplace (`Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Search "GitLab Workflow"
4. Install the extension by GitLab

**Authentication**:

1. Open the Command Palette (`Ctrl+Shift+P`)
2. Run **GitLab: Authenticate**
3. Choose your GitLab instance (GitLab.com or self-managed URL)
4. A browser window opens for OAuth authentication
5. Approve access → return to VS Code

**Features in VS Code**:

- Inline Code Suggestions (appear automatically as you type)
- Duo Chat panel (sidebar)
- MR review workflow (view and approve MRs without leaving VS Code)
- Pipeline status in the status bar
- CI/CD variable management
- Issue and MR management

### JetBrains - GitLab Duo Plugin

The **GitLab Duo** plugin works across all JetBrains IDEs: IntelliJ IDEA, PyCharm, WebStorm, GoLand, CLion, Rider, RubyMine, DataGrip, PhpStorm.

**Installation**:

1. Open your JetBrains IDE
2. Settings/Preferences → Plugins → Marketplace
3. Search "GitLab Duo"
4. Install → restart IDE

**Authentication**:

1. Settings → GitLab Duo → Add account
2. Enter your GitLab URL and a personal access token with `api` scope (or use the browser-based OAuth flow)

**Features in JetBrains**:

- Inline Code Suggestions
- Duo Chat tool window
- Code explanation and refactoring from the right-click menu
- Test generation

### Visual Studio (Windows)

**GitLab Extension for Visual Studio** provides Code Suggestions and Duo Chat for C# and .NET developers in Visual Studio 2022.

**Installation**: Tools → Extensions → search "GitLab Extension for Visual Studio" → install.

### Neovim

**gitlab.nvim** is the community-maintained plugin for Neovim. Provides Code Suggestions, Duo Chat and basic GitLab integration.

**Installation** (using lazy.nvim):

```lua
{
  "harrisoncramer/gitlab.nvim",
  dependencies = {
    "MunifTanjim/nui.nvim",
    "nvim-lua/plenary.nvim",
    "sindrets/diffview.nvim",
    "stevearc/dressing.nvim",
    "nvim-tree/nvim-web-devicons",
  },
  config = function()
    require("gitlab").setup({
      gitlab_url = "https://gitlab.com",
      code_suggestions = { enabled = true },
    })
  end,
}
```

### Web IDE

Duo Chat is built into the Web IDE (browser-based editor). Click the Duo Chat icon in the activity bar. No extension installation needed - it is available automatically for users with Duo access.

---

## Privacy and Data Security

GitLab Duo is built around several core privacy principles.

### What Duo does NOT do

- **Does not train on your private code.** GitLab's contracts with all AI sub-processors explicitly prohibit using your code to train models. Your private code never improves a public model.
- **Does not store prompts or responses at the provider level.** Zero-day retention agreements mean providers discard data immediately after responding.
- **Does not share code between customers.** Your code context is used only for your requests.

### What Duo does do

- **Sends your code to AI providers for processing.** When you use Code Suggestions or Duo Chat in your IDE, the relevant code context is sent to the AI provider (Anthropic, Google or Fireworks). This is necessary for AI features to work.
- **Collects usage analytics.** GitLab collects aggregated, de-identified usage data (which features are used, suggestion acceptance rates, etc.) through its Snowplow analytics pipeline. This is separate from the AI provider data and is governed by GitLab's own privacy policy.
- **Logs trace data for debugging.** When groups enable extended logging for Duo Agent Platform workflows, trace data is retained on GitLab's own infrastructure (not at AI providers) for debugging purposes.

### Context exclusion (GitLab 18.4+)

Groups can configure which files and paths are excluded from Duo's context:

Group → Settings → GitLab Duo → **Context exclusions** → add paths or file patterns.

Files matching these patterns are never sent to AI providers when someone uses Duo features, even if those files are open in the editor. Use this for particularly sensitive files: secret management code, compliance-critical modules, proprietary algorithms.

### Compliance documentation

GitLab maintains a public **AI Transparency Center** at `about.gitlab.com/ai-transparency/` documenting:

- Which features use AI
- Which models are used for each feature
- Data retention policies per sub-processor
- Compliance certifications

For enterprise customers with stringent compliance requirements, this documentation supports vendor assessment processes.

---

## Self-Hosted AI Models

> [!IMPORTANT]
> Self-hosted AI models are available with **Duo Enterprise** add-on on **Ultimate** tier, for **self-managed GitLab** instances.

Some organisations cannot send any data to external AI providers - data sovereignty laws, air-gapped environments, or internal security policies require that all AI processing stays on-premises.

GitLab Duo Self-Hosted (GA GitLab 17.9) supports running AI models on your own infrastructure:

**Supported model serving infrastructure:**

- **vLLM**: open-source LLM inference server. Run models like Llama, Mistral or custom fine-tuned models.
- **AWS Bedrock**: use Amazon's managed model serving with models staying within your AWS account and region.
- **Azure OpenAI Service**: use Azure's managed OpenAI endpoint with Azure data processing agreements.

**Configuration**: in `gitlab.rb` for self-managed instances:

```ruby
gitlab_duo['enable'] = true
gitlab_duo['ai_gateway_url'] = "https://your-internal-ai-gateway.example.com"
```

The AI Gateway component handles routing between GitLab and the model servers.

**Trade-offs of self-hosted models:**

- Complete data sovereignty - no data leaves your infrastructure
- Model quality may be lower than proprietary frontier models (Llama vs Claude)
- Infrastructure, GPU costs and model management become your responsibility
- Some features may not be available with all self-hosted model configurations

---

## GitLab Duo vs GitHub Copilot

| Feature                         | GitLab Duo                                         | GitHub Copilot                                                            |
| ------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------- |
| **Code completion**             | ✓ (Code Suggestions)                               | ✓ (core feature, market leader)                                           |
| **Chat assistant**              | ✓ (Duo Chat)                                       | ✓ (Copilot Chat)                                                          |
| **Inline chat**                 | ✓                                                  | ✓                                                                         |
| **Test generation**             | ✓ (Duo Pro)                                        | ✓                                                                         |
| **MR/PR summaries**             | ✓ (Duo Pro)                                        | ✓ (Copilot Enterprise)                                                    |
| **Agent mode**                  | ✓ (Duo Agent Platform, GA Jan 2026)                | ✓ (agent mode, GA 2025)                                                   |
| **Pipeline failure analysis**   | ✓ (Root Cause Analysis, Duo Enterprise)            | ✗                                                                         |
| **Vulnerability resolution**    | ✓ (Duo Enterprise)                                 | ✗                                                                         |
| **Security analyst agent**      | ✓ (Duo Agent Platform)                             | ✗                                                                         |
| **Issue-to-code flows**         | ✓ (Developer Flow)                                 | Limited                                                                   |
| **Free tier AI**                | ✓ (credits for Free users, Duo Core for Premium)   | ✓ (2,000 completions/month)                                               |
| **Primary AI model**            | Anthropic Claude Sonnet 4                          | OpenAI GPT-4o                                                             |
| **Self-hosted models**          | ✓ (Duo Enterprise, self-managed)                   | ✗                                                                         |
| **Context exclusion**           | ✓ (GitLab 18.4+)                                   | ✓ (configurable)                                                          |
| **Zero data retention**         | ✓ (contractual)                                    | ✓ (configurable)                                                          |
| **IDE support breadth**         | VS Code, JetBrains, Neovim, Visual Studio, Web IDE | VS Code, JetBrains, Vim, Neovim, Visual Studio, Eclipse, Xcode, many more |
| **Copilot completions quality** | Good, improving                                    | Excellent, market-leading                                                 |

**The honest assessment:**

GitHub Copilot is the market leader for raw code completion quality and IDE breadth. It has a larger user base, more training data (from GitHub's vast public repository corpus) and generally produces slightly better suggestions for common programming tasks.

GitLab Duo's advantage is integration. When a Duo agent can see your actual failing pipeline logs, your linked issues, your security vulnerability findings and your team's code ownership all in one system, it can do things Copilot cannot - because Copilot does not have that context. Root Cause Analysis, Vulnerability Resolution and the Developer Flow agent all depend on GitLab-specific context that is simply not available to Copilot.

The choice comes down to whether you primarily want better code completion (Copilot) or AI integrated throughout the full development lifecycle (Duo).

---

## Try It Yourself

**Exercise 1: Set up Duo in VS Code**

1. Install the **GitLab Workflow** extension from the VS Code marketplace
2. Open the Command Palette (`Ctrl+Shift+P`) → **GitLab: Authenticate**
3. Sign in to your GitLab account
4. Open any code file and start typing - Code Suggestions should appear as grey text
5. Press `Tab` to accept a suggestion

**Exercise 2: Explore Duo Chat**

1. In the VS Code GitLab extension sidebar, click the Duo Chat tab (or open it from the GitLab web UI)
2. Select some code in your editor
3. In the chat, type: `/explain`
4. Duo explains what the selected code does
5. Then ask: "What are potential security issues in this code?"
6. Then ask: "Write tests for this function"

**Exercise 3: Use Duo Chat on a GitLab MR**

1. Navigate to any open merge request in your GitLab project
2. Click the Duo Chat icon (appears in the top right area of the MR page)
3. Ask: "Summarise the changes in this MR"
4. Then ask: "What are potential issues with this implementation?"

**Exercise 4: View Root Cause Analysis (requires Duo Enterprise)**

1. Trigger a failing pipeline (introduce a syntax error in `.gitlab-ci.yml` temporarily)
2. Navigate to the failing job
3. Click **Root cause analysis** button in the job log view
4. Read Duo's analysis and suggested fix

---

## Common Mistakes

**Expecting perfect code generation from vague prompts.** Code Suggestions and Duo Chat produce better output with specific, detailed prompts. "Write a function to process user data" produces generic code. "Write a Python function that takes a list of user dictionaries with 'name', 'email' and 'age' fields, filters out users under 18 and returns them sorted by name" produces something immediately useful.

**Not reviewing AI-generated code.** Every suggestion from Duo should be reviewed before accepting. AI-generated code can contain subtle bugs, security issues, or approaches that do not fit the codebase's conventions. The speed benefit of AI comes from faster initial generation, not from skipping review.

**Assuming chat context persists between sessions.** Duo Chat does not remember previous conversations. Each new chat session starts with no memory of past interactions. If you are continuing a discussion, paste the relevant context at the start of the session.

**Not configuring context exclusion for sensitive files.** If your project has particularly sensitive code (cryptographic implementations, proprietary algorithms, compliance-critical modules), configure context exclusion so those files are never sent to AI providers. This is a proactive privacy measure worth setting up before you start using Duo.

**Confusing credits and subscription tiers.** Credits are consumed by agentic features. Basic Code Suggestions and non-agentic chat are not credit-consuming. Teams sometimes budget for Duo Pro but forget to account for credit consumption by heavy users of agentic features. Monitor credit usage in the group settings dashboard.

**Using Duo for the wrong tasks.** Duo excels at: explaining existing code, generating boilerplate, writing tests for existing functions, converting CI/CD configurations, triaging vulnerabilities. Duo is weaker at: greenfield architectural design, complex business logic requiring domain expertise, anything requiring context outside the repository. Match the tool to the task.

**Not enabling Duo on the right groups.** Duo features need to be enabled at the group level before users can access them. If a team reports that Code Suggestions are not working, check Group → Settings → GitLab Duo to verify Duo is enabled and the team members have the appropriate add-on assigned.

---

## Summary

- **GitLab Duo** is an AI suite integrated across the entire development lifecycle - not just code completion, but planning, reviewing, securing and deploying software
- **Three add-on tiers**: Duo Core (free with Premium/Ultimate since 18.0: basic Code Suggestions and Chat), Duo Pro ($19/user: full features + Agent Platform), Duo Enterprise ($39/user, Ultimate only: RCA, vulnerability resolution, AI code review)
- **Credits**: consumed by agentic features at different rates per model. Premium: 12 credits/user/month. Ultimate: 24. Additional credits: $1 each
- **Code Suggestions**: inline code completion and generation in 25+ languages. Available in VS Code, JetBrains, Neovim and the Web IDE. `Tab` to accept, `Escape` to dismiss
- **Duo Chat**: conversational AI in the IDE and GitLab web UI. Non-agentic (fast, no credits) and agentic (multi-step reasoning, credits). Slash commands: `/explain`, `/refactor`, `/tests`, `/fix`, `/reset`
- **Duo Agent Platform** (GA January 15, 2026): agents that take real actions in your GitLab project, not just answer questions
- **Built-in agents**: Planner (work planning), Security Analyst (vulnerability triage), Root Cause Analysis (failed pipeline diagnosis), Vulnerability Resolution (AI-generated code fixes), AI Code Review (automated MR review)
- **Flows**: pre-built automated multi-step workflows. Developer Flow (issue to MR), Convert to GitLab CI/CD, Fix CI/CD Pipeline, Code Review Flow
- **AI Catalog**: central management for agents and flows, including custom agents
- **MCP Client**: connects Duo agents to external tools (Jira, Slack, Confluence, Grafana) via the Model Context Protocol
- **Models**: Anthropic Claude (primary), Google Vertex AI (Codey, Gemini), Fireworks Codestral. Zero-day data retention at all providers
- **Self-hosted models** (Duo Enterprise, self-managed): vLLM, AWS Bedrock, Azure OpenAI for complete data sovereignty
- **Privacy**: no training on private code, zero-day retention, context exclusion for sensitive files, AI Transparency Center

---

## Sources and Further Reading

- [GitLab Duo documentation](https://docs.gitlab.com/user/gitlab_duo/) - complete Duo feature reference
- [Duo Agent Platform](https://about.gitlab.com/gitlab-duo/) - Agent Platform overview
- [Duo Chat documentation](https://docs.gitlab.com/user/gitlab_duo/gitlab_duo_chat/) - chat features and slash commands
- [Code Suggestions documentation](https://docs.gitlab.com/user/project/repository/code_suggestions/) - setup and configuration
- [Duo data usage and privacy](https://docs.gitlab.com/user/gitlab_duo/data_usage/) - sub-processor retention policies
- [AI Transparency Center](https://about.gitlab.com/ai-transparency/) - model and governance documentation
- [Duo self-hosted models](https://docs.gitlab.com/administration/gitlab_duo_self_hosted_models/) - running models on your own infrastructure
- [GitLab Workflow VS Code extension](https://marketplace.visualstudio.com/items?itemName=GitLab.gitlab-workflow) - VS Code extension
- [GitLab Duo JetBrains plugin](https://plugins.jetbrains.com/plugin/22325-gitlab-duo) - JetBrains plugin
- [Credits and billing](https://docs.gitlab.com/subscriptions/gitlab_duo_pro/) - credit system details

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

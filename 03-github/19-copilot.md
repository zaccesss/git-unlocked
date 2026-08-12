# GitHub Copilot

**Difficulty:** 🟡 Intermediate | **Time:** 40 minutes

GitHub Copilot is an AI coding assistant built into your editor. It suggests code as you type, answers questions about your codebase, explains errors, writes tests, refactors functions and can operate autonomously on multi-file tasks. This file covers every current plan, how to get it free as a student, how to set it up in every supported editor, how to configure it properly, how to use it effectively day-to-day and its real limitations you need to understand before relying on it.

---

## Table of Contents

- [What Copilot actually is](#what-copilot-actually-is)
- [How Copilot generates suggestions](#how-copilot-generates-suggestions)
- [Plans and pricing](#plans-and-pricing)
- [Free tier - what you get](#free-tier---what-you-get)
- [Getting Copilot free as a student](#getting-copilot-free-as-a-student)
- [Setting up Copilot in VS Code](#setting-up-copilot-in-vs-code)
- [Selecting and switching models in VS Code](#selecting-and-switching-models-in-vs-code)
- [Setting up Copilot in JetBrains IDEs](#setting-up-copilot-in-jetbrains-ides)
- [Setting up Copilot in Visual Studio](#setting-up-copilot-in-visual-studio)
- [Setting up Copilot in Neovim](#setting-up-copilot-in-neovim)
- [Copilot in the terminal - GitHub CLI](#copilot-in-the-terminal---github-cli)
- [Copilot Chat](#copilot-chat)
- [Chat participants and context variables](#chat-participants-and-context-variables)
- [Copilot Edits](#copilot-edits)
- [Agent mode](#agent-mode)
- [Copilot Workspace](#copilot-workspace)
- [Inline completions - how to use them well](#inline-completions---how-to-use-them-well)
- [Slash commands](#slash-commands)
- [Repository-level custom instructions](#repository-level-custom-instructions)
- [Copilot on GitHub.com](#copilot-on-githubcom)
- [Copilot for pull requests](#copilot-for-pull-requests)
- [Content exclusions for organisations](#content-exclusions-for-organisations)
- [Privacy and your code](#privacy-and-your-code)
- [Real limitations](#real-limitations)
- [Copilot vs alternatives](#copilot-vs-alternatives)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What Copilot Actually Is

GitHub Copilot is a product built on top of large language models (LLMs). The underlying models are developed by OpenAI (GPT-5.x), Anthropic (Claude Sonnet, Claude Opus, Claude Haiku) and Google (Gemini). GitHub wraps these models with editor integrations, IDE plugins, a CLI extension and a web interface.

When you type in your editor, Copilot sends a prompt to the model containing your current file up to the cursor, content from related open files (determined by imports, file names and similarity), your recent edits in the session and any comments or function signatures you have just written. The model predicts what text should come next and returns it to your editor as a suggestion. This happens over the network in milliseconds.

Copilot integrates with: VS Code, Visual Studio, JetBrains IDEs (IntelliJ IDEA, PyCharm, WebStorm, CLion, GoLand, Rider, DataGrip, RubyMine, PhpStorm), Neovim, Xcode (via a plugin) and the GitHub CLI.

---

## How Copilot Generates Suggestions

Understanding how Copilot works helps you use it better and understand why it sometimes fails.

**Context window:** Every LLM has a maximum amount of text it can process at once - called its context window. Copilot fills this context window with your code. The larger the context window, the more of your project Copilot can "see" when generating suggestions. GPT-5 mini has a 128k token context window; Claude Sonnet 4.5 has 200k tokens.

**Tokens:** A token is roughly 3 to 4 characters of code. A 500-line Python file is approximately 5,000 to 10,000 tokens. Large files can exceed what Copilot sends as context, meaning it only sees part of your file.

**Related file gathering:** Copilot in VS Code looks at files you have open in other tabs, files you have recently edited and files that import the current file. You can also explicitly add files to context in Chat with `#file:`.

**Embeddings for workspace search:** When you use `@workspace` in Copilot Chat, VS Code computes vector embeddings for every file in your project and does a semantic similarity search to find the most relevant files for your question. This is why workspace search takes a second or two on large projects.

---

## Plans and Pricing

| Plan | Price | Completions | Chat | Models |
|---|---|---|---|---|
| Free | $0/month | 2000/month | 50 messages/month | Claude Haiku 4.5, GPT-5 mini |
| Pro | $10/month | Unlimited | Unlimited | + Claude Sonnet 4.5 |
| Pro+ | $39/month | Unlimited | Unlimited | + Claude Opus 4.8, GPT-5.5, Gemini 3.1 Pro |
| Business | $19/user/month | Unlimited | Unlimited | Claude Haiku 4.5, GPT-5 mini, Claude Sonnet 4.5 |
| Enterprise | $39/user/month | Unlimited | Unlimited | All models, fine-tuning on internal code |

> [!NOTE]
> Prices and model availability as of August 2026. Models are updated frequently - check `github.com/features/copilot` for current information.

**Pro vs Pro+:** Pro covers everyday development. Pro+ gives access to the most capable and expensive models (Claude Opus 4.8, GPT-5.5) for the hardest algorithmic and architectural problems.

**Business vs Pro:** Business adds organisation management - content exclusions, policy controls, audit logs of Copilot usage and the ability to disable Copilot for specific repositories. Code is contractually not used for training on Business.

**Enterprise:** Adds fine-tuning Copilot on your internal codebase so it understands internal libraries and conventions, Copilot knowledge bases, SSO integration and compliance features.

---

## Free Tier - What You Get

The Copilot Free tier requires no credit card and is available to every GitHub account.

**Included:**

- 2000 code completions per month
- 50 Copilot Chat messages per month
- Access to Claude Haiku 4.5 and GPT-5 mini (switchable)
- Works in VS Code, JetBrains IDEs, Neovim and the GitHub CLI
- Copilot on GitHub.com (PR summaries, repository chat)

**Not included on Free:**

- Unlimited completions
- Premium models (GPT-5.5, Claude Opus 4.8, Gemini 3.1 Pro)
- Copilot Edits (multi-file editing mode)
- Agent mode
- Repository-level custom instructions (`.github/copilot-instructions.md`)

**How limits work:** Both counters reset on the first calendar day of each month. The counter is per GitHub account, not per editor or device. When you hit the completion limit, inline suggestions stop appearing entirely until the month resets or you upgrade.

---

## Getting Copilot Free as a Student

Verified students through the GitHub Student Developer Pack get **Copilot Pro free** with unlimited completions and chat, no monthly limit and access to Claude Sonnet 4.5 in addition to Claude Haiku 4.5 and GPT-5 mini.

**How to apply:**

1. Go to `education.github.com`
2. Click **Get Student Benefits**
3. Sign in with your GitHub account
4. Select **Student**
5. Enter your school name - start typing and select from the dropdown
6. Provide your school email address (`.ac.uk`, `.edu`, `.ac` or your institution's domain). GitHub sends a verification email - click the link to confirm
7. If your institution is not recognised automatically or you have no school email, upload a document as proof
8. Submit and wait - typically 1 to 3 business days for review

Once approved, Copilot Pro activates on your account automatically. Confirm it is active at `github.com/settings/copilot`.

> [!TIP]
> If your school email is not recognised, upload a clear, well-lit, uncropped photo of your current student ID card showing your name, institution name and an expiry or valid-through date. Blurry or cropped photos are the most common rejection reason.

---

## Setting Up Copilot in VS Code

VS Code is the most common editor for Copilot and has the deepest integration of all supported editors.

**Step 1.** Open VS Code.

**Step 2.** Open the Extensions panel: `Ctrl+Shift+X` (🪟🐧) / `Cmd+Shift+X` (🍎).

**Step 3.** Search for `GitHub Copilot`. Install the **GitHub Copilot** extension published by GitHub. The **GitHub Copilot Chat** extension installs alongside it automatically.

**Step 4.** After installation VS Code shows a sign-in prompt. Click **Sign in to GitHub**. A browser tab opens - sign in with your GitHub account and authorise the extension.

**Step 5.** Return to VS Code. You will see the Copilot icon in the bottom status bar (a circle icon). If it shows a warning triangle, click it to re-authenticate.

**Verify it is working:**

Create a new file named `test.py`. Type this comment and press Enter:

```python
# Function that reverses a string and returns it
```

After a short pause, Copilot suggests a function body as greyed-out ghost text. Press `Tab` to accept.

**Full keyboard shortcut reference:**

| Action | 🪟 Windows / 🐧 Linux | 🍎 Mac |
|---|---|---|
| Accept full suggestion | `Tab` | `Tab` |
| Dismiss suggestion | `Escape` | `Escape` |
| Accept one word at a time | `Ctrl+Right` | `Cmd+Right` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Trigger suggestion manually | `Alt+\` | `Option+\` |
| Open suggestions panel (10 alternatives) | `Ctrl+Enter` | `Ctrl+Enter` |
| Open Copilot Chat panel | `Ctrl+Alt+I` | `Cmd+Option+I` |
| Inline chat at cursor | `Ctrl+I` | `Cmd+I` |
| Open Copilot Edits | `Ctrl+Shift+I` | `Cmd+Shift+I` |

**VS Code settings for Copilot:**

Open settings (`Ctrl+,` / `Cmd+,`) and search `copilot`. Key settings you may want to adjust:

```json
{
  "github.copilot.enable": {
    "*": true,
    "markdown": false,
    "plaintext": false
  },
  "github.copilot.editor.enableAutoCompletions": true,
  "github.copilot.renameSuggestions.triggerAutomatically": true
}
```

The `enable` object lets you disable completions per language ID. Setting `"markdown": false` stops Copilot from suggesting text while writing prose or documentation - useful if you want to write your own words.

---

## Selecting and Switching Models in VS Code

On Copilot Pro, Pro+ and Business plans you can choose which model powers your Chat and completions.

**Switch the Chat model:**

1. Open Copilot Chat
2. Click the model name shown in the top-right corner of the chat panel (e.g. "Claude Sonnet 4.5")
3. Select your preferred model from the dropdown

**Available models by plan:**

| Model | Free | Pro | Pro+ | Business |
|---|---|---|---|---|
| GPT-5 mini | Yes | Yes | Yes | Yes |
| Claude Haiku 4.5 | Yes | Yes | Yes | Yes |
| Claude Sonnet 4.5 | No | Yes | Yes | Yes |
| Claude Opus 4.8 | No | No | Yes | No |
| GPT-5.5 | No | No | Yes | No |
| Gemini 3.1 Pro | No | No | Yes | No |

**When to use which model:**

- **GPT-5 mini** - fast, reliable, good for everyday completions and straightforward Chat questions
- **Claude Sonnet 4.5** - strong on complex reasoning, long context and explaining large files. Often better than GPT-5 mini for understanding big codebases or writing detailed explanations
- **Claude Haiku 4.5** - lightweight and fast, best for quick completions and simple Chat queries
- **Claude Opus 4.8 / GPT-5.5 (Pro+)** - the most capable available. Reserve for the hardest problems where the other models keep making errors

---

## Setting Up Copilot in JetBrains IDEs

Works in IntelliJ IDEA, PyCharm, WebStorm, CLion, GoLand, Rider, DataGrip, RubyMine and PhpStorm. Students with the Developer Pack get all JetBrains IDEs free.

**Step 1.** Open your JetBrains IDE.

**Step 2.** Go to **File → Settings** (🪟🐧) or **[IDE Name] → Settings** (🍎). Shortcut: `Ctrl+Alt+S` (🪟🐧) / `Cmd+,` (🍎).

**Step 3.** Navigate to **Plugins → Marketplace**.

**Step 4.** Search `GitHub Copilot`. Click **Install**.

**Step 5.** Restart the IDE when prompted.

**Step 6.** After restart go to **Tools → GitHub Copilot → Login to GitHub**. A browser tab opens. Sign in and authorise.

**JetBrains keyboard shortcuts:**

| Action | 🪟🐧 | 🍎 |
|---|---|---|
| Accept suggestion | `Tab` | `Tab` |
| Dismiss | `Escape` | `Escape` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Open Copilot Chat | `Ctrl+Shift+C` | `Cmd+Shift+C` |

Copilot Chat in JetBrains appears in the right-side AI panel. It supports the same slash commands as VS Code Chat.

---

## Setting Up Copilot in Visual Studio

For developers using Visual Studio 2022 on Windows for .NET, C# or C++ development.

**Step 1.** Open Visual Studio 2022 (version 17.6 or later required).

**Step 2.** Go to **Extensions → Manage Extensions**.

**Step 3.** Search `GitHub Copilot`. Click **Download**.

**Step 4.** Restart Visual Studio to complete installation.

**Step 5.** Go to **View → GitHub Copilot Chat** to open the Chat panel. Sign in with your GitHub account when prompted.

Inline completions work identically to VS Code. The Chat panel is accessible from the **View** menu or by pressing `Ctrl+\` then `Ctrl+C`.

---

## Setting Up Copilot in Neovim

**Prerequisites:** Neovim 0.6.0 or later, Node.js 18 or later installed.

**Using vim-plug:**

Add to your `init.vim`:

```vim
Plug 'github/copilot.vim'
```

Run `:PlugInstall` in Neovim.

**Using lazy.nvim (recommended for modern Neovim):**

```lua
{
  "github/copilot.vim",
  config = function()
    vim.g.copilot_assume_mapped = true
  end,
}
```

**Authenticate:**

Run `:Copilot setup` in Neovim. Copy the device code shown, go to `github.com/login/device` in your browser, paste the code and authorise.

**Remapping Tab if it conflicts with nvim-cmp:**

If you use nvim-cmp or another completion plugin that also uses Tab, remap Copilot's accept key:

```lua
vim.g.copilot_no_tab_map = true
vim.api.nvim_set_keymap("i", "<C-J>", 'copilot#Accept("<CR>")', { silent = true, expr = true })
```

This maps `Ctrl+J` to accept instead of `Tab`.

---

## Copilot in the Terminal - GitHub CLI

The GitHub CLI Copilot extension brings AI assistance directly to your terminal without switching to an editor.

**Install GitHub CLI:**

🪟 **Windows:**

```powershell
winget install --id GitHub.cli
```

🍎 **Mac:**

```bash
brew install gh
```

🐧 **Linux (Debian/Ubuntu):**

```bash
sudo apt update && sudo apt install gh
```

**Authenticate:**

```bash
gh auth login
```

**Install the Copilot extension:**

```bash
gh extension install github/gh-copilot
```

**Upgrade the extension:**

```bash
gh extension upgrade gh-copilot
```

**The two main commands:**

```bash
gh copilot suggest "list all git-tracked files modified in the last 7 days"
gh copilot explain "tar -czf archive.tar.gz ./folder"
```

`suggest` asks what shell you are using (bash, zsh or PowerShell) and generates the correct syntax for it. You can then choose to run the command, copy it to clipboard or ask for further explanation.

`explain` takes any shell command and returns a plain-English explanation of every flag, pipe and argument.

**Interactive mode:**

```bash
gh copilot suggest
```

Running `suggest` with no argument opens an interactive prompt.

**Set up shortcuts:**

🍎🐧 Add to `~/.bashrc` or `~/.zshrc`:

```bash
alias '??'='gh copilot suggest -t shell'
alias 'git?'='gh copilot suggest -t git'
```

🪟 Add to your PowerShell `$PROFILE`:

```powershell
function suggest { gh copilot suggest -t shell $args }
function githelp { gh copilot suggest -t git $args }
```

---

## Copilot Chat

Copilot Chat is a full conversational AI interface embedded in your editor, separate from inline completions. It maintains conversation history and is used for explanations, debugging, writing tests and generating larger blocks of code.

**Opening Chat in VS Code:**

- Click the chat bubble icon in the Activity Bar (left sidebar)
- Press `Ctrl+Alt+I` (🪟🐧) / `Cmd+Option+I` (🍎) for the panel
- Press `Ctrl+I` / `Cmd+I` for **inline chat** which opens at the cursor position in your file

**Inline chat vs panel chat:**

- **Inline chat** (`Ctrl+I`): A small input box opens in your code file at the cursor. Type a request and Copilot edits the surrounding code in place. Press `Enter` to apply changes or `Escape` to cancel. Best for quick, localised changes like "make this function handle null inputs".
- **Panel chat** (`Ctrl+Alt+I`): A dedicated side panel with a persistent conversation history. Better for explanations, multi-step tasks and questions about multiple files.

**What Chat can do:**

- Ask questions about code: "what does this class do and why does it inherit from Thread?"
- Debug: paste an error and ask "what is causing this traceback?"
- Write tests: "write pytest tests for this function covering all edge cases"
- Refactor: "rewrite this using a list comprehension instead of the for loop"
- Generate documentation: "write a Google-style docstring for this function"
- Explain unfamiliar code: "explain this regex step by step: `^(?:(?:25[0-5]|2[0-4][0-9])`"
- Review: "review this function for potential security issues"
- Ask about patterns: "what design pattern is this using and why might you choose it?"

---

## Chat Participants and Context Variables

Copilot Chat has **chat participants** (scoped agents) and **context variables** (ways to inject specific content into your question).

**Chat participants** - prefix with `@`:

| Participant | What it does |
|---|---|
| `@workspace` | Searches your entire project using embeddings to find relevant files. Use for questions about the whole codebase. |
| `@vscode` | Answers questions about VS Code settings, keybindings, extensions and commands. |
| `@terminal` | Helps with terminal commands and explains terminal output in VS Code's integrated terminal. |
| `@github` | Searches GitHub repositories, issues and public code. Requires GitHub sign-in. |

Example usage: `@workspace where is the rate limiting logic implemented?`

**Context variables** - prefix with `#`:

| Variable | What it injects |
|---|---|
| `#file:filename` | The full contents of a specific file |
| `#selection` | Your currently selected text in the editor |
| `#editor` | The entire content of your active editor tab |
| `#terminalLastCommand` | The last command run in the VS Code integrated terminal |
| `#terminalSelection` | Text currently selected in the terminal |
| `#codebase` | Relevant code from across the workspace (similar to `@workspace`) |

**Practical examples:**

```
#file:auth.py explain how the login function validates passwords

#file:routes.js #file:middleware.js are there any authentication gaps between these two files?

#selection convert this to use async/await instead of callbacks
```

Using `#file:` is the most important habit for getting accurate, relevant answers. Without it, Copilot may not have access to the specific code you are asking about.

---

## Copilot Edits

Copilot Edits is a mode that lets Copilot make changes across multiple files simultaneously from a single instruction. Instead of getting text responses in Chat, you get reviewable code diffs.

**Open Copilot Edits in VS Code:**

Press `Ctrl+Shift+I` (🪟🐧) / `Cmd+Shift+I` (🍎) or go to **View → Copilot Edits**.

**How to use it:**

1. Open Copilot Edits
2. Click **Add Files** to add the files you want Copilot to edit or drag files from the Explorer
3. Type your instruction - for example "add input validation to all route handlers" or "convert all callback-based async code to async/await"
4. Copilot analyses the files and proposes a set of diffs across all relevant files
5. Review each changed file - additions in green, deletions in red
6. Click **Accept All** to apply everything, **Discard All** to reject or go file by file

**Good use cases:**

- Renaming a function or variable consistently across a whole codebase
- Adding error handling to a set of similar functions
- Migrating from one pattern to another (e.g. callbacks to promises or dict access to dataclass attributes)
- Adding type annotations to Python or JavaScript across multiple files
- Standardising import style or formatting conventions across a module

> [!NOTE]
> Copilot Edits requires Copilot Pro or above. Not available on the Free tier as of March 2026.

---

## Agent Mode

Agent mode is the most autonomous Copilot feature. It runs in a loop: reads your request, plans actions, executes them, checks results and repeats until done or it needs your input.

**What agent mode can do:**

- Read any file in your project
- Write and modify multiple files
- Run terminal commands (with your explicit confirmation before each one)
- Install packages (`npm install`, `pip install` etc.)
- Run your test suite and iterate based on failures
- Search the web for documentation (on Pro+)

**Enable agent mode in VS Code:**

1. Open Copilot Chat
2. Click the mode selector dropdown at the top of the chat panel (may show "Ask" or "Edit")
3. Select **Agent**

**Example tasks for agent mode:**

- "Set up a new Flask REST API with user authentication using JWT, a SQLite database and pytest tests - create all necessary files"
- "Find all places where we use `var` and convert them to `const` or `let`, then run the tests and fix any failures"
- "The terminal shows a ModuleNotFoundError - install the missing dependency and update requirements.txt"

**Confirming terminal commands:**

Before running any terminal command, agent mode shows you the exact command and waits for your confirmation. You see precisely what it will run before it runs. Read every command before confirming.

> [!CAUTION]
> Agent mode can run destructive commands - `rm -rf`, database migrations, file deletions. Always read each proposed command before confirming. Run agent mode with a clean Git state so you can revert with `git checkout` or `git stash` if something goes wrong.

**Available on:** Copilot Pro+ and Business/Enterprise. Not available on Free or Pro as of March 2026.

---

## Copilot Workspace

Copilot Workspace is a separate GitHub feature, accessed on GitHub.com, for implementing complete features from a GitHub issue.

**How it works:**

1. Open any GitHub issue in a repository you have access to
2. Click **Open in Copilot Workspace** (shown on the issue page)
3. Copilot reads the issue description and the repository structure
4. It generates a step-by-step implementation plan listing which files need to change and how
5. You review and can modify the plan before implementation
6. Copilot implements the plan, creating a branch with the code changes
7. You review the resulting diff and open a pull request

**Copilot Workspace vs Agent mode:**

Workspace is issue-centric and runs entirely on GitHub.com - no local editor required. Agent mode runs in VS Code and can execute code and tests locally. Use Workspace when implementing a feature described in a well-written issue; use Agent mode when the task requires running, testing and iterating locally.

**Access:** Available to Copilot Pro, Pro+ and Business users. Access via `copilot-workspace.githubnext.com` or directly from issues.

---

## Inline Completions - How to Use Them Well

The quality of Copilot's inline suggestions depends almost entirely on how much clear context you give it.

**Write the comment before writing the code:**

```python
# Parse a date string in ISO 8601 format and return a datetime object.
# Raise ValueError with a descriptive message if the string is not valid ISO 8601.
# Examples: "2024-01-15T09:30:00Z", "2024-01-15", "2024-01-15T09:30:00+01:00"
def parse_iso_date(date_string: str) -> datetime:
```

With this comment, Copilot generates a much more complete and correct function body than it would from just `def parse_iso_date(s):`.

**Use descriptive function and variable names:**

`getUserByEmailAndVerifyPassword(email, password)` produces better completions than `getUser(a, b)`.

**Keep related code in the same file or open in tabs:**

If you are writing a function that should match the style and patterns of others, make sure those related functions are visible. Copilot uses surrounding code as its style and pattern reference.

**Name files meaningfully:**

A file named `database_connection.py` with existing database imports produces better database-related suggestions than a blank scratch file.

**Accept partial completions:**

Press `Ctrl+Right` (🪟🐧) / `Cmd+Right` (🍎) to accept the suggestion one word at a time. Use this when the start of a suggestion is correct but the end diverges from what you want.

**Open the completions panel:**

Press `Ctrl+Enter` to see the top 10 alternative suggestions side by side. Useful when the default suggestion is not quite right but the idea is correct.

**Trigger manually when suggestions do not appear:**

Press `Alt+\` (🪟🐧) / `Option+\` (🍎) to trigger a suggestion at the current cursor position.

---

## Slash Commands

In Copilot Chat, slash commands are shortcuts to common operations. Type them at the very start of a chat message.

| Command | What it does |
|---|---|
| `/explain` | Explain the selected code in plain English |
| `/fix` | Identify and fix a problem in the selected code |
| `/tests` | Generate unit tests for the selected code |
| `/doc` | Generate a documentation comment (JSDoc, docstring, XML doc etc.) |
| `/simplify` | Simplify the selected code without changing its behaviour |
| `/new` | Scaffold a new file, component or project from a description |
| `/newNotebook` | Create a new Jupyter notebook |
| `/clear` | Clear the current Chat conversation |
| `/help` | Show available commands and how to use Copilot Chat |

**Practical examples:**

Select a complex function → type `/tests` → Copilot generates unit tests specifically for that function including edge cases.

Select a regular expression → type `/explain` → Copilot explains every part of the pattern in plain English.

Type `/new Express.js REST API endpoint for user registration with email validation` → Copilot generates a complete file with the route, validation and error handling.

---

## Repository-Level Custom Instructions

You can give Copilot standing instructions that apply to every Chat interaction in a specific repository. This is done via a file at `.github/copilot-instructions.md`.

**Create the file:**

In your repository, create `.github/copilot-instructions.md` and commit it.

**Write your instructions - example:**

```markdown
# Copilot Instructions

## Language and runtime
- This project uses Python 3.12 with strict type hints throughout.
- All async code uses asyncio. Never use threading.

## Code style
- Follow PEP 8. Maximum line length is 88 characters (Black formatter).
- Use f-strings, not `.format()` or `%`.
- All public functions must have a Google-style docstring.

## Testing
- Write pytest tests for every function.
- Use `pytest.mark.parametrize` for multiple input/output cases.
- Mock external HTTP calls with `responses` or `httpx.MockTransport`.

## Project context
- This is a data pipeline API built with FastAPI and PostgreSQL.
- Database access uses SQLAlchemy 2.0 async sessions. Never use synchronous sessions.
- Authentication uses OAuth2 Bearer tokens. Token validation happens in `src/auth/middleware.py`.
- All monetary values are stored as integers representing pence/cents to avoid floating point issues.

## What to avoid
- Never use `print()` for logging - use `structlog` via `src/utils/logger.py`.
- Never use bare `except:` clauses - always catch specific exceptions.
- Never use mutable default arguments.
- Do not use `global` variables.
```

**What these instructions do:**

Copilot Chat reads this file and applies these instructions to every response in the repository. Inline completions also take these instructions into account when generating suggestions. This means every developer on the team gets Copilot suggestions that follow the same project conventions.

> [!NOTE]
> Custom instructions via `.github/copilot-instructions.md` are available on Copilot Pro and above. Not supported on the Free tier as of March 2026.

---

## Copilot on GitHub.com

Copilot is available on GitHub.com without opening an editor.

**Repository chat:**

On any repository page, click the **Copilot** icon (circle icon, top-right corner). A chat panel opens with context from the entire repository. Ask questions like:

- "How is authentication implemented in this project?"
- "Which file handles database migrations?"
- "What does the deploy workflow do and when does it trigger?"
- "Which files would I need to edit to add a new REST endpoint?"

**Issue chat:**

On any issue page, click the Copilot icon. Copilot analyses the issue and suggests implementation approaches, identifies the relevant files and can compare it to similar past issues.

**Pull request chat:**

On any PR page, click the Copilot icon to ask questions about the PR: "what is the purpose of this change?" or "are there edge cases these tests do not cover?"

**Auto-generate PR descriptions:**

When opening or editing a PR, click the **Copilot sparkle** icon next to the description field. Copilot reads the diff and commit messages and writes a draft description. Always edit it before submitting.

**Code search assistance:**

On `github.com/search`, Copilot helps interpret natural language search queries and suggests relevant qualifier combinations.

---

## Copilot for Pull Requests

Copilot adds AI-powered features to the pull request workflow.

**Auto-generate PR descriptions:**

When creating or editing a PR, click the Copilot icon next to the description field. Copilot reads the entire diff and all commit messages and generates a description covering what changed and why. Review and edit before submitting.

**PR review summaries:**

On large PRs with many changed files, Copilot can summarise the key changes per file, making it faster to orient yourself during review.

**Inline code suggestions from review comments:**

When a reviewer leaves a comment asking for a change, Copilot can generate the specific code change inline. The reviewer's comment gains a **Copilot suggestion** option that produces code addressing their feedback, which the author can apply with one click.

**Copilot Autofix for security alerts:**

When GitHub's CodeQL code scanning finds a security vulnerability, Copilot can generate an automated fix. In the **Security** tab, vulnerability alerts show a **Fix with Copilot** button. Clicking it produces a new branch and PR with the corrected code for you to review and merge.

---

## Content Exclusions for Organisations

On Copilot Business and Enterprise plans, organisation admins can prevent specific files or repositories from being used as Copilot context. This is called **content exclusion**.

**Why use it:**

- Prevent Copilot from using sensitive or proprietary internal libraries as suggestion context
- Exclude files that contain hardcoded credentials or internal API specifications
- Exclude repositories with unclear IP status (e.g. from an acquisition still under legal review)

**Configuring content exclusions:**

1. Go to your organisation settings on GitHub.com
2. Navigate to **Code, planning and automation → GitHub Copilot → Content exclusions**
3. Add exclusion patterns using `.gitignore` syntax:

```
# Exclude all files under the secrets directory
secrets/**

# Exclude environment files anywhere in the organisation
**/.env
**/*.env.production

# Exclude an entire repository
/internal-legacy-api/**

# Exclude configuration files with sensitive data
**/config/production.yml
```

**Effect of exclusions:**

When a file matches an exclusion pattern, Copilot will not use it as context when generating suggestions, even if that file is open in the developer's editor. The developer can still read and edit the file normally - exclusions only affect what Copilot sends to the AI model, not what the developer can access.

---

## Privacy and Your Code

**What gets sent to servers:**

Every time Copilot generates a suggestion, your code context (surrounding file content, relevant open files) is sent over the network to the model API. This happens on every keystroke (with a short debounce delay to avoid spam).

**Training opt-out:**

By default on Free and Pro plans, GitHub may use your prompts and Copilot's suggestions to improve its models. To opt out:

1. Go to `github.com/settings/copilot`
2. Under **Suggestions matching public code**, choose **Block** to prevent Copilot from suggesting code that matches public repository content verbatim
3. Under **Allow GitHub to use my code snippets for product improvements**, untick to opt out of training data collection

**Copilot Business and Enterprise:**

- Code is contractually never used to train Copilot models
- Prompts and suggestions are not retained by GitHub beyond the immediate request
- Data processing is covered by GitHub's Data Protection Agreement (DPA)

**Practical implication:**

Even with training opted out, your code is still transmitted to process the request. Think of it identically to pasting your code into any AI chat tool - it leaves your machine temporarily. The difference is whether it is retained and used for training afterwards.

> [!IMPORTANT]
> Never type API keys, database credentials, private keys, passwords, personal data or any confidential information into Copilot Chat or write them in comments that are designed to prompt Copilot. Treat every character you type in Copilot as potentially transmitted to a third-party server.

---

## Real Limitations

These are not minor edge cases - they are fundamental characteristics of how LLMs work that affect how safely and effectively you can use Copilot.

**Copilot makes mistakes and you are responsible for the output.** Generated code can compile, pass basic tests and still be wrong in subtle ways. Review every suggestion as carefully as code written by a junior developer who is not familiar with your specific requirements.

**It cannot see your whole project.** Even with `@workspace`, Copilot works within a context window limit. On a large project with hundreds of files, it sees a relevant subset - not everything. It may not know about a utility function in another file, may duplicate existing logic or may suggest patterns inconsistent with the rest of the codebase.

**It can suggest insecure code.** Academic research has demonstrated that Copilot produces SQL injection vulnerabilities, insecure random number generation, use of deprecated cryptographic primitives and other security issues at a non-trivial rate. Never skip security review on the grounds that Copilot wrote the code.

**It hallucinates APIs and library methods.** Copilot sometimes invents functions, class methods, parameters or entire modules that do not exist. For example, it may write `df.group_and_aggregate()` when no such pandas method exists. Always verify unfamiliar API calls in the official documentation before using them.

**Its quality degrades on less common languages.** Copilot is strongest on Python, JavaScript, TypeScript, Java, C#, C++ and Go. For Rust, Haskell, Erlang, VHDL, SystemVerilog, Assembly and other lower-frequency languages, suggestion accuracy drops and the rate of errors increases.

**It does not know your business domain.** Copilot has no idea what your application does, what your database schema represents or what your organisation's conventions mean. Without a `.github/copilot-instructions.md` file, it guesses based on file names and nearby code.

**Its knowledge has a training cutoff.** Copilot does not know about library versions, API changes or framework features released after its training cutoff. It may suggest patterns from an older version of a library that have been deprecated or removed.

**It cannot verify its own suggestions.** Unless using agent mode with tests enabled, Copilot cannot run code to check whether what it suggested actually works. It generates text based on pattern matching, not by simulating execution.

---

## Copilot vs Alternatives

Copilot is not the only AI coding assistant. Understanding the alternatives helps you choose the right tool.

| Tool | Models | Editor support | Free tier | Key differentiator |
|---|---|---|---|---|
| **GitHub Copilot** | GPT-5.x, Claude Sonnet, Claude Opus | VS Code, JetBrains, Neovim, Visual Studio | Yes (2000 completions) | Deepest GitHub integration; PR summaries, Workspace |
| **Cursor** | GPT-5.x, Claude Sonnet | Cursor (VS Code fork) | Yes (limited) | Very capable agent mode; separate editor |
| **Windsurf (Codeium)** | Claude Sonnet, GPT-5.x | VS Code, JetBrains | Yes (generous) | More generous free tier; Cascade agent |
| **Tabnine** | Own model + GPT-5 | Many editors including Vim | Yes (limited) | Can run entirely on-device for air-gapped environments |
| **Amazon Q Developer** | Amazon Bedrock | VS Code, JetBrains | Yes | Best suggestions for AWS infrastructure and SDK code |
| **Sourcegraph Cody** | Claude, GPT-5.x | VS Code, JetBrains | Yes | Strong codebase-wide semantic search context |

**Which to choose:**

If you are already on GitHub and want GitHub-specific features (PR auto-summaries, issue-to-code via Workspace, CodeQL Autofix), Copilot is the natural choice and you get it free as a student. If you want the best pure agent experience and are comfortable using a separate editor, Cursor is worth evaluating. If budget is a constraint beyond the free tier, Windsurf/Codeium has a more generous free plan. If you work in an air-gapped or highly regulated environment, Tabnine's on-device mode is unique.

---

## Try It Yourself

**Exercise 1 - First inline completion:**

1. Open VS Code with Copilot installed and authenticated
2. Create a new file called `practice.py`
3. Type the following and press Enter:

   ```python
   # Function that takes a list of integers and returns a dict with
   # keys 'mean', 'median', 'mode' and their computed values.
   # Handle empty lists by returning None for each key.
   # Do not use statistics module - implement from scratch.
   def describe_list(numbers: list[int]) -> dict:
   ```

4. Wait for the suggestion and press `Tab` to accept
5. Read the generated code carefully - does it handle the empty list case?

**Exercise 2 - Slash commands:**

1. Select the entire function from Exercise 1
2. Open Copilot Chat (`Ctrl+Alt+I` / `Cmd+Option+I`)
3. Type `/tests` and press Enter - read the generated tests, do they cover edge cases?
4. Type `/doc` with the function selected - read the generated docstring

**Exercise 3 - Using context variables:**

1. Create two files: `calculator.py` with several functions and `test_calculator.py` (empty)
2. In Chat type: `#file:calculator.py write comprehensive pytest tests for every function in this file, including edge cases and parametrize where appropriate`
3. Compare the quality and specificity to the `/tests` output from Exercise 2

**Exercise 4 - Terminal Copilot:**

```bash
gh copilot explain "git log --oneline --graph --decorate --all"
gh copilot suggest "find all Python files modified in the last 3 days and count the total lines in them"
```

**Exercise 5 - Custom instructions:**

1. In any repository, create `.github/copilot-instructions.md`
2. Add 5 lines describing your preferred coding style (language version, naming convention, testing framework, what to avoid)
3. Ask Copilot Chat to write a short function - verify it follows your stated preferences

---

## Common Mistakes

**Accepting suggestions without reading them.** Tab is easy to press and Copilot is fast. It is very easy to accept many suggestions in a row without understanding what they do. Read every suggestion before accepting.

**Asking vague questions.** "Fix my code" produces a guess. "The `calculate_discount` function returns `None` when the discount percentage is 100 - here is the function, here is the test that fails, what is causing this?" produces an accurate answer. Be specific.

**Not using `#file:` context.** Without `#file:`, Copilot may not have access to the code you are asking about. Attaching the relevant file is the single biggest improvement to Chat response quality.

**Skipping security review on generated code.** Copilot-generated code has security vulnerabilities at roughly the same rate as average human-written code. Do not skip review.

**Trusting hallucinated APIs.** If Copilot suggests a method you do not recognise, check the library documentation before using it. It may not exist.

**Running agent mode without a clean Git state.** Agent mode can make many file changes. If you have uncommitted work, run `git stash` or commit first so you can recover cleanly.

**Pasting credentials into Chat.** A real database connection string or API key typed into Copilot Chat leaves your machine. Never paste real secrets.

**Not adding `.github/copilot-instructions.md` on team projects.** Without instructions, every developer gets different suggestion styles. A shared instructions file gives everyone consistent behaviour.

---

## Summary

- Copilot is powered by multiple LLMs (GPT-5.x, Claude Sonnet, Claude Opus) and runs via the GitHub API - it is not local
- **Free tier:** 2000 completions and 50 chat messages per month; resets on the 1st of each month
- **Students get Pro free** via the GitHub Student Developer Pack at `education.github.com`
- Install in VS Code via the **GitHub Copilot** extension; sign in with your GitHub account
- **Switch models** in VS Code by clicking the model name in the Chat panel header
- Use `@workspace` to search across your project and `#file:filename` to inject specific file context
- **Copilot Edits** makes multi-file changes from a single instruction; requires Pro
- **Agent mode** runs terminal commands autonomously in a loop; requires Pro+/Business - always read commands before confirming
- **`.github/copilot-instructions.md`** sets repository-level instructions applied to all Chat interactions
- **Content exclusions** on Business/Enterprise prevent sensitive files from being used as context
- Copilot makes mistakes, hallucinates APIs and can suggest insecure code - always review its output
- Alternatives include Cursor (best agent UX), Windsurf (generous free tier) and Tabnine (on-device mode)

---

## Sources and Further Reading

- [GitHub Copilot documentation](https://docs.github.com/en/copilot) - complete official reference
- [GitHub Copilot plans and pricing](https://github.com/features/copilot) - current plan comparison
- [Copilot in VS Code](https://code.visualstudio.com/docs/copilot/overview) - VS Code-specific guide with all shortcuts
- [Chat context variables reference](https://code.visualstudio.com/docs/copilot/copilot-chat#_chat-variables) - full list of `#` variables
- [Copilot custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot) - `.github/copilot-instructions.md` guide
- [Copilot content exclusions](https://docs.github.com/en/copilot/managing-copilot/configuring-and-auditing-content-exclusion) - org-level exclusion configuration
- [Copilot Workspace](https://githubnext.com/projects/copilot-workspace) - issue-to-implementation workflow
- [Copilot Trust Center](https://resources.github.com/copilot-trust-center/) - privacy, security and data handling details
- [gh copilot extension](https://github.com/github/gh-copilot) - terminal Copilot source and documentation
- [GitHub Copilot impact research](https://github.blog/developer-skills/github/research-quantifying-github-copilots-impact-in-the-enterprise/) - GitHub's published research on Copilot productivity

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

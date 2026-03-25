# GitHub Copilot

**Difficulty:** 🟡 Intermediate | **Time:** 30 minutes

GitHub Copilot is an AI coding assistant built into your editor. It suggests code as you type, answers questions about your codebase, explains errors, writes tests, refactors functions and can operate autonomously on multi-file tasks. This file covers every current plan, how to get it free as a student, how to use it effectively in VS Code and the command line, and its real limitations.

---

## Table of Contents

- [What Copilot actually is](#what-copilot-actually-is)
- [Plans and pricing](#plans-and-pricing)
- [Free tier - what you get](#free-tier---what-you-get)
- [Getting Copilot free as a student](#getting-copilot-free-as-a-student)
- [Setting up Copilot in VS Code](#setting-up-copilot-in-vs-code)
- [Setting up Copilot in JetBrains IDEs](#setting-up-copilot-in-jetbrains-ides)
- [Copilot in the terminal - GitHub CLI](#copilot-in-the-terminal---github-cli)
- [Copilot Chat](#copilot-chat)
- [Copilot Edits and agent mode](#copilot-edits-and-agent-mode)
- [Inline completions - how to use them well](#inline-completions---how-to-use-them-well)
- [Slash commands](#slash-commands)
- [Copilot on GitHub.com](#copilot-on-githubcom)
- [Copilot for pull requests](#copilot-for-pull-requests)
- [Privacy and your code](#privacy-and-your-code)
- [Real limitations](#real-limitations)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What Copilot Actually Is

GitHub Copilot is powered by large language models (LLMs) trained on publicly available code and text. When you type in your editor, Copilot reads your current file, related open files, your comments and function names and predicts what code should come next.

It is not a search engine. It does not look up Stack Overflow answers. It generates code by pattern-matching on its training data and your current context. This means it can produce code that looks correct but is subtly wrong, uses deprecated APIs or contains security vulnerabilities. Always review what it produces.

Copilot integrates with VS Code, Visual Studio, JetBrains IDEs (IntelliJ, PyCharm, WebStorm etc.), Neovim, Xcode and the GitHub CLI.

---

## Plans and Pricing

| Plan | Price | Who it is for |
|---|---|---|
| Free | $0/month | Any GitHub account - limited completions |
| Pro | $10/month | Individual developers - unlimited completions |
| Pro+ | $39/month | Power users - access to premium models including GPT-4o and Claude Sonnet |
| Business | $19/user/month | Teams and organisations |
| Enterprise | $39/user/month | Large organisations - fine-tuning on internal code |

> [!NOTE]
> Prices as of March 2026. Check `github.com/features/copilot` for current pricing.

---

## Free Tier - What You Get

The Copilot Free tier (available on any GitHub account) gives you:

- **2000 code completions per month** - inline suggestions as you type
- **50 chat messages per month** - Copilot Chat questions
- Access to the Claude Sonnet 3.5 model and GPT-4o (selectable)
- Works in VS Code and other supported editors

The free tier resets on the first of each month. When you hit the limit, completions stop until the next reset or you upgrade.

---

## Getting Copilot Free as a Student

Verified students through the GitHub Student Developer Pack get **Copilot Pro free** - unlimited completions and chat with no monthly limit.

**How to apply:**

1. Go to `education.github.com`
2. Click **Get Student Benefits**
3. Sign in with your GitHub account
4. Select **Student**
5. Enter your school name and provide proof - a school email address (`*.ac.uk`, `*.edu` etc.) is usually enough; some schools require uploading a student ID or enrolment letter
6. Submit the application
7. Wait for approval - typically 1 to 3 days

Once approved, Copilot Pro is activated automatically on your account. Go to `github.com/settings/copilot` to confirm it is active.

> [!TIP]
> If your school email is not automatically recognised, upload a clear photo of your student ID card or an enrolment letter on official letterhead. Blurry or cropped photos are the most common reason for rejection.

---

## Setting Up Copilot in VS Code

**Step 1.** Open VS Code.

**Step 2.** Click the Extensions icon in the left sidebar (or press `Ctrl+Shift+X` on 🪟 Windows / `Cmd+Shift+X` on 🍎 Mac).

**Step 3.** Search for `GitHub Copilot` and install the **GitHub Copilot** extension (by GitHub). This also installs **GitHub Copilot Chat** automatically.

**Step 4.** After installation, VS Code will prompt you to sign in to GitHub. Click **Sign In** and complete the browser authentication flow.

**Step 5.** Once signed in, Copilot is active. You will see the Copilot icon in the bottom status bar.

**Verify it is working:**

Open any code file and start typing a function name or a comment describing what you want. Copilot suggestions appear as greyed-out text. Press `Tab` to accept or `Escape` to dismiss.

**Keyboard shortcuts:**

| Action | 🪟 Windows / 🐧 Linux | 🍎 Mac |
|---|---|---|
| Accept suggestion | `Tab` | `Tab` |
| Dismiss suggestion | `Escape` | `Escape` |
| Next suggestion | `Alt+]` | `Option+]` |
| Previous suggestion | `Alt+[` | `Option+[` |
| Open Copilot Chat | `Ctrl+Alt+I` | `Cmd+Option+I` |
| Inline chat | `Ctrl+I` | `Cmd+I` |

---

## Setting Up Copilot in JetBrains IDEs

Students with Copilot Pro via the Student Developer Pack also get JetBrains IDEs free (IntelliJ IDEA Ultimate, PyCharm Professional, WebStorm etc.).

**Step 1.** Open your JetBrains IDE.

**Step 2.** Go to **File → Settings** (🪟 Windows/🐧 Linux) or **IDE Name → Settings** (🍎 Mac).

**Step 3.** Go to **Plugins**.

**Step 4.** Search for `GitHub Copilot` and click **Install**.

**Step 5.** Restart the IDE.

**Step 6.** Go to **Tools → GitHub Copilot → Login to GitHub** and complete the sign-in.

Copilot works identically across JetBrains IDEs once installed.

---

## Copilot in the Terminal - GitHub CLI

GitHub CLI has a built-in Copilot command that lets you ask questions and get shell commands without leaving the terminal.

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
sudo apt install gh
```

**Authenticate:**

```bash
gh auth login
```

**Install the Copilot extension:**

```bash
gh extension install github/gh-copilot
```

**Use Copilot in the terminal:**

```bash
gh copilot suggest "undo my last git commit but keep the changes"
gh copilot explain "git rebase -i HEAD~3"
```

`suggest` gives you a shell command to accomplish what you described. `explain` explains what a command does in plain English.

---

## Copilot Chat

Copilot Chat is a full conversational interface built into your editor. Access it in VS Code by clicking the chat icon in the left sidebar or pressing `Ctrl+Alt+I` (🪟🐧) / `Cmd+Option+I` (🍎).

**What you can ask:**

- Explain a file or function: select the code, then ask "explain this"
- Debug an error: paste the error message and ask what is causing it
- Write a test: "write unit tests for this function"
- Refactor: "refactor this to remove duplication"
- Generate documentation: "write a JSDoc comment for this function"

**Context - how Copilot knows about your code:**

Copilot Chat can see:

- Your currently open file
- Files you have open in other tabs
- Code you have selected before opening chat
- Files you explicitly reference with `#file:filename`

Use `#file:` to give Copilot context from a specific file: `#file:auth.js what does the login function do?`

---

## Copilot Edits and Agent Mode

**Copilot Edits** (VS Code) allows Copilot to make changes across multiple files simultaneously rather than one at a time.

Open it via **View → Copilot Edits** or the Copilot icon in the sidebar. Describe a change and Copilot proposes a diff across however many files are needed. You review and accept or reject.

**Agent mode** (available on Copilot Pro+ and Business/Enterprise) goes further. Copilot can autonomously run terminal commands, read files, install packages and iterate on a task. It operates in a loop: plan, act, observe, repeat - similar to how you would instruct a junior developer.

Agent mode is triggered in VS Code by selecting **Agent** from the chat mode dropdown. Use it for scaffolding a new feature, converting a codebase to TypeScript or setting up a testing framework.

> [!CAUTION]
> Agent mode can run terminal commands. Review what it proposes before confirming. Do not run it in a repository with uncommitted production work without a backup.

---

## Inline Completions - How to Use Them Well

Copilot reads your comments and function names to generate completions. The quality of its suggestions depends heavily on how much context you give it.

**Write the comment first:**

```python
# Parse a date string in ISO 8601 format and return a datetime object.
# Raise ValueError if the string is not valid ISO 8601.
def parse_iso_date(date_string):
```

Copilot will generate a much better function body with a descriptive comment than with just the function signature.

**Name your functions and variables clearly:**

`getUserByEmailAndVerifyPassword` produces better completion than `getUser`.

**Use a realistic file name:**

A file named `auth.py` with existing imports produces better auth-related suggestions than a file named `test.py`.

**Accept partial completions:**

Press `Ctrl+Right` (🪟🐧) / `Cmd+Right` (🍎) to accept one word at a time from a suggestion rather than the whole thing.

---

## Slash Commands

In Copilot Chat, slash commands are shortcuts for common tasks:

| Command | What it does |
|---|---|
| `/explain` | Explain the selected code |
| `/fix` | Suggest a fix for the selected code or error |
| `/tests` | Generate unit tests for the selected code |
| `/doc` | Generate documentation comments |
| `/simplify` | Simplify the selected code |
| `/new` | Scaffold a new file or project |
| `/newNotebook` | Create a new Jupyter notebook |

Example: select a function, open Chat, type `/tests` and press Enter.

---

## Copilot on GitHub.com

Copilot is available directly on GitHub.com without opening an editor.

**On any repository page:** Click the Copilot icon (the circle with lines) in the top-right to open Copilot Chat for that repository. Ask questions about the codebase: "how does authentication work in this project?" or "where is the database connection configured?"

**On pull requests:** Copilot can summarise what a PR does, explain individual file changes and suggest improvements. Open a PR and look for the Copilot icon in the PR toolbar.

**On issues:** Copilot can suggest code to fix an issue. Open an issue and click the Copilot icon.

---

## Copilot for Pull Requests

With Copilot Pro and above, Copilot can:

- **Auto-generate a PR description** based on the commits and diff. In a PR, click the Copilot icon next to the description field.
- **Summarise review comments** across a large PR
- **Suggest code changes** in review comments

This is most useful when inheriting code or reviewing a large PR from a contributor.

---

## Privacy and Your Code

**Default behaviour (Copilot Free and Pro):**

- Your code snippets are sent to GitHub/Microsoft servers for processing
- GitHub may use your prompts and suggestions to improve Copilot models (this can be turned off)

**Turning off telemetry:**

1. Go to `github.com/settings/copilot`
2. Under **Suggestions matching public code**, choose your preference
3. Under **Allow GitHub to use my code snippets**, untick if you do not want your code used for training

**Copilot Business and Enterprise:**

- Code is not used to train models by default
- Data is processed but not retained beyond the immediate request
- Enterprise can configure which repositories Copilot is enabled for

> [!IMPORTANT]
> Do not paste API keys, passwords, tokens or personal data into Copilot Chat. Even with privacy settings on, treat anything sent to an AI as potentially leaving your machine.

---

## Real Limitations

**Copilot makes mistakes.** It produces plausible-looking code that can be subtly wrong. Always test generated code.

**It does not understand your whole codebase.** Copilot has a context window. It sees open files and what you reference with `#file:`. It does not have a full picture of a large project.

**It can suggest insecure code.** It has been shown to produce SQL injection vulnerabilities, use deprecated cryptographic functions and suggest patterns that are known to be insecure. Do not skip code review because Copilot wrote the code.

**It can hallucinate APIs and library methods.** Copilot sometimes invents function names that do not exist. Always verify that a suggested library method actually exists before using it.

**It gets worse with less common languages and frameworks.** Copilot works best with Python, JavaScript, TypeScript, Java, C# and Go. For less common languages the suggestions are less reliable.

**It cannot browse the internet.** Copilot does not have access to current documentation, GitHub issues or Stack Overflow. Its knowledge has a training cutoff.

---

## Try It Yourself

**Exercise 1 - first completion:**

1. Open VS Code with Copilot installed and signed in
2. Create a new file called `practice.py`
3. Type the following comment on the first line:

   ```python
   # Function that takes a list of numbers and returns the median value
   ```

4. Press Enter and wait for Copilot to suggest the function
5. Press `Tab` to accept

**Exercise 2 - Copilot Chat:**

1. Select the function Copilot generated in Exercise 1
2. Open Copilot Chat (`Ctrl+Alt+I` / `Cmd+Option+I`)
3. Type `/tests` and press Enter
4. Copilot will generate unit tests for the function

**Exercise 3 - terminal:**

1. Open a terminal
2. Run:

   ```bash
   gh copilot explain "git log --oneline --graph --all --decorate"
   ```

3. Read the plain-English explanation

---

## Common Mistakes

**Accepting suggestions without reading them.** Copilot is fast. It is easy to tab-accept 10 suggestions in a row and end up with code you have not reviewed. Read every suggestion.

**Asking vague questions in Chat.** "Fix my code" produces worse results than "the `calculateTax` function returns undefined when the input is 0 - what is causing this?"

**Expecting Copilot to know your project structure.** Copilot does not automatically know what files are in your project. Use `#file:` to give it context.

**Ignoring the free tier limits.** On the free plan, 2000 completions run out faster than expected. If completions suddenly stop mid-month you have hit the limit.

**Using Copilot for secrets management.** Never ask Copilot to generate or manage API keys, passwords or tokens.

---

## Summary

- Copilot has a **free tier** (2000 completions, 50 chat messages/month) available to all GitHub accounts
- **Students get Copilot Pro free** via the GitHub Student Developer Pack at `education.github.com`
- Install in VS Code via the **GitHub Copilot** extension; sign in with GitHub
- **Inline completions** appear as greyed text - press `Tab` to accept
- **Copilot Chat** answers questions, explains code, writes tests and fixes bugs
- **Copilot Edits** makes changes across multiple files; **agent mode** can run commands autonomously
- The **GitHub CLI** extension (`gh copilot suggest` and `gh copilot explain`) brings Copilot to the terminal
- Copilot makes mistakes - always review generated code, especially for security issues
- Turn off code snippet sharing in **Settings → Copilot** if you are working with sensitive code

---

## Sources and Further Reading

- [GitHub Copilot documentation](https://docs.github.com/en/copilot) - official docs
- [GitHub Copilot plans](https://github.com/features/copilot) - current plan comparison and pricing
- [GitHub Copilot in VS Code](https://code.visualstudio.com/docs/copilot/overview) - VS Code-specific guide
- [gh copilot extension](https://github.com/github/gh-copilot) - terminal Copilot
- [Copilot Trust Center](https://resources.github.com/copilot-trust-center/) - privacy and security details
- [GitHub Education - Copilot for students](https://education.github.com) - free Pro access for students

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

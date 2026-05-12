# What Is a Terminal?

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

Before you can use Git confidently from the command line, you need a clear mental model of what a terminal actually is - and how it differs from a shell, a command prompt, a CLI and a console. These words get used interchangeably online, which causes real confusion. This file untangles them properly.

---

## Table of Contents

1. [The five terms, defined clearly](#1-the-five-terms-defined-clearly)
2. [Where these terms came from](#2-where-these-terms-came-from)
3. [The three layers every terminal uses](#3-the-three-layers-every-terminal-uses)
4. [What you actually see when you open a terminal](#4-what-you-actually-see-when-you-open-a-terminal)
5. [Prompt symbols explained](#5-prompt-symbols-explained)
6. [Terminal emulators on each platform](#6-terminal-emulators-on-each-platform)
7. [What a CLI is, and how it relates to Git](#7-what-a-cli-is-and-how-it-relates-to-git)
8. [Try It Yourself](#8-try-it-yourself)
9. [Common Mistakes](#9-common-mistakes)
10. [Summary](#10-summary)
11. [Sources](#11-sources)

---

## 1. The five terms, defined clearly

Five words describe overlapping concepts and almost everyone conflates them at some point.

**Terminal emulator** - a graphical program that draws a text window and accepts keyboard input. It is software that pretends to be a hardware terminal. Examples: Terminal.app, iTerm2, Windows Terminal, GNOME Terminal, Alacritty, WezTerm, Kitty. The terminal emulator itself does not run commands - it just displays output and passes your keystrokes to the shell running inside it.

**Shell** - a command interpreter. It reads what you type, parses it and runs programs on your behalf. Examples: bash, zsh, fish, PowerShell, cmd.exe. When you type `git status` and press Enter, the shell finds the `git` program on your system and runs it. The shell is the thing that gives you the `$` or `>` prompt.

**CLI (Command Line Interface)** - an abstract concept, not a specific program. It describes any tool or workflow where you control a computer by typing text commands rather than clicking buttons. Git's CLI is the set of commands Git accepts (`git commit`, `git push`, etc.). The opposite of a CLI is a GUI (Graphical User Interface).

**Command Prompt** - in everyday usage, this refers to two different things. It is the text the shell shows to tell you it is ready for input (the `$` or `>` character). It is also the name of the specific Windows program `cmd.exe`, which is why Windows users say "the Command Prompt" to mean that specific application. Context usually makes clear which meaning is intended.

**Console** - historically the physical keyboard and screen wired directly to a server. On Linux today, a "virtual console" is a full-screen text mode you reach with `Ctrl+Alt+F1` through `Ctrl+Alt+F6`, outside the graphical desktop. On Windows, `conhost.exe` is the legacy Console Host that used to power cmd.exe and PowerShell windows before Windows Terminal existed.

In practice, most developers use "terminal" loosely to mean the whole setup - the window, the shell inside it, and the prompt. That is fine in conversation. When something goes wrong or you need to configure something, it helps to know which layer the problem is in.

---

## 2. Where these terms came from

Understanding the history explains why the vocabulary is the way it is.

In the 1960s and 1970s, computers were enormous machines shared by many users. Each user sat at a **teletype (TTY)** - a mechanical device that looked like a typewriter. You typed a command, the teletype sent it to the mainframe over a serial cable, the mainframe ran it and the result came back and printed on paper.

As computers became more capable, physical CRTs replaced paper. The **DEC VT100** (1978) was the defining terminal of its era - a keyboard and a screen with no processing power of its own, just a display. The VT100 defined the ANSI escape code standard that terminals still use today. When you see coloured output in your terminal - green text, bold text, underlined text - that colouring is happening via ANSI escape codes that were standardised in 1978.

When Unix moved to graphical workstations in the 1980s, software had to **emulate** those hardware terminals in a window. `xterm` (1984) was one of the first. Every terminal application you use today is called a terminal **emulator** because it emulates those old hardware devices.

The word **TTY** stuck around. On Linux, every terminal connection maps to a device file in `/dev/` - your current terminal session is `/dev/tty`, opened terminal tabs are `/dev/pts/0`, `/dev/pts/1` etc. The shell is started as a child process of the terminal emulator, connected to the TTY device.

---

## 3. The three layers every terminal uses

Any time you open a terminal window and type a command, three layers are involved.

**Layer 1 - Terminal emulator**: draws the window, renders fonts, handles colours and mouse events. Receives characters from layer 2 and displays them. Sends your keystrokes to layer 2.

**Layer 2 - PTY (pseudo-terminal)**: a kernel abstraction that connects the terminal emulator to the shell. It is a pair of file descriptors - a master side (held by the terminal emulator) and a slave side (used by the shell). The kernel's TTY layer handles line discipline - buffering, `Ctrl+C` for interrupt, `Ctrl+Z` for suspend, backspace processing and echo. You can check what TTY your current session uses with `tty`.

**Layer 3 - Shell**: the process running inside the PTY. It prints the prompt, reads input, parses commands and forks child processes to run programs. When you type `git status`, the shell forks a child process, execs the `git` binary, and waits for it to finish.

This three-layer model matters because **each layer has its own configuration**. Terminal emulator settings (font, colours, key bindings) live in the terminal emulator's config file. Shell settings (aliases, PATH, prompt) live in the shell's config file. Git settings live in `.gitconfig`. Problems at one layer look different from problems at another - a broken PATH is a shell problem, a garbled display is a terminal problem, a missing alias is a shell config problem.

---

## 4. What you actually see when you open a terminal

When you open a terminal and see the prompt, here is exactly what happened:

1. You launched the terminal emulator (Terminal.app, Windows Terminal, GNOME Terminal etc.)
2. The terminal emulator allocated a PTY and started your default shell as a child process
3. The shell read its config files (`.bashrc`, `.zshrc`, `config.fish` etc.)
4. The shell printed its prompt string and waited for input

The prompt itself is just text that the shell printed to the screen. It is not special - it is a regular print statement in the shell's code, controlled by a variable you can change. The format of the prompt is covered in detail in [02-shells-and-platforms.md](02-shells-and-platforms.md).

When you type a command and press Enter:

1. The terminal emulator sends each keystroke to the PTY
2. The PTY's line discipline buffers the input and echoes characters back so you can see what you are typing
3. When Enter is pressed, the PTY delivers the complete line to the shell
4. The shell parses the line, looks up the command and runs it
5. The command's output goes back through the PTY to the terminal, which displays it
6. The shell prints the prompt again and waits

---

## 5. Prompt symbols explained

The character at the end of your prompt before the cursor is one of the most confusing things for new terminal users. Different platforms and shells show different symbols.

| Symbol                 | Shell / context          | Meaning                                                                                                                                            |
| ---------------------- | ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `$`                    | bash, zsh (regular user) | Ready for input as a normal user. The `$` comes from bash's `\$` prompt escape, which expands to `$` when UID is not 0                             |
| `#`                    | bash, zsh (root user)    | Ready for input as root. Same `\$` escape, but when running as root (UID 0) it shows `#` - a visual warning that you have full system access       |
| `%`                    | zsh (some configs)       | zsh's default prompt uses `%#` which shows `%` for normal users and `#` for root. Many zsh themes use `%` as the normal-user symbol instead of `$` |
| `>>>`                  | Python REPL              | You are inside the Python interpreter, not a shell. Press `Ctrl+D` or type `exit()` to return to your shell                                        |
| `PS>` or `PS C:\>`     | PowerShell               | Ready for input in PowerShell. The `PS` prefix comes from PowerShell's default `prompt` function                                                   |
| `>`                    | cmd.exe                  | Ready for input in the Windows Command Prompt. `%PROMPT%` defaults to `$P$G` - current path plus `>`                                               |
| `~$` or `user@host:~$` | bash (common format)     | Same `$` symbol with path or user/hostname context prepended                                                                                       |

The symbols differ purely because each shell's author chose different defaults. There is no deep technical reason `$` means "ready" - it is convention.

**What the tilde means**: `~` is a shorthand for your home directory in all Unix-like shells (bash, zsh, fish). On Linux and macOS, `~` expands to `/home/username` or `/Users/username`. In PowerShell, `~` also expands to `$HOME`. In cmd.exe, `~` does nothing special - it is a literal tilde character.

**What the path before the `$` means**: many shell prompts show your current working directory before the prompt symbol. This tells you where in the filesystem you are. `~` means your home directory. `~/projects/myapp` means `/home/username/projects/myapp`. When you first open a terminal you are usually in your home directory.

---

## 6. Terminal emulators on each platform

Each platform has its own set of terminal emulators. The key thing to remember: the terminal emulator is just a window. You can use any shell inside any terminal emulator, and switching terminal emulators does not change how Git or your shell works.

🪟 **Windows**

**Windows Terminal** is the modern, recommended terminal emulator for Windows. It was open-sourced in May 2019 and has been the **default terminal on Windows 11** since version 22H2. It supports multiple tabs, split panes, GPU-accelerated text rendering, and deep customisation. You can run cmd.exe, PowerShell, WSL (Linux shells), Azure Cloud Shell and SSH sessions all in separate tabs in the same window.

Windows Terminal is a terminal emulator - it hosts other shells. The shell you use inside it (PowerShell, cmd.exe, bash via WSL) is a separate choice from the terminal itself.

Install Windows Terminal from the Microsoft Store or via:

```powershell
winget install Microsoft.WindowsTerminal
```

Older alternatives still in use: **ConHost** (the legacy Windows console host, still used when you launch cmd.exe directly from Run), **cmder** (a popular enhancement built on ConEmu).

**cmd.exe** (Command Prompt) - the legacy Windows shell that has shipped with every version of Windows since NT (1993). It is both a shell and (in practice) its own terminal. You will encounter it in legacy scripts and enterprise environments. It is covered in detail in [02-shells-and-platforms.md](02-shells-and-platforms.md).

🍎 **macOS**

**Terminal.app** - the built-in terminal that comes with macOS. Reliable, fast and functional. Opens zsh by default since macOS Catalina (2019).

**iTerm2** - the most popular third-party terminal for macOS, free, feature-rich. Adds split panes within a tab, rich profile system, shell integration (marks prompt positions, tracks commands, enables click-to-download), a global hotkey drop-down window, tmux integration and much more. Current stable version is 3.5.x/3.6.x. Notable caveat: versions 3.5.6 through 3.5.10 had a bug that logged SSH I/O to `/tmp/framer.txt` on remote hosts - always use 3.5.11 or later.

🐧 **Linux**

Every Linux desktop environment ships a terminal emulator: **GNOME Terminal** (GNOME), **Konsole** (KDE), **xfce4-terminal** (XFCE), **LXTerminal** (LXDE). Any of these work well for daily Git use.

Popular alternatives used by developers: **Alacritty** (GPU-accelerated, minimal, no tabs - pair with tmux), **Kitty** (GPU-accelerated, richer features, supports image display), **WezTerm** (cross-platform, powerful scripting via Lua), **Ghostty** (very new, high performance, cross-platform).

**Cross-platform note**: Alacritty, Kitty, WezTerm and Ghostty run on Linux, macOS and Windows. If you want the same terminal on all three platforms, these are your options.

---

## 7. What a CLI is, and how it relates to Git

Git is a CLI tool. It has no graphical interface of its own - everything Git does is accessed by typing commands. `git init`, `git add`, `git commit`, `git push` - these are all CLI commands.

When you type `git status` in your terminal:

1. Your shell reads `git status`
2. The shell splits it into a command (`git`) and an argument (`status`)
3. The shell searches your `$PATH` (the list of directories where programs live) for a program called `git`
4. It finds `/usr/bin/git` (Linux), `/opt/homebrew/bin/git` (macOS with Homebrew), or `C:\Program Files\Git\cmd\git.exe` (Windows)
5. The shell runs that program, passing `status` as an argument
6. Git prints its output to the terminal
7. The shell prints the prompt again

**Git is just a program.** It is not built into your shell. The shell does not know anything about Git specially - it just runs it like any other program. This means every IDE and editor that has "Git integration" is ultimately calling the same `git` program you call from the terminal. The Git configuration you set up in your terminal applies to your IDE too. This is covered in detail in [03-terminal-and-git.md](03-terminal-and-git.md).

---

## 8. Try It Yourself

**Exercise 1 - identify your terminal layers**

Open your terminal. Identify: (a) which terminal emulator you are using (the application name), (b) which shell is running inside it (run `echo $SHELL` on Mac/Linux or `$PSVersionTable.Shell` in PowerShell), (c) what your prompt symbol is and why.

**Exercise 2 - find your home directory**

In your terminal, type `echo ~` and press Enter. This shows the full path to your home directory. Then type `pwd` (print working directory) to see where you currently are. Do they match?

**Exercise 3 - read your prompt**

Look carefully at your prompt. Write down what each part means. If your prompt shows `user@hostname:~/projects$`, identify: the username, the hostname, the current directory and the prompt symbol.

**Exercise 4 - find your Git binary**

Run these commands and note where Git is installed on your system:

```bash
# Mac/Linux
which git
git --version

# Windows (PowerShell)
Get-Command git
git --version

# Windows (cmd.exe)
where git
git --version
```

**Exercise 5 - explore your TTY**

On Mac or Linux, run `tty` to see the device file for your current terminal session. Open a second terminal tab and run `tty` again - you should see a different device number. This is the kernel-level PTY for each terminal session.

---

## 9. Common Mistakes

Treating "terminal" and "shell" as the same thing leads to confusion when troubleshooting. A PATH problem that makes `git` invisible is a shell problem - your shell config is not setting `$PATH` correctly. A display problem where colours appear as garbled characters is a terminal problem - the terminal emulator is not interpreting ANSI escape codes correctly. Knowing which layer the problem is in points you to the right config file to fix.

Assuming `$` means something is optional. In documentation, `$` at the start of a line often marks a command you should type (a convention borrowed from the shell prompt). When a tutorial shows `$ git status`, the `$` is not part of the command - do not type it. This trips up almost every beginner at least once.

Confusing the Python `>>>` prompt with a shell prompt. If you run `python3` and see `>>>`, you are inside the Python REPL (interactive interpreter), not your shell. Git commands will not work here. Type `exit()` or press `Ctrl+D` to return to your shell.

Opening cmd.exe on Windows when you meant to open PowerShell or WSL. The three Windows shells look similar at a glance but have very different capabilities and syntax. Check the title bar of the window or the prompt format to confirm which shell you have. `Get-Command` works in PowerShell but not in cmd.exe; `echo %PATH%` works in cmd.exe but not in PowerShell the same way.

---

## 10. Summary

A terminal emulator is a graphical application that displays a text window. A shell is a command interpreter that runs inside the terminal and executes programs when you type commands. A CLI is the abstract concept of controlling software by typing text commands. "Command Prompt" means either the text the shell shows before your cursor, or specifically Windows' cmd.exe program. These are different layers that each have their own configuration.

Prompt symbols are just conventions: `$` for bash/zsh normal user, `#` for root, `%` for some zsh configs, `PS>` for PowerShell, `>` for cmd.exe, `>>>` for the Python REPL. When documentation shows `$ git status`, the `$` is not part of the command.

Terminal emulators: on Windows, use Windows Terminal; on macOS, Terminal.app or iTerm2; on Linux, any desktop terminal plus alternatives like Alacritty, Kitty or WezTerm. The choice of terminal emulator does not affect how your shell or Git behaves.

Git is a program that your shell finds and runs. Every IDE and editor that has Git integration is calling the same `git` binary you use in the terminal.

---

## 11. Sources

- [The TTY demystified - Linus Akesson](https://www.linusakesson.net/programming/tty/)
- [DEC VT100 User Guide - ANSI escape codes](https://vt100.net/docs/vt100-ug/)
- [Windows Terminal documentation](https://learn.microsoft.com/en-us/windows/terminal/)
- [iTerm2 documentation](https://iterm2.com/documentation.html)
- [bash reference manual - Readline variables](https://www.gnu.org/software/bash/manual/bash.html)
- [zsh documentation](https://zsh.sourceforge.io/Doc/)

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

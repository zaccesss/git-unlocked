# git-unlocked

> The most complete open source Git and version control course available. Free forever. CC BY-SA 4.0 licensed.

[![markdownlint](https://github.com/zaccesss/git-unlocked/actions/workflows/markdownlint.yml/badge.svg)](https://github.com/zaccesss/git-unlocked/actions/workflows/markdownlint.yml)
[![Check Links](https://github.com/zaccesss/git-unlocked/actions/workflows/linkcheck.yml/badge.svg)](https://github.com/zaccesss/git-unlocked/actions/workflows/linkcheck.yml)
[![Release](https://img.shields.io/github/v/release/zaccesss/git-unlocked?label=release)](https://github.com/zaccesss/git-unlocked/releases/latest)
[![Licence: CC BY-SA 4.0](https://img.shields.io/badge/licence-CC%20BY--SA%204.0-green.svg)](LICENSE)
[![Version](https://img.shields.io/badge/version-1.4.0-blue.svg)](CHANGELOG.md)
[![DOI](https://img.shields.io/badge/DOI-10.5281%2Fzenodo.20695630-blue.svg)](https://doi.org/10.5281/zenodo.20695630)

git-unlocked takes you from absolute zero to professional-level Git across every major platform: GitHub, GitLab, Bitbucket, Azure DevOps, Gitea, Forgejo and Codeberg. Commands are shown for Windows, Mac and Linux side by side. Nothing assumed. Nothing skipped.

**182 files. 12 sections. All free.**

---

## Start here

New to Git? Start at the beginning:

1. [00-welcome/](00-welcome/README.md) - what this course is
2. [What is version control?](01-introduction/04-version-control-concepts.md)
3. [How to use this course](01-introduction/02-how-to-use-this-course.md)
4. [Setting up your environment](01-introduction/03-setting-up.md)

Want to make your first open source contribution right now? Go to [11-first-contribution/](11-first-contribution/README.md). It takes less than ten minutes.

---

## How the course fits together

```mermaid
flowchart LR
    subgraph START["Start here"]
        W["00 welcome<br/>what the course is"] --> I["01 introduction<br/>concepts and setup"]
    end
    subgraph CORE["Git itself"]
        G["02 git<br/>from git init to internals"]
    end
    subgraph PLATFORMS["Pick a platform"]
        GH["03 github"]
        GL["04 gitlab"]
        OP["05 other platforms<br/>Bitbucket, Azure DevOps,<br/>Gitea, Forgejo, Codeberg"]
    end
    subgraph TOOLS["Daily tools"]
        ED["06 ides and editors"]
        TM["07 terminal"]
    end
    subgraph PRACTICE["Put it to work"]
        RW["08 real world<br/>open source, GitOps, monorepos"]
        FC["11 first contribution<br/>a real pull request in ten minutes"]
    end
    subgraph LOOKUP["Keep open"]
        RF["09 reference<br/>cheatsheet, glossary, common mistakes"]
        RS["10 resources<br/>books, videos, tools, communities"]
    end
    I --> G
    G --> GH
    G --> GL
    G --> OP
    GH --> ED
    GL --> ED
    OP --> ED
    ED --> TM --> RW --> FC
    G -. "look things up" .-> RF
    RW -. "go further" .-> RS
```

Read the sections in order for the full course. If Git itself is already familiar, jump straight to a platform. The reference and resources sections are for looking things up at any point.

---

## Course contents

| #   | Section                                      | Files | What is inside                                                 |
| --- | -------------------------------------------- | ----- | -------------------------------------------------------------- |
| 00  | [welcome](00-welcome/)                       | 1     | Introduction to the course                                     |
| 01  | [introduction](01-introduction/)             | 3     | Concepts, setup, how to navigate                               |
| 02  | [git](02-git/)                               | 29    | Everything about Git from `git init` to internals              |
| 03  | [github](03-github/)                         | 28    | Full GitHub platform coverage                                  |
| 04  | [gitlab](04-gitlab/)                         | 16    | Full GitLab platform coverage                                  |
| 05  | [other-platforms](05-other-platforms/)       | 62    | Bitbucket, Azure DevOps, Gitea, Forgejo, Codeberg              |
| 06  | [ides-and-editors](06-ides-and-editors/)     | 14    | VS Code, JetBrains, Neovim, Cursor, Zed and more               |
| 07  | [terminal](07-terminal/)                     | 14    | Shell setup, lazygit, delta, fzf, bat, tig and more            |
| 08  | [real-world](08-real-world/)                 | 8     | Open source contribution, GitOps, monorepos, disaster recovery |
| 09  | [reference](09-reference/)                   | 4     | Cheatsheet, glossary, common mistakes, security                |
| 10  | [resources](10-resources/)                   | 1     | 120+ curated books, videos, tools and communities              |
| 11  | [first-contribution](11-first-contribution/) | 2     | Make your first open source PR here                            |

Each section has an overview file (`00-*.md`) with a full table of contents and reading order for that section.

---

## Difficulty levels

- 🟢 Beginner - no prior experience needed
- 🟡 Intermediate - comfortable with basic Git
- 🔴 Advanced - production depth, internals, edge cases

OS commands are labelled 🪟 Windows, 🍎 Mac and 🐧 Linux. Identical commands appear once without labels.

---

## Make your first contribution

The [11-first-contribution/](11-first-contribution/README.md) folder is a safe sandbox. Add your name to the contributors list and open a pull request. Less than ten minutes. Real GitHub workflow.

---

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md) first, then open a pull request. Everyone who contributes is listed in [HALL_OF_FAME.md](HALL_OF_FAME.md). Please read the [Code of Conduct](CODE_OF_CONDUCT.md) before participating.

---

## Licence

[CC BY-SA 4.0 Licence](LICENSE). Use, share, adapt and build on it - just give credit and keep it under the same licence.

---

## Contact and Support

Open an [issue](https://github.com/zaccesss/git-unlocked/issues) in this repository for questions or bugs.

You can also reach me directly at [contact@isaacadjei.me](mailto:contact@isaacadjei.me) or via my [website contact page](https://isaacadjei.me/contact).

> [!IMPORTANT]
> Found a security issue, such as exposed credentials or harmful instructions in the course content? Do not open a public issue, see [SECURITY.md](SECURITY.md) for how to report it privately.

---

If this course helps, a star on the repository helps others find it. Made by [Isaac Adjei](https://isaacadjei.me).

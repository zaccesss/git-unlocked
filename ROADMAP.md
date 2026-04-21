# Roadmap

This roadmap tracks the development plan for git-unlocked. Completed sections are marked. Planned sections show their intended file structure.

**Current version: v1.1.0**

---

## Status overview

| Section | Files | Status |
|---|---|---|
| introduction/ | 3 | Complete |
| git/ | 29 | Complete |
| github/ | 28 | Complete |
| gitlab/ | 16 | Complete |
| other-platforms/bitbucket/ | 14 | Complete |
| other-platforms/azure-devops/ | 15 | Complete |
| other-platforms/gitea/ | 13 | Complete |
| other-platforms/forgejo/ | 10 | Complete |
| other-platforms/codeberg/ | 10 | Complete |
| ides-and-editors/ | 14 | Complete |
| terminal/ | 14 | Complete |
| real-world/ | 8 | Next |
| reference/ | 3 | Planned |
| resources/ | 1 | Planned |
| first-contribution/ | 1 | Planned |
| assets/ | TBD | Planned |

---

## Completed

### v1.0.0 - Core sections

- introduction/ (3 files)
- git/ (29 files)
- github/ (28 files)
- gitlab/ (16 files)
- other-platforms/ - all five subfolders (62 files total)
- ides-and-editors/ (14 files)
- Infrastructure: markdownlint CI, link checker CI, issue templates, PR template, all root meta files

### v1.1.0 - Terminal section

- terminal/ (14 files) - full terminal toolchain from shell setup through lazygit, delta, fzf, bat, tig, gitui, starship and zoxide

---

## Next: real-world/ (v1.2.0)

Practical scenario files showing Git in real professional contexts. Each file follows full house style: 4,000-6,000 words, Windows/Mac/Linux coverage, Try It Yourself, Common Mistakes, Summary, Sources and footer.

```
real-world/
├── 00-real-world-overview.md
├── 01-contributing-to-open-source.md
├── 02-working-in-a-team.md
├── 03-gitops-workflows.md
├── 04-monorepo-patterns.md
├── 05-migrating-between-platforms.md
├── 06-recovering-from-disasters.md
└── 07-all-real-world-reference.md
```

### File summaries

- `00-real-world-overview.md` - section introduction, how to use these files and reading order
- `01-contributing-to-open-source.md` - forking, cloning, branching, opening a PR, syncing upstream, handling review feedback
- `02-working-in-a-team.md` - branching strategies (GitFlow, trunk-based, GitHub Flow), code review etiquette, protected branches, CI/CD integration
- `03-gitops-workflows.md` - Git as single source of truth for infrastructure, GitOps principles, ArgoCD and Flux patterns, environment promotion
- `04-monorepo-patterns.md` - managing large multi-package repositories, sparse checkout, partial clone, tooling (Nx, Turborepo, Bazel)
- `05-migrating-between-platforms.md` - moving repositories with full history between GitHub, GitLab, Gitea and others; mirror remotes, LFS migration
- `06-recovering-from-disasters.md` - step-by-step recovery for the most common Git disasters: lost commits, bad merges, corrupted repos, force-push accidents
- `07-all-real-world-reference.md` - consolidated quick-reference for every command and pattern introduced in this section

---

## Planned: reference/ (v1.3.0)

```
reference/
├── cheatsheet.md
├── glossary.md
└── common-mistakes.md
```

- `cheatsheet.md` - every essential Git command on one page, organised by task
- `glossary.md` - every term defined: HEAD, index, blob, tree, pack, reflog and more
- `common-mistakes.md` - the most frequent Git errors with exact fix commands

---

## Planned: resources/ (v1.3.0)

```
resources/
└── index.md
```

- `index.md` - curated links to official documentation, books, interactive tools, video courses and community resources

---

## Planned: first-contribution/ (v1.3.0)

A safe sandbox for beginners to make their first open source pull request by adding their name to a contributors list.

---

## Long-term (after all folders complete)

- Trim README (too long; navigation mixed with course guidance)
- Full rewrite of `introduction/02-how-to-use-this-course.md`
- New file `introduction/04-version-control-concepts.md`
- GitHub Pages setup at zaccessss.github.io/git-unlocked
- Interactive HTML quiz pages with instant answer checking
- Animated SVG diagrams for Git concepts
- Cross-reference audit (all internal links verified)
- Accessibility review

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) if you want to help build any of the planned sections.

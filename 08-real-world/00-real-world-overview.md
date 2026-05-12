# Real-world Git: overview

**Difficulty:** 🟢 All levels | **Time:** 5 minutes

---

## Table of contents

- [What this section covers](#what-this-section-covers)
- [Who this section is for](#who-this-section-is-for)
- [How to use this section](#how-to-use-this-section)
- [Files in this section](#files-in-this-section)
- [Reading order](#reading-order)
- [What you need before starting](#what-you-need-before-starting)
- [Key themes](#key-themes)
- [Summary](#summary)

---

## What this section covers

The rest of this course teaches you Git commands and platform features in isolation. This section teaches you how those commands and features combine in the real world, on real teams, solving real problems.

Every file in this section is built around a scenario: contributing to a project you do not own, shipping code with a team of twenty, automating infrastructure deployments, working inside a repository with a million files, moving a codebase between platforms, or digging a team out of a serious Git disaster. These are the situations where knowing the right command is not enough - you also need to know the right sequence, the right tool for the job and the failure modes to avoid.

The content in this section draws on public engineering posts from Google, Meta, Microsoft, Shopify, Netflix and others, on the OpenGitOps specification, on official documentation from the Git project and every major platform and on the collective knowledge of teams that have made (and recovered from) every mistake covered here.

---

## Who this section is for

This section is organised by topic, not by skill level. Each file contains beginner-friendly foundations followed by intermediate and advanced material. Difficulty markers guide you:

- 🟢 Foundation - no prior experience with this topic needed
- 🟡 Intermediate - comfortable with basic Git and at least one platform
- 🔴 Advanced - production-level depth, complex scenarios, edge cases

If you are new to Git, complete the `git/` section first. If you are new to GitHub or GitLab, complete at least the first five files of those sections before diving into `03-gitops-workflows.md` or `04-monorepo-patterns.md`.

---

## How to use this section

Each file follows the same structure:

1. Introduction - what the topic is and why it matters
2. Core concepts - the theory, briefly
3. The workflow - step-by-step, with commands for 🪟 Windows, 🍎 Mac and 🐧 Linux
4. Real-world examples - how well-known teams handle this in production
5. Try it yourself - a practical exercise you can complete locally
6. Common mistakes - the errors most people hit and exactly how to fix them
7. Summary - key takeaways in plain language
8. Sources - official documentation and further reading

You do not need to read all files in order. Each file is self-contained. The reading order below is a recommendation for learners working through the section for the first time.

---

## Files in this section

| #   | File                                                             | Topics                                                                     | Level  |
| --- | ---------------------------------------------------------------- | -------------------------------------------------------------------------- | ------ |
| 00  | [Real-world overview](00-real-world-overview.md)                 | Section map, reading order, prerequisites                                  | 🟢     |
| 01  | [Contributing to open source](01-contributing-to-open-source.md) | Fork workflow, PRs, upstream sync, review feedback                         | 🟢🟡   |
| 02  | [Working in a team](02-working-in-a-team.md)                     | Branching strategies, code review, protected branches, merge queues        | 🟡🔴   |
| 03  | [GitOps workflows](03-gitops-workflows.md)                       | Git as infrastructure source of truth, ArgoCD, Flux, environment promotion | 🟡🔴   |
| 04  | [Monorepo patterns](04-monorepo-patterns.md)                     | Large repos, sparse checkout, partial clone, Nx, Turborepo, Bazel          | 🟡🔴   |
| 05  | [Migrating between platforms](05-migrating-between-platforms.md) | GitHub to GitLab, GitLab to Gitea, LFS migration, gotchas                  | 🟡🔴   |
| 06  | [Recovering from disasters](06-recovering-from-disasters.md)     | Reflog, lost commits, bad merges, force push accidents, committed secrets  | 🟢🟡🔴 |
| 07  | [Real-world reference](07-all-real-world-reference.md)           | Consolidated commands and patterns from all files in this section          | 🟢🟡🔴 |

---

## Reading order

**If you are new to open source contribution**, start at `01-contributing-to-open-source.md`. It covers the foundational fork-and-PR workflow that everything else builds on.

**If you are joining or building a team**, go to `02-working-in-a-team.md` next. Branching strategies, code review and protected branches are the foundation of team Git.

**If you work with Kubernetes or cloud infrastructure**, `03-gitops-workflows.md` introduces ArgoCD and Flux - the two dominant tools for GitOps.

**If your repository is large or growing fast**, `04-monorepo-patterns.md` covers the Git-level and tooling-level solutions used by teams at Microsoft, Meta and Shopify.

**If you are moving a codebase between platforms**, read `05-migrating-between-platforms.md` before you do anything. There are data-loss traps that no error message will warn you about.

**If something has gone wrong**, go directly to `06-recovering-from-disasters.md`. Every scenario has a step-by-step recovery sequence starting with the least destructive approach.

**`07-all-real-world-reference.md`** is a cheatsheet. Return to it when you need to look up a specific command or pattern without re-reading a full file.

---

## What you need before starting

- Git installed and configured with a name and email (`git config --global user.name` and `git config --global user.email`)
- A GitHub or GitLab account for the contribution and team workflow files
- Basic familiarity with `git clone`, `git add`, `git commit` and `git push`

Each file lists any additional requirements at the top.

---

## Key themes

Three ideas run through every file in this section.

**Small, focused changes win.** Whether you are contributing to open source, working in a team, or managing a monorepo, the pattern that produces the least friction is the same: small commits, short-lived branches, fast review cycles. The research behind DORA (DevOps Research and Assessment) consistently finds that elite teams deploy frequently with small batches - not because they work faster, but because small changes are easier to review, easier to revert and easier to reason about.

**The reflog is your safety net.** Every file in this section that involves rewriting history, switching platforms or recovering from a mistake will remind you of the same thing: `git reflog` records every movement of HEAD and every branch tip for 90 days. Before you panic, run `git reflog`. Before you do anything destructive, make sure you understand what you will lose.

**Rotate secrets first, clean history second.** If you accidentally commit a secret - an API key, a password, a private key - the secret is compromised the moment it is pushed. Rewriting history is good hygiene but it is not mitigation. Rotate the credential immediately, then clean the history.

---

## Summary

This section covers the seven scenarios that come up most often when developers use Git in a professional context. Each file is self-contained and practical, with step-by-step workflows, real-world examples and a common mistakes section. Start at `01-contributing-to-open-source.md` if you are new to the section, or jump to whichever file matches your current situation.

---

## Sources and Further Reading

- [git-scm.com](https://git-scm.com/) - official Git documentation
- [docs.github.com](https://docs.github.com/) - GitHub documentation
- [docs.gitlab.com](https://docs.gitlab.com/) - GitLab documentation
- [opengitops.dev](https://opengitops.dev/) - OpenGitOps principles
- [trunkbaseddevelopment.com](https://trunkbaseddevelopment.com/) - trunk-based development reference

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

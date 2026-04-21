# Resources

**Difficulty:** 🟢 All levels | **Time:** Reference - browse as needed

A curated guide to the best Git, GitHub, GitLab and version control learning resources across every medium - official docs, books, videos, interactive tools, podcasts, cheatsheets, communities, newsletters and GitHub repositories.

---

## Table of contents

- [How to use this guide](#how-to-use-this-guide)
- [Official documentation](#official-documentation)
- [Books](#books)
- [Video courses and YouTube](#video-courses-and-youtube)
- [Interactive learning](#interactive-learning)
- [Websites and blogs](#websites-and-blogs)
- [Cheatsheets and quick references](#cheatsheets-and-quick-references)
- [Podcasts and audio](#podcasts-and-audio)
- [GitHub repositories worth starring](#github-repositories-worth-starring)
- [GUI clients and tools](#gui-clients-and-tools)
- [Communities and forums](#communities-and-forums)
- [Newsletters](#newsletters)
- [Certifications](#certifications)
- [Recommended paths by level](#recommended-paths-by-level)
- [Resources to avoid](#resources-to-avoid)
- [Sources and Further Reading](#sources-and-further-reading)

---

## How to use this guide

Every resource is marked with one or more of the following:

- 🟢 Beginner - no prior experience needed
- 🟡 Intermediate - comfortable with basic Git
- 🔴 Advanced - production depth, internals
- 💰 Paid - requires payment (price noted where known)
- 🆓 Free - completely free to access

Resources flagged with ⚠️ are outdated or no longer maintained. They may still contain useful information but should not be treated as current.

---

## Official documentation

These are the primary sources of truth for everything in this course. Bookmark them all.

**[Pro Git book (online)](https://git-scm.com/book/en/v2)** 🟢🟡🔴 🆓
Scott Chacon and Ben Straub, Apress. The definitive free Git book covering everything from installation to internals. The online version is continuously updated by contributors. Print edition ISBN 978-1-4842-0077-3. Start at Chapter 1 for basics; jump to Chapters 7 and 10 for advanced topics and internals.

**[Git reference manual](https://git-scm.com/docs)** 🟡🔴 🆓
Authoritative command reference for every Git command. Also accessible locally via `man git` or `git help <command>`. The gittutorial and gitworkflows guides are particularly useful supplements.

**[GitHub Docs](https://docs.github.com)** 🟢🟡🔴 🆓
Comprehensive documentation for all of GitHub: repositories, Actions, Copilot, Pages, Packages, security features and the API. The learning resources hub at [/en/get-started/start-your-journey/git-and-github-learning-resources](https://docs.github.com/en/get-started/start-your-journey/git-and-github-learning-resources) is a good entry point.

**[GitHub Skills](https://skills.github.com)** 🟢🟡 🆓
Free hands-on courses that run inside real GitHub repositories using Actions for automated feedback. Courses include Introduction to GitHub, Introduction to Git, Communicate with Markdown, GitHub Pages, Review Pull Requests, Resolve Merge Conflicts, Continuous Integration with GitHub Actions and several Copilot courses. Actively maintained.

**[GitLab Docs](https://docs.gitlab.com)** 🟢🟡🔴 🆓
Complete documentation for the GitLab DevSecOps platform: repositories, merge requests, CI/CD, runners, security scanning, Duo AI and self-managed administration. The [Git topic hub](https://docs.gitlab.com/topics/git/) is a good starting point for Git-specific content.

**[GitLab University](https://university.gitlab.com)** 🟢🟡🔴 🆓/💰
GitLab's structured learning platform with role-based tracks and the certification registration portal. Most content is free; certification exams are paid.

**[Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)** 🟢🟡🔴 🆓
Regarded as hosting the clearest conceptual Git diagrams on the open web. Covers basics, branching strategies, rebasing, hooks, submodules, LFS, reflog and SVN migration. Actively maintained and a strong complement to this course.

---

## Books

### Free books

**[Pro Git (online)](https://git-scm.com/book/en/v2)** 🟢🟡🔴 🆓
See Official documentation above. The single most important Git resource that exists.

**[Learn Version Control with Git](https://www.git-tower.com/learn/git/ebook)** 🟢 🆓
Tower. Illustrated, beginner-friendly, continuously refreshed. Available in CLI and GUI editions. The best free entry point for non-developers and designers.

### Paid books (recommended)

**[Version Control with Git, 3rd edition](https://www.oreilly.com/library/view/version-control-with/9781492091189/)** 🟢🟡🔴 💰
Prem Kumar Ponuthorai and Jon Loeliger, O'Reilly, October 2022. ISBN 978-1-4920-9119-6. The most current publisher-quality deep dive; covers everything from basics to advanced internals. Available via O'Reilly subscription or print (~$50).

**[Head First Git](https://www.oreilly.com/library/view/head-first-git/9781492092506/)** 🟢 💰
Raju Gandhi, O'Reilly, February 2022. ISBN 978-1-4920-9251-3. 508 pages. Visually rich, brain-friendly, emphasises the *why* over the *what*. The best book for complete beginners and visual learners.

**[Beginning Git and GitHub, 2nd edition](https://link.springer.com/book/10.1007/979-8-8688-0215-7)** 🟢🟡 💰
Mariot Tsitoara, Apress, March 2024. ISBN 979-8-8688-0214-0. Combines version control, project management and teamwork. The best integrated Git + GitHub introduction for students (~$45).

**[Git for Teams](https://www.oreilly.com/library/view/git-for-teams/9781491911204/)** 🟡🔴 💰
Emma Jane Hogbin Westby, O'Reilly, 2015. ISBN 978-1-4919-1118-1. The only major book focused on people-first team workflow design: governance, branching conventions, code review and release processes. Essential for tech leads and teams despite its age. Flag the 2015 date when recommending it.

**[Git in Practice](https://www.manning.com/books/git-in-practice)** 🟡🔴 💰
Mike McQuaid, Manning, 2014. ISBN 978-1-6172-9197-5. 66 intermediate and advanced recipes. Source text free at [github.com/MikeMcQuaid/GitInPractice](https://github.com/MikeMcQuaid/GitInPractice). ⚠️ Does not cover `switch`/`restore` (2019 additions); otherwise still valuable for workflows.

**[Ultimate Git and GitHub for Modern Software Development](https://orangeava.com/products/ultimate-git-and-github-for-modern-software-development)** 🟢🟡 💰
Pravin Mishra, Orange Education/AVA, June 2024. ISBN 978-81-97223-83-9. Pairs Git fundamentals with GitHub Actions and GitOps (~$35).

**[Oh Shit, Git!?! (zine)](https://wizardzines.com/zines/oh-shit-git/)** 🟢🟡 💰
Julia Evans and Katie Sylor-Miller, Wizard Zines, ~$12-20 PDF. Plain-English recovery recipes in a delightful illustrated format. The free web version lives at [ohshitgit.com](https://ohshitgit.com).

**[How Git Works (zine)](https://wizardzines.com/zines/git/)** 🟡🔴 💰
Julia Evans, Wizard Zines, ~$12-20 PDF. The clearest modern pedagogical writing on Git internals available anywhere.

---

## Video courses and YouTube

### Free YouTube

**[freeCodeCamp - Git & GitHub Crash Course for Beginners (2026)](https://www.youtube.com/watch?v=mAFoROnOfHs)** 🟢 🆓
Sumit Saha. ~1 hour. The recommended free starter for 2026.

**[freeCodeCamp - Git for Professionals](https://www.freecodecamp.org/news/git-for-professionals/)** 🟡 🆓
Tobias Gunther (Tower). ~1 hour. Branching strategies, perfect commits, rebase vs merge. Strong for teams.

**[Fireship - Git Explained in 100 Seconds](https://www.youtube.com/watch?v=hwP7WQkmECE)** 🟢 🆓
Jeff Delaney. 1:40. The fastest concept introduction.

**[Fireship - 13 Advanced Git Techniques and Shortcuts](https://www.youtube.com/watch?v=ecK3EnyGD8o)** 🟡 🆓
Jeff Delaney. ~8 minutes. Power-user commands most developers do not know.

**[Traversy Media - Git & GitHub Crash Course (2025)](https://www.youtube.com/watch?v=vA5TTz6BXhY)** 🟢 🆓
Brad Traversy. ~49 minutes. Solid beginner walkthrough. ⚠️ Avoid the older 2018 version (vA5TTz6BXhY is the current one).

**[Kunal Kushwaha - Complete Git and GitHub Tutorial](https://www.youtube.com/watch?v=apGV9Kg7ics)** 🟢🟡 🆓
~1 hour 13 minutes. Strong for open-source contributors.

**[TechWorld with Nana - GitLab CI/CD Tutorial for Beginners](https://www.youtube.com/watch?v=qP8kir2GUgo)** 🟢🟡 🆓
~1 hour 9 minutes. The best free GitLab-specific tutorial available.

**[Web Dev Simplified - Learn Git in 20 Minutes](https://www.youtube.com/watch?v=IHaTbJPdB-s)** 🟢 🆓
Kyle Cook. 20 minutes for absolute beginners.

**[MIT Missing Semester - Version Control (Git)](https://www.youtube.com/watch?v=2sjqTHE0zok)** 🟡 🆓
~1 hour. Bottom-up "data model first" approach. Written version at [missing.csail.mit.edu/2020/version-control](https://missing.csail.mit.edu/2020/version-control/). The canonical explanation for computer science students.

### Paid courses

**[Udemy - Git Complete](https://www.udemy.com/course/git-complete/)** 🟢🟡 💰
Jason Taylor. 4.6 stars from ~34,700 ratings. ~6 hours. The most popular paid Git course. Usually ~$10-20 on sale.

**[Coursera - Introduction to Git and GitHub (Google)](https://www.coursera.org/learn/introduction-git-github)** 🟢🟡 🆓/💰
4.8 stars from ~8,000 ratings. 20-27 hours. Free to audit; paid certificate. Part of the Google IT Automation with Python Professional Certificate. One of the most taken Git courses in the world.

**[LinkedIn Learning - Git Essential Training (2023)](https://www.linkedin.com/learning/git-essential-training-25677984)** 🟢🟡 💰
Kevin Skoglund. 4.7 stars. ~2-3 hours. Requires LinkedIn Learning subscription (~$40/month).

**[Pluralsight - How Git Works](https://www.pluralsight.com/courses/how-git-works)** 🟡 💰
Paolo Perrotta. ~2 hours. The standout course for conceptual internals - makes Git genuinely click. Requires Pluralsight subscription.

**[Frontend Masters - Git In-depth](https://frontendmasters.com/courses/git-in-depth/)** 🟡🔴 💰
Nina Zakharenko. ~5 hours. Exercise repo at [github.com/nnja/advanced-git](https://github.com/nnja/advanced-git). The best paid deep dive on Git internals. Requires Frontend Masters subscription (~$39/month).

**[egghead.io - Productive Git for Developers](https://egghead.io/courses/productive-git-for-developers)** 🟡 💰
Juri Strumpflohner. ~33 minutes, dense with practical shortcuts.

---

## Interactive learning

**[Learn Git Branching](https://learngitbranching.js.org)** 🟢🟡🔴 🆓
Peter Cottle. [github.com/pcottle/learnGitBranching](https://github.com/pcottle/learnGitBranching). Animated branch-graph sandbox with progressive levels covering intro, remotes, rebase and advanced operations. **The single best interactive Git tutorial available.** Actively maintained.

**[Visualizing Git](https://git-school.github.io/visualizing-git)** 🟢🟡 🆓
[github.com/git-school/visualizing-git](https://github.com/git-school/visualizing-git). D3-powered live commit-graph visualizer. Type Git commands and watch the graph update in real time. Excellent for classroom use and understanding how branching and merging work visually.

**[GitHub Skills](https://skills.github.com)** 🟢🟡 🆓
See Official documentation above. The best structured interactive path for GitHub specifically.

**[Codecademy - Learn Git and GitHub](https://www.codecademy.com/learn/learn-git)** 🟢 🆓/💰
Updated with Copilot and Actions modules; aligned with the GitHub Foundations certification. Free tier has limited content; Pro subscription needed for full course.

**[The Odin Project - Foundations Git track](https://www.theodinproject.com/paths/foundations/courses/foundations)** 🟢 🆓
Free, community-maintained. Lessons on setting up Git, Git basics and practising Git basics. The broader Odin Project curriculum also has a dedicated [Git course](https://www.theodinproject.com/paths/full-stack-ruby-on-rails/courses/ruby-on-rails/lessons/a-railsy-web-refresher-git-refresher).

**[Oh My Git!](https://ohmygit.org)** 🟢🟡 🆓
[github.com/git-learning-game/oh-my-git](https://github.com/git-learning-game/oh-my-git). Open-source desktop game with 44+ puzzle levels running over a real Git backend. Fun way to build muscle memory. ⚠️ Low-maintenance mode as of 2025; still works.

**[Git Immersion](https://gitimmersion.com)** 🟢🟡 🆓
[github.com/git-immersion/gitimmersion](https://github.com/git-immersion/gitimmersion). ~50 guided labs taking you from `git init` to advanced branching. Free (CC BY-NC-SA). Content is solid; style is dated.

**[Roadmap.sh - Git and GitHub](https://roadmap.sh/git-github)** 🟢🟡 🆓
Kamran Ahmed and community. Clickable curriculum with per-node external resources. Very active, updated regularly.

**[NDP Software Visual Git Cheatsheet](https://ndpsoftware.com/git-cheatsheet.html)** 🟢🟡 🆓
Andrew Peterson. Interactive diagram showing how commands move data between stash, workspace, index, local repo and upstream. Not a tutorial but an excellent orientation tool.

---

## Websites and blogs

**[Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)** 🟢🟡🔴 🆓
Covers every major Git topic with the clearest diagrams on the web. The [advanced overview](https://www.atlassian.com/git/tutorials/advanced-overview) covers hooks, refs, reflog, LFS and large-repo patterns. Actively maintained.

**[Tower Git Tutorials and Blog](https://www.git-tower.com/learn)** 🟢🟡 🆓
Tobias Gunther. Free ebook, videos, cheatsheets and active blog covering practical Git topics through 2026.

**[GitKraken Learn Git](https://www.gitkraken.com/learn/git)** 🟢🟡 🆓
Free video and written tutorials covering CLI and GUI workflows, plus the GitKraken command reference.

**[Martin Fowler - Patterns for Managing Source Code Branches](https://martinfowler.com/articles/branching-patterns.html)** 🟡🔴 🆓
May 2020. The authoritative long-form treatment of mainline integration, feature branching, CI, release branches and hotfixes. Essential reading for team leads and anyone designing a branching strategy.

**[Julia Evans - jvns.ca](https://jvns.ca)** 🟢🟡🔴 🆓
Active blog with clear, illustrated Git internals posts. Search "git" on the site for a curated collection.

**[Oh Shit, Git!?! / Dang It, Git!](https://ohshitgit.com)** 🟢🟡 🆓
Katie Sylor-Miller. [github.com/ksylor/ohshitgit](https://github.com/ksylor/ohshitgit). Plain-English recovery recipes. Translated into 28+ languages. Free web version; paid illustrated zine also available.

**[Trunk Based Development](https://trunkbaseddevelopment.com)** 🟡🔴 🆓
Paul Hammant. The canonical reference for trunk-based development and continuous integration. Actively maintained.

**[Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)** 🟢🟡 🆓
The v1.0.0 specification. Stable, canonical.

**[GitHub Blog](https://github.blog)** 🟢🟡🔴 🆓
Official. Updated daily with product news, engineering posts and open source stories.

**[GitLab Blog](https://about.gitlab.com/blog)** 🟢🟡🔴 🆓
Official. Active, covers DevSecOps, CI/CD, security and platform updates.

**[Google Engineering Practices - Code Review](https://google.github.io/eng-practices/review/)** 🟡🔴 🆓
Google's published internal code review standards. The most widely cited public reference on the topic.

**[GitHub Flow](https://docs.github.com/en/get-started/using-github/github-flow)** 🟢 🆓
Official GitHub Flow documentation.

**[GitLab Flow](https://docs.gitlab.com/topics/gitlab_flow/)** 🟡 🆓
Official GitLab Flow documentation and diagrams.

**[Think Like (a) Git](https://think-like-a-git.net)** 🟡 🆓
Sam Livingston-Gray. Graph-theory approach to understanding Git. Evergreen but frozen at 2017.

**[thoughtbot - Git guides](https://github.com/thoughtbot/guides)** 🟡 🆓
Thoughtbot's internal development guides including Git workflow, code review and commit message conventions.

---

## Cheatsheets and quick references

**[GitHub Git Cheat Sheet (PDF)](https://education.github.com/git-cheat-sheet-education.pdf)** 🟢 🆓
GitHub Education. Clean two-page reference organised by task. Print and pin.

**[Atlassian Git Cheat Sheet](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)** 🟢🟡 🆓
Task-grouped, actively maintained.

**[GitLab Git Cheat Sheet (PDF)](https://about.gitlab.com/images/press/git-cheat-sheet.pdf)** 🟢 🆓
Official GitLab reference.

**[Tower Git Cheat Sheet](https://www.git-tower.com/learn/cheat-sheets/git)** 🟢🟡 🆓
Free PDF, actively maintained.

**[GitKraken Git Cheat Sheet](https://www.gitkraken.com/learn/git/commands)** 🟢🟡 🆓
Free, active.

**[NDP Software Visual Git Cheatsheet](https://ndpsoftware.com/git-cheatsheet.html)** 🟢🟡 🆓
Interactive spatial diagram. See Interactive learning above.

**[Oh Shit, Git!?!](https://ohshitgit.com)** 🟢 🆓
Recovery-focused quick reference. See Websites and blogs above.

**[Git Explorer](https://gitexplorer.com)** 🟢 🆓
[github.com/summitech/gitexplorer](https://github.com/summitech/gitexplorer). Pick-a-goal interface that generates the exact command you need.

**[First Aid Git](https://firstaidgit.io)** 🟢 🆓
Searchable FAQ with copy-pasteable answers. Lightly maintained but content is correct.

---

## Podcasts and audio

**[Changelog - "Scott Chacon taught an entire generation of devs how to Git" (Ep. 586)](https://changelog.com/podcast/586)** 🟢🟡🔴 🆓
Scott Chacon on Git history, GitHub, GitButler and the future of version control. All levels. Changelog Git episode archive: [changelog.com/topic/git](https://changelog.com/topic/git).

**[Syntax.fm - Git Fundamentals (Ep. 286)](https://syntax.fm/show/286/git-fundamentals)** 🟢 🆓
Wes Bos and Scott Tolinski. ~59 minutes. Beginner-accessible overview with practical tips. The Syntax feed also has additional episodes on Git aliases and GitHub workflows.

**[Software Engineering Unlocked - Making Git faster with Derrick Stolee](https://www.software-engineering-unlocked.com/episode-6-derrick-stolee/)** 🟡🔴 🆓
Microsoft engineer Derrick Stolee on scalar, commit-graph, sparse-checkout and partial clone at Windows-monorepo scale.

**[Tech Done Right - Building Git with James Coglan (Ep. 59)](https://www.techdoneright.io/59)** 🔴 🆓
~41 minutes. Deep internals dive based on Coglan's book *Building Git*. The best Git internals interview available in audio form.

**[The ReadME Podcast (GitHub)](https://github.com/readme/podcast)** 🟢🟡 🆓
Neha Batra and Martin Woodward. Open source stories from across the GitHub ecosystem. Active.

---

## GitHub repositories worth starring

These are repositories that will actively help you learn or work with Git and GitHub. Star counts are approximate.

### Core tools

**[git/git](https://github.com/git/git)** - ~54k stars
Official read-only Git source mirror. Development happens on the mailing list; this is the primary public-facing source.

**[github/gitignore](https://github.com/github/gitignore)** - ~173k stars
Canonical `.gitignore` templates by language and tool. Always start here before writing your own.

**[newren/git-filter-repo](https://github.com/newren/git-filter-repo)** - ~9k stars
The current recommended tool for rewriting Git history, replacing the deprecated `git filter-branch`.

**[git-ecosystem/git-credential-manager](https://github.com/git-ecosystem/git-credential-manager)** - ~7k stars
Cross-platform credential helper with OAuth, MFA and FIDO2 passkeys. The recommended credential helper for all platforms.

### Learning

**[k88hudson/git-flight-rules](https://github.com/k88hudson/git-flight-rules)** - ~42.5k stars
NASA-style flight rules for Git: "what do I do when X goes wrong?" Hundreds of scenarios with exact commands. Highly recommended alongside this course.

**[tiimgreen/github-cheat-sheet](https://github.com/tiimgreen/github-cheat-sheet)** - ~50k stars
Power-user Git and GitHub URL hacks, shortcuts and features most people do not know about.

**[arslanbilal/git-cheat-sheet](https://github.com/arslanbilal/git-cheat-sheet)** - ~22k stars
Git and git-flow cheat sheet in many languages.

**[pcottle/learnGitBranching](https://github.com/pcottle/learnGitBranching)** - ~31k stars
Source for the Learn Git Branching interactive game. See Interactive learning above.

**[git-school/visualizing-git](https://github.com/git-school/visualizing-git)** - actively maintained
Source for the Visualizing Git sandbox. See Interactive learning above.

**[dictcp/awesome-git](https://github.com/dictcp/awesome-git)** - ~1k stars
Curated list of Git tools, resources and libraries.

### Workflow tools

**[gitleaks/gitleaks](https://github.com/gitleaks/gitleaks)** - ~24.8k stars
Fast secret scanner for Git repositories. Pre-commit hook, CI integration, SARIF output. Covered in [reference/security.md](../09-reference/security.md).

**[trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog)** - ~24.5k stars
Secret scanner with live credential verification. 800+ detectors. Covered in [reference/security.md](../09-reference/security.md).

**[pre-commit/pre-commit](https://github.com/pre-commit/pre-commit)** - ~14k stars
Multi-language pre-commit hook framework. The standard tool for enforcing code quality and secret detection before commits.

**[conventional-commits/conventionalcommits.org](https://github.com/conventional-commits/conventionalcommits.org)** - ~2k stars
Source for the Conventional Commits specification website.

**[semantic-release/semantic-release](https://github.com/semantic-release/semantic-release)** - ~22k stars
Fully automated version management and package publishing driven by commit messages.

**[commitizen/cz-cli](https://github.com/commitizen/cz-cli)** - ~17k stars
JavaScript CLI (`git cz`) for writing Conventional Commits interactively.

**[jesseduffield/lazygit](https://github.com/jesseduffield/lazygit)** - ~63k stars
The leading Git TUI (terminal user interface). Covered in [terminal/06-lazygit.md](../07-terminal/06-lazygit.md).

**[dandavison/delta](https://github.com/dandavison/delta)** - ~26k stars
Syntax-highlighted, side-by-side diff pager. Covered in [terminal/05-delta.md](../07-terminal/05-delta.md).

**[jonas/tig](https://github.com/jonas/tig)** - ~12.5k stars
ncurses-based Git history browser with vi-style navigation. Covered in [terminal/09-tig.md](../07-terminal/09-tig.md).

**[extrawurst/gitui](https://github.com/extrawurst/gitui)** - ~18k stars
Rust-based async Git TUI. Covered in [terminal/10-gitui.md](../07-terminal/10-gitui.md).

**[cli/cli](https://github.com/cli/cli)** - ~39k stars
Official GitHub CLI (`gh`). Covered in [github/21-cli.md](../03-github/21-cli.md).

**[gitlab-org/cli](https://gitlab.com/gitlab-org/cli)** (GitLab, not GitHub)
Official GitLab CLI (`glab`). Covered in [gitlab/12-gitlab-cli.md](../04-gitlab/12-gitlab-cli.md).

**[git-lfs/git-lfs](https://github.com/git-lfs/git-lfs)** - ~13k stars
Large File Storage extension for Git.

**[tummychow/git-absorb](https://github.com/tummychow/git-absorb)** - ~3.5k stars
Automatically generates fixup commits targeted at the right parent. Pairs with `git rebase --autosquash`.

**[tj/git-extras](https://github.com/tj/git-extras)** - ~17k stars
60+ useful Git subcommand utilities.

### Ecosystem

**[jj-vcs/jj](https://github.com/jj-vcs/jj)** - ~27k stars
Jujutsu - a Git-compatible version control system in Rust. No staging area, first-class conflicts, powerful undo. Not a Git replacement yet but worth following: it has fundamentally changed how many advanced users think about version control. Covered in [ides-and-editors/07-antigravity.md](../06-ides-and-editors/07-antigravity.md).

**[getsops/sops](https://github.com/getsops/sops)** - ~22k stars
Secrets management tool using age, KMS or PGP. Integrates with Flux and Helm for GitOps. Covered in [reference/security.md](../09-reference/security.md).

**[external-secrets/external-secrets](https://github.com/external-secrets/external-secrets)** - ~14k stars
Kubernetes operator that syncs secrets from AWS, GCP, Azure, Vault and 30+ other providers. Covered in [reference/security.md](../09-reference/security.md).

---

## GUI clients and tools

### Multi-platform

**[GitKraken Desktop](https://www.gitkraken.com)** 🆓/💰
Cross-platform, visual commit graph, AI commit messages, Workspaces and stacked PRs. Free for public repos; Pro ~$8/seat/month. One of the most feature-rich Git GUIs available.

**[GitHub Desktop](https://desktop.github.com)** 🆓
GitHub. Free, open-source (MIT), GitHub-centric. The easiest GUI for beginners. Covered in [ides-and-editors/04-github-desktop.md](../06-ides-and-editors/04-github-desktop.md).

**[Sourcetree](https://www.sourcetreeapp.com)** 🆓
Atlassian. Free, Mac and Windows only. Solid for Bitbucket users. Maintained but slow cadence.

**[Sublime Merge](https://www.sublimemerge.com)** 🆓/💰
Sublime HQ. Free to evaluate; Personal $99 (3 years of updates) or Business $75/seat/year. Exceptionally fast, cross-platform.

### macOS only

**[Tower](https://www.git-tower.com)** 💰
fournova. Paid subscription ~$69/year individual; free for students via education pack. Considered the most polished native macOS Git client. Tower 12 added stacked PR support. Covered in [ides-and-editors/04-github-desktop.md](../06-ides-and-editors/04-github-desktop.md).

**[Fork](https://git-fork.com)** 💰
Dan Pristupov / fournova. Native clients for macOS and Windows. One-time payment $49.99. Fast and reliable.

### TUI clients

**[lazygit](https://github.com/jesseduffield/lazygit)** 🆓
The leading terminal UI for Git. See GitHub repositories above.

**[tig](https://github.com/jonas/tig)** 🆓
ncurses history browser. See GitHub repositories above.

**[gitui](https://github.com/extrawurst/gitui)** 🆓
Rust-based async TUI. See GitHub repositories above.

---

## Communities and forums

**[Git mailing list (git@vger.kernel.org)](https://lore.kernel.org/git/)** 🔴
The primary Git development channel. Junio Hamano (the Git maintainer) and contributors discuss every patch here. Read the archives to understand how Git evolves.

**[GitHub Community Discussions](https://github.com/orgs/community/discussions)** 🟢🟡🔴
Very active, staffed by GitHub team members. Search before posting.

**[GitLab Community Forum](https://forum.gitlab.com)** 🟢🟡🔴
Official GitLab community space.

**[Stack Overflow - git tag](https://stackoverflow.com/questions/tagged/git)** 🟢🟡🔴
~170,000+ questions. The archive is invaluable for specific error messages.

**[Reddit r/git](https://www.reddit.com/r/git/)** 🟢🟡
~70k members. Good for workflow questions and tool recommendations.

**[Reddit r/github](https://www.reddit.com/r/github/)** 🟢🟡
~200k members.

**[Reddit r/gitlab](https://www.reddit.com/r/gitlab/)** 🟢🟡
~25k members.

**[The Odin Project Discord](https://discord.gg/V75WSQG)** 🟢
Active Git and GitHub help channels. Excellent for beginners.

**[freeCodeCamp Discord](https://discord.gg/freecodecamp)** 🟢
Large beginner-friendly community.

---

## Newsletters

**[Git Rev News](https://git.github.io/rev_news/)** 🟡🔴 🆓
Christian Couder and contributors. **The de facto official Git community newsletter.** Monthly digest of mailing-list discussions, releases, tools and ecosystem news. Published since March 2015; current through 2026.

**[GitHub Changelog](https://github.blog/changelog/)** 🟢🟡🔴 🆓
Official GitHub feature-release feed. Updated multiple times daily. RSS available at [github.blog/changelog/feed/](https://github.blog/changelog/feed/).

**[GitLab Releases](https://about.gitlab.com/releases/)** 🟡🔴 🆓
Official monthly-plus-patch release notes.

---

## Certifications

### GitHub certifications

All GitHub exams are $99 USD (free for verified GitHub Education students). Proctored by PSI. Valid 3 years. Listed on Microsoft Learn.

**[GitHub Foundations (GH-900)](https://learn.microsoft.com/en-us/credentials/certifications/github-foundations/)** 🟢
Beginner - Git fundamentals, repositories, Markdown, issues, PRs, projects and open source. This is the best starting certification for anyone new to GitHub. Can be taken non-proctored (any time).

**[GitHub Actions (GH-200)](https://learn.microsoft.com/en-us/credentials/certifications/github-actions/)** 🟡
Intermediate - CI/CD automation and administration. $99.

**[GitHub Advanced Security (GH-500)](https://learn.microsoft.com/en-us/credentials/certifications/github-advanced-security/)** 🟡🔴
CodeQL, secret scanning, push protection, dependency review. $99.

**[GitHub Administration (GH-100)](https://learn.microsoft.com/en-us/credentials/certifications/github-administration/)** 🟡🔴
Organisation and enterprise administration. $99.

**[GitHub Copilot (GH-300)](https://learn.microsoft.com/en-us/credentials/certifications/github-copilot/)** 🟡
Launched 2025. Prompt engineering, Copilot Chat/CLI, Agent and Edit modes, enterprise policy. $99.

Registration: [examregistration.github.com](https://examregistration.github.com). Program hub: [resources.github.com/learn/certifications](https://resources.github.com/learn/certifications/).

### GitLab certifications

Pricing has unbundled since 2024. Most exams are ~$150 per attempt individually; a ~$650 bundle (eLearning + exam) is also offered. Verify current pricing at [university.gitlab.com/pages/certifications](https://university.gitlab.com/pages/certifications) before registering.

**[GitLab Certified Associate](https://university.gitlab.com)** 🟢🟡
Entry-level: Git, CI/CD, package and release, security.

**[GitLab Certified Git Associate](https://university.gitlab.com)** 🟢🟡
Git fundamentals and GitLab collaboration. Good for non-DevOps roles.

**[GitLab Certified CI/CD Associate](https://university.gitlab.com)** 🟡
Pipelines, runners, deployments.

**[GitLab Certified Security Specialist](https://university.gitlab.com)** 🔴
SAST, DAST, dependency scanning, vulnerability management.

---

## Recommended paths by level

### Complete beginner (never used version control)

Start here, in this order:

1. [git/01-what-is-version-control.md](../02-git/01-what-is-version-control.md) in this course
2. [GitHub Skills - Introduction to GitHub](https://skills.github.com)
3. [Learn Git Branching](https://learngitbranching.js.org) - levels 1-4
4. [freeCodeCamp - Git & GitHub Crash Course 2026](https://www.youtube.com/watch?v=mAFoROnOfHs)
5. [Atlassian Git Tutorials - beginner series](https://www.atlassian.com/git/tutorials/setting-up-a-repository)
6. [Oh Shit, Git!?!](https://ohshitgit.com) for when things go wrong
7. [GitHub Foundations certification (GH-900)](https://learn.microsoft.com/en-us/credentials/certifications/github-foundations/) when ready for formal validation

### Intermediate developer wanting to go deeper

1. [Version Control with Git, 3rd edition](https://www.oreilly.com/library/view/version-control-with/9781492091189/) - read in full
2. [Pluralsight - How Git Works](https://www.pluralsight.com/courses/how-git-works) by Paolo Perrotta
3. [Learn Git Branching](https://learngitbranching.js.org) - complete all levels
4. [Martin Fowler - Patterns for Managing Source Code Branches](https://martinfowler.com/articles/branching-patterns.html)
5. [freeCodeCamp - Git for Professionals](https://www.freecodecamp.org/news/git-for-professionals/)
6. [Julia Evans - How Git Works (zine)](https://wizardzines.com/zines/git/)
7. Adopt lazygit, delta and the gh CLI in your daily workflow

### Advanced user (internals and edge cases)

1. [Pro Git - Chapters 10-11](https://git-scm.com/book/en/v2/Git-Internals-Plumbing-and-Porcelain) on internals and plumbing
2. [Frontend Masters - Git In-depth](https://frontendmasters.com/courses/git-in-depth/) by Nina Zakharenko
3. [MIT Missing Semester - Version Control](https://missing.csail.mit.edu/2020/version-control/)
4. Subscribe to [Git Rev News](https://git.github.io/rev_news/)
5. [Tech Done Right - Building Git with James Coglan](https://www.techdoneright.io/59)
6. Follow the [Git mailing list](https://lore.kernel.org/git/)
7. Explore [Jujutsu (jj)](https://github.com/jj-vcs/jj) to stress-test your Git mental model
8. Study [git-flight-rules](https://github.com/k88hudson/git-flight-rules) for recovery patterns

### Teams and tech leads

1. [Git for Teams](https://www.oreilly.com/library/view/git-for-teams/9781491911204/) by Emma Jane Hogbin Westby
2. [Martin Fowler - Patterns for Managing Source Code Branches](https://martinfowler.com/articles/branching-patterns.html)
3. [Trunk Based Development](https://trunkbaseddevelopment.com)
4. [Google Engineering Practices - Code Review](https://google.github.io/eng-practices/review/)
5. Adopt [Conventional Commits](https://www.conventionalcommits.org), [pre-commit](https://pre-commit.com), [semantic-release](https://github.com/semantic-release/semantic-release) and [gitleaks](https://github.com/gitleaks/gitleaks)
6. [GitHub Advanced Security certification (GH-500)](https://learn.microsoft.com/en-us/credentials/certifications/github-advanced-security/) for security-focused teams
7. [reference/security.md](../09-reference/security.md) in this course for the full security reference

---

## Resources to avoid

These resources are outdated, discontinued or actively misleading. Do not use them as primary references.

**Katacoda** - shut down by O'Reilly on June 15, 2022. Any tutorials linking to katacoda.com will return a 404.

**git-it / jlord/git-it-electron** - [github.com/jlord/git-it-electron](https://github.com/jlord/git-it-electron) - effectively unmaintained since 2021; still uses `master` as the default branch name.

**nvie/gitflow** - the original GitFlow implementation is unmaintained. More importantly, Vincent Driessen himself added a note to his famous 2010 blog post in March 2020 explicitly steering teams doing continuous delivery away from GitFlow and toward GitHub Flow. Do not implement GitFlow for web applications that deploy continuously.

**petervanderdoes/gitflow-avh** - archived on GitHub on June 19, 2023.

**github/hub** - the original GitHub CLI (`hub`) is inactive since 2021. GitHub officially recommends `gh` (cli/cli) as the replacement. The `hub` repository still exists but receives no updates.

**Pre-2020 Git books** - the following books pre-date `git switch`, `git restore`, `git worktree`, sparse-checkout, partial clone and SHA-256. Commands remain valid but workflows and examples are outdated: *Git Pocket Guide* (Silverman, 2013), *Pragmatic Guide to Git* (Swicegood, 2010), *Pragmatic Version Control Using Git* (Swicegood, 2008).

**Exercism Git track** - does not exist despite being mentioned in some reading lists. Exercism has no Git-specific track.

**Corey Schafer - Git Tutorials playlist** - the content is conceptually correct but videos were recorded 2015-2018. Screenshots and some workflow examples are outdated. Use as a fallback, not a primary resource.

**git filter-branch** - not a resource but worth flagging: any tutorial that teaches `git filter-branch` for removing secrets or rewriting history is outdated. The official Git man page explicitly redirects to `git filter-repo`. Use `git filter-repo` instead.

---

## Sources and Further Reading

- [git-scm.com](https://git-scm.com/) - official Git documentation and Pro Git book
- [docs.github.com](https://docs.github.com/) - GitHub documentation
- [docs.gitlab.com](https://docs.gitlab.com/) - GitLab documentation
- [GitHub Skills](https://skills.github.com) - free interactive GitHub courses
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials) - the best third-party Git tutorial site
- [learn.microsoft.com - GitHub certifications](https://learn.microsoft.com/en-us/credentials/certifications/browse/?terms=github) - official GitHub certification catalog
- [university.gitlab.com](https://university.gitlab.com) - GitLab learning and certification platform
- [git.github.io/rev_news](https://git.github.io/rev_news/) - Git Rev News newsletter archive

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

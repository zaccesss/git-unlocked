# Make your first contribution

**Difficulty:** 🟢 Beginner | **Time:** 10 minutes

This is a safe sandbox for making your very first open source pull request. You will fork this repository, add your name to a list, and open a PR. That is it. No code required.

---

## Table of contents

- [What you will learn](#what-you-will-learn)
- [What you need](#what-you-need)
- [Step by step](#step-by-step)
- [What happens next](#what-happens-next)
- [Troubleshooting](#troubleshooting)
- [Want to go further](#want-to-go-further)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What you will learn

By completing this exercise you will have practised the full open source contribution workflow exactly once in a safe, consequence-free environment:

- Forking a repository you do not own
- Cloning your fork to your local machine
- Creating a feature branch
- Making a commit with a proper message
- Pushing to your fork
- Opening a pull request

This is the same workflow used to contribute to projects like VS Code, React, Kubernetes, and the Linux kernel. Once you have done it once, it stops feeling mysterious.

---

## What you need

- Git installed and configured ([git/02-installing-git.md](../git/02-installing-git.md))
- A GitHub account ([github/02-creating-an-account.md](../github/02-creating-an-account.md))
- A terminal or command prompt

That is all. You do not need any programming knowledge to complete this exercise.

---

## Step by step

### Step 1: Fork the repository

Go to [github.com/zaccessss/git-unlocked](https://github.com/zaccessss/git-unlocked) and click **Fork** in the top right corner, then click **Create fork**.

This creates a copy of the repository under your own GitHub account. You now have full write access to your copy.

**Using the GitHub CLI (optional but recommended):**

```bash
gh repo fork zaccessss/git-unlocked --clone=true --remote=true
cd git-unlocked
```

This forks, clones and sets up both `origin` (your fork) and `upstream` (the original) in one command.

### Step 2: Clone your fork

If you did not use the CLI above, clone your fork manually:

```bash
# Replace YOUR_USERNAME with your GitHub username
git clone https://github.com/YOUR_USERNAME/git-unlocked.git
cd git-unlocked
```

Add the original repository as `upstream` so you can sync later:

```bash
git remote add upstream https://github.com/zaccessss/git-unlocked.git
git remote -v
```

You should see two remotes: `origin` (your fork) and `upstream` (the original).

### Step 3: Create a branch

Never commit directly to `main`. Create a branch with your GitHub username in the name:

```bash
git switch -c add-YOUR_USERNAME
```

For example, if your username is `jsmith`:

```bash
git switch -c add-jsmith
```

### Step 4: Add your name to the contributors list

Open `first-contribution/contributors.md` in any text editor.

Find the section that matches the first letter of your GitHub username and add a new line with your name. Follow the exact format already used in the file:

```
- [YOUR_USERNAME](https://github.com/YOUR_USERNAME)
```

For example:

```
- [jsmith](https://github.com/jsmith)
```

Save the file.

### Step 5: Commit your change

Stage the file and commit with a descriptive message:

```bash
git add first-contribution/contributors.md
git commit -m "docs(first-contribution): add YOUR_USERNAME to contributors"
```

Replace `YOUR_USERNAME` with your actual GitHub username. The commit message follows [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) format - this is the standard used by most professional projects.

### Step 6: Push to your fork

```bash
git push -u origin add-YOUR_USERNAME
```

### Step 7: Open a pull request

**Using the GitHub CLI:**

```bash
gh pr create --title "docs(first-contribution): add YOUR_USERNAME to contributors" --body "Adding my name to the contributors list as part of the git-unlocked first-contribution exercise."
```

**Using the GitHub web interface:**

After pushing, GitHub will show a banner at the top of your fork with a **Compare and pull request** button. Click it. Fill in:

- **Title:** `docs(first-contribution): add YOUR_USERNAME to contributors`
- **Body:** A sentence or two about yourself and why you are learning Git

Click **Create pull request**.

That is it. You have made your first open source contribution.

---

## What happens next

Your PR will be reviewed and merged. Once merged, your name will appear permanently in [contributors.md](contributors.md) in the main repository.

You will receive a GitHub notification when your PR is merged. You can view the merged PR at any time in the [Pull requests tab](https://github.com/zaccessss/git-unlocked/pulls).

> [!NOTE]
> PRs that do not follow the format in `contributors.md`, include changes to files other than `contributors.md`, or have commit messages that do not follow the Conventional Commits format may be asked to make changes before merging.

---

## Troubleshooting

**My push was rejected.**

This usually means your local branch has diverged from your remote fork. Run:

```bash
git pull --rebase origin add-YOUR_USERNAME
git push
```

**I accidentally committed to `main`.**

```bash
# Create the correct branch
git branch add-YOUR_USERNAME

# Reset main to the original state
git reset --hard upstream/main

# Switch to the correct branch
git switch add-YOUR_USERNAME
```

**I cannot find the `upstream` remote.**

```bash
git remote add upstream https://github.com/zaccessss/git-unlocked.git
git fetch upstream
```

**My PR has a merge conflict.**

```bash
git fetch upstream
git switch add-YOUR_USERNAME
git rebase upstream/main
# Resolve any conflicts, then:
git push --force-with-lease
```

**I want to start over.**

```bash
# Delete your local branch
git switch main
git branch -D add-YOUR_USERNAME

# Sync main with upstream
git fetch upstream
git reset --hard upstream/main

# Start fresh
git switch -c add-YOUR_USERNAME
```

---

## Want to go further

Now that you have made your first contribution, here are some natural next steps:

**Learn the full workflow in depth**
Read [real-world/01-contributing-to-open-source.md](../real-world/01-contributing-to-open-source.md) for a complete guide to the open source contribution workflow, including how to handle review feedback, sync your fork with upstream over time, and work with projects that have their own specific contribution requirements.

**Find a real project to contribute to**
- [goodfirstissue.dev](https://goodfirstissue.dev) - curated first-timer issues from popular open source projects
- [firstcontributions.github.io](https://firstcontributions.github.io) - similar sandbox to this one, widely used
- [up-for-grabs.net](https://up-for-grabs.net) - projects explicitly welcoming new contributors
- [github.com/explore](https://github.com/explore) - GitHub's discovery feed

**Improve your commit messages**
Read about [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) and why structured commit messages make projects easier to maintain.

**Set up your environment properly**
Read [introduction/03-setting-up.md](../introduction/03-setting-up.md) for a complete development environment setup guide.

---

## Sources and Further Reading

- [GitHub: contributing to a project](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project)
- [GitHub: forking a repository](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo)
- [GitHub: creating a pull request from a fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork)
- [Conventional Commits specification](https://www.conventionalcommits.org/en/v1.0.0/)
- [firstcontributions/first-contributions](https://github.com/firstcontributions/first-contributions) - the original PR sandbox that inspired this one

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

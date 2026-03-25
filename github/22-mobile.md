# GitHub Mobile

**Difficulty:** 🟢 Beginner | **Time:** 15 minutes

GitHub Mobile is the official iOS and Android app for GitHub. It lets you manage issues and pull requests, review code, respond to notifications and stay on top of your repositories from your phone. This file covers everything the app can and cannot do, how to set it up and how to use it effectively.

---

## Table of Contents

- [What GitHub Mobile can do](#what-github-mobile-can-do)
- [What GitHub Mobile cannot do](#what-github-mobile-cannot-do)
- [Installing the app](#installing-the-app)
- [Signing in](#signing-in)
- [Notifications](#notifications)
- [Reviewing pull requests on mobile](#reviewing-pull-requests-on-mobile)
- [Managing issues on mobile](#managing-issues-on-mobile)
- [Browsing repositories and code](#browsing-repositories-and-code)
- [GitHub Mobile for organisations](#github-mobile-for-organisations)
- [Customising notifications](#customising-notifications)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What GitHub Mobile Can Do

GitHub Mobile is a triage and review tool. Its strengths are:

- Receiving and managing GitHub notifications
- Reading, commenting on and closing issues
- Reviewing pull requests - reading diffs, leaving comments and approving or requesting changes
- Merging pull requests
- Browsing repository files and code (read-only with syntax highlighting)
- Viewing your contribution graph, profile and activity feed
- Searching across repositories, issues, users and code
- Managing GitHub Actions - viewing workflow run status and logs
- Creating and managing issues
- Responding to discussions
- Two-factor authentication via the app (built-in authenticator)

---

## What GitHub Mobile Cannot Do

GitHub Mobile is not a coding environment. It cannot:

- Push commits or make code changes directly
- Run a terminal or execute code
- Create branches (as of March 2026)
- Edit files directly (read-only code view)
- Access GitHub Codespaces (use the browser on mobile for this)
- Use GitHub Actions to trigger workflows with custom inputs
- Manage repository settings beyond basic fields

For writing code on mobile, use the browser to open `github.dev` (the web editor) or a Codespace. The GitHub Mobile app is for staying on top of collaboration, not for coding.

---

## Installing the App

🍎 **iOS (iPhone and iPad):**

Search for **GitHub** in the App Store or go to `apps.apple.com` and search for GitHub. The publisher is GitHub Inc.

🤖 **Android:**

Search for **GitHub** in the Google Play Store. The publisher is GitHub Inc.

The app is free. There is no paid version of the app - your plan features (Copilot, etc.) are tied to your GitHub account, not the app itself.

---

## Signing In

**Step 1.** Open the app after installing.

**Step 2.** Tap **Sign in to GitHub.com** (or GitHub Enterprise Server if your organisation uses it).

**Step 3.** You will be redirected to a browser page for authentication. Sign in with your GitHub username and password.

**Step 4.** If you have two-factor authentication enabled (you should), complete the 2FA challenge.

**Step 5.** Tap **Authorise GitHub** to grant the app access to your account.

You are now signed in. The app opens to your notification feed.

**Multiple accounts:**

The app supports multiple GitHub accounts. Go to **Profile → Switch accounts** to add a second account (e.g. a work and a personal account).

---

## Notifications

The notification feed is the most important part of the app. It shows all activity on repositories you watch, issues and PRs you are involved in and mentions of your username.

**Notification types you will see:**

- Issue and PR comments
- Review requests
- Mentions (`@yourusername`)
- CI/CD run results
- Security alerts
- Releases from repositories you watch

**Managing notifications:**

Swipe left on a notification to reveal quick actions: **Mark as read**, **Bookmark** or **Done** (which archives it).

Tap a notification to open the issue or PR directly.

**Filters:**

Use the filter icon to show only specific notification types - unread only, review requests, mentions and so on.

**Bookmark:**

Bookmark important notifications to revisit them later. Bookmarks appear in a separate tab.

---

## Reviewing Pull Requests on Mobile

Mobile PR review is surprisingly usable for a phone screen.

**Opening a PR:**

From the notification feed, tap any PR notification. Or go to a repository, tap **Pull requests** and find the one you want.

**Reading the diff:**

Tap **Files changed** to see the code diff. Files are listed in order; tap any file to expand it. Changed lines are highlighted in green (additions) and red (deletions). Syntax highlighting is applied.

**Leaving a comment on a specific line:**

Tap a line number in the diff to open a comment box. Write your comment and submit. This creates a review comment on that line, exactly as you would on the web.

**Submitting a review:**

After reading and optionally leaving comments, tap the **Review changes** button. Choose:

- **Approve** - you are happy with the changes
- **Request changes** - you require changes before this can merge
- **Comment** - you have feedback but are not blocking or approving

**Merging a PR:**

If you have permission to merge, scroll to the bottom of the PR and tap **Merge pull request**. Choose your merge strategy (merge commit, squash or rebase).

---

## Managing Issues on Mobile

**Creating an issue:**

In any repository, tap the **Issues** tab then the **+** button. Fill in the title and body. You can assign labels, assignees and milestones by tapping the corresponding fields.

**Commenting on an issue:**

Open any issue and scroll to the comment box at the bottom. Markdown works in the comment body. Tap **Comment** to submit.

**Closing and reopening:**

Tap the **Close issue** button at the bottom of an issue to close it. Tap **Reopen** on a closed issue to reopen it.

**Labels and assignees:**

Tap the Labels or Assignees section to add or change them. You can select from the repository's existing labels.

---

## Browsing Repositories and Code

**Finding a repository:**

Use the search bar (magnifying glass icon) to search for any repository by name. Tap **Repositories** in your profile to see your own.

**Browsing files:**

Tap **Code** in a repository to browse the file tree. Tap any file to view its contents with syntax highlighting. Use the branch selector to switch between branches.

**Viewing commit history:**

In a repository, tap the commits count (shown as "N commits" near the top of the file list) to see the commit log. Tap any commit to see the diff for that commit.

**Viewing a specific commit:**

If you have a commit hash, paste it into the search bar to find it.

---

## GitHub Mobile for Organisations

If you belong to a GitHub organisation, you can access its repositories and notifications through the app.

Switch between your personal account and organisation context using the account switcher in the profile tab.

Organisation admins can review and approve repository access requests, manage pending invitations and view organisation-level notifications.

---

## Customising Notifications

Fine-tune which notifications the app shows to avoid noise.

**In the app:**

Go to **Profile → Settings → Notifications**. You can turn on or off:

- Push notifications (shown even when the app is closed)
- In-app notifications

**On GitHub.com (controls what generates notifications):**

Go to `github.com/settings/notifications` on the web. This is where you configure which events trigger notifications at all - review requests, mentions, workflow runs, security alerts and so on. Changes here affect all GitHub notification channels (email, mobile and web).

**Watching repositories:**

You receive notifications for repositories you "watch." To reduce noise, change your watch settings on individual repositories. Go to the repository on the web, click the **Watch** button and select **Custom** to choose only the events you care about (just releases, or just security alerts, for example).

---

## Try It Yourself

**Step 1.** Install the GitHub app on your phone.

**Step 2.** Sign in with your GitHub account.

**Step 3.** Go to your profile tab and view your contribution graph.

**Step 4.** Find the `git-unlocked` repository (search for `zaccessss/git-unlocked`). Star it. Browse the file list and open any file to read it.

**Step 5.** Go to your notification feed. Tap any notification to open the linked issue or PR.

**Step 6.** If you have an open pull request on any repository, open it from the mobile app. Read the diff and leave a comment.

---

## Common Mistakes

**Expecting to write code in the app.** The app does not have a code editor. Use the browser on mobile and go to `github.dev` if you need to make a quick edit from your phone.

**Turning off all notifications.** Notifications are the main value of the mobile app. Disable only the noisy categories (e.g. workflow runs for CI-heavy projects) rather than all of them.

**Missing the bookmark feature.** If a PR or issue needs your attention later but you cannot deal with it now, bookmark it instead of marking it as read. It will not get lost.

**Using the app for everything.** Mobile is best for review and triage. For serious code review, repository management or Actions configuration, use the desktop browser or VS Code.

---

## Summary

- GitHub Mobile is available free on iOS and Android
- Best used for: notifications, PR review, issue management and code browsing
- Cannot be used for: committing code, editing files or running terminals
- The app supports multiple accounts and GitHub Enterprise
- Customise notifications on `github.com/settings/notifications` to control what you receive
- For mobile coding, use the browser at `github.dev` or open a Codespace

---

## Sources and Further Reading

- [GitHub Mobile documentation](https://docs.github.com/en/get-started/using-github/github-mobile) - official guide
- [GitHub for iOS on the App Store](https://apps.apple.com/app/github/id1477376905) - download link
- [GitHub for Android on Google Play](https://play.google.com/store/apps/details?id=com.github.android) - download link
- [Managing notifications on GitHub](https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github) - notification configuration

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

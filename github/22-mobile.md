# GitHub Mobile

**Difficulty:** 🟢 Beginner | **Time:** 20 minutes

GitHub Mobile is the official iOS and Android app for GitHub. It lets you manage issues and pull requests, review code with inline comments, respond to notifications, manage your organisations and stay on top of your repositories from your phone. This file covers everything the app can and cannot do, how to set it up, how to use it as a built-in two-factor authenticator, how to manage notifications effectively and how to get the most out of it for code review on the go.

---

## Table of Contents

- [What GitHub Mobile can do](#what-github-mobile-can-do)
- [What GitHub Mobile cannot do](#what-github-mobile-cannot-do)
- [Installing the app](#installing-the-app)
- [Signing in](#signing-in)
- [Multiple accounts](#multiple-accounts)
- [GitHub Mobile as a 2FA authenticator](#github-mobile-as-a-2fa-authenticator)
- [Notifications](#notifications)
- [Customising notification settings](#customising-notification-settings)
- [Triaging notifications effectively](#triaging-notifications-effectively)
- [Reviewing pull requests on mobile](#reviewing-pull-requests-on-mobile)
- [Managing issues on mobile](#managing-issues-on-mobile)
- [Browsing repositories and code](#browsing-repositories-and-code)
- [Discussions on mobile](#discussions-on-mobile)
- [GitHub Actions on mobile](#github-actions-on-mobile)
- [Searching on mobile](#searching-on-mobile)
- [GitHub Mobile for organisations](#github-mobile-for-organisations)
- [Your profile on mobile](#your-profile-on-mobile)
- [App settings](#app-settings)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What GitHub Mobile Can Do

GitHub Mobile is a triage, review and communication tool. Its strengths are:

**Notifications and communication:**
- Receive push notifications for mentions, review requests, CI results, issue comments and security alerts
- Read and respond to all notifications in a unified feed
- Bookmark notifications to revisit later
- Mark notifications as read, done or muted

**Pull request review:**
- Read full PR descriptions and the complete file diff
- Leave inline comments on specific lines of changed code
- Add general PR comments
- Approve a PR, request changes or comment without approving
- Merge pull requests (merge commit, squash or rebase)
- Check CI status for a PR

**Issues:**
- Create issues with labels, assignees and milestones
- Comment on issues
- Close and reopen issues
- Assign and unassign people
- Add and remove labels

**Repositories:**
- Browse the file tree of any repository
- Read file contents with syntax highlighting
- View commit history and individual commit diffs
- Switch between branches
- Star and watch/unwatch repositories

**Profile and social:**
- View your contribution graph
- View your profile, repositories and starred repositories
- Follow and unfollow users
- View other developers' profiles

**Organisations:**
- View organisation repositories
- Manage pending membership invitations (owners)
- View team memberships

**Security:**
- Built-in TOTP authenticator for two-factor authentication (replaces a separate auth app for GitHub specifically)
- View Dependabot security alerts

**GitHub Actions:**
- View workflow run status and history
- View step-by-step logs for workflow runs
- Re-run failed workflows

---

## What GitHub Mobile Cannot Do

GitHub Mobile is a review and triage tool. It is not a development environment.

- **Cannot push commits or make code changes** - no code editor, no file editing, no git operations
- **Cannot create branches** - branch management is not available in the app
- **Cannot access GitHub Codespaces** directly (use the mobile browser for this)
- **Cannot create or edit GitHub Actions workflows** - workflow YAML editing is web/editor only
- **Cannot manage repository settings** - branch protection, secrets, webhooks, Actions settings
- **Cannot manage organisation billing or security settings** - these require the web
- **Cannot access the GitHub CLI** - the CLI is a desktop tool

For writing code on mobile, open `github.com` in your mobile browser and go to `github.dev` (press `.` on a repository) for a browser-based VS Code editor, or open a Codespace in the browser.

---

## Installing the App

🍎 **iOS (iPhone and iPad):**

Search for **GitHub** in the App Store, or go to `apps.apple.com` and search for GitHub. The publisher is **GitHub Inc.** Requires iOS 16.0 or later.

🤖 **Android:**

Search for **GitHub** in the Google Play Store. The publisher is **GitHub Inc.** Requires Android 8.0 or later.

The app is free. There is no paid version - your plan features (Copilot, private repositories etc.) are tied to your GitHub account.

---

## Signing In

**Step 1.** Open the GitHub app after installing.

**Step 2.** Tap **Sign in to GitHub.com** (or **Sign in to GitHub Enterprise** if your organisation uses a self-hosted GitHub Enterprise Server instance and you know the server URL).

**Step 3.** The app opens a browser view for authentication. Enter your GitHub username and password.

**Step 4.** Complete your two-factor authentication challenge if you have 2FA enabled (you should - see the section below).

**Step 5.** Tap **Authorise GitHub** to grant the app access to your account. You are returned to the app and signed in.

The app opens on your notification feed.

---

## Multiple Accounts

GitHub Mobile supports multiple accounts signed in simultaneously. This is useful if you have a personal GitHub account and a work account.

**Add a second account:**

1. Tap your profile picture in the bottom-right corner
2. Tap the account name at the top of the profile screen
3. Tap **Add account**
4. Sign in with the second account

**Switch between accounts:**

1. Tap your profile picture
2. Tap the account name
3. Select the account to switch to

Each account has its own notification feed and repository access. The app shows which account is currently active.

---

## GitHub Mobile as a 2FA Authenticator

GitHub Mobile has a built-in TOTP (time-based one-time password) authenticator. You can use it as your second factor for GitHub sign-ins instead of a separate app like Google Authenticator or Authy.

**Why use it:** Slightly more convenient for GitHub since it is already installed. Push notification prompts appear directly in the app when you sign in on a new device.

**Why you might prefer a separate app:** A dedicated authenticator (1Password, Authy, Bitwarden) also protects your other accounts (email, banking, etc.) and is independent of GitHub. If your GitHub account is ever compromised, a separate authenticator is not affected.

**Set up GitHub Mobile as your authenticator:**

1. Sign in to `github.com` on a desktop browser
2. Go to **Settings → Password and security → Two-factor authentication**
3. Click **Edit** next to your current 2FA method, or enable 2FA if not already active
4. Select **GitHub Mobile** as your authentication method
5. Open the GitHub app on your phone
6. In the app, go to **Profile → Settings → Two-factor authentication**
7. Tap **Register this device** or follow the prompt that appears

**How it works once set up:**

When you sign in to GitHub on a new device or browser, instead of entering a 6-digit code, GitHub sends a push notification to your phone. Open the notification, verify the sign-in attempt looks legitimate and tap **Approve**.

---

## Notifications

The notification feed is the primary reason most people install GitHub Mobile. It gives you real-time awareness of everything happening on repositories you care about, filtered by type.

**Notification types in the feed:**

- **Mentions** - someone used `@YOUR_USERNAME` in an issue, PR or comment
- **Review requests** - you have been asked to review a pull request
- **Subscribed** - activity on issues or PRs you are watching
- **Team mentions** - a team you belong to was mentioned
- **Security alerts** - Dependabot found a vulnerability in a repository you own
- **CI/CD** - workflow run results on repositories you watch
- **Releases** - new releases from repositories you are watching

**Notification states:**

Each notification can be:
- **Unread** - shown with a blue dot
- **Read** - visible but no indicator
- **Bookmarked** - saved separately in the Bookmarks tab for later
- **Done** - archived/cleared from the main feed

**Quick actions on a notification:**

Swipe left on any notification to reveal action buttons:
- **Done** - archive it (clears from feed, not deleted)
- **Unsubscribe** - stop receiving notifications for that issue/PR
- **Mark as read/unread**

Tap and hold a notification for more options including Bookmark.

---

## Customising Notification Settings

There are two levels of notification configuration: what GitHub generates, and what the app displays.

**In the app - push notification settings:**

1. Tap your profile picture (bottom-right)
2. Tap **Settings**
3. Tap **Notifications**

Here you control:
- Whether push notifications appear at all
- Which types of notifications trigger push alerts (mentions only vs all activity)
- Whether to receive notifications for participating vs watching

**On GitHub.com - what generates notifications:**

The most important configuration is on the web at `github.com/settings/notifications`. This controls what GitHub sends to all your notification channels (email, mobile and web together).

Key settings:

- **Participating and @mentions** - always recommended to keep on; these are direct communications to you
- **Watching** - activity on repositories you have clicked Watch on. This can generate a lot of noise for busy repositories.
- **Workflow runs** - whether CI results generate notifications. Turn this off for repositories with frequent CI activity if the noise becomes distracting.
- **Dependabot alerts** - security vulnerability notifications. Keep these on.

**Controlling per-repository notification volume:**

On any repository page, click the **Watch** button. Options:

- **Participating and @mentions** - only notified when directly involved
- **All Activity** - every issue, PR, comment and commit comment
- **Custom** - choose specific events (issues, PRs, releases, security alerts etc.)
- **Ignore** - never notified, even for @mentions (use carefully)

For active open source repositories, set to **Participating and @mentions** to avoid notification overload. For your own repositories, **All Activity** makes sense.

---

## Triaging Notifications Effectively

The notification feed becomes overwhelming if you do not manage it. A practical approach:

**Daily triage pattern:**

1. Open the app when you have a few minutes
2. Scan the feed - read mentions and review requests first (use the filter button to show only these types)
3. For each notification: handle it, bookmark it for later or mark as Done
4. Aim to clear the unread count daily

**Using filters:**

Tap the filter icon at the top of the notification feed to filter by:
- Type (mentions, review requests, issues, PRs, CI)
- Read/unread
- Specific repository

**Bookmarking:**

Use bookmarks for notifications that need attention but cannot be handled right now - "review this PR tonight" or "respond to this issue when at a computer". The Bookmarks tab in the feed keeps these separate from the main feed.

**Muting conversations:**

On any issue or PR, tap the three-dot menu and select **Unsubscribe** to stop receiving notifications for that specific conversation without unsubscribing from the whole repository.

---

## Reviewing Pull Requests on Mobile

Mobile PR review is more capable than most people expect. It is well-suited for smaller PRs, documentation changes and cases where you want to approve or flag for changes while away from a computer.

**Finding a PR to review:**

From the notification feed: tap any review request notification.

Or navigate manually: tap the search icon → **Pull requests** → filter by **Review requested**.

Or from a repository: tap the **Pull requests** tab and browse.

**Reading the PR:**

The PR view shows:
- Title and description (full Markdown rendered)
- Status: open/closed/merged/draft
- Checks summary (CI pass/fail)
- Reviewers, assignees, labels, milestone
- Commits list
- **Files changed** tab with the full diff

**Reading the diff:**

Tap **Files changed** to see the diff. Files are listed in order. Tap any file to expand it fully. Lines are highlighted: green for additions, red for deletions. Syntax highlighting is applied based on file type.

**Leaving an inline comment on a specific line:**

1. In the diff view, tap any line number
2. A comment input box appears anchored to that line
3. Write your comment - Markdown is supported
4. Tap **Add single comment** to post it immediately, or **Start review** to batch it with other comments

**Submitting a review:**

After leaving any comments:

1. Tap the **Review changes** button (at the top of the Files changed view)
2. Choose:
   - **Approve** - you are satisfied with the changes
   - **Request changes** - you require specific changes before this can merge
   - **Comment** - feedback without blocking or approving
3. Optionally add a summary comment
4. Tap **Submit review**

**Merging a PR:**

Scroll to the bottom of the PR view. If you have permission and checks have passed, you will see a merge button. Tap it and choose the merge strategy:
- Merge commit
- Squash and merge
- Rebase and merge

Tap **Confirm** to complete the merge.

**Checking CI status:**

The PR view shows a summary of all checks (passed/failed/pending). Tap the checks summary to see the full list with individual job results. For detailed logs, you need the web or desktop.

---

## Managing Issues on Mobile

**Creating an issue:**

1. Navigate to the repository
2. Tap the **Issues** tab
3. Tap the **+** button (top right)
4. Fill in the title
5. Write the body - the text editor supports Markdown. Use the toolbar above the keyboard for formatting shortcuts (bold, italic, code, lists, etc.)
6. Tap the **Labels**, **Assignees** and **Milestone** fields to set them
7. Tap **Submit new issue**

**Viewing and filtering issues:**

In the Issues tab, use the filter button to filter by:
- State: open / closed
- Author
- Label
- Assignee
- Milestone
- Sort order

**Commenting on an issue:**

Scroll to the bottom of any issue and type in the comment box. Markdown formatting is supported. Tap **Comment** to post.

**Closing and reopening:**

On any open issue, scroll to the bottom. Tap **Close issue** or **Close as not planned** depending on the reason. On a closed issue, tap **Reopen issue**.

**Labels and assignees:**

In the right panel of any issue or PR, tap **Labels** or **Assignees** to add or change them. You can select from existing labels and organisation members.

**Reacting to comments:**

Long-press any comment to add a reaction emoji. This is a quick way to acknowledge a comment without leaving a full reply.

---

## Browsing Repositories and Code

**Finding a repository:**

Tap the search icon. Type the repository name or `owner/repo` format. Tap **Repositories** in the search results.

**Browsing the file tree:**

On any repository, tap **Code** to open the file browser. Navigate into folders by tapping them. Use the branch selector (showing the current branch name) to switch branches. Tap any file to view it.

**Reading code:**

Files open with syntax highlighting based on their extension. The full file is shown - long files are scrollable. Line numbers are shown on the left.

**Viewing raw file content:**

Tap the three-dot menu on any file view and select **View raw** to see the unformatted file content.

**Commit history:**

On any repository, tap the commit count (shown as "N commits" in the repository header) to open the commit log. Each commit shows the message, author, date and shortened hash. Tap any commit to see its full diff.

**Blame view:**

On any file view, tap the three-dot menu and select **Blame** to see which commit last modified each line. Useful for understanding why a line is the way it is.

**Following links in code:**

On the web, GitHub renders links in Markdown. In the mobile app, tap any link in a rendered Markdown file to follow it - this works for both internal GitHub links (to other files, issues, PRs) and external URLs.

---

## Discussions on Mobile

GitHub Discussions are accessible in the mobile app.

**Finding discussions:**

In any repository that has Discussions enabled, tap the **Discussions** tab.

**Reading a discussion:**

Tap any discussion to read the full thread. Comments are shown in order. The accepted answer (in Q&A discussions) is highlighted at the top.

**Commenting:**

Scroll to the bottom of a discussion and type in the comment box. Tap **Comment** to post.

**Marking an answer (repository owners/maintainers):**

On Q&A discussion answers, tap the tick icon next to the answer you want to mark as accepted. This closes the discussion as answered and awards the Galaxy Brain achievement progress to the answerer.

**Creating a discussion:**

Tap the **+** button in the Discussions tab. Choose the category, write the title and body, and post.

---

## GitHub Actions on Mobile

**Viewing workflow runs:**

In any repository, tap the **Actions** tab to see the workflow run history. Runs are listed with their status (success, failure, in progress, cancelled).

**Viewing a specific run:**

Tap any run to see the jobs it contains. Tap any job to see the step-by-step log for that job.

**Log viewing:**

Mobile log viewing shows the full step logs. Scroll to find failures. Failed steps are shown with a red indicator. The log text is searchable using your phone's built-in text search (in most browsers, not the native app).

**Re-running a workflow:**

On any completed or failed workflow run, tap the three-dot menu and select **Re-run jobs** or **Re-run all jobs**.

**Notifications for workflow runs:**

If you have workflow run notifications enabled, you receive a push notification when a run on a watched repository completes. Tap the notification to go directly to the run result.

---

## Searching on Mobile

The search feature in GitHub Mobile supports the same search syntax as the web.

**Types of search:**

Tap the search icon at the bottom of the app. The search bar accepts:

- Repository names: `zaccessss/git-unlocked`
- User names: `zaccessss`
- Issues and PRs: type a search term and tap **Issues** or **Pull requests** in the results
- Code: limited code search is available
- Topics: `topic:machine-learning`

**Search filters:**

After searching, use the filter button to narrow results by state, label, author and other qualifiers. The same `is:open`, `is:merged`, `label:bug`, `author:@me` qualifiers work as on the web.

---

## GitHub Mobile for Organisations

If you belong to one or more GitHub organisations, you can access their repositories and manage some organisation features from the app.

**Switching to an organisation context:**

Tap your profile picture → tap your account name → select the organisation. The app switches to show that organisation's repositories and activity.

**What you can do for organisations:**

- Browse organisation repositories
- View and manage issues and PRs in organisation repositories
- See organisation members
- Approve or deny pending membership invitations (if you are an owner)
- View team memberships

**What requires the web:**

- Changing organisation settings
- Managing teams
- Configuring branch protection
- Managing billing
- Audit log access

---

## Your Profile on Mobile

**Viewing your profile:**

Tap the profile icon (bottom-right). Your profile shows:
- Profile photo, name, bio, followers and following counts
- Your contribution graph (the green squares calendar) for the past year
- Your pinned repositories
- Your public repositories sorted by recent activity

**Viewing someone else's profile:**

Tap any username anywhere in the app (in issue comments, PR descriptions, commit authors) to go to their profile.

**Following users:**

On any user's profile, tap **Follow**. Following users means their public activity appears in your personal feed on the Explore tab.

**The Explore tab:**

The Explore tab (compass icon) shows:
- Activity from people you follow
- Trending repositories in languages you use
- Topics related to your interests
- Recommended repositories based on your stars and activity

---

## App Settings

Access settings by tapping your profile picture → **Settings**.

**Appearance:**

- Light, dark or system-default theme
- Font size adjustment for code and content

**Notifications:**

- Push notification permissions (controlled per device here, more granular control on GitHub.com)
- Which notification types trigger push alerts

**Accessibility:**

- Bold text option
- Reduce motion
- Large text support (follows system accessibility settings)

**Privacy:**

- Clear browsing data cached in the app
- Sign out option

---

## Try It Yourself

**Step 1.** Install the GitHub app on your phone from the App Store or Google Play.

**Step 2.** Sign in with your GitHub account.

**Step 3.** Set up GitHub Mobile as a 2FA method:
- Go to **Profile → Settings → Two-factor authentication**
- Follow the prompts to register your device

**Step 4.** Browse a repository:
- Tap the search icon and search for `zaccessss/git-unlocked`
- Tap **Code** and browse the folder structure
- Open any `.md` file and read it
- Tap the commit count and read through the recent commit history

**Step 5.** Customise your notifications:
- Tap your profile picture → **Settings → Notifications**
- Turn off push notifications for workflow runs if you find them noisy
- Go to `github.com/settings/notifications` on a desktop browser and review your watching settings

**Step 6.** If you have an open pull request anywhere:
- Find it from the notification feed or by navigating to the repository
- Open the **Files changed** tab
- Tap a line number and leave a test inline comment
- Delete the comment afterwards

---

## Common Mistakes

**Expecting to edit code in the app.** GitHub Mobile has no code editor. For quick edits on mobile, open the repository in your mobile browser and press `.` to use `github.dev`.

**Turning off all notifications.** The entire value of the mobile app is knowing about mentions and review requests immediately. Disabling all notifications means you might as well not have the app. Instead, be selective - disable workflow run notifications if they are noisy, but keep mentions and review requests enabled.

**Not using bookmarks.** Without bookmarks, interesting notifications get buried under new ones and forgotten. Bookmark anything that needs attention later rather than leaving it in the main feed.

**Not setting per-repository watch settings.** Watching busy open source repositories with "All Activity" generates hundreds of notifications. Set busy repositories to "Participating and @mentions" to stay aware without the noise.

**Trying to do serious code review on a phone.** Mobile is fine for small PRs, typo fixes and documentation changes. For a 50-file diff with complex logic, wait until you are at a computer. Approving code you have not properly reviewed causes real problems.

**Missing the built-in 2FA authenticator.** Many people install a separate authenticator app and never realise GitHub Mobile has one built in. If GitHub is the main account you need 2FA for, using the built-in one is simpler.

---

## Summary

- GitHub Mobile is available free for iOS (16+) and Android (8+)
- **Best for:** notifications, PR review and approval, issue management, code browsing
- **Cannot do:** push commits, edit files, create branches, manage repository or organisation settings
- Supports **multiple accounts** - switch between personal and work accounts from the profile tab
- Has a **built-in TOTP 2FA authenticator** - use it for GitHub sign-ins or use a separate authenticator app
- **Notifications** are the main value; configure per-repository watch settings at `github.com/settings/notifications` to control volume
- Use **bookmarks** for notifications that need attention later
- Set busy open source repositories to **Participating and @mentions** to avoid notification overload
- **Inline PR review** works well on mobile for small diffs; for large or complex PRs, wait for a desktop
- For mobile coding, use the mobile browser at `github.dev` or open a Codespace

---

## Sources and Further Reading

- [GitHub Mobile documentation](https://docs.github.com/en/get-started/using-github/github-mobile) - official guide
- [GitHub for iOS - App Store](https://apps.apple.com/app/github/id1477376905)
- [GitHub for Android - Google Play](https://play.google.com/store/apps/details?id=com.github.android)
- [Managing notifications on GitHub](https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github/setting-up-notifications/configuring-notifications) - full notification configuration reference
- [Configuring two-factor authentication](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication) - setting up 2FA including GitHub Mobile

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

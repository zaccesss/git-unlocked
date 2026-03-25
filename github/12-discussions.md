# GitHub Discussions

**Difficulty:** 🟢 Beginner | **Time:** 15 minutes

GitHub Discussions is a forum-style feature built into GitHub repositories and organisations. It provides a space for open-ended conversations that do not fit neatly into the issue tracker - questions, ideas, announcements, polls and community building. Understanding when to use Discussions versus Issues versus pull requests is key to keeping a project's communication organised and accessible.

---

## Table of Contents

- [What is GitHub Discussions?](#what-is-github-discussions)
- [Discussions vs Issues](#discussions-vs-issues)
- [Enabling Discussions](#enabling-discussions)
- [Discussion categories](#discussion-categories)
- [Creating a discussion](#creating-a-discussion)
- [Q&A discussions and marking answers](#qa-discussions-and-marking-answers)
- [Polls](#polls)
- [Announcements](#announcements)
- [Upvoting and reactions](#upvoting-and-reactions)
- [Pinning discussions](#pinning-discussions)
- [Converting issues to discussions](#converting-issues-to-discussions)
- [Converting discussions to issues](#converting-discussions-to-issues)
- [Organisation-level discussions](#organisation-level-discussions)
- [Discussion templates](#discussion-templates)
- [Locking and managing discussions](#locking-and-managing-discussions)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is GitHub Discussions?

GitHub Discussions was launched in 2020 and made generally available in August 2021. It is a community forum built directly into GitHub - no third-party tool required, no separate account, integrated with the same repository and permissions system.

**What Discussions is for:**

- Questions from users who are not sure if something is a bug or expected behaviour
- Brainstorming ideas before they are ready to become a concrete issue
- Community announcements from maintainers
- Polls to gauge community preference
- General conversation and getting-to-know-the-project
- Show and tell - users sharing what they built with the project
- Meeting notes or decision logs

**What you can do in a Discussion:**

- Write with full Markdown support
- Embed images, code blocks and videos
- @mention users and teams
- Quote and reply to specific comments
- Mark answers (in Q&A categories)
- Upvote discussions and comments
- React with emoji
- Create polls
- Pin important discussions

---

## Discussions vs Issues

The clearest way to understand when to use each:

**Use Issues when:**
- There is a specific bug to fix
- There is a specific feature to build
- There is a defined task to complete
- The conversation has a clear resolution - it will be closed when done

**Use Discussions when:**
- You are not sure if something is a bug or expected behaviour
- You have an idea that is not yet ready for an issue
- You want community input before creating an issue
- You want to ask how to do something (Q&A)
- You want to make an announcement
- The conversation may not have a single definitive answer

> [!TIP]
> A common workflow: a question or idea starts as a Discussion. Once the community has discussed it and it crystallises into a clear, actionable task, it is converted to an Issue.

---

## Enabling Discussions

Discussions is not enabled by default on new repositories.

**Enable for a repository:**

Settings → General → Features → Check **Discussions** → Save.

The **Discussions** tab appears in the repository navigation.

**Disable:**

Settings → General → Features → Uncheck **Discussions**.

Existing discussions are preserved but not accessible until re-enabled.

---

## Discussion Categories

Discussions are organised into categories. Every discussion belongs to exactly one category.

**Default categories:**

| Category | Icon | Description |
|---|---|---|
| Announcements | 📣 | Only maintainers can create; everyone can comment |
| General | 💬 | General conversation |
| Ideas | 💡 | Feature ideas and suggestions |
| Polls | 🗳️ | Multiple-choice polls |
| Q&A | 🙏 | Questions with the ability to mark a best answer |
| Show and tell | 🙌 | Share what you built with the project |

**Creating custom categories:**

Discussions → Manage categories → New category.

Set: name, description, emoji, format (open-ended discussion or Q&A). Up to **25 categories** per repository.

**Category formats:**

- **Open-ended discussion:** Standard forum thread format. No answer marking.
- **Question/Answer:** Enables marking a comment as the accepted answer.

**Reordering categories:**

Drag and drop categories in the Manage categories view to change their display order.

---

## Creating a Discussion

1. Go to the **Discussions** tab in a repository
2. Click **New discussion**
3. Select a **category**
4. Write a **title** - be specific and descriptive
5. Write the body in Markdown - include as much context as needed
6. Click **Start discussion**

Discussions support the same Markdown features as issues and pull requests: headings, code blocks, images, tables, task lists, emoji, @mentions and `#N` references to issues and PRs.

**Draft discussions:**

You can save a discussion as a draft before posting. This lets you prepare an announcement or Q&A post in advance without publishing it immediately.

---

## Q&A Discussions and Marking Answers

The Q&A category is designed for questions that have a definitive answer. It allows one comment to be marked as the **accepted answer**.

**How to mark an answer:**

The discussion author (or a repository maintainer) can click **Mark as answer** on any comment. The marked answer:

- Jumps to the top of the comment thread
- Shows a green checkmark
- The discussion shows as "Answered" in the discussion list

**Why it matters:**

- Users searching for the same question immediately find the answer
- The question is visually marked as resolved in the list
- Earns the **Galaxy Brain** achievement for the person whose answer is accepted

**Unmarking an answer:**

Click **Unmark as answer** to remove the designation. Only one answer can be marked at a time - marking a new one automatically unmarks the previous.

---

## Polls

The Polls category lets you create multiple-choice polls to gather community preferences.

**Creating a poll:**

1. Start a new discussion
2. Select the **Polls** category
3. A poll builder appears below the body
4. Add your question as the title
5. Add 2-8 answer options
6. Post

Community members vote by clicking an option. Results show as a percentage bar in real time. Voting is public - you can see who voted for what.

**Use polls for:**

- Choosing between design options
- Gauging interest in a potential feature
- Deciding on naming conventions
- Community preference on breaking changes

---

## Announcements

The Announcements category is special - only repository maintainers and organisation owners can create new announcement discussions. Everyone can comment.

This ensures the Announcements feed is authoritative and signal-to-noise is high. Users who watch a repository get notified of new announcements.

**Common uses:**

- New release announcements
- Breaking change warnings
- Deprecation notices
- Community events or meetings
- Policy changes

---

## Upvoting and Reactions

**Upvoting discussions:**

Click the upward arrow at the top left of any discussion to upvote it. The discussion list can be sorted by most upvoted, helping maintainers see what the community cares most about.

**Reactions:**

Comment on any discussion with emoji reactions (👍 👎 😄 🎉 😕 ❤️ 🚀 👀). Same as issues and pull requests.

**Why upvoting matters for contributors:**

Highly upvoted discussions signal to maintainers which features and issues the community most wants addressed. Upvoting an existing idea is better than creating a duplicate discussion - it concentrates the signal.

---

## Pinning Discussions

Pin important discussions to keep them visible at the top of the Discussions tab.

**Two types of pinning:**

- **Global pins:** Up to 4 discussions pinned at the top of all categories
- **Category pins:** Up to 4 discussions pinned at the top within a specific category

**How to pin:**

Open a discussion → three-dot menu → **Pin discussion** → choose global or category.

**What to pin:**

- Getting started / contributing guide
- Roadmap or upcoming releases
- Community guidelines
- Frequently asked questions discussion
- Current active poll

---

## Converting Issues to Discussions

Sometimes an issue turns out to be a question or a discussion rather than a bug or feature request. You can convert it.

**How to convert:**

Open the issue → right sidebar → **Convert to discussion** → select the target category → **I understand, convert this issue to a discussion**.

**What happens:**

- The issue is converted and closed
- All comments and timeline events are preserved
- The issue number becomes the discussion number (same URL structure)
- Labels and assignees are removed (discussions do not have these)
- GitHub creates a redirect from the issue URL to the new discussion

This cannot be undone - once converted, a discussion cannot be converted back to an issue.

---

## Converting Discussions to Issues

Once a discussion has reached a conclusion and there is a clear action to take, convert it to an issue to track the work.

**How to convert:**

Open the discussion → three-dot menu at the top of the post → **Create issue from discussion**.

GitHub creates a new issue with the discussion's title and body, and adds a comment to the discussion linking to the new issue.

**The discussion remains open** after conversion - the conversation can continue while the work is tracked in the issue.

---

## Organisation-Level Discussions

Organisations can have a Discussions space that spans all repositories - not tied to a single repo.

**Enable organisation Discussions:**

One repository in the organisation is designated as the source for organisation Discussions. Go to the organisation Settings → Member privileges → Enable Discussions → select the source repository.

Organisation discussions appear at `github.com/orgs/YOUR_ORG/discussions`.

**Use cases:**

- Company-wide engineering announcements
- Cross-team Q&A
- Organisation-wide polls
- RFC (Request for Comments) processes

---

## Discussion Templates

Like issue templates, discussion categories can have templates that pre-fill the post body.

**Create a discussion template:**

Create `.github/DISCUSSION_TEMPLATE/` directory. Add `.md` or `.yml` files for each category.

**Example `bug_report.yml` for a Q&A category:**

```yaml
title: "[Question]: "
labels: ["question"]
body:
  - type: markdown
    attributes:
      value: |
        Before asking, please search existing discussions.

  - type: textarea
    id: question
    attributes:
      label: Your question
      description: What are you trying to do?
    validations:
      required: true

  - type: textarea
    id: context
    attributes:
      label: What have you tried?
```

---

## Locking and Managing Discussions

**Locking a discussion:**

Prevents non-maintainer comments. Used for: resolved discussions where comments are no longer relevant, off-topic threads, discussions that have become heated.

Open discussion → three-dot menu → **Lock conversation** → select reason.

**Closing discussions:**

Discussions can be closed without deleting them. Closed discussions remain visible but show as resolved. Use for Q&A discussions once an answer is marked, or for polls once voting is complete.

**Transferring discussions:**

Move a discussion to another repository in the same account or organisation: three-dot menu → **Transfer discussion**.

**Deleting discussions:**

Three-dot menu → **Delete discussion**. This permanently removes the discussion and all its comments. There is no undo. Delete only spam or content that violates community guidelines.

---

## Try It Yourself

**Step 1.** Enable Discussions on one of your repositories:

Settings → General → Features → check **Discussions** → Save.

**Step 2.** Create a Q&A discussion:

- Click **Discussions** tab → **New discussion**
- Category: **Q&A**
- Title: `How do I contribute to this project?`
- Body: Write a question about how someone might contribute to your project

**Step 3.** Reply and mark your own answer:

- Post a comment answering the question
- Click **Mark as answer** on the comment

**Step 4.** Create a poll:

- **New discussion** → Category: **Polls**
- Title: `Which feature should we build next?`
- Add 3 options
- Vote on it

**Step 5.** Create an Announcement:

- **New discussion** → Category: **Announcements**
- Title: `Welcome to the project!`
- Body: Write a short welcome message

**Step 6.** Pin the announcement:

Open it → three-dot menu → **Pin discussion** → pin globally.

**Step 7.** Check your **Galaxy Brain** achievement progress:

Go to `github.com/YOUR_USERNAME` → scroll down to the Achievements section. Getting an answer marked earns you Galaxy Brain at the default tier.

---

## Common Mistakes

**Using Discussions for bug reports.**

Bug reports belong in Issues. Discussions do not have labels, assignees or the ability to be tracked in Projects. A bug report as a Discussion is hard to action and easy to lose. If a user posts a bug as a Discussion, convert it to an issue.

**Not enabling Discussions on active open source projects.**

Without Discussions, users post questions as issues. This clutters the issue tracker with non-actionable items. Enable Discussions early so the community has the right place to ask.

**Ignoring upvoted Discussions.**

Highly upvoted ideas tell you what users want most. Regularly review top-voted Discussions to inform your roadmap. If an idea is popular but not planned, acknowledge it in the discussion.

**Not pinning the getting started/contributing guide.**

New contributors and users always have the same first questions. Pinning a comprehensive "Getting started" discussion saves everyone time and reduces repeat questions.

**Creating a new discussion when one already exists.**

Before posting, search existing discussions. Duplicate discussions fragment the community's responses and make it harder to find definitive answers. Upvote the existing discussion instead.

---

## Summary

- GitHub Discussions is a forum built into GitHub for open-ended conversations, Q&A, announcements, polls and community building
- Enable in Settings → General → Features → Discussions
- Default categories: Announcements (maintainers only), General, Ideas, Polls, Q&A, Show and tell - up to 25 custom categories
- Q&A discussions support marking one comment as the accepted answer - earns the Galaxy Brain achievement
- Polls support 2-8 options with real-time public voting results
- Pin up to 4 globally and 4 per category
- Convert issues to discussions (irreversible) or discussions to issues (keeps both)
- Organisation-level discussions span all repositories
- Use Discussions for questions, brainstorming and community - use Issues for concrete, actionable work

---

## Sources and Further Reading

- [About GitHub Discussions](https://docs.github.com/en/discussions/collaborating-with-your-community-using-discussions/about-discussions) - official overview
- [Quickstart for GitHub Discussions](https://docs.github.com/en/discussions/quickstart) - step-by-step setup guide
- [Best practices for GitHub Discussions](https://docs.github.com/en/discussions/guides/best-practices-for-community-conversations-on-github) - guidance from GitHub on effective community use
- [Discussion templates](https://docs.github.com/en/discussions/managing-discussions-for-your-community/creating-discussion-category-forms) - creating YAML-based discussion forms

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

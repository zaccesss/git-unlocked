# Merge Conflicts

**Difficulty:** 🟡 Intermediate | **Time:** 25 minutes

Merge conflicts are the thing beginners fear most about Git. They look alarming the first time you see them. In reality, they are just Git asking you a question: "Two people changed the same thing differently - which version do you want?" Once you know how to read and resolve them, conflicts stop being scary and become a normal part of collaborative development.

---

## Table of Contents

- [What causes a merge conflict?](#what-causes-a-merge-conflict)
- [What does a conflict look like?](#what-does-a-conflict-look-like)
- [The conflict resolution workflow](#the-conflict-resolution-workflow)
- [Resolving conflicts in the terminal](#resolving-conflicts-in-the-terminal)
- [Resolving conflicts in VS Code](#resolving-conflicts-in-vs-code)
- [Resolving conflicts in JetBrains IDEs](#resolving-conflicts-in-jetbrains-ides)
- [Resolving conflicts in Visual Studio 2026](#resolving-conflicts-in-visual-studio-2026)
- [Resolving conflicts in Xcode 26](#resolving-conflicts-in-xcode-26)
- [Aborting a conflicted merge](#aborting-a-conflicted-merge)
- [Preventing conflicts](#preventing-conflicts)
- [Understanding conflict types](#understanding-conflict-types)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What Causes a Merge Conflict?

A conflict occurs when Git cannot automatically decide which version of a change to keep. This happens when two branches have modified the **same lines** of the **same file** in different ways since they last shared a common ancestor.

Git can handle many situations automatically:

- Two branches modified **different files** - no conflict, Git merges both
- Two branches modified **different parts** of the same file - no conflict, Git combines both sets of changes
- One branch **added** a file, the other did not touch it - no conflict

Git cannot handle:

- Two branches modified the **same lines** of the same file differently
- One branch **deleted** a file that the other branch modified
- Two branches both **added** a file with the same name but different content

In all of these cases Git stops and asks you to decide.

---

## What Does a Conflict Look Like?

When Git encounters a conflict during a merge, it pauses and marks the conflicting sections directly inside the affected file using conflict markers.

Here is what a conflicted file looks like:

```
# Contact Page

<<<<<<< HEAD
Email us at: hello@example.com
=======
Email us at: support@example.com
>>>>>>> feature/update-contact
```

Breaking down the markers:

**`<<<<<<< HEAD`** - everything between this line and `=======` is the version from the branch you are merging **into** (your current branch, referred to as HEAD).

**`=======`** - the divider between the two versions.

**`>>>>>>> feature/update-contact`** - everything between `=======` and this line is the version from the branch you are merging **in** (the source branch).

Your job is to edit the file so it contains exactly what you want, then remove all three marker lines.

---

## The Conflict Resolution Workflow

Regardless of which tool you use to resolve conflicts, the process is always the same:

**Step 1.** Start the merge:

```bash
git merge feature/branch-name
```

**Step 2.** Git reports conflicts:

```
Auto-merging contact.md
CONFLICT (content): Merge conflict in contact.md
Automatic merge failed; fix conflicts and then commit the result.
```

**Step 3.** Find all conflicted files:

```bash
git status
```

Conflicted files appear under "Unmerged paths" with the label `both modified`.

**Step 4.** Open each conflicted file and resolve it.

For each conflict, choose one of:
- Keep the current branch version (everything between `<<<<<<< HEAD` and `=======`)
- Keep the incoming branch version (everything between `=======` and `>>>>>>>`)
- Write a completely new version that combines or replaces both
- Delete the content entirely if that is the right outcome

Then remove all three marker lines (`<<<<<<<`, `=======`, `>>>>>>>`).

**Step 5.** Mark each file as resolved by staging it:

```bash
git add contact.md
```

**Step 6.** Once all conflicts are resolved and staged, complete the merge:

```bash
git commit
```

Git opens your editor with a pre-filled merge commit message. Save and close to complete the merge.

> [!WARNING]
> If you stage a file that still contains conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`), Git will commit those markers as part of the file content. Always double-check that every marker has been removed before staging.

---

## Resolving Conflicts in the Terminal

For small conflicts, editing directly in the terminal with a text editor like Nano is fast and effective.

**Open the conflicted file in Nano:**

```bash
nano contact.md
```

Find the conflict markers, edit the file to contain the correct final version, remove all markers, save with `Ctrl + O`, exit with `Ctrl + X`.

**View conflicts with Git's built-in merge tool:**

```bash
git mergetool
```

This opens the configured merge tool. If none is configured, Git uses its built-in tool which shows the two versions side by side in the terminal. Set a specific merge tool with:

```bash
git config --global merge.tool vimdiff
```

Other options include `kdiff3`, `meld` and `opendiff` (Mac). Most developers find IDE-based conflict resolution more comfortable than terminal tools.

**Check for remaining conflict markers after editing:**

```bash
grep -r "<<<<<<" .
```

If this returns nothing, all conflicts have been resolved.

---

## Resolving Conflicts in VS Code

VS Code has one of the best conflict resolution experiences of any editor. When you open a file with conflicts, VS Code detects the markers and shows a visual interface above each conflict section.

### What you see

Each conflict section shows four clickable options above it:

- **Accept Current Change** - keeps the HEAD version
- **Accept Incoming Change** - keeps the source branch version
- **Accept Both Changes** - keeps both versions, one after the other
- **Compare Changes** - opens a side-by-side diff

Click the option that gives you the correct result. VS Code removes the markers automatically.

For more complex conflicts where you need to write a custom resolution, click into the file and edit it directly.

### The merge editor (VS Code 1.69+)

For a more powerful view, click **Resolve in Merge Editor** at the bottom of the conflicted file. This opens a three-panel view:

- **Left panel** - your current branch version
- **Right panel** - the incoming branch version
- **Bottom panel** - the result (editable directly)

You can accept changes from either side using the checkboxes, or type freely in the result panel.

### Completing the merge in VS Code

After resolving all conflicts, the Source Control panel shows the resolved files as staged. Either click the commit button in VS Code or run `git commit` in the terminal.

---

## Resolving Conflicts in JetBrains IDEs

JetBrains IDEs (IntelliJ IDEA, PyCharm, WebStorm, CLion, Rider and all others) have a powerful three-panel merge tool.

**Opening the conflict resolver:**

When a merge conflict occurs, a notification appears. Click **Resolve** or go to **Git -> Resolve Conflicts**. A list of conflicted files appears - double-click any file to open the merge tool.

**The three-panel view:**

- **Left panel (Your version)** - the current branch version
- **Right panel (Their version)** - the incoming branch version
- **Centre panel (Result)** - the final merged result, editable

Use the `>>` and `<<` arrows to accept individual changes from either side into the centre panel. Edit the centre panel directly for custom resolutions. Lines shown in blue are non-conflicting changes that will be automatically accepted. Lines shown in red are conflicting changes that need your input.

Click **Apply** when done. The IDE stages the resolved file automatically.

---

## Resolving Conflicts in Visual Studio 2026

Visual Studio 2026 detects conflicts when you merge using the Git Changes window.

**Opening the conflict resolver:**

In the **Git Changes** window, conflicted files appear with a warning icon. Click **Resolve conflicts** to see the list. Double-click a file to open the inline merge editor.

**The merge editor:**

Visual Studio shows a side-by-side comparison with your version on the left and the incoming version on the right. Tick the checkboxes next to changes to include them in the result. The merged result previews at the bottom. Click **Accept Merge** when satisfied.

---

## Resolving Conflicts in Xcode 26

When a merge conflict occurs in Xcode 26, the Source Control Navigator shows conflicted files.

**Opening the conflict resolver:**

Click a conflicted file in the Source Control Navigator. Xcode shows the conflict inline in the editor with **Current Change** and **Incoming Change** sections highlighted in different colours.

Use the buttons above each section to accept the current change, accept the incoming change or keep both. Edit the file directly for custom resolutions.

---

## Aborting a Conflicted Merge

If you started a merge, encountered conflicts and decide you do not want to proceed:

```bash
git merge --abort
```

This completely cancels the merge and restores your repository to exactly the state it was in before you ran `git merge`. All conflict markers are removed and your working directory is restored.

You can run `git merge --abort` at any point before you run `git commit` to complete the merge.

---

## Preventing Conflicts

Conflicts cannot be eliminated entirely in a collaborative project, but you can significantly reduce how often they occur and how painful they are when they do.

**Pull frequently.** The longer you go without pulling the latest changes from the remote, the more likely your branch has diverged significantly from the target. Pull `main` often and merge or rebase it into your feature branch regularly.

**Keep branches short-lived.** A feature branch that exists for two weeks accumulates more divergence than one that exists for two days. Smaller, focused branches merged quickly cause fewer conflicts.

**Communicate with your team.** If you know a colleague is heavily modifying a specific file, coordinate to avoid working on the same lines simultaneously.

**Use a `.gitattributes` file for line endings.** Line ending differences between Windows and Unix systems can create spurious conflicts. A properly configured `.gitattributes` prevents this.

**Split large files.** A single enormous file that many people modify is a conflict magnet. Breaking it into smaller focused files reduces the surface area for conflicts.

**Review before merging.** Pull requests exist partly to identify conflicts before they reach `main`. A review process catches problems early.

---

## Understanding Conflict Types

### Content conflict

The most common type. Two branches changed the same lines of the same file.

```
<<<<<<< HEAD
The price is £9.99 per month.
=======
The price is £12.99 per month.
>>>>>>> feature/pricing-update
```

Resolution: decide which price is correct, or write a new line entirely.

### Delete/modify conflict

One branch deleted a file while the other branch modified it.

```
CONFLICT (modify/delete): pricing.md deleted in feature/remove-pricing
and modified in HEAD. Version HEAD of pricing.md left in tree.
```

Git leaves the modified version in your working directory. You decide whether to keep the file (stage it) or delete it (`git rm pricing.md`).

### Add/add conflict

Both branches added a file with the same name but different content.

```
CONFLICT (add/add): Merge conflict in config.yml
```

Git marks the file with conflict markers showing both versions. Resolve as a normal content conflict.

### Rename conflict

One branch renamed a file while the other modified it under the original name. Git usually handles this automatically but sometimes needs guidance.

---

## Try It Yourself

This exercise intentionally creates a merge conflict so you can practise resolving it.

**Step 1.** Set up a repository:

**Windows (PowerShell):**

```powershell
mkdir conflict-practice && cd conflict-practice && git init
echo "Contact: hello@example.com" > contact.txt
git add contact.txt && git commit -m "add: contact file"
```

**Mac and Linux:**

```bash
mkdir conflict-practice && cd conflict-practice && git init
echo "Contact: hello@example.com" > contact.txt
git add contact.txt && git commit -m "add: contact file"
```

**Step 2.** Create a feature branch and change the contact line:

```bash
git switch -c feature/update-contact
```

**Windows:**

```powershell
echo "Contact: support@example.com" > contact.txt
git add contact.txt && git commit -m "update: change contact to support address"
```

**Mac and Linux:**

```bash
echo "Contact: support@example.com" > contact.txt
git add contact.txt && git commit -m "update: change contact to support address"
```

**Step 3.** Switch to main and make a different change to the same line:

```bash
git switch main
```

**Windows:**

```powershell
echo "Contact: admin@example.com" > contact.txt
git add contact.txt && git commit -m "update: change contact to admin address"
```

**Mac and Linux:**

```bash
echo "Contact: admin@example.com" > contact.txt
git add contact.txt && git commit -m "update: change contact to admin address"
```

**Step 4.** Attempt the merge - this will conflict:

```bash
git merge feature/update-contact
```

You should see:

```
Auto-merging contact.txt
CONFLICT (content): Merge conflict in contact.txt
Automatic merge failed; fix conflicts and then commit the result.
```

**Step 5.** Check the status:

```bash
git status
```

**Step 6.** Look at the conflict:

```bash
cat contact.txt
```

You will see the conflict markers showing both versions.

**Step 7.** Resolve the conflict by choosing the support address:

**Windows:**

```powershell
echo "Contact: support@example.com" > contact.txt
```

**Mac and Linux:**

```bash
echo "Contact: support@example.com" > contact.txt
```

**Step 8.** Stage the resolved file and complete the merge:

```bash
git add contact.txt
git commit -m "merge: resolve contact address conflict"
```

**Step 9.** View the result:

```bash
git log --oneline --graph --all
```

You should see the merge commit with two parent branches.

**Step 10.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q conflict-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf conflict-practice
```

You have just created and resolved a real merge conflict.

---

## Common Mistakes

**Committing files that still contain conflict markers.**

If you forget to remove the `<<<<<<<`, `=======` and `>>>>>>>` lines before staging and committing, those markers end up in your file as literal text content. Always check for remaining markers before committing. Run `grep -r "<<<<<<" .` to find any that were missed.

**Using `git add .` before checking all conflicts are resolved.**

If you have multiple conflicted files and only resolved some of them, running `git add .` stages everything - including files that still have unresolved markers. Check each file individually or use `git status` to confirm all conflicts show as resolved before staging.

**Panicking and running `git merge --abort` every time.**

Conflicts are normal. Aborting every time means the work never gets merged. Take a breath, open the conflicted file, read both versions and make a decision. It is almost always straightforward.

**Keeping both changes blindly with "Accept Both".**

In VS Code's conflict resolution, "Accept Both Changes" adds both versions one after the other. This is sometimes what you want (for example, two people added different items to a list) but often results in duplicate or contradictory content. Always read the result before committing.

**Not pulling before starting a merge.**

If `main` has new commits on the remote that you do not have locally, your merge will be based on outdated code. Pull the target branch first.

**Running `git commit -m` instead of letting Git open the editor for the merge message.**

When completing a merge with `git commit`, you can let Git open your editor and use the auto-generated merge message, or pass `-m` with your own message. Both are fine, but running `git commit -m` overwrites the pre-filled message which includes useful information about which branch was merged.

---

## Summary

- A conflict occurs when two branches modified the same lines of the same file in different ways
- Git marks conflicting sections with `<<<<<<< HEAD`, `=======` and `>>>>>>> branch-name`
- The resolution workflow is: find conflicts with `git status`, edit each file to the correct final state, remove all markers, stage with `git add`, then `git commit`
- VS Code, JetBrains, Visual Studio and Xcode all have built-in visual conflict resolution tools
- Abort a conflicted merge at any point with `git merge --abort`
- Reduce conflicts by pulling frequently, keeping branches short-lived and communicating with your team
- Common conflict types: content conflict, delete/modify conflict and add/add conflict

---

## Sources and Further Reading

- [Official git merge documentation](https://git-scm.com/docs/git-merge) - complete reference including all conflict resolution options
- [Pro Git book, Chapter 3.2: Basic Branching and Merging](https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging) - the official guide to merge conflicts with examples
- [Atlassian - Git merge conflicts](https://www.atlassian.com/git/tutorials/using-branches/merge-conflicts) - clear visual walkthrough of how conflicts arise and how to resolve them
- [VS Code merge conflict documentation](https://code.visualstudio.com/docs/sourcecontrol/overview#_merge-conflicts) - official VS Code guide to resolving conflicts visually
- [Oh Shit, Git!?!](https://ohshitgit.com) - practical recovery steps for when merges go badly wrong

---

<div align="center">

Made with 🔓 by [Isaac Adjei (Zaccess)](https://zacess.com)

**Access Granted. Success Unlocked.**

</div>

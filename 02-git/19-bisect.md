# Bisect

**Difficulty:** 🔴 Advanced | **Time:** 20 minutes

`git bisect` is one of Git's most powerful debugging tools. It uses binary search to find exactly which commit introduced a bug - narrowing down potentially hundreds of commits to the single one responsible, in just a handful of steps. Once you know how to use it, you will reach for it every time you need to find when something broke.

---

## Table of Contents

- [What is git bisect?](#what-is-git-bisect)
- [How binary search works](#how-binary-search-works)
- [Basic bisect workflow](#basic-bisect-workflow)
- [Automated bisect](#automated-bisect)
- [Bisect with a script](#bisect-with-a-script)
- [Bisect terms and commands](#bisect-terms-and-commands)
- [Skipping commits](#skipping-commits)
- [Viewing the bisect log](#viewing-the-bisect-log)
- [Resetting after bisect](#resetting-after-bisect)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is git bisect?

`git bisect` helps you find the commit that introduced a bug by performing a binary search through your commit history. You tell Git two things:

1. A **bad** commit - one where the bug exists (usually the current HEAD)
2. A **good** commit - one where the bug did not exist (a known working version)

Git then checks out the commit halfway between them. You test whether the bug is present. You tell Git whether that commit is good or bad. Git halves the remaining range and checks out the middle commit again. You repeat until Git identifies the exact commit that introduced the problem.

This is enormously efficient. If you have 1,000 commits between your good and bad points, bisect finds the culprit in approximately 10 steps (log₂ of 1,000). Manual searching through the same history could take hours.

---

## How Binary Search Works

Binary search works by repeatedly halving the search space.

```
1000 commits to search

Step 1: check commit 500    -> bad  -> problem is in 1-500
Step 2: check commit 250    -> good -> problem is in 251-500
Step 3: check commit 375    -> bad  -> problem is in 251-375
Step 4: check commit 313    -> good -> problem is in 314-375
Step 5: check commit 344    -> bad  -> problem is in 314-344
Step 6: check commit 329    -> good -> problem is in 330-344
Step 7: check commit 337    -> bad  -> problem is in 330-337
Step 8: check commit 333    -> good -> problem is in 334-337
Step 9: check commit 335    -> bad  -> problem is in 334-335
Step 10: check commit 334   -> good -> commit 335 is the first bad commit
```

Ten steps to find the culprit in 1,000 commits. Without bisect, you might check commits one by one or rely on intuition.

---

## Basic Bisect Workflow

**Step 1.** Start the bisect session:

```bash
git bisect start
```

**Step 2.** Mark the current commit as bad (the bug exists here):

```bash
git bisect bad
```

Or specify a specific bad commit:

```bash
git bisect bad HEAD
```

**Step 3.** Mark a known good commit (where the bug did not exist):

```bash
git bisect good v2.0.0
```

Or use a commit hash:

```bash
git bisect good a1b2c3d
```

Git immediately checks out the midpoint commit and tells you how many steps remain:

```
Bisecting: 142 revisions left to test after this (roughly 7 steps)
[e4f5g6h] update: refactor payment processing
```

**Step 4.** Test whether the bug is present at the checked-out commit. Run your application, run a test, or do whatever is needed to determine if the bug exists.

**Step 5.** Tell Git the result:

If the bug is present:
```bash
git bisect bad
```

If the bug is absent:
```bash
git bisect good
```

**Step 6.** Repeat steps 4 and 5. Git checks out a new midpoint each time and narrows the range.

**Step 7.** When Git has found the culprit, it outputs:

```
a1b2c3d4e5f6 is the first bad commit
commit a1b2c3d4e5f6
Author: Your Name <your@email.com>
Date:   Mon Mar 20 14:23:11 2026 +0000

    update: refactor payment redirect logic

 payment.js | 12 ++++++------
 1 file changed, 6 insertions(+), 6 deletions(-)
```

You now know exactly which commit introduced the bug.

**Step 8.** End the bisect session and return to your original branch:

```bash
git bisect reset
```

This is essential - do not forget it. Without it, you remain on whatever commit bisect last checked out.

---

## Automated Bisect

If you can write a script that returns exit code 0 for good and non-zero for bad, Git can run bisect entirely automatically.

```bash
git bisect start
git bisect bad HEAD
git bisect good v2.0.0
git bisect run ./test-script.sh
```

Git runs `./test-script.sh` at each midpoint, interprets the exit code and advances the bisect without any manual input. When it is finished it reports the first bad commit.

**Exit codes:**

- `0` - good (bug not present)
- `1-127` (except 125) - bad (bug present)
- `125` - skip this commit (cannot be tested)
- `128+` - abort the bisect entirely

---

## Bisect with a Script

The automated approach works best with a focused test script. Here are examples:

**Test if a specific function exists in a file:**

```bash
#!/bin/bash
grep -q "function validatePayment" payment.js
exit $?
```

If `validatePayment` exists, `grep` exits 0 (good). If it does not exist, `grep` exits 1 (bad). This finds the commit that removed the function.

**Run a specific test:**

```bash
#!/bin/bash
npm test -- --testNamePattern="payment redirect"
exit $?
```

**Check if a compiled binary has a specific behaviour:**

```bash
#!/bin/bash
make > /dev/null 2>&1
if [ $? -ne 0 ]; then
    exit 125  # skip this commit, it does not compile
fi
./myapp --check-payment > /dev/null 2>&1
exit $?
```

The `exit 125` when the build fails tells bisect to skip that commit rather than marking it as bad or good.

---

## Bisect Terms and Commands

| Command | Meaning |
|---|---|
| `git bisect start` | Begin a bisect session |
| `git bisect bad [commit]` | Mark a commit as bad (bug present). Defaults to HEAD |
| `git bisect good [commit]` | Mark a commit as good (bug absent) |
| `git bisect run <script>` | Run automated bisect using a script |
| `git bisect skip [commit]` | Skip a commit that cannot be tested |
| `git bisect reset` | End the session and return to original HEAD |
| `git bisect log` | Show the bisect history so far |
| `git bisect replay <file>` | Re-run a bisect from a saved log |
| `git bisect visualize` | Open gitk to visualise the remaining range |

---

## Skipping Commits

Sometimes a commit cannot be tested - the code does not compile, a dependency is missing or the test is not applicable. Skip it with:

```bash
git bisect skip
```

Or skip a specific commit:

```bash
git bisect skip a1b2c3d
```

Git marks the commit as untestable and checks out a neighbouring one instead. If the only commits remaining are all skipped, Git reports that it cannot determine the exact first bad commit but narrows it to a small range.

---

## Viewing the Bisect Log

```bash
git bisect log
```

This shows every good/bad decision made so far in the current session. Useful for reviewing your progress or sharing with a colleague.

**Save the log to a file:**

```bash
git bisect log > bisect.log
```

**Replay a saved bisect session:**

```bash
git bisect replay bisect.log
```

This is useful for resuming an interrupted bisect or sharing the session with another developer.

---

## Resetting After Bisect

Always end a bisect session with:

```bash
git bisect reset
```

This checks out the branch you were on before starting bisect and clears all the bisect state. If you close your terminal without resetting, Git leaves the repository in the bisect state and you will see warnings on your next command.

If you get stuck in a bisect state you did not start, `git bisect reset` still clears it.

---

## Try It Yourself

This exercise creates a history with a known bug introduced at a specific commit and uses bisect to find it.

**Step 1.** Create a repository with a simulated bug history:

**Windows (PowerShell):**

```powershell
mkdir bisect-practice && cd bisect-practice && git init
echo "result=correct" > output.txt
git add output.txt && git commit -m "add: initial output - correct"
echo "result=correct" > output.txt
git add output.txt && git commit -m "update: minor change 1"
echo "result=correct" > output.txt
git add output.txt && git commit -m "update: minor change 2"
echo "result=BROKEN" > output.txt
git add output.txt && git commit -m "update: refactor output logic"
echo "result=BROKEN" > output.txt
git add output.txt && git commit -m "update: minor change 4"
echo "result=BROKEN" > output.txt
git add output.txt && git commit -m "update: minor change 5"
```

**Mac and Linux:**

```bash
mkdir bisect-practice && cd bisect-practice && git init
echo "result=correct" > output.txt && git add output.txt && git commit -m "add: initial output - correct"
echo "result=correct" > output.txt && git add output.txt && git commit -m "update: minor change 1"
echo "result=correct" > output.txt && git add output.txt && git commit -m "update: minor change 2"
echo "result=BROKEN" > output.txt && git add output.txt && git commit -m "update: refactor output logic"
echo "result=BROKEN" > output.txt && git add output.txt && git commit -m "update: minor change 4"
echo "result=BROKEN" > output.txt && git add output.txt && git commit -m "update: minor change 5"
```

**Step 2.** View the log to understand the history:

```bash
git log --oneline
```

**Step 3.** Start bisect - current HEAD is bad:

```bash
git bisect start
git bisect bad
```

**Step 4.** The first commit is good - find its hash and mark it:

```bash
git log --oneline | tail -1
```

Copy the hash and mark it good:

```bash
git bisect good [first commit hash]
```

**Step 5.** At each step, check whether output.txt contains "BROKEN":

```bash
cat output.txt
```

If it shows `result=BROKEN`, type `git bisect bad`. If it shows `result=correct`, type `git bisect good`.

**Step 6.** Continue until Git identifies the first bad commit. It should identify the "refactor output logic" commit.

**Step 7.** Reset:

```bash
git bisect reset
```

**Step 8.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q bisect-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf bisect-practice
```

---

## Common Mistakes

**Forgetting `git bisect reset` at the end.**

If you do not reset, you are left on whatever commit bisect last checked out, which is not your branch. Run `git bisect reset` when finished.

**Starting bisect without a clear good/bad definition.**

Bisect only works if your test is deterministic - the same commit always produces the same result. Intermittent bugs (race conditions, timing issues) are very difficult to bisect because the same commit might test as both good and bad.

**Not using `git bisect skip` for untestable commits.**

If a commit cannot be compiled or tested, mark it with `git bisect skip` rather than guessing good or bad. Guessing incorrectly can send bisect in the wrong direction and report the wrong culprit.

**Not knowing what the "bug" is before starting.**

Define your test precisely before starting bisect. "Something is wrong" is not a good bisect criterion. "The payment redirect goes to the wrong URL" is testable and specific.

**Making changes during bisect.**

Do not commit or modify files during a bisect session unless your test script requires it. Changes during bisect can confuse the session.

---

## Summary

- `git bisect` uses binary search to find the commit that introduced a bug in O(log n) steps
- Start with `git bisect start`, mark a bad commit with `git bisect bad` and a good commit with `git bisect good`
- At each step Git checks out the midpoint commit - test it and mark it good or bad
- `git bisect run <script>` automates the process using a script that returns 0 for good and non-zero for bad
- `git bisect skip` skips commits that cannot be tested
- Always end with `git bisect reset` to return to your original branch
- `git bisect log` shows the session history; `git bisect replay` can restore a session from a saved log

---

## Sources and Further Reading

- [Official git bisect documentation](https://git-scm.com/docs/git-bisect) - the complete reference including automated bisect
- [Pro Git book, Chapter 7.10: Debugging with Git](https://git-scm.com/book/en/v2/Git-Tools-Debugging-with-Git) - the official guide to bisect and git blame
- [Atlassian - Git bisect](https://www.atlassian.com/git/tutorials/git-bisect) - practical examples and use cases

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

# .gitattributes

**Difficulty:** 🟡 Intermediate | **Time:** 20 minutes

`.gitattributes` is a file that tells Git how to handle specific files in your repository - how to treat line endings, how to display diffs, how to merge, what to include in exports and more. While `.gitignore` tells Git which files to ignore, `.gitattributes` tells Git how to treat the files it does track.

Getting `.gitattributes` right prevents one of the most common sources of spurious conflicts in cross-platform teams: line ending differences between Windows and Unix systems.

---

## Table of Contents

- [What is .gitattributes?](#what-is-gitattributes)
- [Where it lives and how it works](#where-it-lives-and-how-it-works)
- [Line ending normalisation](#line-ending-normalisation)
- [Diff attributes](#diff-attributes)
- [Merge attributes](#merge-attributes)
- [Export attributes](#export-attributes)
- [Linguist attributes](#linguist-attributes)
- [Git LFS attributes](#git-lfs-attributes)
- [A complete .gitattributes template](#a-complete-gitattributes-template)
- [Try It Yourself](#try-it-yourself)
- [Common Mistakes](#common-mistakes)
- [Summary](#summary)
- [Sources and Further Reading](#sources-and-further-reading)

---

## What is .gitattributes?

When Git stores a file, it can optionally transform it - normalising line endings, applying filters or marking it as binary. When Git displays a diff, it can use custom drivers to produce more readable output. When Git merges, it can use custom strategies for specific file types.

All of this behaviour is controlled by `.gitattributes`.

The most important use case for most teams is **line ending normalisation** - ensuring that files always have consistent line endings in the repository regardless of which operating system contributors use. Without this, Windows users (who use CRLF line endings) and Mac/Linux users (who use LF) will constantly create commits that show thousands of changed lines when nothing actually changed in the code.

---

## Where it Lives and How it Works

`.gitattributes` lives in the root of your repository and is committed like any other file. You can also place `.gitattributes` files in subdirectories - they apply to files in that directory and its subdirectories.

A global attributes file can be configured for rules that apply to all repositories on your machine:

```bash
git config --global core.attributesFile ~/.gitattributes_global
```

**Syntax:**

Each line in `.gitattributes` is a pattern followed by one or more attributes:

```
pattern  attribute1  attribute2  attribute3
```

Patterns use the same glob syntax as `.gitignore`. Later rules override earlier ones.

**Attribute values:**

- `attribute` - set the attribute to true
- `-attribute` - set the attribute to false (unset)
- `attribute=value` - set the attribute to a specific value
- `!attribute` - unspecified (let Git use its default)

---

## Line Ending Normalisation

Line endings are the single most important thing `.gitattributes` controls for cross-platform teams.

**The problem:**

- Windows uses **CRLF** (`\r\n`) line endings
- Mac and Linux use **LF** (`\n`) line endings
- Without normalisation, a file edited on Windows will show every line as changed when viewed on Linux, even if the actual content did not change

**The solution - the `text` and `eol` attributes:**

`text` tells Git to normalise line endings for this file. Git stores files with LF in the repository and converts to the platform's native endings on checkout.

`eol=lf` forces LF line endings on checkout (used for shell scripts, which break with CRLF).

`eol=crlf` forces CRLF on checkout (used for files that must have Windows line endings, such as some `.bat` files).

`binary` tells Git the file is binary - no line ending conversion, no diff, no merge. Equivalent to setting `-text -diff`.

**Recommended setup for most projects:**

```gitattributes
# Auto-detect text files and normalise line endings
* text=auto

# Explicitly declare text files
*.md text
*.txt text
*.html text
*.css text
*.js text
*.ts text
*.jsx text
*.tsx text
*.json text
*.xml text
*.yml text
*.yaml text
*.toml text
*.sh text eol=lf
*.bash text eol=lf
*.py text
*.rb text
*.go text
*.rs text
*.java text
*.kt text
*.c text
*.h text
*.cpp text
*.cs text

# Windows scripts need CRLF
*.bat text eol=crlf
*.cmd text eol=crlf
*.ps1 text eol=crlf

# Declare binary files
*.png binary
*.jpg binary
*.jpeg binary
*.gif binary
*.ico binary
*.pdf binary
*.zip binary
*.tar.gz binary
*.mp4 binary
*.mp3 binary
*.woff binary
*.woff2 binary
*.ttf binary
*.otf binary
*.eot binary
*.exe binary
*.dll binary
*.so binary
*.dylib binary
```

> [!WARNING]
> After adding or changing `.gitattributes`, existing files in your working directory may not be updated immediately. To re-normalise all files, run:

**Windows (PowerShell):**

```powershell
git add --renormalize .
git commit -m "chore: normalise line endings"
```

**Mac and Linux:**

```bash
git add --renormalize .
git commit -m "chore: normalise line endings"
```

This re-stages every file with the new normalisation rules applied without changing any content.

---

## Diff Attributes

The `diff` attribute controls how Git generates diffs for a file. By default, Git shows raw line-by-line diffs. For some file types you can do better.

**Mark a file as binary (suppress diff):**

```gitattributes
*.pdf binary
*.png binary
```

**Use a custom diff driver:**

You can define a diff driver that extracts text from a binary format. For example, to diff Microsoft Word documents:

```gitattributes
*.docx diff=word
```

Then configure the driver in `.git/config` or `~/.gitconfig`:

```ini
[diff "word"]
    textconv = strings
```

`strings` extracts printable text from the binary. You can use more sophisticated tools like `pandoc` for better output.

**Diff of Jupyter notebooks:**

```gitattributes
*.ipynb diff=jupyternotebook
```

Configure:

```ini
[diff "jupyternotebook"]
    textconv = jupyter nbconvert --to script --stdout
```

**Useful built-in diff patterns:**

```gitattributes
# Show function names in diff context for common languages
*.c     diff=cpp
*.h     diff=cpp
*.java  diff=java
*.py    diff=python
*.rb    diff=ruby
*.cs    diff=csharp
*.php   diff=php
```

These use Git's built-in heuristics to show the function or class name at the top of each diff hunk, making diffs significantly easier to understand.

---

## Merge Attributes

The `merge` attribute controls how Git merges a file when conflicts occur.

**`merge=ours` - always keep our version:**

```gitattributes
*.generated.js merge=ours
```

Use this for generated files that should never be merged - just keep the version on the current branch.

**`merge=union` - combine both versions:**

```gitattributes
CHANGELOG.md merge=union
```

Git concatenates both versions instead of marking a conflict. Useful for log-style files where both additions are valid.

**`merge=binary` - treat as binary, no merging:**

```gitattributes
*.xlsx binary
```

Git will not attempt to merge binary files - it keeps one version and marks a conflict if both sides changed.

**`merge=theirs` does not exist as a built-in** - use `ours` in reverse (by checking out from the other side) or use a custom merge driver.

---

## Export Attributes

The `export-ignore` attribute excludes files and directories from `git archive` exports. This is essential for distributing your project as a zip or tarball without including development files.

```gitattributes
# Exclude development files from exports
.github/         export-ignore
.gitignore       export-ignore
.gitattributes   export-ignore
tests/           export-ignore
docs/            export-ignore
*.test.js        export-ignore
Makefile         export-ignore
```

When someone downloads your project as a zip from GitHub or when you run `git archive`, these files and directories are excluded. The result is a clean production package.

`export-subst` substitutes `$Format:` placeholders with commit metadata during export:

```gitattributes
version.txt export-subst
```

Content of `version.txt`:

```
Version: $Format:%h (%cd)$
```

On export, `%h` is replaced with the commit hash and `%cd` with the commit date.

---

## Linguist Attributes

GitHub uses the **Linguist** library to detect languages and generate repository statistics (the language bar at the top of a repository). `.gitattributes` can override its behaviour.

**Mark generated files so they are excluded from statistics:**

```gitattributes
dist/           linguist-generated=true
*.min.js        linguist-generated=true
*.min.css       linguist-generated=true
vendor/         linguist-generated=true
node_modules/   linguist-generated=true
```

Generated files are not counted in the language bar and are collapsed in diffs on GitHub.

**Mark vendored files:**

```gitattributes
vendor/         linguist-vendored=true
third_party/    linguist-vendored=true
```

**Override language detection:**

```gitattributes
*.h linguist-language=C
*.ts linguist-language=TypeScript
```

**Mark documentation files:**

```gitattributes
docs/ linguist-documentation=true
```

**Force a file to be shown in stats:**

```gitattributes
special.proto linguist-detectable=true
```

---

## Git LFS Attributes

Git Large File Storage uses `.gitattributes` to know which files to manage. When you run `git lfs track "*.psd"`, Git LFS automatically adds a line to `.gitattributes`:

```gitattributes
*.psd filter=lfs diff=lfs merge=lfs -text
```

The `filter=lfs` attribute activates the LFS filter driver for those files. Git LFS intercepts checkout and staging operations to download or upload the actual file content from the LFS server.

Full Git LFS coverage is in [22-large-repos.md](22-large-repos.md).

---

## A Complete .gitattributes Template

Here is a well-rounded `.gitattributes` file suitable for most projects:

```gitattributes
# =============================================================
# Default behaviour
# =============================================================

# Auto-detect text files and normalise line endings to LF in repo
* text=auto eol=lf

# =============================================================
# Source code - text with LF
# =============================================================

*.bash    text eol=lf
*.c       text diff=cpp
*.cpp     text diff=cpp
*.cs      text diff=csharp
*.css     text
*.go      text
*.h       text diff=cpp
*.html    text
*.java    text diff=java
*.js      text
*.json    text
*.jsx     text
*.kt      text
*.lua     text
*.md      text
*.php     text diff=php
*.py      text diff=python
*.rb      text diff=ruby
*.rs      text
*.sass    text
*.scss    text
*.sh      text eol=lf
*.sql     text
*.swift   text
*.toml    text
*.ts      text
*.tsx     text
*.txt     text
*.xml     text
*.yml     text
*.yaml    text

# =============================================================
# Windows files - require CRLF
# =============================================================

*.bat     text eol=crlf
*.cmd     text eol=crlf
*.ps1     text eol=crlf

# =============================================================
# Binary files - no conversion, no text diff
# =============================================================

*.ai      binary
*.bmp     binary
*.docx    binary
*.eot     binary
*.exe     binary
*.gif     binary
*.ico     binary
*.jpg     binary
*.jpeg    binary
*.mp3     binary
*.mp4     binary
*.otf     binary
*.pdf     binary
*.png     binary
*.psd     binary
*.svg     binary
*.tiff    binary
*.ttf     binary
*.woff    binary
*.woff2   binary
*.xlsx    binary
*.zip     binary
*.tar.gz  binary

# =============================================================
# Export - exclude from git archive distributions
# =============================================================

.github/          export-ignore
.gitattributes    export-ignore
.gitignore        export-ignore
.markdownlint.json export-ignore
.editorconfig     export-ignore
tests/            export-ignore
test/             export-ignore

# =============================================================
# GitHub Linguist - statistics overrides
# =============================================================

dist/             linguist-generated=true
vendor/           linguist-vendored=true
node_modules/     linguist-vendored=true
*.min.js          linguist-generated=true
*.min.css         linguist-generated=true
```

---

## Try It Yourself

**Step 1.** Create a repository:

**Windows (PowerShell):**

```powershell
mkdir gitattributes-practice && cd gitattributes-practice && git init
```

**Mac and Linux:**

```bash
mkdir gitattributes-practice && cd gitattributes-practice && git init
```

**Step 2.** Create a `.gitattributes` file with basic line ending normalisation:

```gitattributes
* text=auto eol=lf
*.bat text eol=crlf
*.png binary
```

**Step 3.** Commit it:

```bash
git add .gitattributes && git commit -m "add: .gitattributes with line ending normalisation"
```

**Step 4.** Check what attributes apply to a file:

```bash
git check-attr -a README.md
```

**Step 5.** Create a text file and check its attributes:

**Windows:**

```powershell
echo "hello" > hello.sh
git check-attr -a hello.sh
```

**Mac and Linux:**

```bash
echo "hello" > hello.sh
git check-attr -a hello.sh
```

The output should show `text: auto`, `eol: lf`.

**Step 6.** Re-normalise all files after adding `.gitattributes`:

```bash
git add --renormalize .
git status
```

**Step 7.** See the linguist attributes for a file (if your repo is on GitHub, these affect the language bar):

```bash
git check-attr -a *.js 2>/dev/null || echo "no js files yet"
```

**Step 8.** Clean up:

**Windows:**

```powershell
cd .. && rmdir /s /q gitattributes-practice
```

**Mac and Linux:**

```bash
cd .. && rm -rf gitattributes-practice
```

---

## Common Mistakes

**Not committing `.gitattributes` to the repository.**

`.gitattributes` must be committed so every contributor uses the same rules. An uncommitted `.gitattributes` only affects your local machine.

**Adding `.gitattributes` after files are already committed.**

The rules only apply going forward unless you re-normalise. Run `git add --renormalize .` and commit after adding or changing `.gitattributes` to apply the rules to existing files.

**Not marking binary files as `binary`.**

If you do not explicitly mark image, font and archive files as binary, Git may try to normalise their line endings, corrupting them. Always add `binary` (or at minimum `-text`) for non-text files.

**Relying solely on `core.autocrlf` instead of `.gitattributes`.**

`core.autocrlf` is a per-developer setting and is inconsistent across teams. `.gitattributes` is repository-wide and committed - it is the authoritative source. Use `.gitattributes` for all line ending rules and set `core.autocrlf=false` globally if you have a comprehensive `.gitattributes`.

**Forgetting `export-ignore` for sensitive or development-only files.**

If you distribute your project as a zip archive (via GitHub Releases or `git archive`), test files, CI configuration and IDE settings will be included unless you mark them with `export-ignore`.

**Using `linguist-vendored` on your own code.**

`linguist-vendored` excludes files from language statistics. Do not apply it to your own source code - only to third-party code copied into your repository.

---

## Summary

- `.gitattributes` controls how Git handles specific files: line endings, diffs, merges and exports
- Place it in the root of your repository and commit it - rules apply to all contributors
- `* text=auto eol=lf` is the recommended baseline for most projects, normalising all text files to LF in the repository
- Mark binary files explicitly with `binary` to prevent line ending corruption
- Windows script files (`.bat`, `.cmd`, `.ps1`) need `eol=crlf`
- `diff=language` attributes improve diff readability by showing function/class context
- `export-ignore` excludes development files from `git archive` distributions
- `linguist-generated` and `linguist-vendored` control GitHub's language statistics
- After changing `.gitattributes`, run `git add --renormalize .` to apply new rules to existing files

---

## Sources and Further Reading

- [Official gitattributes documentation](https://git-scm.com/docs/gitattributes) - the complete reference for every attribute
- [Pro Git book, Chapter 8.2: Git Attributes](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes) - the official guide with practical examples
- [github/gitattributes](https://github.com/gitattributes/gitattributes) - a collection of useful `.gitattributes` templates for many languages and frameworks
- [GitHub Linguist documentation](https://github.com/github-linguist/linguist/blob/main/docs/overrides.md) - full reference for linguist attribute overrides
- [Git LFS .gitattributes documentation](https://git-lfs.com) - how LFS uses `.gitattributes` to track large files

---

<div align="center">

Made with 🔓 by [Isaac Adjei](https://isaacadjei.me)

**Access Granted. Success Unlocked.**

</div>

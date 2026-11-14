# vale-bringhurst

A [Vale](https://vale.sh/) style and configuration package enforcing micro-typographic conventions from Robert Bringhurst’s _The Elements of Typographic Style_ for **LaTeX** (`.tex`) and **AsciiDoc** (`.adoc`, `.asciidoc`) manuscripts.

All rules include `action` definitions for one-click **Quick Fixes** (`Ctrl+.` / `Cmd+.`) in VSCodium and VS Code.

## What’s Included

Because this is a combined Vale package, `vale sync` automatically installs three style directories alongside a base `.vale.ini` that maps `tex = md` and configures `BlockIgnores` and `TokenIgnores` for math environments, code blocks, comments, and cross-references:

- **`Bringhurst`** -- Shared prose rules applied to both LaTeX and AsciiDoc.
- **`BringhurstTeX`** -- LaTeX-specific rules using native TeX ligatures and macros (`--`, `\dots`, `~`, `\textsc{...}`, math-mode minus signs).
- **`BringhurstAdoc`** -- AsciiDoc-specific rules accounting for Asciidoctor’s HTML compilation (`–`, `…`, `{nbsp}`, `[.smallcaps]#...#`, Unicode `−`).

## Prerequisites

- **Vale** `>= 3.0.0`
- **Asciidoctor** installed in your `$PATH` if linting `.adoc` / `.asciidoc` files.

## Installation

1. Create a `.vale.ini` file in the root of your book or documentation project:

```ini
StylesPath = .vale/styles
MinAlertLevel = suggestion

# Use /releases/latest/download/Bringhurst.zip or pin a specific version tag
Packages = https://github.com/garyritchie/vale-bringhurst/releases/latest/download/Bringhurst.zip
```

2. Download and unpack the styles:

```bash
vale sync

```

3. Add the synced directories to your project's `.gitignore` so upstream package files stay out of version control:

```gitignore
.vale/styles/.vale-config/
.vale/styles/Bringhurst/
.vale/styles/BringhurstAdoc/
.vale/styles/BringhurstTeX/

```

## Rule Reference

### Shared Core (`Bringhurst`)

| Rule                     | Bringhurst Ref. | Description                                                                |
| ------------------------ | --------------- | -------------------------------------------------------------------------- |
| `DatePlurals`            | §5.1.1          | Removes apostrophes from plural decades (`1990's` → `1990s`).              |
| `MultiplicationSign`     | §5.3            | Replaces the letter `x` between dimensions with `×` (`8x10` → `8 × 10`).   |
| `SentenceSpacing`        | §2.1.4          | Flags double spaces after terminal punctuation across paragraphs.          |
| `SpaceBeforePunctuation` | §2.1.4          | Removes stray spaces before `,`, `;`, `:`, `!`, and `?`.                   |
| `UnspacedSlashes`        | §5.2.4          | Closes spaces around slashes between single words (`and / or` → `and/or`). |

### LaTeX (`BringhurstTeX`)

| Rule                     | Bringhurst Ref. | Description                                                                     |
| ------------------------ | --------------- | ------------------------------------------------------------------------------- |
| `TeXDoubleQuotes`        | §5.1.1          | Flags straight `"` in favor of ` `...'' ``or`\enquote{...}`.                    |
| `TeXEllipses`            | §5.2.2          | Replaces jammed (`...`) or spaced (`. . .`) periods with `\dots`.               |
| `TeXHeadingPeriods`      | §5.2.3          | Removes trailing periods from `\part`, `\chapter`, `\section`, etc.             |
| `TeXMinusSign`           | §5.3            | Wraps bare negative numbers (`-5`) in math mode (`$-5$`) for a true minus sign. |
| `TeXNonBreakingSpace`    | §2.2.2          | Replaces breakable spaces with `~` before units and labels (`Fig.~1`, `10~mm`). |
| `TeXNumericalRanges`     | §5.2.1          | Replaces hyphens in number spans with TeX en-dashes (`1914--1918`).             |
| `TeXParentheticalDashes` | §5.2.1          | Enforces spaced en-dashes (`--`) instead of closed em-dashes (`---`).           |
| `TeXSmallCaps`           | §3.2.2          | Wraps bare 4+ letter acronyms in `\textsc{...}`.                                |
| `TeXTimeAndEraSmallCaps` | §3.2.2          | Converts `AM`, `PM`, `BCE`, `CE`, `BC`, and `AD` to lowercase `\textsc{...}`.   |

### AsciiDoc (`BringhurstAdoc`)

| Rule                      | Bringhurst Ref. | Description                                                                        |
| ------------------------- | --------------- | ---------------------------------------------------------------------------------- |
| `AdocEllipses`            | §5.2.2          | Replaces jammed (`...`) or spaced (`. . .`) periods with `…`.                      |
| `AdocHeadingPeriods`      | §5.2.3          | Removes trailing periods from section headings (`= Title.`).                       |
| `AdocMinusSign`           | §5.3            | Replaces hyphens before negative numbers (`-5`) with Unicode minus (`−5`, U+2212). |
| `AdocNonBreakingSpace`    | §2.2.2          | Replaces breakable spaces with `{nbsp}` before units and labels (`Fig.{nbsp}1`).   |
| `AdocNumericalRanges`     | §5.2.1          | Replaces hyphens in number spans with unspaced en-dashes (`1914–1918`).            |
| `AdocParentheticalDashes` | §5.2.1          | Replaces Asciidoctor’s `--` (`—`) with a literal spaced en-dash (`–`).             |
| `AdocSmallCaps`           | §3.2.2          | Wraps bare 4+ letter acronyms in `[.smallcaps]#...#`.                              |
| `AdocTimeAndEraSmallCaps` | §3.2.2          | Wraps `AM`, `PM`, `BCE`, `CE`, `BC`, and `AD` in `[.smallcaps]#...#`.              |

## Customization & Project Overrides

Because Vale merges your local `.vale.ini` on top of the package configuration, you can disable individual rules or swap in custom macros without modifying the synced files.

### Disabling a Rule

```ini
[*.tex]
BringhurstTeX.TeXSmallCaps = NO
Bringhurst.MultiplicationSign = suggestion

```

### Overriding a Rule with a Custom Macro (e.g., `\acro{...}`)

1. Place your project-specific rule in a local style folder tracked by Git (e.g., `.vale/styles/Project/TeXAcronyms.yml`):

```yaml
extends: substitution
message: "Wrap full-height acronyms ('%s') in \\acro{...} for spaced small caps (Bringhurst §3.2.2)."
level: suggestion
ignorecase: false
nonword: true
action:
  name: replace
swap:
  '(?<!\\(?:acro|textsc)\{)\b([A-Z]{4,})\b': '\acro{$1}'
```

2. Activate `Project` and disable the default rule in your project's `.vale.ini`:

```ini
[*.tex]
BasedOnStyles = Project
BringhurstTeX.TeXSmallCaps = NO
TokenIgnores = \\acro\{[^}]*\}

```

## Publishing a Release (Maintainers)

GitHub Releases are built automatically via `.github/workflows/release.yml` whenever a version tag matching `v*` is pushed:

```bash
git tag v0.1.0
git push origin v0.1.0

```

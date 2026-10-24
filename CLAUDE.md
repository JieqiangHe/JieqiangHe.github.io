# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview and architecture

This is Jieqiang He's personal academic website, published at <https://jieqianghe.github.io/>. It is a single static page with no framework, backend, or external runtime dependencies.

- `index.html` contains the page markup, inline CSS in the head, and inline JavaScript at the end of the body. Most of the markup and CSS is compressed into long lines.
- Career history and awards are maintained directly in the HTML. Publications are maintained separately in the inline `PUBS` array and rendered into `<ol id="pubs">` inside the `#publications` section.
- The publication renderer runs immediately after the markup. Keep its target ID (`pubs`, not `publications`) and script placement consistent; without successful JavaScript execution the list stays empty.

### Publication rendering

Each `PUBS` record uses `a` (authors), `t` (title), `j` (journal), `y` (year), `v` (volume/pages/article number), `doi`, `f` (PDF filename), and optional `note`. Records display in array order, with newest years first by convention; there is no automatic sorting.

Author strings use `⟦…⟧` to identify the name highlighted by the renderer. Preserve these delimiters, literal `#`/`*` contribution markers, and the accompanying legend. Titles and notes may contain intentional HTML or entities, such as `<i>` for species names. The renderer uses `innerHTML`, so these are trusted, HTML-bearing records rather than escaped plain text.

`f` is a filename only: the renderer prefixes `pubs/` and URL-encodes the filename. Publication titles open the local PDF; DOI links are separate. The awards entry has its own hardcoded conference-PDF link with a `#page=134` fragment, independent of `PUBS`.

## Development commands

Run commands from the repository root. Preview the site with Python's standard-library HTTP server:

```bash
python3 -m http.server 8000 --bind 127.0.0.1
```

Open `http://127.0.0.1:8000/`. No dependency installation or build step is required. There is no configured linter, automated test suite, or single-test command. Use `git diff --check` to check tracked changes for whitespace errors.

Verify changes in a browser: confirm the publication list renders in the intended order with name highlighting, contribution markers, and italics intact; open changed PDF and DOI links; check the conference award link's PDF page fragment; and check narrow-screen wrapping and keyboard focus.

## Publication assets

`pubs/` stores publication PDFs and conference material linked from the site. Keep PDF filenames and their HTML links in sync when adding or renaming assets.

The repository's detailed naming and metadata rules are in `.claude/skills/jh-publication-pdf-naming/SKILL.md`. The filename format is:

```text
<year>_<first-author-surname>_<normalized-journal>_<normalized-full-title>.pdf
```

Use lowercase ASCII, underscores between the four metadata fields, and hyphens within fields. Preserve the full title (including words inside parentheses), use the full journal name, and use the final issue's publication year. Before renaming a PDF, verify its metadata against both the paper itself and reliable DOI metadata. Co-first/corresponding-author markers must come from the paper's footnotes, not inferred author order or Crossref fields. Check for existing duplicates before adding an asset.

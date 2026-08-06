# Repo conventions

Personal academic homepage, served by GitHub Pages from the root of `main`.
Pushing to `main` publishes. There is no build step, no framework, no package manager.

```
index.html   the entire site — markup, CSS, and the publication data
docs/        publication PDFs, one per entry in the publication list
```

## Editing index.html

The file is deliberately written with very long lines: all CSS on one line, one
`PUBS` entry per line, the render template on one line. **Do not reformat or
pretty-print it** — a whole-file reflow buries any real change in noise. Match the
surrounding compact style when adding to it.

`<!DOCTYPE html>` must stay. Dropping it triggers quirks mode and breaks the grid
layout and `box-sizing`.

Sections `education`, `experience`, `awards` are static HTML — edit in place.
Only `publications` is data-driven, rendered from the `PUBS` array in the single
`<script>` at the bottom.

## Adding a publication

**1. PDF into `docs/`**, named `<year>-<first-author>-<keywords>.pdf`, e.g.
`2025-Cheng-MdGAMYB-MdHVA22g-drought.pdf`.

ASCII only. No spaces, no `γ`, no curly quotes, no typographic hyphens (`‐` U+2010) —
Zotero-style exports are full of these. The filename goes straight into a URL, so
keeping it plain avoids percent-escaping. Avoid a second `.` in the stem
(`MdGH3.6` → `MdGH3-6`).

**2. Entry at the top of `PUBS`** — the list renders in array order, newest first.
Entries are not numbered.

| Field | Meaning |
| --- | --- |
| `a` | author list |
| `t` | title |
| `j` | journal |
| `y` | year |
| `v` | volume, issue, pages |
| `doi` | DOI, bare — the `https://doi.org/` prefix is added at render time |
| `f` | filename in `docs/`; omit and the entry renders as plain text with no PDF link |
| `note` | optional extra line, e.g. `"Co-first &amp; co-corresponding author"` |

Author-list markup, matching the legend rendered above the list:

- `⟦…⟧` wraps the name to highlight (becomes `<span class="me">`, marker-pen underlay)
- `#` co-first author, `*` corresponding author
- `<i>…</i>` for species names, `&amp;` for a literal ampersand

Title and the `PDF` badge both link to `docs/<f>` in a new tab; the DOI is a separate
link. Hovering either the title or the badge highlights both.

## Invariant worth checking after edits

Every `f:` value must correspond to a real file in `docs/`, and every PDF in `docs/`
should be referenced exactly once:

```bash
diff <(grep -o ',f:"[^"]*\.pdf"' index.html | sed 's/,f:"//;s/"//' | sort) \
     <(ls -1 docs/*.pdf | xargs -n1 basename | sort)
```

Note `grep -o 'f:"[^"]*"'` without the leading comma also matches the CSS
`--serif:"Newsreader"` — keep the comma.

## Environment

No `python3` and no `node` in this container. `perl` and `bash` are available.
`micromamba` is installed with a `dev` environment if something genuinely needs
installing — ask first.

## CSS notes

Any rule using `:has()` must be its own standalone rule. A selector list is not
forgiving: one unsupported selector invalidates the entire rule, so mixing
`:has()` into a comma list with plain selectors silently kills the plain ones on
older browsers.

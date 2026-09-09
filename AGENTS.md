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

Sections `education`, `employment`, `experience`, `awards` are static HTML — edit in place.
Only `publications` is data-driven, rendered from the `PUBS` array in the single
`<script>` at the bottom.

The contact row in the hero is a list of `.pill` anchors. Each carries its own
inline icon — a 24×24 stroke-only SVG, `fill="none" stroke="currentColor"
stroke-width="2"`, sized to 13px by CSS. There is no icon font and no sprite, so a
new link needs its path data pasted in. Keep external links `target="_blank"
rel="noopener"`.

## Adding a publication

**1. PDF into `docs/`**. Use the globally installed `$jh-publication-pdf-naming`
skill from `$CODEX_HOME/skills/jh-publication-pdf-naming/SKILL.md`; this repository
does not keep a project-local copy. Use it to generate or validate the filename
from the four-digit year, first-author English surname, publication journal, and
full article title. The format is
`<year>_<first-author-surname>_<normalized-journal>_<normalized-full-title>.pdf`.
Use lowercase ASCII; separate metadata parts with underscores and words inside
the journal or title with hyphens. Replace an internal period with a hyphen
(`MdGH3.6` → `mdgh3-6`). Do not summarize the title or select keywords manually.

Before copying the PDF, verify its title, author order, journal, year, DOI, and
volume/article information against both the paper and reliable DOI metadata. Use
the contribution or correspondence footnotes in the paper for `#` and `*`; never infer
these markers from author order or from missing Crossref fields. When the source
PDF is outside this repository, copy it into `docs/` and leave the source file
untouched. For an online-first paper without volume, issue, or pages, put its
article number in `v`.

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

No `python3` and no `node` in this container. `perl`, `bash` and `git` are available.

`micromamba` is not on `PATH` — call it by full path
`/home/jh/micromamba/bin/micromamba` (a.k.a. `$MAMBA_EXE`). It has no
general-purpose environment: `base` holds only `micromamba` itself, and every
other env is one tool pinned to one version (`seqkit_2.13.0`,
`poppler_26.05.0`, …). Run a tool without activating anything:

```bash
/home/jh/micromamba/bin/micromamba run -n poppler_26.05.0 pdftotext -layout in.pdf out.txt
```

Outbound network is **selectively filtered**: `www.mdpi.com`, `doi.org`,
`example.com` and most other hosts fail with `SSL connect error … unexpected
eof`. Reachable: `mirrors.ustc.edu.cn`, `mirrors.tuna.tsinghua.edu.cn`,
`crossref.org`, `api.crossref.org`, `api.github.com`. So the default conda
channels are unusable — pass the USTC mirror explicitly:

```bash
/home/jh/micromamba/bin/micromamba create -y -n <name> <pkg>=<ver> \
  -c https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/
```

`poppler_26.05.0` provides `pdftotext` — the way to read PDFs in this
container (e.g. a saved MDPI special-issue page from `../srcs/`).

There is no `gh` CLI and no env for it, but `git push origin main` works on
its own — credentials are cached. Pushing kicks off GitHub's own
`pages-build-deployment` workflow, which takes a few minutes; check whether
the publish actually succeeded via the API:

```bash
curl -s "https://api.github.com/repos/JieqiangHe/JieqiangHe.github.io/actions/runs?per_page=3" \
  | grep -E '"name"|"status"|"conclusion"'
```

Creating a new env is a last resort — ask first.

## CSS notes

Any rule using `:has()` must be its own standalone rule. A selector list is not
forgiving: one unsupported selector invalidates the entire rule, so mixing
`:has()` into a comma list with plain selectors silently kills the plain ones on
older browsers.

---
name: jh-publication-pdf-naming
description: Generate or validate publication PDF filenames in the jh convention — <year>_<first-author-surname>_<normalized-journal>_<normalized-full-title>.pdf. Use when adding a paper PDF to a publications folder, renaming a paper PDF, or checking filenames against the convention.
---

# Publication PDF naming

Name publication PDFs like this:

```
<year>_<first-author-surname>_<normalized-journal>_<normalized-full-title>.pdf
```

The four metadata parts are joined with underscores; words inside the journal
or the title are joined with hyphens. Everything is lowercase ASCII — every
other character (parentheses, periods, commas, slashes, ampersands, accents,
superscripts, spaces) is dropped or transliterated. Nothing is deleted from
the text: parentheses and their content vanish as characters, but the words
inside them stay.

## The four parts

1. **Year** — four-digit publication year of the final (issue) version.
2. **First author** — English surname only, lowercase: `chen`, `guan`,
   `zhang`. Not the full author list, not given names, not the corresponding
   author.
3. **Journal** — full journal name, never an abbreviation, lowercase,
   hyphenated. Details below.
4. **Title** — the full article title, verbatim, lowercased and
   hyphenated. Details below.

## Journal normalization

- Expand every abbreviation to the full journal name: *New Phytol* →
  `new-phytologist`, *Plant Physiol* → `plant-physiology`, *JIPB* →
  `plant-biotechnology-journal`, *PCE* → `plant-cell-environment`.
- Keep a leading article: *The Plant Journal* → `the-plant-journal`,
  *The Plant Cell* → `the-plant-cell`.
- Drop an ampersand — do not replace it with "and": *Plant Cell &
  Environment* → `plant-cell-environment`. But a spelled-out "and" stays:
  *Plant Physiology and Biochemistry* → `plant-physiology-and-biochemistry`.
- Normalize branding casing: *iMETA* → `imeta`.
- One-word names stay unhyphenated: `plants`, `horticulturae`.

## Title normalization

- Use the **complete** title. Do not summarize, do not select keywords, keep
  stop words (the, of, in, by, a, and, for, between, via …).
- Gene symbols go lowercase; an internal period becomes a hyphen:
  `MdGH3.6` → `mdgh3-6`, `MdRFNR1-1` → `mdrfnr1-1` (existing hyphens stay).
- Subscripts and superscripts flatten inline: N⁶-methyladenosine →
  `n6-methyladenosine`, m⁶A → `m6a`. Digits already in the name stay:
  `h3k27me3`, `mdhva22g`.
- Greek letters are spelled out: γ-aminobutyric acid →
  `gamma-aminobutyric-acid`.
- Parentheses are removed but their content is kept, hyphenated into place:
  4-methylumbelliferone (4-MU) → `4-methylumbelliferone-4-mu`;
  *Malus prunifolia* (Willd.) Borkh. → `malus-prunifolia-willd-borkh`.
- Italics are lost; species names just join by hyphens: *Alternaria
  alternata* → `alternaria-alternata`.
- Acronyms and method names keep their letters, lowercased: RNAi → `rnai`,
  RNA-seq → `rna-seq`, ATAC-seq → `atac-seq`, GABA → `gaba`, ROS → `ros`.

## Worked examples

- `2022_jiang_the-plant-journal_mdgh3-6-is-targeted-by-mdmyb94-and-plays-a-negative-role-in-apple-water-deficit-stress-tolerance.pdf`
  — journal keeps *The*; `MdGH3.6` → `mdgh3-6`.
- `2021_mao_plants_profiling-of-n6-methyladenosine-m6a-modification-landscape-in-response-to-drought-stress-in-apple-malus-prunifolia-willd-borkh.pdf`
  — N⁶ → `n6`, m⁶A → `m6a`; botanical authority kept, punctuation dropped.
- `2023_zhang_horticulture-research_4-methylumbelliferone-4-mu-enhances-drought-tolerance-of-apple-by-regulating-rhizosphere-microbial-diversity-and-root-architecture.pdf`
  — `(4-MU)` → `-4-mu`.
- `2025_bai_imeta_easymetagenome-a-user-friendly-and-flexible-pipeline-for-shotgun-metagenomic-analysis-in-microbiome-research.pdf`
  — *iMETA* → `imeta`.

## Before renaming a PDF

Verify title, author order, journal, year, DOI, and volume/article number
against **both** the paper itself and reliable DOI metadata, e.g.:

```bash
curl -s "https://api.crossref.org/works/<DOI>" | jq '.message | {title, author, "container-title", issued, volume, "article-number"}'
```

Contribution markers (`#` co-first, `*` corresponding) come only from the
paper's own footnotes — never infer them from author order or from missing
Crossref fields.

Finally, compare against the existing files in the target folder to catch
collisions or near-duplicates before writing the new name.

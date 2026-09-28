# jieqianghe.github.io

Personal academic website of Jieqiang He: employment, education, publications, and awards. It is a single static page with no framework, web fonts, or build step.

**Live:** https://jieqianghe.github.io/

## Contents

- `index.html`: the whole site, including markup, styles, and the publication list (the `PUBS` array in the script at the end)
- `pubs/`: PDFs of the publications, named `<year>_<first-author>_<journal>_<title>.pdf`

## Adding a publication

Add an entry to `PUBS` in `index.html` and put its PDF in `pubs/`. Mark your own name with `⟦ ⟧`, co-first authors with `#`, and corresponding authors with `*`.

## Usage

Open `index.html` in a browser. Pushing to `main` publishes the site through GitHub Pages.

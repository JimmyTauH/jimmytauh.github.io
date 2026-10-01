# Website Maintenance Guide

This guide documents the content-maintenance workflow for Zhentao He's personal website. The site is built with Jekyll and al-folio. Edit source files on `main`; never edit the generated `_site/` directory or the `gh-pages` branch.

## Profile links and favicon

- The compact links below the profile photo are the `profile.more_info` HTML block in `_pages/about.md`.
- Footer icons are managed separately in `_data/socials.yml`.
- The browser-tab icon is set by `icon` in `_config.yml`. Use a square SVG or PNG stored in `assets/img/` (the current monogram is `favicon-zt-red.svg`). Browsers maintain a persistent favicon cache per page URL, so use a new filename and update `icon` whenever replacing the artwork; changing only the contents of the existing file may leave previously visited subpages showing the old icon.

## Local preview and deployment

Start the auto-refreshing local site from the repository root:

```bash
docker compose up -d
```

Open `http://localhost:8080`. Changes to Markdown, YAML, BibTeX, and images are rebuilt automatically. If `_config.yml` changes, Jekyll restarts automatically. Stop the preview with:

```bash
docker compose down
```

Before committing, check the rendered homepage, `/publications/`, `/cv/`, `/projects/`, and `/repositories/`. Then run `npm ci`, `npm run format:check`, and `git diff --check`. Commit on `main` and push; GitHub Pages deploys from the repository workflow.

### Production build policy

- Production deployment must retain site-wide HTML compression through `jekyll-minifier`. Do not remove or disable it merely to shorten the build time.
- The current production build time of approximately 114 seconds is acceptable. Treat this as the intentional cost of full-site compression unless a replacement provides equivalent output and has been verified separately.
- `jekyll-minifier.compress_javascript` remains disabled because JavaScript is minified separately by Terser. The minifier exclusions for `robots.txt` and the generated search scripts are intentional; they do not disable HTML compression for site pages.
- Performance work should target unused build dependencies and unnecessarily broad asset processing without weakening production output.

### Removed optional template features and assets

This personal-site fork deliberately removes unused al-folio demo assets instead of accumulating build exclusions. Prettier still checks all maintained source files; its ignore list is reserved for generated files, vendored code, and formats that Prettier cannot safely rewrite. ImageMagick is intentionally different: it processes only `assets/img/profile/` and `assets/img/publication_preview/` because responsive variants are useful only for those source images.

Jupyter Notebook embedding was supported by the original template but is disabled and removed by default because this site does not publish notebooks. The removal covers the complete feature rather than only its build dependency:

- the `jekyll-jupyter-notebook` gem and Jekyll plugin registration;
- Python/Jupyter installation in the deployment, accessibility, and link-check workflows and in the Docker image;
- `jupyter-nbconvert` in the development container;
- the example notebook under `assets/jupyter/`;
- Jupyter-specific styles, link handling, theme synchronization, and script includes;
- the obsolete Jupyter preview image and template documentation references.

#### Restore Jupyter Notebook support

Only restore this feature when a real notebook will be published. Use the Git history from before the removal as the canonical source so the restored files match this site's al-folio version:

1. Find the deletion commit with `git log --diff-filter=D -- assets/jupyter/blog.ipynb`, then inspect its parent as `<commit-before-removal>`.
2. Restore `assets/jupyter/`, `assets/css/jupyter.css`, `assets/css/jupyter-grade3.css`, `assets/css/jupyter-monokai.css`, and `assets/js/jupyter_new_tab.js` from that commit.
3. Use the same commit's versions of `assets/js/common.js`, `assets/js/theme.js`, `_includes/scripts.liquid`, and `_includes/distill_scripts.liquid` as references, and reapply only their Jupyter-specific blocks. Do not replace whole files, because they may contain newer unrelated changes.
4. Add `gem 'jekyll-jupyter-notebook'` to the `:jekyll_plugins` group in `Gemfile`, add `jekyll-jupyter-notebook` to `_config.yml` under `plugins`, and refresh `Gemfile.lock` with Bundler.
5. Restore Python setup plus the appropriate Jupyter/`nbconvert` installation in `.github/workflows/deploy.yml`, `.github/workflows/axe.yml`, and `.github/workflows/broken-links-site.yml`. Restore `python3-pip` plus `nbconvert` in `Dockerfile`, and add `jupyter-nbconvert` back to `.devcontainer/devcontainer.json` if those environments are used.
6. Add the actual `.ipynb` source under `assets/jupyter/`, integrate it into a page or post using the plugin syntax appropriate to the restored plugin version, and verify light and dark themes locally before deployment.

The following unreferenced template media are also intentionally removed: numbered sample photos, legacy profile images, the rhino image, demonstration publication GIFs, and example audio, video, PDF, HTML, JSON, Plotly, and Distill bibliography files. Restore an individual asset from Git history only when adding content that actually uses it.

## Source-of-truth map

| Website area                                                          | Source file(s)                                              | What to edit                                                                                                |
| --------------------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Homepage biography, research interests, selected-publication ordering | `_pages/about.md`                                           | Biography prose, interests, homepage-only Scholar ordering                                                  |
| Complete publications and homepage selected cards                     | `_bibliography/papers.bib`                                  | BibTeX metadata, abstract, DOI, code, image, selected status/order                                          |
| Publication-wide sorting                                              | `_config.yml`                                               | Scholar currently sorts full publications by year, month, and day, newest first                             |
| CV webpage                                                            | `_data/cv.yml`                                              | General information, education, publications, honors, projects, languages, skills                           |
| Downloadable CV                                                       | `assets/pdf/CV_ZhentaoHe.pdf`                               | Replace this PDF separately after changing CV content; the webpage does **not** regenerate it               |
| Project cards and project detail pages                                | `_projects/*.md`                                            | One Markdown file per project                                                                               |
| Code-repository cards                                                 | `_data/repositories.yml`                                    | Add `owner/repository` under `github_repos`                                                                 |
| Homepage news                                                         | `_news/*.md`                                                | One dated Markdown item per announcement                                                                    |
| Profile image and publication previews                                | `assets/img/profile/` and `assets/img/publication_preview/` | Add image assets, then reference the filename in source metadata                                            |
| Search-index exclusions                                               | Project front matter plus `_includes/metadata.liquid`       | Use `sitemap: false` and `robots: noindex, follow` for a page that should remain accessible but not indexed |

## Add a publication

Follow every relevant step below. A normal research paper should include the publication entry, preview image, code link when available, and an associated project page when the work should be showcased.

### 1. Add the BibTeX entry

Add the entry to `_bibliography/papers.bib`. Use official publisher metadata for authors, title, venue, DOI, and publication date. Include `month` and `day` whenever known; these fields control the full publication-page order.

```bibtex
@article{lastname2027method,
  author = {First Author and Zhentao He and Senior Author},
  title = {Method title in sentence case},
  journal = {Journal Name},
  year = {2027},
  month = jan,
  day = {15},
  volume = {12},
  number = {3},
  pages = {123--135},
  doi = {10.xxxx/example},
  url = {https://doi.org/10.xxxx/example},
  code = {https://github.com/owner/repository},
  preview = {method.png},
  abbr = {Journal Abbr.},
  abstract = {Two or three plain-language sentences stating the problem, method, and main capability or result.},
  selected = {true},
  selected_order = {4},
  note = {Published January 15, 2027}
}
```

Required practice:

- Always add `doi` and `url` when a DOI exists. The DOI button is generated automatically.
- Add a concise `abstract` for every paper. It generates the **Abs** button.
- Add `code` whenever a public repository exists. It generates the **Code** button.
- Save the preview image under `assets/img/publication_preview/` and set `preview` to its filename.
- Use publisher wording for the author list and journal title. Keep the abstract short and reader-facing rather than pasting the full publisher abstract.

### 2. Decide whether it is a homepage selected paper

Set `selected = {true}` only for papers that should appear on the homepage. Homepage cards are automatically selected by the existing query:

```liquid
{% raw %}{% bibliography --group_by none --query @*[selected=true]* %}{% endraw %}
```

Their order is controlled by `selected_order` in `_pages/about.md`'s page-level Scholar configuration. Smaller numbers appear first. Use this policy:

1. Contribution prominence, such as author position and intellectual contribution.
2. Perceived scholarly impact as the tie-breaker.

Do not manually reorder the BibTeX file or alter the Liquid template to change homepage order.

### 3. Add a project page when appropriate

Copy an existing `_projects/*_project.md` file. Set the title, one-sentence description, preview image, `importance`, and category. Include a short overview, key features, paper link, code link, and a `{% raw %}{% cite bibtex_key %}{% endraw %}` reference.

```yaml
---
layout: page
title: Method
description: One-sentence reader-facing description
img: assets/img/publication_preview/method.png
importance: 2
category: work
related_publications: true
---
```

For a project that should be visible on the site but should **not** be indexed by search engines, add:

```yaml
sitemap: false
robots: noindex, follow
```

Do not use `robots.txt` to block such a page: a crawler must be allowed to fetch the page to see the `noindex` directive. Existing search results disappear only after recrawling; use Google Search Console's removal tool when a faster removal is needed.

### 4. Add its repository card when appropriate

If the code repository should appear on `/repositories/`, add its GitHub `owner/repository` name to `_data/repositories.yml`:

```yaml
github_repos:
  - owner/repository
```

Only add public, stable repositories that are useful to visitors. A code link in BibTeX is still appropriate even when the repository does not need a separate card.

### 5. Update the CV and news when appropriate

- Add the publication to the `Publications` section in `_data/cv.yml`, newest first. Include authors, venue/date, and DOI.
- If the publication is a noteworthy milestone, add a dated announcement in `_news/`.
- Replace `assets/pdf/CV_ZhentaoHe.pdf` if the downloadable CV should match the webpage.

## Add an honor, fellowship, degree, or language credential

### CV webpage

Edit `_data/cv.yml`.

- Put awards and fellowships in `Honors & Awards`, newest first.
- Put degree-level distinctions in the relevant `Education` entry only when they are part of the degree record.
- Put language qualifications under `Languages`; for example, `Fluent (IELTS Band 7)` belongs under English rather than General Information.
- Keep `Summary` research-focused rather than repeating credentials.

Example honor:

```yaml
- title: Honors & Awards
  type: time_table
  contents:
    - title: Award or Fellowship Name
      institution: Awarding Institution
      year: 2027
```

### Homepage biography

The homepage does not have a built-in honors section. Integrate only the most identity-defining honors naturally in `_pages/about.md`:

- Current fellowship or exchange status: first paragraph, after institution/advisor context.
- Undergraduate graduation honor: second paragraph, after degree or college context.
- Other awards, including the National Scholarship, normally remain in the CV unless they materially strengthen the short biography.

### Downloadable CV

After updating `_data/cv.yml`, separately regenerate or replace `assets/pdf/CV_ZhentaoHe.pdf` before publishing if visitors should be able to download the same information.

## Update the homepage biography and research interests

Edit only `_pages/about.md` for biographical prose and the `Research Interests` list. Keep the first paragraph focused on current affiliation, advisor, fellowship status, and research identity. Keep the second paragraph focused on undergraduate education, major distinction, and prior research. Update the CV `Summary` in `_data/cv.yml` at the same time when changing the research-positioning statement.

## Update projects, repositories, and news

- **Projects:** add or edit `_projects/*.md`; lower `importance` values appear earlier.
- **Repositories:** edit `_data/repositories.yml`; GitHub cards are fetched from the listed public repositories.
- **News:** add a dated file in `_news/`. Use an announcement for a publication, fellowship, award, or major milestone, but do not duplicate routine CV changes.

## Final quality checklist

- Publication dates, authors, DOI, code link, abstract, and preview image are correct.
- Homepage selected order follows `selected_order`; complete publications are newest first.
- CV webpage and downloadable PDF agree when the PDF is offered.
- New project pages have an intentional indexing choice.
- Homepage, Publications, CV, Projects, and Repositories render correctly in local preview.
- `git diff --check` succeeds before committing.

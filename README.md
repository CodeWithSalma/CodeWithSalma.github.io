# Salma's Portfolio

This repository contains a personal portfolio website built with MkDocs.

Overview
--------

The site is authored as Markdown in the `docs/` folder and styled with the files in `stylesheets/`.
Site configuration is in `mkdocs.yml`.

Quick start
-----------

Prerequisites:
- Python 3.8 or newer
- Git

Install dependencies and run locally:

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
mkdocs serve
```

Open http://127.0.0.1:8000 in your browser to preview changes.

Build
-----

Build the static site into the `site/` directory:

```bash
mkdocs build
```

Deploy
------

You can deploy to GitHub Pages using MkDocs' built-in deploy command (requires git remote configured):

```bash
mkdocs gh-deploy --clean
```

Project structure
-----------------

- `mkdocs.yml` — MkDocs configuration
- `requirements.txt` — Python dependencies (MkDocs and plugins)
- `docs/` — Markdown content for the site (pages, images)
- `stylesheets/` — custom CSS (e.g. `extra.css`)
- `img/` — shared images used by the site

Editing content
---------------

- Edit or add Markdown files under `docs/`.
- Update navigation via `mkdocs.yml` if you change file organization.
- Preview locally with `mkdocs serve` after editing.

Contributing
------------

This repository is a personal portfolio and documentation site. You are free to clone or fork the project and edit it for your own use. Please do not submit pull requests to this repository — maintain your own fork or copy for changes.

License
-------

No license file is included in this repository. Add a `LICENSE` file if you want to define reuse terms.

Contact
-------

This repository is named `salma-portfolio` — update this README to include contact or author details as needed.


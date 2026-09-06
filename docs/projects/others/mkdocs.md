# Technical Documentation Setup with MkDocs — Salma's Tech Journey

A demonstration of technical writing and documentation-system design: building and structuring **Salma's Tech Journey**, a professional portfolio site, using MkDocs and the Material theme — covering site architecture, content standardization, and authoring workflow.

---

## The Problem

A portfolio scattered across a resume, LinkedIn, and loose project write-ups makes it hard for a reader to evaluate someone's technical depth consistently:

- No single, navigable source covering projects, certifications, and professional background
- Project write-ups without a consistent structure are harder to compare and skim
- Plain README files don't support search, theming, or organized navigation at scale

## The Solution

**Salma's Tech Journey** is a static documentation site built with MkDocs and the Material theme, organized as a structured knowledge base rather than a single long resume page: a Home/profile page, a categorized Projects section, Certifications, and an About page — all authored in Markdown with a consistent documentation standard applied across every project entry.

---

## Technology Stack

| Technology | Purpose |
|---|---|
| MkDocs | Static site generator, converts Markdown into a navigable documentation site |
| MkDocs Material | Theme providing navigation tabs, search, dark/light mode, and content components |
| Python | Runtime for MkDocs and its plugin ecosystem |
| PyMdown Extensions | Adds admonitions, tabbed content, code annotations, arithmatex, and superfences (Mermaid) |
| mkdocs-glightbox | Lightbox viewer for images |
| mkdocs-autorefs | Automatic cross-referencing between pages |
| mkdocs-static-i18n | Internationalization support |
| mkdocs-git-revision-date-localized | Displays last-updated dates from Git history |

---

## Site Architecture

```
salma-portfolio/
├── mkdocs.yml              # Theme, navigation, extensions, plugins
├── requirements.txt        # Python dependencies
└── docs/
    ├── index.md             # Home — profile, experience, skills, education
    ├── img/                 # Profile and content images
    ├── stylesheets/
    │   └── extra.css        # Custom theme overrides
    ├── projects/
    │   ├── index.md          # Projects overview
    │   ├── qa/
    │   ├── rpa/               # UiPath Invoice Processing, Vendor Invoice Portal
    │   ├── devops/            # Local DevOps Homelab
    │   └── others/            # BCA engagements
    ├── certifications/index.md
    └── about/index.md
```

Navigation is defined explicitly in `mkdocs.yml`, grouping projects by domain (RPA & Automation, DevOps & Infrastructure, Other Projects) rather than listing them flat — making the site scale cleanly as more projects are added.

---

## Key Capabilities

| Capability | Description |
|---|---|
| Structured Navigation | Tabbed, sectioned nav with grouped categories for projects |
| Custom Branding | Pink/purple light theme, indigo/deep-purple dark theme, custom logo icon |
| Rich Content Components | Admonitions, tabbed blocks, Mermaid diagrams, grid cards for skills |
| Searchable | Full-text search with highlighting and suggestions |
| Image Handling | Lightbox-enabled images with captions via `figure` blocks |
| Consistent Project Format | Every project page follows the same section structure for easy comparison |

---

## Content Standardization

A key part of this documentation work was designing a **repeatable structure** for project write-ups, so every project — regardless of domain (RPA, DevOps, or non-technical engagements) — reads consistently:

1. Title + one-line summary
2. The Problem
3. The Solution
4. Technology Stack (table)
5. Architecture / domain-adapted equivalent
6. Key Capabilities (table)
7. A domain-specific section (e.g. Data Validation, Security Practices)
8. Setup & Configuration
9. Deployment
10. Troubleshooting
11. Business Impact / Learning Outcomes
12. Future Enhancements

This structure was applied across all documented projects — including adapting it for a project with no public repo (BCA engagements), where technical sections were replaced with outcome summaries per engagement instead of being forced into an irrelevant template.

---

## Setup & Configuration

### Prerequisites

- Python 3.x
- pip

### Installation

```bash
pip install -r requirements.txt
```

### Local Preview

```bash
mkdocs serve
```

Serves the site locally with live reload as Markdown files are edited.

### Build

```bash
mkdocs build
```

Generates the static site output ready for deployment.

---

## Markdown Features Used

- **Admonitions** — callout boxes for notes, warnings, and tips
- **Tabbed content** — `=== "Tab"` blocks for grouping related content
- **Mermaid diagrams** — rendered flow/architecture diagrams via `pymdownx.superfences`
- **Grid cards** — used on the Home page to lay out skills by category
- **Code annotations & copy button** — inline numbered annotations on code blocks, one-click copy
- **Figure captions with lightbox** — images open full-size on click via `glightbox`

---

## Business Impact

- Presents projects and professional experience in a single, structured, searchable site instead of a flat resume
- Standardized documentation format makes technical depth easy to evaluate across very different project types
- Reusable structure means new projects can be documented and published quickly without redesigning the format each time

---

## Future Enhancements

- CI/CD deployment pipeline (e.g. auto-publish to GitHub Pages on push)
- Multi-language support using the already-installed `mkdocs-static-i18n` plugin
- Git-based "last updated" timestamps via `mkdocs-git-revision-date-localized`
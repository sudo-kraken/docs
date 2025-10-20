<div align="center">
<img src="docs/assets/logo.png" align="center" width="144px" height="144px"/>

### JH Docs

_A single home for documentation across my repositories. Built with Markdown and published to GitHub Pages._
</div>

<div align="center">

[![Docs](https://img.shields.io/badge/Docs-Live-blue?style=for-the-badge)](https://sudo-kraken.github.io/docs/)
[![GitHub Pages](https://img.shields.io/badge/Hosting-GitHub%20Pages-222?style=for-the-badge&logo=github)](https://sudo-kraken.github.io/docs/)

</div>

## Contents

- [Overview](#overview)
- [Structure at a glance](#structure-at-a-glance)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Quick start](#quick-start)
- [Local development](#local-development)
- [Publishing](#publishing)
- [Troubleshooting](#troubleshooting)
- [Licence](#licence)
- [Security](#security)
- [Contributing](#contributing)
- [Support](#support)

## Overview

This repository hosts the documentation hub for my projects. The published site is available at:

- https://sudo-kraken.github.io/docs/

The docs are written in Markdown, organised under `docs/`, and configured via `mkdocs.yml`. The site is intended to be simple to work on locally and easy to publish.

## Structure at a glance

```
.
├─ docs/                 # Markdown content and assets
│  ├─ index.md
│  └─ assets/
│     └─ logo.png
├─ mkdocs.yml            # Site configuration and navigation
├─ .github/              # Workflows and repository config
└─ README.md
```

## Features

- Straightforward Markdown based content
- Clear repository structure with a single `mkdocs.yml` entry point
- GitHub Pages publishing
- Friendly defaults that are easy to extend

## Prerequisites

- A recent version of Python
- `mkdocs` installed in your environment
- Optionally a theme such as Material for MkDocs

## Quick start

Clone the repository and install mkdocs in your preferred way, then serve locally:

```bash
# from the repository root
pip install mkdocs
mkdocs serve
```

Visit http://127.0.0.1:8000 to view the site locally.

## Local development

- Write pages in `docs/` using Markdown
- Update site navigation and metadata in `mkdocs.yml`
- Use `mkdocs serve` for live reload during editing

Common commands:

```bash
# Start a local preview server
mkdocs serve

# Build the static site into the 'site/' directory
mkdocs build
```

## Publishing

The site is published via GitHub Pages. A typical manual flow is:

```bash
# Build and publish to the gh-pages branch
mkdocs gh-deploy
```

If you use an automated workflow, push changes to `main` and let your pipeline handle deployment.

## Troubleshooting

- **Broken links or pages not visible**  
  Confirm the file path and that the page is included in `mkdocs.yml` navigation.
- **Build fails locally**  
  Ensure `mkdocs` is installed in the active environment.
- **Styles not applying**  
  Check that any theme or extra plugins referenced in `mkdocs.yml` are installed locally.

## Licence

This project is licensed under the MIT Licence. See the [LICENCE](LICENCE) file for details.

## Security

If you discover a security issue, please review and follow the guidance in [SECURITY.md](SECURITY.md), or open a private security-focused issue with minimal details and request a secure contact channel.

## Contributing

Feel free to open issues or submit pull requests if you have suggestions or improvements.  
See [CONTRIBUTING.md](CONTRIBUTING.md)

## Support

Open an [issue](/../../issues) with as much detail as possible, including any steps to reproduce and, where relevant, your local tooling versions.

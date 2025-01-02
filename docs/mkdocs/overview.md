# Overview

I use MkDocs to manage and generate documentation for fabien-renaud-fr organization. MkDocs is a simple, static site
generator specifically designed for project documentation. It uses Markdown files to create well-structured,
easy-to-navigate documentation websites.

## Project layout

This repository follows this layout:

```
mkdocs.yml     # The configuration file
docs/
    index.md   # The documentation homepage
    ...        # Other markdown pages, images and other files
site/
    index.html # The website homepage
    ...        # Other statics files
```

## Workflow

1. Write your content in Markdown files within the `docs/` directory
2. Define website's configuration in `mkdocs.yml`
3. Run the development server with `mkdocs serve` to preview changes locally
4. Commit and push your changes. The GitHub workflow will automatically run the `mkdocs gh-deploy` command to generate
   the `site/` directory and publish it on GitHub pages

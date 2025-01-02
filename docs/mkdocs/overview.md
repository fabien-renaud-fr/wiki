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

To publish new content, follows this workflow:

1. Write your content in Markdown files within the `docs/` directory
2. Define website's configuration in `mkdocs.yml`
3. Run the development server with `mkdocs serve` to preview changes locally
4. Commit and push your changes. The GitHub workflow will automatically run the `mkdocs gh-deploy` command to generate
   the `site/` directory and publish it on GitHub pages

## Configure custom domain

To setup a custom domain on a MkDocs website published on GitHub pages, follow this procedure:

1. Go to your DNS provider and create a new record:
   - type: CNAME
   - subdomain: wiki.fabienrenaud.fr
   - target: fabien-renaud-fr.github.io (The CNAME record should always point to <user>.github.io or <organization>.github.io, excluding the repository name)
2. Create a `docs/CNAME` file that contains the subdomain on a single line
3. Go to your repository's `Settings` / `Pages` / `Custom Domain` page, configure your subdomain and click on `Save`

## References

- [MkDocs / Getting Started with MkDocs](https://www.mkdocs.org/getting-started)
- [MkDocs / Deploying your docs / Custom Domains](https://www.mkdocs.org/user-guide/deploying-your-docs/#custom-domains)
- [GitHub / About custom domains and GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages)
- [GitHub / Manage a custom subdomain / Configuring a subdomain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain)

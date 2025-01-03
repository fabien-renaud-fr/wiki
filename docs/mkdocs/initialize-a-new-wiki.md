# Initialize a new wiki

This page provides a step-by-step guide to set up a new MkDocs-based wiki. It explains how to configure your project,
set up CI/CD pipelines with GitHub Actions to build website, and assign a custom domain name for your documentation
site.

## (Optional) Install MkDocs

You can skip this part if you only want to build website using CI/CD and never run website on your own computer.

**Using Python:**

Follow official instructions here: [MkDocs Installation](https://www.mkdocs.org/user-guide/installation)

**Using Docker:**

You can build your own unofficial Docker image:
[https://github.com/fabien-renaud-fr/wiki/tree/master/docker/mkdocs](https://github.com/fabien-renaud-fr/wiki/tree/master/docker/mkdocs)

## Initialize GitHub repository

- Create a new GitHub repository
- Create `mkdocs.yml` configuration file (can be locally generated by running `mkdocs new <site>` command):

```yaml
site_name: My Docs
```

- Create `docs/index.md` file (also generated by running `mkdocs new <site>` command):

```markdown
# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.
```

## Configure CI/CD

To automatically publish your website, follow this procedure:

- Create `.github/workflows/99-build-and-publish-documentation.yml` file:

```yaml
name: 99. 📖 Build and publish documentation
on:
  push:
jobs:
  build-mkdocs:
    name: 📖 Build and publish documentation
    runs-on: ubuntu-22.04
    steps:
      - name: 📥 Checkout
        uses: actions/checkout@v4

      - name: 👷 Setup python
        uses: actions/setup-python@v5
        with:
          python-version: 3.x

      - name: 📖 Build and publish documentation
        run: |
          pip install mkdocs
          mkdocs gh-deploy --force
```

- Go to your repository's `Settings` / `Pages` / `Build and deployment` page:
    - source `Deploy from a branch`
    - branch: `gh-pages` `/ (root)` *(this branch will be created once
      `.github/workflows/99-build-and-publish-documentation.yml` file is pushed)*

## Configure custom domain

To setup a custom domain on a MkDocs website published on GitHub pages, follow this procedure:

1. Go to your DNS provider and create a new record:
    - type: `CNAME`
    - subdomain: `wiki.fabienrenaud.fr`
    - target: `fabien-renaud-fr.github.io` *(the CNAME record should always point to <user\>.github.io or
      <organization\>.github.io, excluding the repository name)*
2. Create a `docs/CNAME` file that contains the subdomain on a single line
3. Go to your repository's `Settings` / `Pages` / `Custom Domain` page, configure your subdomain and click on `Save`

## References

- [Wiki / MkDocs / Overview](./index.md)
- [(External) MkDocs / MkDocs Installation](https://www.mkdocs.org/user-guide/installation)
- [(External) MkDocs / Deploying your docs / Custom Domains](https://www.mkdocs.org/user-guide/deploying-your-docs/#custom-domains)
- [(External) GitHub / About custom domains and GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/about-custom-domains-and-github-pages)
- [(External) GitHub / Manage a custom subdomain / Configuring a subdomain](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain)

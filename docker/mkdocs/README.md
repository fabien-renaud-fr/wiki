# MkDocs

## Installation

### Build command

```bash
docker build -t mkdocs:latest docker/mkdocs
```

### Run command

```bash
docker run --rm -v ".:/app" mkdocs new wiki
docker run --rm -p 8000:8000 -v ".:/app" mkdocs serve -a 0.0.0.0:8000 # -a is necessary to expose server outside container
docker run --rm -v ".:/app" mkdocs build
```

### Alias

```bash
alias mkdocs='docker run --rm -p 8000:8000 -v ".:/app" mkdocs'
mkdocs new wiki
mkdocs serve -a 0.0.0.0:8000
mkdocs build
```

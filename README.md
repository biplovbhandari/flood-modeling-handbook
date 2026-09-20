# Flood Modeling Handbook

An independently maintained handbook for learning hydrology, hydraulics, and flood-inundation modeling.

The source repository is private, but the rendered GitHub Pages site may be public.
When publishing is enabled, the site will be available at <https://biplovbhandari.github.io/flood-modeling-handbook/>.

## Documentation source

Published handbook content lives under `docs/`.
MkDocs configuration lives in `mkdocs.yml`.

## Local preview

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-docs.txt
python -m mkdocs serve
```

## Strict build

```bash
python -m mkdocs build --strict
```

## Publishing status

GitHub Pages deployment is manual while the public-content review remains pending.
The existence of a successful build does not by itself approve the content for public release.

# Flood Modeling Handbook

A handbook for learning hydrology, hydraulics, flood-inundation modeling, validation, and scientific software practice.

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

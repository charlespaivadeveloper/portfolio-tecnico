# Portfólio Técnico — Charles Paiva

Site de portfólio técnico construído com [MkDocs Material](https://squidfunk.github.io/mkdocs-material/). Publica projetos, decisões arquiteturais e exercícios de system design.

**Site publicado:** https://charlespaivadeveloper.github.io/portfolio-tecnico/

## Rodar localmente

**Pré-requisitos:** Python 3.x

```bash
pip install -r requirements.txt
mkdocs serve
```

Acesse `http://127.0.0.1:8000`.

## Build

```bash
mkdocs build --strict
```

Os artefatos são gerados em `site/`.

## Deploy

O deploy é automático via GitHub Actions a cada push na branch `main` (`.github/workflows/deploy.yaml`).

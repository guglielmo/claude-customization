---
name: prepare-docs
description: Fase 2 del workflow release - aggiorna documentazione e version files, crea il commit di documentazione
---

# Prepare Docs

Usa l'agente `prepare-docs` per aggiornare tutta la documentazione del progetto e i file di versione in preparazione al release.

Richiede il VERSION INFO block e RELEASE SUMMARY prodotti da `/prepare-release`:

```
VERSION INFO:
- Current: vA.B.C → New: vX.Y.Z
- Release type: [PATCH|MINOR|MAJOR]
- Release date: YYYY-MM-DD

RELEASE SUMMARY:
- New features: [lista]
- Bug fixes: [lista]
- Important changes: [lista]
```

L'agente coordina internamente writer specializzati in parallelo:
- **PATCH**: solo changelog-writer
- **MINOR/MAJOR**: changelog-writer + readme-writer + status-writer + claudemd-writer + contributing-writer

Aggiorna i version files (package.json, pyproject.toml, Cargo.toml, ecc.) e crea un commit di documentazione.

Output: VERSION INFO + PREPARATION STATUS da passare a `/publish-release`.

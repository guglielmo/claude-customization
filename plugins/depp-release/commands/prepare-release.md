---
name: prepare-release
description: Fase 1 del workflow release - analizza lo stato del repository e raccomanda il tipo di bump semantico (MAJOR/MINOR/PATCH)
---

# Prepare Release

Usa l'agente `prepare-release` per analizzare lo stato attuale del repository e determinare la versione da rilasciare.

L'agente esamina:
- Modifiche uncommitted (staged e unstaged)
- Commit locali non pushati
- Commit pushati ma non ancora inclusi in un release tag

Produce un report strutturato con:
- VERSION INFO block (`Current: vA.B.C → New: vX.Y.Z`)
- RELEASE SUMMARY (features, bug fixes, breaking changes)
- Raccomandazione MAJOR / MINOR / PATCH con motivazione
- Stato repository (conteggi per categoria)

Usa l'output come input per `/prepare-docs` quando sei pronto a procedere.

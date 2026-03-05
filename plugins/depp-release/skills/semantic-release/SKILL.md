---
name: semantic-release
description: Use this skill when the user mentions releasing, versioning, publishing, tagging, bumping version, creating a changelog, or preparing documentation for a release. Activates the semantic release workflow awareness.
version: 1.0.0
---

# Semantic Release Workflow

Quando l'utente parla di release, versioning, tag, changelog o pubblicazione di una nuova versione, questo progetto usa un workflow strutturato in 3 fasi.

## Le 3 Fasi

### Fase 1: `/prepare-release`
Analizza lo stato del repository e determina la versione da rilasciare.
- Esamina commit dalla last release, modifiche uncommitted, commit non pushati
- Applica semantic versioning (MAJOR / MINOR / PATCH)
- Produce: VERSION INFO block + RELEASE SUMMARY

### Fase 2: `/prepare-docs`
Aggiorna documentazione e version files, crea il commit di documentazione.
- PATCH: aggiorna solo CHANGELOG.md
- MINOR/MAJOR: aggiorna CHANGELOG, README, STATUS, CLAUDE.md, CONTRIBUTING + version files
- Produce: PREPARATION STATUS con commit hash

### Fase 3: `/publish-release`
Crea il tag annotato e fa push su remoto.
- Verifica pre-pubblicazione
- `git tag -a vX.Y.Z` + push commits + push tag
- Verifica pubblicazione remota

## Suggerisci il workflow

Quando rilevi intenzione di fare un release, suggerisci proattivamente:

> "Posso gestire il release con il workflow semantico in 3 fasi. Vuoi iniziare con `/prepare-release` per analizzare lo stato del repo e determinare la versione?"

## Regole operative

- Le 3 fasi sono **sempre sequenziali** con conferma utente tra una e l'altra
- Non unire o saltare fasi
- Ogni fase passa dati strutturati alla successiva — non modificare il formato degli output
- In caso di errore in qualsiasi fase: fermati, mostra l'errore, chiedi istruzioni

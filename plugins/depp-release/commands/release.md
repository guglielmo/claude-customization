---
name: release
description: Esegue il workflow completo di release semantica in 3 fasi - analisi versioning, aggiornamento documentazione, pubblicazione tag e push remoto
---

# Release Workflow Completo

Esegui il workflow di release semantica in 3 fasi sequenziali, con conferma dell'utente tra una fase e l'altra.

## Fase 1 — Analisi (prepare-release)

Usa l'agente `prepare-release` per:
- Analizzare lo stato del repository (uncommitted changes, commit non pushati, commit dalla last release)
- Determinare il tipo di bump semantico (MAJOR / MINOR / PATCH)
- Produrre il VERSION INFO block e il RELEASE SUMMARY

Mostra il report completo all'utente e chiedi conferma prima di procedere.

## Fase 2 — Documentazione (prepare-docs)

Se l'utente conferma, usa l'agente `prepare-docs` passando il VERSION INFO e RELEASE SUMMARY prodotti nella fase 1.

`prepare-docs` coordina internamente i writer specializzati (changelog-writer, readme-writer, status-writer, claudemd-writer, contributing-writer) in base al tipo di release, aggiorna i version files e crea il commit di documentazione.

Mostra il report di completamento e chiedi conferma prima di procedere.

## Fase 3 — Pubblicazione (publish-release)

Se l'utente conferma, usa l'agente `publish-release` passando il VERSION INFO e PREPARATION STATUS prodotti nella fase 2.

`publish-release` verifica lo stato pre-pubblicazione, crea il tag annotato, fa il push dei commit e del tag, verifica la pubblicazione remota.

## Comportamento

- Esegui le 3 fasi **sequenzialmente**, mai in parallelo
- **Pausa con conferma** tra ogni fase — non procedere automaticamente
- Se una fase fallisce, fermati e mostra l'errore all'utente senza tentare recovery automatico
- Usa sempre i dati strutturati prodotti da ogni fase come input per la successiva

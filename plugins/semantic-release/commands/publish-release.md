---
name: publish-release
description: Fase 3 del workflow release - crea il tag git annotato e fa il push su remoto
---

# Publish Release

Usa l'agente `publish-release` per completare il workflow di release: crea il tag annotato e fa il push su remoto.

Richiede il VERSION INFO e PREPARATION STATUS prodotti da `/prepare-docs`:

```
VERSION INFO:
- Version to publish: vX.Y.Z
- Current branch: [branch-name]
- Release type: [PATCH|MINOR|MAJOR]

PREPARATION STATUS:
- Documentation updated: Yes
- Version files updated: Yes
- Documentation commit: [commit-hash]
```

L'agente esegue in sequenza:
1. Verifica pre-pubblicazione (working directory pulita, commit presenti, version files aggiornati)
2. Crea tag annotato: `git tag -a vX.Y.Z -m 'Release vX.Y.Z'`
3. Push commits: `git push origin [branch]`
4. Push tag: `git push origin vX.Y.Z`
5. Verifica pubblicazione remota

In caso di errore (tag esistente, problemi di autenticazione, directory sporca) mostra istruzioni di recovery dettagliate senza procedere automaticamente.

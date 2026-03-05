# semantic-release Plugin

Workflow di release semantica in 3 fasi: analisi del repository, aggiornamento della documentazione e pubblicazione del tag su remoto.

## Comandi

| Comando | Descrizione |
|---------|-------------|
| `/release` | Esegue le 3 fasi in sequenza con conferma tra una e l'altra |
| `/prepare-release` | Analizza i commit dalla last release e raccomanda MAJOR / MINOR / PATCH |
| `/prepare-docs` | Aggiorna CHANGELOG, README, STATUS, CLAUDE.md e version files; crea il commit di documentazione |
| `/publish-release` | Crea il tag annotato e fa push di commit e tag su remoto |

## Skill inclusa

**`semantic-release`** — si attiva proattivamente quando si parla di release o versioning, guidando l'utente attraverso il workflow.

## Agenti inclusi

Chiamati internamente dagli orchestratori:

| Agente | Ruolo |
|--------|-------|
| `prepare-release` | Analizza i commit e determina il tipo di bump |
| `prepare-docs` | Orchestra l'aggiornamento della documentazione |
| `publish-release` | Crea il tag e fa push su remoto |
| `changelog-writer` | Aggiorna CHANGELOG.md |
| `readme-writer` | Aggiorna README.md con le novità della release |
| `status-writer` | Aggiorna STATUS.md |
| `claudemd-writer` | Aggiorna CLAUDE.md |
| `contributing-writer` | Aggiorna CONTRIBUTING.md se necessario |

## Struttura

```
plugins/semantic-release/
├── .claude-plugin/plugin.json
├── agents/
│   ├── prepare-release.md
│   ├── prepare-docs.md
│   ├── publish-release.md
│   ├── changelog-writer.md
│   ├── readme-writer.md
│   ├── status-writer.md
│   ├── claudemd-writer.md
│   └── contributing-writer.md
├── commands/
│   ├── release.md
│   ├── prepare-release.md
│   ├── prepare-docs.md
│   └── publish-release.md
└── skills/
    └── semantic-release/
        └── SKILL.md
```

## License

MIT

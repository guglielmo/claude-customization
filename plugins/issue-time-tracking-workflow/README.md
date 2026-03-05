# issue-time-tracking-workflow Plugin

Automazione del workflow per il tracking delle issue e il time logging su GitLab/GitHub e TMetric, con gestione rigorosa del timer singolo.

## Cosa include

| Componente | Posizione | Descrizione |
|------------|-----------|-------------|
| Skill `issue-time-tracking` | `skills/issue-time-tracking.md` | Si attiva automaticamente quando si parla di issue o timer |
| MCP server `tmetric-minimal` | submodule esterno ([guglielmo/tmetric-minimal-mcp](https://github.com/guglielmo/tmetric-minimal-mcp)) | API TMetric: start/stop timer, lista progetti |

## Utilizzo

Una volta installato, usa il linguaggio naturale:

```
"inizio a lavorare sulla issue #123"
"smetto di lavorare su #123 - corretto il bug di autenticazione"
"metti in pausa il timer"
"riprendi il timer"
"su cosa sto lavorando?"
```

## Funzionalità

- **Timer singolo** — impedisce di avviare più timer contemporaneamente
- **Rilevamento piattaforma** — funziona con GitLab e GitHub
- **Mapping progetti** — suggerisce il progetto TMetric in base al repository
- **Linguaggio naturale** — estrae il sommario del lavoro dal comando
- **Recovery errori** — chiede conferma prima di annullare operazioni parziali

## Requisiti

- **glab** (GitLab) o **gh** (GitHub) — CLI installata e autenticata
- **Account TMetric** con accesso API

## Struttura

```
plugins/issue-time-tracking-workflow/
├── skills/
│   └── issue-time-tracking.md     ← skill installata dal plugin

skills/issue-time-tracking/        ← workspace di sviluppo TDD (non installato)
├── SKILL.md
├── test-scenarios.md
├── baseline-results.md
├── green-phase-results.md
└── quality-checklist.md

mcp-servers/tmetric-minimal-mcp/   ← git submodule (repo separato)
```

> **Nota**: la skill è disponibile anche in modo standalone (senza MCP server) per ambienti che non richiedono TMetric. Vedi [`skills/issue-time-tracking/`](../../skills/issue-time-tracking/).

## Ottenere il token TMetric API

1. Accedi a [TMetric](https://app.tmetric.com/)
2. Vai su Impostazioni → Integrazioni → API
3. Genera un nuovo token API

## Licenza

MIT

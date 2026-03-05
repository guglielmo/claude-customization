# Claude Code Customizations

Raccolta di personalizzazioni per [Claude Code](https://claude.ai/code) sviluppate da Guglielmo Celata per il team DEPP. Il repository contiene:

- **Plugin** — workflow completi installabili con un comando, con agenti, comandi slash e skill integrate
- **Skill standalone** — comportamenti specializzati attivabili in qualsiasi progetto
- **MCP Server** — integrazioni con servizi esterni (TMetric, Google Docs)

---

## Plugin disponibili

### depp-release

Workflow di release semantica in 3 fasi: analisi del repository, aggiornamento della documentazione e pubblicazione del tag su remoto.

**Comandi**:
| Comando | Descrizione |
|---------|-------------|
| `/release` | Esegue le 3 fasi in sequenza con conferma tra una e l'altra |
| `/prepare-release` | Analizza i commit dalla last release e raccomanda MAJOR / MINOR / PATCH |
| `/prepare-docs` | Aggiorna CHANGELOG, README, STATUS, CLAUDE.md e version files; crea il commit di documentazione |
| `/publish-release` | Crea il tag annotato e fa push di commit e tag su remoto |

**Skill inclusa**: `semantic-release` — suggerisce proattivamente il workflow quando si parla di release o versioning.

**Agenti inclusi** (chiamati internamente dagli orchestratori):
`prepare-release` · `prepare-docs` · `publish-release` · `changelog-writer` · `readme-writer` · `status-writer` · `claudemd-writer` · `contributing-writer`

---

### issue-time-tracking-workflow

Sincronizzazione automatica dello stato delle issue e del time logging tra GitLab/GitHub e TMetric con gestione rigorosa del timer singolo.

Vedi [`plugins/issue-time-tracking-workflow/`](./plugins/issue-time-tracking-workflow/) per i dettagli.

---

## Installazione

```bash
# 1. Aggiungere il marketplace
/plugin marketplace add guglielmo/claude-customization

# 2. Installare i plugin desiderati
/plugin install depp-release@depp-plugins
/plugin install issue-time-tracking-workflow@depp-plugins
```

---

## Skill standalone

- **issue-time-tracking** — disponibile anche come skill standalone (senza MCP server) per ambienti che non richiedono TMetric

Vedi [`skills/`](./skills/) per la documentazione e la metodologia di sviluppo.

## MCP Server

- **tmetric-minimal-mcp** — integrazione con TMetric per il time tracking
- **mcp-googledocs-server** — integrazione con Google Docs

Vedi [`mcp-servers/`](./mcp-servers/) per le istruzioni di configurazione dei singoli server.

---

## Licenza

MIT — libero utilizzo, modifica e ridistribuzione.

## Autore

Guglielmo Celata — [@guglielmo](https://github.com/guglielmo)

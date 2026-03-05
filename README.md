# Claude Code Customizations

Raccolta di personalizzazioni per [Claude Code](https://claude.ai/code) sviluppate da Guglielmo Celata e distribuite come marketplace **guglielmo-plugins**. Il repository contiene:

- **Plugin** — workflow completi installabili con un comando, con agenti, comandi slash e skill integrate
- **Skill standalone** — comportamenti specializzati attivabili in qualsiasi progetto
- **MCP Server** — integrazioni con servizi esterni (TMetric, Google Docs)

---

## Plugin disponibili

### semantic-release

Workflow di release semantica in 3 fasi: analisi del repository, aggiornamento della documentazione e pubblicazione del tag su remoto. Include comandi slash (`/release`, `/prepare-release`, `/prepare-docs`, `/publish-release`), agenti specializzati per ogni fase e una skill che si attiva proattivamente quando si parla di release o versioning.

Vedi [`plugins/semantic-release/`](./plugins/semantic-release/) per i dettagli tecnici.

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
/plugin install semantic-release@guglielmo-plugins
/plugin install issue-time-tracking-workflow@guglielmo-plugins
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

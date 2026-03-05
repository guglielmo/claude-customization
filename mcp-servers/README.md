# mcp-servers/

Raccolta di MCP server per estendere Claude Code con integrazioni esterne.

## Server disponibili

### tmetric-minimal-mcp
**Git submodule** — repository separato [`guglielmo/tmetric-minimal-mcp`](https://github.com/guglielmo/tmetric-minimal-mcp)

Integrazione minimale con TMetric per il time tracking: avvio/stop timer, lista progetti e attività. Usato dal plugin `issue-time-tracking-workflow`.

```bash
# Installazione manuale
claude mcp add --scope user tmetric-minimal --env TMETRIC_API_TOKEN=<token> -- npx -y github:guglielmo/tmetric-minimal-mcp
```

---

### mcp-googledocs-server
**Progetto esterno** — copia locale di [`a-bonus/google-docs-mcp`](https://github.com/a-bonus/google-docs-mcp)

Integrazione con Google Docs e Google Drive: lettura, scrittura, formattazione, gestione commenti e file. Configurato localmente con credenziali OAuth Google (gitignorate).

Vedi [`mcp-googledocs-server/README.md`](./mcp-googledocs-server/README.md) per setup e configurazione.

---

## Aggiornare il submodule tmetric-minimal-mcp

```bash
git submodule update --remote mcp-servers/tmetric-minimal-mcp
git add mcp-servers/tmetric-minimal-mcp
git commit -m "chore: update tmetric-minimal-mcp submodule"
```

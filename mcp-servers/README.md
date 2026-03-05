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
Integrazione con Google Docs: lettura e scrittura di documenti tramite API Google.

Vedi [`mcp-googledocs-server/`](./mcp-googledocs-server/) per configurazione e credenziali.

---

### mcp-servers-management
Sistema di gestione per abilitare/disabilitare MCP server per progetto tramite file `.mcp.json` locali e comandi slash (`/mcp-list`, `/mcp-enable`, `/mcp-disable`, `/mcp-sync`).

Vedi [`mcp-servers-management/`](./mcp-servers-management/) per le istruzioni di setup.

---

## Aggiornare il submodule tmetric-minimal-mcp

```bash
git submodule update --remote mcp-servers/tmetric-minimal-mcp
git add mcp-servers/tmetric-minimal-mcp
git commit -m "chore: update tmetric-minimal-mcp submodule"
```

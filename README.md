# guglielmo-plugins

Marketplace di personalizzazioni per [Claude Code](https://claude.ai/code) sviluppate da Guglielmo Celata. Ogni plugin è installabile con un comando e include agenti, comandi slash e skill integrate.

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

## Licenza

MIT — libero utilizzo, modifica e ridistribuzione.

## Autore

Guglielmo Celata — [@guglielmo](https://github.com/guglielmo)

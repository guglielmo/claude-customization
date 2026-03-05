# skills/

Workspace di sviluppo per skill standalone da distribuire come `~/.claude/skills/`.

Le skill qui sviluppate usano la metodologia TDD documentata in `skills/CLAUDE.md`: ogni skill passa per le fasi RED (baseline senza skill), GREEN (skill minimale) e REFACTOR (chiusura dei loophole), con artefatti separati per ogni fase.

## Skill disponibili

| Skill | Descrizione |
|-------|-------------|
| [`issue-time-tracking`](./issue-time-tracking/) | Workflow per avviare/fermare il lavoro su issue con sincronizzazione GitLab/GitHub e TMetric |

> **Nota**: questa è la directory di **sviluppo** della skill `issue-time-tracking` (con artefatti TDD: test, baseline, risultati). La skill compilata finale (`SKILL.md`) viene copiata sia in `~/.claude/skills/` per uso diretto, sia nel plugin `issue-time-tracking-workflow` per distribuzione via marketplace.

## Deployment

```bash
cp skills/<nome>/SKILL.md ~/.claude/skills/<nome>.md
```

## Sviluppo nuove skill

Vedi [`CLAUDE.md`](./CLAUDE.md) per il workflow completo (metodologia TDD con `superpowers:writing-skills`).

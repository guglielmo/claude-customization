# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

Marketplace di Claude Code customizations sviluppate da Guglielmo Celata. Contiene plugin installabili con `/plugin install`, ognuno con agenti, comandi slash e skill integrate.

## Repository Structure

### plugins/
Plugin installabili dal marketplace. Ogni plugin ha la struttura standard:
```
plugins/<nome>/
├── .claude-plugin/plugin.json
├── agents/       ← agenti specializzati
├── commands/     ← slash commands utente
└── skills/       ← skill con auto-attivazione (directory <nome>/SKILL.md)
```

**Plugin attivi**:
- **semantic-release/** — workflow release semantica (3 fasi: analisi, docs, publish)
- **issue-time-tracking-workflow/** — sincronizzazione issue tracking e TMetric

**Sviluppo locale**: gli agenti in `plugins/<nome>/agents/` sono referenziati via symlink da `~/.claude/agents/` per uso immediato durante lo sviluppo. Modificare i file nel plugin, non i symlink.

### .claude-plugin/marketplace.json
Registro dei plugin distribuiti da questo marketplace (`guglielmo-plugins`).

## Development Workflow

### Aggiungere un nuovo plugin
1. Creare `plugins/<nome>/` con la struttura standard
2. Aggiungere la voce in `.claude-plugin/marketplace.json`
3. Per uso locale: creare symlink da `~/.claude/agents/<agente>.md` → `plugins/<nome>/agents/<agente>.md`

### Sviluppare o aggiornare una skill
Le skill vivono dentro il plugin in `plugins/<nome>/skills/<skill-name>/SKILL.md`.
Il workspace di sviluppo TDD (test-scenarios, baseline-results, ecc.) è nella stessa directory.
Usare `superpowers:writing-skills` per il ciclo RED-GREEN-REFACTOR.

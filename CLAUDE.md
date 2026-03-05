# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

Workspace per sviluppare e distribuire Claude Code customizations come plugin installabili dal marketplace. Contiene plugin con agenti, comandi e skill, oltre a skill standalone e MCP server.

## Repository Structure

### plugins/
Plugin installabili dal marketplace. Ogni plugin ha la struttura standard:
```
plugins/<nome>/
├── .claude-plugin/plugin.json
├── agents/       ← agenti specializzati
├── commands/     ← slash commands utente
└── skills/       ← skill con auto-attivazione
```

**Plugin attivi**:
- **depp-release/** — workflow release semantica (3 fasi: analisi, docs, publish)
- **issue-time-tracking-workflow/** — sincronizzazione issue tracking e TMetric

**Sviluppo locale**: gli agenti in `plugins/<nome>/agents/` sono referenziati via symlink da `~/.claude/agents/` per uso immediato durante lo sviluppo. Modificare i file nel plugin, non i symlink.

### skills/
Workspace per sviluppare skill standalone con metodologia TDD.

**Deployment**: Completed skills are deployed to `~/.claude/skills/`

**Process**: Each skill gets a directory with:
- `SKILL.md` - Final skill document
- `test-scenarios.md` - Pressure testing scenarios
- `baseline-results.md` - Agent behavior without skill
- `green-phase-results.md` - Agent behavior with skill
- `quality-checklist.md` - TDD verification

See `skills/CLAUDE.md` for detailed development workflow.

### mcp-servers/
MCP server implementations, including:
- **tmetric-minimal-mcp/** - TMetric time tracking integration
- **mcp-googledocs-server/** - Google Docs integration

**Note**: Some MCP servers may be git submodules or separate repositories.

## Development Workflow

### Aggiungere un nuovo plugin
1. Creare `plugins/<nome>/` con la struttura standard
2. Aggiungere la voce in `.claude-plugin/marketplace.json`
3. Per uso locale: creare symlink da `~/.claude/agents/<agente>.md` → `plugins/<nome>/agents/<agente>.md`

### Creating New Skills
1. Navigate to `skills/` directory
2. Use `superpowers:writing-skills` skill (TDD methodology)
3. Follow RED-GREEN-REFACTOR cycle
4. Deploy completed skill to `~/.claude/skills/`

### Developing MCP Servers
Each MCP server may have its own README and development instructions. Check individual server directories.

## Git Structure

This repository may contain git submodules for MCP servers that are developed in separate repositories. Use `git submodule update --init --recursive` after cloning.

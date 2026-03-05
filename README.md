# Claude Code Customizations

Workspace for developing and distributing [Claude Code](https://claude.ai/code) customizations: plugin, skill, agenti e MCP server.

## Plugins

### depp-release

Workflow di release semantica in 3 fasi con gestione completa di versioning, documentazione e pubblicazione.

**Comandi disponibili**:
- `/release` — pipeline completa (analisi → documentazione → pubblicazione)
- `/prepare-release` — fase 1: analizza i commit e raccomanda MAJOR/MINOR/PATCH
- `/prepare-docs` — fase 2: aggiorna CHANGELOG, README, STATUS, CLAUDE.md e version files
- `/publish-release` — fase 3: crea il tag annotato e fa push su remoto

**Skill inclusa**: `semantic-release` — si auto-attiva quando si parla di release o versioning.

**Agenti inclusi**: `prepare-release`, `prepare-docs`, `publish-release`, `changelog-writer`, `readme-writer`, `status-writer`, `claudemd-writer`, `contributing-writer`.

### issue-time-tracking-workflow

Sincronizzazione automatica dello stato delle issue e del time logging tra GitLab/GitHub e TMetric.

## Contents

### 📚 Skills

Custom Claude Code skills sviluppate con metodologia TDD.

**Current Skills**:
- **issue-time-tracking** — sincronizzazione issue tracking e time logging su GitLab/GitHub e TMetric

See [`skills/`](./skills/) for the development workspace and methodology.

### 🔌 MCP Servers

Model Context Protocol server implementations for extending Claude Code capabilities.

**Current Servers**:
- **tmetric-minimal-mcp** — TMetric time tracking integration
- **mcp-googledocs-server** — Google Docs integration

See [`mcp-servers/`](./mcp-servers/) for individual server documentation.

## Using These Customizations

### Option 1: Install as Plugin (Recommended)

```bash
# Add the marketplace
/plugin marketplace add guglielmo/claude-customization

# Install il workflow di release
/plugin install depp-release@guglielmo-claude-customizations

# Install il workflow di issue tracking
/plugin install issue-time-tracking-workflow@guglielmo-claude-customizations
```

### Option 2: Manual Installation

#### Skills

To use a skill from this repository:

1. Copy the `SKILL.md` file to your personal skills directory:
   ```bash
   cp skills/skill-name/SKILL.md ~/.claude/skills/skill-name.md
   ```

2. The skill will be available in your next Claude Code session

#### MCP Servers

Each MCP server has its own installation and configuration instructions. See the individual server directories for details.

## Development

This repository uses a structured TDD approach for skill development:

- **RED Phase**: Test baseline agent behavior without the skill
- **GREEN Phase**: Write minimal skill to address failures
- **REFACTOR Phase**: Close loopholes and test until bulletproof

See [`skills/CLAUDE.md`](./skills/CLAUDE.md) for the complete development workflow.

## License

MIT License - Feel free to use, modify, and share these customizations.

## Author

Guglielmo Celata - [@guglielmo](https://github.com/guglielmo)

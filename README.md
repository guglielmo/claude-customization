# Claude Code Customizations

Personal workspace for developing [Claude Code](https://claude.ai/code) customizations, including skills and MCP servers.

## Contents

### 📚 Skills

Custom Claude Code skills developed using TDD methodology for process documentation.

**Current Skills**:
- **issue-time-tracking** - Automates synchronized issue status tracking and time logging across GitLab/GitHub and TMetric

See [`skills/`](./skills/) for the development workspace and methodology.

### 🔌 MCP Servers

Model Context Protocol server implementations for extending Claude Code capabilities.

**Current Servers**:
- **tmetric-minimal-mcp** - TMetric time tracking integration
- **mcp-googledocs-server** - Google Docs integration

See [`mcp-servers/`](./mcp-servers/) for individual server documentation.

## Using These Customizations

### Option 1: Install as Plugin (Recommended)

Install everything with a single command:

```bash
# Add the marketplace
/plugin marketplace add guglielmo/claude-customization

# Install the plugin
/plugin install issue-time-tracking-workflow
```

This installs both the skill and the MCP server automatically.

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

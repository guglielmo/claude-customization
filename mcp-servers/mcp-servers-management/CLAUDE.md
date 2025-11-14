# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an MCP (Model Context Protocol) server management system that provides a clean way to enable/disable MCP servers per project using local `.mcp.json` files and custom Claude Code slash commands.

## Core Components

- **Management Script**: `~/.claude/mcp-servers/manage-mcp.sh` - Central bash script for managing server states
- **Server Definitions**: `~/.claude/mcp-servers/available/*.json` - Individual MCP server configuration files
- **Enabled Servers**: `~/.claude/mcp-servers/enabled/` - Symlinks to active server configurations
- **Slash Commands**: `.claude/commands/mcp-*.md` - Claude Code integration commands

## Key Commands

### Setup Commands
```bash
# Create directory structure
mkdir -p ~/.claude/mcp-servers/available ~/.claude/mcp-servers/enabled

# Make management script executable
chmod +x ~/.claude/mcp-servers/manage-mcp.sh

# Create project command directory
mkdir -p .claude/commands
```

### Management Commands
```bash
# List available and enabled servers
~/.claude/mcp-servers/manage-mcp.sh list

# Enable a server
~/.claude/mcp-servers/manage-mcp.sh enable <server-name>

# Disable a server  
~/.claude/mcp-servers/manage-mcp.sh disable <server-name>

# Generate .mcp.json from enabled servers
~/.claude/mcp-servers/manage-mcp.sh sync
```

### Claude Code Slash Commands
- `/mcp-list` - Show available and enabled servers
- `/mcp-enable <server-name>` - Enable server and sync config
- `/mcp-disable <server-name>` - Disable server and sync config  
- `/mcp-sync` - Generate .mcp.json from enabled servers

## Architecture

The system uses a symlink-based approach:
1. Server definitions stored in `~/.claude/mcp-servers/available/`
2. Enabled servers are symlinks in `~/.claude/mcp-servers/enabled/`
3. The `sync` command combines enabled servers into `.mcp.json`
4. Each project gets its own `.mcp.json` with only needed servers

## Server Definition Format

Server configs are JSON files with MCP server definitions:
```json
{
  "server-name": {
    "command": "npx",
    "args": ["-y", "@package/server"],
    "env": {
      "API_KEY": "value"
    }
  }
}
```

## Workflow

1. Create server definitions in `~/.claude/mcp-servers/available/`
2. Use slash commands to enable needed servers per project
3. Run `sync` to generate project-specific `.mcp.json`
4. Restart Claude Code to load new configuration

## Important Notes

- Always run `sync` after enabling/disabling servers
- Restart Claude Code after configuration changes
- Server definitions are reusable across projects
- Each project maintains its own `.mcp.json` file
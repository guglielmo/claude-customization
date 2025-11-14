# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

Personal Claude Code customization workspace containing:
- **skills/** - Custom Claude Code skills development workspace
- **mcp-servers/** - MCP (Model Context Protocol) server implementations

This repository serves as both a development workspace and a shareable collection of Claude customizations.

## Repository Structure

### skills/
Workspace for developing custom Claude Code skills using TDD methodology.

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
- Other server implementations

**Note**: Some MCP servers may be git submodules or separate repositories.

## Development Workflow

### Creating New Skills
1. Navigate to `skills/` directory
2. Use `superpowers:writing-skills` skill (TDD methodology)
3. Follow RED-GREEN-REFACTOR cycle
4. Deploy completed skill to `~/.claude/skills/`

### Developing MCP Servers
Each MCP server may have its own README and development instructions. Check individual server directories.

### Publishing Skills
Skills developed here are personal customizations. To share:
1. Keep development artifacts in this repository
2. Users can copy `SKILL.md` files to their own `~/.claude/skills/`
3. Consider contributing broadly-useful skills to upstream projects

## Git Structure

This repository may contain git submodules for MCP servers that are developed in separate repositories. Use `git submodule update --init --recursive` after cloning.

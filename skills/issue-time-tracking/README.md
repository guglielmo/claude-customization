# Issue Time Tracking Skill

Automates synchronized issue status tracking and time logging across GitLab/GitHub and TMetric.

## Overview

Single command workflow:
- **"start working on issue #123"** - starts timer, updates issue status
- **"stop working on issue #123 - fixed the bug"** - stops timer, logs time, adds comment, updates status

## Features

- **Strict single-timer enforcement** - blocks starting new work when timer active
- **Platform auto-detection** - GitLab vs GitHub from git remotes
- **Smart TMetric project mapping** - ranked suggestions, saved to git config
- **Natural language parsing** - extracts work summaries from commands
- **Graceful error handling** - asks before rolling back partial failures

## Dependencies

### Required MCP Server

This skill **requires** the [tmetric-minimal-mcp](../../mcp-servers/tmetric-minimal-mcp/) MCP server to be installed and configured.

**Installation**:
1. Navigate to the MCP server directory: `cd ../../mcp-servers/tmetric-minimal-mcp/`
2. Follow the installation instructions in that directory's README
3. Configure Claude Code to use the MCP server (add to `~/.claude/config.json`)

### Required CLI Tools

- **glab** (for GitLab) or **gh** (for GitHub) - must be installed and authenticated
- **git** - for repository context and config storage

## Installation

1. **Install dependencies** (see above)

2. **Copy skill to your skills directory**:
   ```bash
   cp SKILL.md ~/.claude/skills/issue-time-tracking.md
   ```

3. **Verify** in next Claude Code session:
   ```
   You: start working on issue #123
   Claude: [follows issue-time-tracking workflow]
   ```

## Usage

See `SKILL.md` for complete workflow documentation.

### Quick Examples

```
# Start work
"start working on issue #456"

# Work with summary
"stop working on #456 - implemented auth flow"

# Pause/resume
"pause timer"
"resume timer"

# Check status
"what am I working on?"
```

## Development

This skill was developed using TDD methodology for documentation. See the development artifacts:

- `test-scenarios.md` - Pressure testing scenarios
- `baseline-results.md` - Agent behavior WITHOUT skill
- `green-phase-results.md` - Agent behavior WITH skill
- `quality-checklist.md` - TDD verification

## License

MIT License

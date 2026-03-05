# Issue Time Tracking Workflow Plugin

Complete workflow automation for issue tracking and time logging across GitLab/GitHub and TMetric.

## What's Included

This plugin bundles:

- **issue-time-tracking skill** - Automated workflow for starting/stopping work on issues
- **tmetric-minimal MCP server** - TMetric time tracking integration

## Installation

### Option 1: From Marketplace (Recommended)

```bash
# Add the marketplace
/plugin marketplace add guglielmo/claude-customization

# Install the plugin
/plugin install issue-time-tracking-workflow@guglielmo-plugins
```

When prompted for `TMETRIC_API_TOKEN`, enter your TMetric API token.

### Option 2: Manual Installation

1. Install the skill:
   ```bash
   cp skills/issue-time-tracking.md ~/.claude/skills/issue-time-tracking.md
   ```

2. Install the MCP server:
   ```bash
   claude mcp add --scope user tmetric-minimal --env TMETRIC_API_TOKEN=your_token_here -- npx -y github:guglielmo/tmetric-minimal-mcp
   ```

## Getting Your TMetric API Token

1. Log in to [TMetric](https://app.tmetric.com/)
2. Go to Settings → Integrations → API
3. Generate a new API token
4. Copy and use it during plugin installation

## Usage

Once installed, use natural language commands:

```
# Start work
"start working on issue #123"

# Stop work with summary
"stop working on #123 - fixed the authentication bug"

# Pause/resume
"pause timer"
"resume timer"

# Check status
"what am I working on?"
```

## Features

- ✅ **Single-timer enforcement** - Prevents accidentally running multiple timers
- ✅ **Platform auto-detection** - Works with both GitLab and GitHub
- ✅ **Smart project mapping** - Suggests TMetric projects based on repository context
- ✅ **Natural language** - Extracts work summaries from your commands
- ✅ **Error recovery** - Asks before rolling back partial operations

## Requirements

- **glab** (for GitLab) or **gh** (for GitHub) CLI installed and authenticated
- **TMetric account** with API access
- **git** for repository context

## Documentation

- [Skill Documentation](../../skills/issue-time-tracking/)
- [MCP Server Documentation](../../mcp-servers/tmetric-minimal-mcp/)
- [Development Process](../../skills/issue-time-tracking/test-scenarios.md)

## License

MIT License

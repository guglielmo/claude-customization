# Claude Code MCP Server Management System

This system provides a clean way to enable/disable MCP servers per project using local `.mcp.json` files and custom Claude Code slash commands.

## 1. Directory Structure Setup

```bash
# Create the directory structure
mkdir -p ~/.claude/mcp-servers/available
mkdir -p ~/.claude/mcp-servers/enabled
```

Expected structure:
```
~/.claude/mcp-servers/
├── manage-mcp.sh           # Management script
├── available/              # Server definitions
│   ├── gitlab.json
│   ├── github.json
│   └── web-search.json
└── enabled/               # Symlinks to enabled servers
    ├── gitlab.json -> ../available/gitlab.json
    └── github.json -> ../available/github.json
```

## 2. Management Script

Create `~/.claude/mcp-servers/manage-mcp.sh`:

```bash
#!/bin/bash
set -e

MCP_DIR="$HOME/.claude/mcp-servers"
AVAILABLE_DIR="$MCP_DIR/available"
ENABLED_DIR="$MCP_DIR/enabled"

# Ensure directories exist
mkdir -p "$AVAILABLE_DIR" "$ENABLED_DIR"

case "$1" in
  enable)
    if [ -z "$2" ]; then
      echo "Usage: manage-mcp.sh enable <server-name>"
      exit 1
    fi
    if [ ! -f "$AVAILABLE_DIR/$2.json" ]; then
      echo "Error: Server '$2' not found in available servers"
      echo "Available servers:"
      ls "$AVAILABLE_DIR"/*.json 2>/dev/null | xargs -n1 basename | sed 's/.json$//' || echo "  (none)"
      exit 1
    fi
    ln -sf "../available/$2.json" "$ENABLED_DIR/$2.json"
    echo "✅ Enabled server: $2"
    ;;
    
  disable)
    if [ -z "$2" ]; then
      echo "Usage: manage-mcp.sh disable <server-name>"
      exit 1
    fi
    if [ -L "$ENABLED_DIR/$2.json" ]; then
      rm -f "$ENABLED_DIR/$2.json"
      echo "❌ Disabled server: $2"
    else
      echo "Server '$2' was not enabled"
    fi
    ;;
    
  list)
    echo "📦 Available servers:"
    ls "$AVAILABLE_DIR"/*.json 2>/dev/null | xargs -n1 basename | sed 's/^/  /' | sed 's/.json$//' || echo "  (none)"
    echo ""
    echo "✅ Enabled servers:"
    ls "$ENABLED_DIR"/*.json 2>/dev/null | xargs -n1 basename | sed 's/^/  /' | sed 's/.json$//' || echo "  (none)"
    ;;
    
  sync)
    # Combine all enabled servers into .mcp.json
    if [ ! -d "$ENABLED_DIR" ] || [ -z "$(ls -A "$ENABLED_DIR" 2>/dev/null)" ]; then
      echo "No servers enabled. Removing .mcp.json"
      rm -f .mcp.json
      exit 0
    fi
    
    echo '{"mcpServers":{' > .mcp.json
    first=true
    for file in "$ENABLED_DIR"/*.json; do
      [ -e "$file" ] || continue
      if [ "$first" = true ]; then
        first=false
      else
        echo "," >> .mcp.json
      fi
      # Extract the server definition (without outer wrapper if it exists)
      if jq -e '.mcpServers' "$file" > /dev/null 2>&1; then
        # File has mcpServers wrapper
        jq -r '.mcpServers | to_entries[] | "\"\(.key)\": \(.value)"' "$file" >> .mcp.json
      else
        # File is direct server definition
        jq -r 'to_entries[] | "\"\(.key)\": \(.value)"' "$file" >> .mcp.json
      fi
    done
    echo '}}' >> .mcp.json
    
    enabled_count=$(ls "$ENABLED_DIR"/*.json 2>/dev/null | wc -l)
    echo "🔄 Generated .mcp.json with $enabled_count server(s)"
    echo "⚠️  Restart Claude Code to apply changes"
    ;;
    
  *)
    echo "Usage: manage-mcp.sh {enable|disable|list|sync} [server-name]"
    echo ""
    echo "Commands:"
    echo "  enable <name>   Enable an MCP server"  
    echo "  disable <name>  Disable an MCP server"
    echo "  list            Show available and enabled servers"
    echo "  sync            Generate .mcp.json from enabled servers"
    exit 1
    ;;
esac
```

Make the script executable:
```bash
chmod +x ~/.claude/mcp-servers/manage-mcp.sh
```

## 3. Claude Code Slash Commands

Create the commands directory in your project:
```bash
mkdir -p .claude/commands
```

### 3.1. Enable MCP Server Command

Create `.claude/commands/mcp-enable.md`:

```markdown
# Enable MCP Server

Enable the specified MCP server and sync the configuration.

Usage: /mcp-enable <server-name>

Arguments: $ARGUMENTS

Execute these commands:

```bash
~/.claude/mcp-servers/manage-mcp.sh enable $ARGUMENTS
~/.claude/mcp-servers/manage-mcp.sh sync
```
```

### 3.2. Disable MCP Server Command

Create `.claude/commands/mcp-disable.md`:

```markdown
# Disable MCP Server

Disable the specified MCP server and sync the configuration.

Usage: /mcp-disable <server-name>

Arguments: $ARGUMENTS

Execute these commands:

```bash
~/.claude/mcp-servers/manage-mcp.sh disable $ARGUMENTS
~/.claude/mcp-servers/manage-mcp.sh sync
```
```

### 3.3. List MCP Servers Command

Create `.claude/commands/mcp-list.md`:

```markdown
# List MCP Servers

Show all available and currently enabled MCP servers.

Execute this command:

```bash
~/.claude/mcp-servers/manage-mcp.sh list
```
```

### 3.4. Sync MCP Configuration Command

Create `.claude/commands/mcp-sync.md`:

```markdown
# Sync MCP Configuration

Generate .mcp.json file from currently enabled servers.

Execute this command:

```bash
~/.claude/mcp-servers/manage-mcp.sh sync
```
```

## 4. Server Definition Examples

### 4.1. GitLab Server

Create `~/.claude/mcp-servers/available/gitlab.json`:

```json
{
  "gitlab": {
    "command": "npx",
    "args": ["-y", "@zereight/mcp-gitlab"],
    "env": {
      "GITLAB_PERSONAL_ACCESS_TOKEN": "glpat-xxxxx",
      "GITLAB_API_URL": "https://gitlab.openpolis.io/api/v4"
    }
  }
}
```

### 4.2. GitHub Server

Create `~/.claude/mcp-servers/available/github.json`:

```json
{
  "github": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-github"],
    "env": {
      "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp-xxxxx"
    }
  }
}
```

### 4.3. Web Search Server

Create `~/.claude/mcp-servers/available/web-search.json`:

```json
{
  "web-search": {
    "command": "npx",
    "args": ["-y", "@modelcontextprotocol/server-brave-search"],
    "env": {
      "BRAVE_API_KEY": "your-brave-api-key"
    }
  }
}
```

## 5. Usage Instructions

### 5.1. Initial Setup

1. Run the directory setup commands
2. Create the management script and make it executable
3. Create your server definition files in `~/.claude/mcp-servers/available/`
4. Create the slash command files in your project's `.claude/commands/` directory

### 5.2. Using the System

In Claude Code, use these slash commands:

```bash
# List all servers
/mcp-list

# Enable specific servers
/mcp-enable gitlab
/mcp-enable github

# Disable a server
/mcp-disable gitlab

# Manually sync if needed
/mcp-sync
```

### 5.3. Manual Usage (without Claude Code)

You can also use the script directly:

```bash
# List servers
~/.claude/mcp-servers/manage-mcp.sh list

# Enable servers
~/.claude/mcp-servers/manage-mcp.sh enable gitlab
~/.claude/mcp-servers/manage-mcp.sh enable github

# Sync configuration
~/.claude/mcp-servers/manage-mcp.sh sync

# Disable a server
~/.claude/mcp-servers/manage-mcp.sh disable gitlab
~/.claude/mcp-servers/manage-mcp.sh sync
```

## 6. Workflow Example

```bash
# 1. Set up a new project
cd /path/to/my-blog-project
mkdir -p .claude/commands

# 2. Copy the slash command files to .claude/commands/

# 3. In Claude Code, enable the servers you need:
/mcp-enable gitlab
# This will:
# - Create symlink: ~/.claude/mcp-servers/enabled/gitlab.json -> ../available/gitlab.json  
# - Generate .mcp.json in current directory
# - Show success message

# 4. Enable additional servers
/mcp-enable web-search

# 5. List what's enabled
/mcp-list

# 6. Restart Claude Code to load the new servers

# 7. Verify servers are loaded
claude mcp list
```

## 7. Benefits

- **Project isolation**: Each project has its own `.mcp.json` with only needed servers
- **Easy enable/disable**: Simple slash commands in Claude Code
- **Reusable definitions**: Store server configs once, use in multiple projects
- **Version control friendly**: Can commit `.mcp.json` to share with team, or add to `.gitignore`
- **No bloated global config**: `~/.claude.json` stays clean
- **Team sharing**: Share the slash commands and server definitions with your team

## 8. Optional: Add to .gitignore

If you want to keep MCP configuration local to each developer:

```bash
echo ".mcp.json" >> .gitignore
```

Or commit it to share with your team:

```bash
git add .mcp.json .claude/
git commit -m "Add MCP server configuration and management commands"
```
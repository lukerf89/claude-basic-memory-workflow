# Configuration Files

This directory contains sample configuration files for setting up the Claude + Basic Memory workflow.

## Files

### `claude_desktop_config.json`
Basic configuration for Claude Desktop when you don't have any existing MCP servers configured.

**Location**: `~/Library/Application Support/Claude/claude_desktop_config.json`

**Usage**:
1. Copy this file to the Claude Desktop config location
2. Replace `yourusername` with your actual username
3. Restart Claude Desktop

### `claude_desktop_config_with_existing.json`
Example configuration showing how to add Basic Memory to an existing Claude Desktop configuration that already has other settings and MCP servers.

**Usage**:
1. Open your existing `claude_desktop_config.json`
2. Add the `basic-memory` section to your existing `mcpServers` object
3. Keep all your existing configuration
4. Replace `yourusername` with your actual username

## Important Notes

### Finding Your Username
```bash
whoami
```

### Finding the Full Path to uvx
```bash
which uvx
```

### Verifying Configuration
After setting up the configuration:

1. Restart Claude Desktop completely
2. Start a new conversation
3. Ask: "Can you create a note using Basic Memory?"

If successful, Claude Desktop should be able to create and read notes through Basic Memory.

## Troubleshooting

### Config File Not Working
- Ensure the filename is exactly `claude_desktop_config.json`
- Check that the JSON syntax is valid (no trailing commas, proper quotes)
- Verify the full path to uvx is correct
- Make sure you've restarted Claude Desktop after changes

### Permission Issues
```bash
# Make sure uvx is executable
chmod +x ~/.local/bin/uvx

# Check if uvx works from command line
uvx basic-memory --version
```

### Path Issues
If uvx is not found, add it to your PATH:

```bash
# Add to ~/.zshrc or ~/.bash_profile
export PATH="$HOME/.local/bin:$PATH"

# Reload your shell configuration
source ~/.zshrc  # or ~/.bash_profile
```
# Troubleshooting Guide

This guide covers common issues and solutions for the Claude + Basic Memory collaboration workflow.

## Table of Contents
- [Installation Issues](#installation-issues)
- [Claude Desktop Configuration](#claude-desktop-configuration)
- [Basic Memory CLI Issues](#basic-memory-cli-issues)
- [Communication Problems](#communication-problems)
- [Performance Issues](#performance-issues)
- [Obsidian Integration](#obsidian-integration)

## Installation Issues

### Basic Memory Installation Fails

**Problem**: Installation script fails or uvx not found after installation.

**Solution**:
```bash
# Check if UV is installed
uv --version

# If not, install UV first
curl -LsSf https://astral.sh/uv/install.sh | sh

# Add to PATH if needed
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Install Basic Memory manually
uv tool install basic-memory
```

### Permission Denied Errors

**Problem**: `Permission denied` when running uvx or basic-memory commands.

**Solution**:
```bash
# Fix permissions
chmod +x ~/.local/bin/uvx
chmod +x ~/.local/bin/uv

# If still having issues, reinstall
uv tool uninstall basic-memory
uv tool install basic-memory
```

### Command Not Found

**Problem**: `uvx: command not found` or `basic-memory: command not found`.

**Solution**:
```bash
# Check if uvx exists
ls -la ~/.local/bin/uvx

# If exists but not in PATH, add to shell profile
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# If doesn't exist, reinstall
curl -LsSf https://basicmemory.com/install/latest.sh | sh
```

## Claude Desktop Configuration

### Claude Desktop Doesn't See Basic Memory

**Problem**: Claude Desktop doesn't show Basic Memory tools after configuration.

**Diagnostic Steps**:
```bash
# 1. Check config file location and name
ls -la "~/Library/Application Support/Claude/claude_desktop_config.json"

# 2. Check config file content
cat "~/Library/Application Support/Claude/claude_desktop_config.json"

# 3. Test uvx path
/Users/$(whoami)/.local/bin/uvx basic-memory --version

# 4. Test MCP server directly
echo '{"jsonrpc": "2.0", "method": "initialize", "params": {"protocolVersion": "2024-11-05", "capabilities": {"roots": {"listChanged": true}, "sampling": {}}, "clientInfo": {"name": "test", "version": "1.0.0"}}, "id": 1}' | /Users/$(whoami)/.local/bin/uvx basic-memory mcp
```

**Common Solutions**:

1. **Wrong config filename**:
   ```bash
   # Should be exactly this name
   mv "~/Library/Application Support/Claude/config.json" \
      "~/Library/Application Support/Claude/claude_desktop_config.json"
   ```

2. **Incorrect uvx path**:
   ```bash
   # Find correct path
   which uvx
   
   # Update config with correct path
   # Edit claude_desktop_config.json and replace the command path
   ```

3. **JSON syntax error**:
   ```bash
   # Validate JSON
   cat "~/Library/Application Support/Claude/claude_desktop_config.json" | python -m json.tool
   ```

4. **Must restart Claude Desktop**:
   - Completely quit Claude Desktop (Cmd+Q)
   - Reopen Claude Desktop
   - Start a new conversation

### Configuration File Gets Overwritten

**Problem**: Changes to `claude_desktop_config.json` are lost after restarting Claude Desktop.

**Solution**:
```bash
# Check file permissions
ls -la "~/Library/Application Support/Claude/claude_desktop_config.json"

# If needed, make it writable
chmod 644 "~/Library/Application Support/Claude/claude_desktop_config.json"

# Backup your config
cp "~/Library/Application Support/Claude/claude_desktop_config.json" \
   "~/Library/Application Support/Claude/claude_desktop_config.json.backup"
```

## Basic Memory CLI Issues

### Database Errors

**Problem**: SQLite database corruption or connection errors.

**Solution**:
```bash
# Reset the database
uvx basic-memory reset

# Sync files with database
uvx basic-memory sync

# Check status
uvx basic-memory status
```

### Project Not Found

**Problem**: `Project 'obsidian' not found` or similar errors.

**Solution**:
```bash
# List available projects
uvx basic-memory project list

# Add missing project
uvx basic-memory project add obsidian "/path/to/your/obsidian/vault"

# Set as default
uvx basic-memory project default obsidian
```

### Note Creation Fails

**Problem**: Cannot create notes via CLI.

**Diagnostic**:
```bash
# Check current project
uvx basic-memory project info

# Test basic functionality
uvx basic-memory tool write-note \
  --title "Test Note" \
  --folder "test" \
  --content "Test content"

# Check if note was created
uvx basic-memory tool read-note "Test Note"
```

## Communication Problems

### Notes Not Syncing Between Interfaces

**Problem**: Claude Desktop can't see notes created by Claude Code or vice versa.

**Diagnostic Steps**:
```bash
# 1. Check if both are using same project
uvx basic-memory project list

# 2. Sync database with files
uvx basic-memory sync

# 3. Check for file system issues
ls -la "$(uvx basic-memory project info | grep 'Project path' | cut -d: -f2 | xargs)"
```

**Solutions**:

1. **Different projects**:
   ```bash
   # Set same project for both
   uvx basic-memory project default your-project-name
   ```

2. **Sync issues**:
   ```bash
   # Force sync
   uvx basic-memory sync --force
   ```

3. **File permissions**:
   ```bash
   # Fix permissions on notes directory
   chmod -R 755 "/path/to/your/notes/directory"
   ```

### Delayed Note Appearance

**Problem**: Notes created in one interface take time to appear in the other.

**Solution**:
This is normal behavior. Basic Memory syncs periodically. To force immediate sync:
```bash
uvx basic-memory sync
```

## Performance Issues

### Slow Note Creation/Reading

**Problem**: Basic Memory operations are slow.

**Diagnostic**:
```bash
# Check database size and status
uvx basic-memory status

# Check available disk space
df -h ~/

# Check for large files in notes directory
find "/path/to/notes" -size +10M -type f
```

**Solutions**:

1. **Large database**:
   ```bash
   # Archive old notes
   uvx basic-memory tool write-note \
     --title "Archive $(date +%Y-%m)" \
     --folder "5-Archives" \
     --content "Archived notes from $(date)"
   ```

2. **Too many files**:
   - Consider organizing notes into subfolders
   - Archive completed projects

### Memory Usage Issues

**Problem**: Basic Memory using too much memory.

**Solution**:
```bash
# Restart Basic Memory MCP server
# (restart Claude Desktop to restart the MCP server)

# Check for memory leaks
ps aux | grep basic-memory
```

## Obsidian Integration

### Obsidian Can't See Notes

**Problem**: Notes created by Basic Memory don't appear in Obsidian.

**Solution**:
```bash
# Check Obsidian vault path
uvx basic-memory project info

# Ensure path matches your Obsidian vault
uvx basic-memory project add obsidian "/correct/path/to/obsidian/vault"
uvx basic-memory project default obsidian

# Force sync
uvx basic-memory sync

# In Obsidian, try refreshing (Cmd+R) or reopening the vault
```

### iCloud Sync Issues

**Problem**: Notes not syncing across devices via iCloud.

**Solution**:
1. **Check iCloud sync status** in System Preferences
2. **Ensure Obsidian vault is in iCloud folder**:
   ```bash
   # Correct path format for iCloud Obsidian
   uvx basic-memory project add obsidian \
     "~/Library/Mobile Documents/iCloud~md~obsidian/Documents/YourVaultName"
   ```
3. **Force iCloud sync** by modifying a file in Finder

### Obsidian File Conflicts

**Problem**: Obsidian creates conflict copies of files.

**Prevention**:
- Use descriptive, unique note titles
- Avoid editing the same note simultaneously in multiple apps
- Let one interface (Claude Desktop or Claude Code) be the primary editor

**Resolution**:
```bash
# Find conflict files
find "/path/to/vault" -name "*conflicted copy*"

# Review and merge manually, then delete conflicts
```

## General Debugging

### Enable Verbose Logging

```bash
# Run Basic Memory with debug output
uvx basic-memory --help  # Check for debug flags

# For MCP server debugging, check Claude Desktop logs
# Location varies by system
```

### Check System Requirements

```bash
# Check macOS version
sw_vers

# Check available disk space
df -h

# Check Python version (used by Basic Memory)
python3 --version
```

### Reset Everything

If all else fails, complete reset:

```bash
# 1. Stop Claude Desktop
# 2. Remove Basic Memory
uv tool uninstall basic-memory

# 3. Remove configuration
rm "~/Library/Application Support/Claude/claude_desktop_config.json"

# 4. Remove Basic Memory data (CAUTION: This deletes all notes!)
rm -rf ~/.local/share/basic-memory  # or wherever data is stored

# 5. Reinstall following setup guide
curl -LsSf https://basicmemory.com/install/latest.sh | sh
```

## Getting Help

### Log Collection

When reporting issues, include:

```bash
# Basic Memory version
uvx basic-memory --version

# System info
uname -a
sw_vers  # macOS only

# Project info
uvx basic-memory project list
uvx basic-memory project info

# Configuration
cat "~/Library/Application Support/Claude/claude_desktop_config.json"
```

### Where to Report Issues

1. **Basic Memory issues**: https://github.com/basic-memory/basic-memory/issues
2. **This workflow issues**: Create issue in this repository
3. **Claude Desktop issues**: Anthropic support
4. **Claude Code issues**: Anthropic support

### Community Help

- GitHub Discussions for this repository
- Basic Memory Discord/community channels
- Stack Overflow (tag with relevant tools)

---

**Remember**: Most issues are resolved by ensuring the correct file paths, restarting Claude Desktop after configuration changes, and verifying that Basic Memory CLI works independently before testing the integration.
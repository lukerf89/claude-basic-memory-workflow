# Complete Setup Guide

This guide will walk you through setting up the Claude Code + Claude Desktop collaboration workflow using Basic Memory.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Step 1: Install Basic Memory](#step-1-install-basic-memory)
- [Step 2: Configure Basic Memory](#step-2-configure-basic-memory)
- [Step 3: Configure Claude Desktop](#step-3-configure-claude-desktop)
- [Step 4: Test the Setup](#step-4-test-the-setup)
- [Step 5: Create Folder Structure](#step-5-create-folder-structure)
- [Verification](#verification)
- [Next Steps](#next-steps)

## Prerequisites

- **macOS** (primary support - Linux/Windows coming soon)
- **Claude Desktop** installed and working
- **Claude Code** CLI access
- **Obsidian** (optional but recommended for enhanced note-taking)

## Step 1: Install Basic Memory

### Option 1: Single Command Installation (Recommended)
```bash
curl -LsSf https://basicmemory.com/install/latest.sh | sh
```

This command will:
- Install UV package manager (if not present)
- Install Basic Memory
- Set up the basic configuration

### Option 2: Manual Installation
```bash
# Install UV first (if not installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install Basic Memory
uv tool install basic-memory
```

### Verify Installation
```bash
uvx basic-memory --version
```
You should see something like: `Basic Memory version: 0.14.2`

## Step 2: Configure Basic Memory

### Default Setup
By default, Basic Memory creates a project at `~/basic-memory`. This works fine, but we recommend integrating with Obsidian for a better experience.

### Obsidian Integration (Recommended)

1. **Create or locate your Obsidian vault**:
   ```bash
   # If you don't have Obsidian, you can still use a regular folder
   mkdir -p ~/Documents/KnowledgeBase
   ```

2. **Add a new Basic Memory project**:
   ```bash
   # Replace with your actual Obsidian vault path
   uvx basic-memory project add obsidian "~/Documents/ObsidianVault"
   ```

3. **Set as default project**:
   ```bash
   uvx basic-memory project default obsidian
   ```

4. **Verify the setup**:
   ```bash
   uvx basic-memory project list
   ```

### Alternative: Use iCloud Obsidian
If you use Obsidian with iCloud sync:
```bash
uvx basic-memory project add obsidian "~/Library/Mobile Documents/iCloud~md~obsidian/Documents/YourVaultName"
uvx basic-memory project default obsidian
```

## Step 3: Configure Claude Desktop

### Locate the Configuration File
The Claude Desktop configuration file should be at:
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

### Create the Configuration

1. **Find the full path to uvx**:
   ```bash
   which uvx
   ```
   This will typically return: `/Users/yourusername/.local/bin/uvx`

2. **Create or edit the configuration file**:
   ```bash
   # Create the file if it doesn't exist
   touch "~/Library/Application Support/Claude/claude_desktop_config.json"
   ```

3. **Add the Basic Memory configuration**:
   ```json
   {
     "mcpServers": {
       "basic-memory": {
         "command": "/Users/yourusername/.local/bin/uvx",
         "args": ["basic-memory", "mcp"]
       }
     }
   }
   ```

   **Important**: Replace `yourusername` with your actual username, or use the full path from step 1.

### If You Have Existing Configuration
If your `claude_desktop_config.json` already has content, add the Basic Memory server to the existing `mcpServers` object:

```json
{
  "darkMode": "dark",
  "otherSettings": "...",
  "mcpServers": {
    "existing-server": {
      "command": "some-command",
      "args": ["arg1", "arg2"]
    },
    "basic-memory": {
      "command": "/Users/yourusername/.local/bin/uvx",
      "args": ["basic-memory", "mcp"]
    }
  }
}
```

## Step 4: Test the Setup

### Test Basic Memory CLI (Claude Code)
```bash
# Test creating a note
uvx basic-memory tool write-note \
  --title "Setup Test" \
  --folder "test" \
  --content "This is a test note created during setup."

# Test reading the note
uvx basic-memory tool read-note "Setup Test"
```

### Test Claude Desktop Integration

1. **Restart Claude Desktop completely** (Cmd+Q to quit, then reopen)

2. **Start a new conversation in Claude Desktop**

3. **Test Basic Memory access**:
   Ask Claude Desktop: "Can you create a note called 'Claude Desktop Test' in a folder called 'test' with the content 'Hello from Claude Desktop!'?"

4. **Verify the note was created**:
   ```bash
   uvx basic-memory tool read-note "Claude Desktop Test"
   ```

### Test Inter-Claude Communication

1. **In Claude Code**, create a message:
   ```bash
   uvx basic-memory tool write-note \
     --title "Message from Claude Code" \
     --folder "coordination" \
     --content "Hello Claude Desktop! This message was created by Claude Code."
   ```

2. **In Claude Desktop**, ask: "Can you read the note 'Message from Claude Code' and respond with a note called 'Response from Claude Desktop'?"

3. **In Claude Code**, read the response:
   ```bash
   uvx basic-memory tool read-note "Response from Claude Desktop"
   ```

## Step 5: Create Folder Structure

Set up a PARA-based organization system:

```bash
# Create the main PARA folders using Claude Code
uvx basic-memory tool write-note --title "_Index" --folder "1-Journal" --content "# Journal\n\nDaily entries and reflections."

uvx basic-memory tool write-note --title "_Index" --folder "2-Projects" --content "# Projects\n\nActive projects and collaborations."

uvx basic-memory tool write-note --folder "2-Projects" --title "_Index" --content "# Projects\n\n## Active Projects\n- [Project 1](link)\n- [Project 2](link)\n\n## Collaboration\n- [Claude Coordination](claude-coordination/)"

uvx basic-memory tool write-note --title "_Index" --folder "3-Areas" --content "# Areas\n\nOngoing life domains and responsibilities."

uvx basic-memory tool write-note --title "_Index" --folder "4-Resources" --content "# Resources\n\nReference materials and knowledge base."

uvx basic-memory tool write-note --title "_Index" --folder "5-Archives" --content="# Archives\n\nCompleted projects and historical information."

# Create coordination folder for inter-Claude communication
uvx basic-memory tool write-note --title "Coordination Guide" --folder "2-Projects/claude-coordination" --content "# Claude Coordination\n\nThis folder is for direct communication between Claude Code and Claude Desktop.\n\n## Usage\n- Create notes here for the other Claude to read\n- Use clear titles and structured content\n- Reference other project folders as needed"
```

## Verification

### Final Verification Checklist

- [ ] Basic Memory CLI works: `uvx basic-memory --version`
- [ ] Can create notes via CLI: `uvx basic-memory tool write-note...`
- [ ] Can read notes via CLI: `uvx basic-memory tool read-note...`
- [ ] Claude Desktop can create notes
- [ ] Claude Desktop can read notes created by CLI
- [ ] Both interfaces can access the same notes
- [ ] Folder structure is created and organized

### Troubleshooting Common Issues

**Claude Desktop doesn't see Basic Memory:**
- Verify the config file name is exactly `claude_desktop_config.json`
- Check the full path to `uvx` is correct
- Restart Claude Desktop completely
- Check for any JSON syntax errors in the config

**Permission issues:**
```bash
# Fix permissions if needed
chmod +x ~/.local/bin/uvx
```

**uvx not found:**
```bash
# Add to your shell profile (~/.zshrc or ~/.bash_profile)
export PATH="$HOME/.local/bin:$PATH"
source ~/.zshrc  # or ~/.bash_profile
```

## Next Steps

1. **Explore Workflows**: Check out [WORKFLOWS.md](./WORKFLOWS.md) for example collaboration patterns
2. **Customize Organization**: Adapt the folder structure to your needs
3. **Create Templates**: Develop note templates for common use cases
4. **Automate**: Consider shell scripts for frequent operations

## Getting Help

- **Basic Memory Documentation**: https://docs.basicmemory.com
- **Claude Code Documentation**: https://docs.anthropic.com/claude-code
- **Issues**: Create an issue in this repository
- **Discussions**: Use GitHub Discussions for questions and ideas

---

**Congratulations!** You now have a collaborative Claude workflow with persistent memory. Both Claude Code and Claude Desktop can work together through your shared knowledge base.
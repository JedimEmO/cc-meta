# Claude Code Metadata Repository

This repository contains metadata for Claude Code including:
- Slash commands
- Agent files

## Structure

```
├── commands/       # Slash commands (*.md with frontmatter)
├── agents/         # Agent files
└── manifest.json   # Optional explicit manifest
```

## Usage

Use `ccproject` to install items from this repository:

```bash
# Initialize ccproject with this repository
ccproject init -r <repository-url>

# List available items
ccproject list

# Install specific items
ccproject install command <command-name>
ccproject install agent <agent-name>
```

# Chat2KB

Convert chat conversations into structured knowledge-base Markdown files.

## Overview

Chat2KB is a cross-platform skill/agent capability that transforms conversational content into well-organized documentation. Whether you're discussing technical designs, brainstorming ideas, or working through problems, Chat2KB helps you capture the key insights and save them to your personal knowledge base.

## Features

- **Structured Output**: Converts conversations into clean Markdown with YAML frontmatter
- **Unique Tracking**: Each conversation gets a unique ID for easy reference and updates
- **Incremental Export**: Support for full export or appending only new content
- **Multi-Platform**: Available for various AI assistants and platforms
- **Customizable**: Configure your preferred save location via environment variables

## Quick Start

### QoderWork

```bash
# Install the skill
# Copy platforms/qoder/ to ~/.qoderwork/skills/chat2kb/

# Set your KB folder (optional)
export CHAT2KB_FOLDER="~/KnowledgeBase"
```

### OpenClaw

```bash
# Install the skill
# Import platforms/openclaw/chat2kb.skill

# Set your KB folder (optional)
export CHAT2KB_FOLDER="~/KnowledgeBase"
```

## Usage

Simply ask your AI assistant:

- "Save this conversation to KB"
- "Export our discussion as notes"
- "Generate knowledge base from this chat"

The skill will:
1. Extract key points from the conversation
2. Generate a unique conversation ID
3. Structure the content with summary, key points, and action items
4. Save to your configured KB folder

## Documentation

- [Design Philosophy](docs/design.md)
- [Specification](docs/spec.md)
- [Changelog](docs/changelog.md)

## Platform Support

| Platform | Status | Location |
|----------|--------|----------|
| QoderWork | ✅ Available | `platforms/qoder/` |
| OpenClaw | ✅ Available | `platforms/openclaw/` |
| Cursor | 🚧 Planned | `platforms/cursor/` |

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT

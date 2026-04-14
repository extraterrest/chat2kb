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

**TL;DR - 2 steps**:

```bash
# 1. Create KB folder
mkdir -p ~/Documents/KnowledgeBase

# 2. Start using Chat2KB!
# In your AI assistant: "Save this conversation to KB"
```

### Per-Platform Installation

**Codex**:
- Copy `platforms/codex/` or `platforms/codex/SKILL.md` into your Codex skills location

**Claude**:
- Copy or import `platforms/claude/SKILL.md`
- Use with enhanced features (multi-topic detection, dry-run, etc.)

**QoderWork**:
- Copy `platforms/qoder/` to `~/.qoderwork/skills/chat2kb/`

**OpenClaw**:
- Import `platforms/openclaw/chat2kb.skill`

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

**Reference**:
- [Design Philosophy](docs/design.md) - Core principles and architecture
- [Specification](docs/spec.md) - Technical format and standards
- [Claude Implementation](platforms/claude/SKILL.md) - How Chat2KB works with Claude
- [Codex Implementation](platforms/codex/SKILL.md) - How Chat2KB works with Codex

**For Developers**:
- [Changelog](docs/changelog.md) - Release history

## Platform Support

| Platform | Status | Location |
|----------|--------|----------|
| Codex | ✅ Available | `platforms/codex/` |
| OpenClaw | ✅ Available | `platforms/openclaw/` |
| Claude | ✅ Available | `platforms/claude/` |
| QoderWork | ✅ Available | `platforms/qoder/` |

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## License

MIT

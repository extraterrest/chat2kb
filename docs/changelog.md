# Changelog

All notable changes to the Chat2KB project will be documented in this file.

## [Unreleased]

### Planned
- Tagging system
- Template customization
- Cloud storage adapters (Notion, Obsidian, Google Drive, OneDrive)
- Multi-topic detection UI improvements
- Content preview and edit before save

## [0.1.1] - 2026-04-10

### Added
- Claude platform implementation with enhanced features
- Improvement suggestions documentation
- Multi-topic detection with confidence scores
- Rich YAML frontmatter with metadata (word_count, language, participants, confidence_score, related_ids)
- Error handling and edge case documentation
- Configuration file support (~/.chat2kb/config.yml)
- Dry-run mode for preview without saving

### Changed
- Enhanced error messages and troubleshooting guide
- Added content quality metrics and suggestions

## [0.1.0] - 2026-04-10

### Added
- Initial release
- QoderWork platform implementation
- OpenClaw platform implementation
- Unique conversation_id generation (timestamp + random hex)
- YAML frontmatter support
- Incremental export capability
- Environment variable configuration (CHAT2KB_FOLDER)
- Structured output format (Summary, Key Points, Code Examples, Action Items)
- Documentation: Design Philosophy, Specification, Changelog

### Features
- Auto topic detection for filename generation
- Full export mode (complete regeneration)
- Incremental export mode (append new content)
- New export mode (independent KB entry)
- Cross-platform compatibility design

## Versioning

This project follows [Semantic Versioning](https://semver.org/):

- **MAJOR**: Incompatible changes to file format or API
- **MINOR**: New features, backwards compatible
- **PATCH**: Bug fixes, backwards compatible

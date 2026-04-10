# Changelog

All notable changes to the Chat2KB project will be documented in this file.

## [Unreleased]

### Planned
- Cursor platform support
- Tagging system
- Template customization

## [1.0.0] - 2026-04-10

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

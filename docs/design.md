# Chat2KB Design

## Philosophy

Chat2KB is built on the principle that **conversations are knowledge**. Technical discussions, brainstorming sessions, and problem-solving chats often contain valuable insights that get lost in chat history. Chat2KB bridges this gap by making it effortless to convert ephemeral conversations into persistent, structured knowledge.

## Core Principles

### 1. Frictionless Capture
- One command to export
- No manual formatting required
- Smart defaults, minimal configuration

### 2. Structured but Flexible
- Consistent output format with YAML frontmatter
- Grouped by themes, not chronologically
- Adaptable to various conversation types

### 3. Traceable and Updateable
- Unique conversation IDs for tracking
- Support for incremental updates
- Clear lineage between related KB entries

### 4. Platform Agnostic
- Core logic is platform-independent
- Platform-specific implementations handle integration
- Consistent user experience across tools

## Feature Set

### Current Features

| Feature | Description |
|---------|-------------|
| **Auto Topic Detection** | Extracts the main topic for filename generation |
| **Unique ID Generation** | Timestamp + random hex for conversation tracking |
| **YAML Frontmatter** | Metadata including ID, timestamps, export type |
| **Structured Content** | Summary, Key Points, Code Examples, Action Items |
| **Incremental Export** | Option to append new content to existing KB |
| **Environment Config** | Customizable save location via env var |

### Planned Features

| Feature | Description | Priority |
|---------|-------------|----------|
| **Tagging System** | Add custom tags to KB entries | Medium |
| **Search Integration** | Index KB for quick retrieval | Low |
| **Template Customization** | User-defined output templates | Medium |
| **Multi-language Support** | Localized output formats | Low |
| **Cloud Storage Adapters** | Sync to Notion, Obsidian, OSS, Google Drive, OneDrive | Medium |
| **Time-Based Export** | Export by time ranges (e.g., "this morning", "last hour") | High |
| **Multi-Topic Detection** | Auto-detect and select from multiple conversation topics | High |

### Cloud Storage Architecture (Planned)

Chat2KB will support multiple cloud storage backends through an adapter pattern:

```
┌─────────────┐ ┌─────────────┐ ┌─────────────────┐
│ Chat2KB     │────▶│ Storage     │────▶│ Local Files     │
│ Core        │     │ Router      │     │ (Default)       │
└─────────────┘ └─────────────┘ └─────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
┌─────────┐         ┌─────────┐         ┌─────────
│ Notion  │         │Obsidian │         │   OSS   │
│ Adapter │         │ Adapter │         │ Adapter │
└─────────┘         └─────────         └─────────┘
      │                   │                   │
┌─────────         ┌─────────┐         ┌─────────┐
│ Notion  │         │Obsidian │         │ Alibaba │
│   API   │         │  Vault  │         │   OSS   │
└─────────┘         └─────────┘         └─────────┘
```

**Supported Backends (Planned):**

| Backend | Type | Use Case |
|---------|------|----------|
| **Notion** | API | Team wiki, collaborative KB |
| **Obsidian** | Local + Sync | Personal knowledge management |
| **Alibaba OSS** | Object Storage | Scalable cloud storage |
| **Google Drive** | Cloud Storage | Integration with Google Workspace |
| **OneDrive** | Cloud Storage | Microsoft ecosystem integration |
| **GitHub** | Git-based | Version-controlled documentation |

**Configuration:**
```bash
# Storage backend selection
export CHAT2KB_STORAGE="notion" # local | notion | obsidian | oss | gdrive | onedrive

# Backend-specific credentials
export NOTION_API_KEY="secret_xxx"
export NOTION_DATABASE_ID="xxx"
export OSS_ACCESS_KEY="xxx"
# ... etc
```

## Workflow

### Three-Phase Process

```
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ Phase 1: User   │ │ Phase 2: LLM    │ │ Phase 3: Confirm│
│ Request         │───▶│ Analysis      │───▶│ & Execute     │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

### Phase 1 - User Request

User specifies (explicitly or implicitly):

| Dimension | Explicit Example | Implicit (Default) |
|-----------|------------------|-------------------|
| **Time** | "今天上午聊的" | Current conversation (full) |
| **Topic** | "广告投放策略" | Auto-detect all topics |

### Phase 2 - LLM Analysis

1. **Select Time Range**
   - Parse user's time specification (if any)
   - Default: entire conversation history

2. **Analyze Content & Detect Topics**
   - Read conversation within selected time range
   - Identify distinct topics/themes
   - Look for previous KB export records in conversation
   - Check if corresponding KB files exist

3. **Map Topics to Existing KBs**
   - For each detected topic, check if previously exported
   - Match conversation_id with existing files

### Phase 3 - User Confirmation & Execution

#### Step 1: Confirm Topics

**Single Topic**: Proceed directly

**Multiple Topics**: Ask user to select

> "Detected 3 topics in this conversation:
> 1. 新的广告投放策略
> 2. 预算分配讨论
> 3. 下周会议安排
>
> Which would you like to save?
> - Enter numbers (e.g., '1,3') for specific topics
> - Enter 'all' to save each as separate KB files"

#### Step 2: Confirm Update Strategy

For each selected topic with existing KB:

> "Topic '广告投放策略' has a previous KB (ID: kb_20260410_143052_a7x9k2m).
> How would you like to proceed?
> - **A**: Update existing KB (overwrite with full content)
> - **B**: Incremental export (only new content since last export)
> - **C**: Create new KB (ignore previous, fresh start)"

#### Step 3: Execute

```
For each confirmed topic:
 ↓
Generate/Retrieve conversation_id
 ↓
Extract: Key points, decisions, code, action items
 ↓
Structure: Apply template with YAML frontmatter
 ↓
Save: Write to KB folder with dated filename
 ↓
Confirm: Report success with conversation_id
```

## Output Format

### Filename Convention
```
YYYY-MM-DD-<topic-slug>.md
```

Examples:
- `2026-04-10-react-performance-optimization.md`
- `2026-04-10-database-schema-design.md`

### File Structure
```markdown
---
conversation_id: kb_20260410_143052_a7x9k2m
created_at: 2026-04-10 14:30
updated_at: 2026-04-10 16:45
export_type: full
---

# Topic Title

**Date**: YYYY-MM-DD
**Participants**: User, Assistant

## Summary
Brief overview of the conversation.

## Key Points

### Theme 1
- Key insight
- Supporting details

### Theme 2
- Key insight
- Supporting details

## Code Examples
```language
// code if applicable
```

## Action Items
- [x] Completed item
- [ ] Pending item
```

## Design Decisions

### Why No Full Conversation Log?
Early versions included complete transcripts, but this created redundancy with the structured Key Points. The current design prioritizes distilled knowledge over raw logs.

### Why Timestamp + Random for ID?
- Timestamp provides chronological sorting
- Random component prevents collisions in multi-device scenarios
- Hex format is URL-safe and compact

### Why YAML Frontmatter?
- Machine-readable metadata
- Compatible with static site generators
- Extensible for future fields

## Future Directions

1. **Knowledge Graph**: Link related KB entries
2. **AI-Powered Summarization**: Smarter extraction of key points
3. **Collaborative KB**: Multi-user conversation support
4. **Integration Hub**: Connect to Notion, Obsidian, etc.
5. **Universal Storage Layer**: Seamless sync across local and cloud backends

## Platform Implementation Guide

When implementing Chat2KB on a new platform:

1. **Read Design Docs First**
   - `docs/design.md` — Philosophy, features, architecture
   - `docs/spec.md` — Technical specifications

2. **Follow Core Workflow**
   - Topic detection → ID generation → Content extraction → Storage → Confirmation
   - Adapt to platform's skill/agent format

3. **Implement Required Features**
   - Unique conversation_id generation
   - YAML frontmatter support
   - Structured output (Summary, Key Points, Code, Action Items)
   - Environment variable configuration

4. **Stay Synchronized**
   - Check design docs for updates
   - Follow spec changes for compatibility
   - Contribute platform-specific improvements back to design docs

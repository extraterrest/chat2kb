# Chat2KB Specification

## Conversation ID Format

### Pattern
```
kb_{YYYYMMDD}_{HHMMSS}_{6hex}
```

### Example
```
kb_20260410_143052_a7c9e2
```

### Generation
```bash
CONVERSATION_ID="kb_$(date +%Y%m%d_%H%M%S)_$(openssl rand -hex 3)"
```

### Rules
- Always starts with `kb_` prefix
- Date and time in UTC or local timezone (consistent per platform)
- Random component: 6 hexadecimal characters (24 bits of entropy)
- Total length: 25 characters

## Filename Format

### Pattern
```
YYYY-MM-DD-<topic-slug>.md
```

### Slug Rules
- Lowercase
- Alphanumeric and hyphens only
- No leading/trailing hyphens
- Maximum 50 characters
- Derived from conversation topic

### Examples
- `2026-04-10-react-performance-optimization.md`
- `2026-04-10-database-schema-design.md`
- `2026-04-10-api-discussion-part2.md`

## YAML Frontmatter Fields

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `conversation_id` | string | Unique identifier for this conversation |
| `created_at` | string | ISO 8601 datetime of first export |
| `updated_at` | string | ISO 8601 datetime of last export |
| `export_type` | enum | `full` or `incremental` |

### Optional Fields

| Field | Type | Description |
|-------|------|-------------|
| `topics` | array | Tags derived from conversation content |
| `language` | string | Detected language of the conversation (e.g. `en`, `zh-CN`) |
| `platform` | string | Platform where conversation occurred |
| `participants` | array | List of participants |

### Example
```yaml
---
conversation_id: kb_20260410_143052_a7c9e2
created_at: 2026-04-10T14:30:00Z
updated_at: 2026-04-10T16:45:00Z
export_type: full
topics: ["react", "performance", "optimization"]
language: en
---
```

## Environment Variables

### CHAT2KB_FOLDER
Specifies the default save location for KB files.

```bash
export CHAT2KB_FOLDER="~/KnowledgeBase"
```

### Behavior
- If set: Save files to this directory
- If not set: Ask user for save location or use platform default
- Path expansion: Support for `~` (home directory)

### CHAT2KB_STORAGE (Planned)
Selects the storage backend adapter.

```bash
export CHAT2KB_STORAGE="local" # local | notion | obsidian | oss | gdrive | onedrive | github
```

### Backend-Specific Variables (Planned)

| Variable | Backend | Description |
|----------|---------|-------------|
| `NOTION_API_KEY` | Notion | Integration API key |
| `NOTION_DATABASE_ID` | Notion | Target database ID |
| `OBSIDIAN_VAULT_PATH` | Obsidian | Local vault directory |
| `OSS_ACCESS_KEY_ID` | Alibaba OSS | Access key |
| `OSS_ACCESS_KEY_SECRET` | Alibaba OSS | Secret key |
| `OSS_BUCKET` | Alibaba OSS | Bucket name |
| `OSS_ENDPOINT` | Alibaba OSS | Endpoint URL |
| `GDRIVE_CREDENTIALS` | Google Drive | OAuth credentials path |
| `ONEDRIVE_TOKEN` | OneDrive | OAuth token |
| `GITHUB_REPO` | GitHub | Target repo (owner/repo format) |
| `GITHUB_TOKEN` | GitHub | Personal access token |

## Content Structure Rules

### Summary Section
- 1-2 paragraphs maximum
- Capture the "why" and "what" of the conversation
- No bullet points

### Key Points Section
- Group by themes, not chronologically
- Use H3 (`###`) for theme headers
- Bullet points only, no prose
- Maximum 3-5 points per theme

### Code Examples Section
- Include only if conversation contains code
- Use fenced code blocks with language tags
- Omit if no code discussed

### Action Items Section
- Use GitHub-style checkboxes: `- [ ]` and `- [x]`
- Mix of completed and pending items allowed
- Omit if no action items

## Incremental Export

### Detection
When user requests KB export:
1. Scan conversation history for previous export requests
2. Look for `conversation_id` in assistant's responses
3. Check if corresponding file exists

### User Options
If previous export detected:

| Option | Behavior |
|--------|----------|
| **A - Update** | Overwrite existing file with full regenerated content |
| **B - Incremental** | Create new file with only new content since last export |
| **C - New** | Generate new `conversation_id`, create independent KB |

### Incremental Filename
When choosing incremental export:
```
YYYY-MM-DD-<topic-slug>-part{N}.md
```

## Platform Implementation Requirements

### Minimum Viable Implementation
1. Generate valid conversation_id
2. Create properly formatted Markdown with YAML frontmatter
3. Save to configurable location
4. Report success with conversation_id

### Recommended Features
1. Detect previous exports in conversation
2. Offer update/incremental/new options
3. Support environment variable for save location

### Optional Enhancements
1. Auto-detect conversation topic
2. Smart content extraction
3. Template customization
4. Integration with external tools

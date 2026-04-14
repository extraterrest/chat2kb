---
name: chat2kb
description: Convert chat conversations into structured Markdown knowledge-base files. Use this skill whenever the user wants to save, capture, export, or document a conversation, even if they do not mention "chat2kb". Trigger on requests like "save this conversation", "export our discussion", "generate KB", "turn this into notes", "保存对话", "生成知识库", "转成KB", "导出笔记", or any request to archive what was discussed.
---

# Chat2KB For Codex

Convert chat history into clean, structured Markdown knowledge-base files.

## When To Use

Use this skill when the user wants to:

- Save the current conversation as notes or KB
- Export part or all of a discussion into Markdown
- Turn a design/debug/brainstorming thread into reusable documentation
- Update a previously saved KB entry with new discussion content

When in doubt, prefer triggering the skill. Missing a save opportunity is worse than asking one extra confirmation question.

## Codex Workflow

### Step 1: Detect topics

Read the relevant conversation scope and identify the distinct topics discussed.

- If the user specified a time range or topic, honor that first
- If only one topic is present, continue directly
- If multiple topics are present, present a numbered list and ask which topics to save
- Support `all` to save each topic separately
- Support `combine` to merge multiple topics into one KB file

Use this format when multiple topics are found:

```text
Detected 3 topics:

1. [Title]
   [One-sentence summary]

2. [Title]
   [One-sentence summary]

3. [Title]
   [One-sentence summary]

Which topics would you like to save?
-> Enter numbers, e.g. 1,3
-> Enter all
-> Enter combine
```

### Step 2: Check for previous export

Before generating a new file, scan the conversation for a previous `conversation_id` for the same topic.

- If no previous export exists, generate a new ID
- If a previous export exists, ask whether to update, append, or create a new KB

Conversation ID format:

```text
kb_{YYYYMMDD}_{HHMMSS}_{6hex}
Example: kb_20260410_143052_a7c9e2
```

Generation:

```bash
CONVERSATION_ID="kb_$(date +%Y%m%d_%H%M%S)_$(openssl rand -hex 3)"
```

Use this prompt when a previous export exists:

```text
Found a previous KB for this topic (ID: kb_20260410_143052_a7c9e2)

-> A: Update (regenerate the full KB with all content)
-> B: Append (export only content added since last save)
-> C: New (ignore previous KB, create an independent one)
```

### Step 3: Extract content

For each topic, extract:

- `summary`: 1-2 paragraph overview
- `key_points`: grouped by theme, not chronology
- `code_examples`: only if code was discussed
- `action_items`: completed and pending items

Extraction rules:

- Preserve technical accuracy over elegance
- Group related ideas by theme
- Prefer decisions, tradeoffs, and takeaways over transcript-like detail
- Include code only when it helps future reuse
- Omit empty sections instead of leaving placeholders

### Step 4: Generate Markdown

Use this structure:

```markdown
---
conversation_id: kb_20260410_143052_a7c9e2
created_at: YYYY-MM-DDTHH:MM:SSZ
updated_at: YYYY-MM-DDTHH:MM:SSZ
export_type: full
topics: [tag1, tag2]
language: en
---

# [Topic Title]

**Date**: YYYY-MM-DD

## Summary

[1-2 paragraphs]

## Key Points

### [Theme 1]
- insight
- detail

### [Theme 2]
- insight

## Code Examples

```language
// code
```

## Action Items

- [x] Done item
- [ ] Pending item
```

Rules:

- Group by theme, not chronology
- Use bullet points in `Key Points`
- Omit `Code Examples` if no code was discussed
- Omit `Action Items` if none exist
- Do not include a full transcript

### Step 5: Preview before writing

Always show the generated Markdown to the user before writing the file.

Use this preview structure:

```text
Preview (not saved yet)

[full Markdown content]

Will save to: /path/to/YYYY-MM-DD-topic-slug.md

-> Y Save
-> E Edit summary
-> N Cancel
```

If the user chooses `E`, accept the revised summary, regenerate the preview, and ask again.

### Step 6: Save the file

When the user confirms:

1. Resolve save path from `~/.chat2kb/config.yml`
2. If missing, fall back to `CHAT2KB_FOLDER`
3. If neither exists, ask the user where to save
4. Create the directory if needed
5. Save as `YYYY-MM-DD-<topic-slug>.md`
6. If appending incrementally, use `YYYY-MM-DD-<topic-slug>-part{N}.md`
7. If `dry_run: true` or `CHAT2KB_DRY_RUN=true`, skip the actual write and clearly say so

For Codex specifically:

- Prefer writing the file directly in the workspace or user-specified KB folder
- Use absolute file paths when confirming where the file was saved
- If a local config file is referenced, read it before asking the user

### Step 7: Confirm success

After writing, report:

- Saved file path
- Filename
- `conversation_id`

Use this format:

```text
Knowledge base saved!

Location: /absolute/path/to/2026-04-10-react-optimization.md
ID: kb_20260410_143052_a7c9e2
```

## Configuration

Default config file:

```yaml
kb_folder: ~/Documents/KnowledgeBase
dry_run: false
create_backup: true
log_level: info
```

Environment overrides:

| Variable | Purpose |
|---|---|
| `CHAT2KB_FOLDER` | KB save location |
| `CHAT2KB_DRY_RUN` | `true` = preview only |
| `CHAT2KB_LOG_LEVEL` | Logging verbosity |

## Error Handling

### Cannot write to folder

```text
Cannot write to folder: /path/to/folder
Reason: directory does not exist or no write permission
```

Suggested fixes:

- Create the directory
- Check write permissions
- Ask the user for an alternate save location

### No content found

```text
No content to export
Reason: no messages found in the selected scope
```

Suggested fix:

- Retry with the full conversation or a broader time range

### File conflict

```text
File already exists: YYYY-MM-DD-topic-slug.md
-> A: Overwrite
-> B: Rename
-> C: Cancel
```

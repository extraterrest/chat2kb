---
name: chat2kb
description: Convert chat conversations into structured Markdown knowledge-base files. Use this skill whenever the user wants to save, capture, export, or document a conversation — even if they don't use the word "chat2kb". Trigger on phrases like "save this conversation", "export our discussion", "generate KB", "turn this into notes", "保存对话", "生成知识库", "转成KB", "导出笔记", or any request to archive what was discussed. When in doubt, trigger — it's better to offer the skill than to miss a save opportunity.
---

# Chat2KB

Convert chat history into clean, structured Markdown knowledge-base files.

## Overview

This skill walks through five steps each time the user wants to save a conversation:

1. **Detect topics** — identify how many distinct subjects were discussed
2. **Confirm selection** — let the user choose which topics to save
3. **Extract content** — pull out key points, decisions, code, and action items
4. **Preview** — show the generated KB and let the user edit before saving
5. **Save** — write the file and confirm with the conversation ID

For detailed prompt templates, see `references/prompts.md`.
For input/output examples, see `references/examples.md`.

---

## Step 1: Detect Topics

Analyze the full conversation (or the time range the user specified) and identify distinct topics.

Use the prompt in `references/prompts.md → ## Detect Topics Prompt`.

**Single topic detected** → skip to Step 3.

**Multiple topics detected** → show the list and ask:

```
Detected N topics:

1. [Title] (confidence: XX%)
   [One-sentence summary]

2. [Title] (confidence: XX%)
   [One-sentence summary]

Which topics would you like to save?
→ Enter numbers, e.g. 1,3
→ Enter all (save each as a separate KB file)
→ Enter combine (merge into one KB file)
```

---

## Step 2: Check for Previous Export

Before extracting content, scan the conversation history for any previous `conversation_id` matching this topic.

**No previous export found** → generate a new ID:
```
kb_{YYYYMMDD}_{HHMMSS}_{6 random hex chars}
Example: kb_20260410_143052_a7c9e2
```

**Previous export found** → ask:
```
Found a previous KB for this topic (ID: kb_20260410_143052_a7c9e2)

→ A: Update (regenerate the full KB with all content)
→ B: Append (export only content added since last save)
→ C: New (ignore previous KB, create an independent one)
```

---

## Step 3: Extract Content

For each selected topic, use the prompt in `references/prompts.md → ## Extract Content Prompt`.

The prompt returns structured JSON with:
- `summary` — 1–2 paragraph overview
- `key_points` — grouped by theme, not chronology
- `code_examples` — only if code was discussed
- `action_items` — completed and pending

---

## Step 4: Generate KB Markdown

Assemble the extracted content into this structure:

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

[1–2 paragraphs]

## Key Points

### [Theme 1]
- insight
- detail

### [Theme 2]
- insight

## Code Examples

\`\`\`language
// code
\`\`\`

## Action Items

- [x] Done item
- [ ] Pending item
```

**Rules:**
- Group by theme, not chronology
- Bullet points only in Key Points — no prose
- Omit Code Examples if no code was discussed
- Omit Action Items if none exist

---

## Step 5: Preview and Save

### Show preview

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Preview (not saved yet)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[full Markdown content]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Will save to: ~/KnowledgeBase/YYYY-MM-DD-slug.md

→ Y  Save
→ E  Edit summary
→ N  Cancel
```

If user chooses **E**, show the current summary and accept a replacement. Then re-show the preview.

### Save the file

1. Read `kb_folder` from `~/.chat2kb/config.yml`, or fall back to `CHAT2KB_FOLDER` env var, or ask the user.
2. Create the directory if it doesn't exist.
3. Filename: `YYYY-MM-DD-<topic-slug>.md` (lowercase, hyphens, max 50 chars).
4. If `dry_run: true` in config or `CHAT2KB_DRY_RUN=true` in env → skip write, show notice instead.
5. Write the file.

### Confirm success

```
✅ Knowledge base saved!

📁 ~/KnowledgeBase/2026-04-10-react-optimization.md
🆔 kb_20260410_143052_a7c9e2
```

---

## Configuration

Create `~/.chat2kb/config.yml` to avoid setting env vars every time:

```yaml
kb_folder: ~/Documents/KnowledgeBase
dry_run: false          # true = preview only, don't write
create_backup: true     # back up before overwriting
log_level: info         # debug | info | warn | error
```

Environment variable overrides (take precedence over config file):

| Variable | Purpose |
|---|---|
| `CHAT2KB_FOLDER` | KB save location |
| `CHAT2KB_DRY_RUN` | `true` = preview only |
| `CHAT2KB_LOG_LEVEL` | Logging verbosity |

---

## Error Handling

Diagnose problems with clear, actionable messages.

**Cannot write to folder**
```
❌ Cannot write to folder: ~/KnowledgeBase
Reason: directory does not exist or no write permission

Fix:
→ mkdir -p ~/KnowledgeBase
→ chmod u+w ~/KnowledgeBase
```

**No content found**
```
❌ No content to export
Reason: no messages found in the selected time range

Fix:
→ Try exporting the full conversation without a time range
```

**File conflict**
```
⚠️  File already exists: 2026-04-10-react-optimization.md
→ A: Overwrite  → B: Rename  → C: Cancel
```

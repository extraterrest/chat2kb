---
name: chat2kb
description: Convert conversation content into a structured knowledge-base Markdown file. Use when the user asks to "save this conversation", "generate KB", "export notes", "save conversation", "转成知识库", "保存对话", "生成知识库", "导出笔记", or any request to summarize/export conversation content as documentation.
---

# Chat2KB

Convert chat history into a clean, structured Markdown knowledge-base file.

## Workflow

### Phase 1: Parse User Request

User may specify (explicitly or implicitly):

| Dimension | Explicit Example | Implicit (Default) |
|-----------|------------------|-------------------|
| **Time** | "今天上午聊的", "最近一小时" | Current conversation (full) |
| **Topic** | "广告投放策略" | Auto-detect all topics |

### Phase 2: Analyze Content

1. **Select Time Range**
   - Parse user's time specification (if any)
   - Default: entire conversation history

2. **Detect Topics**
   - Read conversation within selected time range
   - Identify distinct topics/themes
   - Look for previous KB export records in conversation
   - Check if corresponding KB files exist in `CHAT2KB_FOLDER`

3. **Map to Existing KBs**
   - For each detected topic, check if previously exported
   - Match conversation_id with existing files

### Phase 3: Confirm & Execute

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

#### Step 3: Generate & Save

For each confirmed topic:

1. **Generate conversation_id** (if new or option C):
   ```bash
   CONVERSATION_ID="kb_$(date +%Y%m%d_%H%M%S)_$(openssl rand -hex 3)"
   ```

2. **Extract content**: Key points, decisions, code, action items

3. **Structure the Markdown**:

```yaml
---
conversation_id: kb_20260410_143052_a7x9k2m
created_at: 2026-04-10 14:30
updated_at: 2026-04-10 16:45
export_type: full | incremental
---

# [Topic Title]

**Date**: YYYY-MM-DD
**Participants**: User, Assistant

## Summary
1-2 paragraph overview of what this conversation is about.

## Key Points

### [Theme 1]
- Key insight or decision
- Details and context

### [Theme 2]
- ...

## Code Examples
```language
// code if applicable
```

## Action Items
- [x] Completed item
- [ ] Pending item
```

**Rules:**
- Group content by theme, not chronologically
- Use concise bullet points, not prose
- Include code examples if the conversation contains code
- Track action items with checkboxes
- No "Full Conversation Log" section

4. **Save the file**:
   - Filename: `YYYY-MM-DD-<topic-slug>.md`
   - Location: `CHAT2KB_FOLDER` env var, or ask user

5. **Confirm success**:

> ✅ Knowledge base saved!
> 📁 Location: ~/KnowledgeBase/2026-04-10-ad-strategy.md
> 🆔 ID: kb_20260410_143052_a7x9k2m

## Configuration

Set your KB folder:

```bash
export CHAT2KB_FOLDER="~/KnowledgeBase"
```

Restart Qoder after setting.

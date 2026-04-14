# Chat2KB Prompt Templates

Prompts used in each step of the Chat2KB workflow.
Reference from SKILL.md as needed — only load this file when a step requires it.

---

## Detect Topics Prompt

Use in Step 1 to identify distinct topics in the conversation.

```
Analyze the following conversation and identify the distinct topics discussed.

Guidelines:
- A topic switch is when the conversation clearly moves to a new subject
- A topic needs its own coherent discussion thread to count
- Brief tangents (<3 messages) do not qualify as separate topics

Conversation:
{CONVERSATION}

Return JSON only — no other text:
{
  "topics": [
    {
      "id": 1,
      "title": "Short topic title (5-10 words)",
      "message_range": "1-15",
      "summary": "One sentence describing what this topic covered",
      "confidence": 0.95
    }
  ]
}
```

**Handling the response:**
- 1 topic → skip selection, proceed directly to Step 3
- 2+ topics → present list to user for selection (see SKILL.md Step 1)
- Parse failure → retry once; if it fails again, treat as a single topic and continue

---

## Extract Content Prompt

Use in Step 3 to extract structured content from a specific topic's messages.

```
Based on the conversation below, extract the core knowledge from this topic.

Topic: {TOPIC_TITLE}
Messages (#{START} to #{END}):
{MESSAGES}

Extract the following:

1. summary — 1-2 paragraphs describing what was discussed and what was concluded
2. key_points — grouped by theme (not chronology), 3-5 bullet points per group
3. code_examples — any code from the conversation; empty array if none
4. action_items — to-dos or completed tasks mentioned; empty array if none

Return JSON only — no other text:
{
  "summary": "...",
  "key_points": [
    {
      "theme": "Theme name",
      "points": ["point 1", "point 2", "point 3"]
    }
  ],
  "code_examples": [
    {
      "language": "javascript",
      "title": "Example title",
      "code": "..."
    }
  ],
  "action_items": [
    { "done": true,  "text": "Completed task" },
    { "done": false, "text": "Pending task" }
  ],
  "tags": ["tag1", "tag2"],
  "language": "en"
}
```

**Handling the response:**
- `tags` → use as the `topics` field in KB frontmatter
- `language` → use as the `language` field in KB frontmatter
- Empty `code_examples` → omit the Code Examples section from the KB
- Empty `action_items` → omit the Action Items section from the KB

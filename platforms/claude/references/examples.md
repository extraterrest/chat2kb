# Chat2KB Examples

End-to-end examples showing input conversations and the KB files they produce.
Reference from SKILL.md as needed.

---

## Example 1: Single Topic (Technical)

### Input conversation

```
User: What are the main ways to optimize React component performance?

Claude: There are three main categories:
1. Reduce re-renders: React.memo, useMemo, useCallback
2. Code splitting: React.lazy + Suspense
3. Virtual lists: react-window for long lists

User: When should I use React.memo?

Claude: It's worth using when a component meets both conditions:
- Rendering is expensive (complex UI or heavy computation)
- The parent re-renders often but the props rarely change

If props change every render, memo actually adds overhead.

User: Can you show me an example?

Claude: Sure:

const ProductCard = React.memo(({ product }) => {
  return <div>{product.name}: ${product.price}</div>;
});

This only re-renders when product changes.

User: What about useCallback?

Claude: useCallback caches a function reference so child components
don't re-render just because "the function is new every render":

const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);

Works best when paired with memo on the child.
```

### Generated KB file: `2026-04-10-react-performance.md`

```markdown
---
conversation_id: kb_20260410_143052_a7c9e2
created_at: 2026-04-10T14:30:00Z
updated_at: 2026-04-10T14:30:00Z
export_type: full
topics: [react, performance, optimization]
language: en
---

# React Performance Optimization

**Date**: 2026-04-10

## Summary

Covered three categories of React performance optimization: reducing re-renders with memo/useMemo/useCallback, code splitting with lazy + Suspense, and virtual lists with react-window. Focused in particular on when React.memo and useCallback are (and aren't) worth reaching for.

## Key Points

### When to use React.memo
- Use when the component is expensive to render AND its props change infrequently
- If props change on every render, memo's diffing cost outweighs the savings
- Works best combined with useCallback on functions passed as props

### useCallback
- Caches a function reference across renders
- Prevents child components from re-rendering due to a "new" function identity
- Dependency array accuracy is critical — stale deps lead to bugs

### Other optimization techniques
- React.lazy + Suspense: load components on demand, reduces initial bundle size
- react-window: virtualized lists render only visible rows, handles large datasets

## Code Examples

```javascript
// React.memo — only re-renders when product changes
const ProductCard = React.memo(({ product }) => {
  return <div>{product.name}: ${product.price}</div>;
});
```

```javascript
// useCallback — stable function reference for child components
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);
```

## Action Items

- [ ] Profile the app with DevTools Profiler to find actual bottlenecks before optimizing
- [ ] Apply React.memo to components that re-render frequently with unchanged props
```

---

## Example 2: Multiple Topics Detected

### Input conversation

```
User: What's the difference between interface and type in TypeScript?
Claude: The main differences: interface supports declaration merging
and is better for object shapes; type is more flexible and supports
union types, mapped types, and conditional types...
...(discussion continues)

User: Got it. Switching topics — how should we structure next week's sprint?
Claude: I'd recommend prioritizing by risk: finish all P0 items before
touching P1, and leave buffer for unplanned work...
...(discussion continues)
```

### Topic detection result

```json
{
  "topics": [
    {
      "id": 1,
      "title": "TypeScript interface vs type",
      "message_range": "1-8",
      "summary": "Compared interface and type aliases, covering use cases and key differences",
      "confidence": 0.97
    },
    {
      "id": 2,
      "title": "Sprint planning",
      "message_range": "9-14",
      "summary": "Discussed how to prioritize and structure next week's sprint",
      "confidence": 0.91
    }
  ]
}
```

### Claude's message to user

```
Detected 2 topics:

1. TypeScript interface vs type (confidence: 97%)
   Compared interface and type aliases, covering use cases and key differences

2. Sprint planning (confidence: 91%)
   Discussed how to prioritize and structure next week's sprint

Which topics would you like to save?
→ Enter numbers, e.g. 1,2
→ Enter all (save each as a separate KB file)
→ Enter combine (merge into one KB file)
```

### If user enters `all`, two files are saved

```
✅ Saved 2 knowledge base files:

📁 2026-04-10-typescript-interface-vs-type.md
   🆔 kb_20260410_150312_c3d8e1f

📁 2026-04-10-sprint-planning.md
   🆔 kb_20260410_150318_9b2a74
```

---

## Example 3: Update Existing KB

### Scenario

The user previously saved a KB about React performance (ID: `kb_20260409_103021_c9a2f3`).
Now there has been more discussion on the same topic and they want to update it.

### Claude's message

```
Found a previous KB for this topic (ID: kb_20260409_103021_c9a2f3)
Last exported: 2026-04-09 10:30

→ A: Update (regenerate the full KB with all content)
→ B: Append (export only content added since last save)
→ C: New (ignore previous KB, create an independent one)
```

### If user chooses B (Incremental), the filename becomes

```
2026-04-10-react-performance-part2.md
```

With frontmatter:

```yaml
---
conversation_id: kb_20260409_103021_c9a2f3
created_at: 2026-04-09T10:30:00Z
updated_at: 2026-04-10T16:45:00Z
export_type: incremental
topics: [react, performance]
language: en
---
```

---

## Example 4: Dry-Run Mode

### Setup

```bash
export CHAT2KB_DRY_RUN=true
```

### Claude's behavior

Run the full workflow as normal — detect topics, extract content, generate KB markdown, show preview — but at Step 5, skip the file write and display this instead:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Preview (not saved)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[full KB content]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️  Dry-run mode: file was NOT written.
    Unset CHAT2KB_DRY_RUN and run again to save.
```

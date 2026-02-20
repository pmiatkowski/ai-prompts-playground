---
agent: 'agent'
model: GPT-4o
tools: ['search', 'read', 'write', 'web']
description: 'Generate a new VS Code Copilot prompt file'
argument-hint: 'Describe the task the new prompt should accomplish'
---

Generate a new `.prompt.md` file in the `.agents/prompts/` folder based on the user's description below. Follow the structure and style of [ai.example.prompt.md](./ai.example.prompt.md).

If the user's description is ambiguous or missing critical details (target audience, required tools, key constraints), ask up to 3 clarification questions before generating.

## User Description

${input:taskDescription:What should the new prompt do? e.g. "generate unit tests for a selected function"}

## Output File Format

The generated file MUST follow this exact structure:

```markdown
---
agent: '<ask|agent|plan>'
model: <model name>
tools: [<list of required tools>]
description: '<one-line summary for the user>'
argument-hint: '<hint text for the chat input field>'
---

<Body: clear, actionable instructions in Markdown>
```

## Rules for the Generated Prompt

1. **Filename**: Use the pattern `ai.<task-name>.prompt.md` (lowercase, hyphen-separated).
2. **Frontmatter**: Include all relevant fields — `agent`, `model`, `tools`, `description`, `argument-hint`. Omit optional fields only if truly unnecessary.
3. **Body instructions**: Write from the AI's perspective ("Your goal is to…"). Keep them concise, step-by-step, and action-oriented.
4. **Variables**: Use `${input:name:placeholder}` for user inputs, `${selection}` for editor context, and `${file}` for current file — only when they add value.
5. **File references**: Link to workspace files with relative Markdown paths (e.g., `[guide](../docs/guide.md)`).
6. **Tool references**: Use `#tool:<tool-name>` syntax in the body when referencing specific tools.
7. **Scope**: Each prompt should do ONE thing well. Don't combine unrelated tasks.
8. **Tone**: Direct and practical — no filler text or generic advice.

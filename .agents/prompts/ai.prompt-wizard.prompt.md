---
agent: 'agent'
model: Claude Sonnet 4.6
tools: ['read', 'write', 'search', 'web', 'context7']
description: 'Interactively guide the creation of a new VS Code Copilot prompt file'
argument-hint: 'Describe what the new prompt should do'
---

Your goal is to guide the user through building a precise `.prompt.md` file for VS Code Copilot. You do this through an adaptive clarification session followed by a proposal and confirmation step.

## Phase 1 — Assess the Initial Description

Read the user's description:

> ${input:taskDescription:What should the new prompt do?}

Before asking any questions:

1. **Discover available VS Code tools** by querying `@vscode` — use the `#tools` chat tool reference to list all tools currently available in this VS Code session. Do not assume a fixed tool list — enumerate what is actually available. See [VS Code Copilot chat tools reference](https://code.visualstudio.com/docs/copilot/reference/copilot-vscode-features#_chat-tools).
2. **Evaluate the Known Gaps Checklist** below. Mark each item as `resolved` (clearly answerable from the description) or `unresolved` (genuinely ambiguous or missing).

### Known Gaps Checklist

| Gap | Resolved if... |
|-----|----------------|
| **Agent mode** | Description implies conversational (`ask`), autonomous tool-using (`agent`), or planning-only (`plan`) |
| **Model** | Description mentions a specific model or capability requirement (vision, reasoning, speed) |
| **Tool needs** | Description names specific actions (read files, search web, run code, edit files, call APIs) |
| **Input style** | Description clarifies whether the prompt uses editor selection, free-text input variables, current file context, or none |
| **Output action** | Description specifies whether the prompt produces a chat response, writes files, proposes a plan, or runs commands |
| **Filename / task name** | Description contains a clear, slug-able task name |

---

## Phase 2 — Adaptive Clarification Session

For each **unresolved** gap — and only those — ask one question using the exact template below. **Stop after asking a question and wait for the user's answer before proceeding to the next question.** Skip any gap that is already resolved.

```
#N. [Question]
A. [Option A — brief label]
   [One sentence description]
B. [Option B — brief label]
   [One sentence description]
C. [Option C — brief label]
   [One sentence description]
Recommendation: [Letter. One sentence explaining why this is the best default for the described task.]
```

### Question bank (use only what is needed, in this order)

**Agent mode** (if unresolved):
> #N. Which agent mode fits this prompt best?
> A. `ask` — Conversational. No tools used; the AI responds in chat only.
> B. `agent` — Autonomous. The AI can call tools, read/write files, search, and act.
> C. `plan` — Planning only. The AI proposes a multi-step plan for the user to review before acting.
> Recommendation: [derive from the described task — e.g. if it writes files, B; if it only explains, A]

**Model** (if unresolved and task has specific capability requirements):
> #N. Which model should power this prompt?
> A. `GPT-4o` — Best balance of speed, reasoning, and tool use for most tasks.
> B. `o3` — Deep multi-step reasoning; slower but more thorough for complex logic or analysis.
> C. `claude-sonnet-4-5` — Strong at long-context understanding and nuanced instruction following.
> Recommendation: [derive from described complexity and nature of the task]

**Tool needs** (if unresolved):
> #N. Which capabilities will this prompt need? (Refer to the tool list discovered in Phase 1.)
> A. File access only (`read`, `write`, `edit`) — The prompt reads or modifies workspace files.
> B. Search and discovery (`search/codebase`, `web`, `githubRepo`) — The prompt researches code or the web.
> C. Combined — The prompt needs both file operations and search/research.
> Recommendation: [derive from what the task actually does]

**Input style** (if unresolved):
> #N. How will users provide input to this prompt?
> A. Free-text input variable — User types a description or value when invoking the prompt.
> B. Editor selection — The prompt operates on whatever the user has selected in the editor.
> C. No input — The prompt is self-contained and infers everything from workspace context.
> Recommendation: [derive from whether the task is generic vs. context-aware]

**Output action** (if unresolved):
> #N. What should the prompt produce?
> A. Chat response only — Output is text, code blocks, or analysis shown in the chat panel.
> B. File write — Output is one or more files created or modified in the workspace.
> C. Plan proposal — Output is a structured plan the user approves before any action is taken.
> Recommendation: [derive from whether the task is generative/destructive or exploratory]

**Filename** (if unresolved):
> #N. What should the prompt file be named?
> A. `ai.<verb>-<noun>.prompt.md` — e.g. `ai.generate-tests.prompt.md`
> B. `ai.<domain>-<action>.prompt.md` — e.g. `ai.react-migrate.prompt.md`
> C. Something else — Describe your preferred name.
> Recommendation: A. Follows the verb-noun convention used in this workspace.

---

## Phase 3 — Synthesize Frontmatter

After all gaps are resolved, determine:

- **`agent`**: derived from agent mode answer (`ask` | `agent` | `plan`)
- **`model`**: derived from model answer or default to `GPT-4o`
- **`tools`**: select the **minimal** set from the tools actually available in the workspace (discovered in Phase 1) that cover the task's needs — do not add tools that are not needed
- **`description`**: one-line summary written from the user's perspective
- **`argument-hint`**: short hint for the chat input field (only include if the prompt uses an input variable)
- **Input variables**: include `${input:name:placeholder}` only if the prompt requires user-provided values; include `${selection}` only if the prompt operates on editor selection; omit both if the prompt is self-contained

---

## Phase 4 — Propose and Confirm

Render the complete proposed prompt in a fenced code block:

````markdown
---
agent: '<resolved value>'
model: <resolved value>   ← omit if user want to use his currently selected model
tools: [<minimal resolved list>]
description: '<one-line summary>'
argument-hint: '<hint text>'   ← omit if no input variable
---

<Body: step-by-step instructions written from the AI's perspective>
<Input variables: only those that are genuinely needed>
````

Then ask:

> **Does this look right?**
>
> - Reply **yes** to save as `.agents/prompts/ai.<name>.prompt.md`
> - Or describe any changes and I'll revise before saving.

Write the file **only after receiving explicit confirmation**.

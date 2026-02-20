---
name: coding-rule-adder
description: >
  Use it when user wants to add new or change existing coding rules to the appropriate category in the coding standards.
  The agent reads the coding standards index, verifies the category, checks for duplicates,
  and updates the file with a new rule after user confirmation.
---

## Instructions

### Purpose

This skill allows agents to add new coding rules to the coding standards. The agent performs all steps using its own file tools — no external scripts are needed.

### Inputs

- **Category**: The category of the coding standard (e.g., `react`, `typescript`).
- **Rule**: The new rule to add as a concise bullet point.

### Outputs

- Updates the relevant category file (`.agents/memory/coding-standards/<category>/index.md`) with the new rule.

### Workflow

Follow these steps **in order** when the user asks to add a coding rule:

#### 1. Resolve the category

- Read the master index at `.agents/memory/coding-standards/index.md`.
- Check whether the requested `<category>` has a matching `## <Category>` heading and a link to `./<category>/index.md`.
- **If the category does not exist:**
  - Show the user the list of available categories.
  - Ask the user whether to create a new category or pick an existing one.
  - If creating a new category:
    1. Create the folder `.agents/memory/coding-standards/<category>/`.
    2. Create `.agents/memory/coding-standards/<category>/index.md` (empty file).
    3. Append a new `## <Category>` section with a link to the master index file.

#### 2. Check for duplicate rules

- Read `.agents/memory/coding-standards/<category>/index.md`.
- Compare the new rule against every existing bullet point. Check for:
  - **Exact match** — the rule text is identical (ignoring leading `-` and trailing whitespace).
  - **Semantic match** — an existing rule conveys the same intent even if worded differently.
- **If a duplicate (exact or semantic) is found:**
  - Show the existing rule to the user.
  - Ask the user what to do:
    - **Skip** — do nothing and stop.
    - **Replace** — remove the old rule and add the new one.
    - **Keep both** — add the new rule alongside the existing one.
  - Follow the user's choice before proceeding.

#### 3. Add the rule

- Append `- <rule>` as a new line at the end of `.agents/memory/coding-standards/<category>/index.md`.
- Confirm to the user: *Rule added successfully to "<category>".*

### Edge Cases

- If the category does not exist, the agent lists available categories and offers to create a new one.
- If the rule already exists (exact or semantic match), the agent shows the duplicate and asks the user how to proceed.
- If the category file is empty, the agent simply adds the rule as the first entry.
- If the master index file is missing, the agent notifies the user and stops.

### Examples

**User:** "Add a React rule: Always use React.memo for pure functional components."

**Agent workflow:**

1. Reads `.agents/memory/coding-standards/index.md` — finds `react` category.
2. Reads `.agents/memory/coding-standards/react/index.md` — no duplicate found.
3. Appends `- Always use React.memo for pure functional components.` to the file.
4. Confirms: *Rule added successfully to "react".*

**User:** "Add a TypeScript rule: Don't specify return types when TypeScript can infer them."

**Agent workflow:**

1. Reads `.agents/memory/coding-standards/index.md` — finds `typescript` category.
2. Reads `.agents/memory/coding-standards/typescript/index.md` — finds existing rule "Avoid declaring return type if Typescript can infer it." (semantic match).
3. Shows the duplicate and asks the user: skip, replace, or keep both.
4. Follows the user's choice.

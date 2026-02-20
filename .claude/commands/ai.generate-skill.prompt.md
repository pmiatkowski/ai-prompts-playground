---
agent: plan
description: Generate an Agent Skill following the agentskills.io specification
argument-hint: Describe the skill to generate, e.g. "parse CSV files and return structured JSON"
---

# Generate Agent Skill

Your goal is to generate a new Agent Skill in the `.agents/skills/` folder, strictly following the [Agent Skills Specification](https://agentskills.io/specification).

## Step 1 — Analyze Requirements

<task_description>
${input}
</task_description>

Evaluate the description against these criteria:

| Criterion        | Resolved? | Notes                         |
|------------------|-----------|-------------------------------|
| **Purpose**      | [ ]       | What does the skill do?       |
| **Trigger**      | [ ]       | When should an agent use it?  |
| **Inputs**       | [ ]       | What inputs does it accept?   |
| **Outputs**      | [ ]       | What does it produce?         |
| **Skill type**   | [ ]       | Agent-native or script-based? |
| **Dependencies** | [ ]       | Any runtime requirements?     |

## Step 2 — Clarify Requirements (if needed)

For each **unresolved** criterion, ask ONE question at a time.

**Skip questions when:**

- The description already answers the question
- The answer can be reasonably inferred
- The question is not relevant (e.g., dependencies for agent-native skill)

### Question Templates

**Purpose** (if unclear):

"What is the primary goal of this skill? What problem does it solve for the user?"

**Trigger** (if unclear):

"When should an agent decide to use this skill? What keywords or situations should trigger it?"

**Inputs** (if unclear):

"What inputs will this skill receive? (file paths, text, configuration options, etc.)"

**Outputs** (if unclear):

"What should this skill produce or output? (files, JSON, text response, etc.)"

**Skill type** (if unclear):

"Can this task be done with agent file tools (read/write), or does it need executable code?"

- A. Agent-native — agent uses its own tools
- B. Script-based — needs executable processing

**Dependencies** (if script-based):

"What runtime or packages does this skill require? (Node.js 22+, Python, specific npm packages, etc.)"

## Step 3 — Determine Skill Type

Decide between these two approaches:

### Agent-native skill (no scripts/)

Use when:

- The agent can perform the task using its built-in tools (read, write, search)
- The task is primarily instruction-based (workflow guidance, rules, procedures)
- No computation or transformation is needed beyond file operations

**Example:** `coding-rule-adder` — agent reads/writes files directly using its tools.

### Script-based skill (requires scripts/)

Use when:

- The task requires computation not achievable with agent tools alone
- Specific runtime processing (parsing, transformation, API calls)
- External dependencies or packages are needed

**Example:** `csv-parser` — needs Node.js to parse CSV files into JSON.

## Step 4 — Determine Skill Name

### Name Constraints

- Must be 1–64 characters
- Only lowercase letters (`a-z`), numbers (`0-9`), and hyphens (`-`)
- Must not start or end with `-`
- Must not contain consecutive hyphens (`--`)
- Must match the parent directory name exactly

### Valid Name Examples

- `csv-parser`
- `image-resizer`
- `api-fetcher`
- `code-review`
- `pdf-processing`

### Invalid Name Examples

- `CSV-Parser` (uppercase not allowed)
- `-csv-parser` (cannot start with hyphen)
- `csv--parser` (consecutive hyphens)
- `csv_parser` (underscores not allowed)
- `csv` (too short/vague)

## Step 5 — Create Directory Structure

Create the following inside `.agents/skills/`:

### Required Structure

```text
.agents/skills/<skill-name>/
└── SKILL.md          # Required
```

### Optional Directories

Add these subdirectories only if needed:

**scripts/** — Executable code that agents can run

- `index.js` — Main script (Node.js 22+)
- Include for script-based skills only

**references/** — Additional documentation (loaded on demand)

- `REFERENCE.md` — Detailed technical reference
- `FORMS.md` — Form templates or structured data formats
- Keep files focused; smaller files use less context

**assets/** — Static resources

- Templates (document templates, configuration templates)
- Images (diagrams, examples)
- Data files (lookup tables, schemas)

## Step 6 — Generate SKILL.md

Generate a `SKILL.md` file with YAML frontmatter followed by Markdown instructions.

### SKILL.md Required Frontmatter

```yaml
---
name: <skill-name>                  # Must match directory name, 1-64 chars
description: >
  <What the skill does and when to use it. 1-1024 chars.
   Include keywords for agent discovery.>
---
```

### SKILL.md Optional Frontmatter Fields

Include these when applicable:

```yaml
---
name: <skill-name>
description: <description>
---
```

### Description Best Practices

**Good example:**

```yaml
description: >
  Parse CSV files and return structured JSON. Use when the user provides CSV data
  or asks to convert spreadsheets, tables, or comma-separated values to JSON format.
```

**Poor example:**

```yaml
description: Helps with CSV files.
```

### SKILL.md Body Sections

Include these sections in the Markdown body:

1. **Instructions** — Step-by-step guide for the agent
2. **Inputs** — Expected input format
3. **Outputs** — Expected output format
4. **Edge Cases** — Common issues and how to handle them
5. **Examples** — Concrete usage examples (optional but recommended)

**Keep SKILL.md under 500 lines.** For detailed content, create separate files in `references/`.

## Step 7 — Generate Scripts (if applicable)

For script-based skills, create `scripts/index.js`:

- Target **Node.js 22+** (use ESM `import`/`export`, `fs/promises`, top-level `await`)
- Script must be self-contained or clearly document dependencies
- Include helpful error messages and graceful edge-case handling
- Use `process.argv` or `stdin` for inputs; output to `stdout`

### Script Template

```javascript
#!/usr/bin/env node

/**
 * <Skill Name> - <Brief description>
 *
 * Usage: node index.js [options] <arguments>
 *
 * Options:
 *   --help     Show help
 *   --output   Output format (json|text)
 */

const fs = require('fs/promises');
const path = require('path');

async function main() {
  const args = process.argv.slice(2);

  if (args.includes('--help')) {
    console.log('Usage: node index.js [options] <arguments>');
    process.exit(0);
  }

  try {
    // Main logic here
    const result = await processInput(args);
    console.log(JSON.stringify(result, null, 2));
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
}

async function processInput(args) {
  // Implementation
  return { success: true };
}

main();
```

## Step 8 — Validate Output

Before presenting the skill, verify:

### Output Validation Checklist

**Frontmatter:**

- [ ] `name` matches the directory name exactly
- [ ] `name` is 1-64 chars, lowercase, no leading/trailing hyphens
- [ ] `description` is 1-1024 characters and describes when to use the skill
- [ ] Optional fields are only included when meaningful

**Structure:**

- [ ] `SKILL.md` exists and is valid YAML + Markdown
- [ ] Scripts (if any) are in `scripts/` directory
- [ ] Reference files (if any) are in `references/` directory
- [ ] Assets (if any) are in `assets/` directory

**Content:**

- [ ] Instructions are clear and actionable
- [ ] Inputs and outputs are documented
- [ ] Edge cases are addressed

## Skill Generation Constraints

- One skill per directory — do not combine unrelated functionality
- `name` in `SKILL.md` frontmatter **must exactly match** the parent directory name
- Do not use `--` in the skill name
- Keep skill scope focused; create separate skills for separate concerns
- Keep `SKILL.md` under 500 lines

## Specification Reference

This prompt generates skills compliant with [agentskills.io](https://agentskills.io/specification).

### Key Concepts

**Progressive Disclosure:**

1. **Metadata** (~100 tokens): `name` and `description` loaded at startup for discovery
2. **Instructions** (<5000 tokens recommended): Full SKILL.md loaded when skill is activated
3. **Resources** (as needed): Files in `references/` and `assets/` loaded only when required

**File References:**

- Use relative paths from skill root
- Keep references one level deep from `SKILL.md`
- Avoid deeply nested reference chains

**Validation Tool:**

```bash
skills-ref validate ./my-skill
```

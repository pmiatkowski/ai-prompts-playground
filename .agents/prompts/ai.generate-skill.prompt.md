---
agent: 'agent'
model: GPT-4o
tools: ['search', 'read', 'write', 'web']
description: 'Generate an Agent Skill following the agentskills.io specification'
argument-hint: 'Describe the skill to generate, e.g. "parse CSV files and return structured JSON"'
---

Your goal is to generate a new Agent Skill in the `.agents/skills/` folder, strictly following the [Agent Skills Specification](https://agentskills.io/specification).

## Step 1 — Clarify Requirements

Before generating, ask the user these questions if any are unclear (max 3):

1. What does the skill do, and when should an agent use it?
2. What inputs does it accept and what outputs does it produce?
3. Are there any environment dependencies or runtime constraints beyond Node.js 22+?

## Step 2 — Determine Skill Name

- Must be lowercase, 1–64 characters, only `a-z`, `0-9`, and `-`
- Must not start or end with `-`, and must not contain `--`
- Example: `csv-parser`, `image-resizer`, `api-fetcher`

## Step 3 — Create Directory Structure

Create the following inside `.agents/skills/`:

```
.agents/skills/<skill-name>/
├── SKILL.md          # Required
└── scripts/
    └── index.js      # Main script (Node.js 22+)
```

### Optional Directories

Add these subdirectories only if needed:

**scripts/**
Contains executable code that agents can run. Scripts should:

- Be self-contained or clearly document dependencies
- Include helpful error messages
- Handle edge cases gracefully
- Supported languages depend on the agent implementation. Common options include Python, Bash, and JavaScript.

**references/**
Contains additional documentation that agents can read when needed:

- `REFERENCE.md` - Detailed technical reference
- `FORMS.md` - Form templates or structured data formats
- Domain-specific files (`finance.md`, `legal.md`, etc.)

Keep individual reference files focused. Agents load these on demand, so smaller files mean less use of context.

**assets/**
Contains static resources:

- Templates (document templates, configuration templates)
- Images (diagrams, examples)
- Data files (lookup tables, schemas)

## Step 4 — Generate `SKILL.md`

The file must have YAML frontmatter followed by Markdown instructions:

```yaml
---
name: <skill-name>             # must match the directory name
description: >
  <What the skill does and when an agent should use it. 1–1024 chars.
   Include specific keywords to help agents identify relevant tasks.>
compatibility: Requires Node.js 22+
---
```

Body sections to include:

- Step-by-step instructions for the agent
- Expected inputs and outputs
- Common edge cases and how to handle them
- Reference to the main script: `scripts/index.js`

Keep `SKILL.md` under 500 lines.

## Step 5 — Generate `scripts/index.js`

- Target **Node.js 22+** (use ESM `import`/`export`, `fs/promises`, top-level `await` where appropriate)
- Script must be self-contained or clearly document its dependencies
- Include helpful error messages and graceful edge-case handling
- Use `process.argv` or `stdin` for inputs; output to `stdout`

## Constraints

- One skill per directory — do not combine unrelated functionality
- `name` in `SKILL.md` frontmatter **must exactly match** the parent directory name
- Do not use `--` in the skill name
- Keep skill scope focused; create separate skills for separate concerns

## Input

${input:skillDescription:Describe the skill to generate, e.g. "parse CSV files and return structured JSON"}

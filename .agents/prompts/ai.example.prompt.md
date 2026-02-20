---
agent: 'agent'
model: GPT-4o
tools: ['githubRepo', 'search/codebase', 'read', 'edit']
description: 'Example prompt file for Copilot Prompt Files (usage example: feature="add OAuth login")'
---
Your goal is to generate a concise implementation task plan and starter code guidance based on the current workspace context.

If required details are missing, ask for:

- The feature name
- The target folder or file
- Any framework/runtime constraints

Use these references when available:

- Tool example: #tool:githubRepo owner/repo
- File reference example: [Implementation Guide](../docs/implementation-guide.md)

Requirements:

- Keep output practical and step-by-step
- Prefer minimal, production-ready code
- Call out assumptions explicitly
- Include a short validation checklist

Input from editor selection (if any):
${selection}

Optional user input:
${input:feature:Describe the feature to implement}

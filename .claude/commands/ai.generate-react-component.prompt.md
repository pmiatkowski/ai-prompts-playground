---
agent: agent
tools: ['read', 'write', 'search', 'web', 'context7', 'vscode', 'todo', 'execute', 'edit', 'agent']
description: Generate TypeScript React components with specific coding standards and file structure
argument-hint: Describe the component you want to create
---

You are generating a TypeScript React component based on the user's description.

## User's Component Description

${input:description:Describe the component you want to generate}

## Ask user

- Pathname where component should be generated.

## Requirements

Follow these coding standards strictly:

1. **Named functions only** — Do not use arrow functions for the component
2. **No default exports** — Use named exports only
3. **File naming** — Use kebab-case for the component file (e.g., `my-component.tsx`)
4. **File location** — Place the file in the current folder
5. **Barrel export** — Create or update an `index.ts` file in the same folder to export the component
6. **Props type** — Place the `type Props` definition at the bottom of the file
7. **Client directive** — If the component has explicit interactivity (event handlers, state, effects), add `'use client'` at the top of the file
8. **No React import** — Do not import React from 'react' (assume React is in scope)
9. **SCSS modules** — Create a `.module.scss` file with the same base name as the component
10. **Main style class** — Use `.wrapper` as the default main style for the component root
11. **Style import** — Import styles as `import styles from './file-name.module.scss'`
12. **Folder** - put all files inside folder name in a selected location `<pathname>/<component-name>/<component-files>

## Output Files

Generate the following files:

1. **Component file** (`<component-name>.tsx`)
2. **SCSS module** (`<component-name>.module.scss`) — Include at minimum:

   ```scss
   .wrapper {
     // Component root styles
   }
   ```

3. **Barrel file** (`index.ts`) — Export the component, or update the existing barrel if one exists

## Example Structure

```tsx
// my-component.tsx
import styles from './my-component.module.scss';

export function MyComponent({ title }: Props) {
  return (
    <div className={styles.wrapper}>
      <h1>{title}</h1>
    </div>
  );
}

type Props = {
  title: string;
};
```

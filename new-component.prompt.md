---
description: "Generate a new React component with TypeScript, Tailwind CSS, RTL support, and tests"
---

# New React Component

Create a new React component for the Kun Sharik platform.

## Requirements
- **Component Name**: ${input:Component name in PascalCase}
- **Module**: ${input:Module name or 'shared' for shared components}
- **Type**: ${input:Server Component or Client Component}
- **Has Form**: ${input:Does it include a form? (yes/no)}

## Generate These Files

### 1. Component (`src/{location}/{ComponentName}/index.tsx`)
- Server Component by default — only add `"use client"` if state/effects/events are needed
- Props interface: `{ComponentName}Props`
- Tailwind CSS with logical properties (ps/pe/ms/me, not pl/pr/ml/mr)
- Support RTL via `dir` attribute awareness
- Arabic as default language

### 2. Types (`src/{location}/{ComponentName}/types.ts`)
- Props interface
- Any internal types
- Use `Money` type for financial displays

### 3. Test (`src/{location}/{ComponentName}/{ComponentName}.test.tsx`)
- Render test (both RTL and LTR)
- Props handling
- User interaction (if client component)
- Accessibility check

### 4. Form Schema (if applicable) (`src/{location}/{ComponentName}/schema.ts`)
- Zod validation schema
- Arabic error messages
- Proper types for all fields

## RTL Checklist
- [ ] Uses logical CSS properties (ps/pe not pl/pr)
- [ ] Text alignment is `text-start` not `text-left`
- [ ] Icons flip appropriately in RTL
- [ ] Number formatting uses Arabic numerals in AR mode
- [ ] Currency shows SAR symbol correctly

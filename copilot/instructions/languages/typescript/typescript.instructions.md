---
applyTo: '**/*.ts,**/*.tsx'
---
# TypeScript Instructions

- **CRITICAL**: You MUST ALWAYS read and follow ALL instructions from ALL files from:
    - [./typescript-standards.md](./typescript-standards.md)

## Implementation Requirements

When implementing any TypeScript-related functionality:

- You must have read the complete TypeScript documentation before proceeding
- You must adhere to all guidelines provided in the TypeScript documentation
- You must implement all instructions exactly as specified

## Table of Contents

- [Project Structure & Management](#project-structure--management)
  - [Standard Project Layout](#standard-project-layout)
  - [TypeScript Project Internal Structure](#typescript-project-internal-structure)
  - [Managing TypeScript Projects](#managing-typescript-projects)
- [Keeping Up-to-Date](#keeping-up-to-date)
  - [Latest TypeScript Versions](#latest-typescript-versions)

## Project Structure & Management

### Standard Project Layout

- `package.json`: Root of the working directory
- `tsconfig.json`: TypeScript configuration file
- `src/`: Contains source code files
- `src/index.ts`: Main entry point
- `tests/`: Contains test files
- `dist/` or `build/`: Output directory (in .gitignore)

#### Folder Layout Example

```plaintext
package.json
tsconfig.json
jest.config.js (or other test config)
src/
  index.ts
  types/
    index.ts
  components/
    Component.tsx
  services/
    Service.ts
tests/
  Component.test.tsx
  Service.test.ts
dist/ (or build/)
```

### TypeScript Project Internal Structure

Prefer simple project folder structures. ALWAYS follow existing project conventions. For new projects:

- `src/types/`: For shared type definitions and interfaces
- `src/components/`: For React/UI components (if applicable)
- `src/hooks/`: For React hooks (if applicable)
- `src/services/`: For business logic and API calls
- `src/utils/`: For utility functions
- `src/constants/`: For constant values and enums
- `src/context/`: For React context (if applicable)
- `src/store/`: For state management (Redux, MobX, etc.)
- `src/assets/`: For static assets

Group related functionality into feature folders when using feature-based architecture.

### Managing TypeScript Projects

ALWAYS use the package manager CLI (npm/yarn/pnpm) for:

- **Project Initialization**:
  - Check existing templates: `npm create vite@latest` or `npx create-next-app@latest`
  - ALWAYS use a template when available
  - Initialize TypeScript: `npm init -y && npm add -D typescript @types/node`
- **Adding Dependencies**:
  - Check existing: `npm list` or `yarn list`
  - Development dependencies: `npm add -D package-name`
  - Runtime dependencies: `npm add package-name`
  - Types: `npm add -D @types/package-name`
- **TypeScript Configuration**:
  - Initialize: `npx tsc --init`
  - ALWAYS set strict mode: `"strict": true`
  - Configure paths and output
- **Verification**:
  - Type check: `npx tsc --noEmit`
  - Lint: `npm run lint` (if ESLint configured)
  - Test: `npm test` (with configurations)
  - Build: `npm run build`

## Keeping Up-to-Date

### Latest TypeScript Versions

ALWAYS prefer the latest TypeScript version unless specified otherwise. Refer to official TypeScript documentation for the latest features:

- [What's new in TypeScript 5.0](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-0.html)
- [What's new in TypeScript 5.1](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-1.html)
- [What's new in TypeScript 5.2](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-2.html)
- [What's new in TypeScript 5.3](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-3.html)

# SUSE AI Rancher Extension - Agent Guidelines

## Commands
- **Build**: `yarn build-pkg suseai-rancher-ext`
- **Dev**: `yarn dev`
- **Type check**: `yarn typecheck` (vue-tsc --noEmit)
- **Test**: No test runner configured - use `yarn typecheck` for validation
- **Clean**: `yarn clean`
- **Serve packages**: `yarn serve-pkgs`
- **Publish packages**: `yarn publish-pkgs`

## Code Style
- **Types/Interfaces**: PascalCase (`AppStatus`)
- **Variables/Functions**: camelCase (`appStatus`, `getErrorMessage`)
- **Constants**: UPPER_SNAKE_CASE (`DEFAULT_TIMEOUT`)
- **Files**: kebab-case for Vue components, camelCase for TS files

## Imports
```typescript
// External libs first, then internal utils, type imports, local types
import axios from 'axios'
import { ref } from 'vue'
import { logger } from '../utils/logger'
import type { RancherStore } from '../types/rancher-types'
```

## Vue Components
- Use Composition API with `defineComponent` and setup function
- Define props with types, defaults, validation
- Explicitly declare emitted events
- Scoped styles with CSS variables
- Use `onErrorCaptured` in error boundaries

## Error Handling
- Wrap async operations in try/catch
- Use custom error handlers for consistency
- Implement error boundaries for component isolation
- Use centralized logger for debugging

## Type Safety
- Avoid `any` - use proper types/interfaces
- Prefer discriminated unions for state management
- Implement type guards for runtime checking
- Use bounded generics where appropriate
- Target ES2018, strict mode enabled
- Path mappings: `~/*` and `@/*` resolve to project root, `@shell/*` to Rancher shell

## Formatting
- 2 spaces indentation, no semicolons (ASI)
- Single quotes for strings, double for JSX attributes
- Reasonable line lengths, break long lines appropriately

## Dependencies & Setup
- Vue CLI 5.x with TypeScript support
- Rancher Shell ^3.0.4 as base framework
- Axios ^1.7.2 for HTTP requests
- js-yaml ^4.1.0 for YAML processing
- Node.js >=20 required

## Workflow
- Run `yarn typecheck` before commits
- Run `yarn build-pkg` to verify builds
- Use feature flags for version-aware functionality
- Update README for architectural changes
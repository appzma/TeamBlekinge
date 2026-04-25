# AGENT.md

## Stack
- TypeScript (strict mode) + React + Vite
- Styling: Tailwind CSS
- State: Zustand or React Context (no Redux)
- Data fetching: TanStack Query or native `fetch`
- Testing: Vitest + React Testing Library
- Linting: ESLint + Prettier (configs are authoritative — never disable rules inline)

## Project Structure
```
src/
  components/     # Reusable UI, one component per file
  pages/          # Route-level components
  hooks/          # Custom React hooks (use* prefix)
  store/          # Zustand slices or Context providers
  lib/            # Pure utility functions, no React imports
  types/          # Shared TypeScript interfaces and types
  api/            # fetch wrappers and API contracts
public/           # Static assets
tests/            # Integration tests mirroring src/ structure
```

## TypeScript Rules
- `strict: true` — no exceptions
- No `any`. Use `unknown` and narrow it
- No type assertions (`as Foo`) unless unavoidable; comment why
- Prefer `interface` for object shapes, `type` for unions/intersections
- Export types explicitly; never rely on inferred return types for public functions
- Enums are forbidden — use `as const` objects or union string literals

## Component Rules
- Functional components only
- Props interface defined above the component, named `[ComponentName]Props`
- No inline styles — Tailwind classes only
- Keep components under 150 lines; split if exceeded
- Co-locate component-specific hooks and helpers in the same file unless reused elsewhere
- No `useEffect` for derived state — compute inline or use `useMemo`

## State & Data
- Server state lives in TanStack Query — never duplicate it in Zustand
- Client-only UI state lives in Zustand or local `useState`
- No prop drilling beyond two levels — lift to store or Context
- API calls live exclusively in `src/api/`; components never call `fetch` directly

## Naming
- Components, types, interfaces: `PascalCase`
- Functions, variables, hooks: `camelCase`
- Constants: `SCREAMING_SNAKE_CASE`
- Files: `kebab-case` except component files which match their export (`Button.tsx`)
- Test files: `[subject].test.ts` or `[subject].test.tsx`

## Testing
- Every component gets a test file
- Test behavior, not implementation — no snapshot tests
- Mock only at the boundary (`src/api/` layer); never mock internal modules
- Coverage threshold: 80% lines/branches enforced in CI
- Tests must pass before any commit — no skipped tests in main branch

## Backend / API
- Prefer no backend: use static hosting + third-party services (Auth0, Supabase, etc.)
- If a backend is required, use a single serverless function file per route in `api/` (Vercel/Netlify conventions)
- No Express servers, no custom Node HTTP layer
- All environment variables prefixed `VITE_` for client exposure; never expose secrets to the client

## Formatting
- Prettier enforces 2-space indent, single quotes, trailing commas, 100-char line length
- ESLint `import/order` rule is enforced — never reorder imports manually
- Run `pnpm lint && pnpm test` before marking any task complete

## Docker / Deployment
- Use multi-stage Dockerfile: build with Node, serve with nginx
- Static assets built to `dist/` — serve these directly
- Environment variables injected at build time for Vite, never at runtime
- Docker image exposes port 80, runs as non-root user
- No hot reload in production — development only

## Hard Rules
- Never commit commented-out code
- Never use `console.log` in production paths — use a logger abstraction or remove it
- Never merge failing tests
- Never widen a type to fix a type error — fix the logic
- Never install a library for something achievable in under 20 lines of TypeScript
- Always read existing code before writing new code
- Always test with QA agent before creating PR
- Always create PR after successful QA

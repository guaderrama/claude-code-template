# Proyecto: [NOMBRE_DEL_PROYECTO]

[Breve descripcion del proyecto y sus caracteristicas principales]

## Tech Stack
- **Runtime**: Node.js + TypeScript
- **Framework**: Next.js 16 (App Router)
- **Base de Datos**: PostgreSQL/Supabase
- **Styling**: Tailwind CSS
- **State**: Zustand
- **Testing**: Jest + React Testing Library
- **Validation**: Zod

## Architecture: Feature-First
```
src/
├── app/              # Next.js App Router (routes)
├── features/         # Feature modules (auth/, dashboard/, etc.)
│   └── [feature]/    # components/, hooks/, services/, types/, store/
└── shared/           # Reusable code
    ├── components/   # UI components (shadcn in ui/)
    ├── hooks/        # Generic hooks
    ├── lib/          # Configs (supabase.ts, axios.ts)
    ├── types/        # Shared types
    └── utils/        # Utility functions
```

## IMPORTANT: Commands
- `npm run dev` — Dev server (auto-detects port 3000-3006)
- `npm run build` — Production build
- `npm run test` — Run tests
- `npm run lint` — ESLint
- `npm run typecheck` — TypeScript check
- `npx supabase start` — Local Supabase
- `npx supabase db reset` — Reset database

## IMPORTANT: Code Conventions
- **Files**: Max 500 lines. **Functions**: Max 50 lines.
- **Naming**: camelCase (vars/fns), PascalCase (components), UPPER_SNAKE_CASE (constants), kebab-case (files/folders)
- **TypeScript**: Always type function signatures. Use `unknown` not `any`. Interfaces for objects, Types for unions.
- **Testing**: TDD (Red-Green-Refactor). AAA pattern (Arrange-Act-Assert). 80%+ coverage.
- **Git**: Conventional Commits (`feat(scope): desc`). Feature branches. PR required for main/develop.

## IMPORTANT: Prohibitions
- NEVER use `any` in TypeScript
- NEVER commit without tests
- NEVER skip error handling
- NEVER expose secrets in code
- NEVER edit files in `src/legacy/`
- NEVER create circular dependencies
- NEVER hardcode configurations

## Workflow: PLAN > DIFFS > VERIFY
For significant changes: 1) Explain what you'll do. 2) Show exact diffs. 3) Give verification commands.
Skip for trivial changes (typos, minor styling).

## References
- See @.claude/INDEX.md for complete project map
- See @.claude/docs/ for detailed guides (WORKFLOW.md, ARCHITECTURE.md, GIT_WORKFLOW.md)
- See @.claude/memory/TODO.md for current priorities
- See @.claude/memory/DECISIONS.md for past decisions
- See @.claude/snippets/commands.md for full command reference

<!-- Path-specific rules in .claude/rules/ are loaded automatically when matching files are accessed -->
<!-- Specialized knowledge lives in .claude/skills/ and loads on demand -->
<!-- Auto-memory and auto-dream handle session continuity automatically -->

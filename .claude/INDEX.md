# Claude Code Project Index

Welcome to the project documentation center!

## 📋 Project Setup Documentation

Start here to understand the project infrastructure:

### Quick References
1. **[INFRASTRUCTURE_SUMMARY.md](./INFRASTRUCTURE_SUMMARY.md)** - Overview of everything that was set up
2. **[docs/QUICK_START.md](./docs/QUICK_START.md)** - Quick start guide for common tasks

### Detailed Guides
1. **[docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)** - Complete architecture explanation
2. **[docs/FEATURE_TEMPLATE.md](./docs/FEATURE_TEMPLATE.md)** - Step-by-step feature creation guide
3. **[docs/GIT_WORKFLOW.md](./docs/GIT_WORKFLOW.md)** - Git branching and commit standards
4. **[docs/WORKFLOW.md](./docs/WORKFLOW.md)** - PLAN → DIFFS → VERIFY development process
5. **[docs/CLAUDE_CODE_COMMANDS.md](./docs/CLAUDE_CODE_COMMANDS.md)** - Native Claude Code commands (/compact, /cost, /memory, etc.)
6. **[docs/MCP_GUIDE.md](./docs/MCP_GUIDE.md)** - MCP server configuration and usage
7. **[docs/CONTEXT_MANAGEMENT.md](./docs/CONTEXT_MANAGEMENT.md)** - Context optimization and cost reduction

---

## 🤖 Skills — Slash Commands

Todos invocables con `/nombre`. Formato Skills 2.0.

### Migration & Setup
- **[skills/inicializar-proyecto/SKILL.md](./skills/inicializar-proyecto/SKILL.md)** — `/inicializar-proyecto` — Restructure any project to use optimized Claude Code template
- **[skills/migrar-ui-ux-2026/SKILL.md](./skills/migrar-ui-ux-2026/SKILL.md)** — `/migrar-ui-ux-2026` — Migrate existing projects to UI/UX Pro 2026 setup

### Development Workflows
- **[skills/bucle-agentico/SKILL.md](./skills/bucle-agentico/SKILL.md)** — `/bucle-agentico` — Agentic loop for complex features
- **[skills/explorador/SKILL.md](./skills/explorador/SKILL.md)** — `/explorador` — Explore and understand a project
- **[skills/ejecutar-prp/SKILL.md](./skills/ejecutar-prp/SKILL.md)** — `/ejecutar-prp` — Execute a PRP file end-to-end
- **[skills/generar-prp/SKILL.md](./skills/generar-prp/SKILL.md)** — `/generar-prp` — Generate Product Requirements Proposal
- **[skills/arreglar-issue-github/SKILL.md](./skills/arreglar-issue-github/SKILL.md)** — `/arreglar-issue-github` — Fix GitHub issues end-to-end

### Parallel Execution
- **[skills/preparar-paralelo/SKILL.md](./skills/preparar-paralelo/SKILL.md)** — `/preparar-paralelo` — Initialize parallel git worktrees
- **[skills/ejecutar-paralelo/SKILL.md](./skills/ejecutar-paralelo/SKILL.md)** — `/ejecutar-paralelo` — Run N parallel implementations

### Security
- **[skills/security-review/SKILL.md](./skills/security-review/SKILL.md)** — `/security-review` — OWASP Top 10 audit before deploy

### Skill Optimization
- **[skills/optimize-skill/SKILL.md](./skills/optimize-skill/SKILL.md)** — `/optimize-skill [name]` — Auto-research optimize a single skill
- **[skills/optimize-all-skills/SKILL.md](./skills/optimize-all-skills/SKILL.md)** — `/optimize-all-skills` — Batch optimize all skills

---

## 🎯 Specialized Agents

Agents that automate specific tasks:

- **[agents/gestor-documentacion.md](./agents/gestor-documentacion.md)** - Documentation management
- **[agents/validacion-calidad.md](./agents/validacion-calidad.md)** - Quality validation and testing

---

## 🛡️ Path-Scoped Rules

Rules that load automatically when matching files are accessed (zero cost when not triggered):

- **[rules/frontend.md](./rules/frontend.md)** — `src/**/*.tsx`, `src/**/*.css` — UI/UX, spacing, accessibility, responsive
- **[rules/database.md](./rules/database.md)** — `supabase/**`, `src/**/services/**` — RLS, migrations, queries, schema
- **[rules/testing.md](./rules/testing.md)** — `src/**/*.test.*`, `tests/**` — TDD workflow, AAA pattern, coverage
- **[rules/security.md](./rules/security.md)** — `src/app/api/**`, `middleware.*` — OWASP, auth, input validation

---

## 🧠 Memory & Context System

Session continuity is handled automatically via **auto-memory** and **auto-dream** (enabled in settings.local.json). No manual NOTES.md updates needed.

### Manual Tracking (when needed)
- **[memory/TODO.md](./memory/TODO.md)** - Task priorities (high/medium/low)
- **[memory/DECISIONS.md](./memory/DECISIONS.md)** - Technical decisions with context
- **[memory/BLOCKERS.md](./memory/BLOCKERS.md)** - Active blockers and resolution status
- **[memory/NOTES.md](./memory/NOTES.md)** - Session notes (optional, auto-memory preferred)

### Context Optimization
- See **[docs/CONTEXT_MANAGEMENT.md](./docs/CONTEXT_MANAGEMENT.md)** for cost reduction strategies

---

## 📋 Task Management

Detailed documentation for complex features:

- **[task/0001-template.md](./task/0001-template.md)** - Complete task documentation template
  - Objective and success criteria
  - Implementation plan
  - Technical details
  - Testing strategy
  - Verification commands

**Usage:**
```
Copy template: cp task/0001-template.md task/0002-auth-feature.md
Track feature: Document progress in task file
Reference: "Review task/0002-auth-feature.md for context"
```

---

## 💡 Code Snippets & Quick Reference

Fast access to common commands and configurations:

### Command Reference
- **[snippets/commands.md](./snippets/commands.md)** - Complete development commands
  - Development server commands
  - Testing commands
  - Database operations (Supabase)
  - Git workflows
  - Package management
  - Debugging techniques
  - Performance analysis

### Configuration Templates
- **[snippets/gitignore.txt](./snippets/gitignore.txt)** - Complete .gitignore template
  - Dependencies
  - Build outputs
  - Environment variables
  - IDE files
  - OS files
  - Claude/Cline system

**Usage:**
```
Find command: "Check snippets/commands.md for database reset"
Copy .gitignore: cp snippets/gitignore.txt ../.gitignore
Reference: Keep commands.md open while developing
```

---

## 🛠️ Skills & Tools

Specialized skills for building features:

### UI/UX & Design
- **[skills/ui-ux-pro-max-skill/README.md](./skills/ui-ux-pro-max-skill/README.md)** - Professional UI/UX design system (⭐ 25,300+ stars)
  - 67 UI component styles
  - 10 complete dashboard templates
  - 96 professional color palettes
  - 25 chart and visualization types
  - 100+ design reasoning rules
- **[prompts/ui-design-starter.md](./prompts/ui-design-starter.md)** - UI/UX starter prompts and workflows

### Planning & Design
- **[skills/brainstorming/SKILL.md](./skills/brainstorming/SKILL.md)** - Structured brainstorming before any creative work
  - Uso: `/brainstorming` o automático al crear features
- **[skills/frontend-design/SKILL.md](./skills/frontend-design/SKILL.md)** - Distinctive, production-grade frontend interfaces (Anthropic oficial)
  - Anti "AI slop", bold aesthetic direction, production-grade code

### Quality & Testing
- **[skills/test-driven-development/SKILL.md](./skills/test-driven-development/SKILL.md)** - TDD obligatorio: RED → GREEN → REFACTOR
  - No production code without a failing test first
- **[skills/systematic-debugging/SKILL.md](./skills/systematic-debugging/SKILL.md)** - Debugging como senior developer
  - 4 fases: Root Cause → Pattern Analysis → Hypothesis → Implementation
- **[skills/verification-before-completion/SKILL.md](./skills/verification-before-completion/SKILL.md)** - Evidence before claims, always
  - No "ya quedó" sin ejecutar npm run build/test/typecheck
- **[skills/webapp-testing/SKILL.md](./skills/webapp-testing/SKILL.md)** - Testing visual de web apps con Playwright (Anthropic oficial)
  - Screenshots, DOM inspection, debugging UI, servidor automático

### Performance & Best Practices
- **[skills/react-best-practices/SKILL.md](./skills/react-best-practices/SKILL.md)** - 57 reglas React/Next.js de Vercel Engineering
  - Waterfalls, bundle size, server performance, re-renders, rendering
- **[skills/supabase-postgres-best-practices/SKILL.md](./skills/supabase-postgres-best-practices/SKILL.md)** - Best practices Postgres de Supabase oficial
  - Query performance, connection mgmt, RLS, schema design, indexing

### AI & ML
- **[skills/agent-builder-vercel-sdk/SKILL.md](./skills/agent-builder-vercel-sdk/SKILL.md)** - Build AI agents with Vercel AI SDK + OpenRouter

### Database & Auth
- **[skills/supabase-auth-memory/SKILL.md](./skills/supabase-auth-memory/SKILL.md)** - Supabase authentication and memory

### Web Development
- **[skills/nextjs-16-complete-guide/SKILL.md](./skills/nextjs-16-complete-guide/SKILL.md)** - Next.js 16 complete guide
- **[skills/skill-creator/SKILL.md](./skills/skill-creator/SKILL.md)** - Create custom skills

---

## 📚 Core Project Files

### Root Configuration
- **[../CLAUDE.md](../CLAUDE.md)** - Development principles and context
- **[../README.md](../README.md)** - Project overview
- **[../package.json](../package.json)** - Dependencies and scripts
- **[../tsconfig.json](../tsconfig.json)** - TypeScript configuration
- **[../next.config.js](../next.config.js)** - Next.js configuration
- **[../tailwind.config.ts](../tailwind.config.ts)** - Tailwind CSS config

### Source Code Structure
```
../src/
├── app/              # Next.js App Router (pages & routes)
├── features/         # Feature modules (your code goes here)
└── shared/           # Reusable utilities & components
```

---

## 🚀 Getting Started

### First Time Setup
1. Read [INFRASTRUCTURE_SUMMARY.md](./INFRASTRUCTURE_SUMMARY.md)
2. Review [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)
3. Understand [docs/WORKFLOW.md](./docs/WORKFLOW.md) (PLAN → DIFFS → VERIFY)
4. Check [docs/QUICK_START.md](./docs/QUICK_START.md) for commands

### Daily Development Workflow
1. **Start session**: Auto-memory restores context automatically
2. **Check tasks**: Review [memory/TODO.md](./memory/TODO.md) for priorities
3. **Develop**: Follow [docs/WORKFLOW.md](./docs/WORKFLOW.md) process
4. **Reference**: Use [snippets/commands.md](./snippets/commands.md) or [docs/CLAUDE_CODE_COMMANDS.md](./docs/CLAUDE_CODE_COMMANDS.md)
5. **End session**: Auto-dream consolidates learnings

### For New Features
1. Read [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)
2. Follow [docs/FEATURE_TEMPLATE.md](./docs/FEATURE_TEMPLATE.md)
3. Use `/bucle-agentico` workflow
4. Document in [task/](./task/) if complex

### For AI-Assisted Development
1. Use `/bucle-agentico` to start feature loops
2. Use `/explorador` to understand codebase
3. Use agents for quality validation and docs
4. Reference [docs/WORKFLOW.md](./docs/WORKFLOW.md) for structured development

---

## 🔄 Development Process (PLAN → DIFFS → VERIFY)

This project follows a structured workflow for all significant changes:

1. **PLAN** - Explain what will be done before doing it
2. **DIFFS** - Show exact changes before applying
3. **VERIFY** - Provide commands to validate changes work

See [docs/WORKFLOW.md](./docs/WORKFLOW.md) for complete details.

**Usage:**
```
"Use the workflow from WORKFLOW.md for this feature"
"Show me the PLAN before making changes"
"Give me VERIFY commands after applying"
```

---

## 📊 Project Stats

- **Framework**: Next.js 16
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **Database**: Supabase (PostgreSQL)
- **State**: Zustand
- **Testing**: Jest + React Testing Library

---

## ✅ Quick Checklist

### Initial Setup
- [ ] Read INFRASTRUCTURE_SUMMARY.md
- [ ] Review ARCHITECTURE.md
- [ ] Understand WORKFLOW.md
- [ ] Understand project structure
- [ ] Run `npm install`
- [ ] Create `.env.local` from `.env.example`
- [ ] Run `npm run dev` to start development
- [ ] Review CLAUDE.md for coding standards

### Daily Workflow
- [ ] Check memory/TODO.md for priorities
- [ ] Document decisions in memory/DECISIONS.md (when relevant)
- [ ] Track blockers in memory/BLOCKERS.md (when relevant)
- [ ] Use WORKFLOW.md process for changes
- [ ] Auto-memory handles session continuity automatically

---

## 🎯 Common Tasks

Choose what you want to do:

### Documentation & Planning
1. **Review progress** → Read [memory/NOTES.md](./memory/NOTES.md)
2. **Check tasks** → See [memory/TODO.md](./memory/TODO.md)
3. **Understand decisions** → Read [memory/DECISIONS.md](./memory/DECISIONS.md)
4. **View blockers** → Check [memory/BLOCKERS.md](./memory/BLOCKERS.md)

### Development
1. **Start a new feature** → Read [docs/FEATURE_TEMPLATE.md](./docs/FEATURE_TEMPLATE.md)
2. **Understand the architecture** → Read [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md)
3. **Follow workflow** → Use [docs/WORKFLOW.md](./docs/WORKFLOW.md)
4. **Need commands** → Check [snippets/commands.md](./snippets/commands.md)

### UI/UX Design
1. **Create professional UI** → Use [prompts/ui-design-starter.md](./prompts/ui-design-starter.md)
2. **Generate dashboard** → Reference [skills/ui-ux-pro-max-skill/README.md](./skills/ui-ux-pro-max-skill/README.md)
3. **Install components** → Use shadcn MCP (auto-configured)
4. **Visual testing** → Use Playwright MCP for screenshots
5. **Design guidelines** → See [../CLAUDE.md](../CLAUDE.md) Frontend Aesthetics section

### Git & Collaboration
1. **Git workflow** → See [docs/GIT_WORKFLOW.md](./docs/GIT_WORKFLOW.md)
2. **Create PRP** → Use `/generar-prp`
3. **Fix issue** → Use `/arreglar-issue-github`

### Advanced
1. **Create a custom skill** → See [skills/skill-creator/SKILL.md](./skills/skill-creator/SKILL.md)
2. **Complex feature** → Document in [task/](./task/)

### Migration (Existing Projects)
1. **Add all skills to existing project** → Copy `AGREGAR_UI_UX_PRO.md` to project root and tell Claude to execute it
2. **Migrate with slash command** → Run `/migrar-ui-ux-2026`

---

## 💬 Working with Claude

### Essential Phrases

**Session Management:**
```
"Add to TODO.md: [new task]"
"Check /cost to see token usage"
"/compact [focus topic]" to compress context
```

**Development Process:**
```
"Use the WORKFLOW.md process for this feature"
"Follow PLAN → DIFFS → VERIFY workflow"
"Show me the PLAN before implementing"
```

**Quick Reference:**
```
"Check snippets/commands.md for [command type]"
"What's the command for [action]?"
"How do I [task]? Check snippets"
```

**Decision Making:**
```
"Document this decision in DECISIONS.md"
"Why did we choose [X]? Check DECISIONS.md"
```

**Problem Solving:**
```
"Add blocker to BLOCKERS.md: [issue]"
"Check BLOCKERS.md for similar issues"
```

**UI/UX Design:**
```
"Usa ui-ux-pro-max-skill para generar un dashboard profesional"
"Check prompts/ui-design-starter.md for design workflows"
"Install [component] usando shadcn MCP"
"Evita AI slop aesthetic - check CLAUDE.md Frontend Aesthetics"
"Captura screenshot con Playwright MCP para validar diseño"
```

---

## 🎓 Learning Resources

### For Claude
- Read [../CLAUDE.md](../CLAUDE.md) for project context
- Check [memory/](./memory/) for project history
- Reference [docs/](./docs/) for detailed guides
- Use [snippets/](./snippets/) for quick commands

### For Developers
- Start with [docs/QUICK_START.md](./docs/QUICK_START.md)
- Follow [docs/WORKFLOW.md](./docs/WORKFLOW.md)
- Reference [snippets/commands.md](./snippets/commands.md)
- Track work in [memory/](./memory/)

---

**Last Updated**: Apr 2, 2026
**Status**: ✅ Infrastructure Ready | 🧠 Auto-Memory Active | 🔄 Workflow Integrated | 🎨 UI/UX Pro System Enabled | ⚡ Vercel + Supabase Best Practices | 🛡️ Path-Scoped Rules | 🤖 Skills 2.0 with Model Routing

---
paths:
  - "src/**/*.tsx"
  - "src/**/*.css"
  - "src/shared/components/**"
---

# Frontend Rules

## UI/UX Anti-AI-Slop
- NO generic gradients, NO default shadows, NO cookie-cutter layouts
- Every component needs a clear visual hierarchy and intentional whitespace
- Use brand colors from design tokens, not arbitrary hex values

## Spacing System (8px grid)
- xs: 4px, sm: 8px, md: 16px, lg: 24px, xl: 32px, 2xl: 48px
- Use Tailwind spacing utilities consistently (gap-2, p-4, etc.)

## Component Standards
- shadcn/ui components live in `src/shared/components/ui/`
- Custom components get their own folder: `ComponentName/index.tsx`
- Props interfaces named `{ComponentName}Props`
- Always handle loading, error, and empty states

## Responsive Design
- Mobile-first: base styles for mobile, then sm:, md:, lg:
- Breakpoints: sm(640px), md(768px), lg(1024px), xl(1280px)
- Test at 320px, 768px, 1024px, 1440px minimum

## Interactive States
- Every clickable element needs hover, focus, active, disabled states
- Focus rings for accessibility (focus-visible:ring-2)
- Transitions: 150ms for micro-interactions, 300ms for layout changes

## Accessibility
- Semantic HTML elements (nav, main, article, section)
- ARIA labels on interactive elements without visible text
- Color contrast ratio 4.5:1 minimum (WCAG AA)
- Keyboard navigable: all actions reachable via Tab + Enter/Space

# UI/UX Design Starter Prompts

## Quick Start Prompts

### Dashboard Generation
```
Usa ui-ux-pro-max-skill para generar un dashboard profesional para [INDUSTRIA/SECTOR].

Requisitos:
- Tipo: [Analytics / E-commerce / SaaS / Finance / Healthcare]
- Key metrics: [3-5 métricas principales]
- Color palette: [profesional, no purple gradients]
- Charts: [tipos de visualización necesarios]
- Layout: [sidebar navigation / top nav / hybrid]

Evita: Generic AI aesthetic (Inter font, purple gradients, rounded corners everywhere)
Incluye: Responsive design, dark mode support, accessibility (ARIA labels, keyboard nav)
```

### Component Creation (shadcn)
```
Necesito crear [COMPONENTE] usando shadcn/ui.

Especificaciones:
- Base component: [button / card / form / table / dialog]
- Variants: [primary, secondary, etc.]
- States: [default, hover, focus, disabled, loading]
- Size options: [sm, md, lg]
- Dark mode: yes
- Accessibility: WCAG AA compliance

Usa shadcn MCP para instalar base component, luego customiza según design system.
```

### Color Palette Selection
```
Genera color palette profesional para proyecto [TIPO DE PROYECTO].

Contexto:
- Industria: [fintech / healthcare / e-commerce / etc.]
- Brand personality: [professional, playful, minimal, bold]
- Target audience: [B2B / B2C / Enterprise]

Usa ui-ux-pro-max-skill (96 palettes disponibles).
Incluye:
- Primary, secondary, accent colors
- Semantic colors (success, warning, error, info)
- Neutral scale (50-950)
- Dark mode variants
- Accessibility contrast ratios
```

### Layout Pattern Selection
```
Necesito layout pattern para [TIPO DE APLICACIÓN].

Aplicación:
- Tipo: [Dashboard / Landing page / E-commerce / SaaS app]
- User flow: [describe main user journey]
- Key sections: [list main sections]
- Device priority: [mobile-first / desktop-first]

Usa ui-ux-pro-max-skill templates (10 dashboard patterns).
Evita: Cookie-cutter layouts, generic hero sections.
Incluye: Progressive disclosure, clear hierarchy, responsive grid.
```

## Advanced Prompts

### Complete Design System Generation
```
Genera design system completo para [NOMBRE DEL PROYECTO].

Proyecto:
- Descripción: [breve descripción]
- Tech stack: Next.js 16 + TypeScript + Tailwind CSS + shadcn/ui
- Target platforms: [web / mobile web / PWA]
- Brand guidelines: [si existen]

Genera:
1. Color palette (primary, secondary, neutrals, semantic)
2. Typography scale (headings, body, captions)
3. Spacing system (4px base scale)
4. Component variants (button, input, card, etc.)
5. Animation/transition standards
6. Grid system and breakpoints
7. Icon system (Lucide React recommended)

Output: tailwind.config.ts configuration + component examples
```

### Data Visualization Setup
```
Necesito implementar data visualization para [TIPO DE DATOS].

Datos:
- Tipo: [time series / categorical / hierarchical / geospatial]
- Volumen: [cantidad aproximada de data points]
- Update frequency: [real-time / periodic / static]
- Key insights: [qué insights debe revelar]

Charts necesarios: [usar ui-ux-pro-max-skill - 25 chart types]
- [line / bar / area / pie / scatter / heatmap / treemap / etc.]

Usa: Recharts library
Incluye: Responsive design, tooltips, legends, loading states, empty states
```

### Responsive Layout Refactor
```
Refactorizar layout para ser fully responsive.

Current state: [desktop-only / partially responsive]
Target breakpoints:
- Mobile: 375px, 414px
- Tablet: 768px, 1024px
- Desktop: 1280px, 1440px, 1920px

Approach:
1. Mobile-first CSS
2. CSS Grid para layouts complejos
3. Flexbox para component internals
4. Tailwind responsive classes
5. Test con Playwright MCP (capturas en cada breakpoint)

Evita: Hidden elements en mobile, horizontal scroll
```

### Accessibility Audit & Fix
```
Realizar accessibility audit completo del proyecto.

Checklist WCAG AA:
- [ ] Keyboard navigation (Tab, Enter, Escape, Arrow keys)
- [ ] ARIA labels en elementos interactivos
- [ ] Focus indicators visibles
- [ ] Color contrast ratio ≥ 4.5:1
- [ ] Alt text en imágenes
- [ ] Semantic HTML (header, nav, main, article, section, footer)
- [ ] Form labels y error messages
- [ ] Skip to main content link

Tools: Usa Chrome DevTools MCP para auditoría automatizada
Output: Lista de issues + code fixes
```

## Workflow Prompts

### Design → Code (Figma Integration)
```
Convertir diseño de Figma a código Next.js + Tailwind.

Figma file: [link o descripción]
Páginas/componentes: [listar]

Proceso:
1. Extraer design tokens (colors, typography, spacing)
2. Generar tailwind.config.ts
3. Crear components usando shadcn/ui base
4. Implementar responsive breakpoints
5. Aplicar animations y transitions
6. Testing visual con Playwright MCP

Evita: Hardcoded values, inline styles, generic AI aesthetic
```

### Visual Testing Loop
```
Implementar bucle de testing visual para [COMPONENTE/PÁGINA].

Bucle:
1. Implementar código
2. npm run dev
3. Capturar screenshots (Playwright MCP)
   - Desktop: 1440px
   - Tablet: 768px
   - Mobile: 375px
4. Comparar vs design requirements
5. Identificar discrepancias
6. Iterar hasta pixel-perfect

Criterios:
- Spacing correctos (design system tokens)
- Typography scale aplicado
- Colors match palette
- No AI slop aesthetic
- Dark mode funciona
```

## Quick Commands

### Install shadcn Component
```
Instala [componente] de shadcn/ui y customiza según design system.

Componente: [button / input / card / dialog / dropdown / etc.]
Customizations:
- Colors: usar palette del proyecto
- Spacing: seguir sistema de 4px
- Typography: aplicar scale definido
- States: hover, focus, disabled, loading
- Variants: [listar variants necesarios]
```

### Generate Dashboard from Template
```
Genera dashboard completo usando template de ui-ux-pro-max-skill.

Template: [Analytics / E-commerce / SaaS / Finance / Healthcare]
Customizations:
- Brand colors: [palette]
- Key metrics: [listar KPIs]
- Charts: [tipos necesarios]
- Navigation: [sidebar / topnav]
- Features: [filters, search, export, notifications]

Output: Complete dashboard con routing, components, mock data
```

## Anti-Patterns to Avoid

### ❌ Generic Prompt (Don't Use)
```
"Create a beautiful dashboard with charts"
```
**Problem:** Too vague, will generate generic AI aesthetic

### ✅ Specific Prompt (Use This)
```
"Usa ui-ux-pro-max-skill para generar dashboard de analytics SaaS.
Color palette: profesional (no purple), neutros oscuros + accent teal.
Charts: line chart (revenue trend), bar chart (users by plan), pie chart (traffic sources).
Layout: sidebar navigation, grid de 3 columnas, dark mode support.
Evita: Inter font, glass morphism, generic gradients."
```
**Better:** Specific requirements, references skill, anti-patterns listed

---

## Tips for Effective Prompts

1. **Be Specific**: Menciona industria, target audience, brand personality
2. **Reference Skills**: "Usa ui-ux-pro-max-skill" activa conocimiento especializado
3. **List Anti-Patterns**: "Evita X" previene generic AI aesthetic
4. **Include Context**: Tech stack, existing design system, constraints
5. **Request Accessibility**: Siempre incluir WCAG compliance
6. **Specify Responsive**: Listar breakpoints target
7. **Use MCPs**: shadcn MCP, Playwright MCP, Chrome DevTools MCP
8. **Iterate Visually**: Test → Screenshot → Compare → Iterate

---

**Recuerda:** El objetivo es generar UI/UX profesional que NO parezca hecho por AI.
Usa ui-ux-pro-max-skill, shadcn MCP, y visual testing con Playwright MCP.

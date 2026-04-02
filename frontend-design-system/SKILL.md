---
name: frontend-design-system
description: >-
  Generate production-grade UI components with visual style systems, color
  palettes, typography, and responsive layouts. Use when the user asks to
  build UI, create a landing page, design a component, generate a hero section,
  or wants frontend design help with styling and layout.
version: "1.0.0"
tools:
  - Read
  - Write
  - Bash
---

## Frontend Design System

Generate cohesive, production-grade frontend code with matched design tokens, accessible components, and responsive layouts.

## Workflow

1. **Understand the request**
   Read the user's UI description. Identify:
   - Page type: landing page, dashboard, form, card grid, documentation, blog, SaaS app
   - Visual mood: professional, playful, bold, calm, luxury, technical
   - Framework: React (default), Vue, Svelte, or vanilla HTML/CSS
   - CSS approach: Tailwind (default), CSS Modules, or plain CSS

2. **Select a visual style mode**
   Based on the mood, pick one:

   | Mode             | Character                                       |
   |------------------|------------------------------------------------|
   | Minimalist       | White space, thin lines, muted palette           |
   | Brutalist        | Raw, bold borders, monospace, high contrast      |
   | Glassmorphism    | Blur backgrounds, transparency, gradients        |
   | Neumorphic       | Soft shadows, light/dark depth, rounded          |
   | Dark Corporate   | Deep navy/charcoal, accent highlights            |
   | Soft Gradient    | Pastel gradients, rounded corners, airy          |
   | Neobrutalist     | Thick borders, offset shadows, bright colors     |
   | Clean SaaS       | Light bg, blue/indigo accents, tight spacing     |
   | Editorial        | Serif headings, generous whitespace, type-driven |
   | Cyberpunk        | Neon accents, dark bg, glitch effects            |
   | Nature/Organic   | Earth tones, soft edges, natural textures        |
   | Playful          | Bright colors, bouncy shapes, fun typography     |

3. **Generate the color palette**
   Create a semantic palette with 6-8 tokens:
   ```css
   :root {
     --color-primary: /* main brand color */;
     --color-primary-hover: /* darker variant */;
     --color-secondary: /* complementary accent */;
     --color-background: /* page background */;
     --color-surface: /* card/panel background */;
     --color-text: /* primary text */;
     --color-text-muted: /* secondary text */;
     --color-border: /* dividers, outlines */;
     --color-success: /* #22c55e or variant */;
     --color-warning: /* #f59e0b or variant */;
     --color-error: /* #ef4444 or variant */;
   }
   ```

4. **Select typography pairing**
   Pick from these proven pairings:
   - **Inter + JetBrains Mono** — Modern SaaS, clean and readable
   - **Outfit + Space Mono** — Tech-forward, slightly geometric
   - **DM Sans + DM Serif Display** — Editorial, warm contrast
   - **Plus Jakarta Sans + Instrument Serif** — Luxury editorial
   - **Space Grotesk + IBM Plex Mono** — Brutalist, monospace accent
   - **Nunito + Fira Code** — Friendly, rounded, developer-oriented

5. **Generate the component(s)**
   Build complete, responsive components:
   - Use semantic HTML (section, nav, article, aside, header, footer)
   - Include ARIA labels and roles for accessibility
   - Support dark mode via `prefers-color-scheme` or class toggle
   - Use CSS Grid/Flexbox for layout (never floats)
   - Mobile-first responsive breakpoints: 640px, 768px, 1024px, 1280px
   - Include hover, focus, and active states for all interactive elements

6. **Add design tokens as CSS variables**
   Output all tokens at the top of the CSS so they can be easily customized:
   ```css
   :root {
     --radius-sm: 0.375rem;
     --radius-md: 0.5rem;
     --radius-lg: 0.75rem;
     --radius-full: 9999px;
     --shadow-sm: 0 1px 2px rgb(0 0 0 / 0.05);
     --shadow-md: 0 4px 6px rgb(0 0 0 / 0.07);
     --shadow-lg: 0 10px 15px rgb(0 0 0 / 0.1);
     --spacing-xs: 0.25rem;
     --spacing-sm: 0.5rem;
     --spacing-md: 1rem;
     --spacing-lg: 1.5rem;
     --spacing-xl: 2rem;
     --spacing-2xl: 3rem;
     --transition-fast: 150ms ease;
     --transition-normal: 250ms ease;
   }
   ```

## Rules

- Never use inline styles — always use CSS classes or styled components
- Every color must be a CSS variable so themes can be swapped
- Always include dark mode support
- All interactive elements must have visible focus indicators
- Font sizes must use rem units, not px
- Spacing must use the spacing scale, not arbitrary pixel values
- Generate complete, runnable code — no placeholders like "add your styles here"
- Include a brief design rationale comment at the top explaining the chosen style
- If the user's framework is unspecified, default to React + Tailwind CSS

## Example Output Structure

```css
/* Design System: Clean SaaS style
   Palette: Indigo primary, slate neutrals
   Typography: Inter + JetBrains Mono */

:root {
  --color-primary: #4f46e5;
  /* ... full token set ... */
}

/* Component styles follow... */
```

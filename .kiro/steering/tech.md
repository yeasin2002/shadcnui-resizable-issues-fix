# Tech Stack

## Core Framework
- Next.js 16 (App Router)
- React 19
- TypeScript 5

## Styling
- Tailwind CSS 4 (with `@tailwindcss/postcss`)
- tw-animate-css for animations
- CSS variables for theming (light/dark mode support)
- OKLCH color space for color definitions

## UI Components
- shadcn/ui (new-york style, RSC enabled)
- lucide-react for icons
- react-resizable-panels for resizable layouts
- class-variance-authority + clsx + tailwind-merge for className utilities

## Package Manager
- pnpm

## Common Commands

```bash
# Development server
pnpm dev

# Production build
pnpm build

# Start production server
pnpm start

# Lint
pnpm lint
```

## Path Aliases
- `@/*` maps to project root (e.g., `@/components`, `@/lib`)

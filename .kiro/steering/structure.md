# Project Structure

```
├── app/                    # Next.js App Router
│   ├── layout.tsx          # Root layout with fonts and metadata
│   ├── page.tsx            # Home page (entry point)
│   ├── globals.css         # Global styles, Tailwind config, CSS variables
│   └── favicon.ico
├── components/
│   └── ui/                 # shadcn/ui components
│       └── resizable.tsx   # Resizable panel components
├── lib/
│   └── utils.ts            # Utility functions (cn helper)
├── public/                 # Static assets
├── components.json         # shadcn/ui configuration
├── next.config.ts          # Next.js configuration
├── tsconfig.json           # TypeScript configuration
└── postcss.config.mjs      # PostCSS configuration
```

## Conventions

- UI components go in `components/ui/` (shadcn/ui pattern)
- Shared utilities go in `lib/`
- Use `"use client"` directive for client components
- Use `cn()` helper from `@/lib/utils` for conditional classNames
- Components use `data-slot` attributes for styling hooks
- Follow shadcn/ui component patterns: wrap primitives, expose via named exports

# Resizable Panels Demo

A Next.js demo application showcasing resizable panel layouts using [react-resizable-panels](https://github.com/bvaughn/react-resizable-panels) with [shadcn/ui](https://ui.shadcn.com/) components.

## Tech Stack

- Next.js 16 (App Router)
- React 19
- TypeScript 5
- Tailwind CSS 4
- react-resizable-panels v4
- shadcn/ui components

## Getting Started

```bash
# Install dependencies
pnpm install

# Run development server
pnpm dev
```

Open [http://localhost:3000](http://localhost:3000) to see the demo.

## Project Structure

```
├── app/
│   ├── page.tsx           # Home page
│   ├── ResizableDemo.tsx  # Demo component
│   └── globals.css        # Global styles
├── components/ui/
│   └── resizable.tsx      # shadcn/ui resizable components
├── documents/             # Reference documentation
│   ├── resizable-panels-usage-guide.md
│   ├── imperative-panel-api-guide.md
│   └── react-resizable-panels-v4-migration.md
└── lib/
    └── utils.ts           # Utility functions
```

## Features

- Horizontal and vertical resizable panel groups
- Nested panel layouts
- Draggable resize handles
- shadcn/ui styled components

## v4 Migration Notes

This project uses `react-resizable-panels` v4 with updated APIs:

| v3 | v4 |
|---|---|
| `direction` prop | `orientation` prop |
| `data-[panel-group-direction=...]` | `aria-[orientation=...]` |
| `ImperativePanelHandle` type | `usePanelRef()` hook |
| `<Panel ref={...}>` | `<Panel panelRef={...}>` |

See [fix-changelog.md](https://github.com/bvaughn/react-resizable-panels/blob/main/CHANGELOG.md) for full migration details.

## Documentation

Link: https://react-resizable-panels.vercel.app/ 

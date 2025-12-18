# react-resizable-panels v4 Migration Guide

## Overview

`react-resizable-panels` v4.0.0 introduced breaking changes to better align with web standards like WAI-ARIA and CSS. This document explains the changes and how to migrate your shadcn/ui Resizable component.

## Breaking Changes Summary

| v3 (Old)            | v4 (New)      | Reason                                   |
| ------------------- | ------------- | ---------------------------------------- |
| `PanelGroup`        | `Group`       | Simplified naming                        |
| `PanelResizeHandle` | `Separator`   | Aligns with ARIA "separator" role        |
| `direction` prop    | `orientation` | Aligns with ARIA `orientation` attribute |
| `onCollapse`        | Removed       | Use `onResize` instead                   |
| `onExpand`          | Removed       | Use `onResize` instead                   |
| `autoSaveId`        | Removed       | Use `useDefaultLayout` hook instead      |

## Import Changes

```tsx
// ❌ Old (v2/v3)
import { PanelGroup, Panel, PanelResizeHandle } from "react-resizable-panels";

// ✅ New (v4)
import { Group, Panel, Separator } from "react-resizable-panels";
```

## Prop Changes

```tsx
// ❌ Old (v2/v3)
<PanelGroup direction="horizontal">
  <Panel />
  <PanelResizeHandle />
  <Panel />
</PanelGroup>

// ✅ New (v4)
<Group orientation="horizontal">
  <Panel />
  <Separator />
  <Panel />
</Group>
```

## Data Attribute Changes

In v4, the data attributes have changed significantly:

| v3 (Old)                              | v4 (New)                       |
| ------------------------------------- | ------------------------------ |
| `data-panel-group-direction`          | `aria-orientation`             |
| `data-panel-group-direction=vertical` | `aria-orientation=horizontal`* |

**Important:** The Separator's `aria-orientation` is the **opposite** of the Group's orientation. A horizontal group has vertical separators, and a vertical group has horizontal separators.

## Updated shadcn/ui Resizable Component

Here's the corrected `components/ui/resizable.tsx` for v4:

```tsx
"use client"

import { GripVerticalIcon } from "lucide-react";
import * as React from "react";
import { Group, Panel, Separator } from "react-resizable-panels";

import { cn } from "@/lib/utils";

function ResizablePanelGroup({
  className,
  ...props
}: React.ComponentProps<typeof Group>) {
  return (
    <Group
      data-slot="resizable-panel-group"
      className={cn("flex h-full w-full", className)}
      {...props}
    />
  );
}

function ResizablePanel({ ...props }: React.ComponentProps<typeof Panel>) {
  return <Panel data-slot="resizable-panel" {...props} />;
}

function ResizableHandle({
  withHandle,
  className,
  ...props
}: React.ComponentProps<typeof Separator> & {
  withHandle?: boolean;
}) {
  return (
    <Separator
      data-slot="resizable-handle"
      className={cn(
        "bg-border focus-visible:ring-ring relative flex w-px items-center justify-center after:absolute after:inset-y-0 after:left-1/2 after:w-1 after:-translate-x-1/2 focus-visible:ring-1 focus-visible:ring-offset-1 focus-visible:outline-hidden aria-[orientation=horizontal]:h-px aria-[orientation=horizontal]:w-full aria-[orientation=horizontal]:after:left-0 aria-[orientation=horizontal]:after:h-1 aria-[orientation=horizontal]:after:w-full aria-[orientation=horizontal]:after:translate-x-0 aria-[orientation=horizontal]:after:-translate-y-1/2 [&[aria-orientation=horizontal]>div]:rotate-90",
        className
      )}
      {...props}
    >
      {withHandle && (
        <div className="bg-border z-10 flex h-4 w-3 items-center justify-center rounded-xs border">
          <GripVerticalIcon className="size-2.5" />
        </div>
      )}
    </Separator>
  );
}

export { ResizableHandle, ResizablePanel, ResizablePanelGroup };
```

## Usage Example (Updated)

```tsx
import {
  ResizableHandle,
  ResizablePanel,
  ResizablePanelGroup,
} from "@/components/ui/resizable";

export default function ResizableDemo() {
  return (
    <ResizablePanelGroup
      orientation="horizontal" // Changed from direction="horizontal"
      className="max-w-md rounded-lg border md:min-w-[450px]"
    >
      <ResizablePanel defaultSize={50}>
        <div className="flex h-[200px] items-center justify-center p-6">
          <span className="font-semibold">One</span>
        </div>
      </ResizablePanel>
      <ResizableHandle />
      <ResizablePanel defaultSize={50}>
        <ResizablePanelGroup orientation="vertical">
          {" "}
          {/* Changed */}
          <ResizablePanel defaultSize={25}>
            <div className="flex h-full items-center justify-center p-6">
              <span className="font-semibold">Two</span>
            </div>
          </ResizablePanel>
          <ResizableHandle />
          <ResizablePanel defaultSize={75}>
            <div className="flex h-full items-center justify-center p-6">
              <span className="font-semibold">Three</span>
            </div>
          </ResizablePanel>
        </ResizablePanelGroup>
      </ResizablePanel>
    </ResizablePanelGroup>
  );
}
```

## Persistent Layouts (New Approach)

If you were using `autoSaveId` for persistent layouts:

```tsx
// ❌ Old (v2/v3)
<PanelGroup autoSaveId="unique-group-id" direction="horizontal">
  ...
</PanelGroup>;

// ✅ New (v4)
import {
  Group,
  Panel,
  Separator,
  useDefaultLayout,
} from "react-resizable-panels";

const { defaultLayout, onLayoutChange } = useDefaultLayout({
  groupId: "unique-group-id",
  storage: localStorage,
});

<Group defaultLayout={defaultLayout} onLayoutChange={onLayoutChange}>
  ...
</Group>;
```

## Imperative API Changes

```tsx
// ❌ Old (v2/v3)
import type {
  ImperativePanelGroupHandle,
  ImperativePanelHandle,
} from "react-resizable-panels";
const panelRef = useRef<ImperativePanelHandle>(null);
<Panel ref={panelRef}>...</Panel>;

// ✅ New (v4)
import { usePanelRef, useGroupRef } from "react-resizable-panels";
const panelRef = usePanelRef();
<Panel panelRef={panelRef}>...</Panel>;
```

## onLayoutChange with State

In v4, `onLayoutChange` receives a `Layout` object (map of Panel id to flexGrow value) instead of an array of sizes.

### Layout Type

```ts
// v4 Layout type
type Layout = {
  [panelId: string]: number; // Panel id → flexGrow value (0-100)
};
```

### Basic Usage with State

```tsx
"use client";

import { useState } from "react";
import { Group, Panel, Separator } from "react-resizable-panels";
import type { Layout } from "react-resizable-panels";

export function ResizableWithState() {
  const [layout, setLayout] = useState<Layout>({
    left: 30,
    right: 70,
  });

  const handleLayoutChange = (newLayout: Layout) => {
    setLayout(newLayout);
    console.log("Layout changed:", newLayout);
    // Example output: { left: 25, right: 75 }
  };

  return (
    <Group
      orientation="horizontal"
      defaultLayout={layout}
      onLayoutChange={handleLayoutChange}
    >
      <Panel id="left" defaultSize={30}>
        <div>Left Panel ({layout.left?.toFixed(1)}%)</div>
      </Panel>
      <Separator />
      <Panel id="right" defaultSize={70}>
        <div>Right Panel ({layout.right?.toFixed(1)}%)</div>
      </Panel>
    </Group>
  );
}
```

### Key Differences from v3

| v3 (Old)                                        | v4 (New)                                    |
| ----------------------------------------------- | ------------------------------------------- |
| `onLayout={(sizes) => ...}`                     | `onLayoutChange={(layout) => ...}`          |
| Receives `number[]` (array of sizes)            | Receives `Layout` (object with panel ids)   |
| Sizes accessed by index: `sizes[0]`, `sizes[1]` | Sizes accessed by id: `layout.left`         |
| Panel order matters                             | Panel ids matter (order-independent)        |

### With shadcn/ui Wrapper

```tsx
"use client";

import { useState } from "react";
import {
  ResizableHandle,
  ResizablePanel,
  ResizablePanelGroup,
} from "@/components/ui/resizable";

type Layout = { [id: string]: number };

export function ResizableDemo() {
  const [layout, setLayout] = useState<Layout>({
    sidebar: 25,
    content: 75,
  });

  return (
    <ResizablePanelGroup
      orientation="horizontal"
      defaultLayout={layout}
      onLayoutChange={setLayout}
    >
      <ResizablePanel id="sidebar" defaultSize={25} minSize={15}>
        <div className="p-4">
          Sidebar: {layout.sidebar?.toFixed(1)}%
        </div>
      </ResizablePanel>
      <ResizableHandle />
      <ResizablePanel id="content" defaultSize={75}>
        <div className="p-4">
          Content: {layout.content?.toFixed(1)}%
        </div>
      </ResizablePanel>
    </ResizablePanelGroup>
  );
}
```

### Imperative Layout Control

```tsx
import { useGroupRef } from "react-resizable-panels";

function MyComponent() {
  const groupRef = useGroupRef();

  const resetLayout = () => {
    // Set layout programmatically
    groupRef.current?.setLayout({ left: 50, right: 50 });
  };

  const logCurrentLayout = () => {
    // Get current layout
    const currentLayout = groupRef.current?.getLayout();
    console.log(currentLayout); // { left: 30, right: 70 }
  };

  return (
    <>
      <button onClick={resetLayout}>Reset</button>
      <Group groupRef={groupRef} orientation="horizontal">
        <Panel id="left">Left</Panel>
        <Separator />
        <Panel id="right">Right</Panel>
      </Group>
    </>
  );
}
```

## References

- [react-resizable-panels GitHub](https://github.com/bvaughn/react-resizable-panels)
- [Official Changelog](https://github.com/bvaughn/react-resizable-panels/blob/main/CHANGELOG.md)
- [shadcn/ui Issue #9118](https://github.com/shadcn-ui/ui/issues/9118)

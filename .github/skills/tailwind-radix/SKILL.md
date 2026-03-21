---
name: tailwind-radix
description: "Use when: setting up TailwindCSS v4 with CSS @theme tokens, building accessible UI components with Radix UI, composing variants with CVA, or implementing the dark glassmorphism design aesthetic."
---

# TailwindCSS v4 + Radix UI

> Used by: @app-builder, @job-ops

## TailwindCSS v4 Key Changes from v3

| v3 | v4 |
|----|-----|
| `tailwind.config.ts` | CSS `@theme {}` block |
| `@tailwind base/components/utilities` | `@import "tailwindcss"` |
| `theme()` function | CSS custom properties (`var(--color-primary)`) |
| `content` array | Auto-detected from import graph |

## CSS Setup

```css
/* app/globals.css */
@import "tailwindcss";

@theme {
  /* OKLCh colors for perceptual consistency */
  --color-primary:    oklch(65% 0.20 220);
  --color-primary-fg: oklch(98% 0.01 220);
  --color-surface:    oklch(10% 0.01 240);    /* near-black */
  --color-surface-2:  oklch(15% 0.01 240);
  --color-border:     oklch(30% 0.02 240);
  --color-muted:      oklch(55% 0.02 240);
  --color-accent:     oklch(75% 0.18 195);    /* cyan #00d4ff equivalent */

  --font-sans: 'Inter', system-ui, sans-serif;
  --font-mono: 'JetBrains Mono', monospace;

  --radius-sm:   0.375rem;
  --radius-md:   0.5rem;
  --radius-lg:   0.75rem;
  --radius-card: 0.75rem;
}
```

## CVA Component Composition

```typescript
// lib/utils.ts
import { type ClassValue, clsx } from 'clsx';
import { twMerge } from 'tailwind-merge';
export function cn(...inputs: ClassValue[]) { return twMerge(clsx(inputs)); }
```

```typescript
// components/ui/Button.tsx
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-[--color-primary] text-[--color-primary-fg] hover:opacity-90',
        outline: 'border border-[--color-border] bg-transparent hover:bg-[--color-surface-2]',
        ghost:   'hover:bg-[--color-surface-2]',
      },
      size: {
        sm: 'h-8 px-3 text-xs',
        md: 'h-9 px-4',
        lg: 'h-11 px-6 text-base',
        icon: 'h-9 w-9',
      },
    },
    defaultVariants: { variant: 'default', size: 'md' },
  }
);

interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return <button className={cn(buttonVariants({ variant, size }), className)} {...props} />;
}
```

## Radix UI Patterns

### Dialog
```typescript
import * as Dialog from '@radix-ui/react-dialog';

function ConfirmDialog({ open, onOpenChange, onConfirm }: Props) {
  return (
    <Dialog.Root open={open} onOpenChange={onOpenChange}>
      <Dialog.Portal>
        <Dialog.Overlay className="fixed inset-0 bg-black/50 backdrop-blur-sm" />
        <Dialog.Content className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-[--color-surface-2] border border-[--color-border] rounded-lg p-6 w-full max-w-md">
          <Dialog.Title className="text-lg font-semibold">Confirm Action</Dialog.Title>
          <Dialog.Description className="mt-2 text-[--color-muted]">
            This cannot be undone.
          </Dialog.Description>
          <div className="mt-4 flex gap-2 justify-end">
            <Dialog.Close asChild>
              <Button variant="outline">Cancel</Button>
            </Dialog.Close>
            <Button onClick={onConfirm}>Confirm</Button>
          </div>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

### Glassmorphism Card (Kyle's aesthetic)
```tsx
function GlassCard({ children, className }: { children: React.ReactNode; className?: string }) {
  return (
    <div
      className={cn(
        'rounded-[--radius-card] border border-[--color-border] p-4',
        'bg-white/5 backdrop-blur-[10px]',   // glassmorphism
        className
      )}
    >
      {children}
    </div>
  );
}
```

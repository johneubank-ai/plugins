# Tier 1: Leaf Primitive Template

Use this template for domain-agnostic utility components that compose Tier 0 primitives.

---

## Template

```markdown
# ComponentName

## tier
1 - Leaf Primitive

## purpose
[One sentence describing what this component does]

## where-does-this-appear
- [ParentComponent]: [Placement description]

## composed-of
[List the Tier 0 components this component uses]
- Button from `@/components/button`
- Badge from `@/components/badge`

## variants
[List visual variants]
- default: Base appearance
- [variant-name]: [Description]

## props
```typescript
interface ComponentNameProps {
  // Generic, reusable props - no domain types
  title?: string
  description?: string
  icon?: React.ComponentType<{ className?: string }>
  onAction?: () => void
  disabled?: boolean
  className?: string
}
```

## states
- default: Normal appearance
- hover: Visual feedback on hover
- focus: Focus ring styling
- active: Pressed/selected state
- disabled: Grayed out, non-interactive
- loading: Skeleton or spinner (if applicable)

## size
- width: [Tailwind default class]
- height: [Tailwind default class]
- padding: [Tailwind default class]

## accessibility
- role: [ARIA role if needed]
- aria-label: [Descriptive label pattern]
- keyboard: [Keyboard interactions]
- focus: [Focus management approach]

## storybook-tags
- autodocs
- tier-1
- leaf-primitive
```

---

## Example: ConfirmDialog Component

```markdown
# ConfirmDialog

## tier
1 - Leaf Primitive

## purpose
Display a modal dialog asking the user to confirm or cancel an action.

## where-does-this-appear
- DeleteButton: Confirmation before delete
- FormSubmit: Confirmation before submitting destructive changes
- Navigation: Unsaved changes warning

## composed-of
- Dialog from `@/components/dialog`
- Button from `@/components/button`

## variants
- default: Standard confirmation
- destructive: Red-themed for dangerous actions
- warning: Amber-themed for cautionary actions

## props
```typescript
interface ConfirmDialogProps {
  open: boolean
  onClose: () => void
  onConfirm: () => void
  title: string
  description?: string
  confirmLabel?: string
  cancelLabel?: string
  variant?: 'default' | 'destructive' | 'warning'
  loading?: boolean
}
```

## states
- closed: Dialog not visible
- open: Dialog visible
- loading: Confirm button shows spinner
- disabled: Buttons non-interactive

## size
- width: max-w-md
- height: h-auto
- padding: p-6

## accessibility
- role: alertdialog
- aria-labelledby: Dialog title
- aria-describedby: Dialog description
- keyboard: Escape to close, Tab to navigate, Enter to confirm
- focus: Trapped within dialog, returns to trigger on close

## storybook-tags
- autodocs
- tier-1
- leaf-primitive
- dialog
- confirmation
```

---

## Allowed Dependencies

```typescript
// Tier 0 components
import { Button } from '@/components/button'
import { Dialog, DialogTitle, DialogBody } from '@/components/dialog'
import { Badge } from '@/components/badge'

// Icons
import { ExclamationTriangleIcon } from '@heroicons/react/16/solid'

// Utilities
import clsx from 'clsx'

// React hooks for local UI state only
import { useState } from 'react'
```

## Forbidden

- Domain interfaces (`@/lib/interfaces/*`)
- Server actions (`@/lib/actions/*`)
- Data fetching
- Context providers (except theme/UI)
- `useEffect` for data operations

---

## Storybook Pattern

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { fn } from 'storybook/test'
import { ConfirmDialog } from './confirm-dialog'

const meta = {
  title: 'Primitives/ConfirmDialog',
  component: ConfirmDialog,
  parameters: { layout: 'centered' },
  tags: ['autodocs', 'tier-1', 'leaf-primitive'],
  args: {
    onClose: fn(),
    onConfirm: fn(),
  },
} satisfies Meta<typeof ConfirmDialog>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  args: {
    open: true,
    title: 'Confirm Action',
    description: 'Are you sure you want to proceed?',
  },
}

export const Destructive: Story = {
  args: {
    open: true,
    title: 'Delete Item',
    description: 'This action cannot be undone.',
    variant: 'destructive',
    confirmLabel: 'Delete',
  },
}

export const Loading: Story = {
  args: {
    open: true,
    title: 'Processing',
    description: 'Please wait...',
    loading: true,
  },
}
```

---

## Checklist

- [ ] Component composes Tier 0 primitives
- [ ] No domain-specific types imported
- [ ] No server actions or data fetching
- [ ] Props use generic types (string, number, boolean, callbacks)
- [ ] Can be reused across any domain
- [ ] All states documented
- [ ] Accessibility requirements defined
- [ ] Storybook story uses loaders with simple mock data

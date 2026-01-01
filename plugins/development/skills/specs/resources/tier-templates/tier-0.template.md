# Tier 0: Library Primitive Template

Use this template for Catalyst UI wrapper components that have no business logic.

---

## Template

```markdown
# ComponentName

## tier
0 - Library Primitive

## purpose
[One sentence describing what this component does]

## where-does-this-appear
- [ParentComponent]: [Placement description]

## catalyst-source
[Reference to the Catalyst UI component being wrapped]
- Source: `@headlessui/react` or `@/components/[catalyst-component]`
- Documentation: `docs/catalyst-ui/components/[component].md`

## variants
[List visual variants if applicable]
- default: Base appearance
- [variant-name]: [Description]

## props
```typescript
interface ComponentNameProps {
  // Visual props only - no business logic
  variant?: 'default' | 'other'
  size?: 'sm' | 'md' | 'lg'
  disabled?: boolean
  className?: string
  children?: React.ReactNode
}
```

## states
- default: Normal appearance
- hover: Visual feedback on hover
- focus: Focus ring styling
- active: Pressed state
- disabled: Grayed out, non-interactive

## size
- width: [Tailwind default class, e.g., w-full, w-fit]
- height: [Tailwind default class, e.g., h-auto, h-10]
- padding: [Tailwind default class, e.g., p-2, px-4 py-2]

## accessibility
- role: [ARIA role if not implicit]
- aria-label: [Descriptive label pattern]
- keyboard: [Keyboard interactions]
- focus: [Focus management approach]

## storybook-tags
- autodocs
- tier-0
- library-primitive
```

---

## Example: Badge Component

```markdown
# Badge

## tier
0 - Library Primitive

## purpose
Display a small status indicator or label with various color schemes.

## where-does-this-appear
- TableCell: Status indicators
- Card headers: Category labels
- Lists: Item tags

## catalyst-source
- Source: `@/components/badge`
- Documentation: `docs/catalyst-ui/components/badge.md`

## variants
- default: Gray background
- success: Green background
- warning: Amber background
- error: Red background
- info: Blue background

## props
```typescript
interface BadgeProps {
  color?: 'zinc' | 'green' | 'amber' | 'red' | 'blue'
  children: React.ReactNode
  className?: string
}
```

## states
- default: Normal appearance with subtle background

## size
- width: w-fit
- height: h-auto
- padding: px-2 py-0.5

## accessibility
- role: status (when indicating status)
- aria-label: Badge content should be descriptive

## storybook-tags
- autodocs
- tier-0
- library-primitive
- badge
```

---

## Allowed Dependencies

```typescript
// ONLY these imports are allowed in Tier 0 components
import { ComponentName } from '@headlessui/react'
import clsx from 'clsx'

// Internal Tier 0 primitives
import { OtherPrimitive } from '@/components/other-primitive'
```

## Forbidden

- Server actions (`@/lib/actions/*`)
- Domain types (`@/lib/interfaces/*`)
- Data fetching
- Context providers
- `useEffect` for side effects
- Business logic of any kind

---

## Storybook Pattern

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { Badge } from './badge'

const meta = {
  title: 'Primitives/Badge',
  component: Badge,
  parameters: { layout: 'centered' },
  tags: ['autodocs', 'tier-0', 'library-primitive'],
  argTypes: {
    color: {
      control: 'select',
      options: ['zinc', 'green', 'amber', 'red', 'blue'],
    },
  },
} satisfies Meta<typeof Badge>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  args: {
    children: 'Badge',
  },
}

export const Success: Story = {
  args: {
    color: 'green',
    children: 'Success',
  },
}

export const AllVariants: Story = {
  render: () => (
    <div className="flex gap-2">
      <Badge>Default</Badge>
      <Badge color="green">Success</Badge>
      <Badge color="amber">Warning</Badge>
      <Badge color="red">Error</Badge>
      <Badge color="blue">Info</Badge>
    </div>
  ),
}
```

---

## Checklist

- [ ] Component wraps a Catalyst UI primitive
- [ ] No domain-specific types imported
- [ ] No server actions or data fetching
- [ ] Props are purely presentational
- [ ] All states documented
- [ ] Accessibility requirements defined
- [ ] Storybook story uses loaders (if needed)

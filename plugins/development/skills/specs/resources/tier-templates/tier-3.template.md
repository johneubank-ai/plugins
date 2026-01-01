# Tier 3: Simple Composite Template

Use this template for components that compose 2+ lower-tier components with local UI state and event callbacks.

---

## Template

```markdown
# ComponentName

## tier
3 - Simple Composite

## purpose
[One sentence describing what this component does]

## where-does-this-appear
- [ParentComponent]: [Placement description]

## domain
[Primary domain this component belongs to]
- horse | client | therapist | session | schedule | etc.

## composed-of
[List the Tier 0-2 components this component uses]
- HorseAvatar from `@/components/horse/horse-avatar`
- Badge from `@/components/badge`
- Button from `@/components/button`

## domain-types
[List domain interfaces used]
```typescript
import type { HorseInterface } from '@/lib/interfaces/horse'
```

## data-displayed
[Use $Model.property notation]
- `$Horse.name` - Display name
- `$Horse.status` - Status badge
- `$Horse.breed` - Secondary info

## callbacks
[Document all event callbacks]
- `onEdit(id: string)` - Called when edit action triggered
- `onDelete(id: string)` - Called when delete action triggered
- `onSelect(horse: HorseInterface)` - Called when row selected

## local-ui-state
[Document internal UI state]
- `isExpanded: boolean` - Controls detail section visibility
- `isHovered: boolean` - Tracks hover for action buttons
- `isSelected: boolean` - Selection state for multi-select

## props
```typescript
interface ComponentNameProps {
  // Domain data
  horse: HorseInterface

  // Selection state (controlled)
  selected?: boolean

  // Event callbacks
  onEdit?: (id: string) => void
  onDelete?: (id: string) => void
  onSelect?: (horse: HorseInterface) => void

  // Display options
  showActions?: boolean
  className?: string
}
```

## states
- default: Normal display
- hover: Shows action buttons
- selected: Highlighted row
- expanded: Shows additional details
- loading: Skeleton placeholder

## size
- width: [Tailwind default class]
- height: [Tailwind default class]
- padding: [Tailwind default class]

## accessibility
- role: [ARIA role, e.g., row, listitem]
- aria-selected: [For selectable items]
- keyboard: [Keyboard interactions]
- focus: [Focus management]

## storybook-tags
- autodocs
- tier-3
- simple-composite
- [domain-name]
```

---

## Example: HorseRow Component

```markdown
# HorseRow

## tier
3 - Simple Composite

## purpose
Display a horse as a table row with avatar, info, status, and action buttons.

## where-does-this-appear
- HorsesTable: Main table body rows
- HorseSelect: Expanded selection list

## domain
horse

## composed-of
- HorseAvatar from `@/components/horse/horse-avatar`
- Badge from `@/components/badge`
- Button from `@/components/button`
- Dropdown from `@/components/dropdown`

## domain-types
```typescript
import type { HorseInterface } from '@/lib/interfaces/horse'
```

## data-displayed
- `$Horse.name` - Primary display text
- `$Horse.avatarUrl` - Avatar image
- `$Horse.status` - Status badge (active/inactive/retired)
- `$Horse.breed` - Secondary text
- `$Horse.lastSessionDate` - Formatted date

## callbacks
- `onEdit(id: number)` - Opens horse edit drawer
- `onDelete(id: number)` - Triggers delete confirmation
- `onSelect(horse: HorseInterface)` - Row click handler
- `onViewProfile(id: number)` - Navigate to horse profile

## local-ui-state
- `isHovered: boolean` - Shows/hides action buttons
- `isMenuOpen: boolean` - Dropdown menu state
- `isExpanded: boolean` - Detail row expansion

## props
```typescript
interface HorseRowProps {
  horse: HorseInterface
  selected?: boolean
  onEdit?: (id: number) => void
  onDelete?: (id: number) => void
  onSelect?: (horse: HorseInterface) => void
  onViewProfile?: (id: number) => void
  showActions?: boolean
  className?: string
}
```

## states
- default: Normal row appearance
- hover: Action buttons visible
- selected: Row highlighted with checkbox
- expanded: Shows additional horse details

## size
- width: w-full
- height: h-14 (default), h-auto (expanded)
- padding: px-4 py-3

## accessibility
- role: row
- aria-selected: true when selected
- keyboard: Enter to select, Space to toggle, Tab to actions
- focus: Visible focus ring on row

## storybook-tags
- autodocs
- tier-3
- simple-composite
- horse
- table
```

---

## Allowed Dependencies

```typescript
// Tier 0-2 components
import { Button } from '@/components/button'
import { Badge } from '@/components/badge'
import { Dropdown, DropdownButton, DropdownMenu, DropdownItem } from '@/components/dropdown'
import { HorseAvatar } from '@/components/horse/horse-avatar'

// Domain interfaces (types only)
import type { HorseInterface } from '@/lib/interfaces/horse'

// Icons
import { PencilIcon, TrashIcon, EllipsisVerticalIcon } from '@heroicons/react/16/solid'

// React hooks for LOCAL state only
import { useState, useCallback } from 'react'

// Utilities
import clsx from 'clsx'
```

## Forbidden

- Server actions (`@/lib/actions/*`)
- `useEffect` for data fetching
- Context providers (except UI-only)
- Direct API calls
- Global state mutations

---

## Storybook Pattern

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { fn } from 'storybook/test'
import { HorseRow } from './horse-row'
import type { HorseInterface } from '@/lib/interfaces/horse'

const meta = {
  title: 'Horse/HorseRow',
  component: HorseRow,
  parameters: { layout: 'padded' },
  tags: ['autodocs', 'tier-3', 'simple-composite', 'horse'],
  args: {
    onEdit: fn(),
    onDelete: fn(),
    onSelect: fn(),
    onViewProfile: fn(),
  },
} satisfies Meta<typeof HorseRow>

export default meta
type Story = StoryObj<typeof meta>

// Mock data
const mockHorse: HorseInterface = {
  id: 1,
  name: 'Thunder',
  avatarUrl: '/horses/thunder.jpg',
  status: 'active',
  breed: 'Quarter Horse',
  lastSessionDate: new Date('2024-01-15'),
}

export const Default: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <table className="w-full">
      <tbody>
        <HorseRow {...args} horse={loaded.horse} />
      </tbody>
    </table>
  ),
}

export const Selected: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <table className="w-full">
      <tbody>
        <HorseRow {...args} horse={loaded.horse} selected />
      </tbody>
    </table>
  ),
}

export const WithActions: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <table className="w-full">
      <tbody>
        <HorseRow {...args} horse={loaded.horse} showActions />
      </tbody>
    </table>
  ),
}

export const InactiveHorse: Story = {
  loaders: [async () => ({ horse: { ...mockHorse, status: 'inactive' } })],
  render: (args, { loaded }) => (
    <table className="w-full">
      <tbody>
        <HorseRow {...args} horse={loaded.horse} />
      </tbody>
    </table>
  ),
}
```

---

## Local State Pattern

```typescript
'use client'

import { useState, useCallback } from 'react'

export function HorseRow({
  horse,
  selected = false,
  onEdit,
  onDelete,
  onSelect,
  showActions = true,
}: HorseRowProps) {
  // Local UI state only - no data fetching
  const [isHovered, setIsHovered] = useState(false)
  const [isMenuOpen, setIsMenuOpen] = useState(false)
  const [isExpanded, setIsExpanded] = useState(false)

  // Memoized callbacks
  const handleEdit = useCallback(() => {
    onEdit?.(horse.id)
  }, [onEdit, horse.id])

  const handleDelete = useCallback(() => {
    onDelete?.(horse.id)
  }, [onDelete, horse.id])

  const handleSelect = useCallback(() => {
    onSelect?.(horse)
  }, [onSelect, horse])

  return (
    <tr
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
      onClick={handleSelect}
      className={clsx(
        'cursor-pointer',
        selected && 'bg-blue-50 dark:bg-blue-900/20'
      )}
    >
      {/* Row content */}
    </tr>
  )
}
```

---

## Checklist

- [ ] Composes 2+ lower-tier components
- [ ] Local UI state documented (isExpanded, isHovered, etc.)
- [ ] All callbacks documented with signatures
- [ ] No data fetching or server actions
- [ ] Uses domain types for display only
- [ ] Uses $Model.property notation for data binding
- [ ] All states documented
- [ ] Storybook story uses loaders with action spies
- [ ] Keyboard navigation implemented
- [ ] Focus management documented

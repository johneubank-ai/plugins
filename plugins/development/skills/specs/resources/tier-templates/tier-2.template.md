# Tier 2: Domain Primitive Template

Use this template for domain-aware, display-only components that render domain data.

---

## Template

```markdown
# ComponentName

## tier
2 - Domain Primitive

## purpose
[One sentence describing what this component displays]

## where-does-this-appear
- [ParentComponent]: [Placement description]

## domain
[Primary domain this component belongs to]
- horse | client | therapist | session | schedule | etc.

## composed-of
[List the Tier 0-1 components this component uses]
- Avatar from `@/components/avatar`
- Badge from `@/components/badge`

## domain-types
[List domain interfaces used - TYPES ONLY, no functions]
```typescript
import type { HorseInterface } from '@/lib/interfaces/horse'
import type { ClientInterface } from '@/lib/interfaces/client'
```

## data-displayed
[Use $Model.property notation to document data binding]
- `$Horse.name` - Horse's display name
- `$Horse.avatarUrl` - Horse's profile image
- `$Horse.status` - Current status (active/inactive)

## props
```typescript
interface ComponentNameProps {
  // Domain entity as prop
  horse: HorseInterface
  // Optional display variations
  size?: 'sm' | 'md' | 'lg'
  showStatus?: boolean
  className?: string
}
```

## states
- default: Normal display
- empty: No data provided (edge case)

## size
- width: [Tailwind default class]
- height: [Tailwind default class]
- padding: [Tailwind default class]

## accessibility
- aria-label: [Descriptive label using domain data]
- role: [ARIA role if needed]

## storybook-tags
- autodocs
- tier-2
- domain-primitive
- [domain-name]
```

---

## Example: HorseAvatar Component

```markdown
# HorseAvatar

## tier
2 - Domain Primitive

## purpose
Display a horse's avatar image with optional name and status indicator.

## where-does-this-appear
- HorseRow: Table cell avatar
- SessionCard: Horse assignment display
- HorseSelect: Dropdown option

## domain
horse

## composed-of
- Avatar from `@/components/avatar`
- Badge from `@/components/badge`

## domain-types
```typescript
import type { HorseInterface } from '@/lib/interfaces/horse'
```

## data-displayed
- `$Horse.name` - Display name
- `$Horse.avatarUrl` - Profile image URL
- `$Horse.status` - Active/inactive/retired status

## props
```typescript
interface HorseAvatarProps {
  horse: HorseInterface
  size?: 'sm' | 'md' | 'lg'
  showName?: boolean
  showStatus?: boolean
  className?: string
}
```

## states
- default: Avatar with optional name
- no-image: Initials fallback
- inactive: Grayed out appearance

## size
- sm: size-6 (24px)
- md: size-8 (32px)
- lg: size-12 (48px)

## accessibility
- aria-label: "{Horse name} avatar"
- role: img (when displaying image)

## storybook-tags
- autodocs
- tier-2
- domain-primitive
- horse
```

---

## Allowed Dependencies

```typescript
// Tier 0-1 components
import { Avatar } from '@/components/avatar'
import { Badge } from '@/components/badge'

// Domain interfaces (TYPES ONLY)
import type { HorseInterface } from '@/lib/interfaces/horse'
import type { ClientInterface } from '@/lib/interfaces/client'

// Icons
import { UserIcon } from '@heroicons/react/16/solid'

// Utilities
import clsx from 'clsx'
```

## Forbidden

- Server actions (`@/lib/actions/*`)
- Service functions (`@/lib/services/*`)
- Data fetching (no `useEffect` for API calls)
- Mutations of any kind
- Context providers
- Interface functions (only types)

---

## Storybook Pattern

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { HorseAvatar } from './horse-avatar'
import type { HorseInterface } from '@/lib/interfaces/horse'

const meta = {
  title: 'Horse/HorseAvatar',
  component: HorseAvatar,
  parameters: { layout: 'centered' },
  tags: ['autodocs', 'tier-2', 'domain-primitive', 'horse'],
} satisfies Meta<typeof HorseAvatar>

export default meta
type Story = StoryObj<typeof meta>

// Mock data using loaders
const mockHorse: HorseInterface = {
  id: 1,
  name: 'Thunder',
  avatarUrl: '/horses/thunder.jpg',
  status: 'active',
  // ... other required fields
}

export const Default: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const WithName: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} showName />
  ),
}

export const NoImage: Story = {
  loaders: [async () => ({ horse: { ...mockHorse, avatarUrl: null } })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const AllSizes: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <div className="flex items-center gap-4">
      <HorseAvatar horse={loaded.horse} size="sm" />
      <HorseAvatar horse={loaded.horse} size="md" />
      <HorseAvatar horse={loaded.horse} size="lg" />
    </div>
  ),
}
```

---

## Domain Fixture Pattern

Create reusable fixtures for consistent testing:

```typescript
// fixtures/horse.fixtures.ts
import type { HorseInterface } from '@/lib/interfaces/horse'

export const mockHorseActive: HorseInterface = {
  id: 1,
  name: 'Thunder',
  avatarUrl: '/horses/thunder.jpg',
  status: 'active',
  breed: 'Quarter Horse',
  // ... complete interface
}

export const mockHorseInactive: HorseInterface = {
  ...mockHorseActive,
  id: 2,
  name: 'Storm',
  status: 'inactive',
}

export const mockHorseNoImage: HorseInterface = {
  ...mockHorseActive,
  id: 3,
  name: 'Lightning',
  avatarUrl: null,
}
```

---

## Checklist

- [ ] Component displays domain data (read-only)
- [ ] Domain types imported as `type` only (no functions)
- [ ] No server actions or data fetching
- [ ] No mutations or side effects
- [ ] Uses $Model.property notation for data binding
- [ ] Composes Tier 0-1 components
- [ ] All states documented
- [ ] Storybook story uses loaders with domain fixtures

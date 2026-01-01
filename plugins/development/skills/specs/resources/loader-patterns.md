# Loader Patterns

This document defines patterns for using Storybook loaders to provide mock data for Tier 0-3 components.

---

## When to Use Loaders

| Tier | Data Strategy | When to Use |
|------|---------------|-------------|
| 0-1 | Loaders | Simple mock data or no data |
| 2-3 | Loaders | Domain fixtures |
| 4-6 | MSW | Never use loaders for async data |

**Rule**: If a component receives data via props (not fetching), use loaders.

---

## Basic Loader Pattern

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { ComponentName } from './component-name'

const meta = {
  title: 'Domain/ComponentName',
  component: ComponentName,
  tags: ['autodocs', 'tier-2'],
} satisfies Meta<typeof ComponentName>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  loaders: [
    async () => ({
      entity: {
        id: 1,
        name: 'Entity Name',
        status: 'active',
      },
    }),
  ],
  render: (args, { loaded }) => (
    <ComponentName {...args} entity={loaded.entity} />
  ),
}
```

---

## Why Use Loaders Instead of Args

Loaders are preferred over args for object props because:

1. **Async support**: Loaders can load data asynchronously
2. **Type safety**: Loaded data is typed via render function
3. **Reusability**: Can share fixtures across stories
4. **Isolation**: Each story gets fresh data

```typescript
// BAD: Object in args loses type checking
export const Bad: Story = {
  args: {
    entity: { id: 1, name: 'Test' }, // No type checking
  },
}

// GOOD: Loader with typed render
export const Good: Story = {
  loaders: [async () => ({ entity: mockEntity })],
  render: (args, { loaded }) => (
    <Component entity={loaded.entity} />  // Type checked
  ),
}
```

---

## Domain Fixtures

Create reusable fixtures for domain entities:

```typescript
// fixtures/horse.fixtures.ts
import type { HorseInterface } from '@/lib/interfaces/horse'

export const mockHorseActive: HorseInterface = {
  id: 1,
  name: 'Thunder',
  avatarUrl: '/horses/thunder.jpg',
  status: 'active',
  breed: 'Quarter Horse',
  birthDate: '2018-05-15',
  weight: 1100,
  color: 'Bay',
  notes: 'Excellent therapy horse',
  createdAt: new Date('2020-01-01'),
  updatedAt: new Date('2024-01-01'),
}

export const mockHorseInactive: HorseInterface = {
  ...mockHorseActive,
  id: 2,
  name: 'Storm',
  status: 'inactive',
  avatarUrl: '/horses/storm.jpg',
}

export const mockHorseNoAvatar: HorseInterface = {
  ...mockHorseActive,
  id: 3,
  name: 'Lightning',
  avatarUrl: null,
}

export const mockHorses: HorseInterface[] = [
  mockHorseActive,
  mockHorseInactive,
  mockHorseNoAvatar,
]
```

---

## Using Fixtures in Stories

```typescript
// horse-avatar.stories.tsx
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { HorseAvatar } from './horse-avatar'
import {
  mockHorseActive,
  mockHorseInactive,
  mockHorseNoAvatar,
} from '@/fixtures/horse.fixtures'

const meta = {
  title: 'Horse/HorseAvatar',
  component: HorseAvatar,
  tags: ['autodocs', 'tier-2'],
} satisfies Meta<typeof HorseAvatar>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  loaders: [async () => ({ horse: mockHorseActive })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const Inactive: Story = {
  loaders: [async () => ({ horse: mockHorseInactive })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const NoAvatar: Story = {
  loaders: [async () => ({ horse: mockHorseNoAvatar })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}
```

---

## Multiple Entities

Load multiple related entities:

```typescript
export const WithRelatedData: Story = {
  loaders: [
    async () => ({
      session: mockSession,
      horse: mockHorseActive,
      client: mockClientActive,
      therapist: mockTherapistAvailable,
    }),
  ],
  render: (args, { loaded }) => (
    <SessionCard
      {...args}
      session={loaded.session}
      horse={loaded.horse}
      client={loaded.client}
      therapist={loaded.therapist}
    />
  ),
}
```

---

## Array Data

Load arrays for list components:

```typescript
import { mockHorses } from '@/fixtures/horse.fixtures'

export const MultipleItems: Story = {
  loaders: [async () => ({ horses: mockHorses })],
  render: (args, { loaded }) => (
    <div className="space-y-2">
      {loaded.horses.map((horse) => (
        <HorseRow key={horse.id} horse={horse} {...args} />
      ))}
    </div>
  ),
}

export const EmptyList: Story = {
  loaders: [async () => ({ horses: [] })],
  render: (args, { loaded }) => (
    loaded.horses.length > 0 ? (
      <div className="space-y-2">
        {loaded.horses.map((horse) => (
          <HorseRow key={horse.id} horse={horse} {...args} />
        ))}
      </div>
    ) : (
      <p className="text-gray-500">No horses found</p>
    )
  ),
}
```

---

## Computed/Derived Data

Generate variations of data in loaders:

```typescript
export const LongName: Story = {
  loaders: [
    async () => ({
      horse: {
        ...mockHorseActive,
        name: 'Sir Thunderbolt von Lightning Strike III',
      },
    }),
  ],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const ManyItems: Story = {
  loaders: [
    async () => ({
      horses: Array.from({ length: 50 }, (_, i) => ({
        ...mockHorseActive,
        id: i + 1,
        name: `Horse ${i + 1}`,
      })),
    }),
  ],
  render: (args, { loaded }) => (
    <HorseList horses={loaded.horses} {...args} />
  ),
}
```

---

## Combining Args and Loaders

Use args for primitive props, loaders for object props:

```typescript
export const CustomSize: Story = {
  args: {
    size: 'lg',       // Primitive - use args
    showName: true,   // Primitive - use args
  },
  loaders: [
    async () => ({
      horse: mockHorseActive,  // Object - use loader
    }),
  ],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}
```

---

## Action Callbacks with Loaders

Combine loaders with action spies:

```typescript
import { fn } from 'storybook/test'

const meta = {
  title: 'Horse/HorseRow',
  component: HorseRow,
  args: {
    onEdit: fn(),
    onDelete: fn(),
    onSelect: fn(),
  },
} satisfies Meta<typeof HorseRow>

export const Default: Story = {
  loaders: [async () => ({ horse: mockHorseActive })],
  render: (args, { loaded }) => (
    <HorseRow {...args} horse={loaded.horse} />
  ),
}
```

---

## Async Loaders

Loaders support async operations:

```typescript
// Simulating async data loading
export const WithDelay: Story = {
  loaders: [
    async () => {
      // Simulate API call
      await new Promise(resolve => setTimeout(resolve, 100))
      return { horse: mockHorseActive }
    },
  ],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

// Loading from a fixture file
export const FromFile: Story = {
  loaders: [
    async () => {
      const { mockHorses } = await import('@/fixtures/horse.fixtures')
      return { horses: mockHorses }
    },
  ],
  render: (args, { loaded }) => (
    <HorseList horses={loaded.horses} {...args} />
  ),
}
```

---

## Shared Loaders

Define loaders at meta level for all stories:

```typescript
const meta = {
  title: 'Horse/HorseCard',
  component: HorseCard,
  loaders: [
    async () => ({
      horse: mockHorseActive,
    }),
  ],
} satisfies Meta<typeof HorseCard>

// All stories inherit the loader
export const Default: Story = {
  render: (args, { loaded }) => (
    <HorseCard {...args} horse={loaded.horse} />
  ),
}

// Override loader for specific story
export const Inactive: Story = {
  loaders: [async () => ({ horse: mockHorseInactive })],
  render: (args, { loaded }) => (
    <HorseCard {...args} horse={loaded.horse} />
  ),
}
```

---

## Loader Type Safety

Define types for loaded data:

```typescript
interface LoadedData {
  horse: HorseInterface
  therapist?: TherapistInterface
}

export const WithTypes: Story = {
  loaders: [
    async (): Promise<LoadedData> => ({
      horse: mockHorseActive,
      therapist: mockTherapist,
    }),
  ],
  render: (args, { loaded }: { loaded: LoadedData }) => (
    <AssignmentCard
      {...args}
      horse={loaded.horse}
      therapist={loaded.therapist}
    />
  ),
}
```

---

## Fixture Organization

Organize fixtures by domain:

```
/fixtures/
├── horse.fixtures.ts       # Horse mock data
├── client.fixtures.ts      # Client mock data
├── therapist.fixtures.ts   # Therapist mock data
├── session.fixtures.ts     # Session mock data
└── index.ts                # Re-exports all fixtures
```

```typescript
// fixtures/index.ts
export * from './horse.fixtures'
export * from './client.fixtures'
export * from './therapist.fixtures'
export * from './session.fixtures'
```

---

## Checklist for Loaders

- [ ] Uses loaders for object/array props (not args)
- [ ] Uses domain fixtures for consistency
- [ ] Each story has appropriate fixture variant
- [ ] Edge cases covered (empty, long text, null values)
- [ ] Loaders return typed data
- [ ] Combines with args for primitive props
- [ ] Combines with `fn()` for callbacks
- [ ] Stories are self-contained and isolated

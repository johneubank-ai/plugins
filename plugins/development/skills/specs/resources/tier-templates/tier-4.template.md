# Tier 4: Data-Driven Composite Template

Use this template for components that fetch their own data, call server actions, and handle loading/error/empty states.

---

## Template

```markdown
# ComponentName

## tier
4 - Data-Driven Composite

## purpose
[One sentence describing what this component does, including data operations]

## where-does-this-appear
- [ParentComponent]: [Placement description]

## domain
[Primary domain this component belongs to]
- horse | client | therapist | session | schedule | etc.

## composed-of
[List the Tier 0-3 components this component uses]
- HorseRow from `@/components/horse/horse-row`
- Table from `@/components/table`
- Pagination from `@/components/pagination`

## domain-types
[List domain interfaces used]
```typescript
import type { HorseInterface } from '@/lib/interfaces/horse'
import type { PaginationMeta } from '@/lib/types/pagination'
```

## data-sources
[Document all data fetching operations]
- `getHorses()` from `@/lib/actions/horse-actions`
- `getHorsesByStatus(status)` from `@/lib/actions/horse-actions`

## server-actions
[Document all server actions called]
- `updateHorseStatus(id, status)` - Updates horse active status
- `deleteHorse(id)` - Deletes a horse record

## data-displayed
[Use $Model.property notation]
- `$Horse.name` - Display name
- `$Horse.status` - Status badge
- `$Horse[]` - List of horses

## callbacks
[Document event callbacks if any]
- `onHorseSelect?(horse: HorseInterface)` - Optional external handler

## states
- default: Data loaded and displayed
- loading: Skeleton placeholders
- error: Error message with retry
- empty: Empty state with CTA

## loading-state
[Describe loading behavior]
- Initial load: Full skeleton table
- Pagination: Row-level loading
- Refresh: Overlay spinner

## error-state
[Describe error handling]
- Message: "Failed to load horses"
- Action: Retry button
- Fallback: None (show error)

## empty-state
[Describe empty state]
- Message: "No horses found"
- Action: "Add your first horse" button
- Icon: HorseIcon or similar

## props
```typescript
interface ComponentNameProps {
  // Optional filters
  status?: 'active' | 'inactive' | 'all'

  // Optional handlers for parent integration
  onHorseSelect?: (horse: HorseInterface) => void

  // Display options
  className?: string
}
```

## size
- width: [Tailwind default class]
- height: [Tailwind default class]
- padding: [Tailwind default class]

## accessibility
- aria-live: polite (for loading states)
- aria-busy: true during loading
- role: region with aria-label

## mock-file
`component-name.mock.ts`

## storybook-tags
- autodocs
- tier-4
- data-driven
- [domain-name]
```

---

## Example: HorsesTable Component

```markdown
# HorsesTable

## tier
4 - Data-Driven Composite

## purpose
Display a paginated, filterable table of horses with CRUD operations.

## where-does-this-appear
- HorsesPage: Main content area
- HorseManagementDrawer: Embedded table

## domain
horse

## composed-of
- HorseRow from `@/components/horse/horse-row`
- Table from `@/components/table`
- Pagination from `@/components/pagination`
- EmptyState from `@/components/empty-state`
- LoadingSkeleton from `@/components/loading-skeleton`

## domain-types
```typescript
import type { HorseInterface } from '@/lib/interfaces/horse'
import type { PaginationMeta } from '@/lib/types/pagination'
```

## data-sources
- `getHorses(params)` from `@/lib/actions/horse-actions`
  - Returns: `{ horses: HorseInterface[], meta: PaginationMeta }`
  - Params: `{ page, limit, status, search }`

## server-actions
- `updateHorseStatus(id, status)` - Toggle active/inactive
- `deleteHorse(id)` - Delete with confirmation

## data-displayed
- `$Horse[]` - Array of horse records
- `$Horse.name` - Horse name in row
- `$Horse.status` - Status badge
- `$Horse.breed` - Secondary info
- `$PaginationMeta.total` - Total count
- `$PaginationMeta.page` - Current page

## callbacks
- `onHorseSelect?(horse: HorseInterface)` - External selection handler
- `onHorseEdit?(id: number)` - Opens edit drawer
- `onHorseDelete?(id: number)` - Triggers delete flow

## states
- default: Table with horse data
- loading: Skeleton rows
- error: Error banner with retry
- empty: No horses illustration

## loading-state
- Initial: 5 skeleton rows
- Pagination: Keep current data, show spinner
- Filter change: Full skeleton

## error-state
- Message: "Unable to load horses. Please try again."
- Action: "Retry" button
- Details: Show error code if available

## empty-state
- Message: "No horses in the system yet"
- Action: "Add your first horse" button
- Icon: Illustration of horse

## props
```typescript
interface HorsesTableProps {
  status?: 'active' | 'inactive' | 'all'
  initialPage?: number
  pageSize?: number
  onHorseSelect?: (horse: HorseInterface) => void
  className?: string
}
```

## size
- width: w-full
- height: min-h-[400px]
- padding: p-0 (table manages padding)

## accessibility
- role: region
- aria-label: "Horses table"
- aria-live: polite
- aria-busy: true during loading

## mock-file
`horses-table.mock.ts`

## storybook-tags
- autodocs
- tier-4
- data-driven
- horse
- table
```

---

## Allowed Dependencies

```typescript
// All lower tier components
import { HorseRow } from '@/components/horse/horse-row'
import { Table, TableHead, TableBody } from '@/components/table'
import { Pagination } from '@/components/pagination'
import { EmptyState } from '@/components/empty-state'

// Server actions
import { getHorses, deleteHorse } from '@/lib/actions/horse-actions'

// Services (if needed)
import { horseService } from '@/lib/services/horse-service'

// Context providers
import { useHorseContext } from '@/contexts/horse-context'

// Domain interfaces
import type { HorseInterface } from '@/lib/interfaces/horse'

// React hooks including useEffect for data fetching
import { useState, useEffect, useCallback } from 'react'
```

## Required Files

1. `component-name.tsx` - Main component
2. `component-name.stories.tsx` - Storybook stories
3. `component-name.mock.ts` - MSW handlers
4. `component-name.spec.md` - This specification

---

## MSW Mock File Pattern

```typescript
// horses-table.mock.ts
import { http, HttpResponse, delay } from 'msw'
import type { HorseInterface } from '@/lib/interfaces/horse'

// Mock data
export const mockHorses: HorseInterface[] = [
  { id: 1, name: 'Thunder', status: 'active', breed: 'Quarter Horse' },
  { id: 2, name: 'Storm', status: 'active', breed: 'Appaloosa' },
  { id: 3, name: 'Lightning', status: 'inactive', breed: 'Arabian' },
]

// Default handlers
export const handlers = [
  http.get('/api/horses', async ({ request }) => {
    const url = new URL(request.url)
    const status = url.searchParams.get('status')
    const page = parseInt(url.searchParams.get('page') || '1')
    const limit = parseInt(url.searchParams.get('limit') || '10')

    await delay(100) // Simulate network

    let filtered = mockHorses
    if (status && status !== 'all') {
      filtered = mockHorses.filter(h => h.status === status)
    }

    const start = (page - 1) * limit
    const paginated = filtered.slice(start, start + limit)

    return HttpResponse.json({
      horses: paginated,
      meta: {
        total: filtered.length,
        page,
        limit,
        totalPages: Math.ceil(filtered.length / limit),
      },
    })
  }),

  http.delete('/api/horses/:id', async ({ params }) => {
    await delay(100)
    return new HttpResponse(null, { status: 204 })
  }),
]

// Error state handler
export const errorHandlers = [
  http.get('/api/horses', () => {
    return HttpResponse.json(
      { error: 'Failed to fetch horses' },
      { status: 500 }
    )
  }),
]

// Empty state handler
export const emptyHandlers = [
  http.get('/api/horses', () => {
    return HttpResponse.json({
      horses: [],
      meta: { total: 0, page: 1, limit: 10, totalPages: 0 },
    })
  }),
]

// Loading state handler (for testing loading UI)
export const loadingHandlers = [
  http.get('/api/horses', async () => {
    await delay('infinite')
    return HttpResponse.json({ horses: [] })
  }),
]
```

---

## Storybook Pattern

```typescript
// horses-table.stories.tsx
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { fn } from 'storybook/test'
import { HorsesTable } from './horses-table'
import {
  handlers,
  errorHandlers,
  emptyHandlers,
  loadingHandlers,
} from './horses-table.mock'

const meta = {
  title: 'Horse/HorsesTable',
  component: HorsesTable,
  parameters: {
    layout: 'padded',
  },
  tags: ['autodocs', 'tier-4', 'data-driven', 'horse'],
  args: {
    onHorseSelect: fn(),
  },
} satisfies Meta<typeof HorsesTable>

export default meta
type Story = StoryObj<typeof meta>

// Default story with data
export const Default: Story = {
  parameters: {
    msw: { handlers },
  },
}

// Loading state
export const Loading: Story = {
  parameters: {
    msw: { handlers: loadingHandlers },
  },
}

// Error state
export const Error: Story = {
  parameters: {
    msw: { handlers: errorHandlers },
  },
}

// Empty state
export const Empty: Story = {
  parameters: {
    msw: { handlers: emptyHandlers },
  },
}

// Filtered by status
export const ActiveOnly: Story = {
  args: {
    status: 'active',
  },
  parameters: {
    msw: { handlers },
  },
}
```

---

## Checklist

- [ ] Component fetches its own data
- [ ] All data sources documented
- [ ] All server actions documented
- [ ] Loading state implemented and documented
- [ ] Error state implemented with retry
- [ ] Empty state implemented with CTA
- [ ] MSW mock file created with all handlers
- [ ] Stories cover all states (default, loading, error, empty)
- [ ] Uses MSW for Storybook mocking (not loaders)
- [ ] Accessibility attributes for dynamic content

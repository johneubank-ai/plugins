# MSW (Mock Service Worker) Patterns

This document defines patterns for using MSW to mock API requests in Storybook stories for Tier 4-6 components.

---

## When to Use MSW

| Tier | Data Strategy | When to Use |
|------|---------------|-------------|
| 0-3 | Loaders | Never use MSW |
| 4-6 | MSW | Always use MSW |

**Rule**: If a component fetches data or calls server actions, use MSW handlers.

---

## Mock File Structure

Every Tier 4+ component should have a `.mock.ts` file:

```
/components/horse/horses-table/
├── horses-table.tsx
├── horses-table.stories.tsx
├── horses-table.mock.ts    <-- MSW handlers
├── horses-table.spec.md
└── index.ts
```

---

## Basic Handler Pattern

```typescript
// component-name.mock.ts
import { http, HttpResponse, delay } from 'msw'
import type { EntityInterface } from '@/lib/interfaces/entity'

// Mock data
export const mockEntities: EntityInterface[] = [
  { id: 1, name: 'Entity 1', status: 'active' },
  { id: 2, name: 'Entity 2', status: 'inactive' },
]

// Default handlers
export const handlers = [
  // GET list
  http.get('/api/entities', async () => {
    await delay(100) // Simulate network latency
    return HttpResponse.json(mockEntities)
  }),

  // GET single
  http.get('/api/entities/:id', async ({ params }) => {
    await delay(100)
    const entity = mockEntities.find(e => e.id === Number(params.id))
    if (!entity) {
      return HttpResponse.json({ error: 'Not found' }, { status: 404 })
    }
    return HttpResponse.json(entity)
  }),

  // POST create
  http.post('/api/entities', async ({ request }) => {
    await delay(200)
    const body = await request.json()
    const newEntity = { ...body, id: mockEntities.length + 1 }
    return HttpResponse.json(newEntity, { status: 201 })
  }),

  // PUT update
  http.put('/api/entities/:id', async ({ request, params }) => {
    await delay(200)
    const body = await request.json()
    return HttpResponse.json({ ...body, id: Number(params.id) })
  }),

  // DELETE
  http.delete('/api/entities/:id', async () => {
    await delay(100)
    return new HttpResponse(null, { status: 204 })
  }),
]
```

---

## State-Specific Handlers

Create separate handler sets for different component states:

```typescript
// Default (success) handlers
export const handlers = [
  http.get('/api/entities', () => HttpResponse.json(mockEntities)),
]

// Loading state - infinite delay
export const loadingHandlers = [
  http.get('/api/entities', async () => {
    await delay('infinite')
    return HttpResponse.json([])
  }),
]

// Error state - 500 response
export const errorHandlers = [
  http.get('/api/entities', () => {
    return HttpResponse.json(
      { error: 'Internal server error' },
      { status: 500 }
    )
  }),
]

// Empty state - empty array
export const emptyHandlers = [
  http.get('/api/entities', () => {
    return HttpResponse.json([])
  }),
]

// Validation error - 422 response
export const validationErrorHandlers = [
  http.post('/api/entities', () => {
    return HttpResponse.json(
      {
        error: 'Validation failed',
        details: { name: 'Name already exists' },
      },
      { status: 422 }
    )
  }),
]
```

---

## Using Handlers in Stories

```typescript
// component-name.stories.tsx
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { ComponentName } from './component-name'
import {
  handlers,
  loadingHandlers,
  errorHandlers,
  emptyHandlers,
} from './component-name.mock'

const meta = {
  title: 'Domain/ComponentName',
  component: ComponentName,
  tags: ['autodocs', 'tier-4'],
} satisfies Meta<typeof ComponentName>

export default meta
type Story = StoryObj<typeof meta>

// Default state
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
```

---

## Pagination Handlers

```typescript
export const handlers = [
  http.get('/api/entities', async ({ request }) => {
    const url = new URL(request.url)
    const page = parseInt(url.searchParams.get('page') || '1')
    const limit = parseInt(url.searchParams.get('limit') || '10')

    await delay(100)

    const start = (page - 1) * limit
    const paginated = mockEntities.slice(start, start + limit)

    return HttpResponse.json({
      data: paginated,
      meta: {
        total: mockEntities.length,
        page,
        limit,
        totalPages: Math.ceil(mockEntities.length / limit),
      },
    })
  }),
]
```

---

## Filtering and Search Handlers

```typescript
export const handlers = [
  http.get('/api/entities', async ({ request }) => {
    const url = new URL(request.url)
    const status = url.searchParams.get('status')
    const search = url.searchParams.get('search')

    await delay(100)

    let filtered = [...mockEntities]

    // Filter by status
    if (status && status !== 'all') {
      filtered = filtered.filter(e => e.status === status)
    }

    // Filter by search term
    if (search) {
      filtered = filtered.filter(e =>
        e.name.toLowerCase().includes(search.toLowerCase())
      )
    }

    return HttpResponse.json(filtered)
  }),
]
```

---

## Server Action Mocking

For Next.js server actions, mock the underlying API calls:

```typescript
// If your server action calls an API
export const handlers = [
  http.post('/api/internal/create-entity', async ({ request }) => {
    await delay(200)
    const body = await request.json()
    return HttpResponse.json({ ...body, id: 1, createdAt: new Date() })
  }),
]

// Or mock at the form submission level
export const handlers = [
  http.post('/api/entities', async ({ request }) => {
    await delay(300) // Simulate server processing
    const formData = await request.formData()
    const name = formData.get('name')
    return HttpResponse.json({ id: 1, name, status: 'active' })
  }),
]
```

---

## Composing Handlers

Combine handlers from multiple components:

```typescript
// horses-page.mock.ts
import { handlers as tableHandlers } from '@/components/horse/horses-table.mock'
import { handlers as drawerHandlers } from '@/components/horse/horse-profile-drawer.mock'

// Auth mock for pages
const authHandlers = [
  http.get('/api/auth/session', () => {
    return HttpResponse.json({
      user: { id: '1', name: 'Test User', role: 'admin' },
    })
  }),
]

export const handlers = [
  ...authHandlers,
  ...tableHandlers,
  ...drawerHandlers,
]
```

---

## Response Helpers

Create helper functions for common responses:

```typescript
// mock-helpers.ts
import { HttpResponse, delay } from 'msw'

export async function successResponse<T>(data: T, delayMs = 100) {
  await delay(delayMs)
  return HttpResponse.json(data)
}

export async function errorResponse(message: string, status = 500) {
  await delay(100)
  return HttpResponse.json({ error: message }, { status })
}

export async function notFoundResponse() {
  await delay(100)
  return HttpResponse.json({ error: 'Not found' }, { status: 404 })
}

export async function validationErrorResponse(details: Record<string, string>) {
  await delay(100)
  return HttpResponse.json(
    { error: 'Validation failed', details },
    { status: 422 }
  )
}
```

---

## Dynamic Mock Data

Generate mock data dynamically:

```typescript
// fixtures/horse.fixtures.ts
import type { HorseInterface } from '@/lib/interfaces/horse'

let idCounter = 0

export function createMockHorse(overrides: Partial<HorseInterface> = {}): HorseInterface {
  idCounter++
  return {
    id: idCounter,
    name: `Horse ${idCounter}`,
    breed: 'Quarter Horse',
    status: 'active',
    avatarUrl: `/horses/horse-${idCounter}.jpg`,
    birthDate: '2018-01-01',
    createdAt: new Date(),
    updatedAt: new Date(),
    ...overrides,
  }
}

export function createMockHorses(count: number): HorseInterface[] {
  return Array.from({ length: count }, () => createMockHorse())
}

// Usage in mock file
import { createMockHorses } from '@/fixtures/horse.fixtures'

export const mockHorses = createMockHorses(25)
```

---

## Real-time Updates Mock

Mock real-time/polling endpoints:

```typescript
let updateCount = 0

export const pollingHandlers = [
  http.get('/api/entities/updates', async () => {
    await delay(500)
    updateCount++
    return HttpResponse.json({
      hasUpdates: updateCount % 3 === 0,
      lastUpdate: new Date().toISOString(),
    })
  }),
]
```

---

## File Upload Mock

```typescript
export const uploadHandlers = [
  http.post('/api/upload', async ({ request }) => {
    await delay(1000) // Simulate upload time

    const formData = await request.formData()
    const file = formData.get('file') as File

    return HttpResponse.json({
      url: `/uploads/${file.name}`,
      filename: file.name,
      size: file.size,
    })
  }),
]

// Upload error
export const uploadErrorHandlers = [
  http.post('/api/upload', async () => {
    await delay(500)
    return HttpResponse.json(
      { error: 'File too large' },
      { status: 413 }
    )
  }),
]
```

---

## Network Error Simulation

```typescript
import { HttpResponse } from 'msw'

export const networkErrorHandlers = [
  http.get('/api/entities', () => {
    return HttpResponse.error()
  }),
]
```

---

## Checklist for MSW Mock Files

- [ ] Mock file named `{component-name}.mock.ts`
- [ ] Exports `handlers` as default set
- [ ] Exports `loadingHandlers` for loading state
- [ ] Exports `errorHandlers` for error state
- [ ] Exports `emptyHandlers` for empty state
- [ ] Includes realistic delays with `await delay()`
- [ ] Mock data matches interface types
- [ ] Handles URL parameters and query strings
- [ ] Supports pagination if component uses it
- [ ] Supports filtering if component uses it

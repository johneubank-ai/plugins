# Component Tier Definitions

This document defines the 7-tier component classification system used for organizing and managing UI components.

## Visual Hierarchy

```
                    ┌─────────────────────────────────┐
                    │  Tier 6: Top-Level Layouts/Pages │
                    └─────────────┬───────────────────┘
                                  │
                    ┌─────────────▼───────────────────┐
                    │  Tier 5: Container Composites   │
                    │  (Drawers, Full Forms)          │
                    └─────────────┬───────────────────┘
                                  │
                    ┌─────────────▼───────────────────┐
                    │  Tier 4: Data-Driven Composites │
                    │  (Fetch Data, Server Actions)   │
                    └─────────────┬───────────────────┘
                                  │
         ┌────────────────────────┼────────────────────────┐
         │                        │                        │
┌────────▼────────┐    ┌──────────▼──────────┐    ┌───────▼───────┐
│ Tier 3: Simple  │    │ Tier 2: Domain      │    │ Tier 1: Leaf  │
│ Composites      │    │ Primitives          │    │ Primitives    │
└────────┬────────┘    └──────────┬──────────┘    └───────┬───────┘
         │                        │                        │
         └────────────────────────┼────────────────────────┘
                                  │
                    ┌─────────────▼───────────────────┐
                    │  Tier 0: Library Primitives     │
                    │  (Catalyst UI)                  │
                    └─────────────────────────────────┘
```

---

## The Golden Rule

> **Components may only import from their own tier or lower tiers—never higher.**

This ensures a clean dependency graph and prevents circular dependencies.

---

## Tier 0: Library Primitives (Catalyst UI)

### Location
`/components/` (root level)

### Characteristics
- Direct Catalyst UI wrappers
- Purely presentational props
- No business logic
- Framework-agnostic styling

### Allowed Dependencies
- `@headlessui/react`
- `clsx`
- Internal Tier 0 primitives only

### Forbidden
- Server actions
- `@/lib/*` imports
- Domain types/interfaces
- Data fetching
- Context providers

### Examples
```typescript
// Button, Badge, Input, Select, Textarea, Dialog
import { Button } from '@/components/button'
import { Badge } from '@/components/badge'
import { Dialog, DialogTitle, DialogBody } from '@/components/dialog'
```

### Storybook Mocking
**Strategy**: Loaders (no external data needed)

---

## Tier 1: Leaf Primitives

### Location
`/components/` (root) or `/components/ui/`

### Characteristics
- Domain-agnostic utility components
- Compose Tier 0 primitives
- Reusable across any domain
- No domain-specific types

### Allowed Dependencies
- Tier 0 components
- `@heroicons/react`
- `clsx`

### Forbidden
- Domain interfaces (`@/lib/interfaces/*`)
- Server actions
- Data fetching
- Context providers (except theme/UI)

### Examples
```typescript
// ConfirmDialog - generic confirmation dialog
// DateNavigator - date picker with navigation
// Pagination - generic page controls
// LoadingSpinner - loading indicator
```

### Storybook Mocking
**Strategy**: Loaders with simple mock data

---

## Tier 2: Domain Primitives

### Location
Domain subdirectories: `/components/horse/`, `/components/patient/`, etc.

### Characteristics
- Domain-aware, display-only components
- Props include domain types
- Render domain data
- No mutations or side effects

### Allowed Dependencies
- Tier 0-1 components
- `@/lib/interfaces/*` (types only, no functions)

### Forbidden
- Server actions (`@/lib/actions/*`)
- Data fetching
- Mutations
- Services

### Examples
```typescript
// HorseAvatar - displays horse image and name
interface HorseAvatarProps {
  horse: HorseInterface
  size?: 'sm' | 'md' | 'lg'
}

// TherapistPill - shows therapist badge
// LocationBadge - displays location info
// PatientCard - basic patient display
```

### Data Displayed
Use `$Model.property` notation in specs:
- `$Horse.name`
- `$Horse.avatarUrl`
- `$Therapist.specializations[]`

### Storybook Mocking
**Strategy**: Loaders with domain fixtures

```typescript
export const Default: Story = {
  loaders: [
    async () => ({
      horse: { id: '1', name: 'Thunder', avatarUrl: '/horse.jpg' }
    })
  ],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  )
}
```

---

## Tier 3: Simple Composites

### Location
Domain subdirectories: `/components/horse/`, `/components/session/`, etc.

### Characteristics
- Compose 2+ lower-tier components
- Local UI state (expanded, selected, etc.)
- Event callbacks (onClick, onEdit, onDelete)
- No data fetching

### Allowed Dependencies
- Tier 0-2 components
- `@/lib/interfaces/*`
- `useState`, `useReducer` for local state

### Forbidden
- Server actions
- `useEffect` for data fetching
- Context providers (except UI-only)
- Direct API calls

### Examples
```typescript
// HorseRow - table row with actions
interface HorseRowProps {
  horse: HorseInterface
  onEdit: (id: string) => void
  onDelete: (id: string) => void
}

// SessionCard - session display with expand/collapse
// AgendaItem - calendar item with selection
// PatientListItem - list item with callbacks
```

### Local UI State
Document in spec:
- `isExpanded: boolean`
- `isSelected: boolean`
- `isHovered: boolean`

### Storybook Mocking
**Strategy**: Loaders with action spies

```typescript
import { fn } from 'storybook/test'

export const Default: Story = {
  args: {
    onEdit: fn(),
    onDelete: fn(),
  },
  loaders: [
    async () => ({
      horse: { id: '1', name: 'Thunder', status: 'active' }
    })
  ]
}
```

---

## Tier 4: Data-Driven Composites

### Location
Domain subdirectories: `/components/horse/`, `/components/session/`, etc.

### Characteristics
- Fetch their own data
- Call server actions
- Use context providers
- Handle loading/error/empty states

### Allowed Dependencies
- All lower tiers
- `@/lib/actions/*`
- `@/lib/services/*`
- `@/contexts/*`
- `useEffect` for data fetching

### Required Mock File
`component-name.mock.ts` with MSW handlers

### Examples
```typescript
// HorsesTable - fetches and displays horse list
// AvailabilityDot - fetches availability status
// ConflictsCenter - monitors scheduling conflicts
// NotificationsPanel - fetches notifications
```

### States to Document
- `default` - Normal display
- `loading` - Fetching data
- `error` - Fetch failed
- `empty` - No data returned

### Storybook Mocking
**Strategy**: MSW handlers

```typescript
// horse-table.mock.ts
import { http, HttpResponse } from 'msw'

export const handlers = [
  http.get('/api/horses', () => {
    return HttpResponse.json(mockHorses)
  })
]

// horse-table.stories.tsx
import { handlers } from './horse-table.mock'

export const Default: Story = {
  parameters: {
    msw: { handlers }
  }
}
```

---

## Tier 5: Container Composites

### Location
Domain subdirectories or `/components/` root

### Characteristics
- Full lifecycle management
- Complex form state
- Dialog/Drawer wrappers
- Multi-step flows
- Mutation handling

### Allowed Dependencies
- All lower tiers
- Full access to `@/lib/*`
- Form libraries
- Validation schemas

### Required Mock File
`component-name.mock.ts` with MSW handlers for all API calls

### Examples
```typescript
// AddPatientDrawer - full patient creation flow
// HorseProfileDrawer - edit horse with validation
// SessionDetailsDrawer - view/edit session
// SchedulingWizard - multi-step scheduling
```

### Lifecycle to Document
- `idle` - Drawer closed
- `open` - Drawer visible
- `submitting` - Form being submitted
- `success` - Submission complete
- `error` - Submission failed

### Form State to Document
- Form schema
- Validation rules
- Default values
- Error handling

### Storybook Mocking
**Strategy**: MSW with multiple endpoints

```typescript
export const handlers = [
  http.get('/api/patients/:id', ({ params }) => {
    return HttpResponse.json(findPatient(params.id))
  }),
  http.put('/api/patients/:id', async ({ request }) => {
    const body = await request.json()
    return HttpResponse.json({ ...body, updatedAt: new Date() })
  }),
  http.delete('/api/patients/:id', () => {
    return new HttpResponse(null, { status: 204 })
  })
]
```

---

## Tier 6: Top-Level (Layouts & Pages)

### Location
`/components/` root or `/app/` directory

### Characteristics
- Page structure and navigation
- Route-level composition
- Authentication/authorization
- Layout shells

### Allowed Dependencies
- Any tier
- Next.js routing (`next/navigation`)
- Auth contexts
- Layout providers

### Examples
```typescript
// MainLayout - primary app shell with sidebar
// SidebarLayout - sidebar navigation structure
// AuthLayout - authentication pages wrapper
// DashboardPage - main dashboard composition
```

### Route Params to Document
- URL parameters consumed
- Query string handling
- Dynamic segments

### Auth Requirements to Document
- Required roles
- Redirect behavior
- Loading states

### Storybook Mocking
**Strategy**: MSW with auth and routing mocks

```typescript
export const handlers = [
  http.get('/api/auth/session', () => {
    return HttpResponse.json({
      user: { id: '1', name: 'Test User', role: 'admin' }
    })
  })
]
```

---

## Quick Reference Table

| Tier | Name | Location | Data Strategy | Key Characteristic |
|------|------|----------|---------------|-------------------|
| 0 | Library Primitives | /components | Loaders | Catalyst UI wrappers |
| 1 | Leaf Primitives | /components | Loaders | Domain-agnostic UI |
| 2 | Domain Primitives | /components/{domain} | Loaders | Display domain data |
| 3 | Simple Composites | /components/{domain} | Loaders | Local state + callbacks |
| 4 | Data-Driven | /components/{domain} | MSW | Fetch data + actions |
| 5 | Containers | /components/{domain} | MSW | Full forms + drawers |
| 6 | Top-Level | /app or /components | MSW | Pages + layouts |

---

## Import Validation by Tier

### What Each Tier Can Import

| Tier | Can Import From |
|------|----------------|
| 0 | Nothing above (base level) |
| 1 | Tier 0 only |
| 2 | Tier 0, 1 |
| 3 | Tier 0, 1, 2 |
| 4 | Tier 0, 1, 2, 3 |
| 5 | Tier 0, 1, 2, 3, 4 |
| 6 | All tiers |

### Import Pattern Indicators

```typescript
// TIER 0 - Only these imports
import { Button } from '@headlessui/react'
import clsx from 'clsx'

// TIER 1 - Adds icons
import { PlusIcon } from '@heroicons/react/16/solid'

// TIER 2 - Adds interfaces
import type { HorseInterface } from '@/lib/interfaces/horse'

// TIER 3 - Same as 2, but with callbacks
interface Props {
  onEdit: () => void  // Callback indicates Tier 3+
}

// TIER 4 - Adds actions/services
import { getHorses } from '@/lib/actions/horse'
import { useHorseContext } from '@/contexts/horse'

// TIER 5 - Full access
import { createHorse, updateHorse, deleteHorse } from '@/lib/actions/horse'
import { Dialog } from '@headlessui/react'

// TIER 6 - Next.js routing
import { useRouter, useParams } from 'next/navigation'
import { redirect } from 'next/navigation'
```

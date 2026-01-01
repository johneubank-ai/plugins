# Tier 5: Container Composite Template

Use this template for full lifecycle components like drawers, dialogs, multi-step forms, and wizards.

---

## Template

```markdown
# ComponentName

## tier
5 - Container Composite

## purpose
[One sentence describing the full user journey this component manages]

## where-does-this-appear
- [ParentComponent]: [How it's triggered]

## domain
[Primary domain this component belongs to]
- horse | client | therapist | session | schedule | etc.

## composed-of
[List all child components]
- Dialog/Drawer from `@/components/dialog` or `@/components/drawer`
- Form components from `@/components/`
- Tier 4 data components

## domain-types
[List domain interfaces used]
```typescript
import type { HorseInterface, CreateHorseInput } from '@/lib/interfaces/horse'
```

## data-sources
[Document data fetching for initial load]
- `getHorseById(id)` - Load existing horse for edit mode
- `getHorseBreeds()` - Populate breed dropdown

## server-actions
[Document all mutation actions]
- `createHorse(data)` - Create new horse
- `updateHorse(id, data)` - Update existing horse
- `deleteHorse(id)` - Delete horse

## form-schema
[Document form structure]
```typescript
interface HorseFormData {
  name: string           // required, 1-100 chars
  breed: string          // required, from predefined list
  status: 'active' | 'inactive'
  birthDate?: Date       // optional
  notes?: string         // optional, max 500 chars
}
```

## validation-rules
[Document validation]
- name: Required, 1-100 characters
- breed: Required, must be valid breed
- birthDate: Optional, must be in past
- notes: Optional, max 500 characters

## lifecycle-states
[Document the component's lifecycle]
- idle: Container closed
- opening: Transition in
- loading: Fetching initial data (edit mode)
- ready: Form ready for input
- validating: Client-side validation in progress
- submitting: Server action in progress
- success: Mutation completed
- error: Mutation failed
- closing: Transition out

## form-sections
[Document form sections for complex forms]
1. **Basic Info**: Name, breed, status
2. **Details**: Birth date, notes
3. **Review**: Confirm before submit

## callbacks
- `onSuccess?(entity: HorseInterface)` - Called after successful save
- `onCancel?()` - Called when user cancels
- `onDelete?(id: number)` - Called after successful delete

## props
```typescript
interface ComponentNameProps {
  // Control visibility
  open: boolean
  onClose: () => void

  // Mode: create or edit
  horseId?: number  // If provided, edit mode

  // Callbacks
  onSuccess?: (horse: HorseInterface) => void
  onDelete?: (id: number) => void
}
```

## size
- width: [Drawer/dialog width]
- height: [Drawer/dialog height]
- padding: [Internal padding]

## accessibility
- role: dialog
- aria-modal: true
- aria-labelledby: Dialog title
- focus-trap: Enabled
- keyboard: Escape to close

## mock-file
`component-name.mock.ts`

## storybook-tags
- autodocs
- tier-5
- container
- [domain-name]
- [drawer|dialog|wizard]
```

---

## Example: HorseProfileDrawer Component

```markdown
# HorseProfileDrawer

## tier
5 - Container Composite

## purpose
Manage the complete horse profile lifecycle including create, edit, and delete operations.

## where-does-this-appear
- HorsesPage: "Add Horse" button triggers create mode
- HorseRow: "Edit" action triggers edit mode
- HorseCard: Click triggers edit mode

## domain
horse

## composed-of
- Drawer from `@/components/drawer`
- Input, Select, Textarea from `@/components/`
- Button from `@/components/button`
- ConfirmDialog from `@/components/confirm-dialog`
- HorseAvatar from `@/components/horse/horse-avatar`

## domain-types
```typescript
import type { HorseInterface, CreateHorseInput, UpdateHorseInput } from '@/lib/interfaces/horse'
import type { BreedOption } from '@/lib/interfaces/horse'
```

## data-sources
- `getHorseById(id)` from `@/lib/actions/horse-actions`
  - Used in edit mode to load existing data
- `getBreeds()` from `@/lib/actions/breed-actions`
  - Populates breed select options

## server-actions
- `createHorse(data: CreateHorseInput)` - Create new horse
  - Returns: `HorseInterface`
  - Errors: Validation, duplicate name
- `updateHorse(id, data: UpdateHorseInput)` - Update horse
  - Returns: `HorseInterface`
  - Errors: Validation, not found
- `deleteHorse(id)` - Delete horse
  - Returns: `void`
  - Errors: Not found, has active sessions

## form-schema
```typescript
interface HorseFormData {
  name: string
  breed: string
  status: 'active' | 'inactive' | 'retired'
  birthDate?: string  // ISO date string
  weight?: number     // in pounds
  color?: string
  notes?: string
  avatarUrl?: string
}

const defaultValues: HorseFormData = {
  name: '',
  breed: '',
  status: 'active',
  birthDate: undefined,
  weight: undefined,
  color: '',
  notes: '',
  avatarUrl: undefined,
}
```

## validation-rules
- name: Required, 1-100 chars, unique per facility
- breed: Required, from breeds list
- status: Required, enum value
- birthDate: Optional, must be in past
- weight: Optional, 100-2500 lbs
- color: Optional, max 50 chars
- notes: Optional, max 1000 chars

## lifecycle-states
- idle: Drawer closed, no activity
- opening: Drawer animating open
- loading: Fetching horse data (edit mode only)
- ready: Form displayed and interactive
- validating: Client validation running
- submitting: Save action in progress
- deleting: Delete action in progress
- success: Operation complete, closing
- error: Operation failed, showing error

## form-sections
1. **Identity**: Name, avatar upload
2. **Classification**: Breed, status
3. **Physical**: Birth date, weight, color
4. **Notes**: Free-form notes
5. **Actions**: Save, Cancel, Delete (edit mode)

## callbacks
- `onClose()` - Always called when drawer closes
- `onSuccess?(horse: HorseInterface)` - Called after create/update
- `onDelete?(id: number)` - Called after successful delete

## props
```typescript
interface HorseProfileDrawerProps {
  open: boolean
  onClose: () => void
  horseId?: number  // Omit for create, provide for edit
  onSuccess?: (horse: HorseInterface) => void
  onDelete?: (id: number) => void
}
```

## size
- width: max-w-md (mobile: full width)
- height: h-full (slide from right)
- padding: p-6

## accessibility
- role: dialog
- aria-modal: true
- aria-labelledby: "horse-drawer-title"
- focus-trap: Active when open
- keyboard: Escape closes (with unsaved changes prompt)
- screen-reader: Announces open/close, form errors

## mock-file
`horse-profile-drawer.mock.ts`

## storybook-tags
- autodocs
- tier-5
- container
- horse
- drawer
- form
```

---

## Allowed Dependencies

```typescript
// All lower tier components
import { Drawer, DrawerHeader, DrawerBody, DrawerFooter } from '@/components/drawer'
import { Input, Select, Textarea } from '@/components/input'
import { Button } from '@/components/button'
import { ConfirmDialog } from '@/components/confirm-dialog'

// Server actions (full CRUD)
import { getHorseById, createHorse, updateHorse, deleteHorse } from '@/lib/actions/horse-actions'

// Form libraries
import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'

// Domain interfaces
import type { HorseInterface } from '@/lib/interfaces/horse'

// Context
import { useToast } from '@/contexts/toast-context'

// React
import { useState, useEffect, useCallback } from 'react'
```

---

## MSW Mock File Pattern

```typescript
// horse-profile-drawer.mock.ts
import { http, HttpResponse, delay } from 'msw'
import type { HorseInterface, CreateHorseInput } from '@/lib/interfaces/horse'

// Mock data
const mockHorse: HorseInterface = {
  id: 1,
  name: 'Thunder',
  breed: 'Quarter Horse',
  status: 'active',
  birthDate: '2018-05-15',
  weight: 1100,
  color: 'Bay',
  notes: 'Excellent therapy horse',
  avatarUrl: '/horses/thunder.jpg',
  createdAt: new Date(),
  updatedAt: new Date(),
}

const mockBreeds = [
  { id: 1, name: 'Quarter Horse' },
  { id: 2, name: 'Appaloosa' },
  { id: 3, name: 'Arabian' },
  { id: 4, name: 'Paint' },
]

// Default handlers
export const handlers = [
  // Get horse by ID
  http.get('/api/horses/:id', async ({ params }) => {
    await delay(200)
    if (params.id === '999') {
      return HttpResponse.json({ error: 'Horse not found' }, { status: 404 })
    }
    return HttpResponse.json(mockHorse)
  }),

  // Get breeds
  http.get('/api/breeds', async () => {
    await delay(100)
    return HttpResponse.json(mockBreeds)
  }),

  // Create horse
  http.post('/api/horses', async ({ request }) => {
    await delay(300)
    const body = await request.json() as CreateHorseInput
    return HttpResponse.json({
      ...mockHorse,
      ...body,
      id: 2,
      createdAt: new Date(),
      updatedAt: new Date(),
    }, { status: 201 })
  }),

  // Update horse
  http.put('/api/horses/:id', async ({ request, params }) => {
    await delay(300)
    const body = await request.json()
    return HttpResponse.json({
      ...mockHorse,
      ...body,
      id: Number(params.id),
      updatedAt: new Date(),
    })
  }),

  // Delete horse
  http.delete('/api/horses/:id', async () => {
    await delay(200)
    return new HttpResponse(null, { status: 204 })
  }),
]

// Validation error handlers
export const validationErrorHandlers = [
  http.post('/api/horses', async () => {
    await delay(200)
    return HttpResponse.json({
      error: 'Validation failed',
      details: { name: 'Horse name already exists' },
    }, { status: 422 })
  }),
]

// Server error handlers
export const serverErrorHandlers = [
  http.post('/api/horses', async () => {
    await delay(200)
    return HttpResponse.json({ error: 'Internal server error' }, { status: 500 })
  }),
]

// Delete error (has active sessions)
export const deleteErrorHandlers = [
  ...handlers.filter(h => !h.info.path.includes('DELETE')),
  http.delete('/api/horses/:id', async () => {
    return HttpResponse.json({
      error: 'Cannot delete horse with active sessions',
    }, { status: 409 })
  }),
]
```

---

## Storybook Pattern

```typescript
// horse-profile-drawer.stories.tsx
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { fn } from 'storybook/test'
import { HorseProfileDrawer } from './horse-profile-drawer'
import {
  handlers,
  validationErrorHandlers,
  serverErrorHandlers,
  deleteErrorHandlers,
} from './horse-profile-drawer.mock'

const meta = {
  title: 'Horse/HorseProfileDrawer',
  component: HorseProfileDrawer,
  parameters: {
    layout: 'fullscreen',
  },
  tags: ['autodocs', 'tier-5', 'container', 'horse', 'drawer'],
  args: {
    open: true,
    onClose: fn(),
    onSuccess: fn(),
    onDelete: fn(),
  },
} satisfies Meta<typeof HorseProfileDrawer>

export default meta
type Story = StoryObj<typeof meta>

// Create mode
export const CreateMode: Story = {
  parameters: {
    msw: { handlers },
  },
}

// Edit mode
export const EditMode: Story = {
  args: {
    horseId: 1,
  },
  parameters: {
    msw: { handlers },
  },
}

// Edit mode loading
export const EditModeLoading: Story = {
  args: {
    horseId: 1,
  },
  parameters: {
    msw: {
      handlers: [
        http.get('/api/horses/:id', async () => {
          await delay('infinite')
          return HttpResponse.json({})
        }),
        ...handlers.filter(h => !h.info.path.includes('/api/horses/:id')),
      ],
    },
  },
}

// Validation error
export const ValidationError: Story = {
  parameters: {
    msw: { handlers: validationErrorHandlers },
  },
}

// Server error
export const ServerError: Story = {
  parameters: {
    msw: { handlers: serverErrorHandlers },
  },
}

// Delete error
export const DeleteError: Story = {
  args: {
    horseId: 1,
  },
  parameters: {
    msw: { handlers: deleteErrorHandlers },
  },
}

// Closed state
export const Closed: Story = {
  args: {
    open: false,
  },
}
```

---

## Checklist

- [ ] Full lifecycle documented (idle -> success/error)
- [ ] Form schema with all fields
- [ ] Validation rules for each field
- [ ] All server actions documented with error cases
- [ ] Create and edit modes supported
- [ ] Delete flow with confirmation
- [ ] MSW mock file with all endpoints
- [ ] Stories for all states and error conditions
- [ ] Focus trap and keyboard navigation
- [ ] Unsaved changes warning
- [ ] Success/error toast notifications

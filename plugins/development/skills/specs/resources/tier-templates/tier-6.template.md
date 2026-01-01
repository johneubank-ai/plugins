# Tier 6: Top-Level Layout/Page Template

Use this template for page-level components, layouts, and route handlers that orchestrate the full user experience.

---

## Template

```markdown
# ComponentName

## tier
6 - Top-Level Layout/Page

## purpose
[One sentence describing the page or layout's purpose]

## route
[Next.js route path]
- Path: `/horses` or `/horses/[id]`
- File: `app/horses/page.tsx`

## layout-hierarchy
[Document parent layouts]
- RootLayout (`app/layout.tsx`)
- AuthLayout (`app/(authenticated)/layout.tsx`)
- SidebarLayout (optional)

## composed-of
[List major components used]
- SidebarLayout from `@/components/sidebar-layout`
- PageHeader from `@/components/page-header`
- HorsesTable (Tier 4) from `@/components/horse/horses-table`
- HorseProfileDrawer (Tier 5) from `@/components/horse/horse-profile-drawer`

## domain
[Primary domain(s) this page serves]
- horse

## authentication
[Document auth requirements]
- Required: Yes
- Roles: admin, staff
- Redirect: /login if unauthenticated

## route-params
[Document URL parameters]
- `[id]`: Horse ID (number)
- Query: `?status=active&page=1`

## data-sources
[Document server-side data fetching]
- `getHorses()` - Server component data fetch
- `getHorseById(id)` - For detail pages

## page-sections
[Document major page sections]
1. **Header**: Page title, breadcrumbs, action buttons
2. **Filters**: Status filter, search
3. **Content**: HorsesTable
4. **Drawer**: HorseProfileDrawer (conditional)

## page-states
[Document page-level states]
- authenticated: Normal page render
- unauthenticated: Redirect to login
- unauthorized: Show 403 page
- loading: Page skeleton
- error: Error boundary fallback

## actions
[Document page-level actions]
- "Add Horse" button -> Opens HorseProfileDrawer (create)
- Row click -> Opens HorseProfileDrawer (edit)
- Filter change -> Updates URL and refetches

## navigation
[Document navigation patterns]
- Breadcrumb: Home > Horses
- Back button: None (top-level)
- Related pages: /horses/[id], /sessions

## seo
[Document SEO metadata]
- Title: "Horses | Facility Name"
- Description: "Manage therapy horses"
- OG Image: Default facility image

## error-boundary
[Document error handling]
- Fallback: "Something went wrong" with retry
- Report: Send to error tracking

## props
```typescript
// For page components (usually none - params come from route)
interface PageProps {
  params: { id?: string }
  searchParams: { status?: string; page?: string }
}

// For layout components
interface LayoutProps {
  children: React.ReactNode
}
```

## accessibility
- Skip link: "Skip to main content"
- Landmarks: header, main, nav
- Focus management: Restore on navigation
- Announcements: Page changes

## mock-file
`page-name.mock.ts` (if page fetches data client-side)

## storybook-tags
- autodocs
- tier-6
- top-level
- page
- [domain-name]
```

---

## Example: HorsesPage Component

```markdown
# HorsesPage

## tier
6 - Top-Level Layout/Page

## purpose
Main page for viewing and managing all therapy horses in the facility.

## route
- Path: `/horses`
- File: `app/horses/page.tsx`

## layout-hierarchy
- RootLayout (`app/layout.tsx`) - HTML shell, providers
- AuthLayout (`app/(authenticated)/layout.tsx`) - Auth check
- SidebarLayout - Navigation sidebar

## composed-of
- SidebarLayout from `@/components/sidebar-layout`
- PageHeader from `@/components/page-header`
- FilterBar from `@/components/filter-bar`
- HorsesTable (Tier 4) from `@/components/horse/horses-table`
- HorseProfileDrawer (Tier 5) from `@/components/horse/horse-profile-drawer`
- Button from `@/components/button`

## domain
horse

## authentication
- Required: Yes
- Roles: admin, staff, volunteer (read-only for volunteer)
- Redirect: /login if unauthenticated
- Permissions:
  - View: all authenticated users
  - Create: admin, staff
  - Edit: admin, staff
  - Delete: admin only

## route-params
- Query params:
  - `status`: 'active' | 'inactive' | 'all' (default: 'all')
  - `page`: number (default: 1)
  - `search`: string (default: '')

## data-sources
- Server-side: None (HorsesTable handles fetching)
- Initial state from URL query params

## page-sections
1. **Header**
   - Title: "Horses"
   - Breadcrumb: Home > Horses
   - Action: "Add Horse" button (admin/staff only)

2. **Filter Bar**
   - Status dropdown: All, Active, Inactive
   - Search input: Filter by name

3. **Content**
   - HorsesTable: Main data display
   - Handles pagination internally

4. **Drawer**
   - HorseProfileDrawer: Create/edit horse
   - Triggered by "Add Horse" or row click

## page-states
- authenticated: Show full page
- unauthenticated: Redirect to /login
- unauthorized: Show permission denied for restricted actions
- loading: SidebarLayout skeleton
- error: Error boundary with retry option

## actions
| Action | Trigger | Handler | Permission |
|--------|---------|---------|------------|
| Add Horse | Header button | Open drawer (create) | admin, staff |
| Edit Horse | Row click | Open drawer (edit) | admin, staff |
| Delete Horse | Drawer action | Confirm + delete | admin |
| Filter | Dropdown change | Update URL params | all |
| Search | Input change | Update URL params | all |
| Paginate | Pagination click | Update URL params | all |

## navigation
- Breadcrumb: Home > Horses
- Sidebar: Highlighted "Horses" item
- Related:
  - /horses/[id] - Horse detail page
  - /horses/new - Create form (alternative to drawer)

## seo
```typescript
export const metadata: Metadata = {
  title: 'Horses | Facility Name',
  description: 'View and manage therapy horses',
}
```

## error-boundary
- Component: HorsesPageError
- Fallback UI: "Unable to load horses page"
- Actions: Retry, Go to Dashboard
- Logging: Send to error tracking service

## props
```typescript
interface HorsesPageProps {
  searchParams: {
    status?: string
    page?: string
    search?: string
  }
}
```

## accessibility
- Skip link: Target #horses-table
- Landmarks:
  - header: Page header with title and actions
  - main: Primary content area
  - nav: Sidebar navigation
- Focus: Return to trigger after drawer closes
- Announcements:
  - "Horses page loaded"
  - "Filter updated, showing X horses"

## mock-file
Not required (data fetching handled by child Tier 4 component)

## storybook-tags
- autodocs
- tier-6
- top-level
- page
- horse
```

---

## Allowed Dependencies

```typescript
// Next.js
import { Metadata } from 'next'
import { redirect } from 'next/navigation'
import { Suspense } from 'react'

// Auth
import { getServerSession } from 'next-auth'
import { authOptions } from '@/lib/auth'

// All lower tier components
import { SidebarLayout } from '@/components/sidebar-layout'
import { PageHeader } from '@/components/page-header'
import { HorsesTable } from '@/components/horse/horses-table'
import { HorseProfileDrawer } from '@/components/horse/horse-profile-drawer'

// Server actions (for server components)
import { getHorses } from '@/lib/actions/horse-actions'

// URL state
import { useSearchParams, useRouter, usePathname } from 'next/navigation'
```

---

## Page Structure Pattern

```typescript
// app/horses/page.tsx
import { Metadata } from 'next'
import { Suspense } from 'react'
import { HorsesPageClient } from './horses-page-client'
import { HorsesTableSkeleton } from '@/components/horse/horses-table-skeleton'

export const metadata: Metadata = {
  title: 'Horses | Facility Name',
  description: 'View and manage therapy horses',
}

interface HorsesPageProps {
  searchParams: Promise<{
    status?: string
    page?: string
    search?: string
  }>
}

export default async function HorsesPage({ searchParams }: HorsesPageProps) {
  const params = await searchParams

  return (
    <Suspense fallback={<HorsesTableSkeleton />}>
      <HorsesPageClient
        initialStatus={params.status || 'all'}
        initialPage={Number(params.page) || 1}
        initialSearch={params.search || ''}
      />
    </Suspense>
  )
}
```

```typescript
// app/horses/horses-page-client.tsx
'use client'

import { useState, useCallback } from 'react'
import { useSearchParams, useRouter, usePathname } from 'next/navigation'
import { PageHeader } from '@/components/page-header'
import { HorsesTable } from '@/components/horse/horses-table'
import { HorseProfileDrawer } from '@/components/horse/horse-profile-drawer'
import { Button } from '@/components/button'
import { PlusIcon } from '@heroicons/react/16/solid'
import type { HorseInterface } from '@/lib/interfaces/horse'

interface HorsesPageClientProps {
  initialStatus: string
  initialPage: number
  initialSearch: string
}

export function HorsesPageClient({
  initialStatus,
  initialPage,
  initialSearch,
}: HorsesPageClientProps) {
  const router = useRouter()
  const pathname = usePathname()
  const searchParams = useSearchParams()

  // Drawer state
  const [drawerOpen, setDrawerOpen] = useState(false)
  const [editingHorseId, setEditingHorseId] = useState<number | undefined>()

  // URL state management
  const updateUrl = useCallback((updates: Record<string, string>) => {
    const params = new URLSearchParams(searchParams.toString())
    Object.entries(updates).forEach(([key, value]) => {
      if (value) {
        params.set(key, value)
      } else {
        params.delete(key)
      }
    })
    router.push(`${pathname}?${params.toString()}`)
  }, [searchParams, router, pathname])

  const handleAddHorse = () => {
    setEditingHorseId(undefined)
    setDrawerOpen(true)
  }

  const handleEditHorse = (horse: HorseInterface) => {
    setEditingHorseId(horse.id)
    setDrawerOpen(true)
  }

  const handleDrawerSuccess = () => {
    setDrawerOpen(false)
    router.refresh()
  }

  return (
    <>
      <PageHeader
        title="Horses"
        breadcrumbs={[
          { label: 'Home', href: '/' },
          { label: 'Horses', href: '/horses' },
        ]}
        actions={
          <Button onClick={handleAddHorse}>
            <PlusIcon className="size-4" />
            Add Horse
          </Button>
        }
      />

      <main id="horses-content" className="p-6">
        <HorsesTable
          status={initialStatus as 'active' | 'inactive' | 'all'}
          onHorseSelect={handleEditHorse}
        />
      </main>

      <HorseProfileDrawer
        open={drawerOpen}
        onClose={() => setDrawerOpen(false)}
        horseId={editingHorseId}
        onSuccess={handleDrawerSuccess}
      />
    </>
  )
}
```

---

## MSW Mock File (if needed)

```typescript
// horses-page.mock.ts
import { http, HttpResponse } from 'msw'

// Auth mock
export const authHandlers = [
  http.get('/api/auth/session', () => {
    return HttpResponse.json({
      user: {
        id: '1',
        name: 'Test Admin',
        email: 'admin@example.com',
        role: 'admin',
      },
    })
  }),
]

// Combine with child component mocks
import { handlers as horseTableHandlers } from '@/components/horse/horses-table.mock'
import { handlers as horseDrawerHandlers } from '@/components/horse/horse-profile-drawer.mock'

export const handlers = [
  ...authHandlers,
  ...horseTableHandlers,
  ...horseDrawerHandlers,
]

// Unauthenticated state
export const unauthenticatedHandlers = [
  http.get('/api/auth/session', () => {
    return HttpResponse.json(null)
  }),
]

// Unauthorized state (wrong role)
export const unauthorizedHandlers = [
  http.get('/api/auth/session', () => {
    return HttpResponse.json({
      user: {
        id: '2',
        name: 'Test Volunteer',
        email: 'volunteer@example.com',
        role: 'volunteer',
      },
    })
  }),
]
```

---

## Storybook Pattern

```typescript
// horses-page.stories.tsx
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { HorsesPageClient } from './horses-page-client'
import { handlers, unauthenticatedHandlers, unauthorizedHandlers } from './horses-page.mock'

const meta = {
  title: 'Pages/HorsesPage',
  component: HorsesPageClient,
  parameters: {
    layout: 'fullscreen',
    nextjs: {
      appDirectory: true,
      navigation: {
        pathname: '/horses',
        query: {},
      },
    },
  },
  tags: ['autodocs', 'tier-6', 'top-level', 'page', 'horse'],
} satisfies Meta<typeof HorsesPageClient>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  args: {
    initialStatus: 'all',
    initialPage: 1,
    initialSearch: '',
  },
  parameters: {
    msw: { handlers },
  },
}

export const FilteredByActive: Story = {
  args: {
    initialStatus: 'active',
    initialPage: 1,
    initialSearch: '',
  },
  parameters: {
    msw: { handlers },
    nextjs: {
      navigation: {
        pathname: '/horses',
        query: { status: 'active' },
      },
    },
  },
}

export const WithSearch: Story = {
  args: {
    initialStatus: 'all',
    initialPage: 1,
    initialSearch: 'Thunder',
  },
  parameters: {
    msw: { handlers },
  },
}

// Note: Auth states typically tested at layout level
```

---

## Checklist

- [ ] Route and file path documented
- [ ] Layout hierarchy documented
- [ ] Authentication requirements specified
- [ ] Role-based permissions defined
- [ ] URL parameters documented
- [ ] Page sections defined
- [ ] All user actions mapped
- [ ] Navigation patterns documented
- [ ] SEO metadata configured
- [ ] Error boundary implemented
- [ ] Accessibility landmarks defined
- [ ] Skip links implemented
- [ ] Focus management for modals/drawers
- [ ] Server/client component split optimized

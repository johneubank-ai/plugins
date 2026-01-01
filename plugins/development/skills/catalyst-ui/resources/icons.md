# Icon Usage Reference

## Overview

Catalyst UI components are designed to work with specific icon sizes from Heroicons. Using the correct size is critical for proper visual alignment.

---

## Icon Size Rules

### 16×16 Icons (Default)

**Source**: `@heroicons/react/16/solid`

**Used by**:
- `Button`
- `DropdownItem`
- `DropdownButton`
- `ListboxOption`
- `ComboboxOption`
- Form-related components

**Example**:
```tsx
import { Button } from '@/components/button'
import { PlusIcon, TrashIcon, PencilIcon } from '@heroicons/react/16/solid'

function Example() {
  return (
    <div className="flex gap-3">
      <Button>
        <PlusIcon />
        Add item
      </Button>
      <Button outline>
        <PencilIcon />
        Edit
      </Button>
      <Button plain>
        <TrashIcon />
      </Button>
    </div>
  )
}
```

### 20×20 Icons (Navigation)

**Source**: `@heroicons/react/20/solid`

**Used by**:
- `NavbarItem`
- `SidebarItem`

**Example**:
```tsx
import { SidebarItem, SidebarLabel } from '@/components/sidebar'
import { NavbarItem } from '@/components/navbar'
import { 
  HomeIcon, 
  Cog6ToothIcon, 
  UsersIcon,
  CalendarIcon 
} from '@heroicons/react/20/solid'

function SidebarExample() {
  return (
    <>
      <SidebarItem href="/">
        <HomeIcon />
        <SidebarLabel>Home</SidebarLabel>
      </SidebarItem>
      <SidebarItem href="/users">
        <UsersIcon />
        <SidebarLabel>Users</SidebarLabel>
      </SidebarItem>
      <SidebarItem href="/calendar">
        <CalendarIcon />
        <SidebarLabel>Calendar</SidebarLabel>
      </SidebarItem>
      <SidebarItem href="/settings">
        <Cog6ToothIcon />
        <SidebarLabel>Settings</SidebarLabel>
      </SidebarItem>
    </>
  )
}

function NavbarExample() {
  return (
    <>
      <NavbarItem href="/home">
        <HomeIcon />
      </NavbarItem>
      <NavbarItem href="/settings">
        <Cog6ToothIcon />
      </NavbarItem>
    </>
  )
}
```

---

## Common Icon Mappings

### Actions
| Action | Icon (16/solid) |
|--------|-----------------|
| Add/Create | `PlusIcon` |
| Edit | `PencilIcon` |
| Delete | `TrashIcon` |
| Save | `CheckIcon` |
| Cancel | `XMarkIcon` |
| Refresh | `ArrowPathIcon` |
| Download | `ArrowDownTrayIcon` |
| Upload | `ArrowUpTrayIcon` |
| Copy | `DocumentDuplicateIcon` |
| Share | `ShareIcon` |

### Navigation
| Context | Icon (20/solid) |
|---------|-----------------|
| Home | `HomeIcon` |
| Settings | `Cog6ToothIcon` or `Cog8ToothIcon` |
| Users | `UsersIcon` |
| User Profile | `UserIcon` |
| Calendar | `CalendarIcon` |
| Dashboard | `ChartBarIcon` |
| Search | `MagnifyingGlassIcon` |
| Inbox | `InboxIcon` |

### Status/Feedback
| Status | Icon (16/solid) |
|--------|-----------------|
| Success | `CheckCircleIcon` |
| Warning | `ExclamationTriangleIcon` |
| Error | `XCircleIcon` |
| Info | `InformationCircleIcon` |
| Loading | `ArrowPathIcon` (with animation) |

### UI Elements
| Element | Icon (16/solid) |
|---------|-----------------|
| Menu/More | `EllipsisHorizontalIcon` |
| Dropdown | `ChevronDownIcon` |
| Expand | `ChevronRightIcon` |
| External Link | `ArrowTopRightOnSquareIcon` |
| Close | `XMarkIcon` |

---

## Custom Icons

When using custom SVG icons, add the `data-slot="icon"` attribute so Catalyst applies correct styles:

```tsx
function CustomIcon(props: React.SVGProps<SVGSVGElement>) {
  return (
    <svg viewBox="0 0 16 16" fill="currentColor" {...props}>
      {/* SVG paths */}
    </svg>
  )
}

// Usage
<Button>
  <CustomIcon data-slot="icon" />
  Custom Action
</Button>
```

---

## Icon-Only Buttons

For icon-only buttons, always include `aria-label`:

```tsx
import { Button } from '@/components/button'
import { TrashIcon } from '@heroicons/react/16/solid'

// Plain style for minimal visual weight
<Button plain aria-label="Delete item">
  <TrashIcon />
</Button>

// Outline style for more prominence
<Button outline aria-label="Delete item">
  <TrashIcon />
</Button>
```

---

## Dropdown with Icons

```tsx
import {
  Dropdown,
  DropdownButton,
  DropdownMenu,
  DropdownItem,
  DropdownLabel,
  DropdownDivider,
} from '@/components/dropdown'
import {
  ChevronDownIcon,
  PencilIcon,
  DocumentDuplicateIcon,
  TrashIcon,
} from '@heroicons/react/16/solid'

function Example() {
  return (
    <Dropdown>
      <DropdownButton outline>
        Actions
        <ChevronDownIcon />
      </DropdownButton>
      <DropdownMenu>
        <DropdownItem href="/edit">
          <PencilIcon />
          <DropdownLabel>Edit</DropdownLabel>
        </DropdownItem>
        <DropdownItem onClick={() => duplicate()}>
          <DocumentDuplicateIcon />
          <DropdownLabel>Duplicate</DropdownLabel>
        </DropdownItem>
        <DropdownDivider />
        <DropdownItem onClick={() => remove()}>
          <TrashIcon />
          <DropdownLabel>Delete</DropdownLabel>
        </DropdownItem>
      </DropdownMenu>
    </Dropdown>
  )
}
```

---

## Common Mistakes

❌ **Wrong**: Using 20×20 icons in Button
```tsx
import { PlusIcon } from '@heroicons/react/20/solid' // WRONG SIZE
<Button><PlusIcon />Add</Button>
```

✅ **Correct**: Using 16×16 icons in Button
```tsx
import { PlusIcon } from '@heroicons/react/16/solid' // CORRECT
<Button><PlusIcon />Add</Button>
```

❌ **Wrong**: Using 16×16 icons in SidebarItem
```tsx
import { HomeIcon } from '@heroicons/react/16/solid' // WRONG SIZE
<SidebarItem><HomeIcon /><SidebarLabel>Home</SidebarLabel></SidebarItem>
```

✅ **Correct**: Using 20×20 icons in SidebarItem
```tsx
import { HomeIcon } from '@heroicons/react/20/solid' // CORRECT
<SidebarItem><HomeIcon /><SidebarLabel>Home</SidebarLabel></SidebarItem>
```

---

## Import Cheat Sheet

```tsx
// For Button, Dropdown, Listbox, form components
import {
  PlusIcon,
  PencilIcon,
  TrashIcon,
  CheckIcon,
  XMarkIcon,
  ChevronDownIcon,
  ChevronUpIcon,
  EllipsisHorizontalIcon,
} from '@heroicons/react/16/solid'

// For Navbar, Sidebar
import {
  HomeIcon,
  Cog6ToothIcon,
  Cog8ToothIcon,
  UsersIcon,
  UserIcon,
  CalendarIcon,
  InboxIcon,
  MagnifyingGlassIcon,
} from '@heroicons/react/20/solid'
```

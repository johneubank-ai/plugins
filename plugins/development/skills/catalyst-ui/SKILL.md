---
name: catalyst-ui
description: Build React components using Catalyst UI library with strict Tailwind default theme adherence. Use when creating new UI components, designing component specs (*.spec.md), or implementing any user interface elements. Enforces Catalyst-first approach with zero custom styling unless explicitly instructed.
---

# Catalyst UI Development Skill

## Purpose

Build consistent, high-quality React components using the Catalyst UI component library while strictly adhering to Tailwind's default theme configuration. This skill prioritizes reusing existing Catalyst components over creating custom solutions.

## When to Use This Skill

Automatically activates when:
- Creating new React UI components
- Writing component specification files (*.spec.md)
- Implementing forms, tables, dialogs, navigation, or any interactive UI
- Designing component behavior and states
- Reviewing or refactoring existing components

---

## Core Principles

### 1. Catalyst-First Development

**ALWAYS** use Catalyst UI components as the foundation. Never create custom implementations when a Catalyst component exists.

```tsx
// ✅ CORRECT: Use Catalyst components
import { Button } from '@/components/button'
import { Dialog, DialogTitle, DialogBody, DialogActions } from '@/components/dialog'
import { Table, TableHead, TableBody, TableRow, TableCell } from '@/components/table'

// ❌ WRONG: Custom implementations
<button className="bg-blue-500 ...">Click me</button>
<div className="modal ...">...</div>
```

### 2. Tailwind Default Theme Only

**CRITICAL CONSTRAINT**: Use ONLY Tailwind's default theme values. Do not invent custom values.

```tsx
// ✅ CORRECT: Default theme values
className="p-4 mt-6 gap-3 text-sm text-zinc-500"
className="rounded-lg shadow-md"
className="text-red-500 bg-emerald-100"

// ❌ WRONG: Custom/arbitrary values
className="p-[13px] mt-[22px] gap-[7px]"
className="text-[#3B82F6] bg-[rgba(0,0,0,0.05)]"
className="rounded-[10px] shadow-[0_2px_8px_rgba(0,0,0,0.1)]"
```

**Allowed Spacing Scale**: 0, 0.5, 1, 1.5, 2, 2.5, 3, 3.5, 4, 5, 6, 7, 8, 9, 10, 11, 12, 14, 16, 20, 24, 28, 32, 36, 40, 44, 48, 52, 56, 60, 64, 72, 80, 96

**Allowed Colors**: slate, gray, zinc, neutral, stone, red, orange, amber, yellow, lime, green, emerald, teal, cyan, sky, blue, indigo, violet, purple, fuchsia, pink, rose

**Allowed Font Sizes**: xs, sm, base, lg, xl, 2xl, 3xl, 4xl, 5xl, 6xl, 7xl, 8xl, 9xl

### 3. Zero Styling Freedom

LLMs should NOT:
- Invent new color values
- Create arbitrary spacing values
- Define custom shadows
- Add custom border radii
- Use CSS-in-JS or inline styles for layout

If the default theme doesn't have what you need, **ask the user** rather than improvising.

---

## Icon Usage

Catalyst components are designed for specific icon sizes. Follow these rules exactly:

### Standard Components (16×16 icons)

Most Catalyst components use 16×16 icons from `@heroicons/react/16/solid`:

```tsx
import { Button } from '@/components/button'
import { PlusIcon } from '@heroicons/react/16/solid'

function Example() {
  return (
    <Button>
      <PlusIcon />
      Add item
    </Button>
  )
}
```

**Components using 16×16 icons:**
- Button
- DropdownItem
- ListboxOption
- All form-related components

### Navigation Components (20×20 icons)

NavbarItem and SidebarItem use 20×20 icons from `@heroicons/react/20/solid`:

```tsx
import { SidebarItem, SidebarLabel } from '@/components/sidebar'
import { HomeIcon } from '@heroicons/react/20/solid'

function Example() {
  return (
    <SidebarItem href="/home">
      <HomeIcon />
      <SidebarLabel>Home</SidebarLabel>
    </SidebarItem>
  )
}
```

**Components using 20×20 icons:**
- NavbarItem
- SidebarItem

### Custom Icons

If using custom icons, add the `data-slot="icon"` attribute:

```tsx
<Button>
  <MyCustomIcon data-slot="icon" />
  Custom action
</Button>
```

---

## Component Design Specifications

When designing a new component, create a `*.spec.md` file using this template:

```markdown
# ComponentName

## Purpose
<one-line description of what this component does>

## Where Does This Appear
- ParentComponent: <placement description>
- AnotherParent: <placement description>

## Behaviors
- <behavior 1>
- <behavior 2>

## States
- default: <description>
- hover: <description>
- disabled: <description>
- loading: <description>
- error: <description>

## Data Displayed
- $Model.property1
- $Model.property2
- $RelatedModel.property

## Actions / Events
- onClick: <description>
- onSubmit: <description>
- onChange: <description>

## Validation & Constraints
- <validation rule 1>
- <constraint 1>

## Size
- width: <Tailwind class or "content-based">
- height: <Tailwind class or "auto">
- padding: <Tailwind spacing value>
- responsive: <breakpoint behaviors>

## Accessibility
- aria-label: <description>
- keyboard navigation: <description>
- focus management: <description>
```

### Design Spec Example

```markdown
# TherapistSelector

## Purpose
Dropdown selector for choosing a therapist for a therapy session.

## Where Does This Appear
- TherapySessionForm: In the resource assignment section
- QuickScheduleDialog: Primary selection field

## Behaviors
- Filters therapists by availability for selected date/time
- Shows therapist specializations as badges
- Displays workload indicator

## States
- default: Shows placeholder "Select therapist..."
- open: Displays filtered list with search
- selected: Shows therapist name with avatar
- disabled: Grayed out when no date selected
- loading: Skeleton while fetching therapists

## Data Displayed
- $Therapist.name
- $Therapist.avatarUrl
- $Therapist.specializations[]
- $Therapist.currentWorkload

## Actions / Events
- onChange(therapistId): Fires when selection changes
- onClear: Fires when selection is cleared

## Validation & Constraints
- Required field when session type is "individual"
- Must be available for selected time slot

## Size
- width: w-full
- height: auto (content-based)
- padding: inherits from Select component
- responsive: full-width on all breakpoints

## Accessibility
- aria-label: "Select therapist"
- keyboard: Arrow keys to navigate, Enter to select
- focus: Trap focus when open
```

---

## Available Catalyst Components

Reference documentation files in `docs/catalyst-ui/` for detailed usage:

### Form Components
- [Input](../../docs/catalyst-ui/components/input.md) - Text inputs
- [Textarea](../../docs/catalyst-ui/components/textarea.md) - Multi-line text
- [Select](../../docs/catalyst-ui/components/select.md) - Native select dropdowns
- [Listbox](../../docs/catalyst-ui/components/listbox.md) - Custom styled selects
- [Combobox](../../docs/catalyst-ui/components/combobox.md) - Autocomplete inputs
- [Checkbox](../../docs/catalyst-ui/components/checkbox.md) - Checkboxes
- [Radio](../../docs/catalyst-ui/components/radio-button.md) - Radio buttons
- [Switch](../../docs/catalyst-ui/components/switch.md) - Toggle switches
- [Fieldset](../../docs/catalyst-ui/components/fieldset.md) - Form grouping

### Display Components
- [Table](../../docs/catalyst-ui/components/table.md) - Data tables
- [Badge](../../docs/catalyst-ui/components/badge.md) - Status indicators
- [Avatar](../../docs/catalyst-ui/components/avatar.md) - User/entity avatars
- [Description List](../../docs/catalyst-ui/components/description-list.md) - Key-value displays

### Interactive Components
- [Button](../../docs/catalyst-ui/components/button.md) - Actions and links
- [Dropdown](../../docs/catalyst-ui/components/dropdown.md) - Dropdown menus
- [Dialog](../../docs/catalyst-ui/components/dialog.md) - Modal dialogs
- [Alert](../../docs/catalyst-ui/components/alert.md) - Alert messages

### Navigation Components
- [Navbar](../../docs/catalyst-ui/components/navbar.md) - Top navigation
- [Sidebar](../../docs/catalyst-ui/components/sidebar.md) - Side navigation
- [Pagination](../../docs/catalyst-ui/components/pagination.md) - Page navigation

### Typography
- [Heading](../../docs/catalyst-ui/components/heading.md) - Page/section headings
- [Text](../../docs/catalyst-ui/components/text.md) - Body text
- [Divider](../../docs/catalyst-ui/components/divider.md) - Visual separators

### Layouts
- [Sidebar Layout](../../docs/catalyst-ui/layouts/sidebar-layout.md) - App shell with sidebar
- [Stacked Layout](../../docs/catalyst-ui/layouts/stacked-layout.md) - Stacked page layout
- [Auth Layout](../../docs/catalyst-ui/layouts/auth-layout.md) - Authentication pages

---

## Import Patterns

### Standard Component Imports

```tsx
// Always import from @/components/
import { Button } from '@/components/button'
import { Dialog, DialogTitle, DialogBody, DialogActions } from '@/components/dialog'
import { Table, TableHead, TableBody, TableRow, TableHeader, TableCell } from '@/components/table'
import { Field, Label, Description, FieldGroup, Fieldset, Legend } from '@/components/fieldset'
import { Input } from '@/components/input'
import { Select } from '@/components/select'
```

### Icon Imports

```tsx
// 16×16 icons for most components
import { PlusIcon, TrashIcon, PencilIcon } from '@heroicons/react/16/solid'

// 20×20 icons for Navbar/Sidebar
import { HomeIcon, Cog6ToothIcon } from '@heroicons/react/20/solid'
```

---

## Common Patterns

### Form with Fieldset

```tsx
import { Button } from '@/components/button'
import { Field, FieldGroup, Fieldset, Label, Legend, Description } from '@/components/fieldset'
import { Input } from '@/components/input'
import { Select } from '@/components/select'

function ExampleForm() {
  return (
    <form>
      <Fieldset>
        <Legend>Patient Details</Legend>
        <FieldGroup>
          <Field>
            <Label>Full Name</Label>
            <Input name="name" />
          </Field>
          <Field>
            <Label>Status</Label>
            <Select name="status">
              <option value="active">Active</option>
              <option value="inactive">Inactive</option>
            </Select>
            <Description>Current enrollment status</Description>
          </Field>
        </FieldGroup>
      </Fieldset>
      <div className="mt-6 flex gap-3">
        <Button type="submit">Save</Button>
        <Button plain type="button">Cancel</Button>
      </div>
    </form>
  )
}
```

### Table with Actions

```tsx
import { Table, TableHead, TableBody, TableRow, TableHeader, TableCell } from '@/components/table'
import { Dropdown, DropdownButton, DropdownMenu, DropdownItem } from '@/components/dropdown'
import { EllipsisHorizontalIcon } from '@heroicons/react/16/solid'

function ExampleTable({ items }) {
  return (
    <Table>
      <TableHead>
        <TableRow>
          <TableHeader>Name</TableHeader>
          <TableHeader>Status</TableHeader>
          <TableHeader className="relative w-0">
            <span className="sr-only">Actions</span>
          </TableHeader>
        </TableRow>
      </TableHead>
      <TableBody>
        {items.map((item) => (
          <TableRow key={item.id}>
            <TableCell className="font-medium">{item.name}</TableCell>
            <TableCell className="text-zinc-500">{item.status}</TableCell>
            <TableCell>
              <div className="-mx-3 -my-1.5">
                <Dropdown>
                  <DropdownButton plain aria-label="More options">
                    <EllipsisHorizontalIcon />
                  </DropdownButton>
                  <DropdownMenu anchor="bottom end">
                    <DropdownItem href={`/items/${item.id}`}>View</DropdownItem>
                    <DropdownItem href={`/items/${item.id}/edit`}>Edit</DropdownItem>
                    <DropdownItem>Delete</DropdownItem>
                  </DropdownMenu>
                </Dropdown>
              </div>
            </TableCell>
          </TableRow>
        ))}
      </TableBody>
    </Table>
  )
}
```

### Dialog with Form

```tsx
import { Button } from '@/components/button'
import { Dialog, DialogTitle, DialogDescription, DialogBody, DialogActions } from '@/components/dialog'
import { Field, Label } from '@/components/fieldset'
import { Input } from '@/components/input'
import { useState } from 'react'

function ExampleDialog() {
  const [isOpen, setIsOpen] = useState(false)
  
  return (
    <>
      <Button onClick={() => setIsOpen(true)}>Open Dialog</Button>
      <Dialog open={isOpen} onClose={setIsOpen}>
        <DialogTitle>Add Item</DialogTitle>
        <DialogDescription>Enter the details for the new item.</DialogDescription>
        <DialogBody>
          <Field>
            <Label>Name</Label>
            <Input name="name" autoFocus />
          </Field>
        </DialogBody>
        <DialogActions>
          <Button plain onClick={() => setIsOpen(false)}>Cancel</Button>
          <Button onClick={() => setIsOpen(false)}>Save</Button>
        </DialogActions>
      </Dialog>
    </>
  )
}
```

---

## Resource Files

### [icons.md](resources/icons.md)
Complete icon usage reference with import patterns and size guidelines.

### [design-spec-template.md](resources/design-spec-template.md)
Full component specification template with examples.

### [styling-constraints.md](resources/styling-constraints.md)
Detailed Tailwind default theme reference and constraints.

---

## Anti-Patterns to Avoid

❌ Creating custom button styles when `Button` exists
❌ Using arbitrary Tailwind values like `p-[13px]`
❌ Defining custom colors like `text-[#3B82F6]`
❌ Building custom modals instead of using `Dialog`
❌ Creating custom dropdowns instead of using `Dropdown` or `Listbox`
❌ Using wrong icon sizes (20×20 in Button, 16×16 in Sidebar)
❌ Inline styles for layout or spacing
❌ Custom form layouts without `Fieldset` and `FieldGroup`

---

## Quick Reference

| Need | Use |
|------|-----|
| Button/Link | `Button` with href for links |
| Form inputs | `Input`, `Select`, `Textarea`, etc. |
| Form layout | `Fieldset`, `FieldGroup`, `Field` |
| Modal | `Dialog` with `DialogTitle`, `DialogBody`, `DialogActions` |
| Dropdown menu | `Dropdown` with `DropdownMenu`, `DropdownItem` |
| Custom select | `Listbox` or `Combobox` |
| Data table | `Table` with related components |
| Status indicator | `Badge` |
| User display | `Avatar` |
| Page heading | `Heading` |
| Body text | `Text` |

---

**Skill Status**: COMPLETE ✅
**Styling Mode**: STRICT (Tailwind defaults only)
**Component Priority**: CATALYST-FIRST

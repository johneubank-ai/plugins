---
name: specs
description: Create and manage component specification files using tier-based templates. Use when creating spec files, deriving specs from existing components, validating spec-to-code alignment, or generating Storybook stories. Triggers on keywords like spec, specification, tier, stories, msw, derive spec, review spec, validate spec.
---

# Component Specification Skill

## Purpose

Create, manage, and validate component specification files using a tier-based system. This skill ensures consistent documentation of component behavior, data requirements, and Storybook integration patterns.

## When to Use This Skill

Automatically activates when:
- Creating new component specification files (*.spec.md)
- Deriving specs from existing components
- Validating spec-to-code alignment
- Generating Storybook stories from specs
- Discussing component tiers or classification
- Working with MSW mocking patterns
- Creating or updating component directories

---

## Core Principles

### 1. Tier-Based Template Selection

Components are classified into tiers (0-6) based on their dependencies and responsibilities. Always select the appropriate tier template.

### 2. Styling Integration

**ALWAYS** use the `catalyst-ui` skill for component styling. This skill handles specification structure only.

### 3. Mocking Strategy by Tier

- **Tier 0-3**: Use Storybook loaders for mock data
- **Tier 4-6**: Use MSW (Mock Service Worker) for API mocking

### 4. Component Directory Structure

All components follow this directory pattern:

```
/components/my-component/
├── my-component.spec.md      # Specification file
├── my-component.tsx          # React component
├── my-component.stories.tsx  # Storybook stories
├── my-component.mock.ts      # Mock data (Tier 4+ only)
└── index.ts                  # Barrel export
```

---

## Quick Reference: Component Tiers

| Tier | Name | Dependencies | Data Strategy | Location |
|------|------|--------------|---------------|----------|
| 0 | Library Primitives | @headlessui, clsx only | Loaders | /components |
| 1 | Leaf Primitives | Tier 0, @heroicons | Loaders | /components |
| 2 | Domain Primitives | Tier 0-2, interfaces | Loaders | /components/{domain} |
| 3 | Simple Composites | Tier 0-3, interfaces | Loaders | /components/{domain} |
| 4 | Data-Driven | Actions, services, contexts | MSW | /components/{domain} |
| 5 | Container | Full access, forms, dialogs | MSW | /components/{domain} |
| 6 | Top-Level | Next.js routing, auth | MSW | /app or /components |

**Key Rule**: Components may only import from their tier or lower tiers.

---

## Workflow: Creating a New Spec

### Step 1: Determine the Tier

Analyze what the component needs to import:

```typescript
// Tier 0: Only these imports
import { Button } from '@headlessui/react'
import clsx from 'clsx'

// Tier 2: Adds domain interfaces
import type { HorseInterface } from '@/lib/interfaces/horse'

// Tier 4: Adds actions/services
import { getHorses } from '@/lib/actions/horse'
```

### Step 2: Select Template

Choose the appropriate template from `resources/tier-templates/`:
- `tier-0.template.md` - Library primitives
- `tier-1.template.md` - Leaf primitives
- `tier-2.template.md` - Domain primitives
- `tier-3.template.md` - Simple composites
- `tier-4.template.md` - Data-driven composites
- `tier-5.template.md` - Container composites
- `tier-6.template.md` - Top-level layouts/pages

### Step 3: Fill Template Sections

All specs include these universal sections:
- `# component-name`
- `## tier` - Explicit tier classification
- `## purpose` - One-line description
- `## where-does-this-appear` - Parent components/pages
- `## props` - TypeScript interface
- `## states` - Visual/behavioral states
- `## size` - Tailwind default classes only
- `## accessibility` - ARIA, keyboard, focus
- `## storybook-tags` - Array for filtering

### Step 4: Create Component Directory

```bash
mkdir -p components/my-component
touch components/my-component/my-component.spec.md
touch components/my-component/my-component.tsx
touch components/my-component/my-component.stories.tsx
touch components/my-component/index.ts
# For Tier 4+:
touch components/my-component/my-component.mock.ts
```

---

## Spec Section Reference

### Universal Sections (All Tiers)

```markdown
# ComponentName

## tier
N - Tier Name

## purpose
Single sentence describing what this component does.

## where-does-this-appear
- ParentComponent: Placement description
- AnotherPage: Context description

## props
```typescript
interface ComponentNameProps {
  prop1: string
  prop2?: number
  onAction?: () => void
}
```

## states
- default: Normal appearance
- hover: Visual feedback on hover
- focus: Focus ring styling
- disabled: Grayed out, non-interactive
- loading: Skeleton or spinner
- error: Error styling

## size
- width: w-full or w-fit
- height: h-auto
- padding: p-4

## accessibility
- aria-label: Descriptive label
- keyboard: Tab to focus, Enter to activate
- focus: Visible focus ring

## storybook-tags
- autodocs
- tier-N
```

### Tier-Specific Sections

See resource files for tier-specific sections:
- [Tier Definitions](resources/tier-definitions.md)
- [Tier 0-3 Templates](resources/tier-templates/)
- [Tier 4-6 Templates](resources/tier-templates/)

---

## Storybook Integration

### Story File Format (CSF Next)

**IMPORTANT**: Always use CSF Next format, not CSF3.

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { ComponentName } from './component-name'

const meta = {
  title: 'Domain/ComponentName',
  component: ComponentName,
  parameters: { layout: 'centered' },
  tags: ['autodocs', 'tier-2'],
} satisfies Meta<typeof ComponentName>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  args: { /* props */ }
}
```

### Mocking Patterns

**Tier 0-3 (Loaders)**:
```typescript
export const WithData: Story = {
  loaders: [
    async () => ({
      data: { /* mock data */ }
    })
  ],
  render: (args, { loaded }) => (
    <Component {...args} data={loaded.data} />
  )
}
```

**Tier 4-6 (MSW)**:
```typescript
import { handlers } from './component-name.mock'

export const Default: Story = {
  parameters: {
    msw: { handlers }
  }
}
```

---

## Workflow: Deriving Specs from Existing Components

When an existing component lacks a spec file, follow this process to derive one.

### Step 1: Analyze Component Imports

Read the component file and analyze its imports to determine the tier:

```typescript
// Example: Analyzing imports
import { Button } from '@/components/button'        // Tier 1 component
import type { Horse } from '@/lib/interfaces/horse' // Domain interface = Tier 2+
import { getHorses } from '@/lib/actions/horse'     // Server action = Tier 4+
```

**Decision Tree**:
1. Uses server actions, fetch, or contexts? → Tier 4+
2. Uses form handling or dialogs? → Tier 5
3. Uses Next.js routing or auth? → Tier 6
4. Uses domain interfaces only? → Tier 2-3
5. Uses only Tier 0-1 components? → Tier 1
6. Uses only @headlessui/clsx? → Tier 0

### Step 2: Identify Props Interface

Extract the props from the component:

```typescript
// From the component
interface HorseCardProps {
  horse: HorseInterface
  onEdit?: () => void
  compact?: boolean
}
```

Document each prop with its purpose and any default values.

### Step 3: Map Component States

Identify all visual/behavioral states the component can exhibit:

- Check for conditional rendering
- Look for loading/error states
- Identify interactive states (hover, focus, disabled)
- Note any empty state handling

### Step 4: Document Data Flow

For Tier 4+ components:
- List all API endpoints called
- Document expected request/response shapes
- Note any caching or revalidation patterns

### Step 5: Generate Spec File

Use the appropriate tier template and fill in all discovered information.

### Step 6: Create Mock File (Tier 4+)

If the component makes API calls, create the corresponding `.mock.ts` file with MSW handlers.

---

## Workflow: Reviewing and Validating Specs

### When to Validate

Run validation when:
- A new spec is created
- Component code changes significantly
- Before generating stories from a spec
- During code review

### Validation Checklist

#### 1. Tier Accuracy Check

```
□ Tier matches actual imports in component
□ Component doesn't import from higher tiers
□ Golden Rule not violated (import tier ≤ component tier)
```

#### 2. Props Completeness Check

```
□ All props in component are documented in spec
□ Types match between spec and component
□ Default values are accurate
□ Optional props marked correctly
```

#### 3. States Coverage Check

```
□ All conditional renders have corresponding states
□ Loading state documented if component fetches data
□ Error state documented if component handles errors
□ Empty state documented if component handles empty data
```

#### 4. Storybook Alignment Check

```
□ All documented states have corresponding stories
□ Story tags match spec storybook-tags
□ Mocking strategy matches tier (loaders vs MSW)
□ Args in stories match props in spec
```

#### 5. Accessibility Check

```
□ All interactive elements have keyboard handling documented
□ ARIA attributes documented where needed
□ Focus management documented for complex interactions
□ Screen reader considerations noted
```

### Common Issues and Fixes

| Issue | Detection | Fix |
|-------|-----------|-----|
| Tier mismatch | Component imports higher-tier modules | Update tier or refactor imports |
| Missing state | Conditional render without spec state | Add state to spec |
| Props drift | Spec props differ from component | Update spec to match component |
| Wrong mocking | Tier 4+ using loaders | Convert to MSW handlers |
| Missing stories | Spec states without stories | Generate missing stories |

### Review Output Format

After validation, report:

```markdown
## Spec Review: ComponentName

**Tier**: ✓ Correct (Tier 2)
**Props**: ✓ All documented (5/5)
**States**: ⚠ Missing 1 state (loading)
**Stories**: ✓ All states covered
**Accessibility**: ✓ Complete

### Action Items:
1. Add `loading` state to spec
2. Create `Loading` story variant
```

---

## Example Components

### Tier 2 Example: StatusBadge

Location: `components/examples/status-badge/`

Files:
- `status-badge.spec.md` - Specification
- `status-badge.tsx` - Component
- `status-badge.stories.tsx` - Stories with loaders
- `index.ts` - Barrel export

### Tier 4 Example: HorseListSummary

Location: `components/examples/horse-list-summary/`

Files:
- `horse-list-summary.spec.md` - Specification
- `horse-list-summary.tsx` - Component
- `horse-list-summary.stories.tsx` - Stories with MSW
- `horse-list-summary.mock.ts` - MSW handlers
- `index.ts` - Barrel export

---

## Resource Files

### Templates
- [tier-0.template.md](resources/tier-templates/tier-0.template.md) - Library primitives
- [tier-1.template.md](resources/tier-templates/tier-1.template.md) - Leaf primitives
- [tier-2.template.md](resources/tier-templates/tier-2.template.md) - Domain primitives
- [tier-3.template.md](resources/tier-templates/tier-3.template.md) - Simple composites
- [tier-4.template.md](resources/tier-templates/tier-4.template.md) - Data-driven composites
- [tier-5.template.md](resources/tier-templates/tier-5.template.md) - Container composites
- [tier-6.template.md](resources/tier-templates/tier-6.template.md) - Top-level layouts

### Patterns & Guides
- [tier-definitions.md](resources/tier-definitions.md) - Complete tier hierarchy
- [storybook-patterns.md](resources/storybook-patterns.md) - Story generation patterns
- [msw-patterns.md](resources/msw-patterns.md) - MSW mocking (Tier 4+)
- [loader-patterns.md](resources/loader-patterns.md) - Loader patterns (Tier 0-3)
- [validation-checklist.md](resources/validation-checklist.md) - Spec validation rules

---

## Related Skills

- **catalyst-ui** - Component styling and Tailwind defaults (always use for styling)

---

**Skill Status**: ACTIVE
**Mocking Strategy**: TIER-BASED (Loaders for 0-3, MSW for 4-6)
**Story Format**: CSF Next (NOT CSF3)

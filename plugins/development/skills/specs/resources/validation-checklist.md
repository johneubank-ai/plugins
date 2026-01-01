# Spec Validation Checklist

This document provides checklists for validating component specifications and ensuring spec-to-code alignment.

---

## Universal Validation (All Tiers)

### Spec File Structure

- [ ] File named `{component-name}.spec.md`
- [ ] Located in component directory
- [ ] All universal sections present:
  - [ ] `# ComponentName` (h1 heading)
  - [ ] `## tier`
  - [ ] `## purpose`
  - [ ] `## where-does-this-appear`
  - [ ] `## props`
  - [ ] `## states`
  - [ ] `## size`
  - [ ] `## accessibility`
  - [ ] `## storybook-tags`

### Tier Classification

- [ ] Tier explicitly stated (0-6)
- [ ] Tier matches import patterns in code
- [ ] No imports from higher tiers

### Props Interface

- [ ] Props documented in TypeScript interface format
- [ ] All props in code are documented
- [ ] All documented props exist in code
- [ ] Types match between spec and code
- [ ] Optional props marked with `?`
- [ ] Default values documented

### States

- [ ] All visual states documented
- [ ] States match implemented behavior
- [ ] State descriptions are clear

### Accessibility

- [ ] ARIA attributes documented
- [ ] Keyboard interactions documented
- [ ] Focus management documented
- [ ] Screen reader considerations noted

---

## Tier 0: Library Primitive Validation

### Allowed Dependencies Check

```typescript
// ONLY these are allowed:
import { Component } from '@headlessui/react'
import clsx from 'clsx'
import { OtherTier0 } from '@/components/other'
```

### Forbidden Dependencies Check

- [ ] No `@/lib/interfaces/*` imports
- [ ] No `@/lib/actions/*` imports
- [ ] No `@/lib/services/*` imports
- [ ] No `@/contexts/*` imports
- [ ] No `useEffect` for data fetching
- [ ] No domain types

### Spec Sections

- [ ] `## catalyst-source` documented
- [ ] `## variants` (if applicable)

---

## Tier 1: Leaf Primitive Validation

### Allowed Dependencies Check

```typescript
// Tier 0 + these are allowed:
import { Icon } from '@heroicons/react/16/solid'
```

### Forbidden Dependencies Check

- [ ] No domain interfaces
- [ ] No server actions
- [ ] No data fetching
- [ ] No business logic

### Spec Sections

- [ ] `## composed-of` lists Tier 0 components
- [ ] `## variants` (if applicable)

---

## Tier 2: Domain Primitive Validation

### Allowed Dependencies Check

```typescript
// Tier 0-1 + types only:
import type { HorseInterface } from '@/lib/interfaces/horse'
```

### Forbidden Dependencies Check

- [ ] Interface imports are `type` only
- [ ] No interface functions imported
- [ ] No server actions
- [ ] No data fetching
- [ ] No mutations

### Spec Sections

- [ ] `## domain` specified
- [ ] `## domain-types` lists imported types
- [ ] `## data-displayed` uses `$Model.property` notation
- [ ] `## composed-of` lists Tier 0-1 components

---

## Tier 3: Simple Composite Validation

### Allowed Dependencies Check

```typescript
// Tier 0-2 + local state:
import { useState, useCallback } from 'react'
```

### Forbidden Dependencies Check

- [ ] No server actions
- [ ] No `useEffect` for data fetching
- [ ] No context providers (except UI-only)
- [ ] No direct API calls

### Spec Sections

- [ ] `## callbacks` documents all event handlers
- [ ] `## local-ui-state` documents internal state
- [ ] Callback signatures match code

---

## Tier 4: Data-Driven Validation

### Allowed Dependencies Check

```typescript
// Full access to actions and services:
import { getEntities } from '@/lib/actions/entity-actions'
import { useEntityContext } from '@/contexts/entity-context'
import { useState, useEffect, useCallback } from 'react'
```

### Required Files

- [ ] `{component}.tsx` exists
- [ ] `{component}.stories.tsx` exists
- [ ] `{component}.mock.ts` exists (MSW handlers)
- [ ] `{component}.spec.md` exists

### Spec Sections

- [ ] `## data-sources` documents all fetch operations
- [ ] `## server-actions` documents all mutations
- [ ] `## states` includes loading, error, empty
- [ ] `## loading-state` describes loading behavior
- [ ] `## error-state` describes error handling
- [ ] `## empty-state` describes empty state
- [ ] `## mock-file` references mock file

### MSW Mock File Validation

- [ ] Exports `handlers` (default set)
- [ ] Exports `loadingHandlers`
- [ ] Exports `errorHandlers`
- [ ] Exports `emptyHandlers`
- [ ] Mock data matches interface types

### Storybook Validation

- [ ] Uses MSW (not loaders)
- [ ] Has Default story
- [ ] Has Loading story
- [ ] Has Error story
- [ ] Has Empty story

---

## Tier 5: Container Validation

### Required Files

- [ ] `{component}.tsx` exists
- [ ] `{component}.stories.tsx` exists
- [ ] `{component}.mock.ts` exists
- [ ] `{component}.spec.md` exists

### Spec Sections

- [ ] `## form-schema` documents all fields
- [ ] `## validation-rules` for each field
- [ ] `## lifecycle-states` documents full lifecycle
- [ ] `## form-sections` (for complex forms)
- [ ] All CRUD actions documented

### Form Validation

- [ ] All form fields have validation
- [ ] Error states handled
- [ ] Success states handled
- [ ] Loading states handled
- [ ] Unsaved changes warning documented

### MSW Mock File Validation

- [ ] Handlers for all CRUD operations
- [ ] Validation error handlers
- [ ] Server error handlers
- [ ] Delete error handlers (if applicable)

---

## Tier 6: Top-Level Validation

### Spec Sections

- [ ] `## route` documents Next.js path
- [ ] `## layout-hierarchy` documents parent layouts
- [ ] `## authentication` requirements
- [ ] `## route-params` (URL parameters)
- [ ] `## page-sections` documents major sections
- [ ] `## page-states` documents auth states
- [ ] `## navigation` patterns
- [ ] `## seo` metadata
- [ ] `## error-boundary` handling

### Auth Validation

- [ ] Auth requirements match middleware
- [ ] Role-based access documented
- [ ] Redirect behavior documented

---

## Storybook Story Validation

### Universal Checks

- [ ] Uses CSF Next format (not CSF3)
- [ ] Has `satisfies Meta<typeof Component>`
- [ ] Title follows convention (`Domain/ComponentName`)
- [ ] Tags include `autodocs` and `tier-N`
- [ ] Has Default story
- [ ] Uses `fn()` for callback props

### Tier 0-3 (Loaders)

- [ ] Uses `loaders` array
- [ ] Uses `render` function with `{ loaded }`
- [ ] Mock data matches interface

### Tier 4-6 (MSW)

- [ ] Imports handlers from `.mock.ts`
- [ ] Uses `parameters: { msw: { handlers } }`
- [ ] Has Loading story
- [ ] Has Error story
- [ ] Has Empty story

---

## Code-to-Spec Alignment

### Props Alignment

```typescript
// Spec:
interface Props {
  name: string
  status?: 'active' | 'inactive'
  onEdit?: (id: number) => void
}

// Code must match exactly
```

- [ ] All spec props exist in code
- [ ] All code props exist in spec
- [ ] Types match exactly
- [ ] Optional markers match

### State Alignment

- [ ] All documented states are implemented
- [ ] No undocumented states in code
- [ ] State names match

### Callback Alignment

- [ ] All spec callbacks exist in code
- [ ] Signatures match exactly
- [ ] All code callbacks documented

---

## Quick Validation Commands

### Check Tier by Imports

```bash
# Find potential tier violations
grep -l "@/lib/actions" components/tier-2-component/*.tsx
grep -l "@/lib/services" components/tier-3-component/*.tsx
```

### Check for Missing Mock Files

```bash
# Tier 4+ components should have .mock.ts
find components -name "*.tsx" -path "*tier-4*" | while read f; do
  mock="${f%.tsx}.mock.ts"
  [ ! -f "$mock" ] && echo "Missing: $mock"
done
```

### Check for Missing Specs

```bash
# All components should have .spec.md
find components -name "*.tsx" -not -name "*.stories.tsx" | while read f; do
  spec="${f%.tsx}.spec.md"
  [ ! -f "$spec" ] && echo "Missing: $spec"
done
```

---

## Validation Workflow

1. **Before Writing Code**
   - Create spec file using tier template
   - Define props, states, callbacks
   - Define data sources (Tier 4+)

2. **During Development**
   - Update spec as implementation evolves
   - Keep props aligned
   - Document discovered states

3. **Before Commit**
   - Run tier validation checklist
   - Verify spec-code alignment
   - Ensure stories exist and pass

4. **Code Review**
   - Spec matches implementation
   - Tier classification is correct
   - All required files present
   - Stories cover all states

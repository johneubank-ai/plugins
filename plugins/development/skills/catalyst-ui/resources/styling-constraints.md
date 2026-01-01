# Tailwind Default Theme Constraints

## Overview

Catalyst UI is built around Tailwind's default theme configuration. This document defines the **only** allowed values for styling. Using arbitrary values (like `p-[13px]`) or custom colors (like `text-[#3B82F6]`) is **strictly prohibited**.

---

## Spacing Scale

Use these values for margin, padding, gap, width, height, and positioning.

### Standard Scale

| Class | Value |
|-------|-------|
| 0 | 0px |
| px | 1px |
| 0.5 | 2px (0.125rem) |
| 1 | 4px (0.25rem) |
| 1.5 | 6px (0.375rem) |
| 2 | 8px (0.5rem) |
| 2.5 | 10px (0.625rem) |
| 3 | 12px (0.75rem) |
| 3.5 | 14px (0.875rem) |
| 4 | 16px (1rem) |
| 5 | 20px (1.25rem) |
| 6 | 24px (1.5rem) |
| 7 | 28px (1.75rem) |
| 8 | 32px (2rem) |
| 9 | 36px (2.25rem) |
| 10 | 40px (2.5rem) |
| 11 | 44px (2.75rem) |
| 12 | 48px (3rem) |
| 14 | 56px (3.5rem) |
| 16 | 64px (4rem) |
| 20 | 80px (5rem) |
| 24 | 96px (6rem) |
| 28 | 112px (7rem) |
| 32 | 128px (8rem) |
| 36 | 144px (9rem) |
| 40 | 160px (10rem) |
| 44 | 176px (11rem) |
| 48 | 192px (12rem) |
| 52 | 208px (13rem) |
| 56 | 224px (14rem) |
| 60 | 240px (15rem) |
| 64 | 256px (16rem) |
| 72 | 288px (18rem) |
| 80 | 320px (20rem) |
| 96 | 384px (24rem) |

### Usage Examples

```tsx
// ✅ CORRECT
className="p-4 mt-6 gap-3 mx-auto"
className="w-64 h-12 max-w-md"
className="px-3 py-2 mb-8"

// ❌ WRONG - Arbitrary values
className="p-[13px] mt-[22px] gap-[7px]"
className="w-[250px] h-[45px]"
```

---

## Colors

### Allowed Color Palettes

Each color has shades: 50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950

| Color | Primary Use |
|-------|-------------|
| slate | Neutral with blue undertone |
| gray | Pure neutral |
| zinc | Neutral with slight warmth (Catalyst default) |
| neutral | True neutral |
| stone | Warm neutral |
| red | Error, danger, destructive |
| orange | Warning, caution |
| amber | Warning, attention |
| yellow | Highlight, caution |
| lime | Success (vibrant) |
| green | Success, positive |
| emerald | Success, positive |
| teal | Info, accent |
| cyan | Info, accent |
| sky | Info, links |
| blue | Primary, links |
| indigo | Primary accent |
| violet | Accent |
| purple | Accent |
| fuchsia | Accent |
| pink | Accent |
| rose | Accent, emphasis |

### Catalyst Color Conventions

```tsx
// Primary text
className="text-zinc-950 dark:text-white"

// Secondary text
className="text-zinc-500 dark:text-zinc-400"

// Muted text
className="text-zinc-400 dark:text-zinc-500"

// Borders
className="border-zinc-200 dark:border-zinc-700"

// Backgrounds
className="bg-white dark:bg-zinc-900"
className="bg-zinc-50 dark:bg-zinc-800"
```

### Usage Examples

```tsx
// ✅ CORRECT
className="text-zinc-500 bg-red-100 border-emerald-500"
className="hover:bg-zinc-100 dark:hover:bg-zinc-800"

// ❌ WRONG - Custom colors
className="text-[#64748b] bg-[rgba(239,68,68,0.1)]"
className="border-[#10b981]"
```

---

## Typography

### Font Sizes

| Class | Size | Line Height |
|-------|------|-------------|
| text-xs | 12px (0.75rem) | 16px (1rem) |
| text-sm | 14px (0.875rem) | 20px (1.25rem) |
| text-base | 16px (1rem) | 24px (1.5rem) |
| text-lg | 18px (1.125rem) | 28px (1.75rem) |
| text-xl | 20px (1.25rem) | 28px (1.75rem) |
| text-2xl | 24px (1.5rem) | 32px (2rem) |
| text-3xl | 30px (1.875rem) | 36px (2.25rem) |
| text-4xl | 36px (2.25rem) | 40px (2.5rem) |
| text-5xl | 48px (3rem) | 48px (1) |
| text-6xl | 60px (3.75rem) | 60px (1) |
| text-7xl | 72px (4.5rem) | 72px (1) |
| text-8xl | 96px (6rem) | 96px (1) |
| text-9xl | 128px (8rem) | 128px (1) |

### Font Weights

| Class | Weight |
|-------|--------|
| font-thin | 100 |
| font-extralight | 200 |
| font-light | 300 |
| font-normal | 400 |
| font-medium | 500 |
| font-semibold | 600 |
| font-bold | 700 |
| font-extrabold | 800 |
| font-black | 900 |

### Catalyst Typography Conventions

```tsx
// Page heading
className="text-2xl font-semibold"

// Section heading
className="text-lg font-semibold"

// Body text
className="text-sm" // or use <Text> component

// Small/caption text
className="text-xs text-zinc-500"

// Labels
className="text-sm font-medium"
```

---

## Border Radius

| Class | Value |
|-------|-------|
| rounded-none | 0 |
| rounded-sm | 2px (0.125rem) |
| rounded | 4px (0.25rem) |
| rounded-md | 6px (0.375rem) |
| rounded-lg | 8px (0.5rem) |
| rounded-xl | 12px (0.75rem) |
| rounded-2xl | 16px (1rem) |
| rounded-3xl | 24px (1.5rem) |
| rounded-full | 9999px |

### Usage Examples

```tsx
// ✅ CORRECT
className="rounded-lg" // Standard cards/buttons
className="rounded-xl" // Modals, larger elements
className="rounded-full" // Avatars, pills

// ❌ WRONG
className="rounded-[10px]"
className="rounded-[0.625rem]"
```

---

## Shadows

| Class | Usage |
|-------|-------|
| shadow-sm | Subtle elevation |
| shadow | Standard card shadow |
| shadow-md | Medium elevation |
| shadow-lg | High elevation |
| shadow-xl | Very high elevation |
| shadow-2xl | Maximum elevation |
| shadow-inner | Inset shadow |
| shadow-none | Remove shadow |

### Catalyst Shadow Usage

```tsx
// Card shadows
className="shadow-sm" // Subtle cards
className="shadow" // Standard cards
className="shadow-lg" // Elevated cards, dropdowns

// ❌ WRONG
className="shadow-[0_2px_8px_rgba(0,0,0,0.1)]"
```

---

## Width & Height

### Fixed Widths

| Class | Value |
|-------|-------|
| w-0 | 0 |
| w-px | 1px |
| w-0.5 to w-96 | (same as spacing scale) |
| w-auto | auto |
| w-1/2, w-1/3, etc. | Fractional |
| w-full | 100% |
| w-screen | 100vw |
| w-min | min-content |
| w-max | max-content |
| w-fit | fit-content |

### Max Widths

| Class | Value |
|-------|-------|
| max-w-xs | 320px (20rem) |
| max-w-sm | 384px (24rem) |
| max-w-md | 448px (28rem) |
| max-w-lg | 512px (32rem) |
| max-w-xl | 576px (36rem) |
| max-w-2xl | 672px (42rem) |
| max-w-3xl | 768px (48rem) |
| max-w-4xl | 896px (56rem) |
| max-w-5xl | 1024px (64rem) |
| max-w-6xl | 1152px (72rem) |
| max-w-7xl | 1280px (80rem) |
| max-w-full | 100% |
| max-w-prose | 65ch |

---

## Breakpoints

| Prefix | Minimum Width |
|--------|---------------|
| sm | 640px |
| md | 768px |
| lg | 1024px |
| xl | 1280px |
| 2xl | 1536px |

### Usage

```tsx
className="w-full sm:w-1/2 lg:w-1/3"
className="p-4 sm:p-6 lg:p-8"
className="hidden md:block"
```

---

## Summary of Prohibited Patterns

### ❌ Never Use

```tsx
// Arbitrary values
className="p-[13px] mt-[22px] w-[250px]"
className="rounded-[10px] gap-[7px]"

// Custom colors
className="text-[#3B82F6] bg-[rgba(0,0,0,0.05)]"
className="border-[#e5e7eb]"

// Custom shadows
className="shadow-[0_2px_8px_rgba(0,0,0,0.1)]"

// Inline styles for layout
style={{ padding: '13px', marginTop: '22px' }}

// CSS-in-JS for spacing
const styles = { gap: '7px' }
```

### ✅ Always Use

```tsx
// Default spacing
className="p-3 mt-6 w-64"
className="rounded-lg gap-2"

// Default colors
className="text-blue-500 bg-zinc-50"
className="border-zinc-200"

// Default shadows
className="shadow-lg"

// Responsive patterns
className="p-4 sm:p-6 lg:p-8"
```

---

## When You Need Something Not in Defaults

If the default theme doesn't provide what you need:

1. **Ask the user** for clarification on requirements
2. **Suggest alternatives** using default values
3. **Document the constraint** in the component spec
4. **Never improvise** with arbitrary values

The goal is consistent, maintainable UI that leverages Tailwind's carefully designed defaults.

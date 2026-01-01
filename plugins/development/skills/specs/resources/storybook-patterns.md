# Storybook Patterns

This document defines the standard patterns for writing Storybook stories in this codebase.

---

## Story Format: CSF Next

**IMPORTANT**: Always use CSF Next format, not CSF3.

```typescript
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { ComponentName } from './component-name'

const meta = {
  title: 'Category/ComponentName',
  component: ComponentName,
  parameters: { layout: 'centered' },
  tags: ['autodocs', 'tier-N'],
} satisfies Meta<typeof ComponentName>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  args: {
    // Component props
  },
}
```

---

## Story Organization

### Title Convention

Use a hierarchical title that reflects the component's domain and purpose:

| Tier | Title Pattern | Example |
|------|---------------|---------|
| 0-1 | `Primitives/{ComponentName}` | `Primitives/Badge` |
| 2-3 | `{Domain}/{ComponentName}` | `Horse/HorseAvatar` |
| 4 | `{Domain}/{ComponentName}` | `Horse/HorsesTable` |
| 5 | `{Domain}/{ComponentName}` | `Horse/HorseProfileDrawer` |
| 6 | `Pages/{PageName}` | `Pages/HorsesPage` |

### Tags Convention

Always include appropriate tags for filtering:

```typescript
tags: [
  'autodocs',           // Enable auto-generated docs
  'tier-N',             // Tier classification (tier-0 through tier-6)
  'domain-name',        // Domain (horse, client, therapist, session)
  'component-type',     // Type (table, drawer, form, card, etc.)
]
```

---

## Layout Options

Choose the appropriate layout for your component:

```typescript
// Centered - for small, self-contained components
parameters: { layout: 'centered' }

// Padded - for components that need some breathing room
parameters: { layout: 'padded' }

// Fullscreen - for pages, layouts, and large components
parameters: { layout: 'fullscreen' }
```

---

## Args and ArgTypes

### Basic Args

```typescript
export const Default: Story = {
  args: {
    label: 'Click me',
    variant: 'primary',
    disabled: false,
  },
}
```

### ArgTypes for Controls

```typescript
const meta = {
  // ...
  argTypes: {
    variant: {
      control: 'select',
      options: ['primary', 'secondary', 'danger'],
      description: 'Visual style variant',
    },
    size: {
      control: 'radio',
      options: ['sm', 'md', 'lg'],
    },
    disabled: {
      control: 'boolean',
    },
    onClick: {
      action: 'clicked',  // Log to actions panel
    },
  },
} satisfies Meta<typeof Component>
```

---

## Action Spies with `fn()`

Use `fn()` from `storybook/test` for callback props:

```typescript
import { fn } from 'storybook/test'

const meta = {
  // ...
  args: {
    onClick: fn(),
    onSubmit: fn(),
    onChange: fn(),
  },
} satisfies Meta<typeof Component>
```

This enables:
- Logging in Actions panel
- Assertions in play functions
- Interaction testing

---

## Story Variants

### Common Stories to Include

Every component should have these stories when applicable:

```typescript
// 1. Default state
export const Default: Story = {
  args: { /* default props */ },
}

// 2. All variants (if component has variants)
export const AllVariants: Story = {
  render: () => (
    <div className="flex gap-4">
      <Component variant="primary" />
      <Component variant="secondary" />
      <Component variant="danger" />
    </div>
  ),
}

// 3. All sizes (if component has sizes)
export const AllSizes: Story = {
  render: () => (
    <div className="flex items-center gap-4">
      <Component size="sm" />
      <Component size="md" />
      <Component size="lg" />
    </div>
  ),
}

// 4. States
export const Disabled: Story = {
  args: { disabled: true },
}

export const Loading: Story = {
  args: { loading: true },
}

// 5. Edge cases
export const WithLongText: Story = {
  args: { label: 'This is a very long label that might wrap or truncate' },
}

export const Empty: Story = {
  args: { items: [] },
}
```

---

## Data Mocking by Tier

### Tier 0-3: Use Loaders

```typescript
export const WithData: Story = {
  loaders: [
    async () => ({
      horse: {
        id: 1,
        name: 'Thunder',
        avatarUrl: '/horses/thunder.jpg',
        status: 'active',
      },
    }),
  ],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}
```

### Tier 4-6: Use MSW

```typescript
import { handlers } from './component-name.mock'

export const Default: Story = {
  parameters: {
    msw: { handlers },
  },
}

export const Loading: Story = {
  parameters: {
    msw: { handlers: loadingHandlers },
  },
}

export const Error: Story = {
  parameters: {
    msw: { handlers: errorHandlers },
  },
}
```

---

## Play Functions (Interaction Testing)

Use play functions to test component interactions:

```typescript
import { expect, userEvent, within } from 'storybook/test'

export const ClickInteraction: Story = {
  args: {
    onClick: fn(),
  },
  play: async ({ args, canvasElement }) => {
    const canvas = within(canvasElement)

    // Find and click the button
    const button = canvas.getByRole('button')
    await userEvent.click(button)

    // Verify the callback was called
    await expect(args.onClick).toHaveBeenCalledTimes(1)
  },
}

export const FormInteraction: Story = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement)

    // Fill out form
    await userEvent.type(canvas.getByLabelText('Name'), 'Thunder')
    await userEvent.selectOptions(canvas.getByLabelText('Status'), 'active')

    // Submit
    await userEvent.click(canvas.getByRole('button', { name: /submit/i }))

    // Verify result
    await expect(canvas.getByText('Saved successfully')).toBeInTheDocument()
  },
}
```

---

## Decorators

### Adding Context Providers

```typescript
const meta = {
  // ...
  decorators: [
    (Story) => (
      <ThemeProvider>
        <Story />
      </ThemeProvider>
    ),
  ],
} satisfies Meta<typeof Component>
```

### Wrapper Styling

```typescript
const meta = {
  decorators: [
    (Story) => (
      <div className="p-8 bg-gray-100 dark:bg-gray-900">
        <Story />
      </div>
    ),
  ],
}
```

### Per-Story Decorators

```typescript
export const InDarkMode: Story = {
  decorators: [
    (Story) => (
      <div className="dark bg-gray-900 p-8">
        <Story />
      </div>
    ),
  ],
}
```

---

## Next.js Integration

### Router Mocking

```typescript
const meta = {
  parameters: {
    nextjs: {
      appDirectory: true,
      navigation: {
        pathname: '/horses',
        query: { status: 'active' },
      },
    },
  },
}
```

### Image Component

Next.js `Image` component works automatically with `@storybook/nextjs-vite`.

---

## Dark Mode Stories

Test components in dark mode:

```typescript
export const DarkMode: Story = {
  parameters: {
    backgrounds: { default: 'dark' },
  },
  decorators: [
    (Story) => (
      <div className="dark">
        <Story />
      </div>
    ),
  ],
}
```

---

## Responsive Stories

Test at different viewport sizes:

```typescript
export const Mobile: Story = {
  parameters: {
    viewport: { defaultViewport: 'mobile1' },
  },
}

export const Tablet: Story = {
  parameters: {
    viewport: { defaultViewport: 'tablet' },
  },
}
```

---

## Documentation

### Adding Component Description

```typescript
const meta = {
  title: 'Horse/HorseAvatar',
  component: HorseAvatar,
  parameters: {
    docs: {
      description: {
        component: 'Displays a horse\'s avatar with optional name and status indicator.',
      },
    },
  },
}
```

### Story-Level Documentation

```typescript
export const WithStatus: Story = {
  args: { showStatus: true },
  parameters: {
    docs: {
      description: {
        story: 'Shows the horse\'s current status as a colored ring around the avatar.',
      },
    },
  },
}
```

---

## Complete Example

```typescript
// horse-avatar.stories.tsx
import type { Meta, StoryObj } from '@storybook/nextjs-vite'
import { fn, expect, userEvent, within } from 'storybook/test'
import { HorseAvatar } from './horse-avatar'
import type { HorseInterface } from '@/lib/interfaces/horse'

const mockHorse: HorseInterface = {
  id: 1,
  name: 'Thunder',
  avatarUrl: '/horses/thunder.jpg',
  status: 'active',
  breed: 'Quarter Horse',
}

const meta = {
  title: 'Horse/HorseAvatar',
  component: HorseAvatar,
  parameters: {
    layout: 'centered',
    docs: {
      description: {
        component: 'Displays a horse\'s avatar with optional name and status.',
      },
    },
  },
  tags: ['autodocs', 'tier-2', 'horse', 'avatar'],
  argTypes: {
    size: {
      control: 'radio',
      options: ['sm', 'md', 'lg'],
    },
    showName: { control: 'boolean' },
    showStatus: { control: 'boolean' },
  },
  args: {
    onClick: fn(),
  },
} satisfies Meta<typeof HorseAvatar>

export default meta
type Story = StoryObj<typeof meta>

export const Default: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const WithName: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} showName />
  ),
}

export const AllSizes: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <div className="flex items-center gap-4">
      <HorseAvatar horse={loaded.horse} size="sm" />
      <HorseAvatar horse={loaded.horse} size="md" />
      <HorseAvatar horse={loaded.horse} size="lg" />
    </div>
  ),
}

export const NoImage: Story = {
  loaders: [async () => ({ horse: { ...mockHorse, avatarUrl: null } })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} />
  ),
}

export const InactiveStatus: Story = {
  loaders: [async () => ({ horse: { ...mockHorse, status: 'inactive' } })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} showStatus />
  ),
}

export const Clickable: Story = {
  loaders: [async () => ({ horse: mockHorse })],
  render: (args, { loaded }) => (
    <HorseAvatar {...args} horse={loaded.horse} onClick={args.onClick} />
  ),
  play: async ({ args, canvasElement }) => {
    const canvas = within(canvasElement)
    await userEvent.click(canvas.getByRole('button'))
    await expect(args.onClick).toHaveBeenCalledTimes(1)
  },
}
```

---

## Checklist for Stories

- [ ] Uses CSF Next format (`satisfies Meta<typeof Component>`)
- [ ] Has appropriate title in hierarchy
- [ ] Includes relevant tags (autodocs, tier, domain)
- [ ] Has Default story
- [ ] Shows all variants/sizes if applicable
- [ ] Uses loaders (Tier 0-3) or MSW (Tier 4-6)
- [ ] Has Loading/Error/Empty stories for async components
- [ ] Uses `fn()` for callback props
- [ ] Has play function for interactive components
- [ ] Works in dark mode

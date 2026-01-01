# Component Design Specification Template

## Overview

This template defines the standard format for component specification files (`*.spec.md`). These specs serve as the blueprint for implementing new components and should be created before writing any code.

---

## Template

```markdown
# ComponentName

## Purpose
<one clear sentence describing what this component does and why it exists>

## Where Does This Appear
<list components and pages where this appears, with placement details>
- ParentComponent: <exact placement within parent>
- AnotherParent: <exact placement within parent>

## Behaviors
<list all interactive behaviors>
- <behavior description>
- <behavior description>

## States
<list all visual/functional states>
- default: <description>
- hover: <description>
- active/pressed: <description>
- focus: <description>
- disabled: <description>
- loading: <description>
- error: <description>
- empty: <description>

## Data Displayed
<list all data properties in $Model.property format>
- $Model.propertyName
- $Model.nestedObject.property
- $RelatedModel.property

## Actions / Events
<list all callbacks and events>
- onClick: <what happens>
- onChange(value): <what happens>
- onSubmit: <what happens>

## Validation & Constraints
<list business rules and constraints>
- <validation rule>
- <constraint>

## Size
<specify dimensions using Tailwind defaults only>
- width: <Tailwind class, e.g., "w-full", "w-64", "content-based">
- height: <Tailwind class, e.g., "h-auto", "h-12">
- padding: <Tailwind spacing, e.g., "p-4", "px-3 py-2">
- responsive: <breakpoint behaviors>

## Accessibility
<list accessibility requirements>
- aria-label: <description>
- keyboard: <navigation description>
- focus: <focus management>
- screen reader: <announcement behavior>
```

---

## Full Example: TherapistSelector

```markdown
# TherapistSelector

## Purpose
Dropdown selector for choosing a therapist when scheduling a therapy session.

## Where Does This Appear
- TherapySessionForm: In the "Resource Assignment" section, below date/time picker
- QuickScheduleDialog: Primary field in the scheduling form
- SessionDetailsDrawer: Read-only display with edit capability

## Behaviors
- Opens dropdown on click or Enter key
- Filters therapists by name as user types
- Shows only available therapists for selected date/time when date is set
- Displays therapist avatar, name, and specializations in options
- Shows workload indicator (color-coded bar) in each option
- Clears selection when X button clicked
- Closes on selection, Escape, or click outside

## States
- default: Shows placeholder "Select a therapist..."
- open: Displays scrollable list with search input focused
- searching: Filters visible options based on input
- selected: Shows selected therapist with avatar and name
- disabled: Grayed out, non-interactive (when no date selected)
- loading: Shows skeleton placeholders while fetching therapists
- error: Red border, error message below
- empty: Shows "No therapists available" when filter returns nothing

## Data Displayed
- $Therapist.id
- $Therapist.name
- $Therapist.avatarUrl
- $Therapist.specializations[] (as badges)
- $Therapist.currentWorkload (as progress bar)
- $Therapist.isAvailable (for filtering)

## Actions / Events
- onChange(therapistId: string | null): Fires when selection changes or clears
- onFocus: Fires when selector gains focus
- onBlur: Fires when selector loses focus

## Validation & Constraints
- Required when session type is "individual" or "group"
- Optional when session type is "assessment"
- Selected therapist must be available for the chosen date/time
- Cannot select therapist on leave or inactive status

## Size
- width: w-full
- height: h-10 (button), max-h-60 (dropdown)
- padding: Inherits from Listbox component
- responsive: Full-width on all breakpoints

## Accessibility
- aria-label: "Select therapist"
- aria-required: true/false based on session type
- aria-invalid: true when validation fails
- keyboard: Arrow keys navigate options, Enter selects, Escape closes
- focus: Visible focus ring, focus trapped in dropdown when open
- screen reader: Announces "Therapist selector, required" on focus
```

---

## Full Example: SessionCard

```markdown
# SessionCard

## Purpose
Displays a therapy session summary in calendar views and lists with quick actions.

## Where Does This Appear
- CalendarView: Positioned within time grid cells
- TodayAgenda: Stacked vertically in agenda list
- TherapistScheduleGlance: Compact version in preview panel

## Behaviors
- Clickable to open SessionDetailsDrawer
- Shows color based on session status (scheduled, in-progress, completed)
- Expands on hover to show additional info (time, patient name)
- Displays conflict indicator if scheduling conflicts exist
- Drag-and-drop enabled in CalendarView for rescheduling

## States
- default: Standard card with time and patient name
- hover: Slight elevation, shows full details
- selected: Blue border, elevated shadow
- dragging: Reduced opacity, cursor grabbing
- conflict: Red left border, warning icon
- past: Reduced opacity, no hover effects
- cancelled: Strikethrough text, gray background

## Data Displayed
- $TherapySession.id
- $TherapySession.startTime (formatted as "HH:mm")
- $TherapySession.duration (displayed as height or text)
- $TherapySession.status
- $Patient.name
- $Patient.avatarUrl
- $Therapist.name
- $Horse.name (if assigned)
- $TherapySession.hasConflicts

## Actions / Events
- onClick: Opens SessionDetailsDrawer
- onDragStart: Initiates drag operation (CalendarView only)
- onDragEnd(newTime): Triggers reschedule confirmation

## Validation & Constraints
- Card height proportional to session duration in time grid
- Minimum visible width of 80px
- Truncates patient name if exceeds available space

## Size
- width: Dynamic (fills grid cell) or w-full in list
- height: Calculated from duration in grid, h-auto in list
- padding: p-2
- responsive: sm:p-3 for larger screens

## Accessibility
- role: button
- aria-label: "{Patient name} session at {time}"
- keyboard: Enter/Space opens drawer, Tab navigates between cards
- focus: Visible focus ring
- screen reader: Announces session summary and status
```

---

## Spec Checklist

Before submitting a component spec, verify:

- [ ] Purpose is a single, clear sentence
- [ ] All parent components listed with exact placement
- [ ] All interactive behaviors documented
- [ ] All visual states enumerated
- [ ] Data properties use $Model.property format
- [ ] All events/callbacks documented with parameters
- [ ] Validation rules are specific and testable
- [ ] Sizes use Tailwind default values only
- [ ] Accessibility requirements are complete
- [ ] No custom/arbitrary values in size specifications

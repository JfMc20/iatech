# Design Token Usage Guidelines

## Overview

This document outlines the proper usage of design tokens in the IATECH frontend application to ensure consistency across all components and prevent hardcoded color usage.

## Color System

### Primary Colors
Use these for main actions, links, and brand elements:
- `bg-primary` - Primary background color
- `text-primary` - Primary text color
- `text-primary-foreground` - Text on primary backgrounds
- `border-primary` - Primary border color

### Secondary Colors
Use these for secondary actions and subtle elements:
- `bg-secondary` - Secondary background color (light gray)
- `text-secondary-foreground` - Text on secondary backgrounds
- `border-secondary` - Secondary border color

### Text Colors
- `text-foreground` - Primary text color (replaces `text-gray-900`)
- `text-muted-foreground` - Muted text color (replaces `text-gray-600`, `text-gray-500`)
- `text-card-foreground` - Text on card backgrounds

### Background Colors
- `bg-background` - Main background color
- `bg-card` - Card background color (replaces `bg-white`)
- `bg-muted` - Muted background color
- `bg-secondary` - Secondary background (replaces `bg-gray-50`, `bg-gray-100`)

### Border Colors
- `border-border` - Default border color
- `border-input` - Input field borders (replaces `border-gray-300`)
- `border-muted` - Muted borders

### State Colors
- `bg-destructive` - Error/danger backgrounds
- `text-destructive` - Error/danger text
- `bg-accent` - Accent backgrounds
- `text-accent-foreground` - Text on accent backgrounds

## ❌ NEVER USE These Hardcoded Colors

### Prohibited Blue Colors
- `bg-blue-600` → Use `bg-primary`
- `text-blue-600` → Use `text-primary`
- `from-blue-50` → Use `from-primary-50` or gradient utilities

### Prohibited Gray Colors
- `text-gray-600` → Use `text-muted-foreground`
- `text-gray-900` → Use `text-foreground`
- `bg-gray-100` → Use `bg-secondary`
- `bg-gray-50` → Use `bg-secondary`
- `border-gray-300` → Use `border-input`

## ✅ Correct Usage Examples

### Before (❌ Wrong)
```tsx
<div className="bg-gray-50 text-gray-900 border-gray-300">
  <button className="bg-blue-600 text-white">
    Click me
  </button>
  <p className="text-gray-600">Subtitle text</p>
</div>
```

### After (✅ Correct)
```tsx
<div className="bg-secondary text-foreground border-input">
  <button className="bg-primary text-primary-foreground">
    Click me
  </button>
  <p className="text-muted-foreground">Subtitle text</p>
</div>
```

## Component-Specific Guidelines

### Cards
```tsx
// ✅ Correct
<Card className="bg-card border-border">
  <CardHeader>
    <CardTitle className="text-foreground">Title</CardTitle>
    <CardDescription className="text-muted-foreground">Description</CardDescription>
  </CardHeader>
</Card>
```

### Forms
```tsx
// ✅ Correct
<Input className="border-input focus:border-primary" />
<Label className="text-foreground">Label</Label>
<ErrorMessage className="text-destructive" />
```

### Buttons
```tsx
// ✅ Correct - Use Button component variants
<Button variant="default">Primary Action</Button>
<Button variant="secondary">Secondary Action</Button>
<Button variant="outline">Outline Action</Button>
```

## RGB Variables for Effects

For animations and effects that require rgba values:
- `rgba(var(--color-primary-rgb), 0.1)` - Primary with opacity
- `rgba(var(--color-secondary-rgb), 0.1)` - Secondary with opacity
- `rgba(var(--color-accent-rgb), 0.1)` - Accent with opacity

## Gradient Utilities

Use predefined gradient utilities instead of hardcoded gradients:
- `.gradient-primary` - Primary gradient
- `.gradient-secondary` - Secondary gradient
- `.gradient-accent` - Accent gradient

## Dark Mode Support

All theme colors automatically support dark mode. Never use hardcoded colors that don't adapt to theme changes.

## Component Development Checklist

Before marking any component as complete, verify:

1. ✅ No hardcoded colors (blue-600, gray-500, etc.)
2. ✅ Uses theme variables (primary, secondary, muted-foreground, etc.)
3. ✅ Consistent spacing using established scale
4. ✅ Proper component composition (Card, Button, Input variants)
5. ✅ Consistent hover/focus states using theme colors
6. ✅ Proper loading and error states with theme colors
7. ✅ Mobile responsiveness with established breakpoints
8. ✅ Accessibility compliance (ARIA, keyboard navigation)
9. ✅ Animation consistency using established classes

## Error Message Patterns

Use the standardized ErrorMessage component:

```tsx
import { ErrorMessage } from '@/components/common';

// ✅ Correct
<ErrorMessage 
  message="This field is required" 
  variant="destructive"
  size="sm"
  showIcon={true}
/>
```

## Loading State Patterns

Use the standardized LoadingSpinner component:

```tsx
import { LoadingSpinner } from '@/components/common';

// ✅ Correct
<LoadingSpinner size="md" className="text-primary" />
```

## Questions?

If you're unsure about which color token to use, refer to this hierarchy:

1. **Text**: `text-foreground` > `text-muted-foreground` > `text-card-foreground`
2. **Backgrounds**: `bg-background` > `bg-card` > `bg-secondary` > `bg-muted`
3. **Borders**: `border-border` > `border-input` > `border-muted`
4. **Interactive**: `bg-primary` > `bg-secondary` > `bg-accent`

Remember: Consistency is key to a professional, maintainable design system!
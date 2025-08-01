# IATECH Frontend Design System (✅ IMPLEMENTADO)

## 🎨 Color Palette (COMPLETO)

### Primary Colors (Blue) - ✅ IMPLEMENTADO
- `--color-primary-50` to `--color-primary-950` - Escala completa definida
- `--color-primary-rgb` - Variable RGB para efectos con opacidad
- Use `bg-primary`, `text-primary`, `border-primary` for consistency
- ❌ NUNCA usar valores hardcoded como `bg-blue-600`

### Secondary Colors (Gray/Slate) - ✅ IMPLEMENTADO
- `--color-secondary-50` to `--color-secondary-950` - Escala completa
- `--color-secondary-rgb` - Variable RGB para efectos
- Use for backgrounds, borders, and muted text

### Semantic Colors - ✅ IMPLEMENTADO
- `--color-destructive` - Errores y acciones peligrosas
- `--color-accent` - Highlights y elementos especiales
- `--color-accent-rgb` - Variable RGB para efectos
- Soporte automático para modo oscuro

## 📝 Typography (✅ IMPLEMENTADO)

### Font Families - ✅ CONFIGURADO
- **Primary**: Geist Sans (`--font-geist-sans`) - Configurado en globals.css
- **Monospace**: Geist Mono (`--font-geist-mono`) - Para código y elementos técnicos

### Text Hierarchy - ✅ IMPLEMENTADO
```css
/* ✅ Use semantic classes */
.text-foreground      /* Primary text - Reemplaza text-gray-900 */
.text-muted-foreground /* Secondary text - Reemplaza text-gray-600 */
.text-destructive     /* Error text - Para mensajes de error */
.text-primary         /* Brand text - Para elementos de marca */
.text-card-foreground /* Text on card backgrounds */
```

### ❌ COLORES PROHIBIDOS
```css
/* NUNCA usar estos */
.text-gray-900  → .text-foreground
.text-gray-600  → .text-muted-foreground
.text-blue-600  → .text-primary
```

## 🧩 Component Patterns (✅ IMPLEMENTADO)

### Buttons - ✅ COMPLETO CON CVA
```tsx
// ✅ Consistent button usage - Implementado con CVA
<Button variant="default" size="default">Primary Action</Button>
<Button variant="secondary" size="sm">Secondary Action</Button>
<Button variant="outline" size="lg">Outline Action</Button>
<Button variant="ghost" size="icon"><Icon /></Button>
<Button variant="destructive">Delete Action</Button>

// ✅ Disponible en Storybook con ejemplos interactivos
// Acceso: npm run storybook → UI/Button
```

### 📚 STORYBOOK DOCUMENTATION
- **Acceso**: `npm run storybook` en http://localhost:6006
- **Componentes documentados**: Button, Card, Input, Select, Checkbox, etc.
- **Ejemplos interactivos**: Todos los variants y estados
- **Guidelines**: Patrones de uso y anti-patrones

### Form Fields
```tsx
// ✅ Consistent form field pattern
<div className="space-y-2">
  <Label htmlFor="field">Field Label</Label>
  <Input
    id="field"
    className="focus-visible:ring-primary"
    {...register('field')}
  />
  {errors.field && (
    <p className="text-sm text-destructive">
      {errors.field.message}
    </p>
  )}
</div>
```

### Cards
```tsx
// ✅ Consistent card usage
<Card>
  <CardHeader>
    <CardTitle>Title</CardTitle>
    <CardDescription>Description</CardDescription>
  </CardHeader>
  <CardContent>
    Content here
  </CardContent>
</Card>
```

## Spacing & Layout

### Container Patterns
```css
.container-fluid    /* Full width with responsive padding */
.max-w-md mx-auto  /* Centered content with max width */
```

### Spacing Scale
- Use Tailwind's spacing scale: `space-y-2`, `space-y-4`, `space-y-6`
- Consistent gap patterns: `gap-2`, `gap-4`, `gap-6`

## Animation & Transitions

### Standard Transitions
```css
transition-all duration-200  /* Standard transition */
transition-colors           /* Color-only transitions */
```

### Loading States
```tsx
// ✅ Consistent loading pattern
{isPending ? (
  <div className="flex items-center gap-2">
    <LoadingSpinner size="sm" />
    <span>Loading...</span>
  </div>
) : (
  <span>Action Text</span>
)}
```

## Accessibility

### Focus States
- Use `focus-visible:ring-primary` for consistent focus rings
- Ensure proper ARIA labels and semantic HTML

### Color Contrast
- All text meets WCAG AA standards
- Use semantic color classes for consistency

## Mobile Responsiveness

### Breakpoint Strategy
- Mobile-first approach
- Use responsive utilities: `sm:`, `md:`, `lg:`
- Test on mobile devices regularly

## Common Anti-Patterns to Avoid

### ❌ Don't Use
```tsx
// Hard-coded colors
className="text-gray-700 bg-blue-600"

// Inconsistent spacing
className="mt-3 mb-5 px-2"

// Mixed styling approaches
style={{ color: '#3b82f6' }}
```

### ✅ Do Use
```tsx
// Semantic colors
className="text-foreground bg-primary"

// Consistent spacing
className="space-y-4 px-4"

// Design system classes
className="btn-primary"
```
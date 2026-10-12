# Token recipes (styled mode)

All snippets use `definePreset(Aura, {...})` from `@openng/optimus-ui-themes` and are verified from the official styled-mode docs. Reminder: match the preset's structure (direct vs `colorScheme`) or the override is ignored.

Contents: architecture · primary · surface · noir · form fields · focus ring · component tokens · extend · palette utilities

## Architecture

Three token tiers: **primitive** (context-free colors like `blue.500`; palettes available: emerald, green, lime, red, orange, amber, yellow, teal, cyan, sky, blue, indigo, violet, purple, fuchsia, pink, rose, slate, gray, zinc, neutral, stone — each 50–950) → **semantic** (`primary.color`, `surface.*`, `focusRing`, `formField`, `highlight`; may differ per color scheme) → **component** (`button.background`, `inputtext.background`; map to semantic tokens). Reference tokens with `{indigo.500}` syntax inside presets, `var(--p-indigo-500)` in CSS, `$dt('indigo.500').value` in TS.

## Primary color

```typescript
definePreset(Aura, { semantic: { primary: {
  50: '{indigo.50}', 100: '{indigo.100}', 200: '{indigo.200}', 300: '{indigo.300}', 400: '{indigo.400}',
  500: '{indigo.500}', 600: '{indigo.600}', 700: '{indigo.700}', 800: '{indigo.800}', 900: '{indigo.900}', 950: '{indigo.950}' } } });
```

## Surfaces (different per scheme — must be under `colorScheme`)

```typescript
definePreset(Aura, { semantic: { colorScheme: {
  light: { surface: { 0: '#ffffff', 50: '{zinc.50}', 100: '{zinc.100}', 200: '{zinc.200}', 300: '{zinc.300}', 400: '{zinc.400}', 500: '{zinc.500}', 600: '{zinc.600}', 700: '{zinc.700}', 800: '{zinc.800}', 900: '{zinc.900}', 950: '{zinc.950}' } },
  dark:  { surface: { 0: '#ffffff', 50: '{slate.50}', 100: '{slate.100}', 200: '{slate.200}', 300: '{slate.300}', 400: '{slate.400}', 500: '{slate.500}', 600: '{slate.600}', 700: '{slate.700}', 800: '{slate.800}', 900: '{slate.900}', 950: '{slate.950}' } }
} } });
```

## "Noir" (black/white primary using surface tones)

```typescript
const Noir = definePreset(Aura, { semantic: {
  primary: { 50: '{zinc.50}', 100: '{zinc.100}', 200: '{zinc.200}', 300: '{zinc.300}', 400: '{zinc.400}', 500: '{zinc.500}', 600: '{zinc.600}', 700: '{zinc.700}', 800: '{zinc.800}', 900: '{zinc.900}', 950: '{zinc.950}' },
  colorScheme: {
    light: { primary: { color: '{zinc.950}', inverseColor: '#ffffff', hoverColor: '{zinc.900}', activeColor: '{zinc.800}' },
             highlight: { background: '{zinc.950}', focusBackground: '{zinc.700}', color: '#ffffff', focusColor: '#ffffff' } },
    dark:  { primary: { color: '{zinc.50}', inverseColor: '{zinc.950}', hoverColor: '{zinc.100}', activeColor: '{zinc.200}' },
             highlight: { background: 'rgba(250,250,250,.16)', focusBackground: 'rgba(250,250,250,.24)', color: 'rgba(255,255,255,.87)', focusColor: 'rgba(255,255,255,.87)' } }
  } } });
```

## Form fields (affects select, textarea, datepicker… that derive from `formField`)

```typescript
definePreset(Aura, { semantic: { colorScheme: {
  light: { formField: { hoverBorderColor: '{primary.color}' } },
  dark:  { formField: { hoverBorderColor: '{primary.color}' } } } } });
```

## Focus ring

```typescript
definePreset(Aura, { semantic: { focusRing: { width: '2px', style: 'dashed', color: '{primary.color}', offset: '1px' } } });
```
Customize the ring — never remove it (keyboard accessibility).

## One component type, globally

```typescript
definePreset(Aura, { components: { card: { colorScheme: {
  light: { root: { background: '{surface.0}',   color: '{surface.700}' }, subtitle: { color: '{surface.500}' } },
  dark:  { root: { background: '{surface.900}', color: '{surface.0}'   }, subtitle: { color: '{surface.400}' } } } } } });
```
Prefer building a full custom preset if you are restyling many components; per-component overrides are meant for exceptions. For a single instance use `[dt]`.

## Extend: custom tokens and CSS

```typescript
definePreset(Aura, {
  components: { button: {
    extend: { accent: { color: '#f59e0b', inverseColor: '#ffffff' } },     // button.accent.color, button.accent.inverse.color
    css: ({ dt }) => `.p-button-accent { background: ${dt('button.accent.color')}; color: ${dt('button.accent.inverse.color')}; transition-duration: ${dt('my.transition.fast')}; }`
  } },
  extend: { my: { transition: { slow: '0.75s', normal: '0.5s', fast: '0.25s' } } },   // global custom tokens
  css: ({ dt }) => `img { display: ${dt('my.image.display')}; }`                      // global CSS (define my.image.display too)
});
```
Use in templates: `<p-button label="Accent" styleClass="p-button-accent" />`.

## Utilities recap

`usePreset`, `updatePreset`, `updatePrimaryPalette`, `updateSurfacePalette`, `$dt`, `palette` — see SKILL.md. Example: let users pick a brand color at runtime with `updatePrimaryPalette(palette('#0ea5e9'))` *(verify the returned shape matches the 50–950 object the function expects)*.

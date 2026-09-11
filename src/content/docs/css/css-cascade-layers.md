---
title: CSS Cascade Layers
description: Control which groups of CSS rules win in the cascade without relying on selector specificity.
---

Cascade layers let you organize styles into named groups and decide the order in which those groups compete. They are useful when an application combines reset styles, third-party CSS, design tokens, components, and utility classes.

Normally, a more specific selector can beat a less specific one. With layers, the layer order is considered before selector specificity for normal declarations. A rule in a later layer wins over a rule in an earlier layer, even if the earlier rule uses a more specific selector.

## Define a layer order

Declare the order near the top of your stylesheet. The first name has the lowest priority and the last name has the highest priority.

```css
@layer reset, base, components, utilities;
```

Then add rules to each layer:

```css
@layer reset {
  * {
    box-sizing: border-box;
  }
}

@layer base {
  button {
    font: inherit;
  }
}

@layer components {
  .button {
    background: #2563eb;
    border: 0;
    border-radius: 0.375rem;
    color: white;
    padding: 0.625rem 1rem;
  }
}

@layer utilities {
  .bg-slate {
    background: #334155;
  }
}
```

The `.bg-slate` rule can override the component background because `utilities` comes after `components`.

## Layers beat specificity

Here, the selector in the `base` layer is more specific, but the component rule still wins because its layer comes later.

```css
@layer base, components;

@layer base {
  .profile .title {
    color: #475569;
  }
}

@layer components {
  .title {
    color: #0f172a;
  }
}
```

This makes overrides more predictable and avoids escalating selector specificity over time.

## Put third-party CSS in a low-priority layer

Import a library into its own layer so your project styles can override it without `!important`.

```css
@import url("vendor.css") layer(vendor);

@layer vendor, components;

@layer components {
  .modal {
    border-radius: 0.75rem;
  }
}
```

The `components` styles take precedence over normal declarations from `vendor`.

## Use anonymous layers carefully

You can make a layer without a name:

```css
@layer {
  .legacy-widget {
    color: #64748b;
  }
}
```

Named layers are usually easier to maintain because their purpose and order are visible in the stylesheet.

## Unlayered styles have priority

For normal declarations, styles outside a layer override styles inside layers. That is intentional: use layers for CSS you want to make easy to override, and keep truly global project rules unlayered only when they should have top priority.

```css
@layer components {
  .notice {
    color: #1e3a8a;
  }
}

.notice {
  color: #991b1b;
}
```

The unlayered `.notice` rule is applied.

## Important declarations reverse the order

`!important` declarations follow the reverse layer order: an important rule in an earlier layer wins over an important rule in a later layer. Avoid using this as an override strategy; reserve `!important` for exceptional cases.

## Practical layer structure

A small project can start with four layers:

```css
@layer reset, base, components, utilities;
```

- `reset`: normalization and browser-default fixes.
- `base`: element-level defaults such as typography and form controls.
- `components`: reusable UI patterns.
- `utilities`: small, intentional one-purpose overrides.

Keep the order declaration in one shared entry stylesheet. Adding a layer name later in a different order can make the cascade hard to reason about.

## Key takeaways

- Layers control cascade priority before specificity is compared.
- Declare named layer order once, near the top of the entry stylesheet.
- Put third-party and legacy CSS in low-priority layers.
- Keep selectors simple instead of increasing specificity to force overrides.

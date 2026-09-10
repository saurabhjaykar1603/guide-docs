---
title: CSS Nesting
description: Group related CSS rules with native nesting while keeping selectors clear and maintainable.
---

CSS nesting lets you write related selectors inside one another. It can make component styles easier to scan because the styles for a parent, its children, and its states stay together.

```css
.card {
  padding: 1rem;
  border: 1px solid #d1d5db;

  h2 {
    margin: 0;
  }

  p {
    color: #4b5563;
  }
}
```

The nested rules above are equivalent to:

```css
.card h2 {
  margin: 0;
}

.card p {
  color: #4b5563;
}
```

## Nest states with `&`

Use `&` to refer to the selector of the current rule. This is especially useful for hover, focus, and modifier states.

```css
.button {
  background: #2563eb;
  color: white;

  &:hover {
    background: #1d4ed8;
  }

  &:focus-visible {
    outline: 3px solid #93c5fd;
    outline-offset: 3px;
  }

  &.is-loading {
    cursor: progress;
    opacity: 0.7;
  }
}
```

This produces selectors such as `.button:hover` and `.button.is-loading`.

## Nest child elements

Without `&`, a nested selector targets descendants of the parent.

```css
.navigation {
  display: flex;
  gap: 1rem;

  a {
    color: #1f2937;
    text-decoration: none;

    &:hover {
      text-decoration: underline;
    }
  }
}
```

The hover selector becomes `.navigation a:hover`.

## Combine selectors

You can nest comma-separated selectors when several elements share the same styles.

```css
.article {
  h2,
  h3 {
    color: #111827;
    line-height: 1.2;
  }
}
```

This is equivalent to writing `.article h2, .article h3`.

## Keep nesting shallow

Nesting is helpful for relationships that are genuinely local, but deeply nested selectors become hard to understand and can be overly specific.

```css
/* Prefer this */
.menu-item {
  color: #374151;
}

.menu-item:hover {
  color: #2563eb;
}

/* Avoid long, fragile selector chains */
.page .sidebar .menu .menu-item a span {
  color: #2563eb;
}
```

## Good practices

- Use nesting for component children and states that belong together.
- Keep nesting to a few levels at most.
- Use `&` whenever the nested selector modifies the current selector.
- Check the final selector: nested rules still follow normal cascade and specificity rules.
- Prefer clear class names over long descendant chains.

:::tip
Native CSS nesting is supported in modern browsers. If your project supports older browsers, ensure your build tool transforms nested CSS or provide an appropriate fallback.
:::

## Key points

- Nesting groups related CSS rules in one place.
- A nested selector without `&` selects descendants.
- `&` represents the current selector and is ideal for states such as `:hover` and modifiers such as `.is-active`.
- Shallow nesting keeps styles predictable and easy to maintain.

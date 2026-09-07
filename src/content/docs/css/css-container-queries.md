---
title: CSS Container Queries
description: Create components that adapt to the space available in their container instead of the whole viewport.
---

Container queries let a component respond to the size of the area it occupies. Unlike media queries, which measure the viewport, container queries are useful when the same component can appear in a wide main column, a narrow sidebar, or a card grid.

## Why use container queries?

A media query answers, “How wide is the browser window?” A container query answers, “How wide is this component’s parent?”

For example, a product card may show its image and details side by side in a large content area, but stack them when the card is placed in a narrow sidebar. The viewport can be the same in both cases.

## Create a query container

Set `container-type` on the element whose inline size a child component should observe.

```css
.card-list {
  container-type: inline-size;
}
```

`inline-size` tracks width in horizontal writing modes and is the most common choice. You can also name a container when a component needs to target a specific ancestor.

```css
.sidebar {
  container: sidebar / inline-size;
}
```

The shorthand is:

```css
container: name / type;
```

## Write a container query

Use `@container` to apply styles when the nearest eligible container reaches a condition.

```css
.product-card {
  display: grid;
  gap: 1rem;
}

@container (min-width: 32rem) {
  .product-card {
    grid-template-columns: 10rem 1fr;
    align-items: center;
  }
}
```

Below `32rem`, the card uses its normal single-column layout. Once its container is at least `32rem` wide, the image and details can sit in two columns.

## Target a named container

Use the container name after `@container` when the nearest container is not the one you want to query.

```css
@container sidebar (max-width: 24rem) {
  .product-card__meta {
    display: none;
  }
}
```

Named containers make intent clearer in larger layouts, especially when components are nested.

## Use container query units

Container query units size values relative to the query container. For example, `cqi` represents 1% of a container’s inline size.

```css
.hero-title {
  font-size: clamp(1.75rem, 7cqi, 4rem);
}
```

Useful units include:

| Unit | Relative to |
| --- | --- |
| `cqw` | 1% of the container’s width |
| `cqh` | 1% of the container’s height |
| `cqi` | 1% of the container’s inline size |
| `cqb` | 1% of the container’s block size |
| `cqmin` | The smaller container dimension |
| `cqmax` | The larger container dimension |

Use `clamp()` with these units so text and spacing remain within sensible limits.

## Combine container and media queries

Container queries and media queries solve different problems and work well together.

```css
/* The component responds to its available space. */
@container (min-width: 40rem) {
  .dashboard-card {
    grid-template-columns: 1fr 1fr;
  }
}

/* The page responds to a device or user preference. */
@media (prefers-reduced-motion: reduce) {
  .dashboard-card {
    transition: none;
  }
}
```

Use media queries for page-level layout, input capabilities, and user preferences. Use container queries for reusable component layout.

## Practical guidelines

- Put the query container on a stable parent, not the element whose size you are changing.
- Start with a useful default layout, then add a query only when extra space improves the component.
- Prefer a few meaningful thresholds over many small breakpoints.
- Keep content available at every size; avoid hiding essential controls merely because space is limited.
- Test the component in every context where it appears, including sidebars and grid cards.

## Key points

- Container queries make components responsive to their own available space.
- Add `container-type: inline-size` to establish a size query container.
- Use `@container` rules for layout changes at component-level thresholds.
- Named containers and container query units help with more complex component systems.
- Keep media queries for viewport-wide decisions and user preferences.

## Next topic

Continue with **CSS Nesting** to learn how to group related selectors while keeping styles readable.

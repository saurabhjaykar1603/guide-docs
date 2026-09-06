---
title: CSS Transforms
description: Move, rotate, scale, and skew elements with CSS transforms while keeping layouts predictable.
---

The `transform` property changes how an element is painted without changing its normal place in the document flow. It is useful for small interaction effects, icons, and visual composition.

## Translate

`translate()` moves an element along the horizontal and vertical axes.

```css
.card:hover {
  transform: translateY(-6px);
}

.toast {
  transform: translateX(100%);
}
```

Positive `translateX()` values move right; positive `translateY()` values move down. Percentage values are relative to the transformed element's own size.

## Scale and rotate

`scale()` changes the visual size and `rotate()` turns an element.

```css
.avatar:hover {
  transform: scale(1.05);
}

.chevron[aria-expanded="true"] {
  transform: rotate(180deg);
}
```

Transforms can be combined in one declaration. They are applied from right to left, so order matters.

```css
.badge {
  transform: rotate(-4deg) scale(1.1);
}
```

## Transform origin

`transform-origin` chooses the point used for rotations and scaling. Its default is the center.

```css
.menu {
  transform-origin: top right;
  transform: scale(0.9);
}
```

This is especially helpful for dropdowns, tooltips, and elements that should appear to open from an edge.

## Transitions

Pair a transform with a transition to make a change feel deliberate.

```css
.card {
  transition: transform 180ms ease, box-shadow 180ms ease;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 28px rgb(15 23 42 / 16%);
}
```

Respect motion preferences for nonessential animation.

```css
@media (prefers-reduced-motion: reduce) {
  .card {
    transition: none;
  }
}
```

## Layout considerations

A transformed element still occupies its original layout space. If it moves over nearby content, it may need `position`, `z-index`, or extra spacing. Avoid using transforms to fix a layout that should be handled by Flexbox, Grid, margins, or positioning.

For performance-sensitive animation, transforms and opacity are often good choices because browsers can frequently animate them without recalculating layout.

---
title: CSS Pseudo-classes and Pseudo-elements
description: Use pseudo-classes and pseudo-elements to style element states and generated parts of an element.
---

Pseudo-classes select an element in a particular state. Pseudo-elements select a part of an element or create a generated piece of content. They let you add useful detail without extra HTML.

## Pseudo-classes

A pseudo-class starts with one colon. Common examples include `:hover`, `:focus`, `:active`, and `:disabled`.

```css
.button:hover {
  background-color: #1d4ed8;
}

.button:focus-visible {
  outline: 3px solid #fbbf24;
  outline-offset: 3px;
}

input:disabled {
  cursor: not-allowed;
  opacity: 0.6;
}
```

Use `:focus-visible` for keyboard focus styling. It avoids showing a focus ring after most pointer clicks while preserving a clear indicator for keyboard users.

## Structural pseudo-classes

Structural pseudo-classes select elements based on their position among siblings.

```css
.menu li:first-child {
  border-top: 1px solid #cbd5e1;
}

.menu li:nth-child(odd) {
  background-color: #f8fafc;
}

.card:not(.featured) {
  box-shadow: none;
}
```

Prefer a meaningful class when the styling represents an important concept, such as `.featured`. Use structural selectors for genuinely repeated patterns, such as alternating table rows.

## Pseudo-elements

Pseudo-elements conventionally use two colons. `::before` and `::after` create generated content, while `::first-letter` and `::selection` style a specific part of text.

```css
.external-link::after {
  content: " ↗";
  font-size: 0.85em;
}

.article p::first-letter {
  color: #2563eb;
  font-size: 2.5em;
  font-weight: 700;
}

::selection {
  color: #ffffff;
  background-color: #2563eb;
}
```

`::before` and `::after` need a `content` value to appear. Empty content is valid when the pseudo-element is used as a decorative shape.

```css
.tag {
  position: relative;
}

.tag::before {
  position: absolute;
  inset: 0;
  z-index: -1;
  content: "";
  border-radius: inherit;
  background: #dbeafe;
}
```

## Combining states

Pseudo-classes can be combined to describe a precise state.

```css
a:hover:not(.button) {
  text-decoration-thickness: 2px;
}

input:invalid:focus {
  border-color: #dc2626;
}
```

Keep interactive states consistent: hover should add feedback, focus should be visible, and disabled controls should not appear actionable.

:::caution
Generated content is not a replacement for meaningful text in the HTML. Important labels and instructions should remain in the document so they work for assistive technology and when CSS is unavailable.
:::

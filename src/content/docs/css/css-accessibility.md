---
title: CSS Accessibility
description: Build CSS that preserves readable text, visible focus, usable motion, and adaptable layouts.
---

Accessible CSS helps people perceive and operate an interface in different conditions: keyboard navigation, reduced motion, high contrast needs, zoomed text, and small screens.

## Keep focus visible

Never remove focus styling without replacing it. `:focus-visible` provides a clear keyboard focus indicator.

```css
:focus-visible {
  outline: 3px solid #2563eb;
  outline-offset: 3px;
}

.button:focus-visible {
  outline-color: #fbbf24;
}
```

Use sufficient contrast between the outline and the backgrounds it may overlap.

## Use color with another cue

Color alone should not communicate a state. Add an icon, label, border pattern, or text change.

```css
.field-error {
  border: 2px solid #b91c1c;
}

.field-error-message::before {
  content: "Error: ";
  font-weight: 700;
}
```

The visible text should still describe the problem; CSS-generated text should only reinforce it.

## Respect motion preferences

Some people experience discomfort from movement. Reduce nonessential animation when the operating system requests it.

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    scroll-behavior: auto;
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

Avoid autoplaying or looping decorative motion when a still presentation conveys the same information.

## Let text adapt

Use relative units and avoid fixed heights for text containers. Users may zoom text or use a larger default font size.

```css
.article {
  max-width: 70ch;
  padding: 1rem;
  line-height: 1.6;
}

.notice {
  min-height: 3rem;
  padding: 0.75rem 1rem;
}
```

`ch` gives prose a readable line length, while `rem` scales with the user's root font-size preference.

## Support forced colors

High-contrast modes can replace your palette. Do not depend only on subtle shadows or background color to show controls.

```css
@media (forced-colors: active) {
  .button {
    border: 1px solid ButtonText;
  }
}
```

Use semantic HTML first. CSS can improve its appearance and feedback, but it cannot give a nonsemantic element the behavior of a real button, link, or input.

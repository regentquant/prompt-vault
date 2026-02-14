# Design System Specification

> **Purpose**: Send this file to any AI code assistant to reproduce the exact visual style, layout, and interaction patterns of the Prompt Vault webapp. The result should feel like the same designer built it — warm, editorial, unhurried, and distinctly non-AI-looking.

---

## 1. Design Philosophy

This is **editorial minimalism with warmth**. Think a well-typeset independent magazine, not a SaaS dashboard. Every decision serves calm readability and quiet confidence.

**Core principles:**
- **Warm neutrals over cool grays** — the palette is built on parchment, sand, and sepia tones, never blue-gray or slate
- **Typography carries the hierarchy** — serif headlines, sans-serif body, monospace for metadata. No icons or color needed to distinguish levels
- **Generous negative space** — content breathes. Padding is large, spacing is unhurried
- **Invisible interactions** — transitions are 150ms, hover states are subtle color shifts, nothing bounces or scales. The UI gets out of the way
- **No decoration** — zero gradients, zero shadows, zero rounded corners on cards, zero blurred blobs. Visual interest comes from typography contrast and spatial rhythm alone
- **Borderline brutalist restraint** — elements are separated by 1px lines, not shadows or background fills. The structure is honest

**What this is NOT:**
- Not SaaS (no card grids, no stat dashboards, no gradient hero sections)
- Not startup-trendy (no Inter font, no purple-to-blue gradients, no glassmorphism)
- Not Material/Bootstrap (no elevation shadows, no pill buttons, no rounded-xl cards)

---

## 2. Color System

Colors are defined as CSS custom properties on `:root` with a `[data-theme="dark"]` override. The palette is intentionally **warm** — every gray has yellow/brown undertones.

### Light Theme (default)
```css
:root {
  --bg: #f8f6f1;             /* warm parchment background */
  --text: #2c2a25;            /* dark warm brown — primary text */
  --text-secondary: #6e695f;  /* muted olive-brown — descriptions, labels */
  --accent: #8b7355;          /* warm sepia — links, active states, brand color */
  --border: #e0dcd4;          /* warm light gray — all dividers and borders */
  --recessed: #f0ece4;        /* slightly darker parchment — code blocks, inset areas */
  --tag-bg: #ebe7df;          /* tag/badge backgrounds */
  --row-hover: #f3f0e9;       /* subtle hover highlight */
}
```

### Dark Theme
```css
[data-theme="dark"] {
  --bg: #1a1915;              /* very dark warm brown, NOT pure black */
  --text: #e0dcd4;            /* warm off-white */
  --text-secondary: #9a9488;  /* muted warm gray */
  --accent: #c4a87a;          /* golden sepia — brighter than light theme accent */
  --border: #2e2b25;          /* dark warm separator */
  --recessed: #22201b;        /* slightly lighter than bg */
  --tag-bg: #2a2722;
  --row-hover: #22201b;
}
```

### Key color rules
- **Accent color** (`--accent`) is the ONLY color with personality. Everything else is neutral
- Never use blue, purple, green, or red for UI chrome. Reserve color for semantic meaning only (errors, success)
- Background-to-text contrast is deliberately moderate — not stark white-on-black. This creates the warm, papery feel
- Dark mode is NOT inverted light mode — it's a separate warm-dark palette where the accent shifts to gold

---

## 3. Typography

Three fonts, each with a clear role. This trio creates visual hierarchy without relying on size alone.

### Font Stack
```css
/* Headlines — elegant serif with personality */
font-family: 'Instrument Serif', serif;

/* Body text, UI labels, buttons — clean geometric sans */
font-family: 'DM Sans', sans-serif;

/* Code, metadata, tags — technical monospace */
font-family: 'IBM Plex Mono', monospace;
```

### Load via Google Fonts
```html
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:ital,wght@0,400;0,500;0,600;1,400&family=IBM+Plex+Mono:wght@400;500&family=Instrument+Serif:ital@0;1&display=swap" rel="stylesheet">
```

### Type Scale

| Role | Font | Size | Weight | Extras |
|------|------|------|--------|--------|
| Page title / Sidebar title | Instrument Serif | 1.55rem | 400 | `letter-spacing: -0.01em`, `line-height: 1.2` |
| Section headers (category names) | Instrument Serif | 1.35rem | 400 | `letter-spacing: -0.01em` |
| Body text / Base | DM Sans | 16px (1rem) | 400 | `line-height: 1.6` |
| Item titles | DM Sans | 0.95rem | 500 | `line-height: 1.35` |
| Item descriptions | DM Sans | 0.82rem | 400 | `color: var(--text-secondary)`, `line-height: 1.45` |
| Search input | DM Sans | 0.92rem | 400 | |
| Sidebar nav items | DM Sans | 0.88rem | 400 (500 when active) | |
| Sidebar section label | DM Sans | 0.68rem | 600 | `text-transform: uppercase`, `letter-spacing: 0.08em` |
| Tags / metadata | IBM Plex Mono | 0.65rem | 400 | `opacity: 0.5–0.7` |
| Code / preformatted | IBM Plex Mono | 0.8rem | 400 | `line-height: 1.6`, `white-space: pre-wrap` |
| Badges | DM Sans | 10px | 600 | `letter-spacing: 0.02em` |

### Typography rules
- **Serif is reserved for hierarchy** — only page titles and section headers use Instrument Serif. Never use it for body text or UI elements
- **Monospace signals "data"** — tags, counts, code blocks. It visually separates metadata from human-readable content
- **No bold body text** — use weight 500 (medium) as the heaviest for item titles. 600 only for tiny uppercase labels
- Enable font smoothing: `-webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale;`

---

## 4. Layout Architecture

### Desktop: Fixed sidebar + scrollable main content

```
┌─────────────────────────────────────────────────┐
│ Sidebar (fixed)  │  Main content (scrollable)    │
│ width: 220px     │  margin-left: 220px           │
│                  │  padding: 48px 56px 80px       │
│ ┌──────────────┐ │  max-width: 860px              │
│ │ Title        │ │                                │
│ │ Subtitle     │ │  ┌─ Search ──────────────┐    │
│ │ Credit       │ │  │ border-bottom input    │    │
│ │              │ │  └────────────────────────┘    │
│ │ Quick Links  │ │                                │
│ │ (icon row)   │ │  ┌─ Category Group ──────┐    │
│ │              │ │  │ Serif section header   │    │
│ │ CATEGORIES   │ │  │ ┌─ Row ─────────────┐ │    │
│ │ · All     24 │ │  │ │ Title    [Copy]   │ │    │
│ │ · Coding  12 │ │  │ │ Desc     [DL]     │ │    │
│ │ · Writing  5 │ │  │ ├───────────────────┤ │    │
│ │ · ...        │ │  │ │ (expanded body)   │ │    │
│ │              │ │  │ └───────────────────┘ │    │
│ │              │ │  └────────────────────────┘    │
│ │ 24 prompts   │ │                                │
│ └──────────────┘ │                                │
└─────────────────────────────────────────────────┘
```

### Sidebar specs
- Fixed position, full viewport height
- `width: 220px` (set via `--sidebar-w` CSS variable)
- `padding: 48px 28px 32px`
- `border-right: 1px solid var(--border)`
- Uses flexbox column with `margin-top: auto` on the total count to pin it to the bottom
- Overflow-y: auto (scrolls independently if content is taller than viewport)

### Main content specs
- `margin-left: var(--sidebar-w)` — pushes content right of sidebar
- `padding: 48px 56px 80px`
- `max-width: 860px` — content never stretches too wide on large screens

### Mobile (max-width: 768px)
- Sidebar becomes `position: static`, full-width, horizontal
- Border switches from right to bottom
- Category nav becomes a horizontal scrolling row with mask fade on the right edge
  ```css
  -webkit-mask-image: linear-gradient(to right, black calc(100% - 40px), transparent);
  ```
- Active indicator switches from left border to bottom border
- Category counts hidden on mobile
- Main content: `padding: 28px 20px 60px`, no margin-left

---

## 5. Component Patterns

### 5a. Sidebar Navigation

The sidebar nav is a vertical list of category buttons. Each button shows the category name and a count.

```css
/* Nav button default */
padding: 5px 0 5px 14px;
border-left: 2px solid transparent;
color: var(--text-secondary);
font-size: 0.88rem;
transition: color 0.15s ease;

/* Nav button active */
color: var(--accent);
font-weight: 500;
border-left-color: var(--accent);

/* Nav button hover */
color: var(--accent);

/* Count badge */
font-size: 0.75rem;
color: var(--border);
font-variant-numeric: tabular-nums;
```

### 5b. Search Input

A minimal, borderless input with only a bottom border. No box, no background, no icon.

```css
width: 100%;
max-width: 380px;
font-family: 'DM Sans', sans-serif;
font-size: 0.92rem;
color: var(--text);
background: transparent;
border: none;
border-bottom: 1px solid var(--border);
padding: 6px 0;
outline: none;
/* Focus: border-bottom-color changes to var(--accent) */
```

### 5c. Content Row (List Item)

Items are displayed as **bordered rows**, not cards. This is a key design signature — think spreadsheet/editorial list, not card grid.

```css
/* Row container */
border-top: 1px solid var(--border);
cursor: pointer;
transition: background-color 0.15s ease;

/* Last row also gets border-bottom */
/* Hover: background-color: var(--row-hover) */

/* Row inner layout */
display: flex;
align-items: baseline;
justify-content: space-between;
padding: 14px;
gap: 16px;
```

**Row structure:**
```
┌──────────────────────────────────────────────────┐
│ Title ★ [Badge]    [tag]        [Copy] [Download]│
│ Description text here...                         │
├──────────────────────────────────────────────────┤
│ (Expanded: code block with left accent border)   │
│ #tag1  #tag2  #tag3                              │
└──────────────────────────────────────────────────┘
```

### 5d. Expanded Content Area

When a row is clicked, its body expands below. The expanded area has a distinctive left-border accent.

```css
/* Container */
padding: 0 14px 18px;

/* Inner content block */
background: var(--recessed);
border-left: 2px solid var(--accent);
padding: 16px 20px;
overflow-x: auto;

/* Pre-formatted text inside */
font-family: 'IBM Plex Mono', monospace;
font-size: 0.8rem;
line-height: 1.6;
white-space: pre-wrap;
word-wrap: break-word;
```

**Sticky header on expanded rows:** When a row is expanded and the user scrolls within it, the summary row sticks to the top:
```css
.prompt-row.expanded .prompt-summary {
  position: sticky;
  top: 0;
  z-index: 10;
  background: var(--bg);
  border-bottom: 1px solid var(--border);
}
```

### 5e. Action Buttons

Small, square, icon-only buttons with thin borders. No fill, no rounded corners.

```css
display: flex;
align-items: center;
justify-content: center;
width: 28px;
height: 28px;
color: var(--text-secondary);
background: none;
border: 1px solid var(--border);
cursor: pointer;
transition: color 0.15s ease, border-color 0.15s ease;

/* Hover / Done state */
color: var(--accent);
border-color: var(--accent);

/* Icon size inside */
width: 15px;
height: 15px;
```

### 5f. Badges

Small inline labels for special markers (e.g., "Official", source attribution).

```css
display: inline-block;
font-size: 10px;
font-weight: 600;
letter-spacing: 0.02em;
padding: 1px 6px;
margin-left: 8px;
border-radius: 4px;  /* only element with border-radius */
vertical-align: baseline;
position: relative;
top: -2px;
background: color-mix(in srgb, var(--accent) 12%, transparent);
color: var(--accent);
border: 1px solid color-mix(in srgb, var(--accent) 25%, transparent);
```

### 5g. Primary Button (CTA)

Used sparingly — only for primary actions.

```css
font-family: 'DM Sans', sans-serif;
font-size: 0.82rem;
font-weight: 500;
color: var(--bg);         /* inverted — bg color as text */
background: var(--accent); /* accent as fill */
border: none;
padding: 8px 18px;
cursor: pointer;
transition: opacity 0.15s ease;

/* Hover: opacity: 0.85 */
/* Disabled: opacity: 0.4 */
```

### 5h. Toggle Button (Secondary)

For expandable panels, optional features.

```css
display: inline-flex;
align-items: center;
gap: 6px;
font-size: 0.82rem;
color: var(--accent);
background: none;
border: 1px solid var(--border);
padding: 6px 14px;
cursor: pointer;
/* Hover: border-color: var(--accent), bg: color-mix(in srgb, var(--accent) 6%, transparent) */
```

### 5i. Textarea

For multi-line input areas.

```css
width: 100%;
min-height: 72px;
font-family: 'DM Sans', sans-serif;
font-size: 0.88rem;
color: var(--text);
background: var(--recessed);
border: 1px solid var(--border);
padding: 10px 12px;
resize: vertical;
outline: none;
line-height: 1.5;
/* Focus: border-color: var(--accent) */
```

### 5j. Panel (Expandable Section)

Collapsible content sections that open below a toggle.

```css
border: 1px solid var(--border);
padding: 20px;
/* Hidden by default, toggled via class */
```

---

## 6. Interaction & Animation

### Core principle: everything is 150ms ease

All transitions use the same timing: `0.15s ease`. This creates a cohesive, snappy feel.

```css
/* Standard transition for color/border changes */
transition: color 0.15s ease;
transition: color 0.15s ease, border-color 0.15s ease;
transition: background-color 0.15s ease;
transition: opacity 0.15s ease;
transition: background 0.3s ease, color 0.3s ease; /* only for theme switch on body */
```

### Hover behaviors
- **Nav items**: color shifts to `var(--accent)`
- **Rows**: background shifts to `var(--row-hover)`
- **Action buttons**: color AND border-color shift to `var(--accent)`
- **Primary button**: opacity dims to 0.85
- **Links / icons**: opacity increases from ~0.55 to 1.0

### Click feedback
- **Copy/Download buttons**: icon swaps to a checkmark for 1500ms, then reverts
- **Expandable rows**: body slides open (display toggle, no animation needed)

### Reveal animation (used sparingly)
```css
@keyframes finderReveal {
  from { opacity: 0; transform: translateY(-6px); }
  to { opacity: 1; transform: translateY(0); }
}
/* Duration: 0.35s ease — only for newly revealed UI sections */
```

### What NOT to animate
- No scale transforms on hover (no `hover:scale-105`)
- No translateY on hover (no floating card effects)
- No shadow transitions
- No elastic/bounce easing
- No skeleton loading screens
- No page transitions

---

## 7. Icons

All icons are **inline SVGs** using `stroke="currentColor"` so they inherit text color automatically. No icon library needed.

### Icon specs
- `stroke-width: 1.5` (default) or `2` (for check mark)
- `fill: none` — all icons are outline-only
- Standard size: `width: 15px; height: 15px` inside action buttons
- Quick links size: `width: 20px; height: 20px`

### Icon dark mode handling
For external brand icons (SVG images, not inline), use a class to invert in dark mode:
```css
[data-theme="dark"] .icon-invert {
  filter: invert(1);
}
```

---

## 8. Spacing Rhythm

The spacing system is NOT a strict scale — it follows an editorial rhythm based on content hierarchy.

| Context | Value | Usage |
|---------|-------|-------|
| Section gap | 48–56px | Between major sections (search to content, between category groups) |
| Component gap | 28–32px | Between sidebar sections, between elements within a section |
| Item padding | 14px | Inside rows, body padding |
| Inner content | 16–20px | Inside expanded code blocks, panels |
| Tight gap | 6–8px | Between tags, between small inline elements |
| Element gap | 12–16px | Between label and content, between buttons |
| Micro gap | 2–3px | Between nav items, between title and description |

### Key spacing values to replicate
- Sidebar padding: `48px 28px 32px`
- Main content padding: `48px 56px 80px` (generous bottom padding)
- Search margin-bottom: `48px`
- Category group margin-bottom: `52px`
- Category header margin-bottom: `20px`

---

## 9. Responsive Strategy

A single breakpoint at **768px** handles the mobile adaptation.

### Desktop (>768px)
- Fixed sidebar, scrollable main content
- Search input capped at `max-width: 380px`
- Row layout: horizontal (title/desc left, buttons right)

### Mobile (<=768px)
- Sidebar becomes static header with horizontal scrolling nav
- Nav uses gradient mask for scroll hint: `linear-gradient(to right, black calc(100% - 40px), transparent)`
- Active indicator moves from left border to bottom border
- Row layout stacks vertically (title/desc above, buttons below)
- Main padding shrinks: `28px 20px 60px`
- Search input goes full width
- Category counts and total count are hidden

---

## 10. Dark Mode Implementation

### Mechanism
- CSS custom properties toggled via `[data-theme="dark"]` on `<html>`
- User preference stored in `localStorage.getItem('theme')`
- Falls back to `prefers-color-scheme: dark` media query
- Toggle button swaps between sun/moon SVG icons
- Body transition: `background 0.3s ease, color 0.3s ease`

### Dark mode rules
- Dark background is warm dark brown (`#1a1915`), NEVER pure black
- Accent shifts warmer/brighter in dark mode (sepia → gold)
- Borders get darker, not lighter
- Recessed areas are slightly lighter than background (reversed from light theme)
- Brand icons that are black on light theme use `filter: invert(1)` via `.icon-invert` class

---

## 11. Visual Signature Checklist

When building a new app with this design system, verify these hallmarks are present:

- [ ] Background is warm parchment, not white or cool gray
- [ ] Headlines use a serif font with negative letter-spacing
- [ ] Body uses a clean sans-serif (DM Sans or equivalent)
- [ ] Metadata/tags use monospace at very small sizes with reduced opacity
- [ ] Content is in **rows separated by 1px borders**, not cards with shadows
- [ ] No border-radius anywhere except tiny badges (4px max)
- [ ] Accent color is warm brown/sepia/gold, never blue or purple
- [ ] Hover states are color shifts only — no scale, no shadow, no translateY
- [ ] All transitions are 150ms ease
- [ ] Generous whitespace — content never feels cramped
- [ ] Sidebar uses left-border indicator for active state
- [ ] Expanded/detail areas use a left accent border (2px solid accent)
- [ ] Action buttons are small squares (28px) with thin borders, no fill
- [ ] Primary buttons are filled accent with inverted text, no border-radius
- [ ] Dark mode is warm (brown-black, gold accent), not cool (gray-black, cyan accent)
- [ ] No decorative elements — no gradients, blobs, shadows, or illustrations
- [ ] Overall feeling: like reading a well-designed independent publication

---

## 12. Adapting to Different App Types

This design system works for any **content-heavy, information-dense** application. When adapting:

### For a dashboard
- Replace rows with a table or data grid — still use 1px border separators, not card elevation
- Stat numbers in serif, labels in uppercase sans-serif
- Charts should be monochromatic using accent color tints

### For a blog or content site
- Article titles in Instrument Serif, body in DM Sans
- Sidebar becomes a table of contents or category index
- Pull quotes use the left accent border pattern

### For a form-heavy app
- Inputs follow the search input pattern: transparent background, bottom border only
- Labels use the sidebar-label style: tiny uppercase, secondary color
- Form sections separated by 1px borders with generous vertical spacing

### For an e-commerce or catalog
- Product listings use the row pattern, not card grids
- Filters go in the sidebar
- Price/metadata in IBM Plex Mono
- CTA buttons use the primary button style (filled accent)

### What to keep constant across all adaptations
1. The three-font system (serif titles, sans body, mono metadata)
2. The warm neutral color palette
3. 1px border separators instead of shadows
4. 150ms ease transitions
5. The left-border accent pattern for active/expanded states
6. Square, thin-bordered action buttons
7. Generous negative space

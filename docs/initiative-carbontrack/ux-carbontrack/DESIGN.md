---
name: CarbonTrack
description: Carbon footprint tracking for organizations — calm, trustworthy enterprise SaaS. React + Tailwind CSS v4 with CarbonTrack's own UI kit; tokens below are the values for the colour token names fixed in architecture AD-14.
status: final
sources:
  - ../initiative-carbontrack.md
  - ../architecture-carbontrack/architecture-carbontrack.md
updated: 2026-11-15
colors:
  # Light is the default theme; every token has a -dark twin. CSS names: --color-<key>.
  bg-canvas: '#F8FAF9'
  bg-canvas-dark: '#111827'
  bg-surface: '#FFFFFF'
  bg-surface-dark: '#1F2937'
  bg-subtle: '#F3F4F6'
  bg-subtle-dark: '#374151'
  border-default: '#E5E7EB'
  border-default-dark: '#374151'
  border-strong: '#6B7280'
  border-strong-dark: '#9CA3AF'
  text-primary: '#1F2937'
  text-primary-dark: '#F9FAFB'
  text-secondary: '#4B5563'
  text-secondary-dark: '#D1D5DB'
  text-muted: '#6B7280'
  text-muted-dark: '#9CA3AF'
  brand-primary: '#166534'
  brand-primary-dark: '#10B981'
  brand-on-primary: '#FFFFFF'
  brand-on-primary-dark: '#052E16'
  brand-accent: '#D1FAE5'
  brand-accent-dark: '#064E3B'
  brand-on-accent: '#166534'
  brand-on-accent-dark: '#D1FAE5'
  brand-secondary: '#10B981'
  brand-secondary-dark: '#34D399'
  activity-electricity: '#D97706'
  activity-electricity-dark: '#FBBF24'
  activity-fuel: '#2563EB'
  activity-fuel-dark: '#60A5FA'
  activity-business-travel: '#7C3AED'
  activity-business-travel-dark: '#A78BFA'
  status-success: '#166534'
  status-success-dark: '#34D399'
  status-danger: '#B91C1C'
  status-danger-dark: '#F87171'
  status-warning: '#B45309'
  status-warning-dark: '#FBBF24'
  focus-ring: '#166534'
  focus-ring-dark: '#10B981'
typography:
  display:
    fontFamily: 'Inter'
    fontSize: 40px
    fontWeight: '600'
    lineHeight: 48px
    letterSpacing: -0.01em
  h1:
    fontFamily: 'Inter'
    fontSize: 28px
    fontWeight: '600'
    lineHeight: 36px
    letterSpacing: -0.01em
  h2:
    fontFamily: 'Inter'
    fontSize: 20px
    fontWeight: '600'
    lineHeight: 28px
  h3:
    fontFamily: 'Inter'
    fontSize: 16px
    fontWeight: '600'
    lineHeight: 24px
  body:
    fontFamily: 'Inter'
    fontSize: 14px
    fontWeight: '400'
    lineHeight: 20px
  body-sm:
    fontFamily: 'Inter'
    fontSize: 13px
    fontWeight: '400'
    lineHeight: 18px
  label:
    fontFamily: 'Inter'
    fontSize: 14px
    fontWeight: '500'
    lineHeight: 20px
  label-sm:
    fontFamily: 'Inter'
    fontSize: 13px
    fontWeight: '500'
    lineHeight: 18px
  caption:
    fontFamily: 'Inter'
    fontSize: 12px
    fontWeight: '400'
    lineHeight: 16px
rounded:
  sm: 6px
  md: 8px
  lg: 12px
  xl: 16px
  full: 9999px
spacing:
  xs: 4px
  sm: 8px
  md: 12px
  lg: 16px
  xl: 24px
  2xl: 32px
  sidebar-width: 248px
  topbar-height: 64px
  content-padding: 32px
  side-panel-width: 440px
  dialog-width: 520px
components:
  button-primary:
    background: '{colors.brand-primary}'
    foreground: '{colors.brand-on-primary}'
    radius: '{rounded.md}'
    padding: '{spacing.sm} {spacing.lg}'
    font: '{typography.label}'
  button-secondary:
    background: '{colors.bg-surface}'
    foreground: '{colors.text-primary}'
    border: '1px {colors.border-default}'
    radius: '{rounded.md}'
  button-danger-text:
    foreground: '{colors.status-danger}'
  nav-item-active:
    background: '{colors.brand-accent}'
    foreground: '{colors.brand-on-accent}'
    radius: '{rounded.md}'
  nav-item:
    foreground: '{colors.text-secondary}'
    icon: '{colors.text-muted}'
  card:
    background: '{colors.bg-surface}'
    border: '1px {colors.border-default}'
    radius: '{rounded.lg}'
    shadow: 'card'
  input:
    background: '{colors.bg-surface}'
    border: '1px {colors.border-strong}'
    radius: '{rounded.md}'
    focus: '2px {colors.focus-ring}'
  type-badge:
    background: '{colors.bg-subtle}'
    dot: '{colors.activity-electricity} | {colors.activity-fuel} | {colors.activity-business-travel}'
    radius: '{rounded.full}'
    font: '{typography.label-sm}'
  insight-banner:
    background: '{colors.brand-accent}'
    foreground: '{colors.brand-on-accent}'
    radius: '{rounded.lg}'
  table-row-selected:
    background: '{colors.brand-accent}'
  side-panel:
    background: '{colors.bg-surface}'
    width: '{spacing.side-panel-width}'
    shadow: 'overlay'
  dialog:
    background: '{colors.bg-surface}'
    width: '{spacing.dialog-width}'
    radius: '{rounded.xl}'
    shadow: 'overlay'
    scrim: 'rgba(17, 24, 39, 0.45)'
---

## Brand & Style

CarbonTrack helps a sustainability manager see, in seconds, how much carbon her organization emits and where it comes from. It must feel **calm, professional, modern and trustworthy**: an enterprise sustainability platform, never a student dashboard. The references are Stripe's clean data presentation, Linear's minimal navigation and Vercel's simple dashboards and confident typography. Values to communicate: sustainability, transparency, trust, environmental responsibility.

Green is the brand, not the wallpaper. Neutral off-white canvas and white surfaces carry the interface; Forest Green marks the one main action and the current place; colour elsewhere means data.

**Logo — "Rising bars"** (concept A): three rising bars with a leaf on the tallest, followed by the wordmark *CarbonTrack* in Inter Semi Bold with *Track* in `{colors.brand-primary}`. Dark version: bars and leaf in emerald tones, wordmark white with *Track* in `{colors.brand-primary-dark}`. Sidebar size: 28 px mark, 18 px wordmark. Source vectors: Figma, Logo options, concept A.

Figma reference: [CarbonTrack — UX (CP4)](https://www.figma.com/design/jM5YcK7kvfKwESlMbh9ZQh) (Screens, Components, Round 1 · Options). This document wins over any frame.

## Colors

- **Forest Green** (`{colors.brand-primary}`, #166534) is the brand and the action colour in light mode: primary buttons, links, focus rings, the selected page, the current calculation. It passes 7.1:1 on white.
- **Emerald** (`{colors.brand-secondary}`, #10B981) is a highlight, not a text or button colour, in light mode: it fails contrast on white (2.5:1), so it appears only as fills with dark text on top and in the logo. In **dark mode emerald becomes the primary** (`{colors.brand-primary-dark}`, 5.8:1 on charcoal surfaces) and Forest Green is never used as a foreground on dark (2.1:1).
- **Soft Mint** (`{colors.brand-accent}`, #D1FAE5) marks "this is the current or important thing": the active nav item, the selected table row, the insight banner, success chips. Text on mint is Forest Green (6.3:1).
- **Off-white canvas** (#F8FAF9) and **white surfaces** carry everything; **Dark Charcoal** (#1F2937) is the text colour (14:1 on the canvas). In dark mode the canvas is #111827 and surfaces are #1F2937.
- **Activity types** (palette A, "distinct"): electricity amber `{colors.activity-electricity}`, fuel blue `{colors.activity-fuel}`, business travel violet `{colors.activity-business-travel}`, with lighter dark-mode twins. Every one passes 3:1 against its background in both themes. They appear only where a type is meant: chart series, type badges, legend dots. They are never decoration.
- **Status**: success reuses Forest Green; danger #B91C1C / #F87171 (6.5:1 / 5.3:1); warning #B45309 / #FBBF24.
- **Borders**: `{colors.border-default}` for decorative card and table lines; `{colors.border-strong}` (4.8:1 light, 5.8:1 dark) wherever a border is the only outline of a control — inputs, filter chips, the search box.

Avoid: an all-green interface, gradients, green chart series (green means brand, not a data type), colour as the only cue for anything.

## Typography

**Inter** throughout, in one ramp: Display 40, H1 28, H2 20, H3 16, Body 14, Body small 13, Label 14 / 13 medium, Caption 12. Headings are Semi Bold (600) with slight negative tracking at 28 px and above; labels are Medium (500); body is Regular (400).

Figures use Inter's tabular numerals so columns of kg CO2e line up; emissions in tables are right-aligned. Units are set smaller and muted next to the figure ("98.60 t CO2e").

## Layout & Spacing

A 4-based scale: 4, 8, 12, 16, 24, 32. The app shell is a 248 px sidebar, a 64 px top bar and a content area with 32 px padding on the off-white canvas. Content is full width within the shell (data products need width); cards and tables span it, with 16–24 px gaps. The row side panel is 440 px and slides over the right edge; dialogs are 520 px wide and centred. Breakpoint behaviour lives in EXPERIENCE.md.

## Elevation & Depth

Two shadows only. **Card**: a 1 px, 6% charcoal shadow with a 1 px border — barely lifted, Stripe-like. **Overlay**: 12 px offset, 32 px blur, 18% — for the side panel, dialogs, menus and toasts. Dialogs sit on a 45% charcoal scrim; the side panel has no scrim so the list stays readable. No other elevation.

## Shapes

Radius `{rounded.md}` (8 px) for buttons, inputs, chips and nav items; `{rounded.lg}` (12 px) for cards, tables and the insight banner; `{rounded.xl}` (16 px) for dialogs; `{rounded.full}` for type badges, avatars and status dots. Charts: 3–4 px top corners on the top segment of a stacked bar only.

## Components

- **Button** — Primary: Forest Green fill, white Semi Bold label, optional leading icon; one per view. Secondary: white with a default border. Danger action: secondary button with danger-coloured label and icon (Delete). Height 40 px.
- **Nav item** — 40 px, icon + label; active state uses the mint fill and Forest Green icon and label.
- **Card / stat** — white surface, 12 px radius, card shadow; label in muted label-sm, figure in H1/Display weight with a muted unit.
- **Insight banner** — mint fill with a hairline mint-green border, a white icon tile, an H2 sentence naming the largest source and its share, a body line with the figures, and a white secondary link button.
- **Type badge** — subtle grey pill, 8 px coloured dot and the type's name; colour never stands alone.
- **Table** — subtle grey header row (label-sm Semi Bold, sort arrow on the sorted column), 48 px rows with a bottom hairline, selected row in mint, overflow menu (⋯) at the end, pagination footer with a numbered pager (current page in Forest Green).
- **Input / filter chip / search** — white, `{colors.border-strong}` outline, 8 px radius, 2 px focus ring in `{colors.focus-ring}`; chips show the filter name muted and the value in primary text.
- **Side panel** — white, overlay shadow, header with type badge, title and close; a "current calculation" block on the canvas colour; a history timeline (Forest Green dot for current, grey for earlier); actions pinned to the bottom.
- **Dialog** — 520 px, 16 px radius, H2 title and close, stacked labelled fields, footer with Cancel then the primary action on the right.
- **Charts** — stacked monthly bars and a donut (66% inner radius) in the activity colours, hairline gridlines in `{colors.bg-subtle}`, a legend with names, empty months shown as an empty slot.
- **Disclaimer** — caption text with an ⓘ, muted, under every view that shows factors or emissions.

## Do's and Don'ts

| Do | Don't |
|---|---|
| Keep surfaces neutral; let Forest Green mark the one main action and the current place | Make the interface green, or use green for a chart series |
| Use emerald as a fill under dark text in light mode, and as the primary in dark mode | Put white text on emerald, or Forest Green text on dark backgrounds |
| Pair every activity colour with its name (badge, legend, label) | Rely on colour alone to tell types apart |
| Use `{colors.border-strong}` for input and chip outlines | Outline form controls with the decorative border |
| Right-align figures in tabular numerals with a muted unit | Mix units or decimal places within a column |
| Use the two shadows only | Add new elevations, glows or gradients |
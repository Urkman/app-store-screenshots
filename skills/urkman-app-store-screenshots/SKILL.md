---
name: urkman-app-store-screenshots
description: Use when building App Store screenshot pages, generating exportable marketing screenshots for iOS apps, or creating programmatic screenshot generators with Next.js. Triggers on app store, screenshots, marketing assets, html-to-image, phone mockup.
---

# App Store Screenshots Generator

## Overview

Build a Next.js page that renders iOS App Store screenshots as **advertisements** (not UI showcases) and exports them via `html-to-image` at Apple's required resolutions. Screenshots are the single most important conversion asset on the App Store.

## Core Principle

**Screenshots are advertisements, not documentation.** Every screenshot sells one idea. If you're showing UI, you're doing it wrong — you're selling a *feeling*, an *outcome*, or killing a *pain point*.

## Step 1: Ask the User These Questions

Before writing ANY code, ask the user all of these. Do not proceed until you have answers:

### Required

1. **App screenshots** — "Where are your app screenshots? (PNG files of actual device captures)"
2. **App icon** — "Where is your app icon PNG?"
3. **Brand colors** — "What are your brand colors? (accent color, text color, background preference)"
4. **Font** — "What font does your app use? (or what font do you want for the screenshots?)"
5. **Feature list** — "List your app's features in priority order. What's the #1 thing your app does?"
6. **Number of slides** — "How many screenshots do you want? (Apple allows up to 10)"
7. **Style direction** — "What style do you want? Examples: warm/organic, dark/moody, clean/minimal, bold/colorful, gradient-heavy, flat. Share App Store screenshot references if you have any."

### Optional

8. **Component assets** — "Do you have any UI element PNGs (cards, widgets, etc.) you want as floating decorations? If not, that's fine — we'll skip them."
9. **Additional instructions** — "Any specific requirements, constraints, or preferences?"

### Derived from answers (do NOT ask — decide yourself)

Based on the user's style direction, brand colors, and app aesthetic, decide:
- **Background style**: gradient direction, colors, whether light or dark base
- **Decorative elements**: blobs, glows, geometric shapes, or none — match the style
- **Dark vs light slides**: how many of each, which features suit dark treatment
- **Typography treatment**: weight, tracking, line height — match the brand personality
- **Color palette**: derive text colors, secondary colors, shadow tints from the brand colors

**IMPORTANT:** If the user gives additional instructions at any point during the process, follow them. User instructions always override skill defaults.

## Step 2: Set Up the Project

### Detect Package Manager

Check what's available, use this priority: **bun > pnpm > yarn > npm**

```bash
# Check in order
which bun && echo "use bun" || which pnpm && echo "use pnpm" || which yarn && echo "use yarn" || echo "use npm"
```

### Scaffold (if no existing Next.js project)

```bash
# With bun:
bunx create-next-app@latest . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
bun add html-to-image

# With pnpm:
pnpx create-next-app@latest . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
pnpm add html-to-image

# With yarn:
yarn create next-app . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
yarn add html-to-image

# With npm:
npx create-next-app@latest . --typescript --tailwind --app --src-dir --no-eslint --import-alias "@/*"
npm install html-to-image
```

### Device Framing

The skill includes a phone mockup at `mockup.png`, but do **not** assume it is export-safe. Check whether the screen area is transparent before using it.

Rules:
- If the PNG has an opaque black screen, do **not** layer screenshots behind it. Build the phone frame in CSS instead.
- Prefer CSS-built phone/watch frames when using `html-to-image`; they are easier to scale, easier to tune, and less likely to export with black screens.
- For Apple Watch slides, prefer real portrait watch renders (device + band included) when the user has them. Do not put a watch screenshot inside another synthetic watch frame if a fully framed render already exists.

### File Structure

```
project/
├── public/
│   ├── mockup.png              # Phone frame (included with skill)
│   ├── app-icon.png            # User's app icon
│   └── screenshots/            # User's app screenshots
│       ├── home.png
│       ├── feature-1.png
│       └── ...
├── src/app/
│   ├── layout.tsx              # Font setup
│   └── page.tsx                # The screenshot generator (single file)
└── package.json
```

**The entire generator is a single `page.tsx` file.** No routing, no extra layouts, no API routes.

### Font Setup

```tsx
// src/app/layout.tsx
import { YourFont } from "next/font/google"; // Use whatever font the user specified
const font = YourFont({ subsets: ["latin"] });

export default function Layout({ children }: { children: React.ReactNode }) {
  return <html><body className={font.className}>{children}</body></html>;
}
```

## Step 3: Plan the Slides

### Screenshot Framework (Narrative Arc)

Adapt this framework to the user's requested slide count. Not all slots are required — pick what fits:

| Slot | Purpose | Notes |
|------|---------|-------|
| #1 | **Hero / Main Benefit** | App icon + tagline + home screen. This is the ONLY one most people see. |
| #2 | **Differentiator** | What makes this app unique vs competitors |
| #3 | **Ecosystem** | Widgets, extensions, watch — beyond the main app. Skip if N/A. |
| #4+ | **Core Features** | One feature per slide, most important first |
| 2nd to last | **Trust Signal** | Identity/craft — "made for people who [X]" |
| Last | **More Features** | Pills listing extras + coming soon. Skip if few features. |

**Rules:**
- Each slide sells ONE idea. Never two features on one slide.
- Vary layouts across slides — never repeat the same template structure.
- Include 1-2 contrast slides (inverted bg) for visual rhythm.

## Step 4: Write Copy FIRST

Get all headlines approved before building layouts. Bad copy ruins good design.

### The Iron Rules

1. **One idea per headline.** Never join two things with "and."
2. **Short, common words.** 1-2 syllables. No jargon unless it's domain-specific.
3. **3-5 words per line.** Must be readable at thumbnail size in the App Store.
4. **Line breaks are intentional.** Control where lines break with `<br />`.

### Three Approaches (pick one per slide)

| Type | What it does | Example |
|------|-------------|---------|
| **Paint a moment** | You picture yourself doing it | "Check your coffee without opening the app." |
| **State an outcome** | What your life looks like after | "A home for every coffee you buy." |
| **Kill a pain** | Name a problem and destroy it | "Never waste a great bag of coffee." |

### What NEVER Works

- **Feature lists as headlines**: "Log every item with tags, categories, and notes"
- **Two ideas joined by "and"**: "Track X and never miss Y"
- **Compound clauses**: "Save and customize X for every Y you own"
- **Vague aspirational**: "Every item, tracked"
- **Marketing buzzwords**: "AI-powered tips" (unless it's actually AI)

### Copy Process

1. Write 3 options per slide using the three approaches
2. Read each at arm's length — if you can't parse it in 1 second, it's too complex
3. Check: does each line have 3-5 words? If not, adjust line breaks
4. Present options to the user with reasoning for each

### Reference Apps for Copy Style

- **Raycast** — specific, descriptive, one concrete value per slide
- **Turf** — ultra-simple action verbs, conversational
- **Mela / Notion** — warm, minimal, elegant

## Step 5: Build the Page

### Architecture

```
page.tsx
├── Constants (W, H, SIZES, design tokens from user's brand)
├── Phone component (mockup with screen overlay)
├── Caption component (label + headline)
├── Decorative components (blobs, glows, shapes — based on style direction)
├── Screenshot1..N components (one per slide)
├── SCREENSHOTS array (registry)
├── ScreenshotPreview (ResizeObserver scaling + hover export)
└── ScreenshotsPage (grid + toolbar + export logic)
```

### Export Sizes (Apple Required — iPhone only, portrait)

```typescript
const SIZES = [
  { label: '6.9"', w: 1320, h: 2868 },
  { label: '6.5"', w: 1284, h: 2778 },
  { label: '6.3"', w: 1206, h: 2622 },
  { label: '6.1"', w: 1125, h: 2436 },
] as const;
```

Design at the LARGEST size (1320x2868) and scale down for export.

### Rendering Strategy

Each screenshot is designed at full resolution (1320x2868px). Two copies exist:

1. **Preview**: CSS `transform: scale()` via ResizeObserver to fit a grid card
2. **Export**: Offscreen at `position: absolute; left: -9999px` at true resolution

### Phone Mockup Component

The included `mockup.png` has these pre-measured values:

```typescript
const MK_W = 1022;  // mockup image width
const MK_H = 2082;  // mockup image height
const SC_L = (52 / MK_W) * 100;   // screen left offset %
const SC_T = (46 / MK_H) * 100;   // screen top offset %
const SC_W = (918 / MK_W) * 100;  // screen width %
const SC_H = (1990 / MK_H) * 100; // screen height %
const SC_RX = (126 / 918) * 100;  // border-radius x %
const SC_RY = (126 / 1990) * 100; // border-radius y %
```

```tsx
function Phone({ src, alt, style, className = "" }: {
  src: string; alt: string; style?: React.CSSProperties; className?: string;
}) {
  return (
    <div className={`relative ${className}`}
      style={{ aspectRatio: `${MK_W}/${MK_H}`, ...style }}>
      <img src="/mockup.png" alt=""
        className="block w-full h-full" draggable={false} />
      <div className="absolute z-10 overflow-hidden"
        style={{
          left: `${SC_L}%`, top: `${SC_T}%`,
          width: `${SC_W}%`, height: `${SC_H}%`,
          borderRadius: `${SC_RX}% / ${SC_RY}%`,
        }}>
        <img src={src} alt={alt}
          className="block w-full h-full object-cover object-top"
          draggable={false} />
      </div>
    </div>
  );
}
```

### Typography (Resolution-Independent)

All sizing relative to canvas width W:

| Element | Size | Weight | Line Height |
|---------|------|--------|-------------|
| Category label | `W * 0.028` | 600 (semibold) | default |
| Headline | `W * 0.09` to `W * 0.1` | 700 (bold) | 1.0 |
| Hero headline | `W * 0.1` | 700 (bold) | 0.92 |

### Phone Placement Patterns

Vary across slides — NEVER use the same layout twice in a row:

**Centered phone** (hero, single-feature):
```
bottom: 0, width: "82-86%", translateX(-50%) translateY(12-14%)
```

**Two phones layered** (comparison):
```
Back: left: "-8%", width: "65%", rotate(-4deg), opacity: 0.55
Front: right: "-4%", width: "82%", translateY(10%)
```

**Phone + floating elements** (only if user provided component PNGs):
```
Cards should NOT block the phone's main content.
Position at edges, slight rotation (2-5deg), drop shadows.
If distracting, push partially off-screen or make smaller.
```

### "More Features" Slide (Optional)

Dark/contrast background with app icon, headline ("And so much more."), and feature pills. Can include a "Coming Soon" section with dimmer pills.

## Step 6: Export

### Why html-to-image, NOT html2canvas

`html2canvas` breaks on CSS filters, gradients, drop-shadow, backdrop-filter, and complex clipping. `html-to-image` uses native browser SVG serialization — handles all CSS faithfully.

### Export Implementation

```typescript
import { toJpeg } from "html-to-image";

// Before capture: move element on-screen
el.style.left = "0px";
el.style.opacity = "1";
el.style.zIndex = "-1";

const opts = {
  width: W,
  height: H,
  pixelRatio: 1,
  cacheBust: true,
  backgroundColor: "#ffffff",
  canvasWidth: W,
  canvasHeight: H,
};

// JPEG guarantees there is no alpha channel in the exported App Store asset.
await toJpeg(el, { ...opts, quality: 1 });
const dataUrl = await toJpeg(el, { ...opts, quality: 1 });

// After capture: move back off-screen
el.style.left = "-9999px";
el.style.opacity = "";
el.style.zIndex = "";
```

### Key Rules

- **Do not export transparent assets.** App Store screenshots must be fully opaque. Put a real background color or gradient on the root slide node and do not rely on transparency anywhere in the export path.
- **Default to JPEG for App Store exports.** JPEG has no alpha channel, so it is the safest output format for App Store screenshots. Use quality `1` or `0.98`.
- **Do not spam browser downloads** for full sets. Browsers will often drop files. Export all screenshots into a **single zip archive** instead. Use individual downloads only for one-off slide exports.
- **Wait for image decode before export.** Before each `toJpeg()` or final render call, wait for all `img` elements inside the slide to finish loading and decoding.
- **Inline raster assets for export.** Convert screenshot PNGs to data URLs before rendering them in exportable slides. This avoids `html-to-image` failures on larger outputs, especially 6.9-inch exports.
- **Double-call trick** is optional once assets are inlined and decoded. If exports are still flaky, first fix image loading rather than stacking more retries.
- **On-screen for capture**: Temporarily move to `left: 0` before calling the exporter only if your export path requires it. Avoid unnecessary layout tricks if a simpler export works.
- **Offscreen container**: Use `position: absolute; left: -9999px` (not `fixed`) when staging export nodes.
- **If you must ship PNGs, flatten them first.** Draw the exported image onto an opaque canvas filled with a solid background color before writing the final file, or run a local post-process that strips alpha after export.
- **Resizing**: Load data URL into Image, draw onto an opaque canvas at target size if you need extra resampling control.
- Set `fontFamily` on the export node.
- **Numbered filenames**: Prefix exports with zero-padded index so they sort correctly: `01-hero-1320x2868.jpg`, `02-freshness-1320x2868.jpg`, etc. Use `String(index + 1).padStart(2, "0")`.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| All slides look the same | Vary phone position (center, left, right, two-phone, no-phone) |
| Decorative elements invisible | Increase size and opacity — better too visible than invisible |
| Copy is too complex | "One second at arm's length" test |
| Floating elements block the phone | Move off-screen edges or above the phone |
| Plain white/black background | Use gradients — even subtle ones add depth |
| Too cluttered | Remove floating elements, simplify to phone + caption |
| Too simple/empty | Add larger decorative elements, floating items at edges |
| Headlines use "and" | Split into two slides or pick one idea |
| No visual contrast across slides | Mix light and dark backgrounds |
| Export is blank | Use double-call trick; move element on-screen before capture |

## Practical Layout Guidance

These rules came from real export/debug iterations and should be followed by default:

- **Make key layout values configurable at the top of the file.** Do not hard-code sizes deep in the layout tree. At minimum expose constants for:
  - kicker font size
  - headline font size
  - body font size
  - text column max width
  - body text max width
  - hero device scale
  - hero vertical offset for special slides (for example Apple Watch)
- **Do not leave developer helper text in export layouts.** Any instructional text like “assets are loaded from public/raw” must stay out of exported screenshots.
- **Do not keep decorative note badges by default.** Pills in the top-right corner often look like stray UI chrome in final App Store assets.
- **Support-heavy slides need their own layout.** If a slide combines one hero device with 2-3 supporting screenshots, do not force it through the same layout as simple single-device slides. Create a slide-specific composition.
- **For supporting images, prefer direct crops over nested device mockups.** Small support cards should usually show the screenshot itself, not a tiny device frame inside another card.
- **If a hero device looks visually off-center, center it across the full lower canvas, not just within a right-hand content column.**
- **Avoid muddy gray rectangles behind devices.** Large shadows, translucent backdrop cards, and placeholder columns often export as dirty panels. Use cleaner surfaces and fewer large shadows.

## Apple Watch Guidance

When the user provides Apple Watch screenshots:
- Prefer the portrait renders that already include the watch body and band.
- Make the hero watch noticeably larger than the supporting watches.
- Keep the supporting watch images in a row beneath the hero watch.
- Expose the hero watch offset and scale as constants so the user can nudge the composition without searching through JSX.

## Recommendations To Add To The Generator

When building a reusable screenshot generator, include these controls in the page itself when feasible:
- export only one device size
- export all sizes into a zip
- optionally export only selected slides

These controls remove a lot of manual iteration friction during App Store polish.

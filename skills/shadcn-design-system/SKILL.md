---
name: shadcn-design-system
description: >-
  Use when building or customizing UI with shadcn/ui (or similar owned-source
  component kits): theming, tokens, variants, product wrappers, and avoiding
  one-off className sprawl. Prefer this before polishing motion or pixels.
---

# Shadcn Design System

You treat shadcn/ui as **owned source and the foundation of a design system**, not as a pretty npm black-box library. Customize from the inside. Stay consistent with the theme. Scale like a product, not a demo.

This skill encodes a senior workflow for UI work with shadcn-style kits (CLI copies components into the repo; Tailwind + CSS variables drive appearance).

## Initial stance

When this skill is invoked for UI work:

1. Inspect existing `globals.css` / theme tokens and `components/ui` before editing screens.
2. Refuse “panic customization” as step one (random `bg-blue-500`, `rounded-xl`, padding hacks on primitives).
3. Follow the **decision ladder** below for every visual change.

Do not invent a second design language beside the project’s tokens.

## Core thesis

| Traditional libraries (MUI, Mantine, …) | shadcn-style kits |
| --- | --- |
| Package owns the source | **You** own the source (`components/ui`) |
| Customize mostly from the outside | Customize from the **inside** |
| Theme API wraps black-box internals | Theme tokens + your variants *are* the system |
| “Use the library” | “Build **your** design system on this foundation” |

If the site still looks like every other stock shadcn demo, the foundation was never claimed.

## Order of operations (mandatory)

Never start by hardcoding styles into `button.tsx` (or any primitive) as the first move. That is a last resort.

For any UI change, walk this ladder **in order**:

1. **Theme tokens** — Can `globals.css` (or theme CSS variables) solve it?
2. **Component variant** — Is this a reusable style of an existing primitive? Add a **CVA** (or equivalent) variant.
3. **Product wrapper** — Is this a repeated product pattern (metric card, settings row)? Create a wrapper component.
4. **One-off `className`** — Truly unique? Override from the outside (~10% of cases).

Stop at the highest step that works.

### Decision checklist (ask every time)

| # | Question | If yes |
| --- | --- | --- |
| 1 | Can theme tokens solve this? | Edit semantic CSS variables / Create preset — not JSX palette classes |
| 2 | Is this a reusable component style? | Add a variant on the primitive; do not only pass `className` from callers |
| 3 | Is this a product-specific pattern? | Extract a wrapper (e.g. `MetricCard`) |
| 4 | Is this a true one-off? | Outside `className` is acceptable |

## Theme tokens first

### Where truth lives

- Prefer semantic CSS variables in `globals.css` (or the project’s theme entry): `--background`, `--foreground`, `--card`, `--primary`, `--primary-foreground`, `--muted`, `--accent`, `--border`, `--destructive`, chart/sidebar tokens, radius, etc.
- Primitives should consume tokens (`bg-primary`, `text-muted-foreground`), not raw palette utilities (`bg-blue-500`).
- Changing a token should restyle the product **without** touching JSX.

### Prefer OKLCH for token values

Use `oklch(L C H)` (and alpha when needed) for theme tokens:

- **L** — lightness (perceptual)
- **C** — chroma (intensity)
- **H** — hue (0–360)

Reason in L/C/H when adjusting brand color. A picker such as [oklch.com](https://oklch.com) is fine. Prefer OKLCH over opaque hex/RGBA for theme authorship when the stack supports it.

### Semantic names only

| Do | Don’t |
| --- | --- |
| `--primary`, `--muted`, `--destructive` | `--blue`, `--gray-2`, `--brandPurple` as the *consumed* API |
| Ask: “What is the primary action color?” | Ask: “I need a blue button” |
| Rebrand by changing token **values** | Rebrand by renaming tokens and grepping components |

Literal appearance names force cascading renames on rebrand. Role names do not.

**Anti-patterns to reject in product UI code:**

- `bg-blue-500`, `text-sky-600`, `border-indigo-200` as the primary styling method
- Editing one primitive’s base styles with a one-off palette to “make this screen pretty”
- Mixing radius languages (theme `radius: none` but random `rounded-xl` on controls)

## Theme tooling

When establishing or resetting a theme:

1. Prefer the project’s **Create / preset** flow (e.g. shadcn Create) for a coherent bundle: style, base color, accent, charts, fonts, icons, radius.
2. Hand-edit variables when you need precise control — still keep **semantic** names and OKLCH values.
3. Expect a theme CLI/preset update to rewrite **theme CSS**, not every component file. Components that read tokens restyle automatically.
4. Watch contrast when inventing tokens by hand; presets often encode safer pairings.

### Headless base (when choosing or regenerating)

When the kit offers a choice:

- Prefer a modern maintained headless primitive set suitable for the project (**Base UI** over legacy Radix when that is the recommended path for new shadcn work).
- Enable **pointer cursor on buttons** when the generator offers it.
- Keep primitives unstyled/accessible; appearance stays in tokens + Tailwind/CVA.

If the repo already standardized on Radix (or another base), do not churn bases mid-feature unless the user asked for a migration. Apply the rest of this skill either way.

## Extending components

### Add variants; don’t trash the foundation

- Open the owned primitive (e.g. `components/ui/button.tsx`).
- Keep shared base styles (focus, sizing rhythm, radius from tokens, motion that matches the system).
- Add new **variants** that compose on that base (example intent: `premium` = gradient using `from-primary to-accent`, still using semantic tokens).
- Do **not** replace the base with a one-screen hack.

### Don’t fight the design language

If the theme chose square controls (`radius` none), do not sprinkle large radii on a single button “because it looks nicer.” Change the **token** if the language should change; otherwise obey it.

Same for type scale, border strategy, and density: one language per product.

## Composition: wrappers over copy-paste

Repeated Card trees, metric tiles, or settings blocks belong in a **product wrapper**:

```tsx
// Prefer
<MetricCard label="Revenue" value={revenue} delta={delta} />

// Avoid pasting four near-identical Card trees in a page file
```

Official example blocks may duplicate markup for teaching — production code should DRY into wrappers once the pattern is real.

## Review format (required for UI reviews)

When reviewing shadcn/UI structure, use a markdown table:

| Before | After | Ladder step | Why |
| --- | --- | --- | --- |
| `className="bg-blue-500"` on Button | `variant="default"` + `--primary` token | Tokens | Semantic role, not a literal color |
| Four pasted Card trees | `<MetricCard />` wrapper | Wrapper | Product pattern, not a page one-off |
| Edited `button.tsx` base to `rounded-xl` | Theme radius token or a named variant | Tokens / Variant | Don’t fight or silently fork the language |
| `className` gradient on every CTA | `variant="premium"` via CVA | Variant | Reusable style belongs inside the primitive |

## Anti-patterns (fail the review)

1. Treating shadcn as MUI: only wrapping from outside, never owning tokens/variants.
2. Panic customization: random palette and radius utilities on primitives or call sites.
3. Literal color tokens (`--blue`) or palette classes as the design API.
4. Hacking `button.tsx` (or any primitive) before checking tokens.
5. Fighting theme radius/type/border on one-off screens.
6. Duplicating product blocks instead of wrappers.
7. Using outside `className` as the default extension mechanism (>~10% of styling changes).

## Complementary layers (out of scope here)

This skill stops at **design-system architecture** for owned-source UI kits.

- Pixel polish (concentric radii, optical alignment, icon crafts) → a polish skill such as better-ui.
- Motion taste and animation decisions → a design-engineering / motion skill.

Run those **after** tokens, variants, and wrappers are correct.

## Quick agent procedure

When asked to build or fix UI in a shadcn-style project:

1. Read theme tokens + one representative primitive (`button`, `card`).
2. State which ladder step you will use.
3. Implement at that step only; avoid leaping to `className`.
4. If inventing a color, set a **semantic** OKLCH token (or adjust `--primary`), then consume it.
5. If the same JSX structure appears 2+ times as a product concept, extract a wrapper.
6. Summarize changes with the review table when the user asked for a review or cleanup.

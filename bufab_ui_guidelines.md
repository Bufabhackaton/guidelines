# Bufab UI Design Guidelines
**Version:** 2.1  
**Design language:** Scandinavian Industrial Corporate  
**Reference site:** bufab.com  
**Companion file:** bufab_ui_guidelines.json (v2.0)

---

## How to read this document

This document has two audiences.

**For humans (developers and designers):** Read it once when joining the project. It explains not just what the rules are but why they exist. When you are unsure whether something is correct, this document should answer it in plain language.

**For AI agents (Claude via MCP):** Relevant sections are injected into context automatically by the MCP server depending on which tool is being called. You do not need to read the whole document — the server loads what is needed for each task.

A companion JSON file (`bufab_ui_guidelines.json`) holds the machine-readable version of all rules: exact token values, field schemas, and structural constraints. The JSON is queried programmatically by MCP tools. This document explains the reasoning behind those values and handles the judgment calls that cannot be encoded in JSON.

**When in doubt, apply this test:** Would this look at home on a manufacturing company's annual report website? If the answer is no, it is wrong.

---

## Part 1 — Design philosophy

Understanding why the rules exist prevents you from satisfying the letter of a rule while breaking its spirit.

### Content-first means the UI is invisible

The design should not call attention to itself. If a reviewer notices the UI before reading the content, something is wrong. This eliminates decorative elements, large icons, animated transitions, and anything that exists for visual interest rather than information delivery. Every element on the page must earn its place by helping the user understand or act on something.

### Dense but structured is not the same as cluttered

Bufab's pages are information-rich without feeling crowded. The mechanism is consistent spacing, clear typographic hierarchy, and section separators — not whitespace. SaaS sites use whitespace as a design element to feel premium. Bufab uses structure instead. Section vertical padding is capped at 64px. Anything above that reads as a startup.

### Restraint is a trust signal

Industrial B2B buyers — procurement managers, supply chain engineers — are skeptical of marketing. A restrained, functional interface communicates that the company is serious and reliable. Gradients, animations, and decorative elements undermine this signal even when they are aesthetically well-executed. The absence of flourish is the design statement.

### The page shell is fixed

Every page follows the same wrapper, in order: **header → hero → content sections → footer**. The header is always the first element. The footer is always the last. There is no utility bar, secondary navigation bar, or market selector strip above the header. The header stands alone at the top.

---

## Part 2 — Header rules

The header is the one component with the most specific technical enforcement because it was previously implemented incorrectly.

### Always dark, never white

The header background is always `#0D3349`. It does not change when the user scrolls down. It does not turn white, light grey, or transparent at any scroll position. It is `position: sticky` so it follows the user down the page, and it stays dark the entire time.

**The correct CSS is one rule:**
```css
header {
  background: #0D3349;
  position: sticky;
  top: 0;
  z-index: 100;
}
```

That is all. There is no scroll event listener. There is no JavaScript class toggle. There is no `.scrolled`, `.white-header`, `.is-sticky`, or any other class that conditionally changes the header's appearance. If any such listener or class exists in the codebase, remove it entirely.

**Why:** A dark-to-white header transition on scroll is a SaaS UI pattern. It was introduced on many startup sites as a way to give the hero section a "clean" look before the header becomes visible as a navigation element. It does not belong on an industrial corporate site. The Bufab header is a permanent fixture, always visible, always the same.

### Logo always white

Because the background never changes, the logo is always displayed in its white variant. There is no need for a dark variant of the logo in the header. If the codebase contains logic to swap between a white and dark logo based on scroll position, remove it.

### Structure and fields

The header contains three elements only: logo (left-aligned), primary navigation (right-aligned, horizontal), and a "Contact us" CTA button (far right, orange, `border-radius: 0px`). Height is 64px. Navigation items are 15px, weight 500, white text, with dropdown support where needed. No hamburger menu on desktop.

A subtle `border-bottom: 1px solid rgba(255,255,255,0.1)` is acceptable to visually separate the header from content when scrolled, but it must be white and semi-transparent — never a dark line or a colored separator.

---

## Part 3 — Color rules

For exact hex values, query `get_token(name)` from the JSON. This section explains what each token is for and what counts as a violation.

### Token purposes

**`primary` (#0D3349)** — The brand's dark teal-blue. Used for the header background, footer background, section headings, and secondary button borders. This is the most structurally important color on the page.

**`accent` (#E8610A)** — Orange. Used exclusively for CTA buttons. This is the only place it appears. Not for borders, not for headings, not for hover states on other elements, not for decorative highlights, not for icons.

**`background` (#FFFFFF)** — Default section background. Most content sections sit on white.

**`surface-alt` (#F4F6F8)** — Very light grey. Alternate section background. Sections must alternate between `background` and `surface-alt` so there is a clear visual rhythm down the page. Two consecutive white sections or two consecutive grey sections are a warning.

**`text-primary` (#1A1A1A)** — Near-black. All headings and body text on light backgrounds.

**`text-secondary` (#5C6B7A)** — Muted grey-blue. Labels, captions, secondary descriptions, column separators text.

**`border` (#D0D7DE)** — Used only for dividers and column separators. Not for component outlines.

**`overlay` (rgba(0,0,0,0.45))** — The dark overlay applied over hero background images. Minimum 0.45 opacity. Can go darker if the image is particularly bright, but never lighter.

### Rule COLOR-01 [blocker] — No gradients

No gradients anywhere. Not in backgrounds, button fills, text, borders, or SVG fills. The single permitted exception is a `linearGradient` inside a technical SVG diagram where it represents a continuous physical property — for example, a temperature gradient in a supply chain diagram. Any other use is a violation.

Gradients are the single strongest visual signal of startup or SaaS aesthetic. One gradient anywhere on the page reads as a category error.

### Rule COLOR-02 [blocker] — Accent color is for CTA buttons only

`#E8610A` appears only as a button background. If it appears anywhere else — heading color, border, icon fill, background strip, hover highlight — it is a violation. The orange works as a conversion signal precisely because it appears nowhere else on the page.

### Rule COLOR-03 [blocker] — No colors outside the token set

No custom blues, purples, greens, or reds introduced for visual variety. If a color is not in the token set defined in the JSON, it does not belong on the page.

### Rule COLOR-04 [warning] — Sections must alternate backgrounds

A `background` (#FFFFFF) section must be followed by a `surface-alt` (#F4F6F8) section, and vice versa. Two consecutive same-background sections are a warning unless they are separated by the hero (which is dark and full-bleed and resets the alternation).

---

## Part 4 — Layout and spacing rules

### Rule LAYOUT-01 [blocker] — Hero text is left-aligned

The hero text block is left-aligned, max-width 600px, positioned over a dark-overlaid full-bleed photograph. A centered hero is the single most reliable indicator of startup-style UI. Reject it immediately.

### Rule LAYOUT-02 [blocker] — No cards outside the industries grid

A card is defined as any element that combines: a background fill + a border or shadow + padding, used to contain a discrete piece of content. Value propositions, insights, services, and features must never be presented in cards. Use column separators (1px vertical rule) or plain spacing instead.

Cards are the default component of SaaS design systems. Their presence immediately signals the wrong visual category. The most common violation is wrapping value-proposition columns in `<div>` elements with `background: #F4F6F8` and `border-radius: 8px`. Remove the background, remove the border-radius, remove the containing div. Add a `border-right: 1px solid #D0D7DE` to the column element instead.

The only section where tile or card layout is permitted is `industries-grid`.

### Rule LAYOUT-03 [blocker] — Border radius maximum 2px

No `border-radius` above 2px anywhere on the page except inside `industries-grid` tiles where up to 4px is permitted. Buttons are `border-radius: 0px`. Images have no border-radius. Section containers have no border-radius.

A border-radius of 8px or above reads as a consumer app. Square corners are a deliberate signal of industrial seriousness.

### Rule LAYOUT-04 [warning] — Section padding maximum 64px

Section `padding-top` and `padding-bottom` must not exceed 64px. Anything above 64px creates the airy SaaS spacing that conflicts with the dense-but-structured principle. The base unit is 8px. Work in multiples: 32px, 40px, 48px, 64px.

### Rule LAYOUT-05 [warning] — Contained width

All content sits in a container with `max-width: 1200px; margin: 0 auto`. Content must not span the full viewport width on large screens. Full-bleed is permitted only for the hero background image and dark section backgrounds — never for content itself.

---

## Part 5 — Typography rules

### Rule TYPE-01 [blocker] — System font stack only

Font family must be `"Helvetica Neue", Helvetica, Arial, sans-serif`. No Google Fonts. No `@font-face` imports. No variable fonts. No web font CDN links. This is intentional — system fonts load instantly, work offline, and feel native to the industrial corporate register rather than tech-product-y.

### Rule TYPE-02 [blocker] — Permitted weights are 400 and 600 only

Weight 400 for body text. Weight 600 for headings. Weight 500 is acceptable for navigation items and small labels. No weight 700 or above (too heavy). No weight 300 or below (too thin, reads as luxury/fashion aesthetic).

### Rule TYPE-03 [warning] — H1 maximum 48px

H1 is 40–48px. Oversized hero text above 48px is a startup pattern — it prioritizes drama over information. Bufab headings are authoritative, not theatrical.

### Rule TYPE-04 [warning] — Body text minimum 16px

Body text is 16px, weight 400, line-height 1.65. Do not compress body text below 16px for density. Compressed body text reads as low-quality and inaccessible.

---

## Part 6 — Component rules with correct and incorrect patterns

### Header

**Correct:** Dark background (`#0D3349`) at all scroll positions. Logo left, white variant. Navigation right-aligned, horizontal. Orange CTA button far right. Height 64px. Sticky.

**Incorrect — reject these patterns:**
- Header turns white or light grey when user scrolls down
- JavaScript scroll listener that adds a class to change header appearance
- Logo switches between white and dark variants based on scroll
- Hamburger menu visible on desktop
- Any second navigation bar or utility strip above the header

### Hero

**Correct:** Full-bleed dark-overlaid industrial photograph. Text block left-aligned, max-width 600px. H1 (max 2 lines), one body paragraph (2–3 sentences), one orange CTA button.

**Incorrect — reject these patterns:**
- Centered text layout
- Two CTA buttons in the hero
- `<h2>` or styled subtitle element used instead of a `<p>` body paragraph
- Background color instead of photograph
- Eyebrow text in a colored badge or pill — plain uppercase text only if used at all
- Any animation, parallax, or scroll effect on the hero

### Value-columns

**Correct:** H2 above. Three or four equal columns. Each column has a short bold label and 2–3 sentences of description. Columns separated by `border-right: 1px solid #D0D7DE`. No icons. No per-column background.

**Incorrect — reject these patterns:**
- Icons above each column label
- Any card container per column (background fill + padding + border-radius)
- Fewer than 3 or more than 4 columns
- Bullet lists inside column descriptions — use prose sentences

### Text-image-split

**Correct:** Text block at 55% width, image at 45% width. Neither element has a wrapping container. Image displayed as plain `<img>` or `background-image`, no border-radius, no shadow. Text link (not button) below body text, with `→` prefix.

**Incorrect — reject these patterns:**
- Image inside a styled container or card
- Drop shadow on the image
- `border-radius` above 2px on the image
- A button where the text link should be

### Diagram-section

**Correct:** Heading and bullet list (max 4 items, verb-first phrasing) on the left. SVG or high-resolution technical diagram on the right (40–50% width). Text link below the bullets. Alternating background with adjacent section.

**Incorrect — reject these patterns:**
- Decorative icons in the bullet list — plain markers only
- Illustration or stock image used instead of a technical diagram
- More than 4 bullet items

### Industries-grid

**Correct:** 3 or 4 column grid. Each tile has a real industry photograph and an industry name. Short description is optional. Tiles may have up to 4px border-radius. Subtle 1px border is acceptable.

**Incorrect — reject these patterns:**
- Heavy drop shadows on tiles
- Border-radius above 4px
- Placeholder or stock lifestyle images

### Insights-list

**Correct:** H2 above. Articles listed as plain `<a>` links styled at H3 weight (18–20px, weight 600), stacked vertically. 4–6 articles. One "Explore all" text link below. No thumbnails. No dates. No per-article card.

**Incorrect — reject these patterns:**
- Thumbnail images next to article titles
- Card layout per article with background fill
- Date or author metadata shown in list view
- "Read more" button per article — the title itself is the link

### Text-CTA-block

**Correct:** One sentence (20–24px, weight 500). One orange CTA button. Background `surface-alt`. Padding 48px vertical. Left or centered alignment.

**Incorrect — reject these patterns:**
- Multiple sentences or a heading above the statement
- Image in this section
- Secondary CTA alongside the primary

### Footer

**Correct:** Dark background (`#0D3349`). White text. Four-column layout with grouped links. Logo white variant at top. Bottom bar with Privacy, Cookies, Cookie settings, copyright notice, and company address in 11px muted text with a thin top border.

**Incorrect — reject these patterns:**
- Light background footer
- Fewer than four columns on desktop
- Large colored social media icons — text links or small neutral icons only

---

## Part 7 — Imagery judgment criteria

The JSON cannot encode whether an image is appropriate. Use these criteria when an image decision needs to be made.

### The test

Ask: could this image appear unaltered in a manufacturing company's annual report? If yes, it passes. If it looks like it belongs on a SaaS landing page, a lifestyle brand, or a stock photo site, it fails.

### Pass criteria

- Shows real physical objects: fasteners, bolts, nuts, C-parts, factory equipment, warehouse logistics, production lines, engineering environments
- Neutral or industrial color palette — no artificially vivid post-processing or dramatic color grading
- Is photography, not illustration or 3D render
- Could appear in an industry trade publication without looking out of place

### Fail criteria — reject the image if

- It shows generic business people as the primary subject (handshakes, meetings, smiling office workers in stock-photo poses)
- It is an illustration, icon set, abstract shape composition, or 3D render
- It has been heavily color-graded to look cinematic or dramatic
- It could appear on a startup landing page without looking out of place
- It uses any illustrated or cartoon visual style

### Hackathon fallback

When real Bufab product photography is unavailable, use Unsplash CC0 images. Acceptable search terms: "factory floor", "industrial warehouse", "manufacturing parts", "supply chain logistics", "precision engineering", "metal fasteners". Reject any result where people are the primary subject.

SVG process diagrams and technical schematics are always acceptable as the image element in diagram-sections and text-image-split sections. They are technical illustrations, not decorative ones.

---

## Part 8 — Copy tone judgment criteria

### Pass criteria — the copy is correct if

- Written for a procurement manager or supply chain engineer, not a general consumer
- States a specific capability or value proposition in plain, direct language
- Uses technical vocabulary where appropriate: VMI, PPAP, C-parts, landed cost, supplier consolidation, on-time delivery
- Avoids superlatives and unverifiable claims
- Sentences are complete and professional in register

### Fail criteria — reject the copy if

- Uses buzzwords without technical grounding: "revolutionary", "cutting-edge", "seamless experience", "world-class" used as a generic descriptor
- Written in casual or conversational tone: "Hey, let's get started", "We've got you covered"
- Uses emotional marketing language: "We're passionate about...", "We love helping customers...", "Our mission is to change the world of..."
- Makes the UI or platform the subject: "Experience our powerful platform", "Discover our amazing solution" — the product and its value should be the subject, not the experience of using a website

---

## Part 9 — Anti-patterns quick reference

These are the violations that appear most often. Check for all of them in every review before considering a component done.

| ID | Severity | Pattern to reject |
|----|----------|-------------------|
| AP-01 | Blocker | Hero text centered — must be left-aligned |
| AP-02 | Blocker | Card layout in any section other than industries-grid |
| AP-03 | Blocker | Any gradient — background, text, border, SVG fill |
| AP-04 | Blocker | Accent orange (#E8610A) used anywhere other than CTA button fill |
| AP-05 | Blocker | Any web font or @font-face — system stack only |
| AP-06 | Blocker | Border-radius above 2px outside industries-grid tiles |
| AP-07 | Blocker | Header background changing on scroll — always #0D3349 |
| AP-08 | Blocker | JavaScript scroll listener toggling header class or color |
| AP-09 | Warning | Section padding above 64px |
| AP-10 | Warning | Icons used as primary visual in value-columns |
| AP-11 | Warning | Two consecutive sections with same background color |
| AP-12 | Warning | Marketing or casual copy tone in headings or body text |
| AP-13 | Warning | Thumbnail images in insights-list |

Note that AP-07 and AP-08 are new blockers added following a specific fix made during development. The header scroll behavior was previously implemented incorrectly (turning white on scroll). This is now a named, enforced blocker in the audit.

---

## Part 10 — Severity tiers and compliance scoring

Used by the `audit_page` and `score_ux` MCP tools to produce a compliance report.

**Blocker (−15 points each):** Violations that fundamentally break the visual category of the page. A page with any blocker looks like the wrong kind of website. Blockers must be fixed before a PR can merge. AP-01 through AP-08 and all rules marked [blocker] in Parts 3–5.

**Warning (−5 points each):** Violations that degrade quality but do not break the visual category. Should be fixed but do not block a merge. AP-09 through AP-13 and all rules marked [warning] in Parts 3–5.

**Scoring:** Start at 100. Subtract per violation instance, not per rule. A section with three separate card violations is −45 (three instances of AP-02), not −15. A score of 85 or above is a pass. Below 85 is a fail. A page with zero blockers but many warnings can still fail on score.

---

## Part 11 — Generation checklist

When generating a new section or page, follow this sequence before writing any code.

1. Call `get_section_spec(section_type)` from the JSON — read the field list and rules before writing anything
2. Call `get_token(name)` for every color value — never use a color from memory
3. Confirm the section has a named business purpose — decorative-only sections are forbidden
4. Default to: left-aligned text, system font stack, no border-radius, no shadow, no gradient
5. Check the alternating background rule — use `surface-alt` if the preceding section used `background`
6. After generating, run `validate_component` before delivering the output

**The header check:** Before delivering any page output, confirm the header has `background: #0D3349` as a static CSS value and no scroll event listener of any kind. This is the most common error and it is a blocker.

**The final question:** Remove all styling mentally and look at the content structure. Does it still communicate clearly? If yes, the UI is doing its job. If the content collapses without the styling, the UI is doing too much work — fix the content first, then apply minimal styling.

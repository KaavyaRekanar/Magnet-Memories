# Visual Design System

**Project:** Magnet Memories
**Document:** Visual Design System
**Version:** 1.0
**Status:** Draft

---

# Purpose

This document defines the visual language of Magnet Memories.

It translates the UX principles into reusable rules for:

* colour;
* typography;
* spacing;
* layout;
* materials;
* imagery;
* depth;
* motion;
* icons;
* accessibility;
* component styling.

The design system should ensure that every screen feels like part of the same handcrafted family travel journal.

This document should be treated as the visual source of truth for Version 1.

---

# Visual Vision

Magnet Memories should resemble a beautifully made leather-bound travel journal that has been carefully preserved and added to over many years.

The experience should feel:

* timeless;
* warm;
* personal;
* refined;
* calm;
* tactile;
* quietly premium.

The journal aesthetic should be suggested through composition, colour, typography and motion.

It should not rely on exaggerated imitation of physical materials.

---

# Core Visual Principle

> **Suggest craftsmanship. Avoid theatrical realism.**

The product should not look like a cartoon representation of an old book.

Avoid:

* fake torn paper;
* coffee stains;
* burned page edges;
* exaggerated leather grain;
* oversized stitching;
* cartoon bookmarks;
* dramatic drop shadows;
* ornamental clutter.

Prefer:

* restrained material cues;
* subtle texture;
* balanced typography;
* generous margins;
* realistic but minimal depth;
* carefully composed pages.

---

# Visual Hierarchy

The visual hierarchy should always prioritise the family memory.

The order of attention should usually be:

1. Photograph or Live Photo
2. Journey title or Memory Card
3. Supporting location and date
4. Subtle interface controls
5. Decorative journal elements

Decorative elements must never compete with the photograph.

---

# Design Tokens

The implementation should define central design tokens rather than hardcoding repeated values.

Recommended token groups:

```text
ColorTokens
TypographyTokens
SpacingTokens
ShapeTokens
ElevationTokens
MotionTokens
PageTokens
CoverTokens
```

All screens and components should reference these tokens.

---

# Colour System

The palette is inspired by:

* warm paper;
* dark leather;
* charcoal ink;
* aged brass;
* natural linen;
* walnut wood.

Colours should feel warm without becoming yellow or sepia-heavy.

Photographs should remain the most colourful elements on screen.

---

## Core Palette

### Leather — Deep Brown

Primary resting-state colour.

Use for:

* journal cover;
* cover-opening transitions;
* Workshop headers where appropriate;
* dark framing surfaces.

Suggested starting value:

```text
#3A241A
```

---

### Leather Shadow — Near Black Brown

Use for:

* cover depth;
* dark edge shading;
* text on light brass elements;
* system-bar blending.

Suggested starting value:

```text
#1E1511
```

---

### Leather Highlight — Warm Brown

Use sparingly for:

* subtle cover lighting;
* embossed-edge highlights;
* selected Workshop elements.

Suggested starting value:

```text
#684535
```

---

### Paper — Warm Cream

Primary page background.

Use for:

* title pages;
* Memory Cards;
* quote pages;
* Workshop content surfaces.

Suggested starting value:

```text
#F4EEDF
```

---

### Paper Light — Soft Ivory

Use for:

* elevated paper sheets;
* lighter reading areas;
* high-contrast text sections.

Suggested starting value:

```text
#FBF7ED
```

---

### Paper Shadow — Warm Beige

Use for:

* page edges;
* page-turn depth;
* separators;
* subtle border treatments.

Suggested starting value:

```text
#D8CEB8
```

---

### Ink — Charcoal

Primary text colour.

Suggested starting value:

```text
#2A2824
```

Avoid pure black for large bodies of text.

---

### Ink Secondary — Warm Grey

Use for:

* dates;
* locations;
* supporting metadata;
* inactive controls.

Suggested starting value:

```text
#6C675E
```

---

### Brass — Muted Gold

Use sparingly for:

* embossed cover details;
* focus indicators;
* selected Workshop accents;
* ribbon hardware if displayed.

Suggested starting value:

```text
#A98A52
```

Brass should never become a bright yellow accent.

---

### Linen — Soft Neutral

Use for:

* Workshop backgrounds;
* secondary panels;
* empty states.

Suggested starting value:

```text
#DDD5C5
```

---

### Error — Muted Terracotta

Use only for errors requiring attention.

Suggested starting value:

```text
#9A4F3F
```

Avoid bright red unless required for accessibility or system conventions.

---

### Success — Muted Olive

Use for successful imports, magnet writing and verification.

Suggested starting value:

```text
#66705A
```

---

# Colour Usage Rules

* Do not use gradients as major interface backgrounds.
* Do not use bright accent colours for ordinary actions.
* Do not tint photographs with the journal palette.
* Avoid pure white except where technically necessary.
* Avoid pure black for large areas.
* Brass should occupy less than approximately 5% of most screens.
* Error and success colours should appear only when relevant.
* Text must maintain accessible contrast against paper and leather surfaces.

---

# Optional Journey Accent

Each journey may optionally have a restrained accent colour.

Possible uses include:

* a thin ribbon bookmark;
* a page number marker;
* a subtle title-page rule;
* a small Workshop identifier.

The accent should never recolour the full interface.

Examples:

* Japan — muted crimson
* Sweden — desaturated blue
* Ireland — deep moss green
* New York — warm amber

Journey accents should not rely solely on country stereotypes or national flag colours.

The owner may choose an accent manually in a later version.

Version 1 may assign one from a curated palette.

---

# Typography

Typography should feel literary, elegant and highly readable.

The application should resemble a well-designed printed travel journal rather than a mobile interface.

Use a limited type family system.

Recommended structure:

* Serif display family for titles and quotations
* Readable serif or humanist family for Memory Card text
* Clean sans-serif family for Workshop controls and technical content

---

# Recommended Font Categories

Final fonts should be selected based on Android licensing, rendering quality and bundled-file size.

Suitable open-source candidates include:

## Display Serif

Use for:

* cover title;
* journey title;
* title page;
* closing page;
* favourite quote.

Candidate families:

* Cormorant Garamond
* Libre Baskerville
* EB Garamond
* Crimson Pro

---

## Reading Serif

Use for:

* Memory Card body;
* longer descriptions;
* quote-page text.

Candidate families:

* Source Serif 4
* Literata
* Crimson Pro
* Libre Baskerville

---

## Interface Sans Serif

Use for:

* Workshop labels;
* form fields;
* buttons;
* storage values;
* accessibility settings;
* diagnostic information.

Candidate families:

* Source Sans 3
* Inter
* Noto Sans
* system sans serif

---

# Typography Scale

The application is primarily displayed on a 10.36-inch landscape tablet.

Text sizes should be tested on the HMD T21 and adjusted for viewing from both close and moderate distances.

Suggested starting scale:

| Token            | Use                       | Suggested Size |
| ---------------- | ------------------------- | -------------: |
| `displayLarge`   | Book cover title          |       42–52 sp |
| `displayMedium`  | Journey title page        |       36–44 sp |
| `headlineLarge`  | Memory Card title         |       30–36 sp |
| `headlineMedium` | Workshop section title    |       26–30 sp |
| `titleLarge`     | Journey cards and dialogs |       22–26 sp |
| `bodyLarge`      | Memory Card story         |       20–24 sp |
| `bodyMedium`     | Workshop body text        |       17–19 sp |
| `labelLarge`     | Primary actions           |       17–19 sp |
| `labelMedium`    | Supporting labels         |       14–16 sp |
| `caption`        | Dates, page indicators    |       13–15 sp |

Do not reduce Memory Card body text merely to fit excessive content.

Long text should instead be edited or paginated appropriately.

---

# Typography Rules

* Use no more than three font families.
* Use no more than three weights within a screen.
* Avoid all-capital body text.
* Cover and title-page text may use restrained uppercase lettering where legible.
* Increase line height for Memory Card text.
* Avoid narrow text columns that produce excessive wrapping.
* Avoid overly wide text columns that reduce readability.
* Do not use handwriting fonts for long passages.
* A handwriting-style accent may be considered later for short owner-authored notes, but it is outside Version 1.

---

# Recommended Line Heights

| Text Type        | Suggested Line Height |
| ---------------- | --------------------: |
| Cover title      |             1.05–1.15 |
| Journey title    |               1.1–1.2 |
| Memory Card body |              1.4–1.55 |
| Workshop body    |              1.35–1.5 |
| Captions         |              1.25–1.4 |

---

# Spacing System

Use a consistent spacing scale based on an 8 dp foundation.

Recommended tokens:

| Token     | Value |
| --------- | ----: |
| `space2`  |  2 dp |
| `space4`  |  4 dp |
| `space8`  |  8 dp |
| `space12` | 12 dp |
| `space16` | 16 dp |
| `space24` | 24 dp |
| `space32` | 32 dp |
| `space40` | 40 dp |
| `space48` | 48 dp |
| `space64` | 64 dp |
| `space80` | 80 dp |

Use larger spacing values generously on reading screens.

Workshop may use denser spacing, but should never feel cramped.

---

# Page Margins

Journey pages should maintain consistent safe margins.

Suggested starting values for landscape orientation:

* Outer horizontal margin: 48–72 dp
* Outer vertical margin: 32–56 dp
* Text-column maximum width: approximately 65–75% of the available page width
* Minimum distance between text and page edge: 32 dp

Margins may reduce when a photograph intentionally fills the page.

---

# Page Geometry

The display should suggest an open journal without always rendering two literal physical pages.

Version 1 should support two page compositions:

1. Single-page composition
2. Open-spread composition

---

## Single-Page Composition

Best for:

* full-screen photographs;
* Live Photos;
* title pages;
* closing pages.

The page occupies most or all of the display.

A subtle page edge or surrounding cover surface may be visible.

---

## Open-Spread Composition

Best for:

* Memory Cards;
* photograph plus caption;
* quote plus photograph;
* Workshop previews.

The screen suggests a left and right page separated by a subtle central gutter.

The gutter should not be so wide that it wastes display space.

---

# Central Gutter

Suggested starting width:

```text
16–28 dp
```

The gutter may contain:

* a subtle shadow;
* a slight tonal shift;
* a page fold hint.

Avoid drawing a literal thick book spine across all content.

---

# Safe Areas

Important content must avoid:

* tablet camera cut-outs;
* system gesture zones;
* rounded physical corners;
* mounting-frame overlap;
* NFC scan guidance overlays.

Provide configurable safe-area insets if the physical frame covers part of the screen.

---

# Cover Design

The Book Cover is the most frequently visible screen.

It must look refined when displayed for long periods.

---

## Cover Structure

Suggested visual hierarchy:

1. Subtle leather field
2. Embossed border or thin inset frame
3. Magnet Memories title
4. Optional subtitle
5. Optional established year
6. Optional instruction during onboarding

Example:

```text
MAGNET MEMORIES

Family Travel Journal

Since 2026
```

---

## Cover Material

The cover should use:

* a deep leather colour;
* extremely subtle grain;
* restrained edge darkening;
* soft directional lighting.

The texture must remain subtle enough that it is not distracting from a normal viewing distance.

Avoid using a low-resolution photographic leather texture.

Prefer:

* procedural texture;
* carefully tiled high-resolution texture;
* lightweight vector noise;
* gradient-free tonal variation.

---

## Embossing

Cover text may use a restrained embossed or debossed effect.

Recommended treatment:

* one subtle highlight edge;
* one subtle shadow edge;
* low contrast;
* no glossy metallic bevel.

Gold foil may be suggested through muted brass colouring, not bright yellow shine.

---

## Cover Border

An optional inset border may be used.

It should be:

* thin;
* low contrast;
* aligned consistently;
* positioned well inside safe margins.

Avoid ornamental corners or elaborate flourishes.

---

# Paper Design

Paper should feel warm and tactile without appearing stained or old.

---

## Paper Texture

Use a very subtle texture that suggests natural fibres.

The texture should:

* remain nearly invisible behind body text;
* avoid repeating obvious patterns;
* not reduce contrast;
* not interfere with photographs.

A flat warm-cream background is acceptable when texture quality is insufficient.

---

## Page Edges

Page edges may use:

* slight warm shadow;
* subtle highlight;
* minimal depth.

Avoid curled corners unless part of an intentional page-turn animation.

---

## Page Numbers

Page numbers are optional in Journey mode.

If used:

* keep them small;
* position them near the outer bottom edge;
* use Ink Secondary;
* hide them on full-bleed photo pages;
* do not expose database or media counts.

Workshop may show explicit page order numbers.

---

# Image Treatment

Photographs are the primary visual content.

They must never be degraded for the sake of the journal metaphor.

---

## Image Quality

* Preserve aspect ratio.
* Respect EXIF orientation.
* Use high-quality scaling.
* Avoid unnecessary cropping.
* Avoid filters.
* Avoid automatic sepia, vignette or desaturation.
* Avoid visible compression artefacts.

---

## Image Fit Modes

Support at least:

### Contain

Entire photograph visible.

Use a neutral page or dark backdrop around unused space.

### Cover

Photograph fills the available frame.

Cropping should be minimal and predictable.

The owner may choose the preferred mode per journey or page in a later version.

Version 1 may select a sensible automatic mode based on orientation and aspect ratio.

---

## Portrait Photographs

For portrait photographs in landscape orientation:

Preferred treatments:

1. Centre photograph with generous paper margins.
2. Use an open-spread layout with the photo on one page and optional text on the other.
3. Use a softly blurred extension only if clearly documented and visually restrained.

Avoid duplicating the photo on both sides.

---

## Full-Bleed Photographs

Full-bleed may be used for visually strong landscape images.

When used:

* hide page texture;
* hide page number;
* preserve safe-area controls;
* avoid overlaying excessive text.

---

# Live Photo Design

Live Photos should initially appear indistinguishable from printed photographs.

After a short delay, the photograph comes to life.

This transition is one of the defining visual moments of the product.

---

## Live Photo Indicator

Use a small, discreet indicator.

Possible treatments:

* a minimal concentric-circle icon;
* a small brass dot and ring;
* a subtle motion glyph.

The indicator should:

* appear briefly;
* avoid covering faces;
* disappear during playback;
* remain accessible to screen readers.

Do not imitate Apple's trademarked Live Photo branding unnecessarily.

---

## Motion Transition

Recommended sequence:

1. Display still image.
2. Hold for approximately 800–1,200 ms.
3. Crossfade into motion playback.
4. Play motion once.
5. Settle on final frame or return to the still.
6. Continue according to playback timing.

The still-to-motion transition should not flash or resize.

---

## Live Photo Audio

Audio is enabled by default at a moderate level.

No visual volume overlay should appear unless requested.

When muted, a discreet muted indicator may appear briefly.

---

# Memory Card Design

The Memory Card is the emotional centre of each journey.

It should feel authored, personal and intentional.

---

## Recommended Layout

For an open spread:

### Left Page

* Cover photograph or secondary hero photograph
* Journey title
* Location
* Dates

### Right Page

* Favourite quote
* Personal story
* Optional small decorative rule

Alternative layouts may place the full Memory Card on one page when text is short.

---

## Story Length

The primary Memory Card should be readable without scrolling.

Recommended target:

```text
50–140 words
```

Longer memories may eventually support multiple pages, but Version 1 should encourage concise writing.

---

## Favourite Quote

Display using:

* display serif or italic serif;
* larger line height;
* restrained quotation marks;
* sufficient whitespace.

Do not use oversized decorative quotation icons.

---

# Title Page Design

The Title Page should feel ceremonial.

Recommended content:

* journey title;
* season or date range;
* location;
* optional thin brass rule;
* optional small journey accent.

Avoid photographs unless explicitly used as a very subtle background.

The title page should not resemble a form or information card.

---

# Closing Page Design

The Closing Page should provide emotional closure.

Recommended content:

```text
Journey complete

Japan

Spring 2028
```

Optional secondary content:

* number of pages;
* number of Live Photos.

Avoid analytics or excessive statistics.

The page should remain simple and reflective.

---

# Workshop Visual Language

Workshop is practical, but it should still belong to the same journal.

Use:

* warm paper backgrounds;
* Ink text;
* muted brass focus states;
* restrained cards;
* clear hierarchy;
* readable sans-serif controls.

Workshop should not imitate physical paper controls literally.

It may use conventional Android patterns where they improve accessibility and efficiency.

---

# Workshop Components

---

## Journey Card

Displays:

* cover thumbnail;
* journey title;
* location;
* date;
* page count;
* Live Photo count where useful.

Actions may include:

* Edit
* Preview
* Program Magnet
* Remove

Actions should not all appear simultaneously if this creates clutter.

Use an overflow menu or contextual detail screen where appropriate.

---

## Primary Action

Primary Workshop actions should use a filled button or prominent card.

Examples:

* Add Journey
* Begin Import
* Write Magnet

Suggested treatment:

* Leather background;
* Paper Light text;
* moderate corner radius;
* minimal elevation.

---

## Secondary Action

Use outlined or text buttons for:

* Cancel
* Preview
* Edit Later
* Return to Workshop

---

## Destructive Action

Destructive actions should:

* use muted terracotta;
* require confirmation;
* clearly state that the master archive remains untouched;
* never be the default focused action.

---

## Form Fields

Use familiar Android text-field behaviour.

Visual treatment:

* paper or linen background;
* Ink text;
* brass focus line;
* clear labels;
* sufficient contrast.

Do not use decorative handwriting fields.

---

# Shape System

Shapes should feel refined and restrained.

Suggested values:

| Token         |   Radius |
| ------------- | -------: |
| `shapeSmall`  |     6 dp |
| `shapeMedium` |    10 dp |
| `shapeLarge`  |    16 dp |
| `shapePage`   |   4–8 dp |
| `shapeCover`  | 10–18 dp |

Journey pages should not appear as highly rounded mobile cards.

Use smaller radii for paper.

Use slightly larger radii for Workshop controls and the closed cover.

---

# Elevation and Depth

Depth should suggest stacked pages and a physical cover.

Avoid Material-style floating cards everywhere.

Recommended elevation hierarchy:

| Level | Use                             |
| ----- | ------------------------------- |
| 0     | Full-page content               |
| 1     | Paper resting over a background |
| 2     | Open spread                     |
| 3     | Workshop dialog or focused card |
| 4     | Temporary overlay controls      |

Use soft, wide shadows rather than sharp dark shadows.

---

# Shadow Rules

* Use warm-neutral shadows.
* Avoid pure black.
* Keep opacity low.
* Increase blur rather than opacity.
* Do not combine multiple dramatic shadows.
* Page-turn animation may temporarily use stronger directional shadows.

---

# Iconography

Icons should be:

* simple;
* line-based;
* familiar;
* consistent;
* minimally decorative.

Use established Material icons where they improve recognition.

Avoid custom ornate icons unless required by the journal metaphor.

---

## Icon Use in Journey Mode

Journey mode should show very few icons.

Allowed examples:

* pause;
* play;
* previous;
* next;
* replay;
* close book;
* muted audio.

Icons should appear only when controls are intentionally revealed.

---

## Icon Use in Workshop

Workshop may use icons for:

* add;
* edit;
* delete;
* storage;
* NFC;
* import;
* preferences.

Every important icon must include a text label or accessibility description.

---

# Ribbon Bookmark

A ribbon bookmark may be used as a subtle recurring motif.

Possible roles:

* journey accent;
* current journey marker;
* Workshop selection state;
* progress hint.

The ribbon should not become a navigation tab.

---

## Ribbon Design Rules

* Narrow width
* Muted colour
* Minimal shadow
* No exaggerated fabric animation
* No constant movement
* No country flag patterns

During the Book Cover state, a very small ribbon end may be visible.

Its movement, if any, should be almost imperceptible.

---

# Motion System

Motion must feel calm, deliberate and physical.

Use central motion tokens.

Recommended categories:

```text
motionFast
motionStandard
motionSlow
motionCeremonial
```

---

## Suggested Durations

| Token              |   Duration | Use                                   |
| ------------------ | ---------: | ------------------------------------- |
| `motionFast`       | 120–180 ms | Control appearance, small feedback    |
| `motionStandard`   | 220–320 ms | Workshop transitions                  |
| `motionSlow`       | 350–500 ms | Page turns, major content transitions |
| `motionCeremonial` | 500–800 ms | Opening and closing the journal       |

These values should be tested on the HMD T21.

---

# Cover-Opening Motion

Recommended sequence:

1. Subtle cover highlight shifts.
2. Cover begins rotating or revealing the first page.
3. Page surface becomes visible.
4. Title Page settles.
5. Motion stops completely.

Target duration:

```text
500–700 ms
```

Avoid complex 3D rendering if it causes dropped frames.

A convincing layered 2D animation is preferable to poor 3D animation.

---

# Page-Turn Motion

The page-turn animation should suggest turning paper without requiring physically accurate simulation.

Recommended characteristics:

* directional;
* responsive to swipe;
* smooth;
* brief;
* accompanied by subtle shadow movement;
* cancellable during interactive swipe.

Target duration after release:

```text
350–500 ms
```

---

## Page Direction

In left-to-right locales:

* advancing moves the current page toward the left;
* returning moves it toward the right.

The gesture mapping must be verified in user testing.

Documentation and implementation should use consistent language.

---

# Gesture Feedback

During a swipe:

* page edge follows the gesture;
* next or previous page becomes partially visible;
* shadow shifts naturally;
* releasing before the threshold returns the page;
* releasing after the threshold completes the turn.

Avoid springy or playful overshoot.

---

# Reduced Motion

When reduced motion is enabled:

* replace cover animation with a gentle crossfade;
* replace page turns with directional fades or short slides;
* remove idle cover movement;
* minimise parallax and depth changes;
* retain the narrative sequence.

Reduced motion should preserve elegance.

---

# Easing

Prefer smooth easing curves with gentle acceleration and deceleration.

Avoid:

* bouncy springs;
* elastic easing;
* overshoot;
* abrupt linear motion.

Recommended conceptual easing:

```text
easeInOutCubic
fastOutSlowIn
gentleDecelerate
```

Exact Android animation curves should be documented in implementation.

---

# Idle Behaviour

The Book Cover is displayed for long periods.

It must not produce distracting or repetitive animation.

Optional idle details:

* very subtle directional light shift;
* barely visible embossed highlight change;
* minimal ribbon movement.

Frequency:

```text
No more than once every 2–5 minutes
```

Duration:

```text
1–3 seconds
```

Idle motion should be disabled when reduced motion is enabled.

---

# Loading and Progress Visuals

Avoid standard spinning indicators in Journey mode.

Workshop may use conventional progress indicators where necessary.

---

## Journey Loading

Preferred techniques:

* hold the closed cover briefly;
* display “Opening your journey…”;
* transition when ready.

The loading state must not pretend the content is ready when it is not.

---

## Import Progress

Workshop may show:

* linear progress;
* current task;
* files processed;
* estimated remaining work where reliable;
* cancellation control.

Use journal language:

* Preparing pages…
* Matching Live Photos…
* Optimising photographs…
* Adding the journey…
* Finishing the chapter…

Do not hide real progress during potentially lengthy imports.

---

# Feedback

Feedback should be subtle but clear.

---

## Success

Use:

* muted olive;
* small check icon;
* concise confirmation.

Example:

> Your journey is ready.

---

## Warning

Use:

* muted amber or brass;
* clear explanation;
* recommended next step.

---

## Error

Use:

* muted terracotta;
* plain language;
* recovery action.

Avoid alarming full-screen red states.

---

# Haptics

Haptics are optional and should be restrained.

Potential use:

* successful NFC tag detection;
* successful magnet writing;
* confirmed page turn when initiated by touch;
* destructive action confirmation.

Avoid repeated vibration during slideshow playback.

Haptics should respect system settings.

---

# Sound Design

The application should not use decorative interface sounds in Version 1.

Allowed sound sources:

* Live Photo audio;
* future user-provided journey audio.

Do not add:

* page-turn sound effects;
* leather movement sounds;
* success chimes;
* background music by default.

The journal should sound like the memory, not like an application.

---

# Accessibility

The visual design system must support accessibility without compromising the core aesthetic.

---

## Contrast

Text and controls must meet WCAG-aligned contrast requirements where practical.

Do not place low-contrast brass text directly on cream paper for essential information.

Use Ink for primary text.

---

## Text Scaling

Workshop should respect Android font scaling.

Journey pages should accommodate reasonable scaling while preserving layout.

When text scaling causes the Memory Card to overflow:

* paginate;
* shorten displayed excerpts only with owner approval;
* never clip essential content.

---

## Touch Targets

Minimum interactive target:

```text
48 × 48 dp
```

Larger targets are preferred for:

* older adults;
* children;
* playback controls.

---

## Focus Indicators

Keyboard and accessibility focus should be visible.

Use:

* brass outline;
* sufficient thickness;
* no reliance on colour alone.

---

## Screen Readers

All controls require meaningful labels.

Examples:

* Pause journey
* Resume journey
* Replay moving photograph
* Return to Book Cover
* Write magnet for Japan
* Remove journey from tablet

Decorative textures and shadows should not be announced.

---

## Colour Independence

Status should never rely only on colour.

Combine colour with:

* icon;
* text;
* shape;
* position.

---

# Dark Environment Behaviour

The journal may be used in dim rooms.

Version 1 may provide a reduced-brightness evening preference.

Avoid implementing a completely different dark theme unless necessary.

An evening treatment may:

* reduce screen brightness;
* deepen leather tones;
* slightly dim paper;
* reduce brass intensity;
* preserve readable contrast.

Photographs should not be heavily darkened.

---

# Screen Burn-In Considerations

Because the Book Cover may remain visible for long periods, avoid perfectly static high-contrast elements.

Possible mitigation:

* subtle periodic pixel shifts;
* minimal light movement;
* low-contrast cover composition;
* configurable screen-off schedule;
* dimming after inactivity.

The implementation should prioritise device longevity without creating visible distraction.

---

# System UI

Journey mode should use immersive full-screen presentation where supported.

Requirements:

* hide system status and navigation bars;
* reveal system controls through standard Android gestures;
* prevent accidental permanent lockout;
* restore bars appropriately in Workshop;
* respect accessibility requirements.

System UI transitions should not clash with page animations.

---

# Orientation

The primary orientation is landscape.

Version 1 should lock Journey mode to landscape unless testing identifies a strong reason not to.

Workshop may also remain landscape for consistency.

Layouts must accommodate both left- and right-side NFC antenna placement without shifting core content during use.

---

# Physical Frame Considerations

The tablet may be installed inside a custom decorative frame.

The design system should allow configuration for:

* frame overlap;
* charging-cable position;
* NFC scan target;
* physical shadow from the frame;
* safe margins.

Do not place essential controls against the extreme edges.

---

# NFC Scan Guidance

During initial setup, a temporary NFC guidance overlay may show where to tap the magnet.

Visual treatment:

* subtle outlined target;
* short instruction;
* optional pulsing ring;
* no permanent overlay during normal use.

Once the location is learned, the guidance should be hidden.

---

# Empty States

Empty states belong mainly in Workshop.

Use warm, encouraging language.

Example:

> Your journal is waiting for its first journey.

Visual treatment:

* paper background;
* minimal illustration or icon;
* one primary action;
* no technical details.

---

# Dialogs

Dialogs should be used sparingly.

Workshop dialogs may use:

* paper surface;
* Ink text;
* subtle shadow;
* medium radius;
* clearly separated actions.

Journey mode should avoid dialogs entirely.

---

# Photography and Illustration Style

The product should rely primarily on the family's photographs.

Decorative illustrations should be minimal.

Possible uses:

* subtle line drawing on an empty Workshop screen;
* understated travel motif during onboarding;
* simple NFC guidance.

Avoid:

* stock travel photography;
* cartoon landmarks;
* national stereotypes;
* decorative clip art.

---

# Visual Quality Standards

Before approving a screen, verify:

* The photograph remains dominant.
* Text is readable from the expected viewing distance.
* The journal metaphor remains subtle.
* No element resembles a generic Android dashboard.
* Motion is smooth on the HMD T21.
* The interface remains usable with reduced motion.
* Touch targets are accessible.
* Decorative texture does not reduce clarity.
* The screen still looks appropriate when displayed for an extended period.

---

# Implementation Guidance

Jetpack Compose should implement the system through reusable themes and components.

Recommended structure:

```text
ui/theme/
    Color.kt
    Type.kt
    Shape.kt
    Spacing.kt
    Motion.kt
    Theme.kt

ui/components/
    BookCover.kt
    JournalPage.kt
    OpenSpread.kt
    MemoryCardPage.kt
    PhotoPage.kt
    LivePhotoPage.kt
    JourneyTitlePage.kt
    ClosingPage.kt
    WorkshopCard.kt
    JournalButton.kt
    JournalDialog.kt
```

The implementation should not place visual constants directly inside screen composables unless they are truly screen-specific.

---

# Required Prototype Tests

Before finalising the visual system, create physical-device prototypes for:

1. Book Cover
2. Title Page
3. Memory Card
4. Landscape Photo Page
5. Portrait Photo Page
6. Live Photo transition
7. Page-turn animation
8. Workshop journey card
9. Import progress
10. Reduced-motion page transition

Test them on the actual HMD T21.

Desktop previews are not sufficient for final approval.

---

# Acceptance Criteria

The visual design system is successfully implemented when:

* every screen uses shared design tokens;
* the Book Cover looks appropriate as a permanent home display;
* Journey mode resembles a refined travel journal rather than an Android gallery;
* photographs remain visually dominant;
* animations remain smooth on the target tablet;
* Workshop remains practical and accessible;
* reduced-motion behaviour is available;
* text maintains sufficient contrast and readability;
* decorative effects remain restrained;
* users describe the product as a journal, book or keepsake rather than a tablet application.

---

# Final Principle

> **The journal should feel precious because of the memories it contains, not because of decoration added around them.**

Every visual decision should support the family's stories and then quietly step aside.

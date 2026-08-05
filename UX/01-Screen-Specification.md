# Screen Specification

**Project:** Magnet Memories

**Document:** Screen Specification

**Version:** 1.0

---

# Purpose

This document specifies every user-facing screen and interaction within Magnet Memories Version 1.

Unlike the UX Principles document, this specification is implementation-oriented.

Every screen includes:

* purpose
* layout
* behaviour
* transitions
* gestures
* animations
* edge cases

Developers should be able to build the complete interface from this document.

---

# Screen Hierarchy

```text
Book Cover
    │
    ├── Journey
    │      ├── Opening Page
    │      ├── Title Page
    │      ├── Memory Card
    │      ├── Journey Pages
    │      ├── Closing Page
    │      └── Book Cover
    │
    └── Workshop
            ├── Dashboard
            ├── Journey Manager
            ├── Journey Editor
            ├── Import Journey
            ├── NFC Programming
            ├── Preferences
            └── Storage
```

---

# Global Layout Rules

These rules apply to every screen.

* Landscape orientation only.
* No visible Android navigation bar during Journey mode.
* Safe margins on all sides.
* Consistent page proportions.
* Large touch targets.
* Motion never blocks interaction.
* System notifications should never appear during Journey mode.

---

# Screen 1 — Book Cover

## Purpose

The resting state of the application.

The journal is closed.

The application waits for the next magnet.

---

## Layout

Full-screen leather-bound journal.

Centered:

* Magnet Memories
* Family Travel Journal
* Established year (optional)

Very subtle embossed appearance.

No visible buttons.

No lists.

No menus.

No settings icon.

No journey selection.

Optional discreet instruction during onboarding:

> Tap a travel magnet to begin.

The instruction may be permanently hidden by the owner.

---

## Behaviour

Remain completely still.

Every few minutes:

* subtle lighting shift;
* tiny leather highlight;
* almost imperceptible ribbon movement.

These animations should be extremely restrained.

---

## NFC Behaviour

When a recognised magnet is detected:

Transition immediately to Opening Cover.

---

## Unknown Magnet

Show modal page:

"This magnet hasn't been linked to a journey yet."

Owner may enter Workshop.

Guests cannot.

---

# Screen 2 — Opening Cover

## Purpose

Transition between waiting and storytelling.

---

## Duration

Approximately 300–500 ms.

---

## Animation

Leather cover gently opens.

No abrupt cuts.

No loading spinner.

---

## Behaviour

Immediately continue to Title Page.

---

# Screen 3 — Title Page

## Purpose

Introduce the chapter.

This page resembles the opening page of a printed book.

---

## Layout

Large centred title.

Example:

# Japan

Spring 2028

Optional location beneath.

No story yet.

Minimal design.

Generous margins.

---

## Duration

Approximately 2 seconds.

Tap skips immediately.

---

## Transition

Gentle page turn.

---

# Screen 4 — Memory Card

## Purpose

Provide emotional context.

---

## Layout

Large cover photograph.

Journey title.

Dates.

Location.

Favourite quote (optional).

Story.

The story should comfortably fit on one page.

Scrolling is not permitted.

If text exceeds one page, truncate with "Read more" in Workshop rather than during playback.

---

## Behaviour

Automatic progression after configurable delay.

Default:

3 seconds.

Tap anywhere proceeds immediately.

---

## Animation

Page turns naturally into the first Journey Page.

---

# Screen 5 — Journey Page

## Purpose

Display one memory.

---

## Supported Page Types

Photo

Live Photo

Quote

Future:

Video

Ticket

Map

Audio

---

## Photo Page

Layout:

Full-page image.

Very small caption if enabled.

No visible controls.

---

Duration

Configurable.

Default:

5 seconds.

---

## Gestures

Tap

Pause / Resume

Swipe Left

Previous page

Swipe Right

Next page

Long Press

Reveal playback controls.

---

# Live Photo Page

Initially display the still image.

After approximately one second:

Automatically play motion once.

Motion should not loop.

After playback:

Remain on final frame.

Continue slideshow normally.

---

Tap

Replay Live Photo.

---

Audio

Enabled by default.

Volume controlled in Preferences.

---

# Quote Page

Optional.

Displays:

Large quotation.

Smaller attribution beneath.

Background:

Minimal.

No decorative illustrations.

---

# Playback Controls

Normally hidden.

Long press reveals:

Pause

Resume

Previous

Next

Replay Live Photo (when applicable)

Exit Journey

Controls disappear automatically after inactivity.

---

# End Page

## Purpose

Provide a gentle conclusion.

---

## Layout

Centered.

Journey Complete

Journey title.

Optional summary:

* Number of pages
* Number of Live Photos

No statistics beyond this.

Avoid technical information.

---

## Behaviour

Automatically proceed to Closing Cover.

---

# Closing Cover

The journal gently closes.

Duration:

Approximately 400 ms.

Return to Book Cover.

---

# Journey Interruption

If another recognised magnet is scanned during playback:

Immediately begin:

Closing Cover

↓

Opening Cover

↓

New Journey

Never show confirmation.

The physical interaction takes priority.

---

# Resume Behaviour

If the same journey is reopened within a configurable timeout:

Resume from previous page.

Default:

10 minutes.

After timeout:

Restart from Title Page.

---

# Workshop

Workshop is accessed only by the owner.

It is intentionally separated from Journey mode.

---

# Workshop Dashboard

Displays:

Journeys

Storage

Import Journey

Program Magnet

Preferences

Large cards.

No dense lists.

---

# Journey Manager

Displays every journey.

Each journey card shows:

Cover image.

Title.

Location.

Date.

Number of pages.

Buttons:

Edit

Remove

Preview

---

# Journey Editor

Editable fields:

Title

Location

Dates

Favourite quote

Memory Card

Cover image

Page order

Journey settings

---

# Page Manager

Shows every page.

Supports:

Drag to reorder.

Delete.

Replace.

Preview.

Future:

Insert Quote Page.

Insert Video Page.

---

# Import Journey

Workflow:

Choose folder

↓

Preview

↓

Validate

↓

Estimate storage

↓

Import

↓

Success

Import progress should describe what is happening.

Example:

Preparing pages…

Optimising photographs…

Matching Live Photos…

Building chapter…

Journey ready.

---

# Import Preview

Display:

Cover image.

Journey title.

Detected pages.

Detected Live Photos.

Estimated storage.

Warnings.

Owner must confirm before import.

---

# Program Magnet

Workflow:

Select journey.

↓

Tap blank magnet.

↓

Write.

↓

Verify.

↓

Success.

Clear success confirmation.

---

# Preferences

Categories:

Playback

Live Photos

Audio

Display

Accessibility

Storage

Developer (optional)

---

# Storage

Display:

Available space.

Journey storage.

Largest journeys.

Future estimated remaining capacity.

---

# Error Pages

Errors should always feel calm.

Examples:

Journey Missing

"We couldn't find this journey."

Missing Page

"This page couldn't be loaded."

Continue automatically.

Low Storage

Explain the issue before importing.

Never interrupt playback.

---

# Loading Behaviour

Journey loading should feel like opening a book rather than loading software.

Avoid:

Progress bars.

Spinners.

Loading percentages.

Prefer transitional animation.

---

# Screen Timing

| Screen            | Default Duration |
| ----------------- | ---------------: |
| Opening Cover     |           400 ms |
| Title Page        |              2 s |
| Memory Card       |              3 s |
| Photo Page        |              5 s |
| Live Photo Still  |              1 s |
| Live Photo Motion |  Native duration |
| End Page          |              2 s |
| Closing Cover     |           400 ms |

All durations should be configurable.

---

# Edge Cases

## Missing Cover Image

Use automatically selected first page.

---

## Missing Memory Card

Display Title Page only.

Proceed normally.

---

## Corrupted Live Photo

Display still image.

Continue journey.

---

## Unknown Magnet

Offer Workshop entry for the owner.

Remain on Book Cover for guests.

---

## Low Battery

Never interrupt a journey.

Warn only when returning to Book Cover.

---

## Device Restart

Return directly to Book Cover.

Remain ready for the next magnet.

---

# Acceptance Criteria

The implementation is complete when:

* Every screen behaves exactly as described.
* Every transition is smooth and purposeful.
* No unnecessary interface elements appear during Journey mode.
* The journal metaphor is preserved from beginning to end.
* Users describe the experience as "opening our family travel journal" rather than "using an app."

---

# Final Principle

Every screen should reinforce one simple illusion:

> **The journal has always been there, quietly waiting for the next story to be opened.**

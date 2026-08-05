# UX Principles

**Project:** Magnet Memories
**Document:** UX Principles
**Version:** 2.0
**Status:** Frozen after approval

---

# Purpose

This document defines the design philosophy that governs every interaction in Magnet Memories.

It is intentionally independent of implementation details.

Whenever a design decision is uncertain, these principles take precedence over aesthetics, convenience, trends or engineering simplicity.

This document should remain stable throughout the lifetime of the project.

---

# UX Vision

Magnet Memories is not software that displays photographs.

It is a digital heirloom designed to feel like opening a beautifully bound family travel journal.

Every interaction should reinforce this illusion.

Users should never feel like they are launching an Android application.

They should feel like they are opening a treasured journal that becomes richer with every family journey.

---

# The North Star

Every design decision should answer one question:

> **Does this feel like opening a treasured family travel journal?**

If the answer is no, it should be redesigned.

---

# Design Philosophy

Technology should disappear.

Stories should remain.

The application exists for one purpose:

> To help families relive meaningful journeys together.

Photographs, Live Photos, animations and NFC are simply tools that support that experience.

The technology should never become the focus.

---

# The Core Experience

The complete experience should feel natural enough that it requires no explanation.

```text
Pick up a travel magnet

↓

Tap the journal

↓

The journal opens

↓

Read the story

↓

Relive the journey

↓

The journal closes

↓

Waiting for the next journey
```

Nothing should interrupt this flow.

---

# Journal Rules

The journal behaves like a physical object.

These rules should never be broken.

* The journal is always closed while waiting.
* A recognised magnet always opens directly to its journey.
* Users never browse journeys during normal use.
* Every journey always begins with a title page.
* Every journey always contains a Memory Card.
* Pages are always experienced in order.
* When the journey finishes, the journal quietly closes.
* Technical implementation details should never appear during normal use.

These rules define the personality of the product.

---

# Experience Modes

Magnet Memories consists of three experience modes.

Each mode has exactly one purpose.

---

## Book Cover

The Book Cover is the resting state.

The application is not idle.

It is waiting.

The display resembles a beautifully crafted leather-bound journal resting on a table.

Purpose:

> Invite the next journey.

Primary interaction:

> Tap a travel magnet.

The Book Cover should feel timeless.

It should look beautiful enough that it belongs in a living room.

---

## Journey

Journey mode begins immediately after a recognised magnet is detected.

The journal opens.

The chapter begins.

The interface quietly disappears.

Purpose:

> Relive a journey.

Everything presented during Journey mode exists to support memory and storytelling.

---

## Workshop

Workshop is the owner's workspace.

It is intentionally separated from the public experience.

Purpose:

> Create and maintain the family journal.

Typical activities include:

* Add Journey
* Import Journey Folder
* Edit Journey
* Write Memory Card
* Choose Cover Page
* Arrange Pages
* Program Magnets
* Verify Magnets
* Configure Preferences
* Remove Journey

Workshop prioritises clarity over immersion while maintaining the same visual identity.

---

# One Purpose Per Mode

Every mode exists for exactly one reason.

| Mode       | Purpose  |
| ---------- | -------- |
| Book Cover | Invite   |
| Journey    | Remember |
| Workshop   | Create   |

Modes should never overlap.

---

# The Magnet Is The Navigation

Travel magnets are not shortcuts.

They are chapter keys.

The physical magnet determines which journey opens.

Users should never browse journeys during normal use.

The magnet replaces traditional navigation.

This principle keeps the experience physical rather than digital.

---

# Story Before Memories

Every journey follows the same narrative structure.

```text
Book Cover

↓

Opening Cover

↓

Title Page

↓

Memory Card

↓

Journey Pages

↓

Closing Page

↓

Closing Cover

↓

Book Cover
```

The order should never change.

Context always comes before memories.

---

# The Memory Card

Every journey begins with a Memory Card.

The Memory Card is not metadata.

It is the emotional introduction to the journey.

Every Memory Card should include:

* Journey title
* Location
* Travel dates
* Personal story
* Optional favourite quote

The Memory Card explains why the journey mattered before the photographs begin.

---

# Pages, Not Files

Internally the application manages Moments.

Users experience Pages.

A page may contain:

* Memory Card
* Photograph
* Live Photo
* Quote
* Video (future)
* Ticket (future)
* Map (future)
* Audio (future)

Users should never think about files.

They should simply turn the next page.

---

# The Journal Illusion

The illusion of reading a handcrafted travel journal should never be broken.

The interface should suggest craftsmanship rather than imitate it.

Use:

* elegant typography;
* restrained colours;
* subtle textures;
* generous margins;
* thoughtful page composition.

Avoid exaggerated skeuomorphic effects.

The journal should be suggested, not simulated.

---

# Respect the Book

Every interaction should reinforce the journal metaphor.

Examples:

Instead of:

> Loading...

Prefer:

> Opening your journey...

Instead of:

> Saving...

Prefer:

> Updating your journal...

Instead of:

> Import complete.

Prefer:

> Your journey has been added to the journal.

Instead of:

> Slideshow finished.

Prefer:

> Journey complete.

Language should never break the illusion.

---

# Content Before Interface

The memories are the product.

The interface exists only to support them.

Whenever possible:

* remove controls;
* maximise memories;
* hide navigation;
* minimise interface elements.

If users remember the interface, it has become too visible.

---

# Calm by Default

Magnet Memories should encourage slow interaction.

Avoid:

* flashing animations;
* aggressive transitions;
* bright interface colours;
* crowded layouts;
* unnecessary notifications.

Prefer:

* page turns;
* gentle fades;
* elegant typography;
* generous whitespace;
* quiet confidence.

The application should never demand attention.

It should quietly deserve it.

---

# The Home As Context

Unlike most software, Magnet Memories permanently lives inside a home.

Every screen should therefore be beautiful even when nobody is interacting with it.

The Book Cover should resemble a cherished object rather than a waiting application.

---

# Design For Generations

Families may revisit the same journey twenty years later.

The interface should therefore avoid trends.

Prioritise:

* timelessness;
* simplicity;
* longevity;
* craftsmanship.

Design decisions should still feel appropriate decades from now.

---

# Motion Principles

Motion communicates narrative.

Every animation should resemble interaction with a physical journal.

Preferred transitions include:

**Cover Opening**

Entering a journey.

**Page Turn**

Moving between pages.

**Gentle Fade**

Transitions that should feel invisible.

**Cover Closing**

Returning to the resting state.

Motion should never exist purely for decoration.

---

# Transition Principles

Transitions should feel deliberate.

Book Cover

↓

Journey

should resemble opening a treasured journal.

Journey

↓

Book Cover

should resemble quietly closing it.

Workshop

↓

Book Cover

should resemble placing the journal back onto the shelf.

Nothing should feel abrupt.

---

# Interaction Principles

The interaction model should feel obvious.

Primary gestures:

| Gesture        | Behaviour                |
| -------------- | ------------------------ |
| Tap            | Pause / Resume           |
| Swipe Left     | Previous Page            |
| Swipe Right    | Next Page                |
| Tap Live Photo | Replay                   |
| Long Press     | Reveal Playback Controls |

Hidden gesture combinations should be avoided.

---

# Invisible Interface

The interface should disappear whenever possible.

Journey mode should primarily display:

the page,

not the application.

Navigation only appears when intentionally requested.

---

# Progressive Discovery

The public experience should remain extremely simple.

Advanced functionality belongs in Workshop.

Most users should only ever need to:

* pick up a magnet;
* tap the journal;
* enjoy the journey.

Complexity should remain hidden until intentionally requested.

---

# Creation And Experience Are Separate

Creating memories and reliving memories are different activities.

Workshop should never interrupt Journey mode.

Journey mode should never expose Workshop functionality.

The owner creates.

Everyone remembers.

---

# Technology Should Feel Invisible

Users should think about journeys, not tablets.

They should remember stories, not interfaces.

The best compliment Magnet Memories can receive is:

> "It feels like opening our family travel journal."

Not:

> "That's a beautiful Android app."

---

# Typography Principles

Typography should resemble a beautifully printed travel journal.

Prefer:

* classic serif headings;
* readable body text;
* restrained hierarchy;
* generous margins.

Typography should feel literary rather than technical.

---

# Colour Principles

The visual language should be inspired by heirloom books.

Examples include:

* warm cream paper;
* natural linen;
* aged leather;
* walnut wood;
* brass accents;
* charcoal ink.

Photographs should remain the most colourful element on every page.

---

# The Journal Never Forgets

Every imported journey becomes a permanent chapter in the family's journal.

The journal should never lose memories without the owner's explicit decision.

Original media must never be modified.

Deleting a journey removes only the local copy.

The master archive always remains untouched.

---

# The Three Moments Of Magic

Three interactions define the entire experience.

## 1. The Tap

A travel magnet touches the journal.

The journey begins almost instantly.

This should feel effortless.

---

## 2. The Story

Before any photographs appear, the Memory Card reminds the family why the journey mattered.

This creates emotional context.

---

## 3. The Living Photograph

A still photograph gently comes to life.

This is the moment where memory becomes experience.

These three interactions deserve the highest design attention throughout development.

---

# Accessibility

Accessibility is a core design principle.

Magnet Memories should be enjoyable for:

* children;
* older adults;
* visitors unfamiliar with technology.

Design for:

* large touch targets;
* readable typography;
* sufficient contrast;
* meaningful screen reader labels;
* reduced motion where appropriate.

Accessibility should never compromise elegance.

---

# Anti-Patterns

Magnet Memories deliberately avoids patterns commonly found in productivity applications.

Avoid:

* dashboard layouts;
* bottom navigation bars;
* floating action buttons;
* persistent toolbars;
* technical terminology;
* crowded screens;
* fake paper tears;
* coffee-stain graphics;
* exaggerated leather textures;
* decorative animations without purpose.

Elegance is achieved through restraint.

---

# Success Criteria

The user experience is successful when:

* a child instinctively understands what to do;
* visitors naturally reach for a travel magnet;
* the interface disappears into the background;
* families focus on stories rather than technology;
* every journey feels like reading another chapter of a treasured family journal.

---

# Final Principle

Magnet Memories is not an application that stores photographs.

It is a family travel journal that happens to be digital.

The technology exists only so the stories can live on.

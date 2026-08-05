# UX Principles

**Project:** Magnet Memories

**Version:** 1.0

---

# Purpose

This document defines the user experience principles that guide every design decision within Magnet Memories.

It is not a screen specification or a visual design guide.

Instead, it describes how the product should feel, how people should interact with it, and the principles that should influence every interface decision.

Whenever a design decision is uncertain, these principles take precedence over aesthetics, convenience, or implementation complexity.

---

# UX Vision

Magnet Memories is not software that displays photographs.

It is a piece of interactive furniture that preserves family memories.

The display should feel like a natural part of the home rather than another digital device.

Technology should quietly disappear into the background, allowing memories to become the centre of attention.

---

# Design Philosophy

Every interaction should answer one question:

> **Does this help someone remember?**

If the answer is no, the feature should be simplified, redesigned or removed.

The purpose of Magnet Memories is not to organise photographs.

Its purpose is to help families relive meaningful moments together.

---

# Experience Modes

Magnet Memories consists of three distinct experience modes.

Each mode has a single purpose and a different interaction model.

---

## Gallery

The Gallery is the resting state of Magnet Memories.

It quietly waits for the next journey.

It should feel calm, elegant and inviting.

The Gallery is not an empty screen.

It is an invitation.

Primary purpose:

> Welcome the next journey.

Primary interaction:

> Tap a travel magnet.

The Gallery should always look beautiful, even when nobody is using it.

---

## Journey

Journey mode begins the moment a recognised travel magnet is tapped.

This is the heart of the product.

Journey mode should be immersive and distraction-free.

The interface should almost completely disappear.

Photographs, Live Photos and memories become the experience.

Primary purpose:

> Relive a journey.

The user should never feel like they are operating software.

---

## Studio

Studio is the owner's workspace.

It is where journeys are created, organised and maintained.

Activities include:

* Add Journey
* Import Journey Folder
* Write Memory Cards
* Edit Journey Information
* Program Magnets
* Verify Magnets
* Remove Journeys
* Configure Preferences

Unlike Journey mode, Studio prioritises clarity and efficiency.

Studio should remain visually consistent with the rest of the product while acknowledging that it is a workspace rather than an experience.

---

# One Purpose Per Mode

Each mode exists for exactly one reason.

| Mode    | Purpose |
| ------- | ------- |
| Gallery | Invite  |
| Journey | Relive  |
| Studio  | Create  |

No mode should attempt to perform multiple unrelated tasks.

---

# The Invisible Interface

The interface should never compete with memories.

Controls should only appear when intentionally requested.

During Journey mode:

* hide navigation;
* hide controls;
* hide system chrome where appropriate;
* maximise the memory itself.

If users remember the interface, the interface has become too visible.

---

# Physical Interaction Comes First

The primary interface is the travel magnet.

The touchscreen is secondary.

Users should interact with magnets before they interact with menus.

Navigation should never replace physical interaction.

The NFC tap should feel like unlocking a keepsake rather than launching an application.

---

# Story Before Media

Every journey tells a story.

Photographs support the story.

They are not the story themselves.

Every journey begins with:

1. Cover
2. Memory Card
3. Journey Playback

This order is intentional.

Context should always precede content.

---

# Moments, Not Files

Internally, every journey consists of Moments.

A Moment may be:

* Memory Card
* Photograph
* Live Photo
* Video (future)
* Ticket (future)
* Audio (future)

Users should never think in terms of files or media types.

They should simply experience memories unfolding naturally.

---

# Calm by Default

Magnet Memories should encourage slow interaction.

Avoid:

* flashing elements;
* aggressive animations;
* loud colours;
* unnecessary notifications;
* visual clutter.

Prefer:

* soft fades;
* restrained typography;
* generous whitespace;
* gentle transitions;
* quiet confidence.

The product should never ask for attention.

It should invite it.

---

# The Home as Context

Magnet Memories lives inside a home.

Unlike most applications, it remains visible even when not being used.

Therefore:

* idle screens matter;
* typography matters;
* colours matter;
* transitions matter.

The display should always feel appropriate within a family living space.

---

# Design for Years, Not Months

Families will return to the same journeys many years later.

The interface should therefore be timeless.

Avoid visual trends.

Avoid novelty for its own sake.

Prefer clarity, simplicity and longevity.

---

# Motion Principles

Motion should communicate meaning.

Examples:

Fade

→ Entering a journey.

Slide

→ Moving between moments.

Dissolve

→ Returning to the Gallery.

Motion should never exist purely for decoration.

Animations should feel calm, natural and effortless.

---

# Transition Principles

Moving between experience modes should feel intentional.

Gallery

↓

Journey

should feel ceremonial.

Journey

↓

Gallery

should feel reflective.

Studio

↓

Gallery

should feel like closing a workspace and returning to the exhibition.

Avoid abrupt transitions.

---

# Familiar Interaction

Users should not need to learn the interface.

Interactions should feel obvious.

Primary gestures:

| Gesture        | Behaviour                |
| -------------- | ------------------------ |
| Tap            | Pause / Resume           |
| Swipe Left     | Previous Moment          |
| Swipe Right    | Next Moment              |
| Tap Live Photo | Replay                   |
| Long Press     | Reveal Playback Controls |

Complex gesture combinations should be avoided.

---

# Never Interrupt a Memory

Once Journey mode begins, interruptions should be extremely rare.

Avoid:

* dialogs;
* notifications;
* update prompts;
* confirmation windows;
* advertisements;
* unexpected overlays.

The experience should remain uninterrupted.

---

# Creation and Experience Are Separate

Studio and Journey serve different purposes.

Visitors should rarely encounter Studio.

Likewise, owners should not be forced through Journey interactions while managing their collection.

Creating memories and reliving memories are different experiences.

The interface should respect this distinction.

---

# Respect Attention

Every visual element should earn its place.

If something does not help users remember, it should not appear.

Whitespace is part of the design.

Silence is part of the design.

Stillness is part of the design.

---

# Error Handling

Errors should feel reassuring rather than alarming.

Explain what happened.

Explain what can be done.

Avoid technical language.

A single damaged file should never prevent a family from enjoying the rest of a journey.

---

# Accessibility

Accessibility is a fundamental design requirement.

The experience should be enjoyable for:

* children;
* older adults;
* visitors unfamiliar with technology.

Design principles include:

* large touch targets;
* readable typography;
* sufficient contrast;
* meaningful screen reader labels;
* reduced-motion support where practical.

---

# Moments of Magic

Three interactions define the entire experience.

## 1. The Tap

A travel magnet touches the display.

The journey begins almost instantly.

This should feel effortless.

---

## 2. The Memory Card

Before photographs appear, users are reminded why the journey mattered.

This creates emotional context.

---

## 3. The First Live Photo

A still photograph gently comes to life.

This is the moment the memory feels real.

These three interactions should receive the highest design attention throughout development.

---

# Anti-Patterns

Magnet Memories should deliberately avoid patterns commonly found in productivity applications.

Avoid:

* dashboard layouts;
* floating action buttons;
* bottom navigation bars;
* persistent toolbars during playback;
* crowded screens;
* technical terminology;
* modal dialogs during Journey mode;
* decorative animations without purpose.

If the interface resembles a productivity application, it should be reconsidered.

---

# Success Criteria

The user experience is successful when:

* a child can use the product without guidance;
* a visitor immediately understands what to do;
* the interface disappears into the background;
* families focus on memories rather than controls;
* journeys feel curated rather than browsed;
* users describe the experience as "reliving a journey" instead of "using an app."

---

# UX North Star

Every interaction should reinforce one simple idea:

> **Every magnet tells a story.**

If a feature strengthens that story, it belongs in Magnet Memories.

If it distracts from that story, it does not.

---

# Final Principle

Magnet Memories is not designed to be opened, used and closed.

It is designed to live in a home.

It should be as timeless, welcoming and dependable as the memories it preserves.

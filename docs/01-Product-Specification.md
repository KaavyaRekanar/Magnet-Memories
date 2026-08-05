# Product Specification

**Project:** Magnet Memories

**Version:** 1.0

**Status:** Draft

---

# Purpose

Magnet Memories is an offline-first Android application that transforms physical travel magnets into interactive digital memories.

The application allows users to associate NFC-enabled travel magnets with family journeys. When a magnet is tapped against the tablet, the corresponding journey opens immediately and begins an immersive slideshow consisting of photographs, Live Photos and personal memories.

The application is designed for permanent installation on an Android tablet mounted inside a decorative travel display.

---

# Objectives

The primary objectives are:

* Preserve family memories for decades.
* Encourage families to revisit journeys together.
* Eliminate unnecessary interaction with menus and navigation.
* Provide a calm, immersive viewing experience.
* Operate without requiring cloud services after journeys have been imported.
* Ensure that physical travel magnets remain the primary interaction method.

---

# Target Users

Primary users include:

* Families
* Parents
* Children
* Grandparents
* Visitors

No technical knowledge should be required.

---

# Hardware

## Display

* HMD T21 Android Tablet
* Landscape orientation
* Permanently mounted

## NFC

* Built-in tablet NFC reader
* NTAG216 NFC stickers embedded inside travel magnets

## Storage

Master archive:

* Computer
* External 1 TB hard drive

Display storage:

* Tablet internal storage
* Future support for microSD expansion

---

# Functional Overview

The system consists of six primary capabilities.

1. Journey Management
2. Media Import
3. NFC Interaction
4. Journey Playback
5. Administrative Tools
6. Offline Storage

---

# User Experience

The intended interaction is intentionally simple.

```text
Pick up magnet

↓

Tap tablet

↓

Memory Card

↓

Journey slideshow

↓

Return Home
```

No navigation should be required during normal viewing.

---

# Journey Lifecycle

Each journey progresses through the following lifecycle.

```text
Create Journey

↓

Import Media

↓

Write NFC Tag

↓

Display Journey

↓

Update Journey

↓

Archive Journey
```

---

# Journey Structure

Every journey consists of:

* Cover image
* Journey information
* Memory Card
* Media collection
* Journey settings

---

## Journey Information

Required:

* Journey title
* Unique slug
* Cover photograph

Optional:

* Country
* Region
* City
* Start date
* End date
* Favourite quote
* Memory Card
* Notes

---

# Memory Card

Every journey includes one Memory Card.

The Memory Card is displayed before the slideshow begins.

The Memory Card contains:

* Cover photograph
* Journey title
* Location
* Travel dates
* Favourite quote (optional)
* Personal memory

After a configurable delay (default three seconds), the slideshow begins automatically.

Users may tap anywhere to begin immediately.

---

# Media Types

Version 1 supports:

* JPEG
* PNG
* WebP
* HEIC
* HEIF
* Apple Live Photos

Future versions may support:

* Videos
* Audio
* Documents
* Ticket scans
* Maps

---

# Live Photos

Live Photos are first-class media objects.

Each Live Photo consists of:

* Still photograph
* Motion component

The slideshow should present them as one item.

Motion begins automatically after the still image has been displayed.

Users may replay the Live Photo by tapping it.

---

# Home Screen

The idle display contains:

* Application title
* Invitation to tap a magnet
* Optional rotating background memories
* No technical controls

The home screen should remain visually calm.

---

# NFC Behaviour

When a supported NFC tag is detected:

1. Read URI.
2. Validate URI.
3. Locate matching journey.
4. Open Memory Card.
5. Begin slideshow.

If no journey exists:

Display a friendly message allowing the owner to associate the tag with a journey.

---

# Administrative Mode

Administrative mode is hidden from normal visitors.

It allows the owner to:

* Create journeys
* Import journey folders
* Edit journeys
* Replace cover photographs
* Reorder photographs
* Write NFC tags
* Verify NFC tags
* Delete journeys
* View storage usage
* Configure playback settings

---

# Journey Import

The application imports complete journey folders.

Each folder may contain:

```text
Journey/

photos/

cover.jpg

trip.json
```

The importer should:

* Detect supported images
* Detect Live Photos
* Read optional metadata
* Preview the import
* Estimate storage requirements
* Create tablet-optimised copies
* Preserve originals

The external archive must never be modified.

---

# Playback Experience

Playback should feel cinematic rather than technical.

Requirements:

* Full-screen
* Smooth transitions
* Automatic slideshow
* Gentle animations
* Correct image orientation
* High-quality scaling
* Minimal overlays

The interface should disappear while memories are being viewed.

---

# Playback Controls

Supported gestures:

Tap

* Pause slideshow
* Resume slideshow
* Replay Live Photo

Swipe Left

* Previous memory

Swipe Right

* Next memory

Long Press

* Reveal minimal playback controls

---

# Playback Settings

Users may configure:

* Slide duration
* Transition style
* Live Photo playback
* Live Photo audio
* Background music (future)

---

# Offline Behaviour

After import, journeys must function without:

* Internet
* Google Photos
* External storage
* Computer

All required media should exist locally on the tablet.

---

# Storage Model

Master archive:

Computer + External hard drive

Working library:

Tablet

Deleting a journey removes only local copies.

Original files remain untouched.

---

# Error Handling

The application should fail gracefully.

Examples include:

Unknown NFC tag

→ Offer to associate tag.

Missing media

→ Skip missing file and continue.

Corrupted image

→ Display placeholder.

Low storage

→ Warn before import.

Unsupported format

→ Skip and report.

The slideshow should never terminate unexpectedly because of a single file.

---

# Performance Goals

Journey opening:

< 1 second after NFC detection.

Slideshow transitions:

Smooth at 60 fps where possible.

Memory usage:

Avoid loading unnecessary full-resolution images.

Imports:

Run in the background.

---

# Accessibility

The application should support:

* Large touch targets
* High contrast
* Readable typography
* Landscape viewing
* Reduced motion preferences where practical

---

# Security

The application should:

* Never upload photographs automatically.
* Never modify original files.
* Never write personal information to NFC tags.
* Never require cloud accounts for playback.

---

# Privacy

All imported journeys belong exclusively to the family.

No analytics.

No advertising.

No third-party tracking.

No cloud synchronisation in Version 1.

---

# Future Roadmap

Future versions may introduce:

* Journey maps
* Timeline view
* Video support
* Audio memories
* "On This Day"
* Search
* Multiple family members
* AI-assisted story writing
* Printed journey books

These features are explicitly outside the scope of Version 1.

---

# Success Criteria

Version 1 is complete when a user can:

1. Create a journey.
2. Import photographs.
3. Import Live Photos.
4. Write an NFC tag.
5. Tap the travel magnet.
6. Read the Memory Card.
7. Watch the journey slideshow.
8. Return automatically to the home screen.

No internet connection should be required after import.

---

# Product Definition

Magnet Memories is not a photo gallery.

It is not a cloud backup service.

It is not a travel planner.

It is a digital family keepsake that transforms physical souvenirs into interactive memories.

Every feature should reinforce one simple idea:

> **Every magnet tells a story.**

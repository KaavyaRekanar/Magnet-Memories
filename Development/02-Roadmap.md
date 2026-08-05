# Roadmap

**Project:** Magnet Memories
**Document:** Development Roadmap
**Version:** 1.0
**Status:** Living Document

---

# Purpose

This roadmap defines the recommended implementation order for Magnet Memories.

It is **not** a feature wishlist.

It is an engineering plan.

The objective is to build the system incrementally while preserving architectural integrity and maintaining a usable application at the end of every phase.

Each phase should produce software that:

* compiles successfully;
* passes automated tests;
* respects the documented architecture;
* remains releasable.

No phase should introduce temporary shortcuts that require later architectural rewrites.

---

# Guiding Principles

Development should follow several principles.

## Build Foundations Before Features

Infrastructure should always precede user-facing functionality.

Avoid building interfaces that depend on architecture which does not yet exist.

---

## Keep the Application Working

The application should remain functional throughout development.

Long-lived broken branches should be avoided.

---

## Test Continuously

Every completed feature should include appropriate automated tests.

Testing is part of implementation rather than a separate phase.

---

## Documentation Evolves with Code

Documentation and implementation should remain synchronised.

Whenever architectural behaviour changes, the corresponding documentation should be updated.

---

# Version 0.1 — Project Foundation

## Goal

Establish the engineering foundation.

## Deliverables

* Android project setup
* Jetpack Compose configuration
* Material 3 theme foundation
* Package structure
* Dependency injection
* Navigation framework
* Room integration
* Build configuration
* Static analysis
* Continuous Integration pipeline
* Unit testing framework
* Repository documentation

## Success Criteria

The project builds successfully with a clean architecture ready for feature development.

---

# Version 0.2 — Journal Foundation

## Goal

Create the core data model and journal experience.

## Deliverables

* Journey entity
* Moment entity
* Memory Card entity
* Repository layer
* Local database
* Sample journal data
* Basic journal rendering
* Typography implementation
* Leather-bound visual theme
* Initial page layout

## Success Criteria

The application can display a static Journey without imported media.

---

# Version 0.3 — Media Pipeline

## Goal

Support media processing and playback.

## Deliverables

* Image loading
* Thumbnail generation
* Live Photo pairing
* Media validation
* Playback copies
* Image caching
* Basic page transitions
* Moment rendering

## Success Criteria

Journeys display imported media correctly.

---

# Version 0.4 — Import Pipeline

## Goal

Enable reliable Journey import.

## Deliverables

* Folder discovery
* Metadata extraction
* Journey validation
* Media processing
* Progress reporting
* Import recovery
* Duplicate detection
* Temporary workspace
* Verification before commit

## Success Criteria

Owners can import complete Journeys from the archive.

---

# Version 0.5 — Journal Experience

## Goal

Transform imported content into a polished journal.

## Deliverables

* Guided Journey mode
* Explore Journey mode
* Ambient Journey mode
* Page-turn animation
* Journey cover
* Memory Card presentation
* Story transitions
* Reading flow
* Responsive layouts

## Success Criteria

The journal feels natural, immersive and distraction-free.

---

# Version 0.6 — NFC Workshop

## Goal

Connect physical magnets to digital Journeys.

## Deliverables

* NFC scanning
* NFC writing
* Journey association
* Tag validation
* Workshop interface
* Tag replacement workflow
* Error handling
* Physical setup guidance

## Success Criteria

Scanning a magnet immediately opens the associated Journey.

---

# Version 0.7 — Backup and Recovery

## Goal

Protect long-term reliability.

## Deliverables

* Backup creation
* Restore workflow
* Recovery verification
* Archive validation
* Incident recovery support
* Recovery diagnostics
* Storage verification

## Success Criteria

Journeys can be recovered after device replacement or storage failure.

---

# Version 0.8 — Polish

## Goal

Refine the complete experience.

## Deliverables

* Performance optimisation
* Accessibility improvements
* Animation refinement
* Typography adjustments
* Error message review
* Loading state improvements
* Haptic feedback
* Final UX polish

## Success Criteria

The application feels calm, responsive and complete.

---

# Version 0.9 — Release Candidate

## Goal

Prepare for Version 1.

## Deliverables

* Full regression testing
* Security review
* Performance validation
* Documentation review
* Architecture review
* Bug fixing
* Release preparation

## Success Criteria

The application satisfies all documented architectural guarantees.

---

# Version 1.0 — First Public Release

## Goal

Deliver the complete Magnet Memories experience.

## Features

* Leather-bound journal experience
* Offline-first architecture
* Journey import
* Memory Cards
* Live Photos
* NFC-powered playback
* Three playback modes
* Backup and recovery
* Comprehensive testing
* Complete documentation

## Success Criteria

A family can:

1. Import a Journey.
2. Write a Memory Card.
3. Program an NFC magnet.
4. Scan the magnet.
5. Revisit the Journey years later without requiring internet connectivity.

If these steps can be completed reliably, Version 1 has achieved its primary objective.

---

# Beyond Version 1

The following ideas are intentionally deferred.

They should not influence Version 1 implementation unless explicitly approved through an Architecture Decision Record.

Potential future capabilities include:

* Optional encrypted cloud synchronisation
* Multi-device journal synchronisation
* Desktop companion application
* Family sharing
* AI-assisted Memory Card drafting
* Smart Journey search
* Journal export to PDF
* Printed travel journal generation
* Plugin architecture
* Apple Live Photos enhancements
* Shared household journal
* Companion smartwatch interactions

These ideas are valuable, but they should not compromise the simplicity and architectural integrity of Version 1.

---

# Definition of Complete

Version 1 should not be considered complete because every planned feature has been implemented.

Instead, it is complete when the following statement is true:

> A family can place an NFC magnet on their fridge, scan it years later, and immediately relive a meaningful journey through a beautiful, private, offline-first travel journal—without needing to think about the technology that makes it possible.

If achieving that experience requires leaving some non-essential features for a future release, those features should be deferred.

---

# Final Principle

This roadmap is intentionally conservative.

The objective is not to build the largest application.

The objective is to build the right application.

Every phase should strengthen the architecture established in this repository.

Features may evolve.

Technologies may change.

The central vision should remain constant.

> **Magnet Memories succeeds when the technology quietly disappears and the memories become the only thing the owner notices.**

# Architecture Decision Records (ADR)

**Project:** Magnet Memories
**Document:** Architecture Decision Records
**Version:** 1.0
**Status:** Living Document

---

# Purpose

This document records the significant architectural decisions made during the design and implementation of Magnet Memories.

Unlike implementation documentation, Architecture Decision Records explain **why** a particular decision was made.

As the project evolves, future contributors should update this document whenever an architectural decision materially changes the system.

The purpose of ADRs is to preserve the reasoning behind the architecture, ensuring that future development remains aligned with the project's long-term vision.

---

# Using This Document

Every ADR follows the same structure.

```text
ADR-XXX — Decision Title

Status
Date
Decision Makers

Context

Decision

Alternatives Considered

Consequences

Future Considerations
```

Status may be one of:

* Proposed
* Accepted
* Superseded
* Deprecated

---

# ADR-001 — Local-First Architecture

**Status:** Accepted

## Context

Many modern applications depend on cloud services for storage, authentication and synchronisation.

While convenient, these dependencies introduce additional trust requirements and reduce long-term independence.

## Decision

Magnet Memories will be designed as a **local-first application**.

Every Journey will remain fully functional after import without requiring:

* internet connectivity;
* cloud services;
* user accounts;
* remote APIs.

## Alternatives Considered

* Cloud-first architecture
* Hybrid cloud architecture
* Mandatory synchronisation

## Consequences

### Positive

* Strong privacy
* Reliable offline operation
* Long-term independence
* Reduced attack surface

### Negative

* Manual backup responsibility
* Cross-device synchronisation deferred

## Future Considerations

Optional cloud synchronisation may be added later, but it must extend—not replace—the local-first architecture.

---

# ADR-002 — Immutable External Archive

**Status:** Accepted

## Context

Original family photographs are irreplaceable.

Allowing the application to modify the archive increases the risk of accidental data loss.

## Decision

The external archive is permanently treated as read-only.

The application imports from the archive but never modifies it.

## Alternatives Considered

* In-place editing
* Direct playback from archive

## Consequences

### Positive

* Original media preserved
* Safer imports
* Simplified recovery

### Negative

* Duplicate storage required

## Future Considerations

Future versions may verify archive integrity but will never modify archived originals automatically.

---

# ADR-003 — NFC Tags Store Only Journey URIs

**Status:** Accepted

## Context

NFC tags have limited storage and may be read by anyone with an NFC-capable device.

## Decision

Each NFC tag stores only a URI containing the Journey identifier.

Example:

```text
magnetmemories://journey/new-york-2026
```

No personal information is stored on the tag.

## Alternatives Considered

* JSON payloads
* Journey metadata
* Thumbnail images
* Binary identifiers

## Consequences

### Positive

* Simple implementation
* Excellent Android compatibility
* Minimal privacy exposure
* Easy reprogramming

### Negative

* Journey lookup required after scan

## Future Considerations

Opaque identifiers may replace human-readable slugs if stronger privacy is required.

---

# ADR-004 — Journey Slugs as Stable Identifiers

**Status:** Accepted

## Context

Journeys require stable references for NFC programming, storage and deep links.

## Decision

Every Journey receives a unique slug that serves as its public identifier.

The slug remains stable unless explicitly changed by the owner.

## Alternatives Considered

* Numeric identifiers
* UUIDs
* Folder names

## Consequences

### Positive

* Human readable
* Stable NFC mapping
* Predictable URLs

### Negative

* Slug changes require NFC reprogramming

---

# ADR-005 — Leather-Bound Journal Experience

**Status:** Accepted

## Context

The goal of the application is to recreate the feeling of opening a treasured travel journal rather than browsing a media gallery.

## Decision

Playback uses a leather-bound journal aesthetic.

Scanning an NFC magnet immediately opens the relevant Journey.

Traditional galleries, grids and navigation menus are intentionally avoided during playback.

## Alternatives Considered

* Gallery interface
* Timeline interface
* Folder browser

## Consequences

### Positive

* Memorable experience
* Strong emotional connection
* Distraction-free storytelling

### Negative

* Less suitable for large-scale media browsing

---

# ADR-006 — Live Photos as First-Class Moments

**Status:** Accepted

## Context

Live Photos represent a single memory consisting of both a still image and a short motion clip.

Treating them as independent files would weaken the storytelling experience.

## Decision

Live Photos are modelled as a single Moment with two media components.

## Alternatives Considered

* Separate image and video Moments
* Converting Live Photos to still images

## Consequences

### Positive

* Preserves the original capture experience
* Simplifies playback
* Supports future motion enhancements

---

# ADR-007 — Memory Cards Before Media

**Status:** Accepted

## Context

Stories provide the emotional context that transforms photographs into memories.

## Decision

Each Journey opens with a Memory Card before displaying media.

Memory Cards become the narrative introduction to the Journey.

## Alternatives Considered

* Story after playback
* Optional story
* Caption-only approach

## Consequences

### Positive

* Encourages storytelling
* Preserves context
* Reinforces journal metaphor

---

# ADR-008 — Three Playback Modes

**Status:** Accepted

## Context

Families revisit memories in different ways depending on the situation.

## Decision

Version 1 supports three playback modes:

* Guided Journey
* Explore Journey
* Ambient Journey

Each mode presents the same memories differently without changing the underlying data.

## Alternatives Considered

* Single playback mode

## Consequences

### Positive

* Flexible viewing experience
* Supports different contexts
* Shared architecture

---

# ADR-009 — Relative Paths Instead of Absolute Paths

**Status:** Accepted

## Context

Absolute filesystem paths reduce portability and complicate migration.

## Decision

All database references use relative paths.

## Alternatives Considered

* Absolute filesystem paths
* Content URIs as permanent identifiers

## Consequences

### Positive

* Portable
* Easier restoration
* Device independent

---

# ADR-010 — Copy Rather Than Reference Imported Media

**Status:** Accepted

## Context

External storage may become unavailable after import.

## Decision

Imported media is copied into app-managed storage.

Playback never depends on continued access to the original archive.

## Alternatives Considered

* Direct playback from external storage
* Symbolic references

## Consequences

### Positive

* Reliable offline playback
* Predictable performance
* Independent Journeys

### Negative

* Additional storage required

---

# ADR-011 — App-Private Storage

**Status:** Accepted

## Context

Journey media should not be modified by unrelated applications.

## Decision

Processed media is stored in Android app-private storage.

## Alternatives Considered

* Shared media folders
* Public storage

## Consequences

### Positive

* Improved integrity
* Reduced accidental modification
* Simpler permission model

---

# ADR-012 — Security Through Simplicity

**Status:** Accepted

## Context

Complex systems are more difficult to understand, test and secure.

## Decision

Architectural simplicity is treated as a security objective.

Features should minimise unnecessary complexity wherever practical.

## Consequences

This principle influences every future architectural decision.

---

# Future ADRs

The following topics are expected to receive their own ADRs as the project evolves:

* Encrypted backup format
* Cloud synchronisation architecture
* Family sharing
* Desktop companion application
* AI-assisted Memory Card generation
* Search architecture
* Journal export format
* Plugin architecture
* Accessibility strategy
* Performance optimisation strategy

---

# Maintaining ADRs

Architectural decisions should be updated whenever:

* a significant design change is accepted;
* an earlier decision is replaced;
* new capabilities alter existing assumptions.

Older ADRs should never be deleted.

Instead, they should be marked as **Superseded** and linked to the newer decision.

Maintaining this historical record helps contributors understand not only the current architecture, but also how it evolved over time.

---

# Final Principle

> **Architecture is remembered not by the code that survives, but by the decisions that explain why that code exists. Architecture Decision Records preserve that reasoning so future contributors can build upon it rather than unknowingly undo it.**

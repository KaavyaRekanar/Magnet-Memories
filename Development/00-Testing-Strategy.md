# Testing Strategy

**Project:** Magnet Memories
**Document:** Testing Strategy
**Version:** 1.0
**Status:** Living Document

---

# 1. Purpose

## 1.1 Overview

This document defines the testing strategy for Magnet Memories.

Its purpose is to ensure that every feature released into the application is:

* functionally correct;
* reliable;
* recoverable;
* performant;
* secure;
* maintainable.

Testing is considered an architectural responsibility rather than a final development phase.

Every component should be designed with testability in mind from the beginning.

---

## 1.2 Objectives

The testing strategy has six primary objectives.

* Verify functional correctness.
* Preserve architectural guarantees.
* Detect regressions early.
* Protect family memories from accidental loss.
* Build confidence for future refactoring.
* Enable AI-assisted development without sacrificing quality.

---

# 2. Testing Philosophy

Magnet Memories follows several fundamental testing principles.

## Test Behaviour, Not Implementation

Tests should verify observable behaviour rather than internal implementation details.

Internal classes may change.

Expected behaviour should remain stable.

---

## Test the Journey, Not Just the Function

Many features span multiple architectural layers.

Whenever possible, tests should verify complete workflows instead of isolated functions.

Examples include:

* importing an entire Journey;
* scanning an NFC tag;
* recovering after interruption;
* restoring from backup.

---

## Protect Memories First

When trade-offs exist, testing effort should prioritise protecting family memories over less critical functionality.

Examples:

High priority:

* Memory Cards
* Journey metadata
* Import correctness
* Backup
* Recovery

Lower priority:

* animations
* transition timing
* visual polish

---

## Architecture Before Features

Every new feature should first satisfy the architectural guarantees before adding new capabilities.

---

## Every Bug Becomes a Test

Whenever a defect is discovered:

1. write a failing test;
2. fix the defect;
3. verify the test passes.

This prevents regressions.

---

# 3. Testing Pyramid

The project follows a layered testing strategy.

```text
                Manual Validation
                       ▲
                UI / End-to-End
                       ▲
              Integration Tests
                       ▲
                  Unit Tests
```

Lower layers should contain the greatest number of tests.

Higher layers validate complete user experiences.

---

# 4. Unit Testing

## Purpose

Unit tests verify the behaviour of individual classes and functions.

They should execute quickly and deterministically.

---

## Typical Candidates

* slug generation
* URI parsing
* metadata extraction
* date formatting
* Journey validation
* filename sanitisation
* Live Photo pairing
* sorting logic
* Room converters

---

## Requirements

Unit tests should:

* avoid Android dependencies where possible;
* avoid network access;
* avoid filesystem dependencies unless required;
* execute independently;
* be deterministic.

---

# 5. Integration Testing

Integration tests verify collaboration between architectural components.

Examples include:

* Import Pipeline → Media Pipeline
* Media Pipeline → Room
* Room → Playback
* NFC → Journey Lookup
* Backup → Restore

Integration tests should validate that components work together as intended.

---

# 6. End-to-End Testing

End-to-end tests simulate complete owner workflows.

Examples include:

## Journey Import

Select folder

↓

Validate media

↓

Generate playback copies

↓

Create database records

↓

Playback Journey

---

## NFC Playback

Scan NFC

↓

Resolve Journey

↓

Open journal

↓

Render Memory Card

↓

Display media

---

## Backup Recovery

Create backup

↓

Install application

↓

Restore backup

↓

Verify Journey

---

End-to-end tests should closely resemble real owner behaviour.

---

# 7. User Interface Testing

UI testing verifies visual correctness and interaction behaviour.

Areas include:

* journal navigation;
* page transitions;
* typography;
* image rendering;
* Live Photo playback;
* dialog behaviour;
* error messages;
* accessibility.

Animations should never interfere with usability.

---

# 8. NFC Testing

Every NFC implementation should be validated using both simulated and physical tags.

Test cases include:

* valid Journey URI;
* unknown Journey;
* malformed URI;
* unsupported scheme;
* empty tag;
* corrupted NDEF;
* rewritten tag;
* duplicate tag.

Expected behaviour should always be deterministic.

---

# 9. Media Testing

Media testing validates supported image and video formats.

Supported examples include:

* HEIC
* JPEG
* PNG
* MOV
* MP4

Tests should verify:

* orientation;
* dimensions;
* thumbnail generation;
* playback;
* unsupported formats;
* malformed files.

---

# 10. Live Photo Testing

Live Photos are treated as first-class Moments.

Tests should verify:

* successful pairing;
* missing still image;
* missing motion file;
* duplicate identifiers;
* incorrect timestamps;
* playback synchronisation;
* thumbnail selection.

Every Live Photo should behave as a single logical object.

---

# 11. Import Pipeline Testing

The Import Pipeline is one of the most critical components.

Testing should include:

* complete Journey import;
* duplicate imports;
* interrupted import;
* cancelled import;
* corrupted media;
* unsupported formats;
* duplicate filenames;
* malformed folders;
* storage exhaustion.

Every import must either:

* complete successfully, or
* fail safely.

Partial Journeys must never become visible.

---

# 12. Storage Testing

Storage testing validates:

* app-private storage;
* relative paths;
* backup generation;
* restore;
* archive isolation;
* temporary workspace cleanup.

The external archive should never be modified.

---

# 13. Recovery Testing

Every recovery workflow defined in the Incident Recovery document should be tested.

Examples include:

* interrupted import;
* tablet replacement;
* backup restore;
* corrupted database;
* damaged NFC tag;
* low storage;
* application restart;
* power failure.

Recovery should preserve owner confidence.

---

# 14. Security Testing

Security testing verifies the architectural guarantees.

Examples include:

* path traversal attempts;
* malformed NFC payloads;
* oversized media;
* malformed EXIF;
* invalid UTF-8;
* malformed JSON;
* duplicate Journey slugs;
* invalid backup manifests.

The objective is graceful rejection rather than application failure.

---

# 15. Performance Testing

Performance should be measured on the target hardware.

Primary scenarios include:

* importing 500+ photographs;
* importing multiple Live Photos;
* opening large Journeys;
* rapid page turning;
* thumbnail generation;
* scrolling through Moments.

Performance targets should prioritise perceived responsiveness.

---

# 16. Accessibility Testing

Accessibility is a required quality attribute.

Testing should verify:

* scalable text;
* TalkBack compatibility;
* keyboard navigation where applicable;
* sufficient colour contrast;
* touch target sizes;
* animation reduction support.

Accessibility regressions should be treated as functional defects.

---

# 17. Regression Testing

Before every release:

* execute automated test suite;
* verify critical user journeys;
* test backup and restore;
* validate NFC scanning;
* verify import pipeline;
* confirm architectural guarantees remain satisfied.

No release should introduce regressions in previously working behaviour.

---

# 18. Test Data

Test datasets should include realistic travel content.

The repository should maintain reusable datasets representing:

## Small Journey

* 10 photographs
* 2 Live Photos
* 1 Memory Card

---

## Medium Journey

* 150 photographs
* 25 Live Photos

---

## Large Journey

* 1,000+ photographs
* multiple videos
* extensive Memory Cards

---

## Edge Cases

* corrupted HEIC
* corrupted MOV
* duplicate filenames
* missing metadata
* unsupported codecs
* empty folders

Personally identifiable information should not be included in repository test assets.

---

# 19. Continuous Integration

Every pull request should automatically execute:

* formatting checks;
* static analysis;
* unit tests;
* integration tests;
* architectural validation where possible.

No pull request should be merged if mandatory quality checks fail.

---

# 20. Definition of Done

A feature is considered complete only when all of the following are true:

* Requirements implemented.
* Architecture remains compliant.
* Relevant ADRs reviewed.
* Unit tests added or updated.
* Integration tests added where required.
* Existing tests continue to pass.
* No new security issues introduced.
* Documentation updated.
* Accessibility considered.
* Error handling implemented.
* Performance impact evaluated.
* Code reviewed.

Implementation alone does not constitute completion.

---

# 21. Quality Gates

The following quality gates apply throughout development.

| Area              | Minimum Expectation                              |
| ----------------- | ------------------------------------------------ |
| Unit Tests        | Added for new business logic                     |
| Integration Tests | Required for cross-component behaviour           |
| End-to-End Tests  | Required for critical user journeys              |
| Security Testing  | Required for trust boundary changes              |
| Recovery Testing  | Required for import, backup and storage features |
| Documentation     | Updated alongside architectural changes          |

A feature that bypasses these quality gates should not be considered production ready.

---

# 22. Relationship to Architecture

Testing exists to verify the promises made elsewhere in the documentation.

In particular, the test suite should continuously validate:

* System Architecture
* Data Model
* Import Pipeline
* NFC Specification
* Storage and Backup
* Security & Privacy Architecture
* Architectural Guarantees

Tests should evolve alongside the architecture rather than independently of it.

---

# 23. Future Testing

As Magnet Memories grows, additional testing areas may include:

* cloud synchronisation;
* encrypted backups;
* desktop companion applications;
* family sharing;
* AI-assisted features;
* cross-device migration;
* plugin compatibility.

Every significant capability should introduce corresponding automated tests before release.

---

## Final Principle

Testing is not performed to prove that the software works.

It is performed to build confidence that the software will continue to work as the project evolves.

For Magnet Memories, that confidence carries particular importance.

Every successful test helps ensure that a family's stories, photographs and memories remain available, trustworthy and enjoyable for years to come.

> **The quality of Magnet Memories is measured not by the number of features it contains, but by the confidence that every captured memory will still be there when someone chooses to revisit it.**

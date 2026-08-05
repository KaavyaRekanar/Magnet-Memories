# 12. Architectural Guarantees

## 12.1 Overview

Architectural Guarantees define the non-negotiable properties of Magnet Memories.

Unlike implementation details, these guarantees are intended to remain true regardless of:

* programming language;
* Android version;
* user interface redesign;
* storage implementation;
* database schema evolution;
* future feature additions.

Every future implementation should preserve these guarantees unless a formal Architecture Decision Record explicitly replaces them.

These guarantees therefore serve as the architectural contract between the design and its implementation.

---

# 12.2 Why Architectural Guarantees Exist

Software evolves continuously.

Libraries change.

Frameworks become obsolete.

Implementation techniques improve.

Without stable architectural guarantees, a system can gradually drift away from the principles that originally defined it.

Architectural Guarantees prevent this drift.

They establish behaviours that future implementations must preserve even when every line of code has changed.

---

# 12.3 Guarantee Categories

The guarantees are organised into six categories.

```text id="g9b8u2"
Architectural Guarantees
│
├── Data
├── Privacy
├── Storage
├── Recovery
├── Security
└── Experience
```

Together they describe the expected behaviour of the system throughout its lifetime.

---

# 12.4 Data Guarantees

## AG-001 — Journeys Remain Local

Once imported, every Journey is completely usable without requiring:

* internet connectivity;
* cloud services;
* remote APIs;
* online authentication.

Offline playback is a permanent property of the architecture.

---

## AG-002 — The Archive Is Never Modified

The external archive is permanently treated as read-only.

The application may:

* discover;
* validate;
* import;
* verify.

It must never automatically:

* rename;
* overwrite;
* reorganise;
* delete;
* modify

the owner's archived originals.

---

## AG-003 — Imported Journeys Are Independent

After successful import, Journey playback never depends on continued access to:

* the external hard drive;
* the original folder;
* Storage Access Framework permissions.

The imported journal is self-contained.

---

## AG-004 — Every Journey Has One Stable Identity

Every Journey is represented by one stable identifier.

This identifier remains consistent across:

* playback;
* storage;
* NFC programming;
* backup;
* restore.

Changing a Journey's identifier is considered an exceptional operation.

---

## AG-005 — Every Moment Belongs to Exactly One Journey

A Moment cannot exist without a parent Journey.

Likewise, media files belong to one—and only one—Moment.

Ownership is explicit throughout the data model.

---

# 12.5 Privacy Guarantees

## AG-006 — No Mandatory User Accounts

Version 1 never requires:

* registration;
* login;
* passwords;
* identity providers.

Families own their memories without needing an online identity.

---

## AG-007 — No Automatic Cloud Upload

The application never uploads photographs or Journey data automatically.

Any future cloud capability must always require explicit owner consent.

---

## AG-008 — NFC Tags Never Contain Personal Information

NFC tags store only the minimum information required to identify a Journey.

They never store:

* photographs;
* Memory Cards;
* names;
* locations;
* personal stories.

Loss of an NFC tag must not expose family memories.

---

## AG-009 — Metadata Is Intentionally Minimized

Only metadata with a documented purpose is retained permanently.

Information that does not improve:

* storytelling;
* playback;
* recovery;
* reliability

should not become part of the permanent journal.

---

## AG-010 — Privacy Is the Default

The owner should obtain meaningful privacy without changing any settings.

Privacy is achieved primarily through architectural design rather than configuration.

---

# 12.6 Storage Guarantees

## AG-011 — Media Is Stored in App-Private Storage

Processed Journey media is stored within Android's application sandbox.

Other applications should not modify imported Journeys directly.

---

## AG-012 — Permanent References Are Portable

The application stores relative references rather than device-specific filesystem paths.

Journeys remain portable across future devices and restoration workflows.

---

## AG-013 — Temporary Data Never Becomes Permanent Accidentally

Temporary processing artefacts are isolated from the permanent journal.

Examples include:

* import workspaces;
* intermediate thumbnails;
* transcoding outputs;
* pairing information.

These artefacts should never appear in normal playback.

---

## AG-014 — Verification Precedes Persistence

Media must be:

1. processed;
2. verified;
3. committed.

No unverified media becomes part of a permanent Journey.

---

# 12.7 Recovery Guarantees

## AG-015 — Incomplete Imports Never Appear as Valid Journeys

Interrupted imports remain invisible until they satisfy all readiness requirements.

Owners should never encounter partially imported Journeys.

---

## AG-016 — Recovery Never Modifies the Archive

Recovery procedures operate on the local journal.

The archive remains untouched throughout repair workflows.

---

## AG-017 — Existing Data Is Preserved Before Repair

Whenever practical, the application preserves existing data before attempting corrective actions.

Recovery should minimise additional risk.

---

## AG-018 — Recovery Is Deterministic

Equivalent incidents should follow equivalent recovery workflows.

Predictable recovery improves trust and simplifies testing.

---

# 12.8 Security Guarantees

## AG-019 — External Input Is Never Trusted Automatically

Every external input must pass through appropriate validation before entering the trusted domain.

Examples include:

* NFC tags;
* imported folders;
* metadata;
* backup packages;
* media files.

---

## AG-020 — Trust Boundaries Are Explicit

Every movement of information between trust zones must have a documented purpose.

Trust is earned through validation rather than assumed.

---

## AG-021 — Significant Actions Require Explicit Intent

The application never silently performs significant owner-affecting actions.

Examples include:

* deleting Journeys;
* rewriting NFC tags;
* restoring backups;
* changing Journey identifiers.

These operations always require deliberate owner action.

---

## AG-022 — Simplicity Is Preserved

Architectural simplicity is treated as a security feature.

Future features should avoid introducing unnecessary complexity where simpler alternatives provide equivalent value.

---

# 12.9 Experience Guarantees

## AG-023 — Magnets Are the Navigation

Scanning an NFC magnet always attempts to open the associated Journey directly.

Owners should not need to browse galleries or folders during normal playback.

---

## AG-024 — Stories Come Before Photographs

Every Journey begins with its Memory Card.

Narrative context precedes visual content.

This ordering reflects the philosophy that memories are stories first and media second.

---

## AG-025 — Playback Remains Calm

The journal experience should remain focused on memories rather than technology.

Security, validation and recovery should occur quietly whenever possible without unnecessary interruption.

---

## AG-026 — The Journal Is the Primary Experience

The application presents Journeys as a curated travel journal rather than a generic media browser.

Future features should strengthen—not weaken—that metaphor.

---

# 12.10 Evaluating Future Features

Every proposed feature should be evaluated against these guarantees.

Questions to ask include:

* Does this require additional trust?
* Does it weaken privacy by default?
* Does it introduce unnecessary complexity?
* Does it compromise offline operation?
* Does it affect recoverability?
* Does it preserve owner control?
* Does it maintain the journal experience?

Features that conflict with these guarantees require explicit architectural review before acceptance.

---

# 12.11 Relationship to ADRs

Architectural Guarantees and Architecture Decision Records complement one another.

Architectural Guarantees define **what must remain true**.

Architecture Decision Records explain **why specific design choices were made**.

If an Architectural Guarantee must change, the corresponding ADR should also be reviewed and updated.

Together they provide both stability and historical context for the evolution of the project.

---

# 12.12 Verifying the Guarantees

These guarantees should not remain purely aspirational.

Where practical, they should be verified through:

* automated tests;
* integration tests;
* architecture reviews;
* code reviews;
* documentation reviews.

Future contributors should be able to trace implementation decisions back to one or more guarantees defined in this chapter.

---

# 12.13 Long-Term Evolution

As Magnet Memories grows, new guarantees may be introduced.

Existing guarantees should only be removed when:

* they no longer reflect the intended architecture;
* a superior guarantee replaces them;
* the change is documented in an Architecture Decision Record.

Architectural Guarantees should evolve deliberately rather than incidentally.

---

## Final Principle

Architecture is ultimately about preserving intent.

Code will change.

Frameworks will change.

Devices will change.

Families will continue making memories.

These guarantees exist to ensure that, regardless of how the implementation evolves, the core promises of Magnet Memories remain intact.

> **The architecture of Magnet Memories is built on a simple promise: family memories should remain private, understandable, recoverable, and under the owner's control—not just today, but for many years to come.**

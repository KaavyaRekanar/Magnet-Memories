# 10. Metadata Policy

## 10.1 Overview

Every photograph contains far more information than its visible pixels.

Modern cameras and smartphones embed extensive metadata describing:

* when a photograph was taken;
* where it was captured;
* which device created it;
* how it was processed;
* camera settings;
* software history;
* unique identifiers.

Much of this information is technically useful.

Very little of it is essential to the experience that Magnet Memories is designed to provide.

This chapter defines the project's metadata policy.

Its purpose is to answer a single architectural question:

> **When information enters Magnet Memories, what should happen to it?**

Every piece of imported metadata must be assigned one of four outcomes:

* Retain
* Transform
* Use Temporarily
* Discard

This policy ensures that metadata handling remains consistent throughout the lifetime of the project.

---

# 10.2 Design Principles

The metadata policy is guided by four principles.

## Preserve Meaning

Metadata that contributes to storytelling should be retained.

Examples include:

* capture dates;
* Journey ordering;
* owner-written stories.

---

## Minimise Personal Information

Metadata that does not improve the owner's experience should generally not be stored.

The absence of unnecessary information improves privacy.

---

## Derive Rather Than Persist

If a value can be recomputed safely, it should not normally be stored permanently.

Derived information is preferable to duplicated information.

---

## Temporary Before Permanent

Some metadata is useful only during import.

Examples include:

* pairing identifiers;
* decoder information;
* orientation flags.

Such information should disappear once processing has completed.

---

# 10.3 Metadata Lifecycle

Every imported metadata field follows the same lifecycle.

```text id="i7t6ux"
Imported

↓

Validated

↓

Classified

↓

Retain
Transform
Temporary
Discard

↓

Journal
```

Classification occurs during the Import Pipeline.

No metadata bypasses this process.

---

# 10.4 Metadata Categories

Magnet Memories classifies metadata into five categories.

## Story Metadata

Information that contributes directly to the owner's memories.

Examples:

* capture date;
* Journey title;
* Memory Cards;
* favourite quotes.

Story metadata has the highest long-term value.

---

## Playback Metadata

Information required to display media correctly.

Examples:

* dimensions;
* rotation;
* playback duration;
* aspect ratio.

---

## Pairing Metadata

Temporary information used while constructing Live Photos.

Examples:

* pairing identifiers;
* capture timestamps;
* internal matching keys.

These values normally disappear after import.

---

## Technical Metadata

Information describing how a photograph was created.

Examples:

* camera model;
* lens model;
* exposure;
* ISO;
* shutter speed.

Version 1 does not require this information.

---

## Personal Metadata

Information capable of revealing unnecessary details about the owner.

Examples:

* GPS coordinates;
* device serial numbers;
* software identifiers;
* cloud references.

The application intentionally minimises permanent storage of this category.

---

# 10.5 Metadata Classification Matrix

| Metadata                   |  Import |   Store   | Display | Reason                            |
| -------------------------- | :-----: | :-------: | :-----: | --------------------------------- |
| Journey Title              |    ✓    |     ✓     |    ✓    | Narrative                         |
| Journey Dates              |    ✓    |     ✓     |    ✓    | Story chronology                  |
| Memory Card                |    ✓    |     ✓     |    ✓    | Core feature                      |
| Favourite Quote            |    ✓    |     ✓     |    ✓    | Storytelling                      |
| Capture Date               |    ✓    |     ✓     |    ✓    | Ordering                          |
| Capture Time               |    ✓    |  Optional |    ✗    | Playback sequencing               |
| Image Width                |    ✓    |  Derived  |    ✗    | Rendering                         |
| Image Height               |    ✓    |  Derived  |    ✗    | Rendering                         |
| Orientation                |    ✓    |  Derived  |    ✗    | Rendering                         |
| Duration                   |    ✓    |     ✓     |    ✗    | Live Photos                       |
| Thumbnail                  | Derived |     ✓     |    ✓    | Performance                       |
| GPS Coordinates            |    ✓    |     ✗     |    ✗    | Privacy                           |
| GPS Altitude               |    ✓    |     ✗     |    ✗    | Privacy                           |
| Camera Make                |    ✓    |     ✗     |    ✗    | Not required                      |
| Camera Model               |    ✓    |     ✗     |    ✗    | Not required                      |
| Lens Information           |    ✓    |     ✗     |    ✗    | Not required                      |
| Exposure Settings          |    ✓    |     ✗     |    ✗    | Not required                      |
| ISO                        |    ✓    |     ✗     |    ✗    | Not required                      |
| White Balance              |    ✓    |     ✗     |    ✗    | Not required                      |
| Device Identifier          |    ✓    |     ✗     |    ✗    | Privacy                           |
| Software Version           |    ✓    |     ✗     |    ✗    | Not required                      |
| Live Photo Pair Identifier |    ✓    | Temporary |    ✗    | Pairing                           |
| Original Filename          |    ✓    |  Partial  |    ✗    | Diagnostics & duplicate detection |

---

# 10.6 Retained Metadata

Retained metadata satisfies at least one of the following criteria.

It:

* contributes to storytelling;
* enables playback;
* preserves chronology;
* supports recovery;
* improves reliability.

Examples include:

* Journey titles;
* capture dates;
* owner-authored stories;
* playback duration;
* Journey ordering.

Retained metadata forms part of the permanent journal.

---

# 10.7 Derived Metadata

Some values should never be imported directly.

Instead, they should be derived during processing.

Examples include:

* thumbnail dimensions;
* display aspect ratios;
* playback orientation;
* optimised image size.

Derived metadata can always be regenerated if required.

This reduces long-term storage complexity.

---

# 10.8 Temporary Metadata

Temporary metadata exists only while processing a Journey.

Examples include:

* Live Photo pairing identifiers;
* intermediate hashes;
* import manifests;
* transcoding statistics;
* decoder information.

Temporary metadata should be deleted once the Journey reaches the `READY` state.

Its continued existence provides little value while increasing maintenance complexity.

---

# 10.9 Discarded Metadata

Some metadata is intentionally removed.

Examples include:

* GPS coordinates;
* camera serial numbers;
* software history;
* editing software identifiers;
* cloud export identifiers;
* manufacturer-specific private tags.

Discarding this information provides several benefits:

* stronger privacy;
* smaller storage requirements;
* simpler backups;
* reduced implementation complexity.

Information that serves no meaningful purpose should not become part of the journal.

---

# 10.10 EXIF Policy

EXIF metadata should be parsed conservatively.

The parser should ignore unknown tags unless they are explicitly supported.

Malformed EXIF should never prevent an otherwise valid photograph from being imported.

Where possible:

* recover useful fields;
* ignore unsupported fields;
* continue processing.

---

# 10.11 Live Photo Metadata

Live Photos require additional metadata during import.

Examples include:

* pairing identifiers;
* capture timestamps;
* motion duration;
* codec information.

After pairing succeeds, only the metadata required for playback should remain.

Internal pairing identifiers should not become permanent Journey data.

---

# 10.12 Metadata Provenance

Every retained metadata value should have a clear origin.

Possible sources include:

* imported media;
* owner input;
* derived values;
* application-generated values.

The application should avoid storing values whose origin cannot be explained.

This improves debugging, migration and future maintenance.

---

# 10.13 Metadata Integrity

Metadata should satisfy the same integrity guarantees as media.

Validation includes:

* supported value ranges;
* valid dates;
* consistent dimensions;
* valid durations;
* valid Unicode strings;
* duplicate detection.

Invalid metadata should never silently overwrite trusted values.

---

# 10.14 Metadata During Backup

Backup packages should contain only permanent metadata.

Temporary processing information must not be included.

Backup metadata should be:

* deterministic;
* portable;
* versioned;
* documented.

This ensures that backups remain useful even many years after they are created.

---

# 10.15 Metadata Logging

Diagnostic logging should minimise metadata exposure.

Avoid logging:

* GPS coordinates;
* Memory Card text;
* owner-written stories;
* complete filenames;
* personal notes.

Prefer:

* Journey identifiers;
* import identifiers;
* processing stages;
* validation results.

Logs should help diagnose software without exposing family history.

---

# 10.16 Future Metadata

Future versions may introduce new metadata categories.

Examples include:

* AI-generated summaries;
* accessibility descriptions;
* face clustering;
* semantic scene labels.

Every new metadata field must undergo the same classification process before it becomes part of the permanent journal.

No metadata should be retained simply because it is available.

---

# 10.17 Architectural Guarantees

The metadata system guarantees that:

* owner-authored stories are always preserved;
* imported metadata is validated before use;
* temporary metadata is removed after import;
* unnecessary personal metadata is discarded;
* retained metadata has a documented purpose;
* metadata remains portable across future versions.

These guarantees apply throughout the lifetime of the project.

---

## Closing Statement

Metadata is often treated as an implementation detail.

Within Magnet Memories it is considered an architectural responsibility.

Every retained field increases long-term maintenance obligations.

Every discarded field strengthens privacy.

Every transformed field reflects a deliberate engineering decision.

> **The journal should remember what gives a memory meaning—not every detail the camera happened to record.**

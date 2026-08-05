# Storage and Backup

**Project:** Magnet Memories
**Document:** Storage and Backup
**Version:** 1.0
**Status:** Draft

---

# Purpose

This document defines how Magnet Memories stores, protects, organises and backs up family memories.

It specifies:

* local storage architecture;
* folder structure;
* storage lifecycle;
* backup philosophy;
* restoration;
* data integrity;
* storage monitoring;
* cleanup;
* future synchronisation strategy.

The goal is to ensure that memories remain available, dependable and recoverable for many years without depending on cloud services.

---

# Design Philosophy

> **The tablet is the journal. The hard drive is the archive.**

The external hard drive stores the family's master collection.

The tablet stores a carefully curated, optimised copy designed for everyday viewing.

Neither replaces the other.

---

# Storage Layers

Magnet Memories uses three storage layers.

```text
               Family Archive
                     │
          (1 TB External Drive)
                     │
          Original HEIC / Live Photos
                     │
                     ▼
          ┌─────────────────────┐
          │ Import Pipeline      │
          └─────────────────────┘
                     │
                     ▼
        Tablet Local Journal Storage
                     │
      Optimised Images + Motion Files
      Room Database + Stories
      Thumbnails + Preferences
                     │
                     ▼
          Runtime Memory Cache
```

Each layer has a different responsibility.

---

# Layer 1 — Family Archive

The archive is the permanent source of truth.

It contains:

* original photographs;
* original HEIC files;
* original Live Photos;
* original videos;
* optional `trip.json`;
* future export manifests.

Characteristics:

* never modified by the app;
* full quality;
* stored externally;
* replaceable tablet copies can always be recreated.

---

# Layer 2 — Local Journal

The journal is the owner's daily experience.

It contains:

* optimised display images;
* processed Live Photo motion files;
* thumbnails;
* Room database;
* Memory Cards;
* Journey ordering;
* favourites;
* preferences.

Characteristics:

* completely offline;
* optimised for the HMD T21;
* independent of the archive once imported.

---

# Layer 3 — Runtime Cache

The runtime cache exists only to improve performance.

It may contain:

* decoded bitmaps;
* Media3 buffers;
* temporary page assets;
* image loader cache;
* import temporary files.

Nothing in the runtime cache is irreplaceable.

---

# Storage Locations

Conceptually:

```text
Android App Storage
│
├── database/
│
├── files/
│   └── journeys/
│
├── cache/
│
└── datastore/
```

The application should rely on Android's app-private storage.

No user interaction with these folders should be required.

---

# Files Directory

Recommended structure:

```text
files/
└── journeys/
    ├── {journeyId}/
    │   ├── stills/
    │   ├── motion/
    │   ├── thumbnails/
    │   └── manifest.json (future)
    │
    └── ...
```

The structure should remain stable across future versions.

---

# Database

Room stores only metadata.

Never store:

* image bytes;
* videos;
* thumbnails;
* binary blobs.

Store:

* Journey metadata;
* Moment metadata;
* Memory Cards;
* ordering;
* references;
* playback state.

---

# Preferences

Jetpack DataStore stores:

* playback preferences;
* Live Photo mode;
* brightness preferences;
* resume settings;
* onboarding state;
* Workshop preferences.

Preferences are intentionally separated from Journey data.

---

# Storage Ownership

Every file belongs to exactly one Journey.

Example:

```text
journeys/
└── 8f5c64a2/
    ├── stills/
    ├── motion/
    └── thumbnails/
```

Deleting a Journey removes its directory.

No file should belong to multiple Journeys.

---

# Relative References

The database stores only relative paths.

Example:

```text
journeys/8f5c64a2/stills/42c7658a.jpg
```

Never store:

* absolute filesystem paths;
* `/storage/...`;
* drive letters;
* SAF URIs as playback references.

This keeps the storage portable.

---

# Storage Lifecycle

```text
External Source
        │
        ▼
Temporary Workspace
        │
        ▼
Verification
        │
        ▼
Journey Storage
        │
        ▼
Playback
        │
        ▼
Deletion
```

Media should never move directly from the external drive into playback.

---

# Storage Capacity

The application should estimate storage before import.

Estimate includes:

* still images;
* Live Photos;
* thumbnails;
* temporary files;
* database;
* safety margin.

If space is insufficient:

* prevent import;
* explain why;
* never partially import a Journey.

---

# Storage Health

Monitor:

* available free space;
* database size;
* Journey media size;
* cache size;
* temporary files.

Workshop may present a summary such as:

```text
Journal

18 Journeys

14.2 GB used

5.8 GB free
```

---

# Temporary Storage

Temporary files exist only during import.

Location:

```text
cache/imports/{importId}/
```

Requirements:

* isolated;
* recoverable;
* automatically cleaned;
* never referenced by playback.

---

# Cleanup Policy

Automatically remove:

* abandoned imports;
* temporary thumbnails;
* failed transcodes;
* cancelled import workspaces;
* stale caches.

Never automatically remove:

* Journeys;
* Memory Cards;
* owner-created stories.

---

# Backup Philosophy

Version 1 is **local-first**, not cloud-first.

The owner is expected to maintain:

* the external archive;
* the tablet journal.

The application should never assume cloud availability.

---

# What Must Be Backed Up

The following information is irreplaceable:

* Memory Card stories;
* Journey titles;
* ordering;
* favourite quotes;
* cover selection;
* Journey slugs;
* playback preferences.

These cannot be reconstructed from photographs alone.

---

# Backup Components

A complete backup contains:

```text
Room Database

+

Journey Files

+

Preferences

=

Complete Journal
```

---

# Archive vs Backup

The archive is **not** the same as a backup.

The archive contains:

* original media.

The backup contains:

* the owner's curated journal.

Both are important.

---

# Future Backup Package

A future export may produce:

```text
MagnetMemoriesBackup.zip

├── database/
├── journeys/
├── preferences/
└── manifest.json
```

The manifest should contain:

* application version;
* schema version;
* creation date;
* backup format version.

---

# Restore

Future restore process:

```text
Select Backup

↓

Validate

↓

Restore Database

↓

Restore Media

↓

Verify

↓

Journal Ready
```

Restore should never overwrite existing data without confirmation.

---

# Integrity Verification

Each Journey should be verifiable.

Checks include:

* database references;
* missing files;
* unreadable images;
* missing motion files;
* thumbnail availability.

Workshop may offer:

> Verify Journal

---

# Corruption Recovery

If corruption is detected:

Possible actions:

* rebuild thumbnails;
* regenerate playback copies;
* re-import Journey;
* restore from backup.

Corruption in one Journey must not affect others.

---

# Storage Monitoring

Background checks may detect:

* missing files;
* unexpected deletions;
* storage exhaustion;
* damaged media.

Checks should run infrequently to preserve battery.

---

# Low Storage Behaviour

When storage becomes critically low:

* warn the owner;
* prevent new imports;
* continue playback;
* allow cache cleanup.

Never delete memories automatically.

---

# External Drive

The external drive is disconnected during normal playback.

The application must:

* tolerate absence;
* never display errors because the drive is missing;
* reconnect only when importing or repairing.

---

# Future Synchronisation

Cloud synchronisation is intentionally outside Version 1.

Possible future providers:

* Google Drive;
* OneDrive;
* Dropbox;
* Synology NAS;
* local network storage.

The storage architecture should not prevent later synchronisation.

---

# Storage Security

Requirements:

* app-private storage;
* no world-readable files;
* validated paths;
* no path traversal;
* relative references only.

---

# Privacy

Do not store:

* GPS coordinates;
* cloud credentials;
* unnecessary metadata;
* analytics identifiers.

Only store what is required to recreate the journal experience.

---

# Storage Components

Recommended interfaces:

```kotlin
interface JourneyStorage

interface TemporaryStorage

interface BackupManager

interface RestoreManager

interface StorageEstimator

interface StorageHealthMonitor

interface IntegrityVerifier

interface CleanupManager
```

These abstractions isolate the rest of the application from filesystem details.

---

# Testing

Unit Tests:

* path validation;
* relative path generation;
* storage estimation;
* cleanup;
* backup manifest generation.

Integration Tests:

* import then playback;
* deletion;
* backup creation;
* restore;
* cache cleanup;
* integrity verification.

Physical Device Tests:

* low storage;
* full storage;
* reboot after import;
* external drive disconnected;
* large journal;
* hundreds of Journeys.

---

# ADRs

Create:

```text
ADR-019-Storage-Layout.md

ADR-020-Backup-Format.md

ADR-021-Restore-Strategy.md

ADR-022-Integrity-Verification.md

ADR-023-Storage-Cleanup-Policy.md
```

---

# Acceptance Criteria

The storage system is complete when:

* every Journey has its own storage directory;
* playback never depends on the external drive;
* Room stores metadata only;
* media uses validated relative paths;
* temporary files are cleaned automatically;
* low storage is handled gracefully;
* backups preserve authored content;
* restores recreate the journal correctly;
* corruption can be detected;
* deletion removes only the selected Journey.

---

# Final Principle

> **Family memories deserve the reliability of an archive and the warmth of a journal. The storage system exists to quietly protect both.**

# Import Pipeline

**Project:** Magnet Memories
**Document:** Import Pipeline
**Version:** 1.0
**Status:** Draft

---

# Purpose

This document defines how Magnet Memories imports a Journey from an external folder into the tablet’s local working journal.

It covers:

* folder selection;
* file discovery;
* metadata extraction;
* Live Photo pairing;
* import preview;
* storage estimation;
* owner confirmation;
* media processing;
* local file creation;
* database commit;
* cancellation;
* failure recovery;
* duplicate detection;
* import reporting.

The import pipeline must transform an external source folder into a dependable, fully offline Journey without modifying the original archive.

---

# Import Principle

> **The external folder is the source. The tablet creates its own dependable local chapter.**

The source folder may be disconnected immediately after import.

Journey playback must never depend on continued access to:

* the external hard drive;
* the selected folder;
* temporary Android content URIs;
* a computer;
* an internet connection.

---

# Source and Destination

## Source

The source may be located on:

* an external hard drive recognised by Android;
* a USB drive;
* a microSD card;
* tablet internal storage;
* a folder previously copied to the tablet.

The source is accessed through Android’s Storage Access Framework.

---

## Destination

Imported media is stored in app-managed local storage.

The destination contains:

* optimised still images;
* Live Photo motion files;
* thumbnails;
* Journey metadata;
* import records.

The source files are never modified.

---

# Import Experience

The owner-facing workflow should be:

```text
Choose Journey Folder
        ↓
Discover Memories
        ↓
Review Journey
        ↓
Resolve Issues
        ↓
Confirm Storage
        ↓
Add Journey
        ↓
Verify Chapter
        ↓
Journey Ready
```

The owner should be able to understand what will be imported before any permanent local changes occur.

---

# Supported Source Folder Layouts

The preferred layout is:

```text
new-york-2026/
├── trip.json
├── cover.jpg
└── photos/
    ├── IMG_1001.HEIC
    ├── IMG_1001.MOV
    ├── IMG_1002.JPG
    └── IMG_1003.PNG
```

The following simplified layout must also be supported:

```text
new-york-2026/
├── IMG_1001.HEIC
├── IMG_1001.MOV
├── IMG_1002.JPG
└── IMG_1003.PNG
```

If a `photos` folder exists, it should be treated as the main media source.

If no `photos` folder exists, inspect the selected root folder.

Version 1 does not need to recursively import arbitrary nested folder trees.

---

# Optional Files

The source folder may contain:

* `trip.json`;
* `cover.jpg`;
* supported photographs;
* Live Photo motion files;
* ignored sidecar or system files.

All optional metadata must be validated.

The importer must still work when no metadata file exists.

---

# Import Stages

The import pipeline consists of the following stages:

```text
1. Source Selection
2. Source Validation
3. File Discovery
4. Metadata Extraction
5. Live Photo Pairing
6. Duplicate Detection
7. Journey Draft Creation
8. Import Preview
9. Storage Estimation
10. Owner Confirmation
11. Temporary Workspace Creation
12. Media Processing
13. Output Verification
14. File Promotion
15. Database Commit
16. Journey Readiness Validation
17. Finalisation
18. Cleanup
```

Each stage must produce a structured result.

---

# Stage 1 — Source Selection

Use Android’s Storage Access Framework directory picker.

The application should request access only to the selected tree.

Do not request unrestricted storage access.

The picker should allow the owner to choose a Journey folder.

User-facing language:

> Choose a journey folder.

---

# Source Selection Result

Conceptual model:

```kotlin
sealed interface SourceSelectionResult {

    data class Selected(
        val treeUri: Uri,
        val displayName: String
    ) : SourceSelectionResult

    data object Cancelled : SourceSelectionResult

    data class Failed(
        val reason: SourceSelectionFailure
    ) : SourceSelectionResult
}
```

Cancelling the picker should return safely to Workshop.

---

# Persisted URI Permission

The application may request persistable read permission for the selected folder while the import is active.

After successful import:

* the permission may be released;
* or retained only if the owner explicitly enables future source-folder updates.

Version 1 should not depend on permanent access.

The local imported Journey remains authoritative.

---

# Stage 2 — Source Validation

Before discovery, verify that the selected tree:

* can be opened;
* can be enumerated;
* has a usable display name;
* is not the app’s own output directory;
* is not empty;
* does not contain an obviously unsupported structure;
* remains within the selected Storage Access Framework tree.

The source should not be rejected merely because it lacks `trip.json`.

---

# Invalid Source Examples

Reject or warn when:

* the folder cannot be read;
* the selected item is not a directory;
* all discovered files are unsupported;
* access permission is revoked;
* the source resolves to the application’s private output area;
* the folder contains only zero-byte files.

User-facing example:

> We couldn’t find any supported memories in this folder.

---

# Stage 3 — File Discovery

Discovery should enumerate supported source documents.

It should classify:

* still-image candidates;
* motion candidates;
* optional metadata;
* optional cover image;
* unsupported files;
* unreadable files;
* ignored system files.

Discovery should not yet create local Journey files.

---

# Discovery Rules

Discovery must:

* compare extensions case-insensitively;
* inspect MIME type;
* avoid trusting MIME type alone;
* avoid following paths outside the selected tree;
* ignore hidden system files where appropriate;
* detect duplicate source document references;
* avoid loading complete file contents unnecessarily.

---

# Discovery Result

```kotlin
data class JourneyDiscoveryResult(
    val sourceFolderName: String,
    val stillCandidates: List<StillCandidate>,
    val motionCandidates: List<MotionCandidate>,
    val metadataFile: SourceDocument?,
    val coverCandidate: SourceDocument?,
    val unsupportedFiles: List<UnsupportedSourceFile>,
    val unreadableFiles: List<UnreadableSourceFile>,
    val ignoredFiles: List<IgnoredSourceFile>
)
```

---

# Stage 4 — Metadata Extraction

Extract metadata from every candidate in a bounded, fault-tolerant process.

For still images, inspect:

* format;
* dimensions;
* orientation;
* capture date;
* Live Photo identifier;
* file size;
* colour characteristics.

For motion files, inspect:

* duration;
* dimensions;
* video codec;
* audio codec;
* capture date;
* Live Photo identifier;
* rotation;
* file size.

Metadata failure for one file should not cancel discovery of every other file.

---

# Metadata Concurrency

Metadata extraction may be parallelised conservatively.

Requirements:

* use a bounded worker count;
* avoid simultaneous large decodes;
* remain responsive;
* support cancellation;
* avoid overwhelming external storage.

Suggested starting point:

```text
2–4 concurrent metadata inspections
```

Final concurrency should be tested on the HMD T21.

---

# Stage 5 — Live Photo Pairing

Run the Live Photo pairing engine after metadata extraction.

The result should separate:

* confident pairs;
* unpaired stills;
* unmatched motions;
* ambiguous candidates.

A confident pair becomes one planned `LIVE_PHOTO` Moment.

An unpaired still becomes one planned `STILL_PHOTO` Moment.

An unmatched motion does not become a Journey Moment in Version 1.

---

# Stage 6 — Duplicate Detection

Duplicate detection should occur before permanent processing.

Check for duplicates against:

* existing Moments in the target Journey;
* other imported Journeys where useful;
* duplicate files within the selected source folder.

Signals may include:

* source fingerprint;
* original filename;
* capture date;
* file size;
* still-and-motion pair identity.

Duplicate detection should be conservative.

---

# Duplicate Result

```kotlin
sealed interface DuplicateAssessment {

    data object Unique : DuplicateAssessment

    data class ProbableDuplicate(
        val matchingMomentId: MomentId,
        val evidence: List<DuplicateEvidence>
    ) : DuplicateAssessment

    data class Ambiguous(
        val candidates: List<MomentId>
    ) : DuplicateAssessment
}
```

The owner should be able to:

* skip probable duplicates;
* include them;
* inspect ambiguous cases.

---

# Stage 7 — Journey Draft Creation

Create an in-memory Journey draft before writing permanent records.

The draft may be populated from:

* `trip.json`;
* source folder name;
* cover file;
* inferred dates;
* owner input.

Conceptual model:

```kotlin
data class JourneyImportDraft(
    val proposedTitle: String,
    val proposedSlug: JourneySlug,
    val location: String?,
    val startDate: LocalDate?,
    val endDate: LocalDate?,
    val story: String,
    val favouriteQuote: String?,
    val proposedCover: PlannedMomentId?,
    val plannedMoments: List<PlannedMoment>
)
```

The draft must remain editable before confirmation.

---

# Metadata Precedence

Suggested precedence for Journey fields:

1. owner edits;
2. valid `trip.json`;
3. folder-name inference;
4. media-derived date range;
5. empty optional value.

The application must never overwrite owner edits with later inference.

---

# Folder Name Inference

A folder such as:

```text
new-york-2026
```

may suggest:

```text
Title: New York
Slug: new-york-2026
```

Inference must be presented as a suggestion, not a guaranteed interpretation.

Do not infer a precise date from a year-only folder name.

---

# Stage 8 — Import Preview

The import preview is the owner’s opportunity to review the Journey before processing.

Display:

* proposed Journey title;
* slug;
* location;
* dates;
* proposed cover;
* Still Photo count;
* Live Photo count;
* unsupported file count;
* unmatched motion count;
* ambiguous pairing count;
* probable duplicate count;
* estimated storage.

The owner may edit metadata and resolve issues.

---

# Preview Language

Prefer:

> 124 memories found.

Then break down:

```text
98 photographs
26 Live Photos
2 files need attention
```

Avoid presenting only raw file counts.

---

# Preview Requirements

The preview must allow:

* title editing;
* slug editing;
* date editing;
* location editing;
* story editing;
* favourite quote editing;
* cover selection;
* duplicate inclusion or exclusion;
* ambiguous Live Photo resolution;
* unmatched motion review;
* cancellation.

The owner should not need to complete the Memory Card before importing, but the Journey may remain `DRAFT` until ready.

---

# Stage 9 — Storage Estimation

Estimate:

* processed still-image output;
* thumbnails;
* motion files retained directly;
* motion files requiring transcoding;
* temporary processing space;
* database overhead;
* safety margin.

The estimate should use source metadata and processing profiles.

It does not need to be exact, but it must be conservative enough to prevent predictable failure.

---

# Storage Estimate Model

```kotlin
data class ImportStorageEstimate(
    val finalStillBytes: Long,
    val finalMotionBytes: Long,
    val thumbnailBytes: Long,
    val temporaryBytes: Long,
    val databaseBytes: Long,
    val safetyMarginBytes: Long
) {
    val requiredFreeBytes: Long
        get() = finalStillBytes +
            finalMotionBytes +
            thumbnailBytes +
            temporaryBytes +
            databaseBytes +
            safetyMarginBytes
}
```

---

# Insufficient Storage

If free space is insufficient:

* prevent import;
* explain the estimated requirement;
* show current free space;
* offer access to storage management;
* preserve the import draft where practical.

User-facing example:

> There isn’t enough space to add this journey.

---

# Stage 10 — Owner Confirmation

Before processing begins, show a final summary.

Example:

```text
Add New York to the journal?

112 photographs
18 Live Photos
Estimated space: 2.4 GB
```

Primary action:

> Add Journey

Secondary action:

> Review Again

Do not begin permanent work before explicit owner confirmation.

---

# Stage 11 — Temporary Workspace

Create an isolated temporary workspace.

Recommended structure:

```text
cache/imports/{importId}/
├── stills/
├── motion/
├── thumbnails/
├── records/
└── import-state.json
```

The temporary area must belong to one import only.

---

# Temporary Workspace Rules

* Do not allow Journey playback from temporary files.
* Track every generated file.
* Remove files after successful promotion.
* Remove files after cancellation.
* Recover abandoned workspaces after process restart.
* Do not mix outputs from different imports.
* Validate all generated relative paths.

---

# Import Manifest

Maintain an internal import manifest.

Conceptual model:

```kotlin
data class ImportExecutionManifest(
    val importId: ImportId,
    val journeyId: JourneyId,
    val sourceFolderName: String,
    val plannedItems: List<ImportExecutionItem>,
    val completedItemIds: Set<ImportItemId>,
    val failedItems: List<ImportItemFailure>,
    val startedAt: Instant,
    val updatedAt: Instant
)
```

This manifest may be represented in Room, a temporary JSON file, or both.

The authoritative recovery state should be documented.

---

# Stage 12 — Media Processing

Process planned Moments individually.

For each Still Photo:

1. open source;
2. decode safely;
3. normalise orientation;
4. resize;
5. encode playback copy;
6. generate thumbnail;
7. verify outputs;
8. update progress.

For each Live Photo:

1. process still component;
2. inspect motion compatibility;
3. retain or transcode motion;
4. verify motion playback;
5. generate thumbnail;
6. verify paired outputs;
7. update progress.

---

# Processing Order

Recommended default order:

1. proposed cover;
2. first several Journey Moments;
3. remaining Moments by display order.

This allows early verification of the most important content.

It does not mean Journey playback can begin before import completes.

---

# Per-Item Isolation

One item failure should not automatically cancel the entire import.

Possible outcomes:

* imported successfully;
* Live Photo downgraded to Still Photo;
* skipped with warning;
* failed and requires owner review.

The final Journey may proceed if enough valid Moments remain and the owner accepts the report.

---

# Import Progress

Progress should be based on completed processing units rather than fictional time estimates.

Display:

* current stage;
* number of memories processed;
* total planned memories;
* current item category;
* cancellation action.

Example:

```text
Adding pages…

42 of 124 memories prepared
```

For Live Photos:

```text
Preparing a moving photograph…
```

Avoid rapidly changing filenames in the primary UI.

---

# Progress Model

```kotlin
data class ImportProgress(
    val stage: ImportStage,
    val completedUnits: Int,
    val totalUnits: Int,
    val successfulMoments: Int,
    val downgradedMoments: Int,
    val failedMoments: Int,
    val processedBytes: Long?
)
```

---

# WorkManager

Large imports should use WorkManager or an equivalent lifecycle-safe mechanism.

Requirements:

* continue through Activity recreation;
* expose progress;
* support cancellation;
* recover after process death where possible;
* use foreground execution when required by Android for long-running work;
* display a clear system notification only when Android requires it.

The public Journey experience must not be available while a codec-intensive import is actively using shared resources unless testing proves safe.

---

# Import Exclusivity

Version 1 should allow only one active Journey import at a time.

Reasons:

* predictable storage use;
* simpler recovery;
* bounded codec usage;
* lower thermal load;
* easier progress reporting.

Additional imports may be queued later.

---

# Stage 13 — Output Verification

After processing, verify every planned local output.

For Still Photos:

* playback image decodes;
* thumbnail decodes;
* dimensions are valid;
* file sizes are non-zero;
* relative paths are valid.

For Live Photos:

* still output decodes;
* motion output prepares;
* video track exists;
* duration is valid;
* audio state is recorded;
* paired files share one Moment identity.

---

# Verification Failure

A failed verification must not be committed as a healthy Moment.

Possible responses:

* retry processing once for a transient failure;
* downgrade Live Photo to Still Photo;
* skip the item;
* fail the import if no valid content remains.

Retries must be bounded.

---

# Stage 14 — File Promotion

After all accepted outputs pass verification, move them from temporary storage into the final Journey directory.

Recommended final structure:

```text
files/journeys/{journeyId}/
├── stills/
├── motion/
└── thumbnails/
```

Promotion should be as atomic as practical.

---

# Promotion Strategy

Preferred strategy:

1. prepare complete Journey directory under a temporary name;
2. verify contents;
3. rename or move directory into the final app-managed location;
4. commit Room records;
5. mark Journey ready.

If platform behaviour prevents reliable atomic directory promotion:

* track every move;
* make rollback restart-safe;
* never lose ownership information.

The final chosen strategy should be recorded in an ADR.

---

# Stage 15 — Database Commit

Commit Journey and Moment records in a Room transaction.

The transaction should include:

* Journey row;
* Moment rows;
* final ordering;
* cover selection;
* import status update;
* Journey status update where appropriate.

The database transaction must not contain slow media processing.

---

# Commit Failure

If the database commit fails after files are promoted:

* remove or quarantine the promoted Journey directory;
* keep the external source untouched;
* mark import failed;
* retain enough state for cleanup;
* do not expose the Journey in public playback.

---

# Stage 16 — Journey Readiness Validation

Before setting the Journey to `READY`, verify:

* valid slug;
* title;
* at least one valid Moment;
* valid local file references;
* unique contiguous ordering;
* cover Moment available;
* no unresolved blocking issues;
* no active processing work;
* database and file-system consistency.

If the story is blank:

* allow import to complete;
* keep Journey in `DRAFT`;
* prompt the owner to write the Memory Card before programming the magnet.

This preserves the product principle that every public Journey begins with a story.

---

# Stage 17 — Finalisation

Finalisation should:

* update Journey timestamps;
* mark import complete;
* release unnecessary source permissions;
* remove temporary state;
* refresh Workshop;
* present final report.

User-facing success:

> Your journey has been added to the journal.

If the Journey remains a draft:

> Your journey has been added. Write its Memory Card when you’re ready.

---

# Stage 18 — Cleanup

Cleanup must remove:

* temporary stills;
* temporary motion files;
* temporary thumbnails;
* temporary manifests;
* abandoned conversion outputs;
* stale WorkManager intermediate data.

Cleanup should be idempotent.

Running it more than once must be safe.

---

# Import Outcomes

The overall import may result in:

```kotlin
sealed interface JourneyImportOutcome {

    data class Ready(
        val journeyId: JourneyId,
        val report: ImportReport
    ) : JourneyImportOutcome

    data class Draft(
        val journeyId: JourneyId,
        val report: ImportReport,
        val remainingIssues: List<DraftIssue>
    ) : JourneyImportOutcome

    data class Failed(
        val importId: ImportId,
        val category: ImportFailureCategory,
        val recoverable: Boolean
    ) : JourneyImportOutcome

    data object Cancelled : JourneyImportOutcome
}
```

---

# Import Report

The final report should contain:

* Journey title;
* Still Photos imported;
* Live Photos imported;
* Live Photos downgraded;
* duplicates skipped;
* unsupported files;
* unmatched motion files;
* failed files;
* final local storage used;
* whether the Journey is ready.

Technical details may be available behind an optional diagnostics view.

---

# Cancellation

Cancellation must be available while processing.

Cancellation sequence:

1. stop scheduling new items;
2. request the current processor to stop;
3. finish or safely abort current codec work;
4. mark import cancelling;
5. delete temporary outputs;
6. remove draft database state where appropriate;
7. mark import cancelled;
8. return to Workshop.

The external source remains untouched.

---

# Cancellation Language

While stopping:

> Finishing the current page before stopping…

After completion:

> The journey was not added.

Do not say “cancelled successfully” in the primary experience.

---

# Process Death Recovery

On application startup, inspect imports in states such as:

* `RUNNING`;
* `CLEANING_UP`;
* `FAILED`;
* `REMOVING`.

Recovery policy:

* reconnect to active WorkManager work when possible;
* resume deterministic processing only when safe;
* otherwise clean temporary state and offer retry;
* never mark a Journey ready based solely on an incomplete status;
* verify promoted files before trusting them.

---

# Abandoned Import Recovery

An import is abandoned when:

* no active worker exists;
* state indicates work was running;
* temporary files remain;
* final commit is incomplete.

Possible recovery actions:

* resume from manifest;
* restart processing;
* remove temporary files;
* present owner with retry option.

Version 1 may choose cleanup-and-retry rather than exact resumption if that is more reliable.

The chosen behaviour must be explicit.

---

# Low Storage During Import

Even after estimation, storage may run out.

On low-storage failure:

1. stop processing;
2. preserve completed temporary outputs until cleanup decision;
3. report required additional space;
4. clean temporary data unless retry can reuse it safely;
5. do not create a partial READY Journey.

User-facing example:

> The tablet ran out of space before this journey could be added.

---

# Source Disconnection

The external drive may disconnect during import.

The pipeline should:

* stop reading;
* finish safely with currently buffered data only when valid;
* mark the import failed or paused;
* preserve completed local temporary outputs if useful;
* allow the owner to reconnect and retry;
* never fabricate missing content.

User-facing example:

> The journey folder is no longer connected.

---

# Source Mutation During Import

The source folder may change while processing.

Where practical:

* record initial size and metadata;
* detect unreadable or changed files;
* treat changed files as failures;
* avoid importing partially rewritten media;
* do not repeatedly rescan and silently change the plan.

The confirmed import plan should remain stable.

---

# Import Idempotency

Retrying the same import should not automatically create duplicate Journeys.

Use:

* source folder name;
* planned slug;
* fingerprints;
* prior failed import records;
* Journey IDs;
* existing local outputs.

A retry should either:

* continue or replace the failed draft;
* or clearly ask whether to create a separate Journey.

---

# Adding Media to an Existing Journey

Version 1 may support adding more media later.

The same pipeline should be reusable with a target Journey.

Differences:

* preserve existing Journey metadata;
* detect duplicates against existing Moments;
* append new Moments by default;
* allow later reordering;
* do not rewrite the NFC tag;
* do not replace the cover unless the owner chooses.

This may be implemented after complete new-Journey import works reliably.

---

# Updating Journey Metadata

Metadata edits do not require reprocessing media unless:

* the cover changes;
* ordering changes;
* a Moment is removed;
* a media file is replaced.

Changing title, location, dates, story or favourite quote should update Room only.

Changing the slug requires a warning because programmed magnets depend on it.

---

# Import Security

Source folders and media are untrusted input.

Requirements:

* no path traversal;
* no execution of source content;
* no trust in extension alone;
* bounded allocation;
* safe metadata parsing;
* sanitised metadata strings;
* no source-controlled destination paths;
* no source-controlled database IDs;
* no network upload;
* no shell execution based on filenames.

---

# Import Privacy

Do not persist by default:

* GPS coordinates;
* full source folder paths;
* external device identifiers;
* raw metadata dumps;
* cloud account information.

Persist only what supports:

* playback;
* duplicate detection;
* owner-authored context;
* repair.

---

# Import Logging

Logs may include:

* import ID;
* Journey ID;
* stage;
* counts;
* elapsed time;
* failure categories;
* processor type.

Do not log:

* Memory Card text;
* full original filenames where avoidable;
* GPS coordinates;
* complete external URIs;
* image content identifiers in raw form.

---

# Import Components

Recommended interfaces:

```kotlin
interface SourceFolderReader

interface JourneyMetadataParser

interface MediaDiscoveryService

interface MediaMetadataExtractor

interface LivePhotoPairingEngine

interface DuplicateDetector

interface JourneyImportPlanner

interface StorageEstimator

interface StillImageProcessor

interface MotionCompatibilityChecker

interface MotionTranscoder

interface MediaOutputVerifier

interface ImportFilePromoter

interface ImportCommitter

interface ImportRecoveryManager

interface ImportCleanupManager
```

Each component must return structured results.

---

# Import Coordinator

A central coordinator should manage stage transitions.

```kotlin
interface JourneyImportCoordinator {

    suspend fun plan(
        source: JourneySource
    ): ImportPlanResult

    suspend fun execute(
        plan: ConfirmedImportPlan
    ): JourneyImportOutcome

    suspend fun cancel(
        importId: ImportId
    )

    suspend fun recoverIncompleteImports()
}
```

The coordinator should not depend on Compose UI.

---

# Import State Machine

Conceptual state:

```kotlin
sealed interface JourneyImportState {

    data object Idle : JourneyImportState

    data class Discovering(
        val sourceName: String
    ) : JourneyImportState

    data class PreviewReady(
        val plan: JourneyImportPlan
    ) : JourneyImportState

    data class AwaitingConfirmation(
        val plan: ConfirmedImportPlan
    ) : JourneyImportState

    data class Running(
        val importId: ImportId,
        val progress: ImportProgress
    ) : JourneyImportState

    data class Finalising(
        val importId: ImportId
    ) : JourneyImportState

    data class Completed(
        val journeyId: JourneyId,
        val report: ImportReport
    ) : JourneyImportState

    data class Failed(
        val importId: ImportId?,
        val failure: ImportFailure
    ) : JourneyImportState

    data object Cancelling : JourneyImportState
}
```

Transitions must be deterministic and testable.

---

# Testing Requirements

## Unit Tests

Test:

* folder-layout resolution;
* source validation;
* metadata precedence;
* folder-name title inference;
* slug generation;
* preview counts;
* duplicate handling;
* storage estimation;
* state transitions;
* cancellation;
* readiness validation;
* import report generation.

---

## Integration Tests

Test:

* complete Still Photo Journey import;
* complete Live Photo Journey import;
* mixed media import;
* missing `trip.json`;
* malformed `trip.json`;
* ambiguous pairing;
* duplicate source files;
* low storage;
* external drive disconnection;
* corrupted media;
* database commit failure;
* file-promotion failure;
* process death during media processing;
* process death during finalisation;
* cancellation cleanup;
* retry of failed import.

---

## Physical Device Tests

Test on the HMD T21:

* Storage Access Framework folder selection;
* external hard-drive recognition;
* USB disconnection;
* import speed;
* WorkManager behaviour;
* foreground notification;
* screen rotation behaviour;
* low-battery behaviour;
* thermal behaviour;
* storage estimation accuracy;
* HEIC processing;
* Live Photo motion preparation;
* import with hundreds of Moments;
* playback after drive disconnection.

---

# Performance Goals

The import UI must remain responsive.

The pipeline should:

* avoid loading whole Journeys into memory;
* use bounded concurrency;
* process incrementally;
* provide progress updates;
* avoid repeated full-file reads where possible;
* reuse extracted metadata;
* avoid unnecessary transcoding.

Reliability is more important than maximum speed.

---

# ADRs

Create the following records:

```text
ADR-014-Import-Transaction-Strategy.md
ADR-015-Storage-Estimation-Policy.md
ADR-016-Import-Recovery-Policy.md
ADR-017-Duplicate-Detection-Policy.md
ADR-018-Source-Permission-Retention.md
```

---

# Acceptance Criteria

The Version 1 import pipeline is complete when:

* the owner can select a Journey folder through Storage Access Framework;
* supported media is discovered safely;
* optional `trip.json` is validated;
* Still Photos and Live Photos are represented correctly;
* ambiguous pairings require review;
* duplicate candidates are reported;
* storage is estimated before processing;
* the owner sees a complete import preview;
* media is processed into app-managed local storage;
* originals remain unchanged;
* imports continue through Activity recreation;
* cancellation cleans temporary data;
* process interruption does not create a partial READY Journey;
* file and database commits are coordinated safely;
* a final report explains skipped or downgraded media;
* the imported Journey works offline after the source is disconnected.

---

# Final Principle

> **Adding a Journey should feel simple to the owner because the import pipeline handles complexity carefully, visibly and safely behind the scenes.**
